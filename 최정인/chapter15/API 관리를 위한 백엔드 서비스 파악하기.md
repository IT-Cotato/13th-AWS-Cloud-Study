## **14.1 이벤트 기반 코드 실행 서비스, AWS 람다란?**

**AWS Lambda**는 서버를 구축하지 않고도 코드를 실행할 수 있는 **이벤트 구동형 프로그램 실행 환경**으로, AWS에서 제공하는 서버리스 서비스이다. 이는 넓은 의미의 서버리스이며, 더 명확하게는 **FaaS(Functions-as-a-Service)**로 표현될 수 있다. FaaS는 개발자가 개별 함수를 클라우드에서 실행하고, 특정 이벤트나 요청 발생 시에만 함수가 실행되도록 하는 서버리스 아키텍처이다.

**FaaS (Functions-as-a-Service)**

서버리스의 한 종류로, 함수(코드 조각) 단위로 서비스를 제공하는 방식.

 마치 "코드를 실행하는 기능"만 딱 빌려 쓰는 것과 같다. 

예를 들어, 웹사이트에 새로운 이미지가 업로드되면, 그 이미지를 자동으로 처리하는 *함수*만 잠시 실행되었다가 꺼지는 식이다.

**이벤트 구동형**

뭔가 "일이 벌어졌을 때"만 코드가 실행된다는 의미다. 웹사이트에 누군가 접속하거나, 파일이 업로드되거나, 데이터베이스에 데이터가 추가될 때처럼!

---

## **14.2 이벤트 기반 코드 실행 서비스, AWS 람다 살펴보기**

AWS Lambda는 **이벤트 기반 아키텍처**를 기반으로 작동하는 서버리스 컴퓨팅 서비스이다. 특정 이벤트 발생 시 해당 이벤트를 처리하기 위한 별도의 함수가 생성되어 실행된다. 이 함수는 이벤트에 대한 **핸들러**로 구성되며, 이벤트가 발생하면 핸들러가 코드를 실행하는 구조다.

### **14.2.1 이벤트 기반 코드 실행 서비스, AWS 람다 이점**

AWS Lambda 사용의 주요 이점은 다음과 같다.

- **비용 절감**
    
    코드 실행 시간과 요청 수에 따라 비용이 청구되므로, 코드가 실행되지 않을 때는 비용이 발생하지 않아 효율적이다. 별도의 서버 관리 비용도 절감된다.
    
- **보다 안전한 실행 환경**
    
     AWS가 실행 환경을 관리하므로 온프레미스 환경보다 안전하며, AWS의 보안 전문가 팀이 24시간 시스템을 모니터링한다.
    
- **장애 위험 부담 감소**
    
     AWS가 서버 관리를 담당하므로 람다 함수 하나에 장애가 발생해도 AWS가 대응한다.
    
- **다양한 프로그래밍 언어 제공**
    
    다양한 프로그래밍 언어와 프레임워크를 지원하며, 사용자는 직접 코드를 작성하거나 템플릿을 사용할 수 있다.
    
- **다른 AWS 서비스와의 연동**
    
    다른 AWS 서비스와 연동하여 더욱 다양한 작업을 처리할 수 있다. 예를 들어, S3 웹 호스팅에 DynamoDB 데이터를 연동하거나 Slack과 같은 커뮤니케이션 도구와 연동하여 알림을 받을 수 있다. 
    

### **14.2.2 이벤트 기반 코드 실행 서비스, AWS 람다 구성 요소**

AWS Lambda 함수를 생성할 때 다음 세 가지 유형이 있다.

1. **새로 작성 (Author from scratch)**: 함수 이름, 런타임, 아키텍처, 권한을 직접 구성한다.
    - **함수 이름**: Lambda 함수의 고유한 이름
    - **런타임**: 함수에 사용되는 프로그래밍 언어 및 프레임워크 버전 (예: .NET 8, Java 21, Node.js 22.x, Python 3.13, Ruby 3.4, Amazon Linux 2023).
    - **아키텍처**: 컴퓨터 프로세서 유형 (x86_64 또는 arm64).
    - **권한**: IAM(Identity and Access Management) 서비스를 통해 Lambda 함수가 다른 AWS 서비스와 상호작용하는 데 필요한 권한을 부여.
2. **블루프린트 사용 (Use a blueprint)**: 샘플 코드를 제공받아 함수를 더 간편하게 작성할 수 있다. 블루프린트를 선택하면 런타임과 아키텍처가 자동으로 결정된다.
    - 다양한 사용 사례에 적합한 템플릿을 선택하면, 람다 함수 코드가 자동으로 작성되어 개발자는 빠르게 함수를 작성할 수 있다.
3. **컨테이너 이미지 (Container image)**: 람다 함수를 컨테이너화하여 Amazon ECR(Elastic Container Registry)에 푸시한 이미지를 AWS에 배포한다.
    - 함수 이름, 컨테이너 이미지 URI, 아키텍처, 권한으로 구성된다.
    - 외부 라이브러리나 미들웨어 도입 시 발생할 수 있는 버전 불일치 문제 등을 해결할 수 있다.
    - *람다 계층(Lambda Layers)* 기능을 사용하여 여러 함수에 라이브러리를 공유하고 외부 라이브러리 관리를 별도로 할 수도 있다.

---

## **14.3 AWS 람다 함수 생성해보기**

### **14.3.1 AWS 람다 함수로 Hello World 출력해보기**

AWS 람다 함수를 생성하기 위한 클라우드포메이션 `lambda.yml` 파일의 주요 구성 요소는 다음과 같다.

1. **IAM 역할 생성**: 람다 함수가 AWS 서비스에 접근할 수 있도록 `AWS::iam::Role` 타입의 IAM 역할을 생성ㅎ다. `AWSLambda_FullAccess` 정책을 통해 람다 함수가 필요한 모든 권한을 가진다.
    
    ```yaml
    LambdaIAMRole:
      Type: AWS::iam::Role
      Properties:
        RoleName: !Sub ${SystemName}-${EnvName}-lambdarole
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
        ManagedPolicyArns:
          - "arn:aws:iam::aws:policy/AWSLambda_FullAccess"
    ```
    
2. **람다 함수 생성**: `AWS::lambda::Function` 타입으로 람다 함수를 생성.
    - **FunctionName**: 함수의 이름을 지정.
    - **Code**: 실행할 파이썬 코드를 정의
        
         간단히 "Hello from Lambda!"를 반환.
        
        ```python
        def lambda_handler(event, context):
            # TODO implement
            return {
                'statusCode': 200,
                'body': json.dumps('Hello from Lambda!')
            }
        ```
        
    - **Handler**: 람다가 실행할 메서드 이름 (예: `index.lambda_handler`)
    - **Role**: 위에서 생성한 IAM 역할의 ARN을 지정.
    - **Runtime**: Python 3.12를 런타임으로 지정.
    - **Timeout**: 함수가 중지되기 전까지 실행될 수 있는 최대 시간을 지정.
    
    ```yaml
    LambdaFunction:
      Type: AWS::lambda::Function
      Properties:
        FunctionName: !Sub ${SystemName}-${EnvName}-function
        Code:
          ZipFile: |
            import json
    
            def lambda_handler(event, context):
                # TODO implement
                return {
                    'statusCode': 200,
                    'body': json.dumps('Hello from Lambda!')
                }
        Handler: index.lambda_handler
        Role: !GetAtt LambdaIAMRole.Arn
        Runtime: python3.12
        Timeout: 30
    ```
    

### **14.3.2 UI로 불러와 AWS 람다 함수로 Hello World 출력해보기**

클라우드포메이션 스택을 생성하여 람다 함수를 배포한 후, AWS 콘솔을 통해 함수를 테스트하고 "Hello world" 출력을 확인하는 과정이다.

1. **클라우드포메이션 스택 생성**: 파라미터 기본값을 유지한 상태로 스택을 생성
2. **AWS 람다 콘솔 진입 및 함수 확인**: AWS 람다 콘솔에서 생성된 함수를 확인
3. **람다 함수 정보 확인 및 실행**:
    - **함수 개요**: 람다 계층 및 연동 서비스 등을 확인
    - **코드 카테고리**: 파이썬 코드를 확인하고 `[Test]` 버튼을 클릭
    - **이벤트 생성**: 새로운 테스트 이벤트를 생성
        - 이벤트 이름과 템플릿(예: "hello-world")을 선택합니다. "Hello from Lambda"를 출력하는 코드이므로 `Event JSON`은 필수가 아님
    - **함수 실행 및 결과 확인**: `[Test]`를 클릭하여 함수를 실행하면 `Response`에 `"Hello from Lambda!"`가 출력되는 것을 확인할 수 있다.

---

# **Chapter 15. API 관리를 위한 백엔드 서비스 파악하기**

이 장에서는 API의 개념을 이해하고, AWS에서 제공하는 API 관리 서비스인 Amazon API Gateway의 이점, 구성 요소 및 API 유형을 학습한다.

## **15.1 API 관리 백엔드 서비스, 아마존 API 게이트웨이란?**

**API(Application Programming Interface)**는 소프트웨어나 애플리케이션 기능의 일부를 외부에 공개하여 프로그래밍으로 애플리케이션을 연결하는 방법이다. 예를 들어, 쇼핑몰 개발 시 결제 기능을 직접 개발하는 대신, 신용카드 회사나 은행이 제공하는 API를 이용해 결제 기능을 구현할 수 있다.

**Amazon API Gateway**는 AWS에서 제공하는 완전 관리형 서비스로, API를 간편하게 구축, 게시, 모니터링할 수 있도록 돕난다. AWS Lambda와 연계하여 다양한 이벤트 처리를 수행할 수 있다.

```
예시
1. Amazon S3에서 웹 호스팅.
2. Amazon DynamoDB에 웹 호스팅에 필요한 정보 저장.
3. API Gateway를 사용하여 웹 호스팅에 필요한 데이터를 DynamoDB에서 불러오거나, 웹사이트에서 입력한 데이터를 DynamoDB에 저장하는 이벤트를 처리하고, 이를 통해 AWS Lambda 함수를 호출하여 작업을 처리한다.
```

---

## **15.2 API 관리 백엔드 서비스, 아마존 API 게이트웨이 살펴보기**

Amazon API Gateway를 사용하지 않으면, 애플리케이션은 각 API와 개별적으로 통신해야 한다. 이로 인해 API 관리가 어려워지고 구성이 복잡해진다.

API Gateway를 사용하면 애플리케이션은 API Gateway와 통신하고, API Gateway는 API를 제공하는 서비스와 통신한다. 이를 통해 다양한 애플리케이션 요청을 조합하거나 API에서 발생하는 모든 것을 모니터링 및 관리할 수 있다. API 남용을 방지하고 비용을 절감하며, 애플리케이션과 서비스 간 효율적인 상호작용을 기대할 수 있다.

### **15.2.1 API 관리 백엔드 서비스, 아마존 API 게이트웨이 장단점**

**장점**:

- **효율적인 API 개발**
    
    API의 다양한 버전을 실행하고 테스트하며 업데이트할 수 있는 환경을 제공하여 효율적인 개발을 지원한다.
    
- **간편한 모니터링**
    
    API 호출 정보, 오류율, 데이터 대기 시간 등을 실시간으로 모니터링할 수 있으며, Amazon CloudWatch와 연동하여 문제 원인을 신속하게 파악하고 해결할 수 있다.
    
- **비용 절감**
    
    API 사용량에 따라 비용이 발생하며, 무료 티어를 제공하여 비용을 최소화할 수 있다.
    
- **생산성 향상**
    
    최종 사용자에게 최적의 대기 시간으로 API 요청과 응답을 처리하여 빠른 성능을 보장하고 개발자의 부담을 줄여준다.
    

**단점**:

- **지속 가능성과 신뢰성 저하 가능성**
    
    모든 API를 통합하여 처리하므로, API Gateway에 문제가 발생하면 전체 API 동작이 멈출 수 있다.
    
- **보안에 대한 개발자 관여 어려움**
    
    다른 서비스나 애플리케이션의 API를 사용하는 경우, 해당 API의 보안에 개발자가 직접 관여하기 어렵다는 단점이 있다.
    

### 15.2.2 API 관리를 위한 백엔드 서비스, 아마존 API 게이트웨이 구성 요소

Amazon API Gateway에서 제공하는 API 유형은 다음과 같다.

1. **REST API**:
    - **REST(Representational State Transfer) 규칙**을 따르는 API.
    - **4가지 원칙**:
        - **주소 가능성(Addressability)**: 정보가 URI를 통해 표현될 수 있으며, 각 정보는 고유한 URI를 가진다. (예: `https://google.com/api/xxx`)
        - **상태 비저장(Stateless)**: 정보를 교환할 때 상태를 유지하지 않고 요청과 응답을 한 번만 사용해 완료한다. 재차 이전 정보를 요청하면 다시 요청해야 한다.
        - **연결성(Connectability)**: 정보 내에 다른 리소스에 대한 링크가 포함되어 상호작용이 가능하다. (예: 쇼핑몰 메인 페이지에서 장바구니, 최근 본 상품 링크)
        - **통일 인터페이스(Uniform interface)**: 미리 정의된 공유 방식을 사용하여 정보를 조작한다. HTTP 메서드(GET, PUT, POST, DELETE)가 대표적.
    - 이러한 원칙에 따라 생성된 API를 바탕으로 람다 함수를 실행하거나 데이터베이스 쿼리, 애플리케이션 호출 등을 할 수 있다.
    - **REST API 프라이빗**: REST API와 동일하지만 VPC 내에서만 접근 가능하도록 제한된 API
2. **HTTP API**:
    - **HTTP 프로토콜**을 이용해 API를 구현한 것.
    - 애플리케이션 간 통신을 제공하는 규칙을 의미하지만, 개발자가 직접 URL과 HTTP 메서드를 설계해야 하므로 API 간 통일성이 부족할 수 있다.
    - REST API의 한 종류로 주소 가능성, 상태 비저장, 연결성, 통일 인터페이스 원칙을 따르지만, 이 원칙들을 반드시 적용하지 않는 API로 저렴하게 이용하고 싶은 경우에 적합하다.
    - REST API에 비해 대기 시간이 짧고 비용 효율적이며, 필요한 기능이 적거나 성능과 비용 효율성이 중요할 때 유용하다.
3. **웹소켓 API**:
    - **양방향 통신 프로토콜**
    - HTTP와 달리 통신 설정 후 서버와 클라이언트 간 자유롭게 데이터를 교환할 수 있다.
    - 요청마다 새 연결을 만들 필요가 없고 하나의 TCP 연결로 통신하며, 송수신을 동시에 할 수 있어 실시간 데이터 교환이 가능하다.
    - HTTP에 비해 헤더가 작아 통신 효율이 좋다.
    - 채팅, 게임 등 실시간성이 요구될 때 적합하다.

## **15.3 아마존 API 게이트웨이 활용하기**

API Gateway와 Lambda 함수를 연동하여 REST API 유형으로 API를 생성하고, 브라우저에서 람다 함수가 지정한 텍스트가 출력되는지 확인하는 실습.

### **15.3.1 API 게이트웨이를 활용해 람다 함수 실행해보기**

API Gateway와 Lambda 함수를 통합하는 클라우드포메이션 `APIGateway.yml` 파일의 주요 구성 요소는 다음과 같다.

1. **람다 호출 권한 생성**: `AWS::lambda::Permission` 타입으로 API Gateway가 람다 함수를 호출할 수 있는 권한을 부여한다.
    - **Action**: `lambda:InvokeFunction` 또는 `lambda:GetFunction`을 지정하여 람다 함수 호출 권한을 부여한다.
    - **FunctionName**: 호출 대상 람다 함수의 이름을 지정한다.
    - **Principal**: `apigateway.amazonaws.com`으로 지정하여 API Gateway가 람다 함수를 호출할 수 있도록 한다.
    - **SourceArn**: API Gateway의 ARN을 입력하여 호출 주체를 제한한다.
    
    ```yaml
    LambdaInvokePermission:
      Type: AWS::lambda::Permission
      Properties:
        Action: lambda:InvokeFunction
        FunctionName: !Ref LambdaFunction
        Principal: apigateway.amazonaws.com
        SourceArn: !Sub arn:aws:execute-api:${AWS::Region}:${AWS::AccountId}:${ApiGatewayRestApi}/*/ANY/
    ```
    
2. **API Gateway REST API 생성**: `AWS::apigateway::RestApi` 타입으로 API Gateway REST API를 생성.
    - **Name**: API의 이름을 지정한다.
    - **EndpointConfiguration**: 엔드포인트 유형을 `REGIONAL`로 지정한다.
    
    ```yaml
    ApiGatewayRestApi:
      Type: AWS::apigateway::RestApi
      Properties:
        Name: !Sub ${SystemName}-${EnvName}-api
        EndpointConfiguration:
          Types:
            - REGIONAL
    ```
    
3. **API Gateway 메서드 생성**: `AWS::apigateway::Method` 타입으로 API의 메서드를 생성한다.
    - **HttpMethod**: `ANY`를 지정하여 GET, POST 등 모든 HTTP 메서드 요청을 처리한다.
    - **ResourceId**: `ApiGatewayRestApi`의 RootResourceId를 참조하여 API의 루트 리소스에 메서드를 연결한다.
    - **Integration**: 람다 함수로 라우팅하기 위한 통합 옵션을 지정한다. `AWS_PROXY`를 사용하여 API Gateway가 람다 함수로 요청을 프록시한다.
    - **Uri**: 통합할 람다 함수의 ARN을 지정한다.
    
    ```yaml
    ApiGatewayMethod:
      Type: AWS::apigateway::Method
      Properties:
        AuthorizationType: NONE
        HttpMethod: ANY
        ResourceId: !GetAtt [ApiGatewayRestApi, RootResourceId]
        RestApiId: !Ref ApiGatewayRestApi
        Integration:
          Type: AWS_PROXY
          IntegrationHttpMethod: POST
          Uri: !Join
            - ''
            - - 'arn:'
              - !Ref AWS::Partition
              - ':apigateway:'
              - !Ref AWS::Region
              - ':lambda:path/2015-03-31/functions/'
              - !GetAtt LambdaFunction.Arn
              - '/invocations'
        MethodResponses:
          - StatusCode: 200
    ```
    
4. **API 배포**: `AWS::apigateway::Deployment` 타입으로 API를 배포한다.
    - `DependsOn` 속성을 사용하여 `ApiGatewayMethod`가 먼저 생성된 후 배포가 진행되도록 한다.
    
    ```yaml
    ApiGatewayDeployment:
      Type: AWS::apigateway::Deployment
      DependsOn:
        - ApiGatewayMethod
      Properties:
        RestApiId: !Ref ApiGatewayRestApi
        StageName: !Sub ${SystemName}-${EnvName}-stage
    ```
    

### **15.3.2 UI로 불러와 API 게이트웨이를 활용해 람다 함수 실행해보기**

클라우드포메이션 스택을 생성하여 API Gateway 및 Lambda 함수를 배포한 후, AWS 콘솔을 통해 API 엔드포인트로 접근하여 람다 함수 실행 결과를 확인하는 과정

1. **클라우드포메이션 스택 생성**: 파라미터 기본값을 유지한 상태로 스택을 생성한다.
2. **API Gateway 콘솔 진입 및 API 확인**: AWS 관리 콘솔에서 API Gateway 콘솔로 진입하여 생성된 API(`gr-product-api`)를 확인한다.
3. **리소스 및 메서드 확인**:
    - 생성된 API를 클릭하면 `[리소스]` 카테고리가 표시됨.
    - 해당 `[리소스]` 카테고리를 클릭하면 생성된 메서드(`ANY`)를 확인할 수 있다.
4. **스테이지 및 URL 확인**:
    - `[스테이지]`를 클릭하여 배포된 스테이지(예: `gr-product-stage`)를 확인한다.
    - 해당 스테이지의 `URL`을 확인한다.
5. **API 엔드포인트 접근**:
    - Lambda 함수의 `[구성]` 탭에서 `[트리거]` 섹션에 있는 API 엔드포인트 `URI`를 확인한다.
    - 해당 URI를 클릭하여 웹 브라우저로 접근하면 람다 함수에서 지정한 텍스트("Hello from Lambda!")가 출력되는 것을 확인할 수 있다.