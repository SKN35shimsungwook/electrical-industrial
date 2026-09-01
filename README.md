# 📘 전기산업기사 필기 퀴즈

**전기산업기사 필기시험** 기출문제를 풀면서 공부하는 Streamlit 퀴즈 앱이에요.
회차별 기출(2019~2025년 다수 회차)과 CBT 문제은행을 SQLite DB로 관리하고, 필요하면
Gemini AI가 오답에 대해 추가 설명도 해줘요.

## 기능

- 과목·회차별로 문제를 골라서 풀기
- 채점 결과와 함께 **해설** 확인
- **AI 학습 코치**: 사용자가 버튼을 눌렀을 때만 Gemini API를 호출해서 오답에 대한
  추가 설명을 받을 수 있음 (자동 호출 없음, API 키는 로컬 `.streamlit/secrets.toml`에 저장)
- CSV 데이터가 DB보다 최신이면 앱 실행 시 **자동으로 DB를 재생성** (기존 사용자 풀이 기록은 보존)

## 기술 스택

| 기술 | 역할 |
|---|---|
| **Streamlit** | 퀴즈 화면 |
| **SQLite** | 문제은행 + 사용자 풀이 기록 저장 (`db.py`, `build_db.py`) |
| **Google Gemini API** (`google-genai`) | 오답 추가 해설을 생성하는 AI 코치 기능 |
| **pandas / CSV** | `data/questions.csv`, `data/cbt_questions.csv`가 원본 문제 데이터 |

## 파일 구조

```
전기산업기사 필기/
├── app.py                     # Streamlit 앱 진입점
├── logic.py                    # 채점/문제 선택 로직
├── db.py                        # SQLite 연결 및 조회
├── build_db.py                  # CSV → SQLite DB 빌드
├── ai_coach.py                  # Gemini 기반 AI 학습 코치
├── build_round_*.py              # 각 회차별 기출문제를 CSV로 정리하는 스크립트들
├── retag_script.py               # 문제 태그(과목/연도 등) 재정리 스크립트
├── data/
│   ├── questions.csv              # 정리된 기출문제 원본
│   └── cbt_questions.csv          # CBT(컴퓨터 기반 시험) 문제은행
├── tools/                        # 기출자료 수집 파이프라인 스크립트 + 별도 README
└── requirements.txt
```

자료 수집 파이프라인(어떻게 기출문제를 모으고 정리했는지)은 [`tools/README.md`](./tools/README.md)에
따로 정리돼 있어요.

## 실행하기

```bash
pip install -r requirements.txt
streamlit run app.py
```

AI 코치 기능을 쓰려면 `.streamlit/secrets.toml.example`을 참고해서 Gemini API 키를 설정하세요.

## 코드 구성

`app.py`(화면) / `logic.py`(채점·출제 규칙) / `db.py`+`build_db.py`(SQLite 적재)로 역할이
나뉘어 있고, `build_round_*.py` 스크립트들이 회차별 기출문제를 `data/questions.csv`로 정리해요.

## 트러블슈팅

이 저장소 자체의 커밋 기록은 대부분 **문제 데이터 추가**라서(회차별 기출 추가), 코드 버그 수정
이력은 따로 없어요. 대신 `app.py`/`logic.py`는 첫 커밋부터 자매 프로젝트인
[license(정보처리산업기사 퀴즈)](https://github.com/SKN35shimsungwook/license)를 그대로
복제해서 만들어져서, 그 저장소에서 이미 고쳐진 버그 수정 코드를 처음부터 그대로 물려받았어요.
(`_dedupe_by_core`, `cbt_answers_store`, `disabled=ss.quiz_answered` 등이 실제로 이
저장소 코드에도 들어있는 걸 확인했어요.) 실제 문제가 뭐였고 어떻게 고쳐졌는지는
[license 저장소 README의 트러블슈팅 섹션](https://github.com/SKN35shimsungwook/license#트러블슈팅)에
자세히 정리돼 있어요.

---

🤖 이 저장소의 README는 Claude Code와 함께 작성했어요.
