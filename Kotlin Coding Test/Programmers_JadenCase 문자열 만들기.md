## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/12951
 * Programmers - 연습문제

## 문제 풀이
```kotlin
class Solution {
    fun jadenCase(s: String): String {
        if (s.isNullOrEmpty()) return ""
        return s.substring(0, 1).toUpperCase() + s.substring(1).toLowerCase()
    }
    
    fun solution(s: String): String {
        val s_split = s.split(" ")
        
        var answer = ""
        for (part in s_split) {
            answer += jadenCase(part) + " "
        }
        return answer.dropLast(1)
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (7.78ms, 57.4MB)
테스트 2 〉	통과 (8.25ms, 56.9MB)
테스트 3 〉	통과 (7.57ms, 57.8MB)
테스트 4 〉	통과 (7.86ms, 58.2MB)
테스트 5 〉	통과 (8.22ms, 57.4MB)
테스트 6 〉	통과 (7.87ms, 57.3MB)
테스트 7 〉	통과 (7.81ms, 57.6MB)
테스트 8 〉	통과 (7.73ms, 57.7MB)
테스트 9 〉	통과 (7.83ms, 57.7MB)
테스트 10 〉	통과 (11.47ms, 58.3MB)
테스트 11 〉	통과 (7.48ms, 57.6MB)
테스트 12 〉	통과 (7.82ms, 58.5MB)
테스트 13 〉	통과 (7.66ms, 57.7MB)
테스트 14 〉	통과 (8.14ms, 57.2MB)
테스트 15 〉	통과 (7.84ms, 57.7MB)
테스트 16 〉	통과 (8.13ms, 57.7MB)
테스트 17 〉	통과 (7.69ms, 57.2MB)
테스트 18 〉	통과 (11.85ms, 58.2MB)
```
