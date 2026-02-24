# 🍽️ Context Engineering for Coding Agents

**출처:** Martin Fowler (Thoughtworks)  
**원문:** https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html  
**날짜:** 2026-02-05  

---

## 🎯 The Big Idea

> "Context engineering is curating what the model sees so that you get a better result."

AI 코딩 에이전트의 성능은 모델 자체보다 **모델에게 무엇을 보여주느냐**에 의해 결정된다. Context Engineering은 프롬프트 엔지니어링의 진화형으로, 에이전트가 올바른 맥락을 올바른 시점에 로드하도록 설계하는 체계적 접근법이다.

---

## 📖 Argument Breakdown

### 1. 맥락의 두 축: 재사용 가능한 프롬프트 vs 맥락 인터페이스

Fowler는 코딩 에이전트의 맥락을 두 카테고리로 나눈다:

**재사용 가능한 프롬프트:**
- **Instructions**: 특정 작업 수행 지시 ("E2E 테스트를 이런 방식으로 작성하라")
- **Guidance**: 일반 컨벤션/가드레일 ("테스트는 서로 독립적이어야 한다")

**맥락 인터페이스 (Context Interfaces):**
- **Tools**: bash 명령, 파일 검색 등 내장 기능
- **MCP Servers**: 외부 데이터/API 접근 프로그램
- **Skills**: 필요할 때만 로드되는 추가 리소스/문서/스크립트 묶음

### 2. 누가 맥락을 로드할 것인가? (Who decides)

세 가지 트리거 방식의 트레이드오프:

| 트리거 | 장점 | 단점 |
|--------|------|------|
| **LLM** | 자율 운영 가능 | 비결정적 (로드 안 할 수도) |
| **Human** | 정확한 제어 | 자동화 수준 저하 |
| **Agent Software** | 결정적, 안정적 | 유연성 부족 |

핵심 통찰: 완전 자동화를 원하면 LLM에 위임하되, 중요한 것은 Hooks처럼 결정적 트리거로 보장해야 한다.

### 3. 맥락 크기의 균형 (How much)

컨텍스트 윈도우가 커졌다고 다 넣으면 안 된다:
- 너무 많은 맥락 → 에이전트 효과성 저하 + 비용 증가
- **점진적 구축**이 핵심: 처음부터 다 넣지 말고, 필요에 따라 쌓아라
- 도구 자체도 최적화 중 (대화 히스토리 압축, 툴 표현 최적화 등)

### 4. Claude Code의 맥락 기능 생태계

Fowler는 Claude Code를 예시로 7가지 맥락 설정 기능을 정리:

1. **CLAUDE.md** → 항상 로드되는 프로젝트 규칙
2. **Rules** → 파일 경로별 스코프 가이드라인
3. **Slash Commands** → 인간이 트리거하는 명령 (Skills로 대체 중)
4. **Skills** → LLM이 필요시 로드하는 리소스 묶음
5. **Subagents** → 별도 컨텍스트에서 병렬 실행
6. **MCP Servers** → 외부 API/도구 접근
7. **Hooks** → 라이프사이클 이벤트 기반 스크립트

### 5. 공유의 어려움과 제어의 환상

- 맥락 설정은 **팀 내부 공유는 좋지만, 인터넷 복붙은 위험**
- 남의 설정을 그대로 쓰면 중복/모순 발생
- "ensure it does X" 같은 표현은 환상 → **확률적 사고**가 필요
- LLM이 개입하는 한, 100% 보장은 불가능

---

## 🌍 Context: 왜 지금 중요한가?

2026년 초, AI 코딩 도구 시장이 폭발적으로 성장 중:
- Claude Code, Cursor, GitHub Copilot, Windsurf 등이 경쟁
- "프롬프트 엔지니어링"에서 "컨텍스트 엔지니어링"으로 담론 이동
- 단순히 "좋은 질문"을 넘어, **에이전트가 살아가는 환경을 설계**하는 시대

Fowler 자신도 인정: 현재는 "storming" 단계이며, 곧 기능이 수렴할 것. Skills가 Rules와 Slash Commands를 흡수할 가능성 높음.

이는 개발자의 역할 변화를 의미한다:
- 코드를 직접 쓰는 시간 ↓
- 에이전트를 효과적으로 운용하는 시간 ↑
- **좋은 맥락을 설계하는 능력**이 새로운 핵심 역량

---

## 🛠️ Application: 개발자로서 어떻게 적용할 것인가?

### 즉시 적용 가능한 행동

1. **CLAUDE.md / AGENTS.md 점진적 구축**
   - 한 번에 완성하려 하지 말 것
   - 작업하면서 "이걸 왜 또 말해야 하지?" 싶을 때마다 추가
   - 주기적으로 리뷰하며 불필요한 것 제거

2. **맥락 인터페이스 전략적 선택**
   - MCP Server가 필요한가, 아니면 CLI + Skills 조합으로 충분한가?
   - 항상 로드 vs 필요시 로드를 구분하여 설계

3. **AI-friendly 코드베이스 만들기**
   - 코드 자체가 가장 기본적인 맥락
   - 명확한 네이밍, 잘 구조화된 디렉토리, 적절한 주석
   - 에이전트가 코드를 읽고 이해하기 쉬운 구조

4. **확률적 사고 습관화**
   - "에이전트가 반드시 이렇게 할 것이다" → ❌
   - "이 맥락을 주면 높은 확률로 이렇게 할 것이다" → ✅
   - 중요한 작업에는 인간 검증 단계 유지

### 팀 레벨

- 팀 공통 Rules/Skills를 Git으로 관리
- 개인 설정과 팀 설정을 분리 (CLAUDE.md는 프로젝트 루트, 개인은 홈)
- Context engineering도 코드 리뷰처럼 리뷰하는 문화 만들기

---

## 💡 한 줄 요약

> AI 시대의 개발자 역량은 "코드를 잘 짜는 것"에서 "에이전트가 잘 작동하는 환경을 설계하는 것"으로 확장되고 있다.

---

*Tags: #context-engineering #ai-coding #claude-code #developer-productivity #martin-fowler*
