# 🧩 LeetCode 코딩테스트 로드맵

> **목표:** 12주 안에 코테 기초 완성
> **방식:** 패턴 기반 학습, Easy → Medium 점진적 상승

---

## 📚 학습법

### 1. 3단계 학습 시스템

```
✅ 올바른 순서:

Week 1 (패턴 이해):
1. 패턴 가이드 읽기 (15분) - pattern-XX.md 파일
2. 필수 문제 5개 풀기 (각 20분, 해설 봐도 OK!)
3. 패턴 완전 이해

Week 2 (숙달):
4. 추천 문제 10개 풀기 (Easy 마스터)
5. 3일 후 복습

Week 3+ (심화, 시간 있으면):
6. Medium 도전 3-5개
7. 패턴 조합 문제
```

### 2. 시간 제한 두기

| 난이도 | 고민 시간 | 해설 봐도 OK |
|--------|----------|-------------|
| Easy | 15-20분 | ✅ 배우는 단계! |
| Medium | 25-30분 | ✅ 배우는 단계! |
| Hard | 40-45분 | ✅ 배우는 단계! |

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

**📖 학습 가이드:** `pattern-01-array-hashing.md`

**개념:**
- HashMap으로 O(n²) → O(n) 개선
- Set으로 중복 제거
- 빈도수 세기 패턴

**✅ 필수 문제 (Easy, 5개) - 패턴 이해용**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 1 | [Two Sum](https://leetcode.com/problems/two-sum/) | Easy | HashMap 기초 |
| 217 | [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) | Easy | Set 사용 |
| 242 | [Valid Anagram](https://leetcode.com/problems/valid-anagram/) | Easy | 빈도수 비교 |
| 49 | [Group Anagrams](https://leetcode.com/problems/group-anagrams/) | Medium | HashMap + 정렬 |
| 347 | [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) | Medium | 빈도수 + 정렬 |

**📝 추천 문제 (Easy, 10개) - 숙달용**
| # | 문제 | 설명 |
|---|------|------|
| 383 | [Ransom Note](https://leetcode.com/problems/ransom-note/) | 빈도수 체크 |
| 205 | [Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/) | 문자 매핑 |
| 290 | [Word Pattern](https://leetcode.com/problems/word-pattern/) | 패턴 매칭 |
| 349 | [Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) | Set 활용 |
| 350 | [Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/) | HashMap 활용 |
| 387 | [First Unique Character](https://leetcode.com/problems/first-unique-character-in-a-string/) | 빈도수 + 순서 |
| 169 | [Majority Element](https://leetcode.com/problems/majority-element/) | HashMap 응용 |
| 268 | [Missing Number](https://leetcode.com/problems/missing-number/) | Set 응용 |
| 448 | [Find All Numbers Disappeared](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/) | 인덱스 활용 |
| 442 | [Find All Duplicates](https://leetcode.com/problems/find-all-duplicates-in-an-array/) | 중복 찾기 |

**🔥 Medium 도전 (3개) - 심화**
| # | 문제 | 설명 |
|---|------|------|
| 271 | [Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings/) | 문자열 인코딩 |
| 128 | [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/) | O(n) 알고리즘 |
| 36 | [Valid Sudoku](https://leetcode.com/problems/valid-sudoku/) | HashMap 응용 |

**학습 자료:**
- [NeetCode - Arrays & Hashing Playlist](https://www.youtube.com/playlist?list=PLot-Xpze53leOBgcVsJBEGrHPd_7x_koV)

---

#### Week 3: Two Pointers ⭐

**개념:**
- 정렬된 배열에서 양쪽에서 좁히기
- O(n) 시간복잡도

**✅ 필수 문제 (Easy, 5개)**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 125 | [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) | Easy | 양쪽 비교 |
| 167 | [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | Medium | 정렬된 배열 |
| 344 | [Reverse String](https://leetcode.com/problems/reverse-string/) | Easy | 기초 |
| 283 | [Move Zeroes](https://leetcode.com/problems/move-zeroes/) | Easy | 포인터 이동 |
| 977 | [Squares of Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/) | Easy | 두 포인터 |

**📝 추천 문제 (10개)**
| # | 문제 | 설명 |
|---|------|------|
| 26 | [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) | In-place 수정 |
| 27 | [Remove Element](https://leetcode.com/problems/remove-element/) | In-place 삭제 |
| 80 | [Remove Duplicates II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/) | 중복 2개까지 |
| 88 | [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) | 두 배열 병합 |
| 345 | [Reverse Vowels](https://leetcode.com/problems/reverse-vowels-of-a-string/) | 조건부 swap |
| 455 | [Assign Cookies](https://leetcode.com/problems/assign-cookies/) | 탐욕 알고리즘 |
| 524 | [Longest Word in Dictionary](https://leetcode.com/problems/longest-word-in-dictionary-through-deleting/) | 부분 수열 |
| 844 | [Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/) | 문자 제거 |
| 925 | [Long Pressed Name](https://leetcode.com/problems/long-pressed-name/) | 문자 비교 |
| 986 | [Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/) | 구간 교집합 |

**🔥 Medium 도전 (3개)**
| # | 문제 | 설명 |
|---|------|------|
| 15 | [3Sum](https://leetcode.com/problems/3sum/) | Two Sum 응용 |
| 11 | [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) | 최대값 찾기 |
| 16 | [3Sum Closest](https://leetcode.com/problems/3sum-closest/) | 근사값 |

---

#### Week 4: Sliding Window ⭐⭐

**개념:**
- 연속된 구간(window) 유지
- 구간 합, 최대/최소 찾기

**✅ 필수 문제 (5개)**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 121 | [Best Time to Buy/Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | Easy | 최소값 추적 |
| 643 | [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/) | Easy | 고정 윈도우 |
| 3 | [Longest Substring Without Repeat](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Medium | Set + Window |
| 424 | [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) | Medium | 빈도수 + Window |
| 567 | [Permutation in String](https://leetcode.com/problems/permutation-in-string/) | Medium | 빈도수 비교 |

**📝 추천 문제 (10개)**
| # | 문제 | 설명 |
|---|------|------|
| 209 | [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) | 가변 윈도우 |
| 219 | [Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/) | 거리 제한 |
| 1004 | [Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/) | 0 뒤집기 |
| 1456 | [Maximum Vowels](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | 고정 윈도우 |
| 1208 | [Get Equal Substrings](https://leetcode.com/problems/get-equal-substrings-within-budget/) | 비용 제한 |
| 904 | [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) | 2개 종류 |
| 1052 | [Grumpy Bookstore Owner](https://leetcode.com/problems/grumpy-bookstore-owner/) | 최대화 |
| 438 | [Find All Anagrams](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | Anagram 찾기 |
| 1438 | [Longest Continuous Subarray](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) | 차이 제한 |
| 992 | [Subarrays with K Different](https://leetcode.com/problems/subarrays-with-k-different-integers/) | Hard |

**🔥 Medium 도전 (3개)**
| # | 문제 | 설명 |
|---|------|------|
| 76 | [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | Hard |
| 239 | [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | Deque |
| 30 | [Substring with Concatenation](https://leetcode.com/problems/substring-with-concatenation-of-all-words/) | 복잡한 매칭 |

---

### Phase 2: 자료구조 (5-8주)

#### Week 5: Stack ⭐

**개념:**
- LIFO (Last In First Out)
- 괄호 매칭, 모노토닉 스택

**✅ 필수 문제 (5개)**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 20 | [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/) | Easy | 스택 기초 |
| 155 | [Min Stack](https://leetcode.com/problems/min-stack/) | Medium | 보조 스택 |
| 150 | [Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/) | Medium | 계산기 |
| 739 | [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/) | Medium | 모노토닉 스택 |
| 844 | [Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/) | Easy | 문자 제거 |

**📝 추천 문제 (10개)**
| # | 문제 | 설명 |
|---|------|------|
| 232 | [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/) | 자료구조 변환 |
| 225 | [Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/) | 자료구조 변환 |
| 496 | [Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/) | 모노토닉 |
| 503 | [Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/) | 순환 배열 |
| 682 | [Baseball Game](https://leetcode.com/problems/baseball-game/) | 스택 응용 |
| 1047 | [Remove All Adjacent Duplicates](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/) | 중복 제거 |
| 71 | [Simplify Path](https://leetcode.com/problems/simplify-path/) | 경로 파싱 |
| 394 | [Decode String](https://leetcode.com/problems/decode-string/) | 중첩 구조 |
| 402 | [Remove K Digits](https://leetcode.com/problems/remove-k-digits/) | 모노토닉 |
| 456 | [132 Pattern](https://leetcode.com/problems/132-pattern/) | 패턴 찾기 |

**🔥 Medium 도전 (3개)**
| # | 문제 | 설명 |
|---|------|------|
| 84 | [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/) | Hard |
| 85 | [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/) | Hard |
| 316 | [Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/) | 모노토닉 |

---

#### Week 6: Binary Search ⭐⭐

**개념:**
- 정렬된 배열에서 O(log n) 탐색
- left, right, mid 포인터

**✅ 필수 문제 (5개)**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 704 | [Binary Search](https://leetcode.com/problems/binary-search/) | Easy | 기초 |
| 35 | [Search Insert Position](https://leetcode.com/problems/search-insert-position/) | Easy | lower bound |
| 69 | [Sqrt(x)](https://leetcode.com/problems/sqrtx/) | Easy | 수학 응용 |
| 74 | [Search 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/) | Medium | 2차원 |
| 33 | [Search in Rotated Array](https://leetcode.com/problems/search-in-rotated-sorted-array/) | Medium | 응용 |

**📝 추천 문제 (10개)**
| # | 문제 | 설명 |
|---|------|------|
| 278 | [First Bad Version](https://leetcode.com/problems/first-bad-version/) | API 호출 |
| 374 | [Guess Number](https://leetcode.com/problems/guess-number-higher-or-lower/) | 기초 |
| 367 | [Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/) | 수학 |
| 441 | [Arranging Coins](https://leetcode.com/problems/arranging-coins/) | 수학 응용 |
| 153 | [Find Minimum in Rotated Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) | 회전 배열 |
| 162 | [Find Peak Element](https://leetcode.com/problems/find-peak-element/) | Peak 찾기 |
| 540 | [Single Element](https://leetcode.com/problems/single-element-in-a-sorted-array/) | XOR 응용 |
| 658 | [Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/) | K개 찾기 |
| 1011 | [Capacity To Ship Packages](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) | 최솟값 찾기 |
| 875 | [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) | 속도 찾기 |

**🔥 Medium 도전 (3개)**
| # | 문제 | 설명 |
|---|------|------|
| 34 | [Find First and Last Position](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | 범위 찾기 |
| 81 | [Search in Rotated Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/) | 중복 있음 |
| 4 | [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Hard |

---

#### Week 7-8: Trees ⭐⭐

**개념:**
- BFS (레벨 순회) - Queue
- DFS (전위/중위/후위) - 재귀/Stack
- BST 특성

**✅ 필수 문제 (6개)**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 226 | [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/) | Easy | DFS 기초 |
| 104 | [Maximum Depth](https://leetcode.com/problems/maximum-depth-of-binary-tree/) | Easy | 재귀 |
| 100 | [Same Tree](https://leetcode.com/problems/same-tree/) | Easy | 비교 |
| 572 | [Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/) | Easy | DFS 응용 |
| 102 | [Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) | Medium | BFS |
| 98 | [Validate BST](https://leetcode.com/problems/validate-binary-search-tree/) | Medium | BST 특성 |

**📝 추천 문제 (10개)**
| # | 문제 | 설명 |
|---|------|------|
| 101 | [Symmetric Tree](https://leetcode.com/problems/symmetric-tree/) | 대칭 |
| 108 | [Convert Sorted Array to BST](https://leetcode.com/problems/convert-sorted-array-to-bst/) | 배열→트리 |
| 110 | [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/) | 균형 체크 |
| 111 | [Minimum Depth](https://leetcode.com/problems/minimum-depth-of-binary-tree/) | BFS |
| 112 | [Path Sum](https://leetcode.com/problems/path-sum/) | 경로 합 |
| 94 | [Binary Tree Inorder](https://leetcode.com/problems/binary-tree-inorder-traversal/) | 중위 순회 |
| 144 | [Binary Tree Preorder](https://leetcode.com/problems/binary-tree-preorder-traversal/) | 전위 순회 |
| 145 | [Binary Tree Postorder](https://leetcode.com/problems/binary-tree-postorder-traversal/) | 후위 순회 |
| 543 | [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/) | 직경 |
| 617 | [Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/) | 트리 병합 |

**🔥 Medium 도전 (5개)**
| # | 문제 | 설명 |
|---|------|------|
| 230 | [Kth Smallest in BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) | 중위 순회 |
| 105 | [Construct Tree from Preorder and Inorder](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | 트리 재구성 |
| 236 | [Lowest Common Ancestor](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) | LCA |
| 297 | [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | Hard |
| 124 | [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/) | Hard |

---

### Phase 3: 그래프 & DP (9-12주)

#### Week 9-10: Graphs ⭐⭐⭐

**개념:**
- 인접 리스트 표현
- BFS (최단 거리)
- DFS (연결 요소)

**✅ 필수 문제 (5개)**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 200 | [Number of Islands](https://leetcode.com/problems/number-of-islands/) | Medium | DFS/BFS 기초 |
| 133 | [Clone Graph](https://leetcode.com/problems/clone-graph/) | Medium | HashMap + DFS |
| 695 | [Max Area of Island](https://leetcode.com/problems/max-area-of-island/) | Medium | DFS |
| 207 | [Course Schedule](https://leetcode.com/problems/course-schedule/) | Medium | 위상 정렬 |
| 210 | [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) | Medium | 위상 정렬 응용 |

**📝 추천 문제 (10개)**
| # | 문제 | 설명 |
|---|------|------|
| 733 | [Flood Fill](https://leetcode.com/problems/flood-fill/) | DFS 기초 |
| 994 | [Rotting Oranges](https://leetcode.com/problems/rotting-oranges/) | BFS |
| 547 | [Number of Provinces](https://leetcode.com/problems/number-of-provinces/) | Union Find |
| 684 | [Redundant Connection](https://leetcode.com/problems/redundant-connection/) | Union Find |
| 417 | [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/) | DFS |
| 130 | [Surrounded Regions](https://leetcode.com/problems/surrounded-regions/) | DFS |
| 797 | [All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/) | 백트래킹 |
| 1091 | [Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/) | BFS |
| 127 | [Word Ladder](https://leetcode.com/problems/word-ladder/) | BFS |
| 863 | [All Nodes Distance K](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/) | BFS |

**🔥 Medium 도전 (5개)**
| # | 문제 | 설명 |
|---|------|------|
| 323 | [Number of Connected Components](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/) | Union Find |
| 261 | [Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/) | Union Find |
| 310 | [Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/) | 위상 정렬 |
| 332 | [Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/) | DFS |
| 269 | [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) | 위상 정렬 |

---

#### Week 11-12: Dynamic Programming ⭐⭐⭐

**개념:**
- 점화식 세우기
- 메모이제이션
- Bottom-up vs Top-down

**✅ 필수 문제 (5개)**
| # | 문제 | 난이도 | 핵심 |
|---|------|--------|------|
| 70 | [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) | Easy | 피보나치 |
| 198 | [House Robber](https://leetcode.com/problems/house-robber/) | Medium | 1D DP |
| 322 | [Coin Change](https://leetcode.com/problems/coin-change/) | Medium | 완전 탐색 → DP |
| 300 | [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) | Medium | LIS |
| 62 | [Unique Paths](https://leetcode.com/problems/unique-paths/) | Medium | 2D DP |

**📝 추천 문제 (10개)**
| # | 문제 | 설명 |
|---|------|------|
| 509 | [Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) | 기초 |
| 746 | [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/) | 비용 최소화 |
| 53 | [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) | Kadane |
| 213 | [House Robber II](https://leetcode.com/problems/house-robber-ii/) | 순환 배열 |
| 91 | [Decode Ways](https://leetcode.com/problems/decode-ways/) | 경우의 수 |
| 139 | [Word Break](https://leetcode.com/problems/word-break/) | 문자열 DP |
| 152 | [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/) | 곱셈 |
| 377 | [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/) | 순서 포함 |
| 416 | [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) | 배낭 문제 |
| 494 | [Target Sum](https://leetcode.com/problems/target-sum/) | 배낭 응용 |

**🔥 Medium 도전 (5개)**
| # | 문제 | 설명 |
|---|------|------|
| 63 | [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/) | 장애물 |
| 5 | [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) | 2D DP |
| 72 | [Edit Distance](https://leetcode.com/problems/edit-distance/) | Hard |
| 312 | [Burst Balloons](https://leetcode.com/problems/burst-balloons/) | Hard |
| 10 | [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/) | Hard |

---

## 📊 주차별 학습 목표

### Week 1: 패턴 이해
- ✅ 필수 문제 5개 풀기
- ✅ 패턴 가이드 읽기
- ✅ 해설 봐도 OK!

### Week 2: 숙달
- ✅ 추천 문제 10개 풀기
- ✅ Easy 완전 마스터
- ✅ Day 3, Day 7 복습

### Week 3+: 심화 (여유 있으면)
- ✅ Medium 도전 3-5개
- ✅ 패턴 조합 문제
- ✅ 면접 준비

---

## 🔗 추천 자료

**영상:**
- [NeetCode 로드맵](https://neetcode.io/roadmap) - 체계적인 순서
- [NeetCode YouTube](https://www.youtube.com/@NeetCode) - 문제별 해설

**연습:**
- [LeetCode](https://leetcode.com) - 문제 풀기
- [NeetCode 150](https://neetcode.io/practice) - 필수 150문제

**Kotlin 치트시트:**
```kotlin
// Collections
nums.toSet()
nums.groupingBy { it }.eachCount()
nums.sorted()

// Map
map.getOrDefault(key, 0)
map[key] = value

// Deque
val deque = ArrayDeque<Int>()
deque.addFirst() / addLast()
deque.removeFirst() / removeLast()
```

---

## ✅ 완료 기준

**Phase 1 완료 (1-4주):**
- [ ] Array & Hashing: 필수 5 + 추천 10
- [ ] Two Pointers: 필수 5 + 추천 10
- [ ] Sliding Window: 필수 5 + 추천 10
- [ ] **Easy 문제 50개 이상**

**Phase 2 완료 (5-8주):**
- [ ] Stack: 필수 5 + 추천 10
- [ ] Binary Search: 필수 5 + 추천 10
- [ ] Trees: 필수 6 + 추천 10
- [ ] **Easy 문제 100개 이상**

**Phase 3 완료 (9-12주):**
- [ ] Graphs: 필수 5 + 추천 10
- [ ] DP: 필수 5 + 추천 10
- [ ] **Medium 문제 30개 이상**

**이직 준비 완료:**
- [ ] 전체 패턴 마스터
- [ ] **Easy 95% 이상 성공률** (상향!)
- [ ] **Medium 70-80% 성공률** (현실적 합격 기준)
- [ ] 모의 면접 5회 이상

---

## 🎯 요즘 트렌드: 코테 외 준비사항

### 1. 과제형 코딩 테스트 (2-3일 기한)
**평가 기준:**
- ✅ 요구사항 충족 (기능 구현)
- ✅ 코드 품질 (클린 코드, SOLID 원칙)
- ✅ 테스트 코드 (JUnit, Mockito)
- ✅ README (실행 방법, 설계 설명, 트레이드오프)
- ✅ Git 커밋 히스토리 (의미 있는 커밋 메시지)

**준비 방법:**
- Spring Boot + JPA로 REST API 만들기 연습
- 테스트 코드 커버리지 80% 목표
- README 템플릿 준비 (프로젝트 구조, API 명세, 실행 방법)

### 2. 코드 리뷰 면접
**면접관 질문:**
- "왜 이렇게 설계했나요?"
- "다른 방법도 고려했나요? 트레이드오프는?"
- "성능 개선 여지는?"

**준비 방법:**
- 본인 코드 설명하는 연습
- 설계 결정의 근거 명확히
- 대안과 트레이드오프 정리

### 3. 라이브 코딩 (화면 공유)
**난이도:** ⭐⭐⭐⭐⭐ (가장 어려움!)
**특징:**
- 면접관 앞에서 실시간 코딩
- **말하면서** 코딩해야 함
- 사고 과정 실시간 설명

**준비 방법:**
- LeetCode 풀 때 소리 내서 설명하며 풀기
- 친구/동료와 모의 라이브 코딩
- "지금 이 부분은... 왜냐하면..." 말하는 연습

### 4. 시스템 디자인 + 구현
**형태:**
- 설계 후 바로 구현
- Spring Boot + JPA로 API 만들기
- 45분 안에 완성

**준비 방법:**
- 간단한 API 서버 빠르게 구현 연습 (TODO, 게시판)
- Spring Boot 보일러플레이트 준비
- JPA Entity 설계 연습

---

*12주 코테 로드맵 Ver 2.1 - 2026-02-05 (성공률 상향 + 과제/라이브 코딩 대비)*
*작성: 세이버 ⚔️*
