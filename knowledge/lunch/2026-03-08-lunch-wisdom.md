# 점심 지식 Deep Dive: Harness Engineering - AI를 제어하는 기술

**날짜:** 2026-03-08  
**출처:** [Martin Fowler - Harness Engineering](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)  
**분류:** AI 엔지니어링, 소프트웨어 아키텍처, 유지보수성

---

## The Big Idea

**"AI 에이전트가 코드를 작성하는 시대, 진짜 엔지니어링은 AI를 '하네스'(제어)하는 것이다. 무한한 자유가 아니라, 제약과 가드레일을 설계하는 것이 신뢰할 수 있는 코드의 열쇠다."**

OpenAI 팀: 5개월 동안 **손으로 코드 한 줄 안 쓰고**, AI 에이전트만으로 100만 줄 이상의 프로덕션 애플리케이션 구축 및 유지보수.

Martin Fowler의 통찰: 이게 가능했던 이유는 **"Harness"** - AI를 제어하는 도구와 관행.

---

## Argument Breakdown: Harness Engineering이란 무엇인가

### 1. 실험의 전제: "Forcing Function"

**OpenAI 팀의 규칙:**
```
"No manually typed code at all"
(손으로 코드 한 줄도 쓰지 않는다)
```

**왜 이런 규칙?**
- Forcing function (강제 함수): 불편한 제약을 걸어서 새로운 방법을 찾게 만든다
- AI 에이전트만으로 실제 프로덕션 코드를 유지보수할 수 있는지 극한 테스트
- 5개월 후 결과: 100만 줄 이상의 코드

**"Harness"라는 용어:**
- 말의 마구(harness): 말을 제어하고 방향을 잡는 도구
- Harness Engineering: AI 에이전트를 제어하고 방향을 잡는 엔지니어링
- Mitchell Hashimoto가 최근 블로그에서 사용 → OpenAI 팀도 채택

**Martin Fowler의 반응:**
> "나는 이 공간에서 드물게 마음에 드는 용어를 찾았다. 'Harness' - AI를 제어하는 도구와 관행을 설명하는 단어로 딱 맞다."

### 2. Harness의 3가지 구성 요소

OpenAI 팀의 Harness는 **결정론적(deterministic) 접근**과 **LLM 기반 접근**을 섞어 사용:

#### A. Context Engineering (맥락 엔지니어링)

**정적 맥락:**
- 코드베이스 내부의 **지속적으로 강화되는 지식 베이스**
- 아키텍처 문서, 설계 결정 기록 (ADR), 코딩 가이드라인
- AI 에이전트가 참조하는 "규칙의 책"

**동적 맥락:**
- Observability 데이터 (로그, 메트릭, 트레이스)
- 브라우저 네비게이션 (테스트, UI 검증)
- 실시간 시스템 상태

**왜 중요한가?**
AI는 맥락 없이는 무용지물. 
"이 코드가 왜 이렇게 생겼는가?"를 이해해야 제대로 수정할 수 있다.

**개발자 버전:**
```kotlin
// Bad: 맥락 없는 코드
fun process(data: List<Any>): List<Any> {
    return data.map { transform(it) }
}

// Good: 맥락이 있는 코드
/**
 * 제품 가격 비교 파이프라인
 * 
 * Context:
 * - beauty-sale은 여러 플랫폼에서 가격을 크롤링
 * - 각 플랫폼은 다른 통화, 포맷, 할인 규칙 사용
 * - 이 함수는 모든 플랫폼 데이터를 표준 형식으로 변환
 * 
 * Constraints:
 * - 변환 실패 시 해당 항목 스킵 (전체 실패 X)
 * - 최소 3개 플랫폼 데이터 필요 (신뢰도)
 * 
 * See: docs/adr/2026-001-price-normalization.md
 */
fun normalizePrices(
    rawPrices: List<PlatformPrice>
): NormalizedPriceData {
    // ...
}
```

AI 에이전트가 이 맥락을 읽으면, "왜 3개 플랫폼인가?" 같은 질문에 답을 찾을 수 있다.

#### B. Architectural Constraints (아키텍처 제약)

**결정론적 체크:**
- Custom linters (맞춤 린터)
- Structural tests (구조 테스트)
- "Data shapes at the boundary" 파싱 (경계에서 데이터 형태 강제)

**LLM 기반 에이전트:**
- 아키텍처 가이드라인 위반 감지
- 일관성 체크

**핵심 아이디어:**
> "무한한 자유가 아니라, 제약된 해법 공간"

**Martin Fowler의 통찰:**
```
초기 AI 코딩 hype:
"LLM이 무엇이든 생성! 어떤 언어, 어떤 패턴, 제약 없이!"

현실:
대규모, 유지보수 가능, 신뢰할 수 있는 AI 생성 코드를 원한다면
→ 뭔가를 포기해야 한다

That "something" = 자유
Trade-off: 구체적 아키텍처 패턴, 강제된 경계, 표준화된 구조
```

**구체적 예시:**

**ArchUnit (구조 테스트 프레임워크):**
```kotlin
@Test
fun `Repository는 Service에서만 접근 가능`() {
    classes()
        .that().resideInPackage("..repository..")
        .should().onlyBeAccessed().byAnyPackage("..service..")
        .check(allClasses)
}

@Test
fun `Controller는 DTO만 반환`() {
    classes()
        .that().resideInPackage("..controller..")
        .and().haveSimpleNameEndingWith("Controller")
        .should().haveOnlyMethodReturnTypes(assignableTo(ResponseEntity::class.java))
        .check(allClasses)
}
```

AI 에이전트가 이 규칙을 어기는 코드를 생성하면 → 테스트 실패 → 다시 생성

**Custom Linter:**
```python
# beauty-sale 맞춤 린터 예시
def check_price_comparison_endpoint(code):
    """가격 비교 엔드포인트는 반드시 캐싱 포함"""
    if '@GetMapping' in code and 'price' in code:
        if '@Cacheable' not in code:
            raise LintError("Price endpoints must use @Cacheable")
```

#### C. "Garbage Collection" (엔트로피 관리)

**주기적으로 실행되는 에이전트:**
- 문서 일관성 체크 (코드와 문서가 일치하는가?)
- 아키텍처 제약 위반 검색
- 엔트로피와 부패(decay)와 싸움

**왜 "Garbage Collection"?**
- 소프트웨어는 시간이 지나면 부패한다 (Software Entropy)
- 문서는 오래되고, 코드는 복잡해지고, 일관성은 깨진다
- AI 에이전트가 주기적으로 "청소"

**예시:**
```
Weekly Garbage Collection Agent:

1. 문서 검증:
   - README.md에 언급된 API가 실제로 존재하는가?
   - ADR에 설명된 패턴이 코드에 적용되어 있는가?

2. 아키텍처 부채 탐지:
   - 순환 의존성(circular dependency) 발견
   - 미사용 코드(dead code) 발견
   - 복잡도 임계값 초과 (Cyclomatic Complexity > 15)

3. 자동 수정 제안:
   - "이 함수는 6개월간 호출되지 않았습니다. 제거할까요?"
   - "이 문서는 코드와 불일치합니다. 업데이트할까요?"
```

### 3. 반복적 개선: "Signal as Feedback"

**OpenAI 팀의 원칙:**
> "에이전트가 어려워하면, 그건 신호다: 무엇이 빠졌는가 - 도구, 가드레일, 문서 - 를 찾아내고 레포지토리에 피드백한다. 항상 Codex 자신이 수정을 작성하게 한다."

**선순환:**
```
1. AI 에이전트가 막힌다
   ↓
2. "무엇이 빠졌는가?" 분석
   - 도구? (새 CLI 필요?)
   - 가드레일? (새 린터 규칙?)
   - 문서? (맥락 추가?)
   ↓
3. Codex에게 수정 요청
   ↓
4. Harness 강화
   ↓
5. 다음 문제는 더 쉬워진다
```

**인간 개발자 버전:**
```
시나리오: beauty-sale에 새 기능 추가

1. AI 에이전트: "가격 비교 로직을 어디에 추가할까?"
   → 막힘 (아키텍처 문서 없음)

2. 인간 개발자: 
   - ADR 작성: "왜 Service 계층에서 비교 로직?"
   - 예제 코드 추가
   - 문서 업데이트

3. AI 에이전트: 이제 다음 기능은 쉽게 추가
   (문서와 예제가 있으니까)

4. Harness가 점점 강해진다
```

### 4. Martin Fowler가 놓친 부분

**문제 제기:**
> "내가 글에서 놓친 것: 기능과 동작의 검증"

**OpenAI 글은 다룬 것:**
- 내부 품질 (Internal Quality)
- 유지보수성 (Maintainability)
- 일관성 (Consistency)

**다루지 않은 것:**
- 기능이 제대로 작동하는가? (Functional Correctness)
- 사용자 요구사항을 충족하는가?
- 버그는 없는가?

**Martin Fowler의 암시:**
"이 Gap을 제쳐두고..." → 중요한 문제지만, 이 글에서는 다루지 않았다는 뜻

**개발자가 생각해야 할 것:**
```
Harness가 보장하는 것:
✅ 코드가 일관되다
✅ 아키텍처 패턴을 따른다
✅ 문서와 코드가 일치한다

Harness가 보장하지 못하는 것:
❓ 비즈니스 로직이 맞는가?
❓ 사용자 경험이 좋은가?
❓ 성능이 충분한가?
❓ 보안 취약점은 없는가?

→ 여전히 인간의 판단 필요
```

---

## Context: 왜 지금 이 글이 중요한가?

### 1. AI 코딩의 성숙기 진입 (2026년)

**초기 Hype (2023-2024):**
```
"AI가 코드를 작성한다!"
"개발자는 필요 없어진다!"
"프롬프트만 잘 쓰면 끝!"
```

**현실 (2026년):**
```
"AI가 코드를 작성한다... 하지만"
"누군가 AI를 제어해야 한다"
"그게 바로 엔지니어링이다"
```

**Harness Engineering의 등장:**
- AI는 도구다 (Tool)
- 도구를 제어하는 것이 엔지니어링 (Engineering)
- "Harness" = 제어 메커니즘

**Gartner Hype Cycle:**
```
2023: Peak of Inflated Expectations
      "AI가 모든 걸 해결!"
      
2024-2025: Trough of Disillusionment
           "AI 코드는 쓰레기네?"
           
2026: Slope of Enlightenment ← 우리 여기
      "AI를 제대로 쓰는 법을 알았다"
      = Harness Engineering
```

### 2. "Generate Anything" 환상의 종말

**초기 가정:**
> "LLM은 무엇이든 생성할 것이다. 어떤 언어, 어떤 패턴, 제약 없이."

**Martin Fowler의 반박:**
> "신뢰할 수 있는 대규모 AI 생성 코드를 원한다면, 뭔가를 포기해야 한다."

**Trade-off:**
```
포기하는 것:
- 무한한 자유
- "뭐든지 생성" 환상
- 프롬프트로 모든 것 해결

얻는 것:
- 신뢰성 (Reliability)
- 유지보수성 (Maintainability)
- 일관성 (Consistency)
```

**구체적 예:**
```
AI Copilot (2024):
"이 기능 구현해줘" → 코드 생성 → 복붙 → 작동 → 다음

Harness Engineering (2026):
"이 기능 구현해줘"
 → Harness 체크:
    - 아키텍처 패턴 준수?
    - 테스트 포함?
    - 문서 업데이트?
    - 성능 기준 충족?
 → OK → 통합

결과: 느리지만 신뢰 가능
```

### 3. 기술 스택의 수렴 (Convergence)

**Martin Fowler의 예측:**
> "AI가 코딩을 생성하는 시대, 우리는 더 적은 기술 스택으로 수렴할 것이다."

**왜?**

**과거 (인간이 모든 코드 작성):**
```
개발자 취향 중요:
- "Kotlin이 더 깔끔해"
- "Ruby가 더 재밌어"
- "Go가 더 빠르네"

→ 다양한 기술 스택
```

**미래 (AI가 대부분 코드 작성):**
```
개발자 취향 덜 중요:
- 직접 안 쓰니까 작은 불편함 신경 안 쓰임
- "AI-friendliness" 우선
- "좋은 Harness 있는 스택" 선택

→ 소수의 주요 스택으로 수렴
```

**선택 기준 변화:**
```
과거:
✅ 개발자 경험 (DX)
✅ 생산성
✅ 커뮤니티
✅ 취향

미래:
✅ AI-friendliness
✅ Harness 품질
✅ 구조적 일관성
✅ 유지보수성
(취향은 덜 중요)
```

**구체적 예:**
```
2024: "Kotlin vs Java 논쟁"
      - Kotlin: 간결, 표현력
      - Java: 안정성, 생태계
      
2026: "어느 쪽이 Harness하기 쉬운가?"
      - Kotlin: Coroutines 복잡, DSL 다양
      - Java: 단순, 예측 가능, 표준화
      
결과: Java 선택 (AI 제어 용이)
```

**반직관적이지만 논리적:**
- 인간이 직접 쓸 때: 간결함, 표현력 중요
- AI가 생성할 때: 단순함, 예측 가능성 중요

### 4. 레거시 vs 신규의 갈림길

**Martin Fowler의 질문:**
> "좋은 Harness 기법을 개발하면, 어떤 기법은 기존 애플리케이션에 적용 가능하고, 어떤 것은 Harness를 염두에 둔 처음부터 새로 만든 애플리케이션에만 가능할까?"

**두 개의 세계:**

**Pre-AI 코드베이스:**
```
특징:
- 비표준화 (Snowflake)
- 엔트로피 가득
- 문서 없음 또는 오래됨
- 아키텍처 패턴 불명확

Harness 적용:
- 가능하지만 비용 높음
- "Static Analysis를 처음 돌렸을 때 수천 개 경고"
- ROI 불분명

선택:
- 레거시는 손으로 유지보수?
- 아니면 비용 들여 Harness 적용?
```

**Post-AI 코드베이스:**
```
특징:
- 처음부터 Harness와 함께 설계
- 구조적 일관성
- 문서와 코드 동기화
- 명확한 아키텍처 패턴

Harness 적용:
- 자연스럽게 작동
- AI 에이전트가 쉽게 이해
- 유지보수 비용 낮음

선택:
- 신규 프로젝트는 Harness 기본
```

**실무 시사점:**
```
이직 시 질문:
"이 회사의 코드베이스는 Pre-AI인가 Post-AI인가?"

Pre-AI:
- 레거시 개선 프로젝트
- Harness 도입은 힘듦
- 손으로 많이 써야 함

Post-AI:
- 현대적 아키텍처
- AI 도구 활용 가능
- 생산성 높음

→ Post-AI 코드베이스 선호
```

### 5. "Relocating Rigor" - 엄격함의 재배치

**Chad Fowler의 개념:**
> "AI가 코드를 작성하면, 엄격함(Rigor)은 어디로 가는가?"

**과거:**
```
엄격함의 위치: 코드 작성 시

- 타입 체크
- 테스트 작성
- 코드 리뷰
- 린터 통과

→ 인간이 코드 쓸 때 엄격
```

**미래:**
```
엄격함의 위치: 환경, 피드백 루프, 제어 시스템 설계

- Harness 설계
- Context Engineering
- Architectural Constraints
- Garbage Collection

→ AI를 제어하는 메커니즘이 엄격
```

**OpenAI 팀의 경험:**
> "우리의 가장 어려운 과제는 이제 환경, 피드백 루프, 제어 시스템을 설계하는 것에 집중된다."

**개발자 역할 변화:**
```
과거: 코드를 잘 쓰는 사람
미래: Harness를 잘 설계하는 사람

과거: "이 함수 어떻게 구현할까?"
미래: "AI가 어떻게 안전하게 이 함수를 생성하게 만들까?"

과거: Coder
미래: Harness Engineer
```

**Martin Fowler의 환영:**
> "단순히 '더 나은 모델'이 유지보수 문제를 마법처럼 해결해주길 바라는 게 아니라, 그 엄격함이 어디로 갈지에 대한 구체적 아이디어와 경험을 듣게 되어 신선하다."

---

## Application: 개발자로서 어떻게 적용할 것인가?

### 1. 현재 Harness 점검하기

**Martin Fowler의 질문:**
> "오늘 당신의 Harness는 무엇인가?"

**자가 진단:**
```markdown
## My Current Harness

### 1. Pre-commit Hooks
- [ ] Lint 실행?
- [ ] 테스트 실행?
- [ ] 포맷팅 체크?

### 2. Custom Linters
- [ ] 프로젝트별 규칙 있음?
- [ ] 아키텍처 제약 강제?

### 3. Structural Tests
- [ ] ArchUnit 같은 도구 사용?
- [ ] 모듈 경계 검증?

### 4. Documentation
- [ ] ADR 작성?
- [ ] 아키텍처 문서 최신?
- [ ] 예제 코드 포함?

### 5. Automated Checks
- [ ] CI/CD에서 뭘 체크?
- [ ] 주기적 검증?
```

**beauty-sale 예시:**
```
현재 상태:
✅ Pre-commit: ESLint, Prettier
❌ Custom Linter 없음
❌ Structural Tests 없음
✅ README 있음 (하지만 오래됨)
❌ ADR 없음
✅ CI: 테스트, 빌드

개선 필요:
1. ADR 시작 (큰 결정 기록)
2. ArchUnit 도입 (모듈 경계)
3. Custom Lint 규칙 (가격 비교 엔드포인트 캐싱 필수 등)
4. 문서 자동 검증 (코드와 일치?)
```

### 2. Context Engineering 실천

**지식 베이스 구축:**

**A. ADR (Architecture Decision Records) 시작**
```markdown
# ADR-001: 가격 정규화를 Service 계층에서 처리

## Status
Accepted

## Context
beauty-sale은 여러 플랫폼(쿠팡, 올리브영, 11번가)에서 가격을 수집한다.
각 플랫폼은 다른 통화, 포맷, 할인 규칙을 사용한다.

## Decision
가격 정규화 로직을 Service 계층에 배치한다.
Repository는 원본 데이터 그대로 반환.

## Consequences
✅ Repository 단순화
✅ 비즈니스 로직 중앙화
✅ 테스트 용이
❌ Service 복잡도 증가

## Alternatives Considered
1. Repository에서 정규화: 데이터 접근 계층이 비즈니스 로직 알아야 함
2. Controller에서 정규화: 중복 코드 발생 가능

## References
- `PriceNormalizationService.kt`
- 테스트: `PriceNormalizationServiceTest.kt`
```

**B. 코드 내 풍부한 주석**
```kotlin
/**
 * 가격 비교 결과 캐싱 전략
 * 
 * WHY 캐싱?
 * - 외부 크롤링 비용 높음 (네이버 쇼핑, 쿠팡 API)
 * - 가격은 자주 변하지 않음 (1-2시간 단위)
 * - 동일한 제품 검색이 많음 (인기 제품)
 * 
 * TTL 1시간 이유:
 * - 너무 짧으면: 크롤링 부담
 * - 너무 길면: 가격 변동 놓침
 * - 1시간 = 실시간성과 비용의 균형
 * 
 * 주의사항:
 * - 캐시 미스 시 fallback 로직 (최소 3개 플랫폼)
 * - 가격 급변 상황 (flash sale) 고려 안 됨 → Phase 2
 * 
 * @see ADR-003: Caching Strategy
 * @see PriceComparisonCacheConfig
 */
@Cacheable(
    value = ["priceComparison"],
    key = "#productId",
    cacheManager = "priceComparisonCacheManager"
)
fun comparePrices(productId: String): PriceComparison {
    // ...
}
```

**C. 예제 코드 라이브러리**
```
docs/examples/
├── new-endpoint.kt          # 새 엔드포인트 추가 템플릿
├── new-service.kt           # 새 서비스 추가 템플릿
├── external-api-call.kt     # 외부 API 호출 패턴
├── caching-pattern.kt       # 캐싱 적용 패턴
└── testing-guide.kt         # 테스트 작성 가이드
```

AI 에이전트(또는 주니어 개발자)가 막히면 → 예제 참조

### 3. Architectural Constraints 강화

**A. ArchUnit 도입**
```kotlin
// test/architecture/LayerArchitectureTest.kt

@AnalyzeClasses(packages = ["com.beautysale"])
class LayerArchitectureTest {
    
    @ArchTest
    val `Controller는 Service만 호출` = classes()
        .that().resideInPackage("..controller..")
        .should().onlyDependOnClassesThat()
        .resideInAnyPackage("..service..", "..dto..", "java..")
    
    @ArchTest
    val `Service는 Repository와 다른 Service만 호출` = classes()
        .that().resideInPackage("..service..")
        .should().onlyAccessClassesThat()
        .resideInAnyPackage("..repository..", "..service..", "..domain..", "java..")
    
    @ArchTest
    val `Repository는 Domain만 반환` = classes()
        .that().resideInPackage("..repository..")
        .should().onlyHaveMethodReturnTypes(
            resideInAnyPackage("..domain..", "java..", "kotlin..")
        )
    
    @ArchTest
    val `순환 의존성 금지` = slices()
        .matching("com.beautysale.(*)..")
        .should().beFreeOfCycles()
}
```

**B. Custom Linter (Ktlint 커스텀 규칙)**
```kotlin
// build.gradle.kts에서 ktlint custom rules

// CustomRules.kt
class PriceEndpointCachingRule : Rule("price-endpoint-caching") {
    override fun visit(node: ASTNode, emit: EmitType) {
        if (node.elementType == FUNCTION) {
            val annotations = node.findChildByType(MODIFIER_LIST)?.text ?: ""
            val functionName = node.findChildByType(IDENTIFIER)?.text ?: ""
            
            if (annotations.contains("@GetMapping") && 
                functionName.contains("price", ignoreCase = true) &&
                !annotations.contains("@Cacheable")) {
                emit(node, "Price endpoints must use @Cacheable", false)
            }
        }
    }
}
```

**C. Pre-commit Hook 강화**
```bash
# .git/hooks/pre-commit

#!/bin/bash

echo "🔍 Running Harness checks..."

# 1. Lint
./gradlew ktlintCheck || exit 1

# 2. Architecture Tests
./gradlew test --tests "*ArchitectureTest*" || exit 1

# 3. Custom Checks
python scripts/check_documentation.py || exit 1

echo "✅ All Harness checks passed!"
```

### 4. "Garbage Collection" 에이전트 구현

**주간 자동화 스크립트:**
```kotlin
// scripts/weekly-garbage-collection.main.kts

import java.io.File

fun main() {
    println("🗑️ Running Weekly Garbage Collection...")
    
    // 1. 문서-코드 일치 검증
    checkDocumentationConsistency()
    
    // 2. 미사용 코드 탐지
    findDeadCode()
    
    // 3. 복잡도 임계값 초과
    checkComplexity()
    
    // 4. 순환 의존성
    checkCircularDependencies()
    
    println("✅ Garbage Collection complete!")
}

fun checkDocumentationConsistency() {
    // README.md에 언급된 API 엔드포인트 존재 여부
    val readme = File("README.md").readText()
    val mentionedEndpoints = Regex("""`GET /api/[^`]+`""").findAll(readme)
    
    mentionedEndpoints.forEach { match ->
        val endpoint = match.value.removeSurrounding("`")
        // 실제 코드에서 @GetMapping 검색
        val exists = findInCodebase("@GetMapping(\"$endpoint\")")
        if (!exists) {
            println("⚠️  README mentions $endpoint but it doesn't exist!")
        }
    }
}

fun findDeadCode() {
    // 6개월간 Git history에서 변경되지 않은 파일
    val oldFiles = exec("git log --since='6 months ago' --name-only --pretty=format: | sort | uniq")
    // 분석 및 보고
}

fun checkComplexity() {
    // 복잡도 15 이상 함수 찾기
    exec("./gradlew detekt")
    // 보고서 파싱 및 요약
}
```

**Cron으로 자동 실행:**
```bash
# 매주 일요일 오전 9시
0 9 * * 0 cd /path/to/beauty-sale && ./gradlew weekly-gc
```

### 5. 반복적 개선 루틴

**AI와 함께 일할 때:**
```
1. AI가 막히는 순간 포착
   예: "이 테스트 어떻게 작성해야 하죠?"
   
2. "무엇이 빠졌는가?" 분석
   → 테스트 예제가 없음
   
3. Harness에 추가
   → docs/examples/testing-guide.kt 작성
   
4. 다음에는 AI가 스스로 해결
   
5. Harness 점점 강화
```

**주간 회고에 포함:**
```markdown
## Weekly Retrospective

### AI Struggles This Week
- [ ] AI가 어려워한 작업 3가지
- [ ] 각각 왜 어려웠나?
- [ ] 무엇을 추가하면 다음엔 쉬워질까?

### Harness Improvements
- [ ] 이번 주 추가한 문서
- [ ] 이번 주 추가한 린터 규칙
- [ ] 이번 주 추가한 테스트
```

### 6. 면접 준비: Harness Engineering 어필

**시스템 디자인 면접:**
```
면접관: "대규모 코드베이스를 어떻게 유지보수 가능하게 만드나요?"

약한 답:
"테스트 작성하고, 코드 리뷰하고, 린터 돌립니다"

강한 답:
"Harness Engineering 접근을 사용합니다:

1. Context Engineering:
   - ADR로 아키텍처 결정 기록
   - 예제 코드 라이브러리
   - 풍부한 인라인 문서

2. Architectural Constraints:
   - ArchUnit로 계층 경계 강제
   - 커스텀 린터로 프로젝트별 규칙
   - Pre-commit hook으로 자동 검증

3. Garbage Collection:
   - 주기적 문서-코드 일치 검증
   - 미사용 코드 자동 탐지
   - 복잡도 모니터링

이 접근은 OpenAI 팀이 100만 줄 코드를 AI로 유지보수한 방법에서 영감 받았습니다."

면접관: 😲 "흥미롭네요, 더 설명해주세요"
```

**기술 면접:**
```
면접관: "AI 코딩 도구 사용해봤나요?"

약한 답:
"네, Copilot 씁니다. 코드 자동완성 좋아요"

강한 답:
"네, 하지만 단순 자동완성을 넘어 Harness 설계에 집중합니다.

beauty-sale 프로젝트에서:
- AI가 생성한 코드가 아키텍처 패턴 따르는지 ArchUnit로 검증
- 가격 비교 엔드포인트는 반드시 캐싱 포함하도록 커스텀 린터
- 문서와 코드 일치 여부를 주기적으로 체크

AI는 도구입니다. 진짜 엔지니어링은 AI를 제어하는 Harness를 설계하는 것이죠."

면접관: 😲 "시니어 개발자 마인드네요"
```

### 7. 이직 시 질문할 것

**회사의 코드베이스 파악:**
```
질문 1: "코드베이스는 언제 시작되었나요?"
→ Pre-AI (2023 이전) vs Post-AI (2024 이후)

질문 2: "아키텍처 문서가 있나요? ADR을 작성하나요?"
→ Context Engineering 수준 파악

질문 3: "구조 테스트나 커스텀 린터를 사용하나요?"
→ Architectural Constraints 여부

질문 4: "코드 품질을 어떻게 유지하나요?"
→ Garbage Collection 메커니즘

질문 5: "AI 도구 사용 정책은?"
→ AI 도입 수준, Harness 인식

좋은 답:
"ADR 작성하고, ArchUnit 쓰고, 주기적으로 기술 부채 리뷰합니다"
→ Post-AI 마인드셋

나쁜 답:
"음... 그냥 코드 리뷰요?"
→ Pre-AI 마인드셋
```

### 8. 개인 프로젝트에 적용

**beauty-sale Harness 구축 계획:**

**Week 1-2: Foundation**
- [ ] ADR 템플릿 만들기
- [ ] 첫 3개 ADR 작성 (이미 내린 중요한 결정)
- [ ] README 업데이트 (현재 상태 정확히)

**Week 3-4: Constraints**
- [ ] ArchUnit 의존성 추가
- [ ] 기본 아키텍처 테스트 3개 작성
- [ ] Pre-commit hook 강화

**Week 5-6: Examples**
- [ ] docs/examples/ 폴더 생성
- [ ] 5개 템플릿 작성 (endpoint, service, test 등)
- [ ] 인라인 주석 풍부하게 (기존 코드)

**Week 7-8: Automation**
- [ ] 주간 Garbage Collection 스크립트
- [ ] 문서-코드 일치 검증
- [ ] Cron으로 자동화

**Month 3: 결과 측정**
- AI Copilot이 얼마나 더 정확해졌나?
- 새 기능 추가가 얼마나 빨라졌나?
- 코드 품질 지표 개선?

---

## 핵심 요약: 3가지 Takeaway

### 1. **"AI를 제어하는 것이 진짜 엔지니어링이다"**

**과거 (Pre-AI):**
```
엔지니어 = 코드를 잘 쓰는 사람
```

**현재 (AI 시대):**
```
엔지니어 = AI를 제어하는 Harness를 설계하는 사람
```

**Martin Fowler의 통찰:**
> "Harness - AI를 제어하는 도구와 관행"

**실천:**
- Context Engineering: 맥락 제공
- Architectural Constraints: 경계 설정
- Garbage Collection: 엔트로피 관리

### 2. **"자유를 포기하고 신뢰를 얻는다"**

**환상:**
```
"AI는 무엇이든 생성할 것이다!"
```

**현실:**
```
"신뢰할 수 있는 코드를 원한다면
 → 제약된 해법 공간이 필요하다"
```

**Trade-off:**
```
포기: 무한한 자유
획득: 신뢰성, 유지보수성, 일관성
```

**개발자 적용:**
- ArchUnit으로 계층 경계 강제
- Custom Linter로 프로젝트 규칙 강제
- ADR로 설계 결정 기록

### 3. **"엄격함은 코드에서 Harness로 이동한다"**

**Relocating Rigor:**
```
과거: 코드 작성 시 엄격
미래: 환경/피드백 루프/제어 시스템 설계 시 엄격
```

**OpenAI 팀:**
> "가장 어려운 과제는 환경, 피드백 루프, 제어 시스템을 설계하는 것"

**시니어 개발자의 미래:**
- 코드를 덜 쓴다
- 하지만 더 중요한 일을 한다
- Harness를 설계하고
- AI가 안전하게 코드 생성하도록 만든다

---

## 더 깊이 파고들기

**읽어볼 자료:**
1. **OpenAI - Harness Engineering** - 원본 글
2. **Mitchell Hashimoto - My AI Adoption Journey (Step 5: Engineer the Harness)** - "Harness" 용어의 유래
3. **Chad Fowler - Relocating Rigor** - 엄격함의 재배치
4. **Martin Fowler - Exploring Gen AI** - AI 시대의 소프트웨어 엔지니어링
5. **ArchUnit Documentation** - 구조 테스트 프레임워크

**실험해볼 것:**
1. **ADR 시작**
   - 이번 주 큰 결정 하나를 ADR로 기록
   - 템플릿: Why, Decision, Consequences, Alternatives

2. **ArchUnit 도입**
   - beauty-sale에 의존성 추가
   - 첫 아키텍처 테스트 3개 작성
   - CI에 통합

3. **Custom Linter**
   - 프로젝트별 규칙 하나 정의
   - Ktlint custom rule 작성
   - Pre-commit hook에 추가

4. **Documentation Check**
   - README에 언급된 API 실제 존재 여부 스크립트
   - 주간 Cron으로 자동 실행

5. **AI 실험**
   - Copilot/ChatGPT에게 "이 패턴 따라서 구현해줘"
   - 얼마나 정확한지 측정
   - Harness 추가하면서 정확도 개선 관찰

---

## 마지막 생각

Martin Fowler의 글은 AI 코딩의 성숙기를 보여준다.

**2023-2024: Hype**
```
"AI가 개발자를 대체한다!"
"프롬프트만 잘 쓰면 끝!"
```

**2025: Disillusionment**
```
"AI 코드는 쓰레기..."
"결국 손으로 다시 써야 함"
```

**2026: Enlightenment** ← 우리 여기
```
"AI는 도구다"
"제어하는 것이 엔지니어링이다"
"Harness를 설계하는 것이 진짜 일이다"
```

**두 가지 길:**

**Path A: AI에게 대체되는 개발자**
```
"AI한테 물어보고 복붙"
"작동하면 OK"
"다음 문제로"

→ 1년 후: 여전히 주니어
→ AI가 나보다 낫다
```

**Path B: AI를 제어하는 엔지니어**
```
"AI를 어떻게 안전하게 쓸까?"
"Harness를 어떻게 설계할까?"
"Context, Constraints, Garbage Collection"

→ 1년 후: 시니어
→ AI는 내 도구다
```

**어느 길을 선택할 것인가?**

**Martin Fowler의 메시지:**
> "단순히 '더 나은 모델'을 기다리지 마라. 엄격함이 어디로 가야 하는지 생각하고, Harness를 설계하라."

**우리의 메시지:**
> "르네상스 개발자는 AI를 사용하되, AI에게 대체되지 않는다. Harness Engineering으로 무장한다."

---

**실천 과제:**

**오늘 (일요일):**
1. 현재 프로젝트의 Harness 점검 (체크리스트)
2. 가장 시급한 개선 1가지 선택
3. 월요일 출근하면 바로 시작할 수 있게 준비

**이번 주:**
1. ADR 첫 3개 작성 (이미 내린 중요한 결정)
2. ArchUnit 의존성 추가 및 테스트 3개
3. Pre-commit hook 강화

**이번 달:**
1. docs/examples/ 폴더 생성 및 템플릿 5개
2. Custom Linter 규칙 3개
3. 주간 Garbage Collection 스크립트

**이직 준비:**
1. "Harness Engineering" 개념을 면접에서 어필할 수 있게 연습
2. beauty-sale에 적용한 사례를 구체적으로 설명할 수 있게
3. 회사 면접 시 "당신들의 Harness는?" 질문하기

---

**르네상스 개발자로 가는 길:**

```
기술력 = Kotlin + Spring + AI 도구 활용
제품 감각 = 사용자 공감 + 데이터 드리븐
교양 = Harness Engineering + 시스템 사고

이 세 가지가 만나는 지점 = 시니어 프로덕트 엔지니어
```

**AI 시대, 결국 살아남는 것은 "AI를 제어하는 사람"이다.** 🎯
