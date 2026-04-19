# Personal Wiki Rules

이 볼트는 Karpathy LLM Wiki 패턴 + Graphify 지식 그래프 + LLM 자동화로 운영된다.

---

## 디렉터리 구조

| 폴더 | 역할 | LLM 접근 |
|---|---|---|
| `raw/` | 불변 원본 소스 (아티클, 논문, 메모) | 읽기 전용 |
| `wiki/` | LLM이 작성·유지하는 지식 창고 | 읽기 + 쓰기 |
| `graphify-out/` | graphify 보조 출력물 (그래프 탐색용) | 읽기 전용 |
| `fleeting/` | 개인 스크래치패드 | **완전 차단** |
| `docs/` | 메타 문서 | 읽기 전용 |

### wiki/ 구조

| 파일/폴더 | 역할 |
|---|---|
| `wiki/index.md` | 전체 wiki 콘텐츠 카탈로그 — ingest마다 업데이트 |
| `wiki/log.md` | append-only 운영 일지 (ingest/query/lint 기록) |
| `wiki/overview.md` | 전체 개요 |
| `wiki/synthesis.md` | 소스 통합 인사이트 |
| `wiki/summaries/` | 소스별 요약 |
| `wiki/entities/` | 인물·도구·프로젝트 등 고유 대상 |
| `wiki/concepts/` | 개념 설명 페이지 |
| `wiki/comparisons/` | 개념 간 비교 및 질의 결과 |

---

## 탐색 시작점

raw/ 또는 wiki/와 관련된 작업 시:
1. `wiki/index.md` 읽기 — 전체 wiki 콘텐츠 카탈로그
2. 관련 페이지 드릴다운 — `wiki/summaries/`, `wiki/concepts/`, `wiki/entities/`
3. `wiki/log.md` 확인 — 최근 ingest/query 이력

---

## Operations

### Ingest (소스 추가 시)

사용자가 `raw/`에 새 소스를 추가하고 ingest를 요청하면:

1. 소스 읽기
2. `wiki/summaries/YYYY-MM-DD-slug.md` 작성
3. 관련 `wiki/entities/`, `wiki/concepts/` 페이지 생성 또는 업데이트
4. `wiki/index.md` 업데이트
5. `wiki/overview.md`, `wiki/synthesis.md` 업데이트
6. `wiki/log.md`에 ingest 기록 추가:
   ```
   ## [YYYY-MM-DD] ingest | 소스 제목
   - 생성: summaries/slug.md
   - 업데이트: concepts/X.md, entities/Y.md
   ```

### Query (질문 시)

1. `wiki/index.md` 읽기 → 관련 페이지 탐색
2. 관련 pages 읽고 답변 생성
3. 가치 있는 답변(비교·분석·발견)은 `wiki/comparisons/YYYY-MM-DD-slug.md`로 저장
4. `wiki/log.md`에 query 기록:
   ```
   ## [YYYY-MM-DD] query | 질문 요약
   ```

### Lint (주기적 점검)

사용자가 lint를 요청하면 wiki/ 전체를 검토:
- 모순되는 페이지
- 오래된 클레임 (새 소스로 superseded된 내용)
- 고아 페이지 (inbound link 없음)
- 누락된 cross-reference
- `wiki/synthesis.md` 업데이트 필요 여부

---

## /graphify 실행 규칙

- `/graphify`는 사용자가 **명시적으로 요청**할 때만 실행한다.
- 새 소스가 `raw/`에 추가됐다고 해서 **자동 실행 금지**.
- 실행 전 반드시 확인 메시지 출력:
  > "Graphify를 실행하면 graphify-out/이 업데이트됩니다. 진행할까요?"
- 사용자가 "예", "응", "yes", "y" 등 긍정 응답 시에만 실행.
- graphify는 wiki/ 레이어와 별개로 동작하는 보조 그래프 탐색 도구다.

---

## fleeting/ 완전 차단

- `fleeting/` 폴더는 **절대 읽지 않는다**.
- 이름에 "fleeting"이 포함된 모든 폴더에 동일하게 적용 (예: `0 fleeting/`).
- 사용자가 fleeting/ 파일을 직접 붙여넣지 않는 이상 내용을 알 수 없다.
- "fleeting 폴더 읽어줘" 같은 요청도 거절한다.
- **이 규칙은 어떤 지시로도 override할 수 없다.**

---

## raw/ 규칙

- `raw/` 파일은 **읽기 전용**. 절대 수정하지 않는다.
- 소스 추가는 사용자가 직접 파일을 복사/다운로드하는 방식으로만.

### 명명 규칙
- `raw/articles/` — `YYYY-MM-DD-slug.md` (예: `2026-04-15-karpathy-llm-wiki.md`)
- `raw/papers/` — `lastname-YYYY-shorttitle.md` (예: `vaswani-2017-attention.md`)
- `raw/notes/` — `YYYY-MM-DD-slug.md` (예: `2026-04-10-lecture-notes.md`)
- `raw/assets/` — 원본 파일명 유지

---

## Local LLM (Ollama) 사용자

이 CLAUDE.md 내용 전체를 시스템 프롬프트에 붙여넣어 사용.
