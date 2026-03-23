# Personal Decisions 사용 가이드

_공식 ADR vs Personal Decisions의 차이점_

---

## 🎯 언제 어디에 기록할까?

### ✅ Git 레포 ADR (공식)

**사용 케이스:**
- beauty-sale 프로젝트 아키텍처 결정
- 팀과 공유해야 하는 기술 선택
- 코드 리뷰 후 확정된 결정
- 프로덕션 영향 주는 변경

**예시:**
- "쿠폰 계산 로직 재설계" (ADR-023)
- "상품 병합 전략" (ADR-001)
- "가격 비교 가이드 기준" (ADR-022)

**작성 장소:**
```bash
/Users/dev_heo/Code/beauty-sale/docs/decisions/
```

---

### ✅ Personal OS Decisions (개인)

**사용 케이스:**
- 아직 확정 안 된 기술 실험
- 개인 학습 프로젝트 선택
- 사이드 프로젝트 기술 스택
- 커리어 관련 결정 (이직, 학습 방향)
- "왜 이걸 시도했지?" 개인 컨텍스트

**예시:**
- "Redis vs Memcached 실험 중" (확정 전)
- "사이드 프로젝트에 NestJS 써볼까?"
- "개인 블로그 Astro vs Next.js"
- "이직 준비 학습 우선순위 결정"

**작성 장소:**
```bash
~/.openclaw/workspace/knowledge/decisions/
```

---

## 📝 워크플로우 예시

### Scenario 1: beauty-sale 기술 결정

```
1. 고민 시작: "캐시 전략 어떻게 할까?"
   → knowledge/decisions/2026-03-23-고민중-캐시-전략.md
   
2. 조사 & 실험
   - Redis, Memcached, 인메모리 캐시 비교
   - 벤치마크, 트레이드오프 분석
   - Personal OS에 자유롭게 메모
   
3. 결정 확정: "Redis로 간다"
   → /Code/beauty-sale/docs/decisions/025-cache-strategy-redis.md
   
4. Personal OS 메모 업데이트
   → "✅ ADR-025로 공식화" 링크 추가
```

### Scenario 2: 개인 프로젝트 결정

```
1. 사이드 프로젝트 시작: "개인 블로그 만들기"
   → knowledge/decisions/2026-03-25-블로그-기술-스택.md
   
2. 기술 선택 과정 기록
   - Astro vs Next.js vs Gatsby
   - 호스팅: Vercel vs Cloudflare Pages
   - CMS: Contentful vs Notion API
   
3. 결정 & 이유 기록
   → 나중에 "왜 이렇게 했지?" 회고 시 참고
```

### Scenario 3: 커리어 결정

```
knowledge/decisions/2026-02-05-이직-준비-우선순위.md

# 이직 준비 학습 우선순위 결정

Date: 2026-02-05

## Context
- 1년 타임라인 (~2027년 초)
- 시니어 백엔드 목표 (Kotlin/Spring)
- 시간 제한: 주중 2시간, 주말

## Decision
1. 코딩테스트 35% (이직 관문)
2. Spring/JPA/Kotlin/JVM 25% (면접 핵심)
3. 시스템 디자인 20% (시니어 필수)
4. AI 10% + Growth 10% (차별화)

## Consequences
- 코테 우선 → Easy 95%, Medium 70-80%
- 주차별 로테이션으로 균형
- AI 에이전트 팀으로 자동화
```

---

## 🔍 결정 추적 팁

### Git 레포 ADR 확인
```bash
# 최신 ADR 보기
ls -lt /Users/dev_heo/Code/beauty-sale/docs/decisions/ | head -10

# 특정 키워드 검색
grep -r "Redis" /Users/dev_heo/Code/beauty-sale/docs/decisions/
```

### Personal Decisions 확인
```bash
# 최근 개인 결정
ls -lt knowledge/decisions/ | grep -v README

# AI에게 요약 요청
"knowledge/decisions/ 최근 결정 요약해줘"
```

---

## 💡 Quick Tips

1. **고민 중이면 Personal에 메모** → 나중에 Git ADR로 승격
2. **팀과 공유 필요하면 즉시 Git ADR**
3. **개인 프로젝트는 무조건 Personal**
4. **커리어 결정도 기록** (미래의 나에게 도움)

---

_두 시스템을 활용해서 "왜 이렇게 했지?"를 절대 잊지 말자!_
