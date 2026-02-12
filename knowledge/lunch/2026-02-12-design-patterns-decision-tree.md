# Design Patterns Decision Tree

> Stop Memorizing Design Patterns: Use This Decision Tree Instead  
> 출처: https://medium.com/womenintechnology/stop-memorizing-design-patterns-use-this-decision-tree-instead-e84f22fca9fa  
> 날짜: 2026-02-12

---

## 💡 핵심 메시지

**디자인 패턴을 암기하지 말고, 실제 문제(pain point)를 기반으로 선택하라.**

---

## 🎯 핵심 통찰

### 디자인 패턴이 실패하는 이유

- **"잘못된 패턴"이 아니라 "잘못된 타이밍"**에 적용하기 때문
- 어려운 건 Strategy 패턴을 "아는 것"이 아니라, **지금 코드에 "필요한지 판단"**하는 것
- 패턴을 대안이 아닌 **진짜 문제를 명명하는 대체제**로 사용하는 경우

### Decision Tree의 역할

Decision Tree는 패턴 선택 전에 한 단계의 훈련을 강제한다:

> **"내가 제거하려는 마찰(friction)은 무엇인가?"**

이 질문에 먼저 답하면, 추측 시간은 줄고 의사결정 시간은 늘어난다.

---

## 🔍 3가지 핵심 질문

### 1️⃣ 객체 생성이 점점 복잡해지고 있나?

**→ Creational Patterns**

- **Factory Pattern**: 객체 생성 로직을 캡슐화
- **Builder Pattern**: 복잡한 객체를 단계적으로 생성
- **Singleton Pattern**: 인스턴스가 하나만 존재하도록 보장

**언제 사용?**
- 생성자 파라미터가 많아질 때
- 객체 생성 조건이 복잡할 때
- 생성 로직이 여러 곳에 중복될 때

---

### 2️⃣ 컴포넌트 간 경계나 외부 의존성과 싸우고 있나?

**→ Structural Patterns**

- **Adapter Pattern**: 호환되지 않는 인터페이스를 연결
- **Facade Pattern**: 복잡한 시스템에 간단한 인터페이스 제공
- **Proxy Pattern**: 실제 객체에 대한 대리자 제공

**언제 사용?**
- 외부 라이브러리 인터페이스가 맞지 않을 때
- 복잡한 서브시스템을 단순화하고 싶을 때
- 객체 접근을 제어하고 싶을 때

---

### 3️⃣ 동작이 계속 변경되고 조건문이 늘어나나?

**→ Behavioral Patterns**

- **Strategy Pattern**: 알고리즘을 캡슐화하고 교체 가능하게
- **State Pattern**: 상태에 따라 동작이 달라지는 객체
- **Command Pattern**: 요청을 객체로 캡슐화

**언제 사용?**
- if/else나 switch 문이 계속 늘어날 때
- 런타임에 동작을 변경해야 할 때
- 동작의 히스토리를 관리해야 할 때

---

## 🚀 실전 적용 가이드

### ✅ DO (해야 할 것)

1. **문제를 먼저 명확히 하라**
   ```
   "이 코드의 문제는 뭐지?"
   → 객체 생성이 복잡? 인터페이스 불일치? 조건문 과다?
   ```

2. **더 간단한 해결책을 먼저 고려하라**
   ```
   패턴 적용 전에 물어보기:
   "단순히 함수를 분리하면 안 될까?"
   "변수 하나 추가하면 해결되지 않을까?"
   ```

3. **판단 기준을 가져라**
   ```
   Decision Tree를 통해:
   - 추측 시간 ↓
   - 의사결정 시간 ↑
   ```

### ❌ DON'T (하지 말아야 할 것)

1. **패턴을 먼저 고르지 말라**
   ```
   ❌ "Strategy 패턴 써볼까?"
   ✅ "조건문이 너무 많은데, 어떻게 줄일까?"
   ```

2. **과도한 엔지니어링 주의**
   ```
   간단한 if문으로 충분한데 Strategy 패턴 적용 → 오버킬
   ```

3. **암기에 의존하지 말라**
   ```
   23개 GoF 패턴 외우기 < 3가지 질문으로 좁혀가기
   ```

---

## 💼 실무 예시: Shopify 앱 개발

### 상황: 진단 횟수 제한 로직

**요구사항:**
```javascript
Starter: 50회 제한
Pro: 200회 제한
Enterprise: 무제한
```

**Decision Tree 적용:**

**Q: 동작이 계속 변경되고 조건문이 늘어나나?**
- 현재: 3개 플랜 고정
- 미래: 플랜 추가 가능성? 제한 로직 복잡해질 가능성?

**A1: 고정이고 단순하다면 → 패턴 불필요**
```javascript
// 단순한 게 나음
if (plan === "Starter" && usage >= 50) {
  throw new Error("진단 횟수 초과");
}
if (plan === "Pro" && usage >= 200) {
  throw new Error("진단 횟수 초과");
}
```

**A2: 플랜이 자주 바뀌고 로직이 복잡해진다면 → Strategy**
```javascript
// Strategy 패턴
class StarterPlanStrategy {
  checkLimit(usage) {
    if (usage >= 50) throw new Error("진단 횟수 초과");
  }
}

class ProPlanStrategy {
  checkLimit(usage) {
    if (usage >= 200) throw new Error("진단 횟수 초과");
  }
}

const strategies = {
  starter: new StarterPlanStrategy(),
  pro: new ProPlanStrategy(),
};

strategies[plan].checkLimit(usage);
```

**결론:**  
현재는 단순 if문으로 시작 → 나중에 복잡해지면 리팩토링

---

## 📚 주요 패턴 Quick Reference

### Creational (생성)
| 패턴 | 한 줄 설명 | 언제 |
|------|-----------|------|
| Factory | 객체 생성 로직 캡슐화 | 생성 조건이 복잡할 때 |
| Builder | 단계적 객체 생성 | 생성자 파라미터가 많을 때 |
| Singleton | 인스턴스 하나만 | 전역 상태 관리 |

### Structural (구조)
| 패턴 | 한 줄 설명 | 언제 |
|------|-----------|------|
| Adapter | 인터페이스 변환 | 외부 API가 안 맞을 때 |
| Facade | 복잡한 시스템 단순화 | 서브시스템이 복잡할 때 |
| Proxy | 접근 제어 | 지연 로딩, 권한 체크 |

### Behavioral (행동)
| 패턴 | 한 줄 설명 | 언제 |
|------|-----------|------|
| Strategy | 알고리즘 교체 가능 | 조건문이 많을 때 |
| State | 상태별 동작 변경 | 상태 전이가 복잡할 때 |
| Command | 요청을 객체화 | Undo/Redo 필요할 때 |

---

## 🎓 핵심 원칙

> **"패턴은 도구다. 도구를 먼저 고르지 말고, 못을 먼저 찾아라."**

1. **문제 우선, 패턴은 나중에**
2. **단순함이 항상 승리한다**
3. **Decision Tree로 범위를 좁혀라**

---

## 🔗 더 읽어보기

- 원문: [Stop Memorizing Design Patterns](https://medium.com/womenintechnology/stop-memorizing-design-patterns-use-this-decision-tree-instead-e84f22fca9fa)
- GoF Design Patterns (Gang of Four)
- Head First Design Patterns
- Refactoring Guru: https://refactoring.guru/design-patterns

---

**저장 위치:**
- Notion: ✅ 2faee28e06ad80d885a5e20bd3e09f89
- GitHub: renaissance-dev/knowledge/lunch/
