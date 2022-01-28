## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/12985
 * Programmers - 2017 팁스타운

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    fun divideByPowOf2(a: Int, power: Int) = a / (2.0).pow(power).toInt()
    
    fun solution(n: Int, a: Int, b: Int): Int {
        val A = a - 1
        val B = b - 1
        
        var power = 0
        while(true) {
            power += 1
            if (divideByPowOf2(A, power) == divideByPowOf2(B, power)) {
                return power
            }
        }
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.04ms, 56.3MB)
테스트 2 〉	통과 (0.03ms, 56MB)
테스트 3 〉	통과 (0.03ms, 56.6MB)
테스트 4 〉	통과 (0.08ms, 56.5MB)
테스트 5 〉	통과 (0.12ms, 57MB)
테스트 6 〉	통과 (0.04ms, 56.7MB)
테스트 7 〉	통과 (0.13ms, 56.2MB)
테스트 8 〉	통과 (0.07ms, 56.4MB)
테스트 9 〉	통과 (0.05ms, 56.9MB)
테스트 10 〉	통과 (0.04ms, 57MB)
테스트 11 〉	통과 (0.04ms, 57.4MB)
테스트 12 〉	통과 (0.03ms, 56.7MB)
테스트 13 〉	통과 (0.04ms, 56.8MB)
테스트 14 〉	통과 (0.04ms, 56.5MB)
테스트 15 〉	통과 (0.03ms, 56.7MB)
테스트 16 〉	통과 (0.04ms, 55.7MB)
테스트 17 〉	통과 (0.03ms, 56MB)
테스트 18 〉	통과 (0.04ms, 58.2MB)
테스트 19 〉	통과 (0.03ms, 56.6MB)
테스트 20 〉	통과 (0.04ms, 56.7MB)
테스트 21 〉	통과 (0.05ms, 56.6MB)
테스트 22 〉	통과 (0.04ms, 56.3MB)
테스트 23 〉	통과 (0.06ms, 56.3MB)
테스트 24 〉	통과 (0.05ms, 56.7MB)
테스트 25 〉	통과 (0.05ms, 56.2MB)
테스트 26 〉	통과 (0.03ms, 56.2MB)
테스트 27 〉	통과 (0.03ms, 56.8MB)
테스트 28 〉	통과 (0.04ms, 56.3MB)
테스트 29 〉	통과 (0.04ms, 57.1MB)
테스트 30 〉	통과 (0.04ms, 56.4MB)
테스트 31 〉	통과 (0.04ms, 56.8MB)
테스트 32 〉	통과 (0.04ms, 56.1MB)
테스트 33 〉	통과 (0.05ms, 56.1MB)
테스트 34 〉	통과 (0.11ms, 56.4MB)
```
