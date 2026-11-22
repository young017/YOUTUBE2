# 유튜브 영상 제작을 위한 맞춤형 피드백 서비스

2025 유튜브 영상 데이터를 기반으로, 분석부터 맞춤형 피드백까지 모든 유튜버가 성장할 수 있는 유튜브 생태계를 만듭니다.

[데모 보기](https://yudaag.github.io/youtube/index.html)

## 주요 기능

| 기능 | 설명 |
|:---|:---|
| **조회수 예측** | 카테고리별 최적화된 ML 모델(CatBoost, LightGBM, XGBoost)로 업로드 전 예상 조회수와 인기 확률을 예측합니다. |
| **태그 추천** | SBERT 시맨틱 유사도와 OpenAI 임베딩을 결합한 하이브리드 방식으로 태그를 추천합니다. |
| **제목 생성** | GPT 기반 SEO 최적화 제목 생성기로 클릭률을 높이는 제목을 만들어 줍니다. |
| **트렌드 분석** | Kaggle YouTube 데이터를 기반으로 월별 인기 카테고리 트렌드를 분석합니다. |

## 기술 스택

| 분류 | 기술 |
|:---|:---|
| **Backend** | FastAPI, Python 3.11, SQLite |
| **ML** | CatBoost, LightGBM, XGBoost |
| **NLP** | SBERT, OpenAI API |
| **Data** | Pandas, NumPy, Kaggle API |
| **Infra** | Railway, Hugging Face Hub |

## 시작하기

Python 3.11 또는 3.12가 필요합니다.

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

# 4. 환경 변수 설정 (.env 파일 생성, 아래 표 참고)

# 5. 데이터베이스 초기화
python init_database.py

# 6. 서버 실행
python fastapi_server.py
```

서버 실행 후 아래 주소에서 확인할 수 있습니다.

| 주소 | 설명 |
|:---|:---|
| http://localhost:8001 | API 서버 |
| http://localhost:8001/docs | Swagger UI |
| http://localhost:8001/redoc | ReDoc |

<details>
<summary><b>자주 발생하는 설치 문제</b></summary>

**CatBoost 설치 실패 (Python 3.13)**
```bash
pip install catboost --no-build-isolation
```

**Hugging Face 모델 다운로드 실패**
```bash
pip install huggingface_hub
huggingface-cli login
```

**포트 충돌 (8001)**
```bash
# Windows
set PORT=8002
# macOS / Linux
export PORT=8002
```

</details>

## 환경 변수

프로젝트 루트에 `.env` 파일을 만들고 필요한 변수를 설정합니다.

| 변수 | 사용 기능 | 필수 여부 |
|:---|:---|:---|
| `OPENAI_API_KEY` | 태그 보정, 제목 생성 | 선택 |
| `KAGGLE_USERNAME`, `KAGGLE_KEY` | 트렌드 데이터 수집 | 선택 |
| `YOUTUBE_API_KEY` | 카테고리 메타데이터 수집 | 선택 |
| `PORT` | 서버 포트 (기본값 8001) | 선택 |

조회수 예측과 기본 태그 추천은 API 키 없이도 동작합니다.

## API

| Method | Endpoint | 설명 |
|:---|:---|:---|
| `POST` | `/api/auth/register` | 회원가입 |
| `POST` | `/api/auth/login` | 로그인 |
| `POST` | `/api/auth/logout` | 로그아웃 |
| `GET` | `/api/auth/profile` | 프로필 조회 |
| `POST` | `/api/videos/create` | 영상 저장 및 조회수 예측 |
| `GET` | `/api/videos/list` | 내 영상 목록 |
| `POST` | `/api/tags/recommend` | 태그 추천 |
| `POST` | `/api/tags/enrich` | 태그 강화 |
| `POST` | `/api/titles/generate` | 제목 생성 |
| `POST` | `/api/trends/update-month` | 월별 트렌드 분석 |

<details>
<summary><b>사용 예시</b></summary>

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

## 배포

Railway에 배포했습니다. 저장소를 연결하고 Variables 탭에 환경 변수를 등록하면 자동 배포됩니다.
ML 모델은 첫 실행 시 Hugging Face Hub에서 내려받으며, `youtube_analytics.db`는 영구 스토리지에 마운트해야 데이터가 유지됩니다.

## 라이선스

[MIT License](LICENSE)
