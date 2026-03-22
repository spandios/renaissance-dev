# How to Build AI Product Sense

**Source:** [Lenny's Newsletter](https://www.lennysnewsletter.com/p/how-to-build-ai-product-sense)  
**Author:** Tal Raviv & Aman Khan  
**Date:** 2026-03-22  
**Category:** AI Product Development, Learning

---

## The Big Idea

**AI product sense isn't built by watching demos or reading infographics—it's built by using AI coding agents like Cursor and Claude Code for daily, non-technical work, where you can see how AI actually operates under the hood.**

진정한 AI 제품 감각은 화려한 데모나 인포그래픽이 아니라, **Cursor나 Claude Code 같은 AI 코딩 에이전트를 일상적인 비기술적 작업에 사용하면서** 내부 동작을 직접 관찰할 때 생긴다.

---

## Argument Breakdown

### 1. The Problem: AI Hype vs. Real Understanding

**현재 상황:**
- "AI hype industrial complex": 대부분의 AI 콘텐츠는 교육이 아니라 FOMO 유발이 목적
- "This model is INSANE" 류의 포스트, 지저분한 현실을 숨긴 데모, 복잡한 다이어그램
- 용어는 알지만 ("subagents", "context engineering", "agent memory") 실전에서 사용하지 못함

**결과:**
- ChatGPT, Granola, Lovable 같은 consumer-grade UI만 써서는 진짜 이해가 생기지 않음
- 표면적 지식만 쌓이고 깊이가 없음

### 2. The Solution: Move to Coding Agents

**핵심 전략:**
- **Cursor/Claude Code 같은 AI 코딩 에이전트로 이동**
- 이들은 작업 과정을 투명하게 보여줌:
  - AI의 reasoning을 읽을 수 있음
  - tool call을 inspect할 수 있음
  - context window가 채워지는 것을 볼 수 있음

**학습 효과:**
- 엔지니어가 AI 애플리케이션을 만들 때 겪는 장벽을 직접 체험
- 자연스럽게 해결책을 떠올리게 됨
- 업계 동향과 발표를 예측할 수 있게 됨

**저자들의 경험:**
> "We've learned more about how AI products actually work in the past **3 months** by using Cursor for daily, non-technical tasks than in **3 years** of using ChatGPT."

### 3. Why Cursor? The Three Components

Cursor는 복잡해 보이지만 실제로는 **익숙한 3가지 도구의 조합**:

1. **ChatGPT** (Agent 모드)
2. **Text Editor** (파일 편집)
3. **File Explorer** (파일 탐색)

**핵심 차이점:**
```
ChatGPT Projects vs Cursor
━━━━━━━━━━━━━━━━━━━━━━━━
ChatGPT:
- 대화에 모든 것이 쌓임
- 수동으로 프로젝트 지식에 복사

Cursor:
- 파일에 출력물이 저장됨
- 대화는 일회성 (disposable)
- 매일 사용하면서 자동으로 지식 베이스 개선
```

**Form Factor의 힘:**
- 특정 파일/폴더를 각 채팅에 드래그 앤 드롭 (selective context)
- AI가 파일을 직접 수정 (malleable knowledge)
→ **tight loop**: 모든 채팅이 자동으로 프로젝트 지식 개선

### 4. The Core Concepts: Models, Tools, Context

#### A. Model Selection
- Cursor는 여러 모델을 선택할 수 있음 (OpenAI, Anthropic, Gemini 등)
- 같은 쿼리를 여러 모델로 시도하면서 차이점 학습
- 예시: Claude는 저작권에 민감 (Disney 가사 수정 거부), OpenAI는 덜 민감하지만 tool calling이 불안정

**저자의 선택:**
- 글쓰기, 복잡한 기획, 미묘한 조언: **Claude Opus**
- 많은 컨텍스트 필요한 작업: **Claude Sonnet** (1M token context window)

**중요한 통찰:**
> "There are only a few frontier LLMs, and they're available to all product teams. **Innovation is how we apply them.**"

몇 개 안 되는 최고급 LLM은 모든 팀이 사용 가능. **차별화는 어떻게 적용하느냐에 달림.**

#### B. Tool Calling
- **LLM은 텍스트만 생성**, 하지만 action을 취할 때는 **tool을 호출**
- Tool calling은 reasoning/writing과 별개의 skill

**작동 방식:**
1. LLM이 tool 이름을 출력 (예: `read_file`, `search_replace`)
2. Cursor가 인식하고 실제로 실행
3. 결과를 LLM에게 전달
4. LLM이 다음 action 결정

**비유:**
- LLM = 집주인 (원하는 것을 설명)
- Cursor = 핸디맨 (실제 작업 수행)

Cursor가 하는 역할을 기술 용어로 "**MCP client**" 또는 "**agent harness**"라고 부름.

**Mental Model:**
> "Classic ChatGPT" = LLM과 인간의 DM  
> "AI Agents" = LLM, 인간, 도구의 3자 그룹 채팅

#### C. Model Context Protocol (MCP)
- 문제: 각 SaaS가 각 LLM마다 별도 connector 구축해야 함
- 해결: **MCP** = 표준화된 인터페이스 (USB/Bluetooth와 같은 개념)
- SaaS는 한 번만 MCP connector 구축하면 모든 LLM에서 작동

**실무 질문 변화:**
> "우리 agent가 X를 할 수 있을까?" 
> → "어떤 도구가 필요하고, 우리 모델이 그걸 얼마나 잘 호출할까?"

### 5. Building Your Personal OS

**실습 프로젝트:**
- 연락처, 메모, 녹취록, 할 일 등을 정리하는 경량 생산성 시스템 구축
- RAG, memory, context engineering 학습
- 실제로 매일 사용할 수 있는 AI 시스템 완성

**구조:**
```
Build AI Product Sense/
├── knowledge/       # 지식 저장
│   ├── contacts.md
│   ├── notes.md
│   └── goals.md
├── memory/          # 대화 컨텍스트
└── tasks/           # 할 일 관리
```

**학습 효과:**
- Discovery, distribution, pricing 무시하고 **기술적 가능성에 집중**
- RAG (Retrieval-Augmented Generation) 체험
- Context engineering 실습
- Agent memory 이해

---

## Context: Why This Matters Now

### 1. The "AI Product Sense Gap"
현재 많은 PM/개발자들이 겪는 문제:
- AI 용어는 알지만 실제로 어떻게 작동하는지 모름
- ChatGPT만 써서는 깊은 이해 불가능
- 데모와 실제 제품 사이의 간극이 큼

### 2. The Shift from Consumer UIs to Developer Tools
**패러다임 전환:**
```
ChatGPT/Lovable (Consumer UI)
→ 결과만 보임, 블랙박스

Cursor/Claude Code (Developer Tool)
→ 과정 투명, reasoning 노출, tool call 관찰
```

### 3. Tool Calling is the New Competitive Advantage
- 같은 LLM을 다 쓸 수 있음
- 차별화는 **도구 설계 + tool calling 최적화**에서 나옴
- MCP 표준화로 SaaS 통합이 쉬워짐

### 4. Context Engineering > Prompt Engineering
- 단순히 좋은 prompt 작성을 넘어
- **어떤 context를 언제 주입할지** 설계하는 것이 핵심
- RAG, memory, selective context 모두 context engineering의 일부

---

## Application: 개발자로서 어떻게 적용할 것인가

### 1. 즉시 실천 가능한 것들

#### A. Cursor를 일상 도구로 전환
**Before:**
```
ChatGPT: 질문 → 답변 → 복사 → 붙여넣기
```

**After:**
```
Cursor: 질문 → 파일 직접 수정 → 지식 자동 축적
```

**구체적 사용 사례:**
- 전략 문서 작성
- 데이터 분석
- 우선순위 정리
- 의사결정 메모
- 회의록 정리

#### B. 모델 비교 습관 들이기
중요한 작업에는 **여러 모델로 시도**:
```python
# 같은 쿼리로 테스트
models = ["Opus 4.5", "Sonnet 4", "GPT-5.3", "Gemini 3 Pro"]
for model in models:
    response = agent.query(task, model=model)
    compare(response)
```

**학습 포인트:**
- 어떤 모델이 어떤 작업에 강한가?
- Tool calling 성공률 차이
- Context 처리 능력 차이

#### C. Tool Calling 관찰하기
매번 agent가 작동할 때:
1. "어떤 도구를 사용했나?"
2. "왜 이 순서로 호출했나?"
3. "실패했다면 어디서?"

**예시:**
```
Task: "contacts.md에서 서울 거주자만 추출"

Tool Calls:
1. read_file("contacts.md")
2. [Thinking: 파싱, 필터링 로직]
3. write_file("seoul_contacts.md", filtered_data)

→ 관찰: "파일 읽기 → 처리 → 쓰기" 패턴 학습
```

### 2. 중기 목표 (1-3개월)

#### A. Personal OS 구축
```markdown
# my-workspace/
├── IDENTITY.md          # 나는 누구인가
├── knowledge/
│   ├── projects.md      # 진행 중인 프로젝트
│   ├── people.md        # 주요 인물/관계
│   ├── decisions.md     # 중요한 결정 기록
│   └── learnings.md     # 배운 것들
├── memory/
│   └── conversations/   # 주요 대화 기록
└── tasks/
    ├── backlog.md       # 해야 할 일
    └── done.md          # 완료한 일
```

**효과:**
- 매일 Cursor와 상호작용하면서 지식 베이스 자동 개선
- Context가 점점 풍부해짐
- AI가 나를 더 잘 이해하게 됨

#### B. 프로젝트에 적용
팀 프로젝트에서 배운 것 적용:

**Before (감 기반):**
```
PM: "AI로 이거 할 수 있을까?"
Dev: "...아마도요?"
```

**After (경험 기반):**
```
PM: "AI로 이거 할 수 있을까?"
Dev: "네, 필요한 도구는 X, Y, Z고,
     Opus가 tool calling을 잘하니까
     3일이면 프로토타입 가능합니다."
```

#### C. RAG 시스템 이해
**직접 경험하면서 배우는 것:**
- Context window 제한 체감
- Retrieval 전략의 중요성
- Chunking과 embedding의 trade-off
- "Selective context"가 왜 중요한지

### 3. 장기 목표 (3-6개월)

#### A. AI Product Sense 획득
**지표:**
- [ ] "Context rot" 문제를 코드 보기 전에 예측 가능
- [ ] 새 AI 기능 공지 보고 "아, 이건 tool calling 개선이네" 즉시 파악
- [ ] Agent memory 전략을 직관적으로 설계 가능
- [ ] 모델 선택 기준을 명확히 설명 가능

#### B. 팀 AI 역량 향상
**할 수 있는 것:**
1. **내부 워크샵**: Cursor 도입 + 실습
2. **Best Practice 문서화**: 어떤 작업에 어떤 모델, 어떤 도구
3. **Tool Library 구축**: 팀 전용 MCP server 개발
4. **Context Engineering 가이드**: 효과적인 knowledge base 설계

#### C. 실제 AI 제품 설계
배운 것을 제품에 적용:

**User Story:**
```
"사용자가 support ticket을 보내면,
AI가 과거 유사 케이스를 찾아서 (RAG)
user context를 기억하고 (Memory)
적절한 해결책을 제안한다 (Tool Calling)"
```

**설계 질문:**
- 어떤 모델? (Opus vs Sonnet vs GPT)
- 어떤 도구가 필요? (Zendesk API, Knowledge Base Search)
- Context를 어떻게 관리? (Session memory vs Long-term memory)
- MCP로 어떤 것들 통합? (Slack, Notion, Linear)

---

## Key Takeaways

### 1. AI Product Sense는 "Doing"에서 나온다
- 책 읽기 < 데모 보기 < **직접 사용하기**
- Consumer UI < **Developer Tool** (투명성)
- 일주일에 한 번 < **매일 사용** (습관)

### 2. 차별화는 Application에 있다
> "Innovation is how we apply them."

- 최고급 LLM은 다 같음
- 경쟁력 = Tool 설계 + Context Engineering + User Experience

### 3. Tool Calling이 핵심
```
Text Generation (기본)
+ Tool Calling (action)
+ Context Management (memory)
= Useful AI Product
```

### 4. Form Factor가 Everything을 바꾼다
**ChatGPT Projects:**
- 대화 중심
- 수동 지식 관리

**Cursor:**
- 파일 중심
- 자동 지식 축적
- Tight feedback loop

### 5. 3개월이면 3년치를 배울 수 있다
> "3 months using Cursor > 3 years using ChatGPT"

단, **매일 실제 업무에 사용**해야 함.

---

## Practical Next Steps

### 오늘 바로 시작:
1. ✅ [Cursor 다운로드](https://cursor.com/download)
2. ✅ Disney 가사 수정 실습 (Step 4)
3. ✅ 여러 모델로 시도해보기

### 이번 주:
1. ⏳ Cursor를 일상 도구로 사용 (3-5회 이상)
2. ⏳ Tool calling 관찰 습관 들이기
3. ⏳ `knowledge/` 폴더 생성 및 첫 문서 작성

### 이번 달:
1. ⏳ Personal OS 기본 구조 완성
2. ⏳ 팀 프로젝트에 학습 내용 적용
3. ⏳ RAG/Memory 실험해보기

### 3개월:
1. ⏳ AI Product Sense 지표 달성
2. ⏳ 팀에 Best Practice 공유
3. ⏳ 실제 AI 기능 설계 및 구현

---

## Reflection

이 글의 가장 큰 가치는 **"Learning by Doing"을 강제하는 구조**에 있습니다.

단순히 개념 설명이 아니라, **Cursor 안에서 이 글을 읽으라**고 합니다. 읽는 동안 직접 실습하고, AI가 어떻게 작동하는지 관찰하게 됩니다.

**르네상스 개발자에게 필수:**
- 기술만 아는 것이 아니라
- **제품을 어떻게 만들지** 직관이 생김
- AI 트렌드를 **예측**할 수 있음
- 팀에 **실용적 가이드** 제공 가능

**사용자에게 추천:**
1. Cursor 다운로드하고 실습 진행
2. beauty-sale에 어떤 AI 기능 넣을지 구체적으로 설계
3. 팀원들과 함께 AI Product Sense 키우기

---

**"AI product sense는 코드가 아니라 경험에서 나온다."**
