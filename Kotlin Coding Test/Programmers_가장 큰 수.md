## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/42746
 * Programmers - 코딩테스트 고득점 Kit - 정렬

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    
    // 4번 반복한 것의 첫 4개의 숫자를 반환
    fun repeat4(num: Int): String = (num.toString().repeat(4)).substring(0, 4)
    
    fun solution(numbers: IntArray): String {
        
        // 모두 0만으로 구성된 경우 0을 반환
        if (numbers.max() == 0) return "0"

        // 1->'1111', 234->'2342', 56->'5656', 7895->'7895', 12->'1212'
        var sortedNumbers = mutableListOf<String>()
        for (num in numbers) {
            sortedNumbers.add(repeat4(num) + "_" + num)
        }
        sortedNumbers = sortedNumbers.sortedDescending().toMutableList()
        
        // 최종 답안 생성
        var answer = ""
        for (num in sortedNumbers) {
            answer += num.split("_")[1]
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (5282.78ms, 377MB)
테스트 2 〉	통과 (1732.32ms, 374MB)
테스트 3 〉	통과 (9063.03ms, 513MB)
테스트 4 〉	통과 (51.69ms, 79MB)
테스트 5 〉	통과 (4209.58ms, 378MB)
테스트 6 〉	통과 (3315.75ms, 375MB)
테스트 7 〉	통과 (24.16ms, 61.4MB)
테스트 8 〉	통과 (25.27ms, 60.8MB)
테스트 9 〉	통과 (24.65ms, 60.9MB)
테스트 10 〉	통과 (23.21ms, 61.2MB)
테스트 11 〉	통과 (11.33ms, 59.1MB)
테스트 12 〉	통과 (23.26ms, 61MB)
테스트 13 〉	통과 (41.07ms, 61MB)
테스트 14 〉	통과 (35.89ms, 60.6MB)
테스트 15 〉	통과 (30.01ms, 60.6MB)
```
