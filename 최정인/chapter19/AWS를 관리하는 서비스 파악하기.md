 **19.1 AWS를 관리하는 서비스 유형 파악하기**

AWS 환경을 구축한 후 안정적으로 유지하고 관리하기 위해 다양한 서비스가 제공된다.

주요 서비스는 아래와 같다.

- 백업 및 복원 관리를 위한 **AWS 백업**
- 인프라 관리를 위한 **AWS 시스템 관리자**    
- 모니터링 및 로깅을 위한 **AWS 클라우드워치**
- 외부 공격 방어를 위한 **AWS WAF**
- 네트워크 트래픽 로깅을 위한 **VPC 플로우 로그**
- 네트워크 구성 효율화를 위한 **관리형 접두사 목록**

이러한 서비스들을 통해 AWS 환경을 효율적으로 관리하고 안정적으로 운영할 수 있다.

### **19.2 백업 및 복원 관리 서비스 파악하기**
<img width="320" height="320" alt="image (1)" src="https://github.com/user-attachments/assets/d6aa8c26-6eb2-4384-8483-c69fd2701ab1" />

EC2 인스턴스가 중지되거나 사용 불가능한 상태가 될 경우, 신속하게 새 인스턴스를 생성하더라도 내부 데이터 복구에는 많은 시간이 소요된다. 이는 서비스 사용자에게 큰 불편을 초래할 수 있다. **AWS 백업**은 이러한 문제를 해결하기 위해 유용한 서비스로, EC2 인스턴스 및 다른 다양한 AWS 리소스의 백업과 복원을 빠르고 효율적으로 지원한다. 

#### **19.2.1 백업 및 복원 관리를 위한 서비스, AWS 백업이란?**


**AWS 백업**을 사용하면 EC2 인스턴스를 수동으로 새로 생성하고 데이터를 복원하는 것보다 훨씬 빠르게 복원할 수 있다. 백업 시간대를 지정하여 매일 특정 시간에 자동 백업이 수행되도록 설정할 수 있으며, 사용자가 원하는 시점으로 복원이 가능하다. 예를 들어, 바이러스 감염 시 감염 전 시점으로 복원할 수 있다. AWS 백업은 EC2 외에도 Amazon RDS, Amazon EBS, Amazon S3, Amazon Aurora, AWS CloudFormation 등 다양한 서비스를 백업 및 복원할 수 있어 클라우드 환경의 안정적인 운영에 필수적이다.

#### **RPO와 RTO**

백업 전략을 세울 때 가장 중요한 두 가지 지표가 있다:

- RPO (Recovery Point Objective): 데이터 복구 시점
- RTO (Recovery Time Objective): 서비스 복구 소요 시간

이 두 지표를 기준으로 백업 주기, 보존 기간, 스토리지 계층 정책이 결정된다.

#### **19.2.2 백업 및 복원 관리 서비스, AWS 백업 살펴보기**

AWS 백업은 크게 **백업 볼트(Backup Vault)** 와 **백업 계획(Backup Plan)** 두 가지 구성으로 이루어져 있다.

1. **Backup Vault** 
    - 백업된 데이터를 안전하게 저장하는 암호화된 보관소.
    - EC2 인스턴스 백업 시 생성되는 AMI(Amazon Machine Image)와 스냅샷을 정리하여 관리한다
    - KMS(Key Management Service) 암호화를 설정하여 데이터를 일관되게 보호할 수 있다.
    - 2020년 이후에는 Amazon EFS뿐만 아니라 Amazon S3, AWS CloudFormation, Amazon DynamoDB 등 다양한 서비스의 암호화된 백업을 지원한다.
2. **Backup Plan**
    - 백업 작업의 시간대와 주기를 정의하고 실행하며, 백업 볼트 내에 복구 지점을 생성한다.
    - 태그 기반으로 동적으로 할당할 수도 있어, 리소스가 늘어나도 자동 적용이 가능하다.

**특정 시점 복구(PITR, Point-in-Time Recovery)** 기능도 지원하며, 사용자가 특정 날짜와 시간을 정해 원하는 시점으로 복원할 수 있다. 최대 35일 이전으로 1초 이내의 정확도로 복원이 가능하며, 현재는 Amazon RDS와 Amazon S3만 지원된다. 

백업할 리소스는 **[모든 리소스 유형 포함]** 또는 **[특정 리소스 유형 포함]** 옵션을 통해 지정할 수 있다. 특정 리소스 유형을 선택하거나, 특정 리소스 ID를 제외하거나, 태그를 기준으로 특정 태그를 사용하는 리소스만 백업할 수도 있다. 

결론적으로, AWS 백업은 **백업 볼트**와 **백업 계획**으로 구성되어 EC2, RDS, DynamoDB 등 다양한 AWS 리소스를 체계적으로 백업한다. 이를 통해 데이터 손실에 대비하고 데이터를 안전하게 보호하며, 신속한 복구를 가능하게 하여 클라우드 환경을 안정적으로 운영할 수 있도록 돕는다.

| **서비스 분류** | **지원 리소스** | **설명** |
| --- | --- | --- |
| 컴퓨팅 (Compute) | EC2, EBS | EC2 인스턴스 및 EBS 볼륨 스냅샷 |
| 데이터베이스 (Database), 애널리틱스 (Analytics) | RDS, DocumentDB, Redshift, DynamoDB, Neptune | 데이터베이스별 자동 백업 스케쥴 및 시점 복구, 클러스터 및 스냅샷 백업 |
| 스토리지 (Storage) | EFS, FSx(Windows, Lustre, OpenZFS, ONTAP), S3 | 파일 시스템 및 객체 스토리지 백업 (일부 리전 한정) |
| 기타 (ETC) | AWS CloudFormation Template, Amazon Timestream, AWS Storage Gateway, Amazon EC2 인스턴스의 SAP HANA 데이터베이스 … | 인프라 상태 및 리소스 구성 백업 및 특수 목적의 서비스 백업 |

### **19.3 AWS 백업 활용하기**

AWS 백업을 활용하여 EC2 인스턴스를 백업하고 복원하는 실습이다. 백업 볼트와 백업 계획은 CloudFormation을 통해 생성할 수 있다. 복원 작업은 AWS 콘솔에서 수행해야 한다.

#### **19.3.1 AWS 백업을 활용해 EC2 인스턴스 백업 및 복원해보기**

**CloudFormation으로 백업 볼트 및 백업 계획 생성 예시:**

- **백업 볼트 생성**: `Type: "AWS::Backup::BackupVault"`를 사용하여 백업 볼트 이름과 태그를 지정한다.
    
    ```yaml
    EC2BackupVault:Type:"AWS::Backup::BackupVault"Properties:BackupVaultName:!Sub ${SystemName}-${EnvName}-bv-ec2BackupVaultTags:Name:!Sub ${SystemName}-${EnvName}-bv-ec2Env:!Sub ${EnvName}
    ```
    
- **백업 계획 생성**: `Type: AWS::Backup::BackupPlan`을 사용하여 백업 계획을 생성하고, `BackupPlanRule`에 백업 규칙을 설정한다. 여기에는 백업 볼트(`TargetBackupVault`), Cron 표현식을 사용한 백업 시간대(`ScheduleExpression`), 시간대(`ScheduleExpressionTimezone`), 시작 및 완료 시간(`StartWindowMinutes`, `CompletionWindowMinutes`), 보존 기간(`Lifecycle`) 등이 포함된다.
    
    ```yaml
      EC2BackupPlan:
        Type: AWS::Backup::BackupPlan
        Properties:
          BackupPlan:
            BackupPlanName: !Sub ${SystemName}-${EnvName}-bp-ec2
            BackupPlanRule:
              - RuleName: !Sub ${SystemName}-${EnvName}-bprule-ec2
                TargetBackupVault: !Ref EC2BackupVault
                ScheduleExpression: cron(0 * * * ? *)
                ScheduleExpressionTimezone: Asia/Seoul
                StartWindowMinutes: 60
                CompletionWindowMinutes: 120
                Lifecycle:
                  DeleteAfterDays: 7
          BackupPlanTags:
            Name: !Sub ${SystemName}-${EnvName}-bp-ec2
            Env: !Sub ${EnvName}
    ```
    
- **백업 리소스 지정**: `Type: AWS::Backup::BackupSelection`을 사용하여 백업할 리소스를 지정한다. 여기에는 `BackupPlanId`, `SelectionName`, `IamRoleArn` (백업 및 복원 권한을 가진 IAM 역할), `ListOfTags` (특정 태그를 가진 리소스를 대상으로 함) 등이 포함된다.
    
    ```yaml
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
    ```
    

#### **19.3.2 UI로 불러와 EC2 인스턴스 백업 및 복원해보기**

1. **클라우드포메이션 스택 생성**: 백업에 필요한 모든 자원(백업 볼트, 백업 계획 등)을 기본값으로 CloudFormation 스택을 생성한다. 
2. **생성된 백업 볼트 확인**: AWS 백업 콘솔에서 `[볼트]` 메뉴로 이동하여 생성된 백업 볼트(`gr-product-bv-ec2`)와 상세 정보를 확인한다.<img width="980" height="610" alt="스크린샷 2026-07-04 오전 10 01 31" src="https://github.com/user-attachments/assets/4edd6697-1182-4cca-9205-987cb0bc1bdb" />

3. **생성된 백업 계획 확인**: `[백업 계획]` 메뉴로 이동하여 생성된 백업 계획(`gr-product-bp-ec2`)과 백업 규칙을 확인한다. 백업 빈도, 시작 시간, 보존 기간 등의 상세 정보를 볼 수 있다.    <img width="1047" height="449" alt="스크린샷 2026-07-04 오전 10 02 54" src="https://github.com/user-attachments/assets/f94e918e-f8be-46c2-ac8f-46708bff7a1c" />

4. **할당된 리소스 확인**: 백업 계획 내 `[리소스 할당]`을 클릭하여 어떤 리소스(`gr-product-selection-ec2`)가 백업 대상인지 확인한다. 이 할당은 특정 태그를 가진 EC2 인스턴스를 대상으로 한다.   <img width="554" height="195" alt="스크린샷 2026-07-04 오전 10 03 27" src="https://github.com/user-attachments/assets/4f840edf-0659-4b54-a0eb-6a75f02dac40" />

5. **백업 상황 확인**: `[작업]` 메뉴에서 백업 작업이 수행되고 `[완료됨]` 상태인지 확인한다. EC2 콘솔에서 `AMI(Amazon Machine Image)` 목록으로 이동하면 생성된 AMI를 확인할 수 있으며, 이를 바탕으로 EC2 인스턴스를 복원할 수 있다.  백업 작업은 한 시간마다 진행될 수 있다.
6. **복구 시점 확인 및 복원**: AWS Backup 콘솔 `[보호된 리소스]`에서 `[복구 시점]`을 확인한다. 지정된 시간대에 백업이 완료된 복구 시점을 클릭하여 `[복원]`을 진행한다. 복원 설정 시 `[기본 역할]`을 선택하고 `[보호된 리소스 태그]`를 체크하여 백업된 EC2 인스턴스와 동일한 태그를 갖도록 설정한다. 마지막으로 `[백업 복원]`을 클릭하여 복원 작업을 시작한다. 
7. **복원 결과 확인**: `[복원 작업]`에서 복원 상태가 `[완료됨]`으로 표시되고, EC2 콘솔의 인스턴스 목록에서 복원된 EC2 인스턴스를 확인할 수 있다. 복원은 백업된 결과물과 동일한 리소스를 생성하는 것을 의미한다. 복원 후 불필요한 기존 리소스는 삭제하거나 중지하여 추가 비용이 발생하지 않도록 관리해야 한다.
