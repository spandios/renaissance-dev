# Week 2: GA4 실무 - 개발자를 위한 완벽 가이드

**목표**: 프로젝트에 GA4를 제대로 심고, 분석하고, 운영팀과 협업하기

---

## 📋 목차

1. [GA4 이벤트 설계 전략](#1-ga4-이벤트-설계-전략)
2. [이벤트 네이밍 컨벤션](#2-이벤트-네이밍-컨벤션)
3. [GTM vs 직접 코드](#3-gtm-vs-직접-코드)
4. [React/Next.js 구현](#4-reactnextjs-구현)
5. [GA4 보고서 읽는 법](#5-ga4-보고서-읽는-법)
6. [퍼널 분석 실전](#6-퍼널-분석-실전)
7. [운영팀 협업 가이드](#7-운영팀-협업-가이드)

---

## 1. GA4 이벤트 설계 전략

### 1.1 무엇을 추적할까? (AARRR 기반)

**핵심 원칙**: 모든 것을 추적하지 말고, **의사결정에 필요한 것만** 추적하세요.

```
AARRR 단계별 핵심 이벤트:

📱 Acquisition (획득)
├─ landing_page_view (어디서 왔는지)
└─ campaign_click (어떤 캠페인 클릭했는지)

✅ Activation (활성화)
├─ sign_up_start (가입 시작)
├─ sign_up_complete (가입 완료)
└─ first_action (첫 핵심 행동)

💰 Revenue (수익)
├─ add_to_cart (장바구니 담기)
├─ begin_checkout (결제 시작)
└─ purchase (구매 완료)

🔁 Retention (재방문)
├─ login (재로그인)
└─ return_visit (재방문)

📢 Referral (추천)
├─ share_click (공유 클릭)
└─ referral_complete (추천 완료)
```

### 1.2 이벤트 우선순위

**Phase 1: 필수 (지금 당장)**
- `page_view` (자동 수집)
- `sign_up_complete`
- `purchase` (또는 핵심 전환 이벤트)
- `login`

**Phase 2: 중요 (1-2주 내)**
- `add_to_cart`
- `begin_checkout`
- `search`
- `first_action`

**Phase 3: 최적화 (한 달 내)**
- 버튼 클릭 세분화
- 스크롤 깊이
- 페이지 체류 시간 커스텀
- 에러/이탈 포인트

---

## 2. 이벤트 네이밍 컨번션

### 2.1 Google 권장 형식

```
동사_명사 (snake_case)
```

**좋은 예:**
- `add_to_cart`
- `begin_checkout`
- `view_item`
- `search`

**나쁜 예:**
- `AddToCart` (camelCase ❌)
- `button_click` (너무 일반적 ❌)
- `클릭` (한글 ❌)

### 2.2 커스텀 이벤트 네이밍 전략

```javascript
// 패턴: [영역]_[행동]_[대상]

// ✅ Good
'product_add_cart'
'product_remove_cart'
'checkout_start'
'checkout_complete'
'signup_email_submit'
'signup_social_click'

// ❌ Bad
'click' // 너무 일반적
'event1' // 의미 없음
'button_1234' // 코드 냄새
```

### 2.3 파라미터 네이밍

```javascript
// Google 권장 파라미터 (GA4가 자동 인식)
{
  item_id: 'SKU_12345',
  item_name: '무선 키보드',
  price: 59000,
  currency: 'KRW',
  quantity: 1,
  category: '전자제품'
}

// 커스텀 파라미터 (직접 정의)
{
  user_tier: 'premium',      // 유저 등급
  page_section: 'hero',      // 페이지 섹션
  ab_test_variant: 'B',      // A/B 테스트 그룹
  error_type: '404'          // 에러 타입
}
```

---

## 3. GTM vs 직접 코드

### 3.1 언제 GTM을 쓸까?

**GTM 사용 ✅**
- 마케팅 팀이 직접 태그 관리하고 싶을 때
- A/B 테스트 태그를 자주 변경할 때
- 여러 마케팅 툴 통합 (Facebook Pixel, Google Ads)
- 배포 없이 이벤트 수정하고 싶을 때

**직접 코드 ✅ (추천)**
- 개발자가 코드 제어 선호
- 정확한 타이밍 제어 필요 (비동기 처리)
- TypeScript 타입 안정성 원할 때
- 단순한 이벤트 추적 (복잡한 태그 관리 불필요)

### 3.2 프로젝트에 어떤 게 맞을까?

```
현재 상황:
- 작은 팀
- 개발자가 GA 담당
- 마케팅 팀 별도 없음
- 정확한 데이터 수집 중요

→ 결론: 직접 코드 추천! 🎯
```

**이유:**
1. GTM 러닝 커브 불필요
2. 코드 리뷰 가능 (git history)
3. TypeScript 타입 체크
4. 디버깅 쉬움

---

## 4. React/Next.js 구현

### 4.1 설치

```bash
npm install react-ga4
# or
yarn add react-ga4
```

### 4.2 초기화 (Next.js App Router 예시)

```typescript
// app/providers/AnalyticsProvider.tsx
'use client';

import { useEffect } from 'react';
import ReactGA from 'react-ga4';
import { usePathname, useSearchParams } from 'next/navigation';

const GA_MEASUREMENT_ID = process.env.NEXT_PUBLIC_GA_MEASUREMENT_ID!;

export function AnalyticsProvider({ children }: { children: React.ReactNode }) {
  const pathname = usePathname();
  const searchParams = useSearchParams();

  useEffect(() => {
    // GA4 초기화 (프로덕션에서만)
    if (process.env.NODE_ENV === 'production') {
      ReactGA.initialize(GA_MEASUREMENT_ID, {
        gaOptions: {
          debug_mode: false,
        },
      });
    }
  }, []);

  useEffect(() => {
    // 페이지뷰 자동 추적
    if (process.env.NODE_ENV === 'production') {
      const url = pathname + (searchParams?.toString() ? `?${searchParams}` : '');
      ReactGA.send({ hitType: 'pageview', page: url });
    }
  }, [pathname, searchParams]);

  return <>{children}</>;
}
```

```typescript
// app/layout.tsx
import { AnalyticsProvider } from './providers/AnalyticsProvider';

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="ko">
      <body>
        <AnalyticsProvider>{children}</AnalyticsProvider>
      </body>
    </html>
  );
}
```

### 4.3 이벤트 추적 유틸리티

```typescript
// lib/analytics.ts
import ReactGA from 'react-ga4';

type EventParams = {
  category?: string;
  label?: string;
  value?: number;
  [key: string]: any;
};

export const analytics = {
  // 일반 이벤트
  event(action: string, params?: EventParams) {
    if (process.env.NODE_ENV === 'production') {
      ReactGA.event(action, params);
    } else {
      console.log('[Analytics]', action, params);
    }
  },

  // 전자상거래: 제품 조회
  viewItem(item: { id: string; name: string; price: number; category?: string }) {
    this.event('view_item', {
      currency: 'KRW',
      items: [
        {
          item_id: item.id,
          item_name: item.name,
          price: item.price,
          item_category: item.category,
        },
      ],
    });
  },

  // 전자상거래: 장바구니 추가
  addToCart(item: { id: string; name: string; price: number; quantity: number }) {
    this.event('add_to_cart', {
      currency: 'KRW',
      value: item.price * item.quantity,
      items: [
        {
          item_id: item.id,
          item_name: item.name,
          price: item.price,
          quantity: item.quantity,
        },
      ],
    });
  },

  // 전자상거래: 구매
  purchase(orderId: string, value: number, items: any[]) {
    this.event('purchase', {
      transaction_id: orderId,
      currency: 'KRW',
      value: value,
      items: items,
    });
  },

  // 회원가입
  signUp(method: 'email' | 'google' | 'kakao') {
    this.event('sign_up', {
      method: method,
    });
  },

  // 로그인
  login(method: 'email' | 'google' | 'kakao') {
    this.event('login', {
      method: method,
    });
  },

  // 검색
  search(searchTerm: string, resultCount?: number) {
    this.event('search', {
      search_term: searchTerm,
      result_count: resultCount,
    });
  },

  // 공유
  share(method: string, contentType: string, itemId?: string) {
    this.event('share', {
      method: method,
      content_type: contentType,
      item_id: itemId,
    });
  },
};
```

### 4.4 실전 사용 예시

```typescript
// app/products/[id]/page.tsx
'use client';

import { useEffect } from 'react';
import { analytics } from '@/lib/analytics';

export default function ProductPage({ params }: { params: { id: string } }) {
  const product = {
    id: params.id,
    name: '무선 키보드',
    price: 59000,
    category: '전자제품',
  };

  // 페이지 진입 시 제품 조회 이벤트
  useEffect(() => {
    analytics.viewItem(product);
  }, []);

  const handleAddToCart = () => {
    analytics.addToCart({
      ...product,
      quantity: 1,
    });

    // 실제 장바구니 추가 로직...
  };

  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.price}원</p>
      <button onClick={handleAddToCart}>장바구니 담기</button>
    </div>
  );
}
```

```typescript
// 회원가입 페이지
const handleSignUp = async (email: string, password: string) => {
  try {
    await signUpAPI(email, password);
    analytics.signUp('email'); // 이벤트 전송
    router.push('/welcome');
  } catch (error) {
    console.error(error);
  }
};
```

### 4.5 Spring Boot 백엔드에서 이벤트 전송

중요한 비즈니스 이벤트는 백엔드에서 전송하는 게 더 정확합니다.

```kotlin
// GA4 백엔드 전송 (Measurement Protocol)
@Service
class AnalyticsService {
    private val measurementId = "G-XXXXXXXXXX"
    private val apiSecret = "your_api_secret"
    private val restTemplate = RestTemplate()

    fun sendEvent(
        clientId: String,
        eventName: String,
        params: Map<String, Any>
    ) {
        val url = "https://www.google-analytics.com/mp/collect?measurement_id=$measurementId&api_secret=$apiSecret"
        
        val payload = mapOf(
            "client_id" to clientId,
            "events" to listOf(
                mapOf(
                    "name" to eventName,
                    "params" to params
                )
            )
        )

        try {
            restTemplate.postForEntity(url, payload, String::class.java)
        } catch (e: Exception) {
            logger.error("GA4 전송 실패", e)
        }
    }
}

// 사용 예시
@PostMapping("/api/orders")
fun createOrder(@RequestBody order: OrderRequest): Order {
    val createdOrder = orderService.create(order)
    
    // GA4 구매 이벤트 전송
    analyticsService.sendEvent(
        clientId = order.userId,
        eventName = "purchase",
        params = mapOf(
            "transaction_id" to createdOrder.id,
            "value" to createdOrder.totalAmount,
            "currency" to "KRW",
            "items" to createdOrder.items
        )
    )
    
    return createdOrder
}
```

---

## 5. GA4 보고서 읽는 법

### 5.1 개발자가 매일 봐야 할 보고서

**1. 실시간 보고서** (방금 심은 이벤트 확인)
- 경로: `보고서 > 실시간`
- 용도: 이벤트가 제대로 전송되는지 즉시 확인
- 체크: 이벤트 이름, 파라미터, 수량

**2. 이벤트 보고서** (이벤트 통계)
- 경로: `보고서 > 참여도 > 이벤트`
- 용도: 각 이벤트가 얼마나 발생했는지
- 체크: 이벤트 수, 사용자 수, 세션당 평균

**3. 전환 보고서** (핵심 지표)
- 경로: `보고서 > 참여도 > 전환`
- 용도: 비즈니스 핵심 이벤트 추적
- 체크: 구매, 가입, 핵심 액션

### 5.2 주간 리뷰 보고서

**월요일 체크리스트:**
1. 지난주 전환율 (구매/가입)
2. 트래픽 소스 (어디서 왔는지)
3. 이탈률 높은 페이지
4. 가장 많이 본 제품/콘텐츠

### 5.3 핵심 지표 해석

```
이벤트 수 vs 사용자 수 vs 세션 수

예시:
- 이벤트 수: 1,000회
- 사용자 수: 200명
- 세션 수: 300회

→ 해석: 평균적으로 한 사용자가 5번 클릭 (1000/200)
→ 세션당 3.3번 클릭 (1000/300)
```

**좋은 지표:**
- 세션당 이벤트: 높을수록 참여도 높음
- 전환율: (전환 수 / 세션 수) × 100
- 재방문율: 로그인 사용자 / 전체 사용자

---

## 6. 퍼널 분석 실전

### 6.1 퍼널이란?

사용자가 목표까지 가는 단계별 여정입니다.

```
회원가입 퍼널 예시:

1. 랜딩 페이지 도착      → 1000명 (100%)
2. 회원가입 버튼 클릭     → 500명 (50%) ← 50% 이탈
3. 이메일 입력 완료       → 400명 (40%)
4. 비밀번호 입력 완료     → 350명 (35%)
5. 회원가입 완료          → 300명 (30%)

→ 최종 전환율: 30%
→ 최대 이탈 구간: 1→2 (50% 이탈)
```

### 6.2 GA4에서 퍼널 만들기

**경로**: `탐색 > 유입경로 탐색`

**설정 예시 (구매 퍼널):**
```
Step 1: page_view (경로: /products)
Step 2: view_item
Step 3: add_to_cart
Step 4: begin_checkout
Step 5: purchase
```

**분석 포인트:**
1. 어느 단계에서 가장 많이 이탈하는가?
2. 이탈률이 50% 이상이면 문제 있음
3. 해당 페이지 UX 개선 필요

### 6.3 실전 퍼널 분석 예시

**상황**: 구매 전환율이 낮음 (2%)

```
분석 결과:
1. 제품 페이지 방문: 10,000명
2. 장바구니 담기: 3,000명 (30%)  ← OK
3. 결제 시작: 1,500명 (15%)      ← 여기서 50% 이탈!
4. 결제 완료: 200명 (2%)         ← 최종 전환율 낮음

진단:
- 장바구니 → 결제 시작: 50% 이탈 (문제 구간)

가설:
- 배송비가 너무 높아서?
- 회원가입 강제해서?
- 결제 수단 부족?

액션:
- 비회원 결제 추가
- 배송비 정책 개선
- A/B 테스트 실행
```

---

## 7. 운영팀 협업 가이드

### 7.1 운영팀이 자주 묻는 질문

**Q1: "이 이벤트 심을 수 있어요?"**

✅ **개발자 체크리스트:**
1. 이 이벤트가 AARRR 중 어디에 속하는지?
2. 의사결정에 실제로 쓰일지?
3. 기존 이벤트로 커버 가능한지?

**답변 템플릿:**
> "네, 가능합니다! 다만 몇 가지 확인하고 싶어요:
> 1. 이 데이터로 어떤 의사결정을 하실 계획인가요?
> 2. 얼마나 자주 보실 예정인가요?
> 3. 기존 `XXX` 이벤트와 차이점이 뭔가요?"

**Q2: "왜 숫자가 안 맞아요?"**

흔한 원인:
- 실시간 vs 최종 데이터 (24시간 차이)
- 필터 설정 차이
- 세션 vs 사용자 vs 이벤트 혼동
- 시간대 설정 (KST vs UTC)

**해결법:**
1. 같은 기간, 같은 필터 사용
2. "이벤트 수"가 아닌 "사용자 수" 기준 비교
3. DebugView로 실제 전송 확인

### 7.2 요구사항 정의 프로세스

**Step 1: 목표 명확화**
```
운영팀: "버튼 클릭 추적해주세요."

개발자: (아래 질문 먼저!)
- 어떤 버튼인가요? (위치, 기능)
- 왜 추적하고 싶으신가요? (목표)
- 어떤 의사결정에 쓰실 건가요?
- 얼마나 자주 보실 건가요?
```

**Step 2: 우선순위 정하기**

```
Priority Matrix:

긴급 & 중요      → 이번 주 배포
긴급 & 덜중요    → 다음 주 배포
중요 & 덜긴급    → 2주 내 배포
덜중요 & 덜긴급  → 백로그
```

**예시:**
- 🔴 구매 완료 이벤트 누락 → 긴급 & 중요
- 🟡 신규 배너 클릭 추적 → 긴급 & 덜중요
- 🟢 소셜 공유 버튼 → 중요 & 덜긴급
- ⚪ 푸터 링크 클릭 → 낮은 우선순위

### 7.3 커뮤니케이션 템플릿

**이벤트 추가 요청서 (운영팀 작성용)**

```markdown
## GA4 이벤트 추가 요청

**요청자**: [이름]
**요청일**: 2026-02-05
**배포 희망일**: 2026-02-12

### 1. 추적하려는 액션
- 버튼명: "프리미엄 가입하기"
- 위치: 메인 페이지 > 상단 배너
- 트리거: 클릭 시

### 2. 목적
- A/B 테스트로 배너 효과 측정
- 프리미엄 가입 전환율 향상

### 3. 의사결정
- 2주 후 배너 디자인 개선 여부 결정
- 클릭률 3% 이상이면 상시 노출

### 4. 이벤트명 (제안)
`premium_banner_click`

### 5. 추가 파라미터
- banner_position: 'top'
- banner_variant: 'A' 또는 'B'
```

**개발자 회신 템플릿:**

```markdown
## GA4 이벤트 구현 계획

**검토 완료**: 2026-02-05
**예상 공수**: 2시간
**배포 예정**: 2026-02-07 (금)

### 구현 내용
- 이벤트명: `premium_banner_click`
- 파라미터:
  - banner_position: 'top'
  - banner_variant: 'A' | 'B'
  - page_path: 현재 페이지 경로

### 테스트 방법
1. 개발 환경에서 클릭
2. GA4 DebugView 확인
3. 실시간 보고서 확인

### 확인 요청
배포 후 다음 링크에서 확인 부탁드립니다:
- [GA4 실시간 보고서](링크)
- 클릭 후 30초 내 이벤트 표시됨
```

### 7.4 주간 리뷰 미팅 (30분)

**매주 월요일 오전 추천**

**아젠다:**
1. 지난주 핵심 지표 리뷰 (10분)
   - 전환율, 트래픽, 이탈률
2. 이슈 사항 (5분)
   - 숫자 이상, 이벤트 누락
3. 이번주 이벤트 추가 계획 (10분)
   - 우선순위, 공수, 일정
4. 다음 액션 아이템 (5분)

**회의록 템플릿:**
```markdown
# GA4 주간 리뷰 (2026-02-03)

## 핵심 지표
- 전환율: 2.1% (↓ 0.3%p)
- 방문자: 10,352명 (↑ 12%)
- 신규 가입: 231명 (→ 유지)

## 이슈
- [해결] 구매 완료 이벤트 누락 → 수정 배포 완료
- [진행중] 모바일 이탈률 높음 → 원인 분석 중

## 이번주 계획
- [ ] 프리미엄 배너 이벤트 추가 (공수: 2h)
- [ ] 검색 이벤트 파라미터 추가 (공수: 1h)

## 액션 아이템
- @운영팀: 프리미엄 배너 A/B 디자인 확정
- @개발: 이벤트 구현 및 배포
- @분석: 모바일 퍼널 분석 리포트
```

---

## 8. 체크리스트 & 다음 단계

### ✅ Week 2 완료 체크리스트

**기초:**
- [ ] GA4 이벤트 5개 이상 심기 (sign_up, login, purchase 등)
- [ ] 개발/프로덕션 환경 분리
- [ ] 실시간 보고서에서 이벤트 확인

**실무:**
- [ ] 운영팀과 이벤트 요구사항 정의 프로세스 합의
- [ ] 퍼널 1개 만들어보기
- [ ] 이벤트 네이밍 컨벤션 문서화

**협업:**
- [ ] 주간 리뷰 미팅 일정 잡기
- [ ] 이벤트 추가 요청서 양식 공유

### 🚀 Week 3 예고: A/B 테스트 & 빠른 실험

다음주에는:
- A/B 테스트 설계 방법
- GA4 + Firebase로 실험 돌리기
- 통계적 유의성 판단
- 빠른 학습 사이클 (Build → Measure → Learn)

---

## 📚 참고 자료

**공식 문서:**
- [GA4 Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/ga4)
- [react-ga4 GitHub](https://github.com/codler/react-ga4)

**추천 읽을거리:**
- [GA4 vs UA 차이점](https://support.google.com/analytics/answer/11583528)
- [이벤트 네이밍 Best Practices](https://www.ga4.guide/event-tracking/)

**디버깅 도구:**
- GA4 DebugView: 실시간 이벤트 디버깅
- Google Tag Assistant: 크롬 확장 프로그램

---

**다음 액션:**
1. 지금 당장 프로젝트에 GA4 초기화하기
2. 핵심 이벤트 3개 심기 (sign_up, login, purchase)
3. 실시간 보고서에서 확인하기
4. 운영팀과 첫 미팅 잡기

화이팅! 🚀
