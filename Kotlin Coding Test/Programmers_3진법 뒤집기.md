## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/68935
 * Programmers - 월간 코드 챌린지 시즌1

## 문제 풀이
```kotlin
class Solution {
    
    // 10진법 -> 3진법
    fun tenToThree(n: Int): String {
        var result = ""
        var N = n
        while (N > 0) {
            result = (N % 3).toString() + result
            N /= 3
        }
        return result
    }
    
    // 뒤집기
    fun reverse(s: String): String {
        var result = ""
        for (c in s) result = c + result
        return result
    }
    
    // 3진법 -> 10진법
    fun threeToTen(s: String): Int {
        var result = 0
        for (i in 0..s.length-1) {
            if (i == 0) result = s[i].toString().toInt()
            else result = result * 3 + s[i].toString().toInt()
        }
        return result
    }
    
    fun solution(n: Int): Int {
        return threeToTen(reverse(tenToThree(n)))
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.04ms, 58.3MB)
테스트 2 〉	통과 (0.09ms, 57.1MB)
테스트 3 〉	통과 (0.06ms, 56.2MB)
테스트 4 〉	통과 (0.05ms, 56.5MB)
테스트 5 〉	통과 (0.06ms, 58.4MB)
테스트 6 〉	통과 (0.06ms, 56.6MB)
테스트 7 〉	통과 (0.07ms, 57.3MB)
테스트 8 〉	통과 (0.07ms, 56.4MB)
테스트 9 〉	통과 (0.07ms, 56.8MB)
테스트 10 〉	통과 (0.08ms, 56.5MB)
```
