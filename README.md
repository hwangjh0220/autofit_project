# WearCast 🌤️👔
> **기상청·에어코리아 공공데이터와 내 옷장(User Closet)을 결합한 맞춤형 외출 복장 자동 추천 텔레그램 봇**

`WearCast`는 매일 아침 기상청 단기예보(기온, 강수 형태, 풍속)와 에어코리아 대기오염(미세먼지/초미세먼지) 데이터를 실시간 수집·분석하여, 사용자가 **실제 보유한 옷장 데이터(JSON Inventory)** 내에서 최적의 코디를 조합한 뒤 **텔레그램(Telegram) 봇**으로 브리핑을 자동 발송하는 파이썬 기반 자동화 프로젝트입니다.

---

## 📌 Key Features

* **실시간 기상 및 대기질 데이터 수집:**
  * 기상청 단기예보 API를 활용한 기온(TMP), 일교차(TMX/TMN), 강수형태(PTY), 풍속(WSD) 조회
  * 에어코리아 대기오염정보 API를 활용한 실시간 미세먼지(PM10) 및 초미세먼지(PM2.5) 등급 산출
* **GPS/위치 기반 동적 좌표 변환:**
  * Lambert 등각원추 투영 변환 수식을 적용해 위도/경도 $\rightarrow$ 기상청 격자 좌표(`nx`, `ny`) 자동 변환
* **내 옷장(User Closet) 기반 개인화 추천:**
  * 일반적인 기온별 권장 복장이 아닌, 사용자가 등록한 실제 옷 목록(`closet.json`)에서 적정 온도의 상·하의·아우터 필터링 매칭
  * 강수 확률에 따른 우산 챙김 알림 및 미세먼지 농도에 맞춘 마스크(KF94/KF80) 권고
* **영구 무인 자동화 (Telegram Bot API):**
  * 토큰 만료 주기가 없는 텔레그램 봇 API를 채택해 장기적인 무인 운영 안정성 확보
  * 팀 단체 대화방 연동을 통한 팀원 전원 동시 수신 및 결과 모니터링 가능
  * Markdown 서식을 적용한 가독성 높은 아침 브리핑 카드 전송
* **스케줄링 & 보안 관리:**
  * `schedule` 라이브러리로 매일 아침 지정 시각(예: 07:30) 자동 파이프라인 트리거
  * `python-dotenv`를 활용해 API 키 및 봇 토큰을 코드에서 분리하고 `.env`로 은닉 관리

---

## 🛠 Tech Stack

| Category | Details |
| :--- | :--- |
| **Language** | Python 3.10+ |
| **Data Collection** | `requests`, 공공데이터포털 API (기상청, 에어코리아) |
| **Data Processing** | `json`, `math` (Lambert 투영 좌표 변환) |
| **Notification** | Telegram Bot API (`requests` 기반 REST 호출) |
| **Automation** | `schedule`, `python-dotenv` |
| **VCS & Collab** | Git, GitHub (GitHub Flow, Issue, PR, Code Review) |

---

## 🏗 System Architecture

```text
[1. 위치 좌표 / 스케줄러]
       │ (매일 아침 07:30 자동 트리거)
       ▼
[2. 공공데이터 API 수집]
       ├─ 기상청 단기예보 API (기온, 강수형태, 풍속)
       └─ 에어코리아 API (PM10, PM2.5 대기질)
       ▼
[3. 복장 추천 엔진 (Recommendation Engine)]
       ├─ 사용자 옷장 데이터 (data/closet.json) 로드
       ├─ 기온/체감온도/날씨 조건에 맞는 의류 필터링 및 조합
       └─ 우산 / KF 마스크 등 준비물 패키징
       ▼
[4. 텔레그램 봇 (Notifier)]
       └─ Telegram Bot API ──> 팀원 개인 대화방 or 단체 그룹방 전송
```

---

## 📁 Project Structure

```text
wearcast/
├── config/
│   └── settings.py          # 환경변수 로드 및 공통 상수 정의
├── data/
│   └── closet.json          # 사용자가 보유한 실제 옷 목록 (인벤토리)
├── modules/
│   ├── __init__.py
│   ├── weather_collector.py # 기상청 단기예보 & 에어코리아 API 연동
│   ├── coordinate.py        # 위도·경도 -> 기상청 격자(nx, ny) 변환 공식
│   ├── recommendation.py    # 날씨 데이터와 옷장 매핑 알고리즘
│   └── telegram_bot.py      # 텔레그램 봇 메시지 전송 모듈
├── .env.example             # 환경변수 설정 예시 파일
├── .gitignore               # 민감 정보(.env) 및 가상환경 제외
├── main.py                  # schedule 기반 전체 파이프라인 실행 진입점
├── requirements.txt         # 필수 라이브러리 목록
└── README.md                # 프로젝트 안내 문서
```

---

## 👥 Team Roles & Responsibilities

| Role | Name | Responsibilities |
| :--- | :--- | :--- |
| **Team Leader** | 팀장 | 프로젝트 전체 아키텍처 설계, GitHub 리포지토리 총괄, 메인 파이프라인 통합 및 `schedule` 스케줄러 구현 |
| **Member 1** | 팀원 1 | 기상청 단기예보 및 에어코리아 대기정보 API 데이터 수집, 위·경도 $\rightarrow$ 격자 좌표 변환 모듈 구현 |
| **Member 2** | 팀원 2 | 사용자 옷장 스키마(`closet.json`) 설계 및 날씨·기온 기반 의류 필터링 추천 알고리즘 구현 |
| **Member 3** | 팀원 3 | 텔레그램 Bot API 연동 모듈 개발, Markdown 메시지 서식 템플릿화 및 발송 예외 처리 |

---

## 🚀 Getting Started

### 1. Requirements Installation
```bash
# 리포지토리 클론
git clone https://github.com/YourTeam/wearcast.git
cd wearcast

# 가상환경 생성 및 활성화
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 패키지 설치
pip install -r requirements.txt
```

### 2. Environment Variables (.env)
프로젝트 루트 디렉토리에 `.env` 파일을 만들고 아래 형식으로 키를 입력합니다.
```env
# 공공데이터포털 디코딩 인증키
WEATHER_API_KEY=your_public_data_portal_api_key

# 텔레그램 봇 설정 (@BotFather 발급)
TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrSTUvwxYZ
TELEGRAM_CHAT_ID=123456789

# 스케줄 실행 설정
TARGET_TIME=07:30
```

### 3. Closet Inventory Setup (`data/closet.json`)
내가 가지고 있는 실제 옷을 아래 형식으로 등록합니다.
```json
[
  {
    "id": 1,
    "name": "오버핏 옥스포드 셔츠",
    "category": "top",
    "temp_range": [12, 20]
  },
  {
    "id": 2,
    "name": "경량 패딩 조끼",
    "category": "outer",
    "temp_range": [5, 12]
  },
  {
    "id": 3,
    "name": "와이드 슬랙스",
    "category": "bottom",
    "temp_range": [5, 24]
  }
]
```

### 4. Running
```bash
# 1회성 즉시 발송 테스트
python main.py --now

# 매일 아침 자동 스케줄러 백그라운드 구동
python main.py
```

---

## 🔒 Git Collaboration Rules

* **Branch Rule:** `main`(배포용) $\rightarrow$ `develop`(개발 통합) $\rightarrow$ `feature/<기능명>` 브랜치 분리 작업
* **Code Review:** Pull Request 생성 시 최소 1명 이상의 팀원 승인(Approve) 후 병합 진행
* **Security:** 개인 API Key와 Telegram Bot Token이 포함된 `.env` 파일은 절대 Git 커밋에 포함하지 않습니다.