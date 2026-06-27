# 16장. 유연한 NoSQL 데이터베이스 서비스 파악하기
 
# 1. 아마존 다이나모DB란?
 
### NoSQL 특징
 
- 확장성
- 유연한 스키마
- 고성능
- 분산 처리
- 빅데이터 처리에 적합
### 다이나모DB 장점
 
- 완전 관리형: 인프라 관리를 하지 않아도 됨
- 고가용성 및 확장성: 자동으로 수평 확장 가능
- 무제한 스케일링: 테이블 크기 제한 X, 성능에 영향 주지 않고 대규모 트래픽 처리 가능
- 초저지연성: 읽기/쓰기 작업에서 매우 낮은 지연 시간
- 내구성: 세 개의 가용 영역에 자동으로 데이터 복제
- 보안 통합: AWS IAM과 통합되어 세부적인 보안 관리
- 서버리스 모델: 용량 계획 없이 사용한 만큼만 비용 지불
다이나모DB는 VPC 설계 없이도 생성할 수 있으며, 리전 단위로 관리된다.
 
운영에서도 OS 패치 적용과 같은 정기 유지보수는 AWS에서 직접 처리하며, 데이터의 용량과 트래픽이 증가하더라도 퍼포먼스가 떨어지는 일은 거의 없다. → 별도의 시스템 운용 부담이 적음
 
# 2. 아마존 다이나모DB 살펴보기
 
## 2.1 아마존 다이나모DB 제약
 
다이나모DB는 키밸류 형태의 NoSQL이므로 OLAP(Online Analytical Processing) 데이터 분석 처리에는 적합하지 않다.
 
> **OLAP(Online Analytical Processing)**: 데이터를 다차원적으로 분석하고 그 결과를 신속하게 사용자에게 반환하는 기법 또는 도구, ex) 시장 분석, 성과 관리
> ⇒ 대규모 데이터 분석에 적합
 
OLTP(On Line Transaction Processing)는 소규모 데이터를 대량으로 처리하는 데 탁월한 기술이며, 다이나모DB에 적합하다.
 
> **OLTP(On Line Transaction Processing)**: 주로 트랜잭션 처리 및 실시간 데이터 읽기 및 쓰기에 중점을 둔 작업
 
OLTP는 쇼핑몰과 같은 EC 사이트에서 구매를 처리하는 데 사용되며, 상품의 구매로부터 결제, 발송 수속 등 일련의 처리를 실시할 수 있다.
 
다이나모DB는 항목당 크기 상한이 400KB이므로 큰 데이터를 직접 저장하는 용도로 적합하지 않다.
 
### 다이나모DB 제약사항
 
- 트랜잭션 제약
  - RDS에 비하면 단일 트랜잭션 내 개별 작업 최대 처리양이 적다.
- 검색 조건의 유연성 제약
  - 다이나모DB는 두 가지 유형의 보조 인덱스를 지원한다.
    글로벌 보조 인덱스로 많은 검색 패턴에 대응 가능하지만, SQL에 비해 유연성이 부족하다.
  - 다이나모DB는 인덱스 구조에 크게 의존하므로 검색 조건의 유연성에 제약이 있다.
- JOIN 제약
  - 다이나모DB로 복수 테이블을 취급할 수도 있지만, 테이블 간의 JOIN은 불가능
  - 단일 테이블에 항목을 정리하는 싱글 테이블으로 설계하는 것이 권장된다.
## RDS vs DynamoDB 비교
 
| 구분 | Amazon RDS | Amazon DynamoDB |
|---|---|---|
| 유형 | 관계형 데이터베이스 (RDBMS)<br>MySQL, PostgreSQL, MariaDB, Oracle, SQL Server 등 지원 | NoSQL 데이터베이스 (Key-Value / Document 모델) |
| 검색조건 | SQL 기반으로 컬럼, 조인, 서브쿼리 등 자유로운 조건 검색 가능 | 기본적으로 Partition Key(+Sort Key) 기반 조회가 핵심. 복잡한 조건 검색은 GSI/LSI(보조 인덱스)를 설계해둬야 가능 |
| 검색 처리 | 쿼리 옵티마이저가 실행 계획을 수립, 복잡한 JOIN·집계·트랜잭션 처리에 강함 | 인덱스를 직접 조회하는 방식이라 매우 빠르고 일정한 응답속도(ms 단위) 제공. 단, JOIN 미지원, 복잡한 집계는 별도 처리 필요 (예: Lambda, Streams) |
| 확장성 | 수직 확장(인스턴스 사양 업그레이드) 중심, 읽기 확장은 Read Replica로 가능. 쓰기 확장은 제한적(샤딩 직접 구현 필요) | 수평 확장(파티션 자동 분산)에 최적화, 트래픽에 따라 자동/온디맨드 확장 가능. 대규모 쓰기/읽기 트래픽에 유리 |
| 사용사례 | 트랜잭션이 중요한 시스템 (결제, 주문, 재고관리), 복잡한 조인/리포팅이 필요한 업무 시스템, ERP/CRM | 대규모 트래픽의 단순 조회 패턴 (세션 스토어, 게임 리더보드, IoT 센서 데이터, 실시간 추천/장바구니), 빠르고 일관된 지연시간이 중요한 서비스 |
 
## 2.2 아마존 다이나모DB 구성요소
 
### 파티션과 키
 
다이나모DB 데이터는 파티션을 저장하고 분산하는 단위를 의미한다.
 
데이터는 여러 파티션에 분산되어 저장되는데 파티션 키를 바탕으로 어느 파티션에 저장될지 결정된다.
 
정렬 키가 설정된 경우, 데이터는 파티션 내에서 정렬 키 기준으로 정렬되어 물리적으로 가깝게 배치된다.
 
### 보조 인덱스
 
기본키는 파티션 키 또는 파티션 키와 정렬 키의 복합키이다.
 
- 글로벌 보조 인덱스, 로컬 보조 인덱스 기능 제공
  - 테이블의 파티션 키와 정렬 키만으로 충분하지 않을 경우 다른 파티션 키와 정렬 키를 설정
  - 글로벌 보조 인덱스는 테이블 작성 후 설정, 로컬 보조 인덱스는 테이블 생성 시 설정
### 다이나모DB 엑셀러레이터
 
다이나모DB 엑셀러레이터는 다이나모DB와 호환되는 완전 관리형 인메모리 캐시 서비스이다.
 
밀리초에서 마이크로초로 퍼포먼스를 올려 고속화할 수 있다.
 
**동작 과정**
 
1. 클라이언트가 다이나모DB 엑셀러레이터로 요청 전송
2. 다이나모DB 엑셀러레이터에 캐시가 있다면 결과를 클라이언트로 반환, 없다면 다이나모DB에 요청
3. 다이나모DB로부터 요청받은 결과를 다이나모DB 엑셀러레이터에 보존, 결과를 클라이언트로 반환
### 용량 모드
 
- 온디맨드 모드
  - 읽기 및 쓰기 용량을 설정하지 않음, 자동으로 용량 스케일링하여 트래픽 처리
    → 트래픽 예측할 수 없을 때 유용
- 프로비저닝 모드
  - 오토스케일링 기능 사용 가능. 일정 시간 내 네트워크상에서 전송되는 데이터량의 변화에 따라 용량 자동 조절
  - 읽기 및 쓰기 용량 설정, 설정한 용량을 상한으로 트래픽 처리
    → 트래픽 예측 가능할 때 유용
### 가용성과 내구성
 
다이나모DB는 다중 AZ를 지원하며, 같은 리전 내 3개의 가용 영역간 데이터가 실시간으로 복제된다.
 
글로벌 테이블을 생성해 다중 리전에 걸쳐 테이블을 복제하고 빠르게 읽거나 쓸 수 있으며 높은 가용성을 제공한다.
 
글로벌 테이블을 사용하면 자체적으로 복제 솔루션을 구축/관리할 필요 없이 각 리전에 같은 테이블을 생성하고 데이터 변경 내용을 모든 리전에 전파할 수 있다.
 
→ 대규모 애플리케이션에 적합, 리전에 상관없이 낮은 대기 시간으로 데이터 제공
 
# 3. 아마존 다이나모DB 활용하기
 
- Lambda.yml
```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Create API Gateway & Lambda
 
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
# Create Lambda
# ------------------------------------------------------------#
Resources:
  GetDataLambdaFunction:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: !Sub ${SystemName}-${EnvName}-GetData
      Code:
        ZipFile: |
          import boto3
          dynamodb = boto3.resource('dynamodb')
          table = dynamodb.Table("gr-product-db")  # 조회할 DynamoDB 테이블명
          def lambda_handler(event, context):
              response = table.scan()  # 테이블 전체 데이터 조회
              return response['Items']
      Handler: index.lambda_handler  # 인라인 코드의 진입점
      Role: !GetAtt LambdaIAMRole.Arn
      Runtime: python3.12
      Timeout: 30
 
  LambdaIAMRole:
    Type: AWS::IAM::Role
    DeletionPolicy: Delete
    Properties:
      RoleName: !Sub ${SystemName}-${EnvName}-dynamodbrole
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: "Allow"
            Principal:
              Service:
                - "lambda.amazonaws.com"  # Lambda가 이 역할을 사용
            Action:
              - "sts:AssumeRole"
      Path: "/"
      ManagedPolicyArns:
        - "arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess"  # DynamoDB 전체 액세스 권한
        - "arn:aws:iam::aws:policy/service-role/AWSLambdaDynamoDBExecutionRole"  # DynamoDB Streams 실행 권한
```
 
- DynamoDB.yml
```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Create API Gateway & Lambda
 
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
# Create REST API
# ------------------------------------------------------------#
Resources:
  MyDynamoDBTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Sub ${SystemName}-${EnvName}-db
      AttributeDefinitions:
        - AttributeName: id  # 파티션 키
          AttributeType: S    # 문자열 타입
      KeySchema:
        - AttributeName: id
          KeyType: HASH        # 파티션 키 지정
      BillingMode: PAY_PER_REQUEST  # 온디맨드 모드
      Tags:
        - Key: Name
          Value: !Sub ${SystemName}-${EnvName}-dynamoDB
        - Key: Env
          Value: !Sub ${EnvName}
```
 