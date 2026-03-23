# Knowledge Base - Personal OS

_사용자의 지식 체계_

Last updated: 2026-03-23

---

## 🗺️ 전체 구조

```
knowledge/
├── INDEX.md (이 파일)
├── projects/       # 현재 진행 중인 프로젝트
├── people/         # 함께 일하는 사람들
├── decisions/      # 중요한 결정 기록
├── learnings/      # 학습 기록
│   ├── daily/         # 일일 학습 (간단한 메모)
│   ├── topics/        # 주제별 심화 (레퍼런스)
│   └── retrospectives/ # 프로젝트 회고
├── guides/         # 기술 가이드 (기존)
├── lunch/          # 점심 인사이트 (자동 큐레이션)
├── weekly/         # 주간 큐레이션
└── sunday/         # 주말 정리
```

---

## 📋 Quick Links

### 📂 프로젝트
- [Beauty-Sale](projects/beauty-sale.md) - K-뷰티 가격 비교 앱

### 👥 사람
- [팀원 정보](people/team.md)

### 🔐 결정
- [결정 기록 README](decisions/README.md)

### 📚 학습
- [학습 기록 README](learnings/README.md)
- [일일 학습](learnings/daily/)
- [주제별 심화](learnings/topics/)
- [프로젝트 회고](learnings/retrospectives/)

### 🛠️ 가이드 (기존)
- [LeetCode Roadmap](guides/leetcode-roadmap.md)

---

## 🎯 Personal OS 활용법

### 매일 (5-10분)
```bash
# 아침: 오늘 할 일 확인
"오늘 할 일 정리해줘" (HEARTBEAT.md 기반)

# 저녁: 오늘 배운 것 기록
"오늘 배운 것 learnings/daily/YYYY-MM-DD.md에 정리해줘"
```

### 매주 (30분)
```bash
# 일요일: 주간 회고
"이번 주 회고 작성해줘"
→ learnings/retrospectives/YYYY-WW-weekly.md

# 프로젝트 현황 업데이트
→ projects/beauty-sale.md
```

### 필요할 때
```bash
# 중요한 결정을 내렸을 때
"오늘 결정한 [주제] decisions/에 기록해줘"

# 깊게 공부한 주제
"Spring AOP 정리본 topics/spring-aop.md로 만들어줘"

# 회의 후
"오늘 회의 내용 projects/beauty-sale.md에 추가해줘"
```

---

## 🚀 시작 가이드

### 1단계: 기본 구조 (완료! ✅)
- [x] projects/ 생성
- [x] people/ 생성
- [x] decisions/ 생성
- [x] learnings/ 생성

### 2단계: 내용 채우기 (지금부터!)
- [ ] beauty-sale.md에 현재 상황 업데이트
- [ ] team.md에 팀원 정보 추가
- [ ] 오늘부터 daily 학습 기록 시작

### 3단계: 습관화 (1-2주)
- [ ] 매일 저녁 학습 기록
- [ ] 주말 회고 루틴
- [ ] 중요한 결정 즉시 기록

---

## 💡 활용 팁

### 빠른 검색
```bash
# 키워드로 전체 검색
grep -r "Spring" knowledge/

# 최근 학습 내용
ls -lt knowledge/learnings/daily/
```

### AI 활용
```bash
# 과거 컨텍스트 로드
"knowledge/projects/beauty-sale.md 읽고 현재 상황 요약해줘"

# 자동 정리
"지난주 learnings/ 내용 요약해줘"
"이번 달 주요 결정 리스트업해줘"
```

### Git 동기화
```bash
# 주기적으로 커밋 (중요한 기록은 백업!)
git add knowledge/
git commit -m "Update personal OS: [내용]"
git push
```

---

## 🎓 철학

> "Your brain is for having ideas, not holding them." - David Allen (GTD)

Personal OS의 목표:
1. **기억 외주화** - 중요한 것은 파일에, 뇌는 생각에 집중
2. **성장 추적** - 6개월 후 "내가 얼마나 성장했나?" 확인 가능
3. **컨텍스트 누적** - AI가 사용자을 점점 더 잘 이해
4. **미래 투자** - 오늘의 기록이 미래의 자산

---

## 📊 현재 상태

### 학습 중
- 코딩테스트: LeetCode 패턴 마스터
- AI: Mini Project 실습 (화요일)
- Growth: GA4 + AARRR (목요일)
- Spring/JPA/Kotlin/JVM: 주차별 로테이션 (금요일)

### 프로젝트
- Beauty-Sale: MVP 개발 중

### 이직 준비
- 타임라인: ~2027년 초
- 목표: 시니어 백엔드 (Kotlin/Spring)

---

_Personal OS는 살아있는 시스템입니다. 지속적으로 개선하고 진화시키세요!_
