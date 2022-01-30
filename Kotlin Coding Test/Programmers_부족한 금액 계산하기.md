## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/82612
 * Programmers - 위클리 챌린지

## 문제 풀이
```kotlin
class Solution {
    fun solution(price: Int, money: Int, count: Int): Long {
        return Math.max(0, price * (count * (count + 1) / 2).toLong() - money).toLong()
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.03ms, 57.8MB)
테스트 2 〉	통과 (0.04ms, 57.3MB)
테스트 3 〉	통과 (0.03ms, 58MB)
테스트 4 〉	통과 (0.03ms, 56.1MB)
테스트 5 〉	통과 (0.04ms, 57.4MB)
테스트 6 〉	통과 (0.03ms, 57.8MB)
테스트 7 〉	통과 (0.03ms, 58.4MB)
테스트 8 〉	통과 (0.04ms, 56.5MB)
테스트 9 〉	통과 (0.03ms, 57.7MB)
테스트 10 〉	통과 (0.03ms, 57.9MB)
테스트 11 〉	통과 (0.03ms, 57.5MB)
테스트 12 〉	통과 (0.04ms, 56.3MB)
테스트 13 〉	통과 (0.03ms, 57.3MB)
테스트 14 〉	통과 (0.03ms, 57MB)
테스트 15 〉	통과 (0.03ms, 57.1MB)
테스트 16 〉	통과 (0.05ms, 57.3MB)
테스트 17 〉	통과 (0.03ms, 56.1MB)
테스트 18 〉	통과 (0.03ms, 56.3MB)
테스트 19 〉	통과 (0.03ms, 57.2MB)
테스트 20 〉	통과 (0.04ms, 56.7MB)
테스트 21 〉	통과 (0.03ms, 56.4MB)
테스트 22 〉	통과 (0.03ms, 56.4MB)
테스트 23 〉	통과 (0.03ms, 57.6MB)
```
