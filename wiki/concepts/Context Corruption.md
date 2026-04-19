---
title: "Context Corruption"
type: concept
sources: [raw/notes/하네스 엔지니어링.md]
related: [하네스 엔지니어링, Context File]
---

# Context Corruption (컨텍스트 부패)

긴 대화 세션에서 LLM이 앞서 주어진 지침이나 맥락을 잊어버리는 현상.

## 증상

- 세션 초반에 정한 규칙을 후반에 어김
- 이전에 논의한 결정을 재논의함
- 일관성 없는 행동

## 해결책 (하네스 관점)

온보딩 문서(`claude.md`)를 **매 세션마다 자동으로 읽게** 설정. 규칙을 프롬프트로 전달하는 게 아니라 구조적으로 항상 주입.

## 관련

- [[하네스 엔지니어링]]
- [[Context File]]
