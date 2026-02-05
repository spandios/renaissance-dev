# 📝 패턴 1: Array & Hashing

**Phase 1 - Week 1-2**

---

## 🎥 학습 자료

**NeetCode Playlist:**
- [Arrays & Hashing - NeetCode](https://www.youtube.com/playlist?list=PLot-Xpze53leOBgcVsJBEGrHPd_7x_koV)

**개별 영상:**
- [Contains Duplicate - Leetcode 217](https://www.youtube.com/watch?v=3OamzN90kPg)
- [Valid Anagram - Leetcode 242](https://www.youtube.com/watch?v=9UtInBqnCgA)
- [Two Sum - Leetcode 1](https://www.youtube.com/watch?v=KLlXCFG5TnA)

---

## 💡 핵심 개념

### 1. 왜 HashMap을 쓰는가?

**문제 상황:**
```kotlin
// ❌ 나쁜 방법: 이중 for문 O(n²)
fun twoSum(nums: IntArray, target: Int): IntArray {
    for (i in nums.indices) {
        for (j in i + 1 until nums.size) {
            if (nums[i] + nums[j] == target) {
                return intArrayOf(i, j)
            }
        }
    }
    return intArrayOf()
}
// 시간복잡도: O(n²) - 느림!
```

**해결:**
```kotlin
// ✅ 좋은 방법: HashMap O(n)
fun twoSum(nums: IntArray, target: Int): IntArray {
    val map = mutableMapOf<Int, Int>()  // <값, 인덱스>
    
    for (i in nums.indices) {
        val complement = target - nums[i]
        if (map.containsKey(complement)) {
            return intArrayOf(map[complement]!!, i)
        }
        map[nums[i]] = i
    }
    return intArrayOf()
}
// 시간복잡도: O(n) - 빠름!
```

**핵심 아이디어:**
- HashMap은 O(1) 탐색
- 이중 for문 O(n²) → HashMap O(n)으로 개선
- **"배열에서 뭔가 찾아야 한다" = HashMap 떠올리기**

---

## 🎯 자주 쓰는 패턴

### 1. 빈도수 세기 (Frequency Count)

```kotlin
fun countFrequency(arr: IntArray): Map<Int, Int> {
    val freq = mutableMapOf<Int, Int>()
    
    for (num in arr) {
        freq[num] = freq.getOrDefault(num, 0) + 1
    }
    
    return freq
}

// Kotlin 스타일
fun countFrequencyKotlin(arr: IntArray): Map<Int, Int> {
    return arr.groupingBy { it }.eachCount()
}
```

### 2. Set으로 중복 제거

```kotlin
fun containsDuplicate(nums: IntArray): Boolean {
    val seen = mutableSetOf<Int>()
    
    for (num in nums) {
        if (num in seen) {
            return true  // 중복 발견!
        }
        seen.add(num)
    }
    
    return false
}

// Kotlin 스타일 (1줄!)
fun containsDuplicateKotlin(nums: IntArray): Boolean {
    return nums.size != nums.toSet().size
}
```

### 3. Anagram 체크 (정렬 vs 빈도수)

```kotlin
// 방법 1: 정렬 O(n log n)
fun isAnagram(s: String, t: String): Boolean {
    if (s.length != t.length) return false
    return s.toCharArray().sorted() == t.toCharArray().sorted()
}

// 방법 2: HashMap O(n) - 더 빠름!
fun isAnagramHashMap(s: String, t: String): Boolean {
    if (s.length != t.length) return false
    
    val count = mutableMapOf<Char, Int>()
    
    for (c in s) {
        count[c] = count.getOrDefault(c, 0) + 1
    }
    
    for (c in t) {
        count[c] = count.getOrDefault(c, 0) - 1
        if (count[c]!! < 0) return false
    }
    
    return true
}
```

---

## 🔥 꿀팁 & 주의사항

### 1. HashMap으로 O(n²) → O(n) 개선
- 배열에서 "두 수의 합", "특정 값 찾기" → HashMap!
- 공간복잡도 O(n) 써도 괜찮으면 무조건 HashMap

### 2. 빈도수 세기 패턴
```kotlin
// Java 스타일
map[key] = map.getOrDefault(key, 0) + 1

// Kotlin 스타일
map.groupingBy { it }.eachCount()
```

### 3. Set으로 중복 체크
- `if (num in seen)` - O(1) 탐색
- `seen.add(num)` - O(1) 삽입

### 4. Anagram은 정렬 후 비교가 간단
- 정렬: O(n log n)
- HashMap: O(n) - 더 빠르지만 코드 길어짐
- **면접에서는 둘 다 설명하기**

### 5. Kotlin 편리 함수 활용
```kotlin
// groupBy, count, sorted 등
nums.groupingBy { it }.eachCount()
nums.sorted()
nums.toSet()
```

---

## 📚 대표 문제 (Easy)

### 1. LeetCode 1: Two Sum
**문제:** 배열에서 두 수의 합이 target인 인덱스 찾기

**핵심:** HashMap에 `<값, 인덱스>` 저장

```kotlin
fun twoSum(nums: IntArray, target: Int): IntArray {
    val map = mutableMapOf<Int, Int>()
    
    for (i in nums.indices) {
        val complement = target - nums[i]
        if (map.containsKey(complement)) {
            return intArrayOf(map[complement]!!, i)
        }
        map[nums[i]] = i
    }
    
    return intArrayOf()
}
```

### 2. LeetCode 217: Contains Duplicate
**문제:** 배열에 중복 값이 있는가?

**핵심:** Set 크기 비교

```kotlin
fun containsDuplicate(nums: IntArray): Boolean {
    return nums.size != nums.toSet().size
}
```

### 3. LeetCode 242: Valid Anagram
**문제:** 두 문자열이 애너그램인가?

**핵심:** 빈도수 비교

```kotlin
fun isAnagram(s: String, t: String): Boolean {
    if (s.length != t.length) return false
    return s.toCharArray().sorted() == t.toCharArray().sorted()
}
```

---

## ⚠️ 면접 포인트

**Q: Two Sum을 O(n)으로 풀 수 있나요?**
A: HashMap을 써서 complement를 O(1)로 찾으면 됩니다.

**Q: 공간복잡도를 줄일 수 있나요?**
A: Two Sum은 불가능합니다. 정렬 후 Two Pointers를 쓰면 O(1) 공간이지만, 인덱스를 못 돌려줍니다.

**Q: Anagram 체크를 O(n)으로?**
A: HashMap으로 빈도수를 세면 O(n)입니다. 정렬은 O(n log n)입니다.

---

## 🎓 학습 순서

1. **개념 이해** (15분)
   - HashMap이 O(1) 탐색인 이유
   - Set vs HashMap 차이

2. **코드 작성** (20분)
   - Two Sum 직접 구현
   - Contains Duplicate 구현
   - Valid Anagram 두 가지 방법으로 구현

3. **복습** (3일 후)
   - 같은 문제 다시 풀기
   - 시간복잡도 설명하기

---

## 🔗 다음 패턴

Week 3: **Two Pointers** - 정렬된 배열에서 양쪽에서 좁히기

---

_Last updated: 2026-02-05_
_Author: 세이버 ⚔️_
