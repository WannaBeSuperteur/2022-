## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/42842
 * Programmers - 코딩테스트 고득점 Kit - 완전탐색

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    fun solution(brown: Int, yellow: Int): IntArray {
        val blocks = brown + yellow
        val maxHeight = sqrt(blocks.toDouble()).toInt()
        
        for (h in 3..maxHeight) {
            if (blocks % h == 0) {
                val w : Int = blocks / h
                val estimated_yellow : Int = (w - 2) * (h - 2)
                
                if (estimated_yellow == yellow) {
                    return intArrayOf(w, h)
                }
            }
        }
        
        return intArrayOf(-1, -1)
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.04ms, 57.1MB)
테스트 2 〉	통과 (0.04ms, 56.5MB)
테스트 3 〉	통과 (0.04ms, 55.6MB)
테스트 4 〉	통과 (0.08ms, 56.8MB)
테스트 5 〉	통과 (0.03ms, 56.5MB)
테스트 6 〉	통과 (0.04ms, 56.2MB)
테스트 7 〉	통과 (0.04ms, 58.1MB)
테스트 8 〉	통과 (0.04ms, 56.4MB)
테스트 9 〉	통과 (0.04ms, 56.8MB)
테스트 10 〉	통과 (0.04ms, 56.3MB)
테스트 11 〉	통과 (0.09ms, 56.1MB)
테스트 12 〉	통과 (0.03ms, 56.2MB)
테스트 13 〉	통과 (0.02ms, 56.3MB)
```
