# 0.2 AWS 사용 방법 살펴보기

## 0.2.1 AWS 루트 사용자

- **루트 사용자** : AWS 계정 내 모든 AWS 서비스 및 리소스에 액세스할 수 있는 권한을 가지며 AWS 콘솔에 접근할 수 있다.
- 루트 사용자는 AWS 계정 내 모든 권한을 가지므로 보안에 주의해야 한다.

⇒ 보안을 위해 AWS는 루트 사용자에게 추가 보안 계층 역할을 하는 다중 인증 (Multi-factor authentication)을 활성화하는 것을 권장한다.

## 0.2.2 레벨별 AWS를 사용하는 방법

> AWS는 사용자가 다양한 레벨에서 AWS를 활용할 수 있도록 사용 방법을 제공한다.
> 
- 레벨 1 : 사용자 인터페이스(UI) 사용이 가능한 `AWS 관리 콘솔`
    - AWS 관리 콘솔은 UI를 통해 직관적인 사용이 가능하지만 생성한 AWS 리소스가 늘어날수록 관리가 힘들어진다.
- 레벨 2 : `AWS SDK`**와 `AWS CLI`로 스크립트와 터미널을 이용해 AWS 사용 가능
- 레벨 3 : IaC 툴인 `클라우드포메이션`을 이용해 JSON, YAML 형식으로 클라우드 인프라를 정의하여 다수의 AWS 리소스를 생성/관리 가능
- 레벨 4 : `AWS CDK(Cloud Development Kit)`는 사용자가 익숙한 프로그래밍 언어를 사용하여 클라우드 인프라를 정의할 수 있어 더 직관적인 환경을 제공
    - AWS CDK는 특정 프로젝트나 팀의 요구사항에 따라 프로그래밍 언어가 달라지므로 사용하려면 다양한 프로그래밍 경험이 필요하다.

<br>

# 0.3 비주얼 스튜디오 코드로 클라우드포메이션 템플릿 관리하기

> 레벨 3의 클라우드포메이션 사용 환경을 구축해보자.
> 
- 비주얼 스튜디오 코드를 사용하면 클라우드포메이션 확장 프로그램인 클라우드포메이션 린터를 설치 가능
- 클라우드 포메이션 린터 : JSON, YAML 문법을 검사하여 클라우드포메이션 템플릿 형식 유지를 도와줌

## 0.3.2 윈도우에서 클라우드포메이션 템플릿 관리하기

1. 비주얼 스튜디오 코드 다운로드
2. 파이썬 다운로드 https://www.python.org/downloads/windows/
3. 비주얼 스튜디오 코드 터미널에서 pip를 이용해 클라우드포메이션 린터 설치
    

    
    - 로컬 환경에 파이썬 설치 확인
    - 파이썬 패키지인 pip를 최신 버전으로 업그레이드
    - pip를 이용해 클라우드포메이션 린터 설치
    <img width="556" height="348" alt="Image" src="https://github.com/user-attachments/assets/6e68a5a2-5a36-424e-ab1c-8ac7dde9ccb7" />
    <img width="305" height="65" alt="Image" src="https://github.com/user-attachments/assets/154082c6-a548-4af5-9679-19bf09c3485a" />
   
4. 비주얼 스튜디오 코드에 CloudFormation Linter extension 설치

   <img width="528" height="152" alt="Image" src="https://github.com/user-attachments/assets/ddd7ceca-59c8-4880-8e22-ad7457ae4930" />

    
<br>

# 0.5 클라우드포메이션 사용해보기

> 클라우드포메이션으로 아마존 VPC를 생성해보자.
> 
- **아마존 VPC(Amazon Virtual Private Cloud)** : 클라우드의 네트워크 환경을 구축하고 관리할 수 있는 서비스

1. AWS 관리 콘솔에서 CloudFormation 검색
    - 클라우드포메이션 콘솔 화면에서 스택 생성 화면으로 이동하며 스택 생성, 관리 가능
    - 스택 생성 클릭하거나 오른쪽의 스택 생성 → 새 리소스 사용(표준)으로 스택 생성
    
    <img width="955" height="432" alt="Image" src="https://github.com/user-attachments/assets/31f0f35b-2ce2-4c8a-b61b-3755559731fb" />

    
2. 기존 템플릿 선택 및 템플릿 파일 업로드를 선택해 VPC.yml 업로드
 
    <img width="1193" height="727" alt="Image" src="https://github.com/user-attachments/assets/d3b60a52-7fd2-4ff0-b4a5-bdb5d3b57444" />

    
3. 스택 세부 정보 지정에서 정보 입력
    - 클라우드포메이션 템플릿은 AWS에서 스택이라는 이름으로 생성되어 관리됨
    - 클라우드포메이션은 템플릿에서 정의되며 파라미터의 각 항목에서 사용자 지정 값 입력 가능
        - VPC의 CIDR, 비밀번호, 시스템 이름, 환경 이름과 같이 데이터를 코드에 직접 입력해야 하는 하드코딩이 필요한 값은 파라미터를 통해 관리 → 코드를 더 간결하게 구성
    
    
    <img width="917" height="796" alt="Image" src="https://github.com/user-attachments/assets/680cbbf3-0b89-437a-a10b-16e52776c5a2" />

    
4. 스택 옵션 구성의 각 항목은 기본값 유지
    
    
    <img width="1248" height="743" alt="Image" src="https://github.com/user-attachments/assets/4fffe866-546c-4c0c-94b0-99f171005180" />

    
5. 생성할 템플릿을 확인하고 클라우드포메이션 스택을 생성
    - 정보 확인 후 전송 버튼 클릭
    
   
    <img width="1272" height="736" alt="Image" src="https://github.com/user-attachments/assets/08606450-dced-478e-a338-5747e3bc625c" />

    
    
    <img width="1236" height="386" alt="Image" src="https://github.com/user-attachments/assets/7bfeb5fd-00c8-4910-87bd-b1ab3dd6ec7c" />

    
6. 클라우드포메이션 스택 생성 확인
    - CREATE_IN_PROGRESS → CREATE_COMPLETE로 변경되는 스택 생성 과정 확인
    - 이벤트 탭에서 생성된 리소스 확인
    
    
    <img width="1898" height="587" alt="Image" src="https://github.com/user-attachments/assets/9c33fe59-c55c-4c93-8ff1-2e5d72e509a1" />

    
7. 생성된 리소스를 확인하기 위해 VPC 콘솔 화면으로 이동
    - 왼쪽 카테고리에서 VPC, 서브넷, 라우팅 테이블 등 다양한 AWS 네트워크 서비스 확인 가능
    - VPC 클릭 시 클라우드포메이션으로 생성된 VPC 확인 가능
    
   
    <img width="1912" height="359" alt="Image" src="https://github.com/user-attachments/assets/c606f941-1a58-41df-ad7a-c4ee57001e17" />

    
8. 생성된 클라우드포메이션 스택 삭제 
    - 스택 삭제는 클라우드포메이션 스택 화면에서 진행
    - 상태가 DELETE_IN_PROGRESS로 변경되며 완료되면 목록에서 스택이 사라짐
    
    
   <img width="1199" height="588" alt="Image" src="https://github.com/user-attachments/assets/c5af830b-5d34-41b0-acb5-af129df3d579" /> 

    
    - 실수로 인한 스택 삭제를 방지하기 위해 종료 방지 기능도 지원한다.
        - [스택 작업] → [종료 방지 편집]
<br>

# 0.6 AWS에서 생성형 AI 활용 방안

실무에서 생성형 AI를 활용하는 방법으로 크게 2가지로 나눠볼 수 있다.

- 코드 작성
- 솔루션과 트러블 슈팅

## 0.6.1 코드 작성

- AWS에서는 인프라 구축을 위한 IaC 코드나 IAM 권한 설정을 위한 JSON 코드를 작성해야 하는 경우가 많다.
- AWS는 클라우드포메이션에서 사용할 수 있는 각 서비스의 기본 예시 코드를 제공하지만, 처음 AWS를 접하는 사용자에게는 다소 어렵게 느껴질 수 있다.

⇒ 생성형 AI를 활용하면, 필요한 코드 조각을 빠르고 효율적으로 생성/수정 가능해 업무 생산성을 높일 수 있다.

### 생성형 AI를 활용해 IaC 코드 작성하는 법

- 클라우드포메이션 코드를 YAML 언어로 작성해달라고 요청한다.
- 각 리소스의 이름 규칙을 명확하게 전달한다.
- 가용 영역을 하드코딩하지 말고 동적으로 설정하도록 부탁한다.
- 필요한 최소한의 파라미터(네트워크 CIDR)를 안내한다.

```
// 예시 프롬프트
다음 파라미터를 이용해서 CloudFormation 코드 (YAML)를 작성해줘

각 리소스의 이름은 {EnvName}-{SystemName}-resourcename
ex) {EnvName}-{SystemName}-vpc

AvailabilityZone 값을 !Select와 !GetAZs를 활용해 하드코딩 없이 동적으로 설정하도록 해줘

Region : seoul
VPC CIDR : 10.0.0.0/16
Public Subnet1 CIDR : 10.0.1.0/24
Public Subnet2 CIDR : 10.0.2.0/24
Private Subnet1 CIDR : 10.0.11.0/24
Private Subnet2 CIDR : 10.0.12.0/24
EnvName : gr
SystemName : prd
```

- Gemini의 답변 결과
    
    ```yaml
    AWSTemplateFormatVersion: '2010-09-09'
    Description: VPC Network Infrastructure with Dynamic AZ Selection
    
    Parameters:
      EnvName:
        Type: String
        Default: gr
      SystemName:
        Type: String
        Default: prd
      VpcCidr:
        Type: String
        Default: 10.0.0.0/16
      PubSub1Cidr:
        Type: String
        Default: 10.0.1.0/24
      PubSub2Cidr:
        Type: String
        Default: 10.0.2.0/24
      PriSub1Cidr:
        Type: String
        Default: 10.0.11.0/24
      PriSub2Cidr:
        Type: String
        Default: 10.0.12.0/24
    
    Resources:
      # --- VPC ---
      VPC:
        Type: AWS::EC2::VPC
        Properties:
          CidrBlock: !Ref VpcCidr
          EnableDnsSupport: true
          EnableDnsHostnames: true
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-vpc"
    
      # --- Internet Gateway ---
      InternetGateway:
        Type: AWS::EC2::InternetGateway
        Properties:
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-igw"
    
      VPCGatewayAttachment:
        Type: AWS::EC2::VPCGatewayAttachment
        Properties:
          VpcId: !Ref VPC
          InternetGatewayId: !Ref InternetGateway
    
      # --- Public Subnets ---
      PublicSubnet1:
        Type: AWS::EC2::Subnet
        Properties:
          VpcId: !Ref VPC
          CidrBlock: !Ref PubSub1Cidr
          # 서울 리전의 첫 번째 가용 영역 선택
          AvailabilityZone: !Select [ 0, !GetAZs '' ]
          MapPublicIpOnLaunch: true
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-pub-sub-1"
    
      PublicSubnet2:
        Type: AWS::EC2::Subnet
        Properties:
          VpcId: !Ref VPC
          CidrBlock: !Ref PubSub2Cidr
          # 서울 리전의 두 번째 가용 영역 선택
          AvailabilityZone: !Select [ 1, !GetAZs '' ]
          MapPublicIpOnLaunch: true
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-pub-sub-2"
    
      # --- Private Subnets ---
      PrivateSubnet1:
        Type: AWS::EC2::Subnet
        Properties:
          VpcId: !Ref VPC
          CidrBlock: !Ref PriSub1Cidr
          AvailabilityZone: !Select [ 0, !GetAZs '' ]
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-pri-sub-1"
    
      PrivateSubnet2:
        Type: AWS::EC2::Subnet
        Properties:
          VpcId: !Ref VPC
          CidrBlock: !Ref PriSub2Cidr
          AvailabilityZone: !Select [ 1, !GetAZs '' ]
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-pri-sub-2"
    
      # --- Route Tables ---
      PublicRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
          VpcId: !Ref VPC
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-pub-rt"
    
      PublicRoute:
        Type: AWS::EC2::Route
        DependsOn: VPCGatewayAttachment
        Properties:
          RouteTableId: !Ref PublicRouteTable
          DestinationCidrBlock: 0.0.0.0/0
          GatewayId: !Ref InternetGateway
    
      PrivateRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
          VpcId: !Ref VPC
          Tags:
            - Key: Name
              Value: !Sub "${EnvName}-${SystemName}-pri-rt"
    
      # --- Route Table Associations ---
      PublicSubnet1Association:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
          SubnetId: !Ref PublicSubnet1
          RouteTableId: !Ref PublicRouteTable
    
      PublicSubnet2Association:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
          SubnetId: !Ref PublicSubnet2
          RouteTableId: !Ref PublicRouteTable
    
      PrivateSubnet1Association:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
          SubnetId: !Ref PrivateSubnet1
          RouteTableId: !Ref PrivateRouteTable
    
      PrivateSubnet2Association:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
          SubnetId: !Ref PrivateSubnet2
          RouteTableId: !Ref PrivateRouteTable
    
    Outputs:
      VpcId:
        Value: !Ref VPC
        Export:
          Name: !Sub "${EnvName}-${SystemName}-VpcId"
    ```
    
- 사용자 요구에 맞춘 코드를 작성하려면 보다 구체적인 요구사항을 명확히 하는 것이 필요하다.
- 실제로 코드가 제대로 동작하는지 테스트하고 검증해야 하며, 충분한 검토가 반드시 필요하다.

## 0.6.2 솔루션과 트러블 슈팅

인프라 환경에서는 ‘완벽한 상태’를 추구해야 하므로, 끊임없이 발생하는 트러블슈팅에 신속히 대응하고 문제를 최소화하는 것이 중요하다.

> 솔루션 설계나 트러블슈팅에 퍼플렉시티를 자주 활용한다.
> 
- AWS 공식 문서를 일일이 확인하지 않고도 생성형 AI를 활용해 더 효율적이고 신속하게 정보를 찾아볼 수 있다.
    - 반드시 검증 과정이 필요하다.
