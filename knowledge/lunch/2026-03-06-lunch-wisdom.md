# 점심 지식 Deep Dive: Code Mode의 혁명

**날짜:** 2026-03-06  
**출처:** [Cloudflare Blog - Code Mode: give agents an entire API in 1,000 tokens](https://blog.cloudflare.com/code-mode-mcp/)  
**분류:** AI 에이전트, API 설계, 시스템 아키텍처

---

## The Big Idea

**API를 수천 개의 도구로 나열하는 대신, 모델이 코드를 작성하게 하라. 컨텍스트 윈도우는 1,000배 절약되고, 능력은 무한대로 확장된다.**

Cloudflare는 2,500개 이상의 API 엔드포인트를 단 2개의 도구(`search()`, `execute()`)로 압축했다. 토큰 사용량은 117만 → 1,000개로 **99.9% 감소**했다.

---

## Argument Breakdown: 왜 Code Mode가 게임 체인저인가

### 1. MCP의 근본적 딜레마

**문제의 핵심:**
- AI 에이전트가 유용한 작업을 하려면 많은 도구가 필요하다
- 하지만 도구를 추가할수록 컨텍스트 윈도우가 가득 찬다
- 실제 작업에 사용할 공간이 줄어든다

**구체적 사례:**
```json
// 기존 MCP 방식: 각 API 엔드포인트마다 도구 정의
{
  "name": "create_dns_record",
  "description": "Create a new DNS record for a zone",
  "inputSchema": { /* 복잡한 스키마 */ }
}

// 2,500개 엔드포인트 × 평균 468 토큰 = 1,170,000 토큰
// → Claude Opus 4의 전체 context window보다 큼!
```

### 2. Code Mode의 우아한 해결책

**핵심 아이디어:**
도구 목록을 보여주는 대신, **모델이 코드를 작성하게 하라**.

```json
// Code Mode 방식: 단 2개의 도구
[
  {
    "name": "search",
    "description": "Search the Cloudflare OpenAPI spec",
    "inputSchema": {
      "code": "JavaScript async arrow function to search"
    }
  },
  {
    "name": "execute",
    "description": "Execute JavaScript code against the API",
    "inputSchema": {
      "code": "JavaScript async arrow function to execute"
    }
  }
]

// 총 토큰: ~1,000개 (고정)
```

**작동 방식:**

**Step 1: Discovery** (검색으로 필요한 엔드포인트만 찾기)

```javascript
// 모델이 작성한 코드
async () => {
  const results = [];
  for (const [path, methods] of Object.entries(spec.paths)) {
    if (path.includes('/zones/') && 
        path.includes('firewall/waf')) {
      for (const [method, op] of Object.entries(methods)) {
        results.push({ 
          method: method.toUpperCase(), 
          path, 
          summary: op.summary 
        });
      }
    }
  }
  return results;
}

// 결과: 2,500개 엔드포인트 → 10개로 압축
```

**Step 2: Execution** (여러 API 호출을 하나의 코드로)

```javascript
// 모델이 작성한 코드
async () => {
  // 1. DDoS 설정 확인
  const ddos = await cloudflare.request({
    method: "GET",
    path: `/zones/${zoneId}/rulesets/phases/ddos_l7/entrypoint`
  });

  // 2. WAF 설정 확인
  const waf = await cloudflare.request({
    method: "GET",
    path: `/zones/${zoneId}/rulesets/phases/http_request_firewall_managed/entrypoint`
  });

  // 3. 결과 비교 및 업데이트 로직
  // ...

  return { ddos, waf };
}

// 단일 tool call로 복잡한 워크플로우 실행
```

### 3. 보안: Dynamic Worker Isolate

**문제:** 모델이 작성한 코드를 실행하는 것은 위험하다.

**해결책:** V8 샌드박스 (Cloudflare Workers의 Dynamic Worker Loader)

```
✅ 안전하게 격리된 환경
✅ 파일 시스템 접근 불가
✅ 환경 변수 유출 방지 (prompt injection 방어)
✅ 외부 fetch 기본 비활성화
✅ 필요시 명시적으로 제어
```

CLI 접근 방식 (OpenClaw, Moltworker)과 비교:
- CLI는 셸이 필요 → 훨씬 넓은 공격 표면
- Code Mode는 격리된 샌드박스 → 최소한의 권한만

### 4. Progressive Discovery (점진적 발견)

**기존 방식:**
- 모든 도구를 미리 나열 → 컨텍스트 윈도우 낭비
- 에이전트가 뭘 할 수 있는지 사전에 알아야 함

**Code Mode:**
- 필요한 것만 검색으로 찾기
- 스키마 내부까지 드릴다운 가능

```javascript
// 예: "phase" enum 값 찾기
async () => {
  const op = spec.paths['/zones/{zone_id}/rulesets']?.get;
  const items = op?.responses?.['200']?.content?.['application/json']?.schema;
  const props = items?.allOf?.[1]?.properties?.result?.items?.allOf?.[1]?.properties;
  return { phases: props?.phase?.enum };
}

// 결과:
// ["ddos_l4", "ddos_l7", "http_request_firewall_custom", ...]
```

모델은 **필요한 정보만** 점진적으로 발견하며, 전체 스펙은 컨텍스트에 들어가지 않는다.

### 5. 다른 접근법과의 비교

| 방식 | 토큰 사용 | 보안 | Progressive Discovery | 에이전트 수정 필요 |
|------|-----------|------|----------------------|-------------------|
| **Native MCP** | 매우 높음 | 안전 | 없음 | 없음 |
| **Client-side Code Mode** | 낮음 | 샌드박스 필요 | 있음 | 있음 (샌드박스) |
| **CLI 변환** | 낮음 | 위험 (셸) | 있음 | 있음 (셸) |
| **Dynamic Tool Search** | 중간 | 안전 | 제한적 | 없음 |
| **Server-side Code Mode** | 매우 낮음 | 안전 (isolate) | 완전 | **없음** |

**Server-side Code Mode의 승리:**
- ✅ 고정 토큰 비용 (API 크기 무관)
- ✅ 에이전트 수정 불필요
- ✅ 완전한 progressive discovery
- ✅ 안전한 샌드박스 실행

---

## Context: 왜 지금 중요한가?

### 1. AI 에이전트의 폭발적 성장

**2026년 현황:**
- Claude Code, OpenAI Codex, Cursor 등 코딩 에이전트 대중화
- 개발자의 95%가 AI 에이전트 사용 (OpenAI 내부)
- MCP가 사실상 표준 프로토콜로 자리잡음

**문제:**
- 기업 API는 수백~수천 개 엔드포인트
- 모든 도구를 나열하면 컨텍스트 윈도우가 터짐
- → 에이전트가 실제 작업을 못하게 됨

### 2. 컨텍스트 윈도우 전쟁

**현재 최고 성능 모델들:**
- Claude Opus 4: 200K 토큰
- GPT-5.3 Codex: 128K 토큰
- Gemini 3 Pro: 1M 토큰 (하지만 실제 품질은 128K 이후 급락)

**현실:**
- Cloudflare API를 native MCP로 노출: 1.17M 토큰 필요
- → 최고 성능 모델도 감당 불가
- → Code Mode로 1,000 토큰으로 압축

### 3. "Models will eat your scaffolding for breakfast"

**Nicolas Bustamante의 통찰:**
> "모델이 점점 똑똑해지면서, 우리가 만든 '보조 바퀴'(scaffolding)를 불필요하게 만든다."

**구체적 사례:**
- 2023: RAG 파이프라인에 수백 줄의 전처리/후처리 코드
- 2024: 모델이 더 좋아지면서 대부분 불필요해짐
- 2025: Anthropic의 "Extended Thinking" → 복잡한 reasoning 체인도 모델이 직접

**Code Mode의 교훈:**
- 도구를 수동으로 매핑하고 관리하는 것 = scaffolding
- 모델에게 코드 작성 능력을 주면 → 무한 확장

**The Bitter Lesson (Richard Sutton):**
> "인간의 지식을 하드코딩하는 것보다, 계산(computation)과 탐색(search)을 활용하는 것이 장기적으로 이긴다."

Code Mode는 이 원칙을 API 도구 설계에 적용한 것이다.

### 4. 보안 vs 유연성의 균형

**AI 에이전트 보안 문제:**
- Prompt injection: 악의적 입력으로 에이전트 조종
- Privilege escalation: 권한 없는 작업 수행
- Data exfiltration: 민감 정보 유출

**Code Mode의 방어:**
1. **OAuth 2.1 준수:** 사용자가 명시적으로 승인한 권한만
2. **V8 isolate:** 파일 시스템, 환경 변수 차단
3. **Fetch control:** 외부 요청 명시적 허용 필요

**개발자에게 주는 교훈:**
> "보안을 위해 유연성을 희생하지 마라. 올바른 샌드박스를 사용하면 둘 다 얻을 수 있다."

---

## Application: 개발자로서 어떻게 적용할 것인가?

### 1. API 설계 패러다임 전환

**기존 사고방식:**
- "사용자가 할 수 있는 모든 작업을 개별 엔드포인트로 노출"
- RESTful API, GraphQL, gRPC 등

**새로운 사고방식:**
- "사용자가 코드를 작성하게 하고, 안전하게 실행하는 환경 제공"
- Code Mode, Function-as-a-Service (FaaS)

**실제 적용:**

```typescript
// beauty-sale 프로젝트 예시

// 기존: 개별 엔드포인트
GET  /products/search
POST /products/compare
GET  /products/{id}/price-history
POST /analytics/track-view

// Code Mode 방식
POST /execute
Body: {
  code: `
    async () => {
      // 검색
      const products = await api.products.search({ query: '선크림' });
      
      // 가격 비교
      const comparison = await api.products.compare(products.map(p => p.id));
      
      // 조회수 추적
      await api.analytics.trackView({ productIds: products.map(p => p.id) });
      
      return { products, comparison };
    }
  `
}
```

**장점:**
- 클라이언트가 복잡한 워크플로우를 하나의 요청으로 처리
- 네트워크 왕복 횟수 감소
- API 버전 관리 단순화

**주의사항:**
- 실행 시간 제한 필요
- 리소스 사용량 모니터링
- 악의적 코드 방어 (샌드박스 필수)

### 2. AI 에이전트 시대의 문서화

**기존 문서화:**
- OpenAPI/Swagger 스펙 자동 생성
- 각 엔드포인트마다 상세한 설명
- 예제 요청/응답

**Code Mode 시대의 문서화:**
- **타입 정의가 곧 문서**
- TypeScript/Zod 스키마로 API 정의
- 에이전트가 타입 정보만으로 코드 작성 가능

```typescript
// beauty-sale API 타입 정의
interface BeautySaleAPI {
  products: {
    search(query: { query: string; filters?: Filter[] }): Promise<Product[]>;
    compare(productIds: string[]): Promise<Comparison>;
    getPriceHistory(productId: string, days: number): Promise<PriceHistory>;
  };
  
  analytics: {
    trackView(productIds: string[]): Promise<void>;
    getPopularProducts(limit: number): Promise<Product[]>;
  };
}
```

**개발자 행동 지침:**
1. API를 설계할 때 타입부터 정의하라
2. TypeScript/Zod로 런타임 검증까지 커버하라
3. 복잡한 설명보다 명확한 타입 시그니처를 우선하라

### 3. 컨텍스트 윈도우 최적화 전략

**AI 에이전트 개발 시 적용:**

```python
# 나쁜 예: 모든 도구를 미리 정의
tools = [
    {
        "name": "search_product_by_keyword",
        "description": "Search products by keyword with filters...",
        "parameters": { ... }  # 수백 줄
    },
    {
        "name": "search_product_by_category",
        "description": "Search products by category...",
        "parameters": { ... }
    },
    # ... 수백 개 도구
]

# 좋은 예: Code Mode
tools = [
    {
        "name": "search",
        "description": "Search the API spec",
        "parameters": {
            "code": "JavaScript function to explore the spec"
        }
    },
    {
        "name": "execute",
        "description": "Execute code against the API",
        "parameters": {
            "code": "JavaScript function to execute"
        }
    }
]
```

**적용 시나리오:**
- **사이드 프로젝트:** 복잡한 API를 에이전트에 연결할 때
- **회사 업무:** 내부 도구를 AI 에이전트가 사용하게 만들 때
- **오픈소스 기여:** MCP 서버 개발 시

### 4. 샌드박스 실행 환경 구축

**Cloudflare Workers 대안 (로컬/온프레미스):**

1. **Node.js VM2 / Isolated-VM**
   ```typescript
   import { VM } from 'vm2';

   const vm = new VM({
     timeout: 1000,
     sandbox: {
       api: { /* 제한된 API만 노출 */ }
     }
   });

   const result = vm.run(userCode);
   ```

2. **Deno Sandbox**
   ```typescript
   // Deno는 기본적으로 권한 시스템이 엄격
   import { sandbox } from "https://deno.land/x/sandbox/mod.ts";

   const result = await sandbox.run(userCode, {
     permissions: { read: false, write: false, net: ["api.example.com"] }
   });
   ```

3. **Docker Container (무거워도 안전)**
   ```bash
   docker run --rm --network=none \
     --memory=128m --cpus=0.5 \
     node:alpine node -e "$USER_CODE"
   ```

**선택 기준:**
- 지연시간 < 100ms 필요 → V8 isolate (Workers, Deno)
- 복잡한 종속성 필요 → Docker
- 중간 수준 → VM2

### 5. 면접 준비: 이 개념을 말할 수 있는가?

**시스템 디자인 면접에서:**

Q: "수천 개의 API 엔드포인트를 AI 에이전트에게 어떻게 노출하겠습니까?"

**나쁜 답:**
- "각 엔드포인트를 개별 도구로 정의하고, 임베딩 검색으로 필요한 것만 찾습니다."

**좋은 답:**
- "Code Mode 패턴을 사용하겠습니다. 두 가지 도구만 노출합니다: `search()`로 API 스펙을 탐색하고, `execute()`로 코드를 실행합니다. 컨텍스트 윈도우 사용량은 O(1)로 고정되고, 모델의 코드 작성 능력을 활용하여 무한한 조합이 가능합니다. 보안을 위해 V8 isolate에서 코드를 실행하고, OAuth로 권한을 제한합니다."

**트레이드오프 설명:**
- 장점: 토큰 사용 99.9% 절감, 확장성 무한대
- 단점: 샌드박스 구현 복잡도, 실행 시간 오버헤드
- 언제 사용: API 엔드포인트 > 50개, 에이전트가 복잡한 워크플로우 수행

### 6. 실전 프로젝트: beauty-sale에 적용

**현재 상황:**
- beauty-sale은 멀티 플랫폼 가격 비교 앱
- API 엔드포인트 증가 중 (제품 검색, 가격 비교, 알림 등)

**Code Mode 적용 계획:**

**Phase 1: 내부 관리 도구 (우선)**
```typescript
// 관리자가 AI 에이전트로 복잡한 쿼리 실행
async () => {
  // 1. 최근 1주일간 급등한 제품 찾기
  const priceChanges = await db.query(`
    SELECT product_id, AVG(price_change_pct) as avg_change
    FROM price_history
    WHERE date > DATE_SUB(NOW(), INTERVAL 7 DAY)
    GROUP BY product_id
    HAVING avg_change > 20
  `);
  
  // 2. 해당 제품들의 조회수 확인
  const views = await analytics.getViews(priceChanges.map(p => p.product_id));
  
  // 3. 알림 보낼 사용자 찾기
  const users = await db.query(`
    SELECT user_id FROM user_watchlist
    WHERE product_id IN (${priceChanges.map(p => p.product_id).join(',')})
  `);
  
  return { priceChanges, views, usersToNotify: users.length };
}
```

**Phase 2: 고급 사용자 기능 (실험)**
```typescript
// 파워 유저가 커스텀 가격 알림 로직 작성
async () => {
  const myWatchlist = await api.watchlist.get();
  
  for (const item of myWatchlist) {
    const history = await api.products.getPriceHistory(item.productId, 30);
    const avg30d = history.reduce((sum, p) => sum + p.price, 0) / 30;
    
    if (item.currentPrice < avg30d * 0.8) {  // 30일 평균 대비 20% 이상 하락
      await api.notifications.send({
        title: `${item.name} 가격 급락!`,
        body: `30일 평균 대비 ${((1 - item.currentPrice / avg30d) * 100).toFixed(1)}% 하락`
      });
    }
  }
}
```

**예상 효과:**
- 관리자: 복잡한 분석 쿼리를 코드로 즉시 실행
- 파워 유저: 개인화된 알림 로직 구현
- 개발팀: API 엔드포인트 관리 부담 감소

---

## 핵심 요약: 3가지 Takeaway

### 1. **"Scaffolding은 모델이 먹어치운다"**

> 도구를 수동으로 나열하고 관리하는 것은 임시방편이다. 모델에게 코드를 작성하게 하고, 안전한 환경에서 실행하게 하라.

**실천 방법:**
- API를 설계할 때 "에이전트가 코드를 작성하면 어떻게 될까?" 고민
- 타입 정의를 먼저, 도구 설명은 나중에
- Progressive discovery를 염두에 둔 설계

### 2. **"컨텍스트 윈도우는 귀하다"**

> 토큰은 금이다. 1,000개로 할 수 있는 일을 117만 개 쓰지 마라.

**실천 방법:**
- AI 에이전트 개발 시 항상 토큰 사용량 측정
- `tiktoken` 등으로 프롬프트 크기 모니터링
- 도구가 10개 넘어가면 Code Mode 고려

### 3. **"샌드박스는 필수다"**

> 사용자가 작성한 코드를 실행하는 것은 위험하다. 하지만 올바른 샌드박스를 사용하면 안전하고 강력하다.

**실천 방법:**
- V8 isolate (Workers, Deno) > VM2 > Docker
- 파일 시스템, 네트워크, 환경 변수 접근 차단
- OAuth로 권한 명시적 제한

---

## 더 깊이 파고들기

**읽어볼 자료:**
1. [The Bitter Lesson (Richard Sutton)](http://www.incompleteideas.net/IncIdeas/BitterLesson.html) - 계산 > 인간 지식
2. [Anthropic's Code Execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp) - 동일 패턴을 독립적으로 발견
3. [Cloudflare MCP Server Portals](https://blog.cloudflare.com/zero-trust-mcp-server-portals/) - 여러 MCP 서버를 하나의 게이트웨이로

**실험해볼 것:**
1. [Cloudflare MCP Server](https://github.com/cloudflare/mcp) 직접 연결
2. [Code Mode SDK](https://github.com/cloudflare/agents/tree/main/packages/codemode) 로 자신의 API에 적용
3. beauty-sale 관리 도구에 Code Mode 프로토타입 구현

---

## 마지막 생각

Code Mode는 단순한 최적화 기법이 아니다. **AI 에이전트 시대의 API 설계 철학**이다.

**기존:**
- "사용자가 할 수 있는 모든 것을 미리 정의"
- API는 명령어 집합

**미래:**
- "사용자가 원하는 것을 표현할 수 있는 언어 제공"
- API는 안전한 실행 환경

이 패러다임 전환은 다음과 같은 질문을 던진다:

> "개발자가 작성하는 코드와 AI가 작성하는 코드의 경계는 어디인가?"

답은 아직 명확하지 않다. 하지만 한 가지는 확실하다:

**우리는 더 이상 모든 가능성을 미리 나열할 필요가 없다. 모델에게 탐색하고 조합할 수 있는 도구만 주면 된다.**

---

**실천 과제:**
1. 오늘 점심 후, `tiktoken`으로 자주 사용하는 프롬프트의 토큰 수를 측정해보라.
2. 이번 주, beauty-sale의 한 기능을 "Code Mode로 설계하면 어떨까?" 관점에서 다시 생각해보라.
3. 다음 1:1 미팅에서, "Code Mode가 우리 API 설계에 주는 시사점"을 논의하라.

**르네상스 개발자가 되는 길:**
- 기술 = Kotlin + Spring + AI
- 제품 = 사용자가 원하는 것을 표현할 언어 설계
- 교양 = 계산 철학, 시스템 사고, 보안 원칙

**Code Mode는 이 세 가지가 만나는 지점이다.**
