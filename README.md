# YouTube Analytics

유튜브 크리에이터를 위한 AI 기반 맞춤형 피드백 플랫폼

- [라이브 데모](https://yudaag.github.io/youtube/index.html)
- [API 문서](http://localhost:8001/docs)
- [버그 리포트](https://github.com/young017/YOUTUBE2/issues)

<br/>

## 목차

- [주요 기능](#-주요-기능)
- [기술 스택](#-기술-스택)
- [시작하기](#-시작하기)
- [환경 변수](#-환경-변수)
- [API 레퍼런스](#-api-레퍼런스)
- [프로젝트 구조](#-프로젝트-구조)
- [배포](#-배포)
- [기여하기](#-기여하기)
- [라이선스](#-라이선스)

<br/>

## 주요 기능

- **조회수 예측**: CatBoost · LightGBM · XGBoost를 활용한 카테고리별 ML 모델로 업로드 전 예상 조회수와 인기 확률 예측
- **AI 태그 추천**: SBERT 기반 시맨틱 유사도와 OpenAI 임베딩을 결합한 하이브리드 태그 추천
- **제목 자동 생성**: GPT 기반 SEO 최적화 제목 생성기로 클릭률을 높이는 제목 생성
- **트렌드 분석**: Kaggle YouTube 데이터를 기반으로 월별 인기 카테고리 트렌드 분석 및 시각화

<br/>

## 기술 스택

- **Backend**: FastAPI, Python 3.11, SQLite
- **ML/AI**: CatBoost, LightGBM, XGBoost
- **NLP**: SBERT, OpenAI API
- **Data**: Pandas, NumPy, Kaggle API
- **Infra**: Railway, HuggingFace Hub

<br/>

## 🚀 시작하기

### 사전 요구사항

- Python **3.11** 이상
- pip 패키지 관리자

### 설치

```bash
# 1. 저장소 클론
git clone https://github.com/young017/YOUTUBE2.git
cd YOUTUBE2

# 2. 가상 환경 생성 및 활성화
python -m venv venv

# Windows
venv\Scripts\activate
# macOS / Linux
source venv/bin/activate

# 3. 의존성 설치
pip install -r requirements.txt

# 4. 환경 변수 설정 (.env 파일 생성 — 아래 섹션 참고)

# 5. 데이터베이스 초기화
python init_database.py

# 6. 서버 실행
python fastapi_server.py
```

서버 실행 후 아래 주소에서 확인하세요.

| 주소 | 설명 |
|---|---|
| http://localhost:8001 | API 서버 |
| http://localhost:8001/docs | Swagger UI (추천) |
| http://localhost:8001/redoc | ReDoc |

<details>
<summary><b>💡 자주 발생하는 설치 문제</b></summary>

<br/>

**CatBoost 설치 실패 (Python 3.13)**
```bash
pip install catboost --no-build-isolation
# Python 3.11 / 3.12 사용을 강력 권장합니다
```

**Hugging Face 모델 다운로드 실패**
```bash
pip install huggingface_hub
huggingface-cli login  # 토큰 설정
```

**포트 충돌 (8001)**
```bash
# Windows
set PORT=8002
# macOS / Linux
export PORT=8002

python fastapi_server.py
```

**데이터베이스 파일 없음**
```bash
python init_database.py
```

</details>

<br/>

## 🔐 환경 변수

프로젝트 루트에 `.env` 파일을 생성하고 아래 변수를 설정하세요.

```env
# OpenAI API — 태그 보정 및 제목 생성에 사용 (선택)
OPENAI_API_KEY=sk-...

# Kaggle API — 트렌드 데이터 수집에 사용 (선택)
KAGGLE_USERNAME=your_username
KAGGLE_KEY=your_key

# YouTube Data API — 카테고리 메타데이터 수집에 사용 (선택)
YOUTUBE_API_KEY=your_youtube_key

# 서버 포트 (기본값: 8001)
PORT=8001
```

> **핵심 기능(조회수 예측, 기본 태그 추천)은 API 키 없이도 동작합니다.**  
> OpenAI · Kaggle · YouTube API 키는 해당 기능 사용 시에만 필요합니다.

<details>
<summary><b>API 키 발급 방법</b></summary>

<br/>

**OpenAI** → https://platform.openai.com/api-keys  
**Kaggle** → 계정 설정 → API → Create New Token → `kaggle.json`에서 추출  
**YouTube Data API v3** → Google Cloud Console → API 및 서비스 → 사용자 인증 정보

</details>

<br/>

## 📡 API 레퍼런스

### 인증

| Method | Endpoint | 설명 |
|:---:|---|---|
| `POST` | `/api/auth/register` | 회원가입 |
| `POST` | `/api/auth/login` | 로그인 |
| `POST` | `/api/auth/logout` | 로그아웃 |
| `GET` | `/api/auth/profile` | 프로필 조회 |

### 핵심 기능

| Method | Endpoint | 설명 |
|:---:|---|---|
| `POST` | `/api/videos/create` | 영상 저장 + 조회수 예측 |
| `GET` | `/api/videos/list` | 내 영상 목록 |
| `POST` | `/api/tags/recommend` | 태그 추천 (SBERT · 하이브리드) |
| `POST` | `/api/tags/enrich` | 태그 강화 (OpenAI) |
| `POST` | `/api/titles/generate` | 제목 자동 생성 (GPT) |
| `POST` | `/api/trends/update-month` | 월별 트렌드 분석 |

<details>
<summary><b>사용 예시 보기</b></summary>

<br/>

**조회수 예측**
```python
import requests

response = requests.post(
    "http://localhost:8001/api/videos/create",
    params={"session_token": "<your_token>"},
    json={
        "title": "맛있는 파스타 만들기",
        "category": "26",
        "length": 15.5,
        "upload_time": "2025-01-15T18:00",
        "has_subtitles": "provided",
        "video_quality": "HD",
        "subscriber_count": 100000,
    },
)
result = response.json()["data"]["prediction"]
print(f"예상 조회수: {result['predicted_views']:,}")
print(f"인기 확률:   {result['confidence']:.1f}%")
```

**태그 추천**
```python
response = requests.post(
    "http://localhost:8001/api/tags/recommend",
    json={"title": "맛있는 파스타 만들기", "top_k": 10, "method": "hybrid"},
)
print(response.json()["recommended_tags"])
```

**제목 생성**
```python
response = requests.post(
    "http://localhost:8001/api/titles/generate",
    json={"keyword": "파스타 레시피", "imageText": "크림 파스타 요리 과정", "n": 5},
)
for title in response.json()["titles"]:
    print(title)
```

</details>

<br/>

## 📁 프로젝트 구조

```
YOUTUBE2/
├── fastapi_server.py        # FastAPI 애플리케이션 진입점
├── database.py              # SQLite 데이터베이스 레이어
├── init_database.py         # DB 초기화 및 데모 계정 생성
├── enrich_tags.py           # 태그 강화 파이프라인
├── requirements.txt
├── runtime.txt
├── railway.json             # Railway 배포 설정
│
├── tags/                    # 태그 추천 모듈
│   ├── tag_recommendation_model.py
│   ├── predict_tags.py
│   ├── enrich_tags.py
│   └── enrich_tags_openai_embed.py
│
├── 모델/                    # ML 예측 모델 (HuggingFace 자동 다운로드)
│   ├── catboost_model_*.cbm    # 카테고리 1, 15, 19
│   ├── lgbm_model_*.pkl        # 카테고리 10, 22, 24, 26
│   └── xgb_model_*.pkl         # 카테고리 17, 20, 23, 28
│
├── UI/                      # 웹 프론트엔드
│   ├── index.html
│   ├── login.html
│   ├── signup.html
│   ├── mypage.html
│   ├── feedback.html
│   ├── feedback-result.html
│   └── trend.html
│
└── docs/                    # 배포용 정적 파일 (UI 미러)
```

<br/>

## 🚢 배포

### Railway (권장)

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/)

1. [Railway](https://railway.app/)에서 **New Project → Deploy from GitHub repo** 선택
2. 이 저장소 연결
3. **Variables** 탭에서 `.env` 변수 설정
4. 자동 배포 완료

### Docker

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "fastapi_server:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 기타 플랫폼 (Heroku / VPS)

```bash
# Procfile
echo "web: uvicorn fastapi_server:app --host 0.0.0.0 --port \$PORT" > Procfile

# 실행
uvicorn fastapi_server:app --host 0.0.0.0 --port 8000
```

> **배포 시 주의사항**  
> - 모든 환경 변수를 서버에 등록하세요.  
> - ML 모델은 첫 실행 시 Hugging Face Hub에서 자동으로 다운로드됩니다.  
> - `youtube_analytics.db`는 영구 스토리지에 마운트하여 데이터 손실을 방지하세요.

<br/>

## 🤝 기여하기

기여는 언제나 환영합니다! 아래 절차를 따라 주세요.

```
1. Fork  →  2. 브랜치 생성 (feat/your-feature)  →  3. 커밋  →  4. PR 제출
```

```bash
git checkout -b feat/your-feature
git commit -m "feat: 기능 설명"
git push origin feat/your-feature
```

버그 리포트나 기능 제안은 **[Issues](https://github.com/young017/YOUTUBE2/issues)** 에 남겨 주세요.

<br/>

## 📄 라이선스

이 프로젝트는 [MIT License](LICENSE) 하에 배포됩니다.

<br/>

---

API 문서는 서버 실행 후 http://localhost:8001/docs 에서 확인하세요.
