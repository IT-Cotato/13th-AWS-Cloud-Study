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

