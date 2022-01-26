## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/42747
 * Programmers - 코딩테스트 고득점 Kit - 정렬

## 문제 풀이
```kotlin
class Solution {
    fun solution(citations: IntArray): Int {
        
        // citation 횟수를 내림차순으로 정렬
        val sortedCitations = citations.sortedDescending().toMutableList()
        val n = sortedCitations.size
        
        // 특이한 케이스 (H-index = 0 또는 H-index 는 citations의 길이)
        if (sortedCitations[0] == 0) return 0
        if (sortedCitations[n-1] >= n) return n
        
        // 이진 탐색
        var max_ = n
        var min_ = 0
        
        while (true) {
            val mid_ = (max_ + min_) / 2
            
            if (max_ - min_ <= 1) {
                return min_
            } else if (sortedCitations[mid_ - 1] >= mid_) {
                min_ = mid_
            } else {
                max_ = mid_
            }
        }
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (16.06ms, 59.4MB)
테스트 2 〉	통과 (16.69ms, 60.1MB)
테스트 3 〉	통과 (16.56ms, 59.7MB)
테스트 4 〉	통과 (16.13ms, 59.5MB)
테스트 5 〉	통과 (16.99ms, 59.6MB)
테스트 6 〉	통과 (16.42ms, 59.8MB)
테스트 7 〉	통과 (17.67ms, 60.2MB)
테스트 8 〉	통과 (16.80ms, 59.8MB)
테스트 9 〉	통과 (15.88ms, 60.5MB)
테스트 10 〉	통과 (16.56ms, 59.8MB)
테스트 11 〉	통과 (19.49ms, 59.7MB)
테스트 12 〉	통과 (16.49ms, 59.4MB)
테스트 13 〉	통과 (16.42ms, 59.5MB)
테스트 14 〉	통과 (16.58ms, 59.7MB)
테스트 15 〉	통과 (16.79ms, 59.5MB)
테스트 16 〉	통과 (16.15ms, 59.9MB)
```
