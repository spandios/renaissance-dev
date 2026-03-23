# Claude Code 활용 방식: 계획과 실행의 분리

**Source:** [GeekNews](https://news.hada.io/topic?id=26907)  
**Date:** 2026-03-23  
**Category:** AI Tools, Software Development, Workflow

---

## The Big Idea

**AI 코딩 도구의 진짜 가치는 "코드 자동 생성"이 아니라, 계획과 실행을 명확히 분리한 구조화된 워크플로우를 통해 인간의 판단력과 AI의 실행력을 최적으로 결합하는 데 있다.**

---

## Argument Breakdown

### 1. 기존 방식의 문제점

**일반적인 AI 코딩 도구 사용 패턴:**
```
프롬프트 입력 → 코드 생성 → 실행 → 문제 발견 → 수정 → 반복...
```

**문제점:**
- 🔴 잘못된 가정 위에 코드를 쌓음
- 🔴 불필요한 시행착오 반복
- 🔴 아키텍처 결정권 상실
- 🔴 토큰 낭비 (맥락 재설명, 되돌리기)
- 🔴 캐싱 레이어 무시, ORM 규칙 미반영, 중복 로직 생성 등

**근본 원인:**
> AI에게 **생각(Thinking)과 실행(Typing)을 동시에** 맡기면, 잘못된 방향으로 빠르게 진행됨.

### 2. 해결책: 계획과 실행의 분리

**핵심 원칙:**
```
"계획 승인 전에는 Claude에게 코드를 쓰게 하지 않는다"
```

**워크플로우:**
```
Research → Plan → Annotation → Todo List → Implementation → Feedback
   ↓         ↓         ↓           ↓              ↓             ↓
 이해하기   계획하기  다듬기     세분화하기      실행하기      검증하기
```

**각 단계의 역할:**
| Phase | 목적 | 산출물 | 핵심 명령 |
|-------|------|--------|-----------|
| Research | 코드베이스 이해 | `research.md` | "깊이 읽고 분석하라" |
| Plan | 구현 계획 수립 | `plan.md` | "상세 계획을 작성하라" |
| Annotation | 계획 검토 및 수정 | 주석이 달린 `plan.md` | "주석 반영, 구현은 금지" |
| Todo List | 작업 세분화 | Todo 항목 추가된 `plan.md` | "세부 작업 목록 추가" |
| Implementation | 계획 실행 | 코드 | "계획대로 구현, 멈추지 말라" |
| Feedback | 피드백 및 수정 | 수정된 코드 | 짧고 명확한 지시 |

### 3. Phase 1: Research (이해하기)

**목적:**
- Claude가 코드베이스를 제대로 이해했는지 검증
- 잘못된 이해는 잘못된 계획으로 이어지므로 최우선 차단

**실행:**
```markdown
Prompt:
"auth/ 폴더를 깊이 읽고 (detailed, intricacies) 분석하라.
결과를 research.md에 기록하라."
```

**산출물 (research.md):**
```markdown
# Auth Module Research

## Architecture
- JWT 기반 인증
- Refresh token rotation 구현
- Redis 세션 저장

## Key Components
1. AuthService: JWT 발급/검증
2. AuthMiddleware: 요청 인증 체크
3. TokenRefresher: 자동 갱신 로직

## Dependencies
- passport.js
- jsonwebtoken
- redis client

## 주요 발견
- 캐싱 레이어는 CacheService 사용 (직접 구현 금지)
- ORM은 TypeORM (raw query 지양)
- 에러 처리는 CustomException 상속 필수
```

**검증:**
```
❌ 잘못된 이해:
"auth는 세션 기반입니다"
→ 수정: "JWT 기반이야. 다시 읽어"

✅ 올바른 이해:
"JWT + Refresh token rotation 사용"
→ 승인: "맞아, 다음 단계로"
```

**예방하는 실패:**
- 캐싱 레이어 무시 → 직접 Redis 접근 (기존 CacheService 무시)
- ORM 규칙 미반영 → raw query 남발
- 중복 로직 생성 → 이미 있는 유틸리티 함수 재구현

### 4. Phase 2: Planning (계획하기)

**목적:**
- 실제 코드 작성 전, 상세한 구현 계획 수립
- 코드 스니펫, 파일 경로, 트레이드오프 명시

**실행:**
```markdown
Prompt:
"research.md 기반으로 소셜 로그인 기능 구현 계획을 plan.md에 작성하라.
- 수정할 파일 경로
- 코드 스니펫 (실제 작동할 수준)
- 트레이드오프 설명
포함. 아직 구현하지 말 것 (don't implement yet)."
```

**산출물 (plan.md):**
```markdown
# Social Login Implementation Plan

## Files to Modify
1. `src/auth/auth.controller.ts` - 새 엔드포인트 추가
2. `src/auth/auth.service.ts` - OAuth 로직 추가
3. `src/users/users.service.ts` - 소셜 계정 연동
4. `src/config/passport.config.ts` - 전략 추가

## Implementation

### 1. Google OAuth Strategy
```typescript
// src/config/passport.config.ts
import { Strategy as GoogleStrategy } from 'passport-google-oauth20';

passport.use(new GoogleStrategy({
  clientID: process.env.GOOGLE_CLIENT_ID,
  clientSecret: process.env.GOOGLE_CLIENT_SECRET,
  callbackURL: '/auth/google/callback'
}, async (accessToken, refreshToken, profile, done) => {
  // User lookup/create logic
}));
```

### 2. Controller Endpoint
```typescript
// src/auth/auth.controller.ts
@Get('google')
@UseGuards(AuthGuard('google'))
async googleAuth() {}

@Get('google/callback')
@UseGuards(AuthGuard('google'))
async googleAuthCallback(@Req() req) {
  return this.authService.login(req.user);
}
```

### 3. Service Logic
- 기존 사용자: 소셜 계정 연동
- 신규 사용자: 계정 생성 + 로그인
- JWT 발급은 기존 로직 재사용

## Trade-offs
- Passport.js vs 직접 구현: Passport 선택 (보안 + 유지보수)
- Email 중복 처리: 동일 이메일은 하나의 계정으로 통합
- Profile 정보 저장: 최소한만 저장 (GDPR 고려)

## Dependencies
- passport-google-oauth20
- passport-facebook (향후)
```

**품질 향상 팁:**
> 오픈소스의 **참조 구현(reference implementation)**을 함께 제공하면, Claude의 계획 품질이 크게 향상됨.

**예시:**
```
"NestJS의 passport 공식 예제를 참고해서 계획 수정해줘:
https://github.com/nestjs/nest/tree/master/sample/19-auth-jwt"
```

### 5. Annotation Cycle (다듬기) - 가장 중요!

**목적:**
- 계획에 인간의 도메인 지식과 판단 주입
- 잘못된 가정 수정, 불필요한 부분 제거

**실행 (1~6회 반복):**
```markdown
Step 1: plan.md 열기 → 인라인 주석 추가

# Plan (주석 추가됨)
## Files to Modify
1. src/auth/auth.controller.ts
2. src/auth/auth.service.ts
3. src/users/users.service.ts
4. src/config/passport.config.ts

[주석] 4번은 불필요. 우리는 passport.config.ts 대신
strategy/ 폴더에 각 전략을 별도 파일로 분리함.

## Controller Endpoint
@Get('google')  // [주석] POST로 변경. GET은 CSRF 취약
@UseGuards(AuthGuard('google'))
async googleAuth() {}

[주석] callback URL에서 JWT를 쿼리 파라미터로 전달하지 말 것.
HttpOnly 쿠키로 설정해야 XSS 방지 가능.

## Service Logic
- 기존 사용자: 소셜 계정 연동
- 신규 사용자: 계정 생성 + 로그인

[주석] 이메일 인증 우회 금지. 소셜 로그인도 이메일 인증 필수.
```

**Step 2: Claude에게 주석 반영 지시**
```markdown
Prompt:
"plan.md의 주석을 모두 반영해서 문서를 갱신하라.
아직 구현하지 말 것 (don't implement yet)."
```

**Step 3: 갱신된 plan.md 확인 → 추가 주석 → 반복**

**핵심 명령어:**
```
"don't implement yet"  ← 가장 중요한 안전장치!
```

**왜 markdown 문서인가?**
- ✅ **공유 가능한 상태(state)**: 대화형 지시보다 명확
- ✅ **구조적**: 섹션별 수정 가능
- ✅ **버전 관리**: Git으로 변경 추적
- ✅ **재사용**: 다른 기능 구현 시 템플릿으로 활용

**Annotation의 힘:**
```
일반적 계획 → (주석 1~6회) → 실제 시스템에 완벽히 맞는 사양
```

### 6. Todo List 생성 (세분화하기)

**목적:**
- 구현 작업을 세부 태스크로 분해
- 진행 상황 추적

**실행:**
```markdown
Prompt:
"plan.md에 세부 작업 목록(todo list)을 추가하라."
```

**산출물:**
```markdown
# Social Login Implementation Plan

## Todo List
- [ ] Google OAuth 전략 구현
  - [ ] GoogleStrategy 클래스 생성
  - [ ] 환경 변수 설정
  - [ ] Callback 로직 구현
- [ ] Controller 엔드포인트 추가
  - [ ] /auth/google (POST)
  - [ ] /auth/google/callback
- [ ] Service 로직 구현
  - [ ] 기존 사용자 찾기
  - [ ] 신규 사용자 생성
  - [ ] 소셜 계정 연동
- [ ] 테스트 작성
  - [ ] Unit tests
  - [ ] Integration tests
- [ ] 문서화
  - [ ] API 문서 업데이트
  - [ ] README 수정
```

**효과:**
- Claude가 완료 항목을 체크하므로, 장시간 세션에서도 진행 상황 파악 용이
- 중간에 중단해도 어디까지 했는지 명확

### 7. Phase 3: Implementation (실행하기)

**목적:**
- 모든 결정이 확정된 후, 계획을 기계적으로 실행

**실행 (표준화된 프롬프트):**
```markdown
"plan.md의 모든 태스크를 완료할 때까지 멈추지 말 것.

규칙:
- 불필요한 주석 금지
- any/unknown 타입 금지
- 지속적 타입체크 수행
- Todo 완료 시 체크 표시
- 막히면 물어보지 말고 계획 참조"
```

**특징:**
- 🤖 **기계적 실행**: 창의적 판단은 이미 계획 단계에서 완료
- 🚫 **자율권 제한**: Claude가 임의로 판단하지 않음
- ⚡ **연속 실행**: 한 번에 모든 작업 완료

**계획 없이 바로 구현하면?**
```
❌ 잘못된 가정 위에 코드를 쌓음
→ 전체 구조 재작업 필요
→ 토큰 낭비 + 시간 낭비
```

**계획 후 구현하면?**
```
✅ 검증된 계획을 실행
→ 한 번에 완성
→ 효율적 토큰 사용
```

### 8. Feedback During Implementation (검증하기)

**목적:**
- 실행 중 발견된 문제 즉시 수정
- 작성자는 **감독자(Supervisor)** 역할

**피드백 방식:**
```
짧고 명확하게 (Short & Direct)

❌ 장황: "로그인 함수가 누락된 것 같은데, auth.service.ts에 추가해주시겠어요?"
✅ 간결: "login() 함수 누락. 추가"

❌ 장황: "이 컴포넌트를 admin 폴더로 옮기는 게 좋을 것 같아요."
✅ 간결: "admin 앱으로 이동"
```

**프론트엔드 피드백:**
```
단문 지시:
"wider"
"2px gap"
"더 어둡게"

또는 스크린샷:
[이미지 첨부] "이렇게 만들어"
```

**기존 코드 참조:**
```
"UserProfile 컴포넌트처럼 스타일링해줘"
→ 일관된 UI/UX 유지
```

**잘못된 방향으로 진행되면?**
```
git revert → 범위 축소 → 재시도

점진적 수정보다 효과적:
- 잘못된 코드 위에 패치 쌓기 (❌)
- 깨끗한 상태로 돌아가서 다시 시작 (✅)
```

### 9. Staying in the Driver's Seat (운전석 지키기)

**핵심 원칙:**
```
Claude에게 완전한 자율권을 주지 않는다.
최종 결정은 항상 사람이 내린다.
```

**예시:**

**제안 선택:**
```
Claude: "3가지 방법을 제안합니다:
1. Promise.all 사용
2. async/await 체인
3. RxJS Observable"

나: "첫 번째만 적용"
```

**제안 거부:**
```
Claude: "다운로드 기능 추가했습니다."
나: "다운로드 기능 제거. 요청하지 않음."
```

**기술 선택 재정의:**
```
Claude: "GraphQL 사용 제안드립니다."
나: "REST API로 유지. 이유: 팀 숙련도 + 인프라 제약"
```

**API 변경 금지:**
```
Claude: "함수 시그니처 변경했습니다."
나: "함수 시그니처 변경 금지. 기존 코드 호환성 유지."
```

**라이브러리 강제:**
```
Claude: "날짜 처리를 직접 구현했습니다."
나: "date-fns 사용. 직접 구현 금지."
```

**역할 분담:**
```
Claude: 기계적 실행 (코드 타이핑)
사람: 판단과 우선순위 결정
```

### 10. Single Long Sessions (긴 세션 유지)

**전략:**
```
리서치부터 구현까지 하나의 긴 세션에서 연속 수행
```

**이유:**
- ✅ **맥락 축적**: Claude가 지속적으로 컨텍스트 유지
- ✅ **Auto-compaction**: 긴 세션에도 충분한 문맥 관리
- ✅ **plan.md 보존**: 세션 내내 완전한 형태로 유지, 언제든 참조 가능

**전통적 방식:**
```
세션 1: 리서치 → 종료
세션 2: 계획 → 종료 (맥락 손실)
세션 3: 구현 → 종료 (다시 설명)
```

**새로운 방식:**
```
세션 1: 리서치 → 계획 → 주석 → 구현 → 피드백 → 완료
(모든 맥락 유지)
```

---

## Context: Why This Matters Now

### 1. AI 코딩 도구의 성숙기 진입

**현재 상황:**
- ChatGPT, Copilot, Claude Code 등 다양한 도구 존재
- "코드 자동 생성" 기능은 이미 상용화
- 하지만 **효과적 활용법**은 여전히 미정립

**문제:**
```
도구는 강력 → 사용법은 미숙
= 도구 탓 (❌) vs 사용법 탓 (✅)
```

### 2. "프롬프트 엔지니어링"의 한계

**일반적 접근:**
```
"완벽한 프롬프트를 작성하면 완벽한 코드가 나온다"
```

**현실:**
```
❌ 프롬프트가 길수록 오류 가능성 증가
❌ 한 번에 모든 것을 설명하기 어려움
❌ AI가 중간에 방향 틀면 복구 어려움
```

**이 글의 접근:**
```
프롬프트 < 워크플로우
(무엇을 쓰느냐 < 어떻게 진행하느냐)
```

### 3. 토큰 효율성의 중요성

**기존 방식:**
```
시행착오 방식:
- 코드 생성 (1000 tokens)
- 문제 발견 (500 tokens)
- 수정 시도 (1000 tokens)
- 또 문제 (500 tokens)
- 다시 수정 (1000 tokens)
= 4000 tokens

결과: 비효율 + 비용 증가
```

**계획-실행 분리:**
```
- 리서치 (500 tokens)
- 계획 (1000 tokens)
- 주석 + 수정 (500 tokens)
- 구현 (1000 tokens)
= 3000 tokens

결과: 효율 + 비용 절감 + 품질 향상
```

### 4. 아키텍처 결정권의 중요성

**AI에게 모든 것을 맡기면?**
```
❌ AI의 임의 판단:
- "GraphQL이 좋겠어요" (팀은 REST 사용)
- "Redux 추가했어요" (과도한 상태 관리)
- "마이크로서비스 제안드려요" (현재는 모놀리스가 적합)
```

**계획 검토 단계가 있으면?**
```
✅ 인간의 판단:
- "우리 팀은 REST가 익숙해"
- "상태 관리는 Context API로 충분해"
- "지금은 모놀리스가 적합해"
```

### 5. "Don't Implement Yet"의 힘

**왜 중요한가?**
```
AI는 강력 → 빠르게 실행
But, 잘못된 방향이면 → 빠르게 실패

"구현 금지" 명령 = 브레이크
→ 방향 확인 후 출발
```

**실제 효과:**
```
Before (구현 금지 없음):
- 5분 만에 1000줄 코드 생성
- 전체 구조가 잘못됨
- 모두 삭제 후 재시작
= 시간 낭비

After (구현 금지 활용):
- 계획 단계에서 방향 수정
- 10분 계획 + 5분 구현
- 한 번에 완성
= 시간 절약
```

---

## Application: 개발자로서 어떻게 적용할 것인가

### 1. 즉시 실천 가능한 것들

#### A. 워크플로우 템플릿 만들기

**파일 구조:**
```
project/
├── .ai/
│   ├── research.md    # Claude가 분석한 내용
│   ├── plan.md        # 구현 계획
│   └── todo.md        # 작업 목록 (선택)
└── src/
```

**research.md 템플릿:**
```markdown
# Research: [기능명]

## 목표
[무엇을 이해하려는가?]

## 분석 대상
- 폴더: [경로]
- 파일: [주요 파일 목록]

## 분석 결과

### 아키텍처
[전체 구조 설명]

### 주요 컴포넌트
[핵심 요소들]

### Dependencies
[의존성 목록]

### 주요 발견
[중요한 패턴, 규칙, 제약사항]

## 질문 및 불확실성
[명확하지 않은 부분]
```

**plan.md 템플릿:**
```markdown
# Implementation Plan: [기능명]

## 목표
[무엇을 구현하는가?]

## Files to Modify
1. [파일 경로] - [변경 내용]
2. ...

## Implementation

### [Section 1]
```코드 스니펫```

[설명]

### [Section 2]
...

## Trade-offs
- [선택지 A vs B]: [선택 이유]

## Dependencies
- [새로 추가할 패키지]

## Todo List
- [ ] [태스크 1]
  - [ ] [세부 작업 1-1]
  - [ ] [세부 작업 1-2]
- [ ] [태스크 2]
...
```

#### B. 표준화된 프롬프트 작성

**1. Research 프롬프트:**
```
"[대상 폴더/파일]을 깊이 읽고(detailed, intricacies) 분석하라.
다음을 포함하여 research.md에 기록:
- 아키텍처 개요
- 주요 컴포넌트
- 의존성
- 중요한 패턴 및 규칙
- 불확실한 부분

완료 후 알려줘."
```

**2. Planning 프롬프트:**
```
"research.md 기반으로 [기능명] 구현 계획을 plan.md에 작성하라.
포함 사항:
- 수정할 파일 경로
- 실제 작동할 수준의 코드 스니펫
- 트레이드오프 설명
- 세부 todo list

아직 구현하지 말 것 (don't implement yet)."
```

**3. Annotation 프롬프트:**
```
"plan.md의 주석을 모두 반영해서 문서를 갱신하라.
아직 구현하지 말 것 (don't implement yet)."
```

**4. Implementation 프롬프트:**
```
"plan.md의 모든 태스크를 완료할 때까지 멈추지 말 것.

규칙:
- 불필요한 주석 금지
- any/unknown 타입 금지
- 지속적 타입체크 수행
- Todo 완료 시 체크 표시
- 막히면 계획 참조, 임의 판단 금지

완료 후 요약 보고."
```

#### C. 체크리스트 만들기

```markdown
## Claude Code 워크플로우 체크리스트

### Phase 1: Research
- [ ] 대상 폴더/파일 명확히 지정
- [ ] "깊이 읽고 분석" 명령
- [ ] research.md 생성 확인
- [ ] 분석 내용 검토 (오해 여부)
- [ ] 필요시 재분석 요청

### Phase 2: Planning
- [ ] research.md 기반 계획 요청
- [ ] "don't implement yet" 명령 포함
- [ ] plan.md 생성 확인
- [ ] 참조 구현 제공 (선택)

### Phase 3: Annotation (1~6회 반복)
- [ ] plan.md 열기
- [ ] 인라인 주석 추가
  - [ ] 잘못된 가정 수정
  - [ ] 불필요한 부분 제거
  - [ ] 도메인 지식 추가
- [ ] "주석 반영, 구현 금지" 명령
- [ ] 갱신된 문서 확인
- [ ] 만족할 때까지 반복

### Phase 4: Todo List
- [ ] "todo list 추가" 명령
- [ ] 작업 세분화 확인

### Phase 5: Implementation
- [ ] 표준 구현 프롬프트 실행
- [ ] 진행 상황 모니터링
- [ ] Todo 체크 확인

### Phase 6: Feedback
- [ ] 실행 결과 검토
- [ ] 짧고 명확한 피드백
- [ ] 필요시 git revert → 재시도

### Phase 7: Completion
- [ ] 모든 todo 완료 확인
- [ ] 테스트 실행
- [ ] 코드 리뷰
- [ ] Git commit
```

### 2. 중기 목표 (1-3개월)

#### A. 팀 워크플로우 정립

**1주차: 개인 실험**
```
- 작은 기능으로 시작 (버그 수정, 단순 CRUD)
- 워크플로우 경험
- 병목 지점 파악
```

**2주차: 프로세스 문서화**
```
- 효과적이었던 패턴 기록
- 피해야 할 함정 정리
- 팀 가이드라인 초안 작성
```

**3-4주차: 팀 공유 및 조정**
```
- 팀 미팅에서 공유
- 피드백 수렴
- 팀 표준 확립
```

**2-3개월: 정착 및 개선**
```
- 모든 팀원이 워크플로우 사용
- 주기적 회고
- 지속적 개선
```

#### B. Research 라이브러리 구축

**목적:**
- 반복 분석 작업 제거
- 팀 지식 축적

**구조:**
```
.ai/
├── research/
│   ├── auth-module.md          # 인증 모듈 분석
│   ├── payment-integration.md  # 결제 연동 분석
│   ├── caching-layer.md        # 캐싱 레이어 분석
│   └── ...
├── plans/
│   ├── social-login.md         # 소셜 로그인 계획
│   ├── subscription.md         # 구독 기능 계획
│   └── ...
└── templates/
    ├── feature-research.md
    ├── feature-plan.md
    └── bug-fix-plan.md
```

**활용:**
```
새 기능 개발 시:
1. research/ 에서 관련 분석 찾기
2. 기존 분석 기반으로 계획 수립
3. 반복 작업 제거, 시간 절약
```

#### C. 참조 구현(Reference Implementation) 수집

**왜 중요한가?**
> 참조 구현을 제공하면 Claude의 계획 품질이 **크게 향상**됨.

**수집 대상:**
```
1. 공식 문서 예제
   - NestJS 공식 예제
   - React 공식 패턴
   - TypeORM Best Practices

2. 우수 오픈소스
   - Awesome Lists
   - GitHub 트렌딩
   - Production-ready 프로젝트

3. 팀 내부 구현
   - 잘 작성된 기존 코드
   - 모범 사례
   - 팀 컨벤션
```

**활용 방법:**
```
"이 기능 구현 계획을 작성할 때,
다음 참조 구현을 참고해:
[GitHub URL 또는 파일 경로]"
```

### 3. 장기 목표 (3-6개월)

#### A. beauty-sale 프로젝트 적용

**시나리오 1: 새 기능 개발 (예: 찜하기)**

**Step 1: Research**
```
"src/products/ 폴더를 깊이 읽고 분석하라.
특히:
- 제품 데이터 모델
- API 엔드포인트 구조
- 상태 관리 방식
- 캐싱 전략

research.md에 기록."
```

**Step 2: Planning**
```
"research.md 기반으로 '찜하기' 기능 계획을 plan.md에 작성하라.

요구사항:
- 사용자별 찜 목록 관리
- 실시간 동기화 (Redis 활용)
- 찜 개수 카운팅
- API 엔드포인트: /wishlist

아직 구현하지 말 것."
```

**Step 3: Annotation**
```
[주석 1]
"Redis 대신 PostgreSQL로. 이유: 단순 CRUD + 트랜잭션 필요"

[주석 2]
"찜 개수는 product 테이블에 denormalized 컬럼 추가.
매번 카운트 쿼리는 성능 이슈."

[주석 3]
"API는 /api/v1/wishlist 형식. 버전 관리 필수."

→ "주석 반영, 구현 금지"
```

**Step 4: Implementation**
```
"plan.md 모든 태스크 완료.
규칙:
- TypeORM 사용
- DTO 타입 명확히
- 불필요한 주석 금지"
```

**Step 5: Feedback**
```
"찜 해제 API 추가"
"로딩 스피너 추가"
"에러 메시지 사용자 친화적으로"
```

**시나리오 2: 버그 수정**

**간단한 버그:**
```
Research 스킵 → Plan만 → 구현
(이미 코드베이스 이해 완료된 경우)
```

**복잡한 버그:**
```
Research (버그 원인 분석) → Plan (수정 계획) → 구현
```

**시나리오 3: 리팩토링**

**Step 1: Research**
```
"src/payment/ 모듈 전체를 분석하라.
현재 구조의 문제점과 개선 가능 영역 파악.
research.md에 기록."
```

**Step 2: Planning**
```
"research.md 기반으로 리팩토링 계획 작성.

목표:
- 결제 로직 Service로 분리
- DTO 타입 강화
- 에러 처리 개선

단계별로 진행 (한 번에 모두 변경 금지).
아직 구현하지 말 것."
```

**Step 3: Annotation**
```
[주석]
"1단계만 먼저 진행. 2-3단계는 다음 PR로 분리."

→ "주석 반영, 구현 금지"
```

**Step 4: Implementation**
```
"plan.md의 1단계만 구현.
기존 기능 동작 보장 (테스트 통과 필수)."
```

#### B. 코드 리뷰 프로세스 개선

**기존 프로세스:**
```
PR 생성 → 팀원 리뷰 → 수정 → 머지
```

**개선된 프로세스:**
```
PR 생성 전:
1. plan.md 첨부
2. research.md 첨부 (복잡한 변경 시)
3. 주요 결정 사항 명시

PR 리뷰 시:
1. 계획 검토 (plan.md)
2. 계획 대비 구현 일치 확인
3. 코드 리뷰

결과:
- 리뷰어가 맥락 빠르게 파악
- "왜 이렇게 했어요?" 질문 감소
- 리뷰 시간 단축
```

#### C. AI 활용 역량 측정

**지표:**
```
1. 첫 시도 성공률
   Before: 30% (여러 번 수정)
   After: 80% (계획 단계에서 수정)

2. 평균 토큰 사용량
   Before: 기능당 5000 tokens
   After: 기능당 3000 tokens

3. 개발 시간
   Before: 신규 기능 3일
   After: 신규 기능 1.5일

4. 코드 품질
   Before: 리뷰 코멘트 평균 10개
   After: 리뷰 코멘트 평균 3개
```

---

## Key Takeaways

### 1. 계획과 실행의 분리가 핵심

```
❌ 잘못된 접근:
"AI야, 소셜 로그인 만들어줘"
→ 불확실한 결과

✅ 올바른 접근:
1. 코드베이스 이해 (Research)
2. 계획 수립 (Plan)
3. 계획 검토 및 수정 (Annotation)
4. 실행 (Implementation)
→ 예측 가능한 결과
```

### 2. "Don't Implement Yet"이 가장 중요한 명령

```
이 명령 = 브레이크

없으면:
- AI가 빠르게 질주
- 잘못된 방향이면 전체 재작업

있으면:
- 방향 확인 후 출발
- 한 번에 올바른 결과
```

### 3. Markdown 문서 = 공유 가능한 상태

```
대화형 지시:
"이거 저거 수정해줘"
→ 추적 어려움, 재사용 불가

Markdown 문서:
research.md, plan.md
→ 추적 가능, 재사용 가능, 팀 공유 가능
```

### 4. Annotation Cycle이 품질을 결정

```
일반적 계획 (초안)
↓ (주석 1회)
개선된 계획
↓ (주석 2회)
더 나은 계획
↓ (주석 3-6회)
완벽한 사양
```

**반복할수록 품질 향상.**

### 5. 역할 분담 명확화

```
AI의 역할: 기계적 실행 (코드 타이핑)
인간의 역할: 판단과 우선순위 결정

AI에게 자율권을 주지 않음.
인간이 운전석을 지킴.
```

### 6. 긴 세션 유지

```
짧은 세션 여러 번:
- 맥락 손실
- 재설명 필요
- 비효율

긴 세션 한 번:
- 맥락 유지
- 연속적 작업
- 효율
```

### 7. 참조 구현 제공하면 품질 급상승

```
참조 없음:
"소셜 로그인 구현해줘"
→ AI가 임의로 판단

참조 제공:
"이 예제를 참고해서 구현해줘: [URL]"
→ 검증된 패턴 적용
```

---

## Practical Next Steps

### 오늘 바로 시작:
1. ✅ `.ai/` 폴더 생성
2. ✅ `research.md`, `plan.md` 템플릿 작성
3. ✅ 간단한 버그 수정으로 워크플로우 실험

### 이번 주:
1. ⏳ 표준 프롬프트 작성 및 문서화
2. ⏳ 체크리스트 만들기
3. ⏳ 첫 실전 적용 (작은 기능)

### 이번 달:
1. ⏳ beauty-sale에 워크플로우 적용 (찜하기 기능)
2. ⏳ 팀 가이드라인 초안 작성
3. ⏳ Research 라이브러리 구축 시작

### 3개월:
1. ⏳ 팀 전체 워크플로우 정착
2. ⏳ 참조 구현 라이브러리 완성
3. ⏳ 효과 측정 및 개선

---

## Reflection

이 글의 가장 큰 가치는 **"AI는 도구가 아니라 협업자"**라는 인식 전환입니다.

**전통적 관점:**
```
AI = 자동화 도구
→ 명령 입력 → 결과 출력
```

**새로운 관점:**
```
AI = 협업 파트너
→ Research → Plan → Review → Execute
```

**핵심 통찰:**
> "깊이 읽고, 계획을 쓰고, 주석으로 다듬은 뒤, 한 번에 실행하라."

이 워크플로우는:
- ✅ 복잡한 프롬프트 불필요
- ✅ 시스템 지시 불필요
- ✅ 규율적 파이프라인만으로 고품질 확보

**르네상스 개발자에게 필수:**
- 기술만 아는 것이 아니라
- **AI와 협업하는 방법**을 아는 것
- 도구가 아니라 **워크플로우**가 경쟁력

**사용자에게 추천:**
1. beauty-sale 다음 기능 개발 시 이 워크플로우 적용
2. `.ai/` 폴더 구조 구축
3. 팀원들과 함께 프로세스 정립
4. 토큰 사용량 + 개발 시간 측정 → 효과 검증

---

**"계획은 인간이, 실행은 AI가. 운전석은 절대 내주지 않는다."**
