## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/76502
 * Programmers - 월간 코드 챌린지 시즌 2

## 문제 풀이
```kotlin
class Solution {
    fun check(s: String): Boolean {
        var stack = ""

        for (char in s) {
            when(char) {
                '(' -> stack += "("
                '{' -> stack += "{"
                '[' -> stack += "["
                ')' -> {
                    if(!stack.endsWith("(")) return false
                    stack = stack.dropLast(1)
                }
                '}' -> {
                    if(!stack.endsWith("{")) return false
                    stack = stack.dropLast(1)
                }
                ']' -> {
                    if(!stack.endsWith("[")) return false
                    stack = stack.dropLast(1)
                }
            }
        }
        return stack.isEmpty()
    }
    
    fun solution(s: String): Int {
        var result = 0
        
        for (n in 0..s.length-1) {
            if (check(s.substring(n) + s.substring(0, n))) result++
        }
        return result
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (24.47ms, 73.6MB)
테스트 2 〉	통과 (17.96ms, 64.5MB)
테스트 3 〉	통과 (15.70ms, 63.6MB)
테스트 4 〉	통과 (17.88ms, 65.9MB)
테스트 5 〉	통과 (33.34ms, 74.5MB)
테스트 6 〉	통과 (19.92ms, 68.8MB)
테스트 7 〉	통과 (21.53ms, 68.7MB)
테스트 8 〉	통과 (24.88ms, 71.3MB)
테스트 9 〉	통과 (38.34ms, 81.4MB)
테스트 10 〉	통과 (43.40ms, 77.9MB)
테스트 11 〉	통과 (52.25ms, 79.7MB)
테스트 12 〉	통과 (5.41ms, 57.4MB)
테스트 13 〉	통과 (7.94ms, 58MB)
테스트 14 〉	통과 (6.47ms, 57.2MB)
```
