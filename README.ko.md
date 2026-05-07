![head-banner](images/head-banner.png)

# claude-genius

**"훌륭합니다!"만 5번 돌아온 코드 리뷰, 당신도 받아본 적 있죠?**

*AI가 비위를 맞추는 게 지긋지긋한 개발자를 위한 CLAUDE.md 하나.*

[![English README](https://img.shields.io/badge/README-English-lightgrey?style=flat-square)](README.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](LICENSE)
[![Stars](https://img.shields.io/github/stars/sangrokjung/claude-genius?style=flat-square)](https://github.com/sangrokjung/claude-genius/stargazers)
[![PRs Welcome](https://img.shields.io/badge/PRs-환영-brightgreen.svg?style=flat-square)](#기여하기)

---

## 왜 만들었나

6개월 동안 클로드한테 *"훌륭한 관점입니다!"*만 들었다.

아키텍처 리뷰를 부탁하면 *"이 구조 정말 깔끔하네요."* 버그 가능성을 물으면 *"충분히 고려하셨군요."* 반박하면 *"좋은 지적입니다, 제가 다시 생각해보니..."* 하고 슬그머니 내 말에 동의했다.

[Anthropic도 공식으로 인정](https://openai.com/index/sycophancy-in-gpt-4o/)했다. Claude는 구조적으로 사용자 비위를 맞추도록 훈련되어 있다. OpenAI도 GPT-4o에서 같은 문제를 2025년에 발표했다 — sycophancy 응답 비율이 14.5%에서 6% 이하로 떨어진 건 패치를 적용한 *후*의 일이었다. 이건 음모론이 아니라 RLHF의 부작용이 프로덕션까지 나온 것이다.

`claude-genius`는 이걸 고치는 CLAUDE.md 파일 **하나**다.

프로젝트 루트에 붙여넣으면, AI가 당신이 만나본 가장 솔직한 시니어 개발자처럼 행동하기 시작한다. 기분 좋으라고 말하는 게 아니라, 코드가 터지기 전에 먼저 말하는 사람으로.

---

## 5초 설치

```bash
# 방법 A — curl (가장 빠름)
curl -O https://raw.githubusercontent.com/sangrokjung/claude-genius/main/CLAUDE.md

# 방법 B — 클론 후 복사
git clone https://github.com/sangrokjung/claude-genius.git
cp claude-genius/CLAUDE.md ./
```

같은 디렉토리에서 Claude Code (또는 Claude 연동 에디터) 열면 끝이다.

API 키 없음. 플러그인 없음. 구독 없음. **파일 하나.**

---

## 데모

![before-after](images/before-after.png)

주니어 개발자가 묻는다: *"드디어 첫 PR 머지했어! 이틀 걸렸는데 잘 만든 것 같아서 봐줬으면 해."*

❌ **Sycophant Claude:**

> "정말 대단한 첫걸음이야! 코드도 깔끔하고 로직도 잘 따라가지고, 변수명도 고민한 흔적이 보여. 이 정도면 시니어들도 좋아할 거야. 자랑스러워해도 돼."

✅ **Genius Claude:**

> "`userData` 하나에 세 가지 책임이 다 들어가 있어 — 인증 상태, 프로필 필드, UI 플래그. 나중에 건드릴 수 없는 코드 된다. 분리해. 그리고 동기 `JSON.parse`에 try/catch를 감쌌는데 async 경계가 없잖아. 실패 케이스를 명시하든가, 아니면 그냥 없애."

[**→ 더 많은 비포/애프터 사례 (창업자, 시니어 엔지니어, PM)**](EXAMPLES.md)

---

## 자가 진단: 당신에게 필요한가?

5개 중 3개 이상 "예"면 설치를 권장한다.

1. AI 응답에서 *"Great question!"* 또는 *"좋은 질문이에요"*가 나올 때 즉시 신뢰가 떨어진 적 있다.
2. 아이디어를 공유했더니 AI가 동의했다. 그 동의가 너무 빠르게, 너무 깔끔하게 나와서 *오히려* 불안했다.
3. 반박했을 때 AI가 즉시 *"좋은 지적이에요, 수정하겠습니다"*라고 하면 짜증난 적 있다.
4. 결정을 내려야 하는 순간에 AI가 *"두 가지 다 타당합니다"*라는 답을 준 적 있다.
5. AI가 코드나 계획을 칭찬했는데, 나중에 실제로 문제가 있었던 걸 발견했다.

**진단 결과:** 3개+ "예" → 당신은 동의를 원하는 게 아니다. **정확도**를 원한다. 이 페르소나가 그걸 제공한다.

---

## 작동 원리

![demo-screenshot](images/demo-screenshot.png)

`CLAUDE.md`는 Claude가 프로젝트 단위로 읽는 시스템 프롬프트다. Anthropic이 공식으로 만든 기능이다. `claude-genius`는 이 파일을 이용해서 AI의 기본 태도를 바꾼다.

핵심 지시문 일부 (원문):

```
Do not praise my questions or validate my premises.
If I'm wrong, tell me immediately.
If I push back, do not cave unless I provide new evidence.
Accuracy is your success metric. Not my approval.
```

추가로 포함된 것:
- **한국어/영어 자동 감지** — 한국어로 쓰면 한국어로, 영어로 쓰면 영어로. 어떤 언어든 같은 직설성으로 답변
- **명시적 신뢰도 표기** — `[높음]` / `[중간]` / `[낮음]` / `[알 수 없음]`을 붙여서 얼마나 확신하는지 드러냄
- **면책 조항 제로** — *"참고로 이는 전문적 조언이 아닙니다"* 같은 문장 완전 제거
- **반박해도 입장 유지** — 새로운 증거 없이 반박하면 굴복하지 않음

Claude는 매 응답 전에 이 파일을 읽는다. 효과는 즉시, 프로젝트 컨텍스트 내에서 지속된다.

---

## 지원 언어

한국어와 영어를 자동으로 감지한다. 별도 설정 없다.

한국어로 질문하면 한국어로 직설적으로 답하고, 영어로 질문하면 영어로 직설적으로 답한다. 두 언어 모두 같은 수준의 비타협적 정확성이 적용된다.

---

## FAQ

**Q1. Claude가 아닌 다른 AI에도 작동하나요?**
`CLAUDE.md` 형식은 Claude 전용이지만 페르소나 텍스트 자체는 어디서든 시스템 프롬프트로 작동한다. GPT-5 커스텀 지시문, Gemini 시스템 프롬프트, Cursor의 `.cursorrules`, Codex의 `AGENTS.md` — 다 된다. 원리는 어디서든 작동한다.

**Q2. 너무 무례하게 답하지 않나요?**
정확성을 위해 설계된 것이지 무례함을 위한 게 아니다. 페르소나 지시문에 *"정확하되 쓸데없이 날카롭거나 현학적이지 않게"*라고 명시되어 있다. 당신 시간을 존중하는 시니어 개발자이지, 일부러 기분 나쁘게 하는 AI가 아니다. [EXAMPLES.md](EXAMPLES.md)에서 직접 확인해보면 된다.

**Q3. 커스텀 가능한가요?**
MIT 라이선스다. 포크하고, 필요 없는 섹션 빼고, 도메인별 지시문 추가해도 된다. 이 파일은 완성품이 아니라 출발점이다.

**Q4. 회사 프로젝트에 써도 되나요?**
된다. MIT다. 어트리뷰션도 필요 없다 (스타는 좋다).

**Q5. 유지보수는 계속되나요?**
Anthropic의 공식 `CLAUDE.md` 스펙 변경에 맞춰 업데이트한다. PR도 환영한다.

---

## 기여하기

PR 환영한다. 특히:
- **도메인별 페르소나 변형** (보안, 데이터, 프론트엔드, 백엔드, ML)
- **실제 비포/애프터 사례** ([EXAMPLES.md](EXAMPLES.md)에 추가)
- **타 언어 번역** (일본어, 중국어, 스페인어, 독일어, 프랑스어)
- **실패 모드** — 페르소나가 무너지는 상황과 프롬프트 보완 제안

---

## 라이선스

MIT — [LICENSE](LICENSE) 참조.

> *"AI가 나한테 한 말 중 가장 유용한 건 답이 아니었어. '먼저 하나만 짚을게'였어. 그러고 나서 내 두 시간짜리 회의가 결정을 하나도 안 했다고 했어."*

---

*Made by [@sangrokjung](https://x.com/sangrokjung) · [English README](README.md) · [더 많은 사례](EXAMPLES.md)*
