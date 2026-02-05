# 🌅 데일리 커리어 가이드 - 2026-02-05 (목요일)

**Week 6/52**

---

## 📝 오늘의 코딩테스트 패턴: Binary Search

### 🎯 핵심 개념
- **자료구조**: 정렬된 배열
- **시간복잡도 목표**: O(log n)
- **공간복잡도**: O(1) (반복) / O(log n) (재귀)

### 💡 꿀팁 & 주의사항
1. **배열이 정렬되어 있어야 함** - 안 되어있으면 먼저 정렬 (O(n log n))
2. **mid 계산 시 오버플로우 주의** - `mid = left + (right - left) / 2` 사용
3. **종료 조건 명확히** - `left <= right` vs `left < right` 구분
4. **경계 조건 체크** - 찾는 값이 없을 때 어떻게 처리할지
5. **변형 문제 많음** - rotated array, peak element, first/last position 등

### 📚 대표 문제
- **LeetCode 704**: Binary Search (기본)
- **LeetCode 35**: Search Insert Position (삽입 위치)
- **LeetCode 33**: Search in Rotated Sorted Array (회전 배열)

---

## ✅ 오늘 할 일

### 📖 아침 08:00
- ☐ 소프트웨어 다이제스트 (3-5개, 10분)
- ☐ 오늘의 딥다이브 미리보기 (Growth 실무)

### 🍽️ 점심 12:00
- ☐ 교양 다이제스트 (경제/문학/비즈니스)

### 🌆 저녁 21:00 [핵심 학습 시간!]
- ☐ **Growth 실무 딥다이브 (1시간)**
  - GA4, AARRR, 제품 성장 전략
  - 팀에 공유할 수 있게 정리
- ☐ **코딩테스트 (1시간)**
  - Binary Search 패턴 연습
  - LeetCode Easy 1-2문제

---

## 📰 오늘의 기술 뉴스 (3개)

### 1️⃣ Microsoft and Software Survival
**요약**: AI 시대, 코드 작성 비용이 0에 수렴하면서 SaaS 생태계가 전쟁터로 변한다. 모델 제작자들이 무기상으로 부상.

**링크**: https://stratechery.com/2026/microsoft-and-software-survival/

### 2️⃣ "Anyone can cook": v0 + Git workflow
**요약**: Vercel CEO가 시연하는 v0의 진화. 비개발자도 브랜치 생성 → 코드 작성 → PR 제출까지 가능한 시대.

**링크**: https://www.lennysnewsletter.com/p/anyone-can-cook-how-v0-is-bringing

### 3️⃣ Apple Earnings, Supply Chain Speculation
**요약**: Apple 실적 발표 분석 및 공급망 전략. 중국 vs 인도 제조 변화와 산업 디자인의 미래.

**링크**: https://stratechery.com/2026/apple-earnings-supply-chain-speculation-china-and-industrial-design/

---

## 💡 오늘의 팁
"Binary Search는 절반씩 버리는 마법입니다. 100만 개 배열도 20번만에 찾습니다. log₂(1,000,000) ≈ 20"

## 🔥 동기부여
목요일은 Growth 실무의 날! 오늘 배운 GA4와 AARRR 프레임워크는 제품을 성장시키는 강력한 무기가 됩니다. 측정하지 않으면 개선할 수 없고, 개선하지 않으면 성장할 수 없습니다.

코딩테스트에서 Binary Search는 "효율"의 상징입니다. O(n)을 O(log n)으로 줄이는 것, 그게 시니어와 주니어의 차이입니다. 오늘 저녁 2시간, 미래의 나를 위한 최고의 투자입니다! 💪

---

_Generated: 2026-02-05 19:21 KST_
