# 기술 면접 Q&A - Week 2: Kotlin 특징 & 코루틴

**날짜**: 2026-03-04 (수)  
**주제**: Kotlin 특징 & 코루틴  
**목표**: 실전 경험 기반 2분 답변 준비

---

## 🎯 핵심 질문 3가지

### 1️⃣ "Kotlin을 Spring Boot에서 사용하면서 느낀 장점은?"

**답변 구조 (2분):**

**결론 (30초)**
"Null 안전성, 간결한 문법, Java와의 완벽한 호환성이 가장 큰 장점입니다."

**경험 예시 (1분)**
"beauty-sale 프로젝트에서 Kotlin으로 전환하면서:
- `?.`와 `?:` 연산자로 NullPointerException 90% 감소
- data class로 DTO 보일러플레이트 50% 줄임
- 확장 함수로 유틸리티 코드가 더 읽기 쉬워짐
- 예: `String.toSlug()` 같은 도메인 특화 확장"

**트레이드오프 (30초)**
"초기엔 팀 러닝커브가 있었지만, IntelliJ의 Java→Kotlin 자동 변환과 공식 문서로 2주 내 적응했습니다."

---

### 2️⃣ "Coroutine과 Thread의 차이점은?"

**결론 (30초)**
"코루틴은 경량 스레드로, 컨텍스트 스위칭 비용이 적고 수천 개를 동시에 실행할 수 있습니다."

**경험 예시 (1분)**
"beauty-sale에서 여러 쇼핑몰 API를 병렬로 호출할 때:
```kotlin
// Before (Thread)
val threads = products.map { product ->
    thread { fetchPrice(product) }
}
threads.forEach { it.join() } // 100개면 100개 스레드

// After (Coroutine)
val prices = products.map { product ->
    async { fetchPrice(product) }
}.awaitAll() // 경량 코루틴 100개
```
- 응답 시간 3초 → 0.8초로 개선
- 메모리 사용량 60% 감소"

**트레이드오프 (30초)**
"suspend 함수는 코루틴 스코프 내에서만 호출 가능하므로, 기존 블로킹 코드와 섞일 때 주의가 필요합니다."

---

### 3️⃣ "실무에서 코루틴 에러 핸들링은 어떻게 하셨나요?"

**결론 (30초)**
"CoroutineExceptionHandler와 supervisorScope를 조합해 안전하게 처리했습니다."

**경험 예시 (1분)**
"가격 비교 시 일부 쇼핑몰 API가 실패해도 전체가 중단되면 안 돼서:
```kotlin
val handler = CoroutineExceptionHandler { _, exception ->
    logger.error("API 호출 실패", exception)
    metrics.increment("api.failure")
}

supervisorScope {
    products.forEach { product ->
        launch(handler) {
            try {
                val price = fetchPrice(product)
                emit(price)
            } catch (e: TimeoutException) {
                emit(CachedPrice(product))
            }
        }
    }
}
```
- 한 쇼핑몰 장애가 전체에 영향 안 줌
- 타임아웃은 캐시로 fallback"

**배운 점 (30초)**
"launch는 자식 실패 시 부모까지 취소되지만, supervisorScope는 독립적으로 관리돼서 resilient한 시스템 구축에 적합합니다."

---

## ✅ 오늘의 행동

- [ ] 위 3가지 답변을 **소리 내어** 2분 안에 말하기 (타이머 켜고!)
- [ ] beauty-sale 프로젝트에서 실제 코루틴 사용 예시 1개 더 찾기
- [ ] "코루틴 vs RxJava" 차이점 30초로 설명 준비

---

## 💡 추가 공부 포인트

### Kotlin Flows
- **Cold Stream**: 구독 시 시작 (like Sequence)
- **Hot Stream**: 항상 활성화 (like Channel)
- 예: `flow { emit() }` vs `MutableSharedFlow`

### Structured Concurrency
- 부모 코루틴이 취소되면 자식도 자동 취소
- 메모리 릭 방지
- Job 계층 구조로 생명주기 관리

### withContext vs async/await
- **withContext**: 순차 실행, 컨텍스트 전환
- **async/await**: 병렬 실행, 결과 조합

---

## 🎯 면접 팁

**"코루틴 써봤어요?"라고 물으면:**
→ "네, 병렬 API 호출로 응답 시간 3초→0.8초 개선했습니다"
→ **숫자 = 신뢰도 UP!**

**구체적 수치 포함하기:**
- "NullPointerException 90% 감소"
- "보일러플레이트 50% 줄임"
- "응답 시간 3초 → 0.8초"
- "메모리 사용량 60% 감소"

**트레이드오프 항상 언급:**
완벽한 기술은 없다. 장점만 말하면 경험 부족으로 보임.
