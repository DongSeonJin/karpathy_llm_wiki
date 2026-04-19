---
title: "Auto Enforcement System"
type: concept
sources: [raw/notes/하네스 엔지니어링.md]
related: [하네스 엔지니어링, Garbage Collection]
---

# 자동 강제 시스템 (Auto Enforcement System)

린터(Linter)와 테스트 도구를 통해 코드 품질을 자동으로 교정하는 루프.

## 동작 방식

코드가 기준에 맞지 않으면 → 자동으로 수정 요청 → AI가 수정 → 재검사

프롬프트로 "잘 짜줘"라고 부탁하는 게 아니라, 기준 미달 시 통과 자체가 불가능하게 만드는 구조.

## 구성

- Pre-commit 훅
- 린터 (ESLint, Pylint 등)
- 테스트 자동화

## 관련

- [[하네스 엔지니어링]]
- [[Garbage Collection]]
