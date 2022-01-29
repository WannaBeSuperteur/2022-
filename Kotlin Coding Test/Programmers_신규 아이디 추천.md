## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/72410
 * Programmers - 2021 KAKAO BLIND RECRUITMENT

## 문제 풀이
```kotlin
class Solution {
    fun solution(new_id: String): String {
        var answer: String = new_id
        
        // 1
        answer = answer.toLowerCase()
        
        // 2
        answer = answer.replace("[^0-9a-z_.-]".toRegex(), "")
        
        // 3
        var old_len = answer.length
        while (true) {
            answer = answer.replace("..", ".")
            if (answer.length == old_len) break
            old_len = answer.length
        }
        
        // 4
        if (answer[0] == '.') answer = answer.substring(1)
        if (answer.length > 0 && answer[answer.length - 1] == '.') answer = answer.dropLast(1)
        
        // 5
        if (answer.isEmpty()) answer = "a"
        
        // 6
        if (answer.length >= 16) answer = answer.substring(0, 15)
        if (answer[answer.length - 1] == '.') answer = answer.dropLast(1)
        
        // 7
        while (answer.length <= 2) answer = answer + answer.substring(answer.length - 1)
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (28.47ms, 61.7MB)
테스트 2 〉	통과 (32.30ms, 62.4MB)
테스트 3 〉	통과 (29.10ms, 61.4MB)
테스트 4 〉	통과 (31.06ms, 62MB)
테스트 5 〉	통과 (36.50ms, 61.4MB)
테스트 6 〉	통과 (40.08ms, 61.3MB)
테스트 7 〉	통과 (29.25ms, 61.9MB)
테스트 8 〉	통과 (41.94ms, 61.1MB)
테스트 9 〉	통과 (28.33ms, 62.3MB)
테스트 10 〉	통과 (30.39ms, 62MB)
테스트 11 〉	통과 (30.21ms, 61.1MB)
테스트 12 〉	통과 (35.62ms, 61.5MB)
테스트 13 〉	통과 (30.71ms, 61.7MB)
테스트 14 〉	통과 (33.17ms, 61.1MB)
테스트 15 〉	통과 (31.00ms, 60.6MB)
테스트 16 〉	통과 (30.27ms, 61.5MB)
테스트 17 〉	통과 (31.90ms, 62.2MB)
테스트 18 〉	통과 (30.04ms, 61.5MB)
테스트 19 〉	통과 (33.72ms, 61.2MB)
테스트 20 〉	통과 (32.29ms, 63.7MB)
테스트 21 〉	통과 (44.06ms, 62MB)
테스트 22 〉	통과 (31.75ms, 63.4MB)
테스트 23 〉	통과 (38.67ms, 62.4MB)
테스트 24 〉	통과 (31.12ms, 62MB)
테스트 25 〉	통과 (35.69ms, 62MB)
테스트 26 〉	통과 (33.75ms, 62.2MB)
```
