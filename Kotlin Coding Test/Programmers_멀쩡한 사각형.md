## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/62048
 * Programmers - Summer/Winter Coding(2019)

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    
    // 최대공약수 계산
    fun GCD(a: Long, b: Long): Long {
        var A: Long = a
        var B: Long = b
        
        while(A > 0 && B > 0) {
            if (A >= B) A -= B
            else B -= A
        }
        return max(A, B)
    }
    
    fun solution(w: Int, h: Int): Long {
        
        val W: Long = w.toLong()
        val H: Long = h.toLong()
        
        // (사용 가능한 칸 수) = (전체) - (가로 + 세로) + (최대공약수)
        val answer: Long = W * H - (W + H) + GCD(W, H)
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.03ms, 56.5MB)
테스트 2 〉	통과 (0.03ms, 56.1MB)
테스트 3 〉	통과 (0.04ms, 57.1MB)
테스트 4 〉	통과 (0.05ms, 56.6MB)
테스트 5 〉	통과 (0.05ms, 57.8MB)
테스트 6 〉	통과 (0.03ms, 56.1MB)
테스트 7 〉	통과 (0.03ms, 56.2MB)
테스트 8 〉	통과 (0.27ms, 56.3MB)
테스트 9 〉	통과 (0.05ms, 58.4MB)
테스트 10 〉	통과 (0.04ms, 56MB)
테스트 11 〉	통과 (50.17ms, 56.4MB)
테스트 12 〉	통과 (0.08ms, 56.5MB)
테스트 13 〉	통과 (0.05ms, 56.8MB)
테스트 14 〉	통과 (0.05ms, 56.9MB)
테스트 15 〉	통과 (0.05ms, 56.3MB)
테스트 16 〉	통과 (0.06ms, 56.5MB)
테스트 17 〉	통과 (0.03ms, 57.1MB)
테스트 18 〉	통과 (57.34ms, 56.9MB)
```
