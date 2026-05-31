# 15장. API 관리 백엔드 서비스, 아마존 API 게이트웨이란?

API(Application Programming Interface): 소프트웨어나 애플리케이션 기능의 일부를 외부에 공개하는 것으로 프로그래밍으로 애플리케이션을 연결하는 것

API 게이트웨이를 사용하면 AWS 람다와 연계해 다양한 이벤트 처리를 수행할 수 있다.

1. S3에서 웹 호스팅
2. 다이나모DB에 웹 호스팅에 필요한 정보 저장
3. 웹 호스트에 필요한 데이터를 불러오거나, 저장하는 이벤트를 처리할 때 API 게이트웨이를 사용해 API를 생성하고, AWS 람다 함수를 호출해 작업 처리

# 1. 아마존 API 게이트웨이 살펴보기

API 게이트웨이를 사용하지 않는다면, API를 요청하는 애플리케이션은 각 API와 개별적으로 통신한다.

하나의 애플리케이션에서 복수의 API 요청이 필요해서 API 관리가 어렵다.

→ API 게이트를 사용하면, 애플리케이션은 API 게이트웨이와 통신, API 게이트웨이는 API를 제공하는 애플리케이션/서비스와 통신

# 2. 아마존 API 게이트웨이 장단점

### 장점

- 효율적인 API 개발
- 간편한 모니터링
    - API 호출 정보, 오류율, 데이터 대기 시간 등 실시간 모니터링
    - 클라우드워치와 함께 사용하면 데이터 시각적으로 확인 → API 성능 추적
- 비용 절감
- 생산성
    - 클라우드프론트와 통합되어 글로벌 에지를 활용해 최적의 대기 시간으로 API 요청, 응답 처리

### 단점

- 지속 가능성, 신뢰성이 떨어짐
    - API 게이트웨이에 문제가 발생하면 전체 API 장애
- API 보안에 개발자가 직접 관여 불가
    - 다른 서비스나 애플리케이션의 API를 사용하는 경우

# 3. 아마존 API 게이트웨이 구성요소

## REST API

REST(Representational State Transfer) 규칙으로 만든 API

- 주소 가능성
    - 제공하는 정보가 URI를 통해 표현될 수 있음
    - 각 정보는 고유한 URI를 가지며 해당 URI를 사용해 특정 정보에 접근
- 상태 비저장
    - 정보를 교환할 때 상태를 유지하지 않고 요청과 응답을 한 번만 사용해 완료하는 것
- 상태 저장
    - 요청과 응답 간의 상태를 유지해 연속적인 상호작용을 가능하게 함
- 연결성
    - 정보 내에 다른 리소스에 대한 링크가 포함되어 있음
- 통일 인터페이스
    - 미리 정의한 공유 방식
    - 통일 메서드를 사용해 정보 조작
    - HTTP 메서드

REST API 프라이빗은 REST API와 동일하지만 VPC 내에서만 접근할 수 있도록 제한된 API이다.

## HTTP API

HTTP 프로토콜을 이용해 API를 구현한 것

REST API는 HTTP API의 한 종류로 주소 가능성, 상태 비저장, 연결성, 통일된 인터페이스 원칙을 따른다.

AWS API 게이트웨이에서 제공하는 HTTP API 옵션으로 REST API에 비해 대기 시간이 짧으며, 비용 효율성이 중요할 때 유용하다.

## 웹소켓 API

웹소켓은 양방향 통신 프로토콜이다. 통신이 설정된 이후 서버와 클라이언트 간 자유롭게 데이터를 교환할 수 있다. 하나의  TCP 연결로 통신할 수 있다.

HTTP에 비해 헤더가 작기 때문에 통신 효율이 좋다.

# 4. 아마존 API 게이트웨이 활용하기

- APIGateway.yml
    
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
    #  Create Lambda Role
    # ------------------------------------------------------------#
    Resources : 
      LambdaIAMRole:
        Type: AWS::IAM::Role
        DeletionPolicy: Delete
        Properties:
          RoleName: !Sub ${SystemName}-${EnvName}-lambdarole
          # AWS Lambda 서비스가 이 역할을 수임(Assume)할 수 있도록 신뢰 관계 설정
          AssumeRolePolicyDocument: 
            Version: "2012-10-17"
            Statement: 
              - Effect: "Allow"
                Principal: 
                  Service: 
                    - "lambda.amazonaws.com"
                Action: 
                  - "sts:AssumeRole"
          Path: "/"
          # Lambda 실행에 필요한 기본 권한 부여 (실무에서는 최소 권한 원칙 권장)
          ManagedPolicyArns:
            - "arn:aws:iam::aws:policy/AWSLambda_FullAccess"
    
    # ------------------------------------------------------------#
    #  Create Lambda
    # ------------------------------------------------------------#
      LambdaFunction:
        Type: AWS::Lambda::Function
        Properties:
          FunctionName: !Sub ${SystemName}-${EnvName}-function
          # 콘솔에서 직접 수정 가능한 인라인 형태의 Python 소스 코드 정의
          Code:
            ZipFile: |
              import json
    
              def lambda_handler(event, context):
                  # TODO implement
                  return {
                      'statusCode': 200,
                      'body': json.dumps('Hello from Lambda!')
                  }
          # 인라인 코드(ZipFile)의 경우 기본 파일명이 index로 지정됨
          Handler: index.lambda_handler
          Role: !GetAtt LambdaIAMRole.Arn
          Runtime: python3.12
          Timeout: 30
    
      # API Gateway가 이 Lambda 함수를 호출할 수 있도록 보안 통로 개방
      LambdaInvokePermission:
        Type: AWS::Lambda::Permission
        Properties:
          Action: lambda:InvokeFunction
          FunctionName: !Ref LambdaFunction
          Principal: apigateway.amazonaws.com
          # 특정 API Gateway의 모든 스테이지 및 메서드(/*/ANY/)로부터의 호출만 허용
          SourceArn: 
            Fn::Sub: arn:aws:execute-api:${AWS::Region}:${AWS::AccountId}:${ApiGatewayRestApi}/*/ANY/
    
    # ------------------------------------------------------------#
    #  Create REST API 
    # ------------------------------------------------------------#
      ApiGatewayRestApi:
        Type: AWS::ApiGateway::RestApi
        Properties:
          Name: !Sub ${SystemName}-${EnvName}-api
          EndpointConfiguration:
            Types:
              - REGIONAL
    
    # ------------------------------------------------------------#
    #  Create API Gateway Method 
    # ------------------------------------------------------------#
      ApiGatewayMethod:
        Type: AWS::ApiGateway::Method
        Properties:
          AuthorizationType: NONE
          HttpMethod: ANY # 모든 HTTP 메서드(GET, POST 등)를 이 엔드포인트 하나로 수신
          # API의 최상위 루트 경로('/')에 메서드를 연결
          ResourceId: 
            Fn::GetAtt: [ApiGatewayRestApi, RootResourceId]
          RestApiId:
            Ref: ApiGatewayRestApi
          # API Gateway가 백엔드(Lambda)와 통신하는 방식 정의
          Integration:
            Type: AWS_PROXY # 클라이언트의 요청 프록시 전체를 Lambda로 그대로 전달
            IntegrationHttpMethod: POST # API Gateway가 Lambda를 호출할 때는 항상 POST 방식을 사용함
            # API Gateway가 Lambda 함수를 호출하기 위한 표준 포맷의 Amazon Resource Name(ARN) 생성
            Uri:
              Fn::Join:
                - ""
                - - "arn:"
                  - Ref: AWS::Partition
                  - ":apigateway:"
                  - Ref: AWS::Region
                  - ":lambda:path/2015-03-31/functions/"
                  - Fn::GetAtt:
                    - LambdaFunction
                    - Arn
                  - "/invocations"
          MethodResponses:
            - StatusCode: 200
    
    # ------------------------------------------------------------#
    #  Create API Gateway Deployment 
    # ------------------------------------------------------------#
      ApiGatewayDeployment:
        Type: AWS::ApiGateway::Deployment
        Properties:
          RestApiId: !Ref ApiGatewayRestApi
          StageName: !Sub ${SystemName}-${EnvName}-stage
        # 메서드(ApiGatewayMethod)가 완전히 생성된 이후에 배포가 진행되도록 순서 보장
        DependsOn: 
          - ApiGatewayMethod
    ```
    
1. 스택 생성
2. 람다 함수 이벤트 생성, 실행 결과 확인
    - 람다 콘솔> [Test] 클릭> 이벤트 생성> [Test] 클릭
3. API 게이트웨이 콘솔 화면 진입
    - [API]> [리소스]> 생성된 메서드 확인
4. 생성된 스테이지 확인 
5. API 엔드포인트로 접근
    - 람다 함수에서 [구성] 클릭> API 엔드포인트의 URI 확인