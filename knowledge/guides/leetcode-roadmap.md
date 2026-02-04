# 🧩 LeetCode 코딩테스트 로드맵

> **목표:** 12주 안에 코테 기초 완성
> **방식:** 패턴 기반 학습, Easy → Medium 점진적 상승

---

## 📚 학습법

### 1. 패턴 먼저, 문제는 나중에

```
❌ 문제 보고 바로 풀기 시도 → 막히면 좌절

✅ 올바른 순서:
1. 패턴 개념 학습 (15분) - YouTube 영상 or 블로그
2. 패턴 적용 Easy 문제 풀기
3. 15-20분 고민
4. 못 풀면 해설 보기 (OK! 배우는 단계!)
5. 해설 이해 후 직접 구현
6. 3일 후 복습
```

### 2. 시간 제한 두기

| 난이도 | 고민 시간 | 해설 봐도 OK |
|--------|----------|-------------|
| Easy | 15-20분 | ✅ |
| Medium | 25-30분 | ✅ |
| Hard | 40-45분 | ✅ |

**핵심:** 시간 넘으면 해설 보고 배우기. 무한 고민 X.

### 3. 복습이 진짜 실력

```
Day 1: 문제 풀기 (해설 봐도 OK)
Day 3: 같은 문제 다시 풀기
Day 7: 한 번 더 복습
```

Anki 또는 노션에 틀린 문제 기록!

---

## 🗺️ 12주 로드맵

### Phase 1: 기초 패턴 (1-4주)

#### Week 1-2: Array & Hashing ⭐

**개념:**
- HashMap으로 O(n) 탐색
- Set으로 중복 제거
- 빈도수 세기

**필수 문제 (Easy):**
| # | 문제 | 핵심 |
|---|------|------|
| 1 | [Two Sum](https://leetcode.com/problems/two-sum/) | HashMap 기초 |
| 217 | [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) | Set 사용 |
| 242 | [Valid Anagram](https://leetcode.com/problems/valid-anagram/) | 빈도수 비교 |
| 49 | [Group Anagrams](https://leetcode.com/problems/group-anagrams/) | HashMap + 정렬 |
| 347 | [Top K Frequent](https://leetcode.com/problems/top-k-frequent-elements/) | 빈도수 + 정렬 |

**학습 자료:**
- [NeetCode - Arrays & Hashing](https://www.youtube.com/watch?v=KLlXCFG5TnA)

---

#### Week 3: Two Pointers ⭐

**개념:**
- 정렬된 배열에서 양쪽에서 좁히기
- O(n) 시간복잡도 달성

**필수 문제:**
| # | 문제 | 핵심 |
|---|------|------|
| 125 | [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) | 양쪽에서 비교 |
| 167 | [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | 정렬된 배열 |
| 15 | [3Sum](https://leetcode.com/problems/3sum/) | Two Sum 응용 |
| 11 | [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) | 최대값 찾기 |

---

#### Week 4: Sliding Window ⭐⭐

**개념:**
- 연속된 구간(window) 유지
- 구간 합, 최대/최소 찾기

**필수 문제:**
| # | 문제 | 핵심 |
|---|------|------|
| 121 | [Best Time to Buy/Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | 최소값 추적 |
| 3 | [Longest Substring Without Repeat](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Set + Window |
| 424 | [Longest Repeating Character](https://leetcode.com/problems/longest-repeating-character-replacement/) | 빈도수 + Window |
| 76 | [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | Hard, 나중에 |

---

### Phase 2: 자료구조 (5-8주)

#### Week 5: Stack ⭐

**개념:**
- LIFO (Last In First Out)
- 괄호 매칭, 모노토닉 스택

**필수 문제:**
| # | 문제 | 핵심 |
|---|------|------|
| 20 | [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/) | 스택 기초 |
| 155 | [Min Stack](https://leetcode.com/problems/min-stack/) | 보조 스택 |
| 150 | [Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/) | 계산기 |
| 739 | [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/) | 모노토닉 스택 |

---

#### Week 6: Binary Search ⭐⭐

**개념:**
- 정렬된 배열에서 O(log n) 탐색
- left, right, mid 포인터

**필수 문제:**
| # | 문제 | 핵심 |
|---|------|------|
| 704 | [Binary Search](https://leetcode.com/problems/binary-search/) | 기초 |
| 35 | [Search Insert Position](https://leetcode.com/problems/search-insert-position/) | lower bound |
| 74 | [Search 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/) | 2차원 |
| 33 | [Search in Rotated Array](https://leetcode.com/problems/search-in-rotated-sorted-array/) | 응용 |

---

#### Week 7-8: Trees ⭐⭐

**개념:**
- BFS (레벨 순회) - Queue 사용
- DFS (전위/중위/후위) - 재귀 또는 Stack
- Binary Search Tree 특성

**필수 문제:**
| # | 문제 | 핵심 |
|---|------|------|
| 226 | [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/) | DFS 기초 |
| 104 | [Maximum Depth](https://leetcode.com/problems/maximum-depth-of-binary-tree/) | 재귀 |
| 100 | [Same Tree](https://leetcode.com/problems/same-tree/) | 비교 |
| 102 | [Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) | BFS |
| 98 | [Validate BST](https://leetcode.com/problems/validate-binary-search-tree/) | BST 특성 |
| 230 | [Kth Smallest in BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) | 중위 순회 |

---

### Phase 3: 그래프 & DP (9-12주)

#### Week 9-10: Graphs ⭐⭐⭐

**개념:**
- 인접 리스트 표현
- BFS (최단 거리)
- DFS (연결 요소, 사이클)

**필수 문제:**
| # | 문제 | 핵심 |
|---|------|------|
| 200 | [Number of Islands](https://leetcode.com/problems/number-of-islands/) | DFS/BFS 기초 |
| 133 | [Clone Graph](https://leetcode.com/problems/clone-graph/) | HashMap + DFS |
| 695 | [Max Area of Island](https://leetcode.com/problems/max-area-of-island/) | DFS |
| 207 | [Course Schedule](https://leetcode.com/problems/course-schedule/) | 위상 정렬 |

---

#### Week 11-12: Dynamic Programming ⭐⭐⭐

**개념:**
- 점화식 세우기
- 메모이제이션
- Bottom-up vs Top-down

**필수 문제:**
| # | 문제 | 핵심 |
|---|------|------|
| 70 | [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) | 피보나치 |
| 198 | [House Robber](https://leetcode.com/problems/house-robber/) | 1D DP |
| 322 | [Coin Change](https://leetcode.com/problems/coin-change/) | 완전 탐색 → DP |
| 300 | [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) | LIS |
| 62 | [Unique Paths](https://leetcode.com/problems/unique-paths/) | 2D DP |

---

## 📊 진행 상황 추적

```markdown
## Week X: [패턴명]

### 학습
- [ ] 개념 영상 시청
- [ ] 블로그 정리 읽기

### 문제
- [ ] 문제1: ✅ 혼자 품 / ⚠️ 힌트 봄 / ❌ 해설 봄
- [ ] 문제2: 
- [ ] 문제3:
- [ ] 문제4:

### 복습
- [ ] Day 3 복습
- [ ] Day 7 복습
```

---

## 🔗 추천 자료

**영상:**
- [NeetCode 로드맵](https://neetcode.io/roadmap) - 체계적인 순서
- [NeetCode YouTube](https://www.youtube.com/@NeetCode) - 문제별 해설

**연습:**
- [LeetCode](https://leetcode.com) - 문제 풀기
- [NeetCode 150](https://neetcode.io/practice) - 필수 150문제

**언어별 치트시트:**
- Kotlin: Collections, Sequences, sorted(), groupBy()

---

## ✅ 완료 기준

```
□ Array & Hashing 필수 문제 완료
□ Two Pointers 필수 문제 완료
□ Sliding Window 필수 문제 완료
□ Stack 필수 문제 완료
□ Binary Search 필수 문제 완료
□ Trees 필수 문제 완료
□ Graphs 필수 문제 완료
□ DP 필수 문제 완료
□ Medium 문제 30개 이상 풀이
```

---

*12주 코테 로드맵 - 2026-02-05*
*작성: 세이버 ⚔️*
