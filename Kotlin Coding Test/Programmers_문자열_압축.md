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
