# 13th AWS Cloud Study

코테이토 13기 AWS 클라우드 스터디 팀입니다. <br/>
교재 「AWS 잘하는 개발자 되기」를 바탕으로 AWS의 핵심 개념과 주요 서비스를 함께 학습하고, 매주 공부한 내용을 정리하여 공유하며 클라우드 환경에 대한 이해를 넓혀가고 있습니다. <br/>
AWS의 다양한 서비스를 직접 실습하며, 콘솔(UI)과 CloudFormation 방식 모두를 경험하는 것을 목표로 합니다.

---

## 👥 스터디원

| 이채영 | 이해원 | 임준서 | 최정인 |
|:------:|:------:|:------:|:------:|
| <img src="https://github.com/chaeyylee.png" width="150" height="150" /> | <img src="https://github.com/leehwx.png" width="150" height="150" /> | <img src="https://github.com/junjunseo.png" width="150" height="150" /> | <img src="https://github.com/ch0iii.png" width="150" height="150" /> |
| [@chaeyylee](https://github.com/chaeyylee) | [@leehwx](https://github.com/leehwx) | [@junjunseo](https://github.com/junjunseo) | [@ch0iii](https://github.com/ch0iii) |
| 13기 BE | 12기 BE | 12기 BE | 13기 BE |

---

## 🗓️ 스터디 일정

- **정기 모임**: 일요일 오후 8시 30분
- **진행 방식**: 비대면 디스코드
- **과제 제출 마감**: 토요일 23:59까지
- **지각 제출 마감**: 일요일 23:59까지

---

## 🧭 스터디 진행 방식

- 매주 정해진 분량을 학습한 뒤, 실습 코드와 공부 내용을 정리하여 GitHub에 업로드합니다.
- 정기 모임에서는 해당 주차 발표 담당자가 핵심 개념과 실습 내용을 간단히 발표하고, 이후 질의응답을 통해 이해가 부족한 부분을 함께 점검합니다.
- 추가로 AWS 및 클라우드 관련 기술 블로그, 뉴스, 실무 사례 등을 함께 공유합니다.

---

## 📖 학습 커리큘럼

> 스터디 진도는 상황에 따라 변경될 수 있습니다.

| 주차 | 학습 범위 | 학습 주제 |
|------|----------|----------|
| 1주차 | 00장 AWS 실습 환경 구축하기<br>01장 AWS를 쓰려면 알아야 하는 넓고 얕은 배경지식 | AWS 실습 환경 구축<br>AWS 기초 개념 이해 |
| 2주차 | 02장 AWS를 잘 쓰려면 알아야 하는 네트워크 지식 | AWS 네트워크 기초 (VPC) |
| 3주차 | 03장 AWS를 잘 쓰려면 알아야 하는 기본 서비스<br>04장 가상 클라우드 서버 파악하기 | AWS 권한 관리 (IAM)<br>가상 서버 서비스 (EC2) |
| 4주차 | 05장 관계형 데이터베이스 서비스 파악하기 | 관계형 데이터베이스 서비스 (RDS) |
| 5주차 | 06장 객체 스토리지 서비스 파악하기 | 객체 스토리지 서비스 (S3) |
| 6주차 | 11장 백엔드 서비스 이해하기 | 로드 밸런싱 서비스 (Elastic Load Balancer) |
| 7주차 | 12장 클라우드 서버 최적화를 위한 백엔드 서비스 파악하기 | 서버 자동 확장 (EC2 Auto Scaling) |
| 8주차 | 13장 컨테이너를 위한 백엔드 서비스 파악하기 | 컨테이너 서비스 (ECS) |
| 9주차 | 14장 이벤트 기반 코드 실행 백엔드 서비스 파악하기<br>15장 API 관리를 위한 백엔드 서비스 파악하기 | 서버리스 컴퓨팅 (Lambda)<br>API 관리 서비스 (API Gateway) |
| 10주차 | (추후 추가) | - |

---

## 🧑‍💻 GitHub 운영 방식

AWS 클라우드 스터디는 **COTATO Organization의 스터디 Repository를 fork한 뒤, 각자 학습 내용을 정리하여 PR로 제출하는 방식**으로 운영합니다.

### 1. 초기 설정

- COTATO Organization의 스터디 저장소를 본인 계정으로 fork
- fork한 저장소를 로컬 환경으로 clone

```bash
git clone [본인 fork repository URL]
```

- 본인 이름 폴더를 생성한 뒤 해당 폴더 아래에서 작업

```bash
mkdir -p [본인이름]/chapter0
```

### 2. 학습 내용 업로드

- 모든 실습 및 정리 내용은 **본인 이름 폴더 아래**에 작성합니다.
- 챕터별로 폴더를 만들고, 개념 정리 파일은 `.md` 형식으로 저장합니다.
- `.md` 파일명은 **해당 주차 단원명**으로 작성합니다.

예시

```bash
이채영/
└── chapter0/
    ├── 00장 AWS 실습 환경 구축하기.md
    └── VPC/
        └── IAM_User.yml
```

### 3. 커밋 및 푸시

```bash
git add .
git commit -m "docs: 1주차 AWS 기초 개념 정리"
git push origin main
```

예시

```bash
docs: 2주차 VPC 개념 정리
feat: chapter2 실습 파일 추가
fix: EC2 실습 설정 오류 수정
```

### 4. Pull Request 생성

- 본인 fork 저장소에서 원본 COTATO Repository로 Pull Request를 생성합니다.
- 팀장이 PR을 확인한 뒤 merge합니다.

#### PR 제목 규칙

```bash
[OOO(이름)] AWS Cloud Study - N주차
```

예시

```bash
[이채영] AWS Cloud Study - 1주차
```

#### PR 본문에 포함하면 좋은 내용

- 해당 주차에 학습한 내용 요약
- 실습하면서 새롭게 알게 된 점
- 어려웠던 부분 또는 질문
- 블로그 / 노션 정리 링크

---

## 📁 디렉토리 구조

```bash
/
├── README.md
│
├── 이채영/
│   ├── chapter0/
│   │   ├── 00장 AWS 실습 환경 구축하기.md
│   │   └── VPC/
│   │       └── VPC.yml
│   │
│   ├── chapter1/
│   │   └── 01장 AWS를 쓰려면 알아야 하는 넓고 얕은 배경지식.md
│   │
│   └── ...
│
├── 이해원/
│   ├── chapter0/
│   └── ...
│
├── 임준서/
│   ├── chapter0/
│   └── ...
│
├── 최정인/
│   ├── chapter0/
│   └── ...
│
└── ...
