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
