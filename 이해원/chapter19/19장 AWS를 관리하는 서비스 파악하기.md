# 19장 AWS를 관리하는 서비스 파악하기

# 1. AWS를 관리하는 서비스 유형 파악하기

- AWS 백업: 백업 및 복원 관리 서비스
- AWS 시스템 관리자: 인프라 관리 서비스
- AWS 클라우드워치: 모니터링 및 로깅 서비스
- AWS WAF: 외부 공격로부터 보호하는 방화벽 서비스
- VPC 플로우 로그: 네트워크 트래픽 로깅 서비스
- 관리형 접두사 목록: 네트워크 구성을 효율화

# 2. 백업 및 복원 관리 서비스 파악하기

EC2 인스턴스 구축하고 운영하는 과정에서 

1. EC2 인스턴스가 중지되거나 사용 불가능한 상태를 가정해보자.
2. 신속하게 EC2 인스턴스를 생성한다고 하더라도 EC2 인스턴스 내부 데이터까지 복구하기까지 많은 시간이 걸린다.

⇒ AWS 백업 서비스

## 2.1 AWS 백업이란?

- 백업할 시간대 지정
- 다양한 서비스 백업 및 복원
    - RDS, EBS, S3, 오로라, 클라우드포메이션 등

## 2.2 백업 및 복원 관리 서비스, AWS 백업 살펴보기

### 백업 볼트

백업을 구성하는 컨테이너

- EC2 인스턴스를 백업할 대 생성되는 AMI와 스냅샷을 정리해 관리하는 그릇 역할을 한다.
- KMS 암호화 → 데이터 보호

### 백업 계획

백업 작업 시간대를 정의/실행하며, 백업 볼트 내에 복구 지점을 생성한다.

- 백업 계획은 하나 이상의 백업 규칙 포함
- 백업 규칙에서는 백업 빈도, 백업 시간대, 백업 볼트 등 지정
    - 백업 빈도: 온디맨드 백업 → 한 번만 백업 수행
- 백업 규칙에서는 PITR(특정 시점 복구 기능) 지원
    - 5분 전과 같이 사용자가 특정 날짜, 시간을 정해 원하는 시점으로 복원할 수 있는 것
    - 특정 시점 복구는 RDS, S3만 지원
- 백업 규칙 지정 후 백업할 리소스 할당
    - [모든 리소스 유형 포함], [특정 리소스 유형 포함]

# 3. AWS 백업 활용하기

3.1 AWS 백업을 활용해 EC2 인스턴스 백업 및 복원해보기

- VPC.yml
    
    ```yaml
    AWSTemplateFormatVersion: "2010-09-09"
    Description: VPC Network Set
    
    Parameters:
      SystemName:
        Type: String
        Default: "gr"          # 리소스 이름에 붙는 시스템 접두사 (gr-product-vpc 등)
      EnvName:
        Type: String
        Default: "product"     # 환경 구분자 (dev/stage/product 등), 리소스명·Export명에 사용
    
      # ── VPC 및 서브넷 CIDR 대역 정의 ──────────────────────────────
      # 전체 VPC는 10.0.0.0/24 (256개 IP)를 3계층(Public/Web/Datastore) ×
      # 2개 AZ(1a, 1b)로 쪼갠 구조. 각 서브넷은 /27 = 32개 IP.
      VPCParam:
        Default: 10.0.0.0/24
      PublicSubnet1aParam:
        Default: 10.0.0.0/27      # Public 계층 - AZ 1a (예: ALB, NAT GW 배치용)
      PublicSubnet1bParam:
        Default: 10.0.0.32/27     # Public 계층 - AZ 1b
      WebSubnet1aParam:
        Default: 10.0.0.64/27     # Web/App 계층 - AZ 1a (EC2, ECS 등)
      WebSubnet1bParam:
        Default: 10.0.0.96/27     # Web/App 계층 - AZ 1b
      DatastoreSubnet1aParam:
        Default: 10.0.0.128/27    # DB 계층 - AZ 1a (RDS 등, 외부 접근 차단 대상)
      DatastoreSubnet1bParam:
        Default: 10.0.0.160/27    # DB 계층 - AZ 1b
    
    Resources:
      VPC:
        Type: AWS::EC2::VPC
        Properties:
          CidrBlock: !Ref VPCParam
          EnableDnsSupport: "true"     # VPC 내부 DNS 확인 허용
          EnableDnsHostnames: "true"   # 퍼블릭 IP에 대한 DNS 호스트명 자동 부여
    
      # ── 서브넷 6개 생성 ─────────────────────────────────────────
      # Fn::Select + Fn::GetAZs 조합으로 AZ를 하드코딩하지 않고
      # 리전 내 사용 가능한 AZ 목록에서 0번째/1번째를 자동 선택
      PublicSubnet1a:
        Type: AWS::EC2::Subnet
        Properties:
          AvailabilityZone:
            Fn::Select: [0, Fn::GetAZs: ""]   # 첫 번째 AZ
          CidrBlock: !Ref PublicSubnet1aParam
          VpcId: !Ref VPC
      # (WebSubnet1a/1b, DatastoreSubnet1a/1b도 동일 패턴 반복)
    
      # ── 인터넷 게이트웨이 ──────────────────────────────────────
      InternetGateway:
        Type: AWS::EC2::InternetGateway
      InternetGatewayAttachment:
        Type: AWS::EC2::VPCGatewayAttachment   # IGW를 VPC에 연결해야 실제로 동작
        Properties:
          InternetGatewayId: !Ref InternetGateway
          VpcId: !Ref VPC
    
      # ── 라우팅 테이블 3개 (Public / Web / Datastore 계층별 분리) ──
      PublicRTB:
        Type: AWS::EC2::RouteTable
      WebRTB:
        Type: AWS::EC2::RouteTable
      DatastoreRTB:
        Type: AWS::EC2::RouteTable
        # ⚠ 주의: Datastore용 라우팅 테이블에는 0.0.0.0/0 → IGW 라우트가
        # 없어서(PublicRoute만 존재) DB 서브넷은 인터넷 아웃바운드 불가.
        # NAT 게이트웨이가 필요하면 별도 라우트를 추가해야 함(현재 템플릿엔 NAT GW 없음).
    
      PublicRoute:
        Type: AWS::EC2::Route
        Properties:
          RouteTableId: !Ref PublicRTB
          DestinationCidrBlock: "0.0.0.0/0"
          GatewayId: !Ref InternetGateway   # Public 서브넷만 인터넷 직접 연결
    
      # ── 라우팅 테이블-서브넷 연결 (각 AZ별로 매핑) ─────────────
      PublicRTBAssociation1:
        Type: AWS::EC2::SubnetRouteTableAssociation
      # (이하 동일 패턴으로 6개 서브넷 모두 연결)
    
    Outputs:
      # 다른 스택에서 !ImportValue 로 참조할 수 있도록 Export
      # 이름 규칙: {EnvName}-{리소스설명}  (예: product-vpc)
      VPC:
        Export:
          Name: !Sub ${EnvName}-vpc
      # ⚠ 오타 주의: Logical ID는 WebSubnet1b/DatastoreSubnet1b인데
      # Output 이름은 WebSubnet1c / DatastoreSubnet1c 로 되어 있음
      # (실제 값은 맞게 나가지만 네이밍이 헷갈릴 수 있음, 향후 수정 권장)
      WebSubnet1c:
        Value: !Ref WebSubnet1b
        Export:
          Name: !Sub ${EnvName}-web-subnet-1b
      DatastoreSubnet1c:
        Value: !Ref DatastoreSubnet1b
        Export:
          Name: !Sub ${EnvName}-datastore-subnet-1b
    ```
    
- Security_Group.yml
    
    ```yaml
    AWSTemplateFormatVersion: "2010-09-09"
    Description: Create Security Group
    # ------------------------------------------------------------#
    # Input Parameters
    # ------------------------------------------------------------# 
    Parameters:
      SystemName:
        Description: "System name of each resource names."
        Type: String
        Default: "gr"
      EnvName:
        Description: "Environment name of each resource names."
        Type: String
        Default: "product"
    # ------------------------------------------------------------#
    #  Create Security Group
    # ------------------------------------------------------------#
    Resources:
      EC2SecurityGroup:
        Type: AWS::EC2::SecurityGroup # 보안 그룹을 생성
        Properties:
          GroupName: !Sub ${SystemName}-${EnvName}-ec2-sg # 보안 그룹 이름을 지정
          GroupDescription: !Sub ${SystemName}-${EnvName}-ec2-sg # 보안 그룹 설명
          SecurityGroupIngress: # 허용할 IP 주소 설정
                - IpProtocol: tcp
                  FromPort: 22
                  ToPort: 22
                  CidrIp: 0.0.0.0/0
          VpcId:
            Fn::ImportValue: !Sub ${EnvName}-vpc
          Tags: 
            - Key: Name
              Value: !Sub ${SystemName}-${EnvName}-ec2-sg
            - Key: Env
              Value: !Sub ${EnvName}
    # ------------------------------------------------------------
    # Output Parameters
    # ------------------------------------------------------------
    Outputs:
    # Security Groups
      EC2SecurityGroup:
        Value: !Ref EC2SecurityGroup
        Export:
          Name: !Sub ${EnvName}-ec2-sg
    ```
    
- EC2.yml
    
    ```yaml
    AWSTemplateFormatVersion: "2010-09-09"
    Description: Create EC2
    
    Parameters:
      SystemName:
        Default: "gr"
      EnvName:
        Default: "product"
      KeyPairName:
        Default: gr-product-ec2-key   # 생성할 키페어 이름
      EC2AMI:
        Default: ami-02d081c743d676996
      InstanceType:
        Default: t3.micro
    
    Resources:
      NewKeyPair:
        Type: AWS::EC2::KeyPair
        Properties:
          KeyName: !Ref KeyPairName
        # 이 리소스로 키페어를 새로 생성함.
        # 프라이빗 키는 AWS Systems Manager Parameter Store에 자동 저장되며
        # (콘솔/CLI에서 따로 받아야 함) 스택을 삭제하면 키페어도 함께 삭제되어
        # 기존에 발급받은 .pem 파일로는 더 이상 접속 불가해질 수 있음.
        # 이미 존재하는 키페어를 쓰려면 KeyPair 타입을 만들지 말고
        # KeyPairName 파라미터 값만 EC2Instance의 KeyName에 직접 참조하면 됨.
    
      EC2Instance:
        Type: AWS::EC2::Instance
        Properties:
          ImageId: !Ref EC2AMI
          InstanceType: !Ref InstanceType
          KeyName: !Ref NewKeyPair          # 위에서 만든 키페어 사용
    
          NetworkInterfaces:
            - DeviceIndex: 0
              SubnetId:
                Fn::ImportValue: !Sub "${EnvName}-public-subnet-1a"
                # VPC 스택에서 export한 Public 서브넷(1a)에 배치
                # 이름 그대로 Public 서브넷에 인스턴스를 올리는 구성
    
              GroupSet:
                - Fn::ImportValue: !Sub
    ```
    
- Backup.yml
    
    ```yaml
    AWSTemplateFormatVersion: "2010-09-09"
    Description: Create AWS Backup
    
    Parameters:
      SystemName:
        Default: "gr"
      EnvName:
        Default: "product"
    
    Resources:
      # ── 백업을 저장할 저장소(Vault) 생성 ──────────────────────
      EC2BackupVault:
        Type: "AWS::Backup::BackupVault"
        Properties:
          BackupVaultName: !Sub ${SystemName}-${EnvName}-bv-ec2
          BackupVaultTags:
            Name: !Sub ${SystemName}-${EnvName}-bv-ec2
            Env: !Sub ${EnvName}
          # 참고: AccessPolicy나 EncryptionKeyArn을 지정하지 않으면
          # AWS 관리형 KMS 키(aws/backup)로 기본 암호화됨. 별도 KMS 키를
          # 쓰고 싶다면 EncryptionKeyArn을 명시해야 함.
    
      # ── 백업 정책(스케줄/보관주기) 정의 ────────────────────────
      EC2BackupPlan:
        Type: AWS::Backup::BackupPlan
        Properties:
          BackupPlan:
            BackupPlanName: !Sub ${SystemName}-${EnvName}-bp-ec2
            BackupPlanRule:
              - RuleName: !Sub ${SystemName}-${EnvName}-bprule-ec2
                TargetBackupVault: !Ref EC2BackupVault
                ScheduleExpression: cron(0 * * * ? *)
                # 매시 정각(1시간마다) 백업 실행
                # → EC2 전체 스냅샷 성격이라 1시간 주기는 상당히 잦은 편.
                #   비용(스토리지)과 실제 RPO 요구사항을 고려해 조정 필요
                #   (보통 EC2는 하루 1회 정도가 일반적)
                ScheduleExpressionTimezone: Asia/Seoul
                StartWindowMinutes: 60       # 예정 시각 이후 60분 내 시작 안되면 실패 처리
                CompletionWindowMinutes: 120 # 시작 후 120분 내 완료 안되면 실패 처리
                Lifecycle:
                  DeleteAfterDays: 7
                  # 보관 기간 7일로 짧은 편. 장애 발생 후 뒤늦게 발견되는
                  # 케이스(예: 며칠 지나서 데이터 손상 인지)를 대비하려면
                  # 운영 환경에서는 더 긴 보관 기간(예: 30일) 고려 권장
                  # 또한 MoveToColdStorageAfterDays 옵션으로 콜드 스토리지
                  # 이전을 추가하면 장기 보관 비용을 절감할 수도 있음
            BackupPlanTags:
              Name: !Sub ${SystemName}-${EnvName}-bp-ec2
              Env: !Sub ${EnvName}
    
      # ── 백업 대상 선택 (태그 기반) ─────────────────────────────
      EC2BackupSelection:
        Type: AWS::Backup::BackupSelection
        Properties:
          BackupPlanId: !Ref EC2BackupPlan
          BackupSelection:
            SelectionName: !Sub ${SystemName}-${EnvName}-selection-ec2
            IamRoleArn: !GetAtt EC2BackupRole.Arn
            ListOfTags:
              - ConditionType: "STRINGEQUALS"
                ConditionKey: Name
                ConditionValue: gr-product-ec2
    
      # ── AWS Backup 서비스가 EC2에 접근할 수 있도록 하는 IAM 역할 ──
      EC2BackupRole:
        Type: "AWS::IAM::Role"
        Properties:
          RoleName: !Sub ${SystemName}-p-role-backup-ec2
          AssumeRolePolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: "Allow"
                Action: "sts:AssumeRole"
                Principal:
                  Service: "backup.amazonaws.com"
                # AWS Backup 서비스만 이 역할을 위임(assume)할 수 있도록 신뢰관계 설정
    
          ManagedPolicyArns:
            - "arn:aws:iam::aws:policy/service-role/AWSBackupServiceRolePolicyForBackup"
            - "arn:aws:iam::aws:policy/service-role/AWSBackupServiceRolePolicyForRestores"
            # AWS 관리형 정책 사용: 백업 생성 권한 + 복원 권한
            # 최소권한 원칙 관점에서는 문제없는 표준적인 구성
    
          Path: "/service-role/"
    ```
    

- 트러블슈팅
    
    ec2 스택 생성 실패
    
    ```
    리소스(EC2Instance)가 CREATE_FAILED 상태입니다
    이 AWS::EC2::Instance 리소스가 CREATE_FAILED 상태입니다
    
    Resource handler returned message: "The image id '[ami-02d081c743d676996]' does not exist (Service: Ec2, Status Code: 400, Request ID: 43e4978a-cbee-4b26-8bfb-e6b830ed60a3) (SDK Attempt Count: 1)" (RequestToken: 7fe5a639-ae8e-4f2f-0b53-2eb409cb2f5f, HandlerErrorCode: InvalidRequest)
    ```
    
    aws cli 다음 명령어 입력해 나온 ami 값으로 코드 교체
    
    ```
    aws ec2 describe-images --owners amazon --filters "Name=name,Values=al2023-ami-*-x86_64" "Name=state,Values=available" --query "sort_by(Images, &CreationDate)[-1].[ImageId,Name]" --output table --region ap-northeast-2
    ```
    
    aws 리전 서울로 변경
    

- 백업 볼트 확인
    

<img width="1122" height="212" alt="Image" src="https://github.com/user-attachments/assets/2b2a8482-6849-4ac3-a3e9-af8a82cb9186" />
    
- 백업 계획
- 백업 규칙
    

<!-- Uploading "image.png"... -->
    
- 백업 상황
- 복구 시점
- 복원 실시
- 복원 결과 확인