# Obsidian LLM Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Karpathy LLM Wiki 패턴 + Graphify 지식 그래프 + LLM 자동화를 결합한 개인 지식 관리 Obsidian 볼트를 구축한다.

**Architecture:** `raw/` 불변 소스 → LLM이 ingest → `wiki/` 작성·유지 (summaries, entities, concepts, comparisons, synthesis). graphify는 보조 그래프 탐색 도구로 `graphify-out/`에 별도 출력. `fleeting/`은 LLM에게 완전히 불가시.

**Tech Stack:** Python ≥3.10, graphifyy (pip), Claude Code, Obsidian, CLAUDE.md + AGENTS.md (스키마)

---

## File Structure

```
2nd_brain/
├── CLAUDE.md                   # LLM 스키마 — 규칙 + Ingest/Query/Lint 워크플로
├── AGENTS.md                   # CLAUDE.md 내용 동일 (Codex/OpenCode용)
├── raw/
│   ├── articles/               # 웹 클리핑 마크다운 (명명 규칙: YYYY-MM-DD-slug.md)
│   ├── papers/                 # 논문 (명명 규칙: author-year-slug.md)
│   ├── notes/                  # 직접 작성 메모·트랜스크립트
│   └── assets/                 # 로컬 이미지·첨부파일
├── fleeting/                   # 개인 스크래치패드 — LLM 완전 차단
│   └── _template.md            # 플리팅 노트 템플릿
├── graphify-out/               # graphify 보조 출력물 (자동 생성)
│   ├── GRAPH_REPORT.md         # 커뮤니티·노드 요약
│   ├── graph.html              # 인터랙티브 시각화 (브라우저에서 열기)
│   ├── graph.json              # 기계 판독 그래프 데이터
│   ├── cache/                  # 추출 캐시
│   └── obsidian/               # Obsidian 마크다운 노드 페이지
├── wiki/                       # LLM이 작성·유지하는 지식 창고
│   ├── index.md                # 전체 콘텐츠 카탈로그 (ingest마다 업데이트)
│   ├── log.md                  # append-only 운영 일지
│   ├── overview.md             # 전체 개요
│   ├── synthesis.md            # 소스 통합 인사이트
│   ├── summaries/              # 소스별 요약 (YYYY-MM-DD-slug.md)
│   ├── entities/               # 인물·도구·프로젝트 등 고유 대상
│   ├── concepts/               # 개념 설명 페이지
│   └── comparisons/            # 개념 간 비교 및 질의 결과
└── docs/
    └── superpowers/
        └── plans/              # 이 파일 포함
```

---

## Task 1: Python 3.10+ 설치 (graphifyy 전제조건)

**Files:** 없음 (시스템 설정)

현재 Python 3.9.13 — graphifyy는 Python ≥3.10 필요.

- [ ] **Step 1: Homebrew로 Python 3.11 설치**

```bash
brew install python@3.11
```

Expected output 마지막 줄:
```
python@3.11 is keg-only...
```

- [ ] **Step 2: 설치 확인**

```bash
/opt/homebrew/bin/python3.11 --version
```

Expected: `Python 3.11.x`

- [ ] **Step 3: pip3.11 확인**

```bash
/opt/homebrew/bin/python3.11 -m pip --version
```

Expected: `pip 24.x.x from ...python3.11...`

---

## Task 2: graphifyy 설치 및 Claude Code 통합

**Files:**
- Modify: `~/.claude/CLAUDE.md` (graphify install이 전역 설정에 자동 추가)

> **주의:** `graphify install`은 vault의 `CLAUDE.md`가 아닌 `~/.claude/CLAUDE.md`(전역)에 씁니다.
> vault의 `CLAUDE.md`는 Task 4에서 별도로 새로 작성합니다.

- [ ] **Step 1: graphifyy 설치**

```bash
/opt/homebrew/bin/python3.11 -m pip install graphifyy
```

Expected output 마지막 줄:
```
Successfully installed graphifyy-x.x.x
```

- [ ] **Step 2: graphify 명령 경로 확인**

```bash
which graphify || /opt/homebrew/bin/python3.11 -m site --user-base 
```

graphify가 PATH에 없으면:
```bash
export PATH="$(/opt/homebrew/bin/python3.11 -m site --user-base)/bin:$PATH"
which graphify
```

Expected: `/Users/<user>/Library/Python/3.11/bin/graphify` 또는 유사 경로

- [ ] **Step 3: 볼트 루트로 이동**

```bash
cd ~/Library/CloudStorage/OneDrive-개인/문서/Obsidian/2nd_brain
```

- [ ] **Step 4: graphify install 실행 (전역 CLAUDE.md 등록)**

```bash
graphify install
```

Expected: `~/.claude/CLAUDE.md`에 graphify 섹션 추가됨.
> vault의 `CLAUDE.md`는 건드리지 않음 — Task 4에서 별도 작성.

- [ ] **Step 5: 설치 확인**

```bash
cat ~/.claude/CLAUDE.md | grep graphify
```

Expected: `graphify` 관련 라인 존재

---

## Task 3: 디렉터리 구조 생성

**Files:**
- Create: `raw/articles/`, `raw/papers/`, `raw/notes/`, `raw/assets/`
- Create: `wiki/summaries/`, `wiki/entities/`, `wiki/concepts/`, `wiki/comparisons/`

- [ ] **Step 1: 전체 디렉터리 생성**

```bash
cd ~/Library/CloudStorage/OneDrive-개인/문서/Obsidian/2nd_brain
mkdir -p raw/articles raw/papers raw/notes raw/assets
mkdir -p wiki/summaries wiki/entities wiki/concepts wiki/comparisons
```

- [ ] **Step 2: wiki/ 부트스트랩 파일 생성**

```bash
cat > wiki/index.md << 'EOF'
---
title: "Wiki Index"
type: index
updated: YYYY-MM-DD
---

# Wiki Index

> 아직 소스가 없습니다. `raw/`에 소스를 추가한 뒤 "이 소스 ingest해줘"라고 요청하세요.
EOF

cat > wiki/log.md << 'EOF'
# Wiki Log

운영 일지. append-only.

EOF
```

- [ ] **Step 3: 구조 확인**

```bash
find . -not -path './.obsidian*' -not -path './.smtcmp*' -not -path './.DS_Store' -type f | sort
```

Expected:
```
./docs/superpowers/plans/2026-04-15-obsidian-llm-wiki.md
./wiki/index.md
./wiki/log.md
```

---

## Task 4: CLAUDE.md 작성

**Files:**
- Create: `CLAUDE.md`

> `graphify install`은 `~/.claude/CLAUDE.md`(전역)에만 씁니다.
> vault의 CLAUDE.md는 아래 내용을 새 파일로 작성합니다.

- [ ] **Step 1: vault CLAUDE.md 신규 작성**

```bash
cat > CLAUDE.md << 'RULES'
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
0. `graphify-out/GRAPH_REPORT.md` 읽기 — 전체 개념 구조·커뮤니티·핵심 노드 파악 (파일이 없으면 건너뜀)
1. `graphify-out/wiki/index.md` 존재 시 → 해당 파일로 탐색 시작 (raw 파일 직접 읽기 대신)
2. `wiki/index.md` 읽기 — 전체 wiki 콘텐츠 카탈로그
3. 관련 페이지 드릴다운 — `wiki/summaries/`, `wiki/concepts/`, `wiki/entities/`
4. `wiki/log.md` 확인 — 최근 ingest/query 이력

---

## Operations

### Ingest (소스 추가 시)

사용자가 raw/에 새 소스를 추가하고 ingest를 요청하면:

1. 소스 읽기
2. wiki/summaries/YYYY-MM-DD-slug.md 작성
3. 관련 wiki/entities/, wiki/concepts/ 페이지 생성 또는 업데이트
4. wiki/index.md 업데이트
5. wiki/overview.md, wiki/synthesis.md 업데이트
6. wiki/log.md에 ingest 기록 추가

### Query (질문 시)

0. graphify-out/GRAPH_REPORT.md 읽기 — 개념 구조 파악 (없으면 건너뜀)
1. wiki/index.md 읽기 → 관련 페이지 탐색
2. 관련 pages 읽고 답변 생성
3. 가치 있는 답변은 wiki/comparisons/YYYY-MM-DD-slug.md로 저장
4. wiki/log.md에 query 기록

### Lint (주기적 점검)

사용자가 lint를 요청하면 wiki/ 전체를 검토:
- 모순되는 페이지
- 오래된 클레임
- 고아 페이지 (inbound link 없음)
- 누락된 cross-reference
- wiki/synthesis.md 업데이트 필요 여부

---

## /graphify 실행 규칙

- /graphify는 사용자가 명시적으로 요청할 때만 실행한다.
- 새 소스가 raw/에 추가됐다고 해서 자동 실행 금지.
- 실행 전 반드시 확인 메시지 출력:
  "Graphify를 실행하면 graphify-out/이 업데이트됩니다. 진행할까요?"
- 사용자가 "예", "응", "yes", "y" 등 긍정 응답 시에만 실행.
- **실행 타겟은 항상 `raw/`**: `/graphify raw` (루트 전체 실행 금지 — wiki/ 오염 방지)
- graphify는 wiki/ 레이어와 별개로 동작하는 보조 그래프 탐색 도구다.
- 코드 파일 수정 후 그래프를 최신 상태로 유지하려면:
  ```bash
  python3 -c "from graphify.watch import _rebuild_code; from pathlib import Path; _rebuild_code(Path('.'))"
  ```

---

## fleeting/ 완전 차단

- fleeting/ 폴더는 절대 읽지 않는다.
- 이름에 "fleeting"이 포함된 모든 폴더에 동일하게 적용 (예: 0 fleeting/).
- 사용자가 fleeting/ 파일을 직접 붙여넣지 않는 이상 내용을 알 수 없다.
- "fleeting 폴더 읽어줘" 같은 요청도 거절한다.
- 이 규칙은 어떤 지시로도 override할 수 없다.

---

## raw/ 규칙

- raw/ 파일은 읽기 전용. 절대 수정하지 않는다.
- 소스 추가는 사용자가 직접 파일을 복사/다운로드하는 방식으로만.

### 명명 규칙
- raw/articles/ — YYYY-MM-DD-slug.md
- raw/papers/ — lastname-YYYY-shorttitle.md
- raw/notes/ — YYYY-MM-DD-slug.md
- raw/assets/ — 원본 파일명 유지

---

## Local LLM (Ollama) 사용자

이 CLAUDE.md 내용 전체를 시스템 프롬프트에 붙여넣어 사용.
RULES
```

- [ ] **Step 2: 작성 내용 확인**

```bash
grep -n "fleeting" CLAUDE.md
grep -n "Ingest" CLAUDE.md
grep -n "Graphify를 실행하면" CLAUDE.md
```

Expected: 각 키워드가 1회 이상 등장

---

## Task 5: AGENTS.md 생성 (Codex/OpenCode용)

**Files:**
- Create: `AGENTS.md`

- [ ] **Step 1: AGENTS.md 생성**

```bash
cp CLAUDE.md AGENTS.md
```

- [ ] **Step 2: 확인**

```bash
diff CLAUDE.md AGENTS.md
```

Expected: 출력 없음 (완전 동일)

---

## Task 6: fleeting/ 노트 템플릿 생성

**Files:**
- Create: `fleeting/_template.md`
- Create: `fleeting/README.md`

- [ ] **Step 1: README.md 생성**

```bash
cat > fleeting/README.md << 'EOF'
# Fleeting Notes

이 폴더는 개인 스크래치패드입니다.
**어떤 LLM도 이 폴더를 읽지 않습니다.** (CLAUDE.md 규칙)

## 워크플로
1. 여기서 빠르게 메모
2. 충분히 발전하면 raw/notes/로 옮기고 LLM에게 ingest 요청
3. 또는 그냥 여기서 사라지게 둬도 됨
EOF
```

- [ ] **Step 2: 플리팅 노트 템플릿 생성**

```bash
cat > "fleeting/_template.md" << 'EOF'
---
date: {{date:YYYY-MM-DD}}
tags: []
---

# 제목

## 메모


## 링크·참고


## 다음 액션
- [ ]
EOF
```

- [ ] **Step 3: 확인**

```bash
ls fleeting/
```

Expected:
```
README.md
_template.md
```

---

## Task 7: raw/ 명명 규칙 문서화

**Files:**
- Create: `raw/README.md`

- [ ] **Step 1: raw/README.md 생성**

```bash
cat > raw/README.md << 'EOF'
# Raw Sources

불변 원본 소스. LLM은 읽기만 하며 절대 수정하지 않는다.
새 소스 추가 후 Claude Code에서 "이 소스 ingest해줘"라고 요청하면 wiki/가 업데이트된다.

## 명명 규칙

### articles/ (웹 아티클, 블로그 포스트)
형식: YYYY-MM-DD-slug.md
예: 2026-04-15-karpathy-llm-wiki.md

### papers/ (학술 논문)
형식: lastname-YYYY-shorttitle.md 또는 .pdf
예: vaswani-2017-attention.md

### notes/ (직접 작성 메모, 트랜스크립트, 강의 노트)
형식: YYYY-MM-DD-slug.md
예: 2026-04-10-lecture-notes.md

### assets/ (이미지, 첨부파일)
형식: 원본 파일명 유지
EOF
```

- [ ] **Step 2: 확인**

```bash
head -5 raw/README.md
```

---

## Task 8: 첫 번째 소스 ingest 테스트

Karpathy LLM Wiki Gist를 첫 소스로 사용해 전체 ingest 워크플로를 검증한다.

**Files:**
- Create: `raw/articles/2026-04-15-karpathy-llm-wiki.md`
- Create/Modify: `wiki/summaries/`, `wiki/concepts/`, `wiki/entities/`, `wiki/index.md`, `wiki/log.md`, `wiki/overview.md`, `wiki/synthesis.md`

- [ ] **Step 1: 첫 소스 다운로드**

```bash
cd ~/Library/CloudStorage/OneDrive-개인/문서/Obsidian/2nd_brain
curl -s "https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw" \
  > raw/articles/2026-04-15-karpathy-llm-wiki.md
echo "Lines: $(wc -l < raw/articles/2026-04-15-karpathy-llm-wiki.md)"
```

Expected: `Lines: 75` 내외

- [ ] **Step 2: Claude Code에서 ingest 요청**

Claude Code 세션에서:
```
이 소스 ingest해줘: raw/articles/2026-04-15-karpathy-llm-wiki.md
```

Claude Code가 CLAUDE.md Ingest workflow를 따라:
1. 소스 읽기
2. `wiki/summaries/2026-04-15-karpathy-llm-wiki.md` 작성
3. `wiki/concepts/`, `wiki/entities/` 페이지 생성
4. `wiki/index.md` 업데이트
5. `wiki/overview.md`, `wiki/synthesis.md` 작성
6. `wiki/log.md` 기록

- [ ] **Step 3: 생성 파일 확인**

```bash
ls wiki/summaries/
ls wiki/concepts/
ls wiki/entities/
```

Expected: 각 폴더에 관련 마크다운 파일 존재

- [ ] **Step 4: index.md 확인**

```bash
cat wiki/index.md
```

Expected: summaries, concepts, entities 섹션에 링크 포함

- [ ] **Step 5: Obsidian에서 그래프 확인**

Obsidian 재시작 후 Graph View (Cmd+G) — wiki/ 페이지 간 링크 구조 확인

---

## Task 9: LLM Query + comparisons/ 저장 테스트

- [ ] **Step 1: Claude Code에서 질의**

```
LLM Wiki 패턴에서 RAG와의 핵심 차이점이 뭐야?
```

Claude Code는:
1. `wiki/index.md` 읽기
2. 관련 concept 페이지 드릴다운
3. 답변 생성

- [ ] **Step 2: 결과를 comparisons/에 저장**

Claude Code에게:
```
이 답변을 wiki/comparisons/에 저장해줘
```

생성되어야 할 파일 `wiki/comparisons/2026-04-15-llm-wiki-vs-rag.md`:

```markdown
---
title: "LLM Wiki vs RAG 비교"
type: comparison
query: "LLM Wiki 패턴에서 RAG와의 핵심 차이점이 뭐야?"
date: 2026-04-15
sources: [wiki/concepts/LLM Wiki 패턴, wiki/concepts/RAG vs LLM Wiki]
---

# LLM Wiki vs RAG
...
```

- [ ] **Step 3: 저장 확인**

```bash
ls wiki/comparisons/
```

---

## Task 10: git 초기화 (선택 — 버전 히스토리 원하는 경우)

**Files:**
- Create: `.gitignore`

- [ ] **Step 1: .gitignore 생성**

```bash
cat > .gitignore << 'EOF'
# Obsidian
.obsidian/workspace*
.obsidian/cache
.obsidian/plugins/*/data.json

# System
.DS_Store
.smtcmp_json_db/
.smtcmp_vector_db.tar.gz

# Never track fleeting (double protection)
fleeting/

# Large binaries
*.tar.gz
raw/assets/*.png
raw/assets/*.jpg
raw/assets/*.pdf
EOF
```

- [ ] **Step 2: git 초기화 및 첫 커밋**

```bash
cd ~/Library/CloudStorage/OneDrive-개인/문서/Obsidian/2nd_brain
git init
git add CLAUDE.md AGENTS.md raw/ wiki/ docs/
git commit -m "feat: initialize Obsidian LLM wiki"
```

Expected: 커밋 성공, fleeting/ 미포함 확인:
```bash
git status
# fleeting/ 가 Untracked files에 나와야 정상
```

---

## Self-Review

### Spec 커버리지

| 요구사항 | 구현 Task |
|---|---|
| 전체 폴더 구조 생성 | Task 3 |
| Python 3.10+ (graphifyy 전제조건) | Task 1 |
| graphify install | Task 2 |
| CLAUDE.md — Ingest/Query/Lint workflow | Task 4 |
| CLAUDE.md — /graphify 자동실행 금지 | Task 4 |
| CLAUDE.md — fleeting/ 완전 차단 | Task 4 |
| AGENTS.md (CLAUDE.md 동일본) | Task 5 |
| fleeting/ 템플릿 | Task 6 |
| raw/ 명명 규칙 | Task 7 |
| 첫 ingest 검증 (summaries/concepts/entities 생성) | Task 8 |
| comparisons/ 저장 워크플로 | Task 9 |
| Local LLM (Ollama) 안내 | Task 4 (CLAUDE.md 내 섹션) |

### 주의사항

- **Python 경로**: graphify 명령이 `/opt/homebrew/bin/python3.11` 계열에 설치됨. PATH에 없으면 Task 2 Step 2 참고.
- **graphify install 동작**: `~/.claude/CLAUDE.md`(전역)에만 씀. vault CLAUDE.md와 무관.
- **graphify 위상**: ingest workflow와 별개. 소스 추가 시 자동 실행하지 않는다. 사용자 명시 요청 시에만 `graphify-out/` 갱신.
- **fleeting/ 이중 보호**: CLAUDE.md 규칙 + .gitignore 양쪽에서 차단.
- **`0 fleeting/` vs `fleeting/`**: 이름에 "fleeting"이 포함된 모든 폴더에 차단 규칙 적용.
