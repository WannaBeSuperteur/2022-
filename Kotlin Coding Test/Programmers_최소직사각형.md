## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/86491
 * Programmers - 위클리 챌린지

## 문제 풀이
```kotlin
class Solution {
    fun solution(sizes: Array<IntArray>): Int {
        var max_1st = 0
        var max_2nd = 0
        
        for (size in sizes) {
            max_1st = Math.max(max_1st, Math.max(size[0], size[1]))
            max_2nd = Math.max(max_2nd, Math.min(size[0], size[1]))
        }
        
        return max_1st * max_2nd
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.03ms, 57.6MB)
테스트 2 〉	통과 (0.03ms, 56.3MB)
테스트 3 〉	통과 (0.05ms, 56.2MB)
테스트 4 〉	통과 (0.03ms, 57.9MB)
테스트 5 〉	통과 (0.04ms, 56.3MB)
테스트 6 〉	통과 (0.04ms, 56.2MB)
테스트 7 〉	통과 (0.04ms, 58.3MB)
테스트 8 〉	통과 (0.04ms, 56.4MB)
테스트 9 〉	통과 (0.03ms, 58.2MB)
테스트 10 〉	통과 (0.09ms, 57.2MB)
테스트 11 〉	통과 (0.05ms, 56.7MB)
테스트 12 〉	통과 (0.06ms, 57.2MB)
테스트 13 〉	통과 (0.15ms, 57.5MB)
테스트 14 〉	통과 (0.34ms, 57.8MB)
테스트 15 〉	통과 (0.42ms, 61.8MB)
테스트 16 〉	통과 (0.74ms, 65MB)
테스트 17 〉	통과 (1.03ms, 67.7MB)
테스트 18 〉	통과 (1.19ms, 69.3MB)
테스트 19 〉	통과 (1.13ms, 68.6MB)
테스트 20 〉	통과 (1.28ms, 68.3MB)
```
