# 1등 유튜버 되기

YouTube 영상의 조회수를 예측하고, 제목·태그 추천과 월별 트렌드 분석을 제공하는 웹 애플리케이션입니다.

> [작성 필요: 스크린샷 또는 GIF 추가] · [작성 필요: 라이브 데모 URL]

## 주요 기능

- 영상 정보와 카테고리를 바탕으로 예상 조회수와 인기 확률을 예측합니다.
- 영상 제목에 어울리는 태그를 추천하고 OpenAI로 보정합니다.
- 키워드를 바탕으로 YouTube 영상 제목을 생성합니다.
- Kaggle 및 YouTube Data API를 이용해 월별 인기 카테고리를 분석합니다.
- 회원가입·로그인 후 영상 분석 결과를 저장하고 조회합니다.

## 기술 스택

- **프론트엔드:** HTML, CSS, JavaScript, Bootstrap 5, Chart.js
- **백엔드:** Python 3.11, FastAPI, Uvicorn
- **데이터베이스:** SQLite
- **분석·AI:** CatBoost, LightGBM, XGBoost, Sentence Transformers, OpenAI API
- **외부 API:** Kaggle API, YouTube Data API v3

## 설치 및 실행

프로젝트 루트에서 PowerShell로 실행합니다. Python 3.11이 필요합니다.

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe fastapi_server.py
```

서버 실행 후 다음 주소에서 확인할 수 있습니다.

- 웹 인터페이스: `UI/index.html`을 브라우저에서 열기
- API 안내: http://localhost:8001
- API 문서: http://localhost:8001/docs

데이터베이스는 서버 시작 시 생성됩니다. 첫 실행 시 조회수 예측·태그 추천 모델을 Hugging Face Hub에서 내려받습니다. OpenAI 기능에는 `OPENAI_API_KEY`, 트렌드 업데이트에는 `KAGGLE_USERNAME`, `KAGGLE_KEY`, `YOUTUBE_API_KEY`가 필요합니다. 사용할 기능의 키만 프로젝트 루트 `.env` 파일에 설정하세요.

```dotenv
OPENAI_API_KEY=[작성 필요]
KAGGLE_USERNAME=[작성 필요]
KAGGLE_KEY=[작성 필요]
YOUTUBE_API_KEY=[작성 필요]
```

## 사용 예제

서버를 실행한 뒤 PowerShell에서 제목 기반 태그 추천 API를 호출합니다.

```powershell
$body = @{ title = "한국 여행 브이로그"; top_k = 5; method = "hybrid" } | ConvertTo-Json
Invoke-RestMethod -Uri "http://localhost:8001/api/tags/recommend" -Method Post -ContentType "application/json" -Body $body
```

응답에는 추천 태그(`recommended_tags`)와 유사한 제목(`similar_titles`)이 포함됩니다. 전체 요청·응답 형식은 `/docs`에서 확인할 수 있습니다.

## 라이선스

[작성 필요: 라이선스를 선택하고 저장소 루트에 `LICENSE` 파일을 추가하세요.]

## 추가 문서

- [시스템 아키텍처](docs/system-architecture.md)
