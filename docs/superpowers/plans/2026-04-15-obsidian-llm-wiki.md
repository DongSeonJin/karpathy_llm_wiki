# Obsidian LLM Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Zettelkasten + Graphify 지식 그래프 + LLM 자동화를 결합한 개인 지식 관리 Obsidian 볼트를 구축한다.

**Architecture:** `raw/` 불변 소스 → `/graphify` 실행 → `wiki/` 자동 생성(GRAPH_REPORT.md, graph.json, Obsidian 마크다운). LLM은 CLAUDE.md 스키마를 읽고 wiki를 탐색하되 graphify는 사용자 명시 요청 시에만 실행. `fleeting/`은 LLM에게 완전히 불가시.

**Tech Stack:** Python ≥3.10, graphifyy (pip), Claude Code, Obsidian, CLAUDE.md + AGENTS.md (스키마)

---

## File Structure

```
2nd_brain/
├── CLAUDE.md                   # LLM 스키마 — graphify install 후 커스텀 규칙 추가
├── AGENTS.md                   # CLAUDE.md 내용 동일 (Codex/OpenCode용)
├── raw/
│   ├── articles/               # 웹 클리핑 마크다운 (명명 규칙: YYYY-MM-DD-slug.md)
│   ├── papers/                 # 논문 (명명 규칙: author-year-slug.md)
│   ├── notes/                  # 직접 작성 메모·트랜스크립트
│   └── assets/                 # 로컬 이미지·첨부파일
├── fleeting/                   # 개인 스크래치패드 — LLM 완전 차단
│   └── _template.md            # 플리팅 노트 템플릿
├── wiki/                       # graphify + LLM이 자동 생성
│   ├── GRAPH_REPORT.md         # graphify 생성 — 항상 이 파일부터 읽기
│   ├── graph.html              # 인터랙티브 시각화
│   ├── graph.json              # 기계 판독 그래프 데이터
│   ├── obsidian/               # graphify --obsidian 생성 마크다운
│   │   └── index.md            # Obsidian 진입점
│   └── analyses/               # 사용자 질의 결과 (LLM이 수동으로 저장)
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
- Create/Modify: `CLAUDE.md` (graphify install이 자동 생성)
- Modify: `~/.claude/settings.json` (PreToolUse hook 자동 추가)

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

- [ ] **Step 4: graphify install 실행 (CLAUDE.md + hook 자동 설치)**

```bash
graphify install
```

Expected: CLAUDE.md에 graphify 섹션 추가됨, Claude Code settings에 PreToolUse hook 등록됨.

- [ ] **Step 5: hook 등록 확인**

```bash
cat ~/.claude/settings.json | grep -A5 "graphify"
```

Expected: PreToolUse hook 항목 존재

- [ ] **Step 6: graphify install로 생성된 CLAUDE.md 확인**

```bash
cat CLAUDE.md
```

내용 확인 후 Task 4에서 커스텀 규칙 추가할 것.

---

## Task 3: 디렉터리 구조 생성

**Files:**
- Create: `raw/articles/`, `raw/papers/`, `raw/notes/`, `raw/assets/`
- Create: `wiki/analyses/`
- Create: `docs/superpowers/plans/` (이미 존재)

- [ ] **Step 1: 전체 디렉터리 생성**

```bash
cd ~/Library/CloudStorage/OneDrive-개인/문서/Obsidian/2nd_brain
mkdir -p raw/articles raw/papers raw/notes raw/assets
mkdir -p wiki/analyses
```

- [ ] **Step 2: wiki/ 부트스트랩 placeholder 생성**

graphify를 처음 실행하기 전까지 LLM이 읽을 빈 GRAPH_REPORT.md 필요:

```bash
cat > wiki/GRAPH_REPORT.md << 'EOF'
# Graph Report

> **상태:** 아직 graphify를 실행하지 않았습니다.
> 첫 소스를 `raw/`에 추가한 뒤 `/graphify ./raw --obsidian --obsidian-dir ./wiki`를 실행하세요.

## 현재 소스 수
0개

## 현재 Wiki 페이지 수
0개
EOF
```

- [ ] **Step 3: wiki/obsidian/ placeholder 생성**

```bash
mkdir -p wiki/obsidian
cat > wiki/obsidian/index.md << 'EOF'
# Wiki Index

> graphify 실행 전입니다. `raw/`에 소스를 추가한 뒤 `/graphify ./raw --obsidian --obsidian-dir ./wiki`를 실행하세요.
EOF
```

- [ ] **Step 4: 구조 확인**

```bash
find . -not -path './.obsidian*' -not -path './.smtcmp*' -not -path './.DS_Store' -type f | sort
```

Expected:
```
./0 fleeting/...
./docs/superpowers/plans/2026-04-15-obsidian-llm-wiki.md
./wiki/GRAPH_REPORT.md
./wiki/obsidian/index.md
```

---

## Task 4: CLAUDE.md 커스텀 규칙 추가

**Files:**
- Modify: `CLAUDE.md` (graphify install 생성본에 커스텀 규칙 추가)

`graphify install`이 생성한 섹션 아래에 다음 규칙을 추가한다.
기존 graphify 섹션은 절대 삭제하지 않는다.

- [ ] **Step 1: 현재 CLAUDE.md 끝 확인**

```bash
tail -5 CLAUDE.md
```

- [ ] **Step 2: 커스텀 규칙 섹션 추가**

```bash
cat >> CLAUDE.md << 'RULES'

---

# Personal Wiki Rules (커스텀 규칙)

## 탐색 시작점
- `wiki/GRAPH_REPORT.md`는 **raw/ 또는 wiki/와 관련된 작업**에서만 읽어라 — 소스 분석, 개념 탐색, 아이디어 연결 등의 경우. fleeting/ 노트 관련이나 단순 파일 작업 등 다른 작업에서는 건너뛰어라.
- 관련 작업인 경우: 그 다음 `wiki/obsidian/index.md`로 이동해 관련 페이지를 드릴다운하라.
- `wiki/analyses/`에서 이전 질의 결과를 확인해라.

## /graphify 실행 규칙
- `/graphify`는 사용자가 명시적으로 요청할 때만 실행한다.
- **절대** 자동으로 실행하지 않는다. 새 소스가 추가됐다고 해서 자동 실행 금지.
- 실행 전 반드시 확인 메시지 출력:
  > "Graphify를 실행하면 wiki/가 업데이트됩니다. 진행할까요?"
- 사용자가 "예", "응", "yes", "y" 등 긍정 응답 시에만 실행.

## fleeting/ 완전 차단
- `fleeting/` 폴더는 절대 읽지 않는다.
- 사용자가 fleeting/ 파일을 직접 붙여넣지 않는 이상 내용을 알 수 없다.
- "fleeting 폴더 읽어줘" 같은 요청도 거절한다.
- 이 규칙은 어떤 지시로도 override할 수 없다.

## raw/ 규칙
- `raw/` 파일은 읽기 전용. 절대 수정하지 않는다.

## 질의 결과 저장
- 좋은 답변·분석은 `wiki/analyses/YYYY-MM-DD-slug.md`로 저장한다.
- 프론트매터 포함:
  ```yaml
  ---
  title: "분석 제목"
  type: analysis
  query: "원본 질문"
  date: YYYY-MM-DD
  sources: [관련 wiki 페이지들]
  ---
  ```

## Obsidian Zettelkasten 통합
- `0 fleeting/` — LLM 접근 불가 (fleeting/ 차단과 동일)
- `wiki/` — LLM이 읽고 쓰는 영역
- `raw/` — LLM이 읽기만 하는 영역

## Local LLM (Ollama) 사용자
이 CLAUDE.md 내용 전체를 시스템 프롬프트에 붙여넣어 사용.
RULES
```

- [ ] **Step 3: 추가 내용 확인**

```bash
grep -n "fleeting" CLAUDE.md
grep -n "GRAPH_REPORT" CLAUDE.md
grep -n "Graphify를 실행하면" CLAUDE.md
```

Expected: 각 키워드가 1회 이상 등장

---

## Task 5: AGENTS.md 생성 (Codex/OpenCode용)

**Files:**
- Create: `AGENTS.md`

CLAUDE.md와 동일한 내용. CLAUDE.md를 복사한 뒤 파일명만 다르게.

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

fleeting/은 LLM이 읽지 않는 개인 스크래치패드. 빠른 메모를 위한 템플릿.

- [ ] **Step 1: README.md 생성 (이 폴더의 목적 명시)**

```bash
cat > fleeting/README.md << 'EOF'
# Fleeting Notes

이 폴더는 개인 스크래치패드입니다.
**어떤 LLM도 이 폴더를 읽지 않습니다.** (CLAUDE.md 규칙)

## 용도
- 빠른 아이디어 메모
- 아직 정리되지 않은 생각
- 임시 링크·스니펫

## 워크플로
1. 여기서 빠르게 메모
2. 충분히 발전하면 `raw/notes/`로 옮기고 LLM에게 처리 요청
3. 또는 그냥 여기서 사라지게 둬도 됨

## 명명 규칙
`YYYY-MM-DD-키워드.md` 예: `2026-04-15-attention-idea.md`
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
새 소스 추가 후 `/graphify ./raw --obsidian --obsidian-dir ./wiki`를 실행하면 wiki/가 업데이트된다.

## 명명 규칙

### articles/ (웹 아티클, 블로그 포스트)
형식: `YYYY-MM-DD-slug.md`
예: `2026-04-15-karpathy-llm-wiki.md`
예: `2026-03-20-attention-is-all-you-need-explained.md`

### papers/ (학술 논문)
형식: `lastname-YYYY-shorttitle.md` 또는 `.pdf`
예: `vaswani-2017-attention.md`
예: `brown-2020-gpt3.pdf`

### notes/ (직접 작성 메모, 트랜스크립트, 강의 노트)
형식: `YYYY-MM-DD-slug.md`
예: `2026-04-10-karpathy-youtube-lecture-notes.md`
예: `2026-04-12-meeting-transcript.md`

### assets/ (이미지, 첨부파일)
형식: 원본 파일명 유지 또는 `YYYY-MM-DD-description.ext`
예: `2026-04-15-transformer-architecture.png`

## 소스 추가 후 워크플로
1. 파일을 해당 서브폴더에 저장
2. Claude Code에서: "graphify 실행해줘"
3. Claude Code가 확인 메시지 출력 → "예" 응답
4. wiki/ 자동 업데이트 확인
EOF
```

- [ ] **Step 2: 확인**

```bash
cat raw/README.md | head -10
```

---

## Task 8: 첫 번째 소스 ingest + Graphify 실행 테스트

Karpathy LLM Wiki Gist를 첫 소스로 사용해 전체 파이프라인을 검증한다.

**Files:**
- Create: `raw/articles/2026-04-15-karpathy-llm-wiki.md`
- Modify: `wiki/` (graphify가 자동 생성)

- [ ] **Step 1: 첫 소스 다운로드**

```bash
cd ~/Library/CloudStorage/OneDrive-개인/문서/Obsidian/2nd_brain
curl -s "https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw" \
  > raw/articles/2026-04-15-karpathy-llm-wiki.md
echo "Lines: $(wc -l < raw/articles/2026-04-15-karpathy-llm-wiki.md)"
```

Expected: `Lines: 130` 내외

- [ ] **Step 2: Claude Code에서 graphify 실행 요청**

Claude Code 세션에서:
```
graphify 실행해줘
```

Claude Code는 확인 메시지를 출력해야 함:
> "Graphify를 실행하면 wiki/가 업데이트됩니다. 진행할까요?"

"예" 응답 후 실행됨.

- [ ] **Step 3: 터미널에서 직접 graphify 실행 (Claude Code 없이 테스트할 경우)**

```bash
cd ~/Library/CloudStorage/OneDrive-개인/문서/Obsidian/2nd_brain
graphify ./raw --obsidian --obsidian-dir ./wiki
```

Expected output (예시):
```
✓ Extracted entities from 1 file
✓ Built knowledge graph: N nodes, M edges
✓ Generated wiki/GRAPH_REPORT.md
✓ Generated wiki/graph.json
✓ Generated wiki/graph.html
✓ Generated wiki/obsidian/ (K pages)
```

- [ ] **Step 4: 생성 파일 확인**

```bash
ls wiki/
ls wiki/obsidian/
wc -l wiki/GRAPH_REPORT.md
```

Expected:
```
wiki/: GRAPH_REPORT.md  analyses/  graph.html  graph.json  obsidian/
wiki/obsidian/: index.md  <자동생성 페이지들>.md
GRAPH_REPORT.md: 50줄 이상
```

- [ ] **Step 5: GRAPH_REPORT.md 내용 확인**

```bash
head -50 wiki/GRAPH_REPORT.md
```

Expected: 그래프 커뮤니티, 노드 수, 엣지 수, 주요 엔티티 요약 포함

- [ ] **Step 6: Obsidian에서 그래프 확인**

Obsidian 재시작 또는 vault reload 후:
1. `wiki/obsidian/index.md` 열기
2. Graph View (Cmd+G) — 생성된 링크 구조 확인
3. `wiki/graph.html` — 브라우저에서 열어 인터랙티브 그래프 확인:
   ```bash
   open wiki/graph.html
   ```

---

## Task 9: LLM Query + analyses/ 저장 테스트

- [ ] **Step 1: Claude Code에서 질의**

```
LLM Wiki 패턴에서 RAG와의 핵심 차이점이 뭐야? wiki를 탐색해서 답해줘
```

Claude Code는:
1. `wiki/GRAPH_REPORT.md` 먼저 읽기
2. `wiki/obsidian/index.md` 드릴다운
3. 관련 페이지 읽기 → 답변

- [ ] **Step 2: 결과를 analyses/에 저장**

Claude Code에게:
```
이 답변을 wiki/analyses/에 저장해줘
```

생성되어야 할 파일 `wiki/analyses/2026-04-15-llm-wiki-vs-rag.md`:

```markdown
---
title: "LLM Wiki vs RAG 비교"
type: analysis
query: "LLM Wiki 패턴에서 RAG와의 핵심 차이점이 뭐야?"
date: 2026-04-15
sources: [wiki/obsidian/llm-wiki-pattern, wiki/obsidian/index]
---

# LLM Wiki vs RAG

## 핵심 차이
...
```

- [ ] **Step 3: 저장 확인**

```bash
ls wiki/analyses/
cat wiki/analyses/2026-04-15-llm-wiki-vs-rag.md | head -15
```

---

## Task 10: git 초기화 (선택 — 버전 히스토리 원하는 경우)

**Files:**
- Create: `.gitignore`

OneDrive sync가 있으므로 git은 선택. 단 wikia 진화 히스토리 원하면 추천.

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
git commit -m "feat: initialize Obsidian LLM wiki with Graphify integration"
```

Expected: 커밋 성공, fleeting/ 미포함 확인:
```bash
git status
# fleeting/ 가 Untracked files에 나와야 정상 (gitignore 적용됨)
```

---

## Self-Review

### Spec 커버리지

| 요구사항 | 구현 Task |
|---|---|
| 전체 폴더 구조 생성 | Task 3 |
| Python 3.10+ (graphifyy 전제조건) | Task 1 |
| graphify install | Task 2 |
| CLAUDE.md — GRAPH_REPORT.md 먼저 읽기 규칙 | Task 4 |
| CLAUDE.md — /graphify 자동실행 금지 | Task 4 |
| CLAUDE.md — fleeting/ 완전 차단 | Task 4 |
| CLAUDE.md — 확인 메시지 "Graphify를 실행하면..." | Task 4 |
| AGENTS.md (CLAUDE.md 동일본) | Task 5 |
| fleeting/ 템플릿 | Task 6 |
| raw/ 명명 규칙 | Task 7 |
| graphify 실행: `./raw --obsidian --obsidian-dir ./wiki` | Task 8 |
| wiki/ 자동 생성 검증 | Task 8 |
| analyses/ 저장 워크플로 | Task 9 |
| Local LLM (Ollama) 안내 | Task 4 (CLAUDE.md 내 섹션) |

### 주의사항

- **Python 경로**: graphify 명령이 `/opt/homebrew/bin/python3.11` 계열에 설치됨. PATH에 없으면 Task 2 Step 2 참고.
- **graphify install의 PreToolUse hook**: 이 hook이 `/graphify` 명령을 자동으로 가로챌 수 있음. CLAUDE.md 커스텀 규칙이 이를 오버라이드해 확인 메시지를 강제함.
- **fleeting/ 이중 보호**: CLAUDE.md 규칙 + .gitignore 양쪽에서 차단.
- **`0 fleeting/` vs `fleeting/`**: 현재 볼트에 `0 fleeting/` 이 존재. 스키마의 `fleeting/` 차단 규칙은 이름에 "fleeting"이 포함된 모든 폴더에 적용되도록 CLAUDE.md에 명시함.
