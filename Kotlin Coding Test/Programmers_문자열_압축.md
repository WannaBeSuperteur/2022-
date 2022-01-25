## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/60057
 * Programmers - 2020 KAKAO BLIND RECRUITMENT

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    fun numbers(num: Int): Int {
        return log10(num.toDouble()).toInt() + 1
    }
    
    fun solution(s: String): Int {
        
        // 2글자 이하인 경우 그대로 하는 것이 최선
        if (s.length <= 2) return s.length
        
        var minLength: Int = 2000
        
        // i 단위로 문자열을 자를 때
        for (i in 1..s.length-1) {
            val counts = mutableListOf<Int>()
            var currentCount: Int = 1
            
            // 반복되는 문자열의 연속된 개수를 계산하여 배열에 저장
            for (j in i..s.length-1 step i) {
                if(j + i <= s.length) {
                    if(s.substring(j, j+i).equals(s.substring(j-i, j))) {
                        currentCount++
                    } else {
                        counts.add(currentCount)
                        currentCount = 1
                    }
                }
            }
            counts.add(currentCount)
            
            // 문자열을 압축했을 때의 길이를 저장
            var length: Int = s.length
            for (count in counts) {
                if (count > 1) {
                    length -= i * (count - 1)
                    length += numbers(count)
                }
            }
            
            // 압축했을 때의 길이의 최솟값을 갱신
            if (length < minLength) {
                minLength = length
            }
        }
        return minLength
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (3.28ms, 56.3MB)
테스트 2 〉	통과 (6.32ms, 57.4MB)
테스트 3 〉	통과 (4.62ms, 56.7MB)
테스트 4 〉	통과 (3.24ms, 58.8MB)
테스트 5 〉	통과 (0.03ms, 56.1MB)
테스트 6 〉	통과 (3.25ms, 57.2MB)
테스트 7 〉	통과 (4.66ms, 56.9MB)
테스트 8 〉	통과 (6.10ms, 57.3MB)
테스트 9 〉	통과 (6.76ms, 56.7MB)
테스트 10 〉    통과 (8.36ms, 57.8MB)
테스트 11 〉	통과 (3.84ms, 57.2MB)
테스트 12 〉	통과 (3.14ms, 56.8MB)
테스트 13 〉	통과 (3.77ms, 56.8MB)
테스트 14 〉	통과 (4.30ms, 59MB)
테스트 15 〉	통과 (3.39ms, 56.9MB)
테스트 16 〉	통과 (2.64ms, 57MB)
테스트 17 〉	통과 (5.04ms, 57.5MB)
테스트 18 〉	통과 (4.86ms, 57.3MB)
테스트 19 〉	통과 (5.00ms, 57.6MB)
테스트 20 〉	통과 (6.76ms, 57.9MB)
테스트 21 〉	통과 (6.60ms, 58.2MB)
테스트 22 〉	통과 (6.65ms, 58MB)
테스트 23 〉	통과 (6.73ms, 58.1MB)
테스트 24 〉	통과 (6.90ms, 58.3MB)
테스트 25 〉	통과 (6.71ms, 58.7MB)
테스트 26 〉	통과 (6.73ms, 58MB)
테스트 27 〉	통과 (7.58ms, 58MB)
테스트 28 〉	통과 (2.96ms, 57.5MB)
```
