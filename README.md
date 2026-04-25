# Obsidian LLM Wiki

Andrej Karpathy가 제안한 LLM Wiki 패턴에 Graphify 지식 그래프를 결합한 개인 지식 관리 시스템이다.
아티클, 논문, 메모 등 원본 소스를 `raw/`에 쌓아두면 LLM이 자동으로 읽고 분석해
summaries, entity pages, concept pages, comparisons, synthesis를 담은 wiki를 만들어준다.
소스가 쌓일수록 wiki는 점점 풍부해진다.

### Obsidian과의 관계

Obsidian은 이 시스템의 **브라우저이자 IDE** 역할을 한다.
LLM(Claude Code)이 wiki 파일을 작성하면 Obsidian에서 실시간으로 확인할 수 있다.

- **Graph View** — `wiki/` 안의 페이지들이 서로 링크로 연결되어 지식 그래프를 시각화한다
- **Dataview** — wiki 페이지의 YAML 프론트매터를 활용해 동적 테이블·리스트를 만들 수 있다
- **Web Clipper** — 브라우저 확장으로 웹 아티클을 마크다운으로 변환해 `raw/articles/`에 바로 저장한다

LLM은 **프로그래머**, Obsidian은 **IDE**, wiki는 **코드베이스**다.
LLM이 파일을 편집하면 Obsidian에서 결과를 실시간으로 탐색하는 구조다.

---

## RAG와 뭐가 다른가

| | RAG | LLM Wiki |
|---|---|---|
| 지식 합성 시점 | 질문할 때마다 | 소스 추가 시 1회 |
| 결과 보존 | 채팅 기록에만 | wiki 파일로 영구 저장 |
| 누적 효과 | 없음 | 소스가 쌓일수록 wiki가 풍부해짐 |
| 교차 참조 | 매번 재발견 | 이미 연결되어 있음 |
| 유지 비용 | LLM이 매번 처음부터 | LLM이 변경분만 업데이트 |

핵심 차이: RAG는 **검색**이고 LLM Wiki는 **컴파일**이다.
소스를 추가할수록 지식이 쌓이는 복리 구조.

---

## 아키텍처

```
raw/              ← 불변 원본 소스 (LLM 읽기 전용)
    ├── articles/   웹 아티클, 블로그 포스트
    ├── papers/     학술 논문
    ├── notes/      직접 작성 메모, 강의 노트, 트랜스크립트
    └── assets/     이미지, 첨부파일
          │
          └─ ingest 요청 (Claude Code에서 "이 소스 ingest해줘")
                    │
                    ▼
wiki/             ← LLM이 작성·유지하는 지식 창고
    ├── index.md        콘텐츠 카탈로그 (ingest마다 업데이트)
    ├── log.md          운영 일지 (append-only)
    ├── overview.md     전체 개요
    ├── synthesis.md    소스 통합 인사이트
    ├── summaries/      소스별 요약
    ├── entities/       인물·도구·프로젝트 등 고유 대상
    ├── concepts/       개념 설명 페이지
    └── comparisons/    개념 간 비교 및 질의 결과

graphify-out/     ← graphify 보조 출력물 (그래프 탐색용)
    ├── GRAPH_REPORT.md
    ├── graph.html        브라우저 인터랙티브 시각화
    ├── graph.json
    └── obsidian/

fleeting/         ← 개인 스크래치패드 — LLM 완전 차단, 사람만 읽음
CLAUDE.md         ← LLM 스키마 (규칙·워크플로 정의)
AGENTS.md         ← CLAUDE.md 동일본 (Codex / OpenCode용)
```

---

## 디렉터리 용도

| 폴더 | 용도 |
|---|---|
| `raw/` | 불변 원본 소스. LLM은 읽기만 하며 절대 수정하지 않는다. |
| `wiki/` | LLM이 직접 작성·유지하는 지식 창고. summaries, entities, concepts, comparisons, synthesis 등으로 구성된다. |
| `graphify-out/` | graphify가 자동 생성하는 그래프 탐색 보조 도구. wiki/와 별개로 관리된다. |
| `fleeting/` | LLM에게 완전히 차단된 개인 스크래치패드. 갑자기 떠오른 아이디어나 정리 전 생각을 자유롭게 기록하는 공간. |

---

## 빠른 시작

플랜 파일을 그대로 따라가면 구축이 완료된다.

```
docs/superpowers/plans/2026-04-15-obsidian-llm-wiki.md
```

### 전제 조건

```bash
# Python 3.10 이상
brew install python@3.11

# graphify 설치
pip3.11 install graphifyy
graphify install          # Claude Code 전역 설정 등록
```

### 소스 추가 워크플로

```
1. raw/articles/, raw/papers/, raw/notes/ 중 맞는 폴더에 파일 저장
2. Claude Code에서: "이 소스 ingest해줘"
3. LLM이 소스 읽기 → summaries/ 작성 → entities/concepts/ 업데이트 → index/log 기록
4. (선택) "graphify 실행해줘" → graphify-out/ 그래프 갱신
```

### Obsidian Web Clipper 설정

브라우저에서 웹 페이지를 클리핑해 `raw/` 폴더에 바로 저장하는 확장 프로그램이다.

**설치:** [Obsidian Web Clipper](https://obsidian.md/clipper) 크롬 확장 설치

> **템플릿 파일 준비:**
> 1. Web Clipper 아이콘 → Settings → Templates → 기본 템플릿 선택 → 내보내기(Export)
>    → `기본-템플릿-clipper.json`으로 루트에 저장
> 2. LLM에게 아래와 같이 요청하면 나머지 4개를 자동 생성할 수 있다:
>    ```
>    기본-템플릿-clipper.json을 참고해서 raw/articles, raw/notes, raw/papers, raw/assets
>    각각에 맞는 Web Clipper 템플릿 JSON 파일을 루트 디렉터리에 생성해줘.
>    ```

**템플릿 임포트:**
루트 디렉터리의 JSON 파일을 Web Clipper에 임포트한다.
Web Clipper 아이콘 → Settings → Templates → Import template

| 파일 | 저장 위치 | 자동 감지 URL |
|---|---|---|
| `clipper-articles.json` | `raw/articles/` | (없음 — 기본 템플릿) |
| `clipper-notes.json` | `raw/notes/` | YouTube, Spotify, Apple Podcasts, Overcast, Goodreads |
| `clipper-papers.json` | `raw/papers/` | arXiv, DOI, Semantic Scholar, HuggingFace Papers |
| `clipper-assets.json` | `raw/assets/` | (없음) |

**사용법:**
1. 저장할 페이지에서 Web Clipper 아이콘 클릭
2. URL에 맞는 템플릿이 자동 선택됨 (YouTube → notes 등)
3. 템플릿 확인 후 Save — `raw/` 하위 폴더에 마크다운으로 저장
4. Claude Code에서 "ingest해줘" 요청

**파일명 자동 생성 규칙:**
- articles / notes: `YYYY-MM-DD-title-slug.md`
- papers: `lastname-YYYY-title-slug.md` (저장 후 shorttitle로 수동 축약 권장)
- assets: 페이지 제목 그대로

**템플릿 공통 프론트매터:**
```yaml
source_url:   # 원본 URL — graphify 노드 메타데이터로 연결
captured_at:  # 수집 날짜
type:         # article / note / paper / asset
author:
tags:         # raw/article 등 — wiki 연결 추적용
```

### 파일 명명 규칙

| 폴더 | 형식 | 예시 |
|---|---|---|
| articles/ | `YYYY-MM-DD-slug.md` | `2026-04-15-karpathy-llm-wiki.md` |
| papers/ | `lastname-YYYY-shorttitle.md` | `vaswani-2017-attention.md` |
| notes/ | `YYYY-MM-DD-slug.md` | `2026-04-10-lecture-notes.md` |
| assets/ | 원본 파일명 유지 | `transformer-diagram.png` |

---

## 플랜 파일로 구축하기

이 시스템의 구축 플랜은 Claude Code의 [superpowers](https://github.com/obra/superpowers-marketplace) 플러그인을 이용해 작성되었다.

```
docs/superpowers/plans/2026-04-15-obsidian-llm-wiki.md
```

플랜 파일을 열고 LLM에게 단계별로 요청하면 구축이 완료된다.
superpowers 플러그인 없이 플랜을 직접 읽고 수동으로 따라해도 된다.

superpowers를 설치하면 플랜 실행을 자동화할 수 있다:

```
/plugin install superpowers@claude-plugins-official
```

---

## Claude Code 없이 사용하는 법 (로컬 LLM / 오픈소스 모델)

이 시스템은 Claude Code에 종속되지 않는다.
**CLAUDE.md / AGENTS.md 내용을 시스템 프롬프트에 붙여넣으면** 어떤 LLM이든 동일하게 동작한다.

### Ollama (로컬 모델)

```bash
ollama run llama3.2
```

대화 시작 시 CLAUDE.md 전체 내용을 시스템 프롬프트로 입력:

```
<system>
[CLAUDE.md 내용 붙여넣기]
</system>
```

### OpenCode / Codex

루트의 `AGENTS.md`를 자동으로 읽는다. 별도 설정 불필요.

### 기타 LLM 도구 (Cursor, Aider 등)

graphify가 각 플랫폼 설치를 지원한다:

```bash
graphify cursor install    # Cursor
graphify aider install     # Aider
graphify codex install     # Codex
```

---

## CLAUDE.md 핵심 규칙 요약

LLM이 따르는 규칙:

1. **Ingest workflow** — 소스 추가 시 summaries/ 작성 → entities/concepts/ 업데이트 → index/log 기록
2. **Query workflow** — wiki/index.md 탐색 → 답변 → 가치 있는 결과는 comparisons/에 저장
3. **Lint workflow** — 주기적 wiki 점검 (모순, 고아 페이지, 오래된 클레임)
4. **graphify 수동 실행** — 사용자 명시 요청 시에만, 실행 전 확인 메시지 출력
5. **fleeting/ 완전 차단** — 어떤 지시로도 override 불가
6. **raw/ 읽기 전용** — LLM은 절대 수정하지 않음

---

## Graphify 명령어 사용법

graphify는 `raw/` 폴더를 지식 그래프로 변환하는 도구다. Claude Code에서 `/graphify` 슬래시 커맨드로 실행한다.

### 그래프 만들기

| 명령어 | 언제 쓰나 |
|---|---|
| `/graphify raw/` | 처음 그래프 생성 |
| `/graphify raw/ --update` | 새 파일 추가 후 증분 업데이트 (변경된 파일만 재처리) |
| `/graphify raw/ --mode deep` | 더 많은 연결 추출 (토큰 더 소모) |

### 그래프 탐색

| 명령어 | 언제 쓰나 |
|---|---|
| `/graphify query "질문"` | 자연어로 그래프 검색 |
| `/graphify path "NodeA" "NodeB"` | 두 개념 사이의 연결 경로 추적 |
| `/graphify explain "NodeName"` | 특정 노드 설명 (어디서 왔고 무엇과 연결됐는지) |

### 소스 추가

| 명령어 | 언제 쓰나 |
|---|---|
| `/graphify add <URL>` | URL 직접 ingestion (arXiv, 트위터, 웹페이지 등) — `raw/`에 자동 저장 |

### 출력 포맷

| 명령어 | 언제 쓰나 |
|---|---|
| `/graphify raw/ --wiki` | `graphify-out/wiki/` 생성 — LLM 탐색용 마크다운 wiki |
| `/graphify raw/ --graphml` | Gephi에서 열 수 있는 포맷으로 export (더 나은 시각화) |
| `/graphify raw/ --mcp` | MCP 서버로 실행 — 다른 에이전트가 그래프 실시간 쿼리 가능 |

### 실용 흐름

```
소스 추가           →  /graphify raw/ --update
개념 검색           →  /graphify query "질문"
두 개념 연결 추적   →  /graphify path "A" "B"
특정 노드 파악      →  /graphify explain "노드명"
예쁜 시각화         →  /graphify raw/ --graphml  (Gephi에서 열기)
```

### 주의사항

- 파일명에 영어 단어 `token`, `secret`, `password` 등이 포함되면 graphify 보안 스캐너에 걸려 스킵된다. 파일명을 한글로 변경하면 해결된다.
- graphify는 `raw/` 만 타겟으로 실행한다. `wiki/`나 루트 전체 실행 금지.

---

## 라이선스

MIT
