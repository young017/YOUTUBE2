# 유튜브 영상 제작을 위한 맞춤형 피드백 서비스

유튜브 영상 데이터를 분석해 조회수를 예측하고, 제목과 태그를 추천하는 서비스입니다.

## 스크린샷 및 데모

<!-- TODO: 스크린샷 또는 GIF 파일을 추가하세요. -->

[라이브 데모](https://yudaag.github.io/youtube/index.html)

## 주요 기능

- 영상 카테고리별 머신러닝 모델로 예상 조회수와 인기 확률을 예측합니다.
- 영상 제목에 어울리는 태그를 추천하고 후보 태그를 보정합니다.
- OpenAI API를 사용해 영상 제목을 생성합니다.
- Kaggle 및 YouTube 데이터를 이용해 월별 트렌드를 분석합니다.
- 사용자 계정과 영상 기록을 SQLite에 저장합니다.

## 기술 스택

- **백엔드:** Python 3.11, FastAPI, SQLite
- **머신러닝:** CatBoost, LightGBM, XGBoost
- **자연어 처리:** Sentence Transformers (SBERT), OpenAI API
- **데이터 및 모델 배포:** Kaggle API, YouTube Data API, Hugging Face Hub

## 설치 및 실행

### 사전 요구사항

- Python 3.11
- Hugging Face Hub에서 모델을 내려받을 수 있는 인터넷 연결

### 실행

```bash
git clone https://github.com/young017/YOUTUBE2.git
cd YOUTUBE2
python -m venv .venv
```

가상 환경을 활성화합니다.

```powershell
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
```

```bash
# macOS / Linux
source .venv/bin/activate
```

의존성을 설치하고 서버를 실행합니다.

```bash
python -m pip install -r requirements.txt
python -m uvicorn tags.fastapi_server:app --host 127.0.0.1 --port 8001
```

첫 실행 시 태그 추천 모델을 Hugging Face Hub에서 내려받습니다. 서버가 실행되면 아래 주소에서 API 문서를 확인할 수 있습니다.

- API: <http://127.0.0.1:8001>
- Swagger UI: <http://127.0.0.1:8001/docs>
- ReDoc: <http://127.0.0.1:8001/redoc>

필요한 기능에 따라 프로젝트 루트의 `.env` 파일에 API 키를 설정합니다.

| 환경 변수 | 사용 기능 | 값 |
|---|---|---|
| `OPENAI_API_KEY` | 제목 생성, 태그 보정 | [작성 필요] |
| `KAGGLE_USERNAME`, `KAGGLE_KEY` | 트렌드 데이터 수집 | [작성 필요] |
| `YOUTUBE_API_KEY` | YouTube 데이터 수집 | [작성 필요] |

## 사용 예제

실행 중인 서버에 제목 기반 태그 추천을 요청합니다.

```python
import json
from urllib.request import Request, urlopen

request = Request(
    "http://127.0.0.1:8001/api/tags/recommend",
    data=json.dumps({
        "title": "맛있는 파스타 만들기",
        "top_k": 10,
        "method": "hybrid",
    }).encode("utf-8"),
    headers={"Content-Type": "application/json"},
    method="POST",
)

with urlopen(request) as response:
    result = json.load(response)
    print(result["recommended_tags"])
```

응답에는 추천 태그와 유사한 제목 정보가 포함됩니다. 전체 요청·응답 스키마는 서버 실행 후 `/docs`에서 확인할 수 있습니다.

## 라이선스

[작성 필요: 라이선스 종류를 결정하고 저장소 루트에 해당 LICENSE 파일을 추가하세요.]
