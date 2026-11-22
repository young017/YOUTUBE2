# 유튜브 영상 제작을 위한 맞춤형 피드백 서비스

2025 유튜브 영상 데이터를 학습해, 업로드 전에 예상 조회수와 인기 확률을 예측하고
영상에 맞는 태그와 제목을 추천해 주는 크리에이터용 서비스입니다.

[라이브 데모](https://yudaag.github.io/youtube/index.html)

<!-- TODO: 스크린샷 또는 GIF를 여기에 추가하세요. -->

## 주요 기능

- **조회수 예측**: 카테고리별로 최적화한 모델(CatBoost, LightGBM, XGBoost)로 예상 조회수와 인기 확률을 계산합니다.
- **태그 추천**: SBERT 시맨틱 유사도와 OpenAI 임베딩을 결합한 하이브리드 방식으로 태그를 추천합니다.
- **제목 생성**: GPT로 SEO를 고려한 제목 후보를 만들어 줍니다.
- **트렌드 분석**: Kaggle YouTube 데이터로 월별 인기 카테고리를 분석합니다.
- **계정 및 기록**: 회원가입과 영상 기록 저장을 지원합니다.

> 조회수 예측과 기본 태그 추천은 API 키 없이도 동작합니다.

## 기술 스택

- **백엔드:** Python 3.11, FastAPI, SQLite
- **머신러닝:** CatBoost, LightGBM, XGBoost
- **자연어 처리:** Sentence Transformers (SBERT), OpenAI API
- **데이터 및 배포:** Kaggle API, YouTube Data API, Hugging Face Hub, Railway

## 모델 및 설계

- **데이터:** [작성 필요: 데이터 출처와 규모]
- **성능:** [작성 필요: 평가 지표와 수치]
- **카테고리별 모델을 따로 쓴 이유:** [작성 필요]
- **지원 카테고리:** [작성 필요: 모델이 있는 카테고리 범위]
- **내 역할:** [작성 필요: 팀 프로젝트라면 담당한 부분]

## 설치 및 실행

Python 3.11 (3.13에서는 CatBoost 설치가 실패할 수 있습니다)이 필요하고,
첫 실행 시 Hugging Face Hub에서 모델을 내려받습니다.

```bash
git clone https://github.com/young017/YOUTUBE2.git
cd YOUTUBE2
python -m venv .venv
```

```bash
# Windows PowerShell
.\.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

```bash
python -m pip install -r requirements.txt
python init_database.py
python -m uvicorn tags.fastapi_server:app --host 127.0.0.1 --port 8001
```

서버가 실행되면 아래 주소에서 확인할 수 있습니다.

- API: <http://127.0.0.1:8001>
- Swagger UI: <http://127.0.0.1:8001/docs>

### 환경 변수 (선택)

일부 기능에만 필요하며, 프로젝트 루트의 `.env` 파일에 설정합니다.

| 변수 | 사용 기능 |
|---|---|
| `OPENAI_API_KEY` | 제목 생성, 태그 보정 |
| `KAGGLE_USERNAME`, `KAGGLE_KEY` | 트렌드 데이터 수집 |
| `YOUTUBE_API_KEY` | YouTube 카테고리 데이터 수집 |

## 사용 예제

제목으로 태그를 추천받는 예시입니다.

```python
import requests

response = requests.post(
    "http://127.0.0.1:8001/api/tags/recommend",
    json={"title": "맛있는 파스타 만들기", "top_k": 10, "method": "hybrid"},
)
print(response.json()["recommended_tags"])
```

전체 API 목록과 요청·응답 형식은 서버 실행 후 `/docs`에서 확인할 수 있습니다.
