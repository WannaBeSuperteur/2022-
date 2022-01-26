## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/12949
 * Programmers - 연습문제

## 문제 풀이
```kotlin
class Solution {
    fun solution(arr1: Array<IntArray>, arr2: Array<IntArray>): Array<IntArray> {
        val I = arr1.size
        val J = arr2.size
        val K = arr2[0].size
        
        var answer = Array(I, {IntArray(K)})
        
        for(i in 0..I-1) {
            for (j in 0..J-1) {
                for (k in 0..K-1) {
                    answer[i][k] += arr1[i][j] * arr2[j][k]
                }
            }
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.89ms, 60.6MB)
테스트 2 〉	통과 (5.97ms, 60.2MB)
테스트 3 〉	통과 (6.55ms, 62.1MB)
테스트 4 〉	통과 (0.56ms, 56.6MB)
테스트 5 〉	통과 (4.88ms, 66.2MB)
테스트 6 〉	통과 (3.63ms, 64.7MB)
테스트 7 〉	통과 (0.30ms, 56.9MB)
테스트 8 〉	통과 (0.12ms, 57.1MB)
테스트 9 〉	통과 (0.10ms, 56.4MB)
테스트 10 〉	통과 (5.62ms, 61.4MB)
테스트 11 〉	통과 (0.90ms, 57.1MB)
테스트 12 〉	통과 (0.12ms, 56.9MB)
테스트 13 〉	통과 (4.09ms, 64MB)
테스트 14 〉	통과 (6.53ms, 61.8MB)
테스트 15 〉	통과 (3.25ms, 61.4MB)
테스트 16 〉	통과 (1.41ms, 57.2MB)
```
