## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/70129
 * Programmers - 월간 코드 챌린지 시즌1 - 이진 변환 반복하기

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    
    fun binary(n: Int): String {
        var result = ""
        var N = n
        
        while (N > 0) {
            result = (N % 2).toString() + result
            N /= 2
        }
        
        return result
    }
    
    fun solution(s: String): IntArray {
        var count = 0
        var removedZeros = 0
        var var_s = s
        
        while(var_s.length > 1) {
            val zeros = var_s.length - var_s.replace("0", "").length
            var_s = binary(var_s.length - zeros)
            
            removedZeros += zeros
            count++
        }
        
        var answer: IntArray = intArrayOf(count, removedZeros)
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (26.27ms, 61.9MB)
테스트 2 〉	통과 (85.40ms, 85MB)
테스트 3 〉	통과 (30.70ms, 61.5MB)
테스트 4 〉	통과 (36.92ms, 61.1MB)
테스트 5 〉	통과 (26.78ms, 61.4MB)
테스트 6 〉	통과 (28.46ms, 61.8MB)
테스트 7 〉	통과 (29.29ms, 62.2MB)
테스트 8 〉	통과 (29.92ms, 61.8MB)
테스트 9 〉	통과 (39.88ms, 66.3MB)
테스트 10 〉	통과 (59.07ms, 76.2MB)
테스트 11 〉	통과 (60.71ms, 85.7MB)
```
