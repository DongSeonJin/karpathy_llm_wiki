# Obsidian LLM Wiki

Andrej Karpathy가 제안한 LLM Wiki 패턴에 Graphify 지식 그래프를 결합한 개인 지식 관리 시스템이다.
아티클, 논문, 메모 등 원본 소스를 `raw/`에 쌓아두면 LLM이 자동으로 읽고 분석해
개념·엔티티·관계를 추출한 wiki를 만들어준다. 소스가 쌓일수록 wiki는 점점 풍부해진다.

### Obsidian과의 관계

Obsidian은 이 시스템의 **브라우저이자 IDE** 역할을 한다.
LLM(Claude Code)이 wiki 파일을 작성하면 Obsidian에서 실시간으로 확인할 수 있다.

- **Graph View** — `wiki/obsidian/` 안의 페이지들이 서로 링크로 연결되어 지식 그래프를 시각화한다
- **Canvas** — graphify가 커뮤니티별로 구조화된 레이아웃을 자동 생성한다
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
raw/          ← 불변 원본 소스 (LLM 읽기 전용)
    ├── articles/   웹 아티클, 블로그 포스트
    ├── papers/     학술 논문
    ├── notes/      직접 작성 메모, 강의 노트, 트랜스크립트
    └── assets/     이미지, 첨부파일
    │
    └─ graphify 실행 (Claude Code에서 "graphify 실행해줘")
              │
              ▼
graphify-out/ ← 그래프 파이프라인 원본 출력
    ├── GRAPH_REPORT.md   (커뮤니티·노드 요약)
    ├── graph.html        (브라우저 인터랙티브 시각화)
    ├── graph.json        (기계 판독 그래프 데이터)
    └── obsidian/         (Obsidian 마크다운 페이지)
              │
              └─ wiki/로 동기화 (LLM이 읽는 영역)

wiki/         ← LLM + Obsidian이 탐색하는 영역
    ├── GRAPH_REPORT.md
    ├── graph.json
    ├── obsidian/
    └── analyses/         (질의 결과 저장)

fleeting/     ← 개인 스크래치패드 — LLM 완전 차단, 사람만 읽음
CLAUDE.md     ← LLM 스키마 (규칙·워크플로 정의)
AGENTS.md     ← CLAUDE.md 동일본 (Codex / OpenCode용)
```

---

## 디렉터리 용도

| 폴더 | 용도 |
|---|---|
| `raw/` | 불변 원본 소스. LLM은 읽기만 하며 절대 수정하지 않는다. |
| `wiki/` | graphify가 자동 생성·유지하는 지식 그래프. LLM이 탐색하고 질의 결과를 저장하는 영역. |
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
2. Claude Code에서: "graphify 실행해줘"
3. 확인 메시지 → "예"
4. graphify-out/ 생성 → wiki/ 동기화
```

### 파일 명명 규칙

| 폴더 | 형식 | 예시 |
|---|---|---|
| articles/ | `YYYY-MM-DD-slug.md` | `2026-04-15-karpathy-llm-wiki.md` |
| papers/ | `lastname-YYYY-shorttitle.md` | `vaswani-2017-attention.md` |
| notes/ | `YYYY-MM-DD-slug.md` | `2026-04-10-lecture-notes.md` |
| assets/ | 원본 파일명 유지 | `transformer-diagram.png` |

---

## 플랜 실행에 필요한 스킬: superpowers

플랜 파일은 [superpowers](https://github.com/obra/superpowers-marketplace) 플러그인의 스킬을 사용한다.
Claude Code에 플러그인을 설치하면 플랜을 자동으로 실행할 수 있다.

### 설치

Claude Code에서 아래 명령어로 설치:

```
/plugin install superpowers@claude-plugins-official
```

설치 후 활성화:

```
/reload-plugins
```

### 플랜 실행에 사용되는 주요 스킬

| 스킬 | 역할 |
|---|---|
| `superpowers:writing-plans` | 플랜 문서 작성 |
| `superpowers:subagent-driven-development` | 태스크별 서브에이전트 투입 실행 |
| `superpowers:executing-plans` | 현재 세션에서 순차 실행 |
| `superpowers:systematic-debugging` | 오류 발생 시 체계적 디버깅 |

플랜을 실행하려면 Claude Code에서:

```
execute the plan
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

LLM이 따르는 4가지 규칙:

1. **GRAPH_REPORT 조건부 읽기** — raw/ 또는 wiki/ 관련 작업에서만 `wiki/GRAPH_REPORT.md` 읽기
2. **graphify 수동 실행** — 사용자 명시 요청 시에만, 실행 전 확인 메시지 출력
3. **fleeting/ 완전 차단** — 어떤 지시로도 override 불가
4. **raw/ 읽기 전용** — LLM은 절대 수정하지 않음

---

## 라이선스

MIT
