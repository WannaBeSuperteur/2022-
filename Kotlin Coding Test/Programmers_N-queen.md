## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/12952
 * Programmers - 연습문제

## 문제 풀이
```kotlin
class Solution {
    
    // 주어진 경우에서 퀸이 모두 서로 공격 불가이면 true
    fun feasible(queens: MutableList<Int>) : Boolean {
        val n = queens.count()
        
        for (i in 0..n-1) {
            for (j in 0..i-1) {
                if (queens[i] == queens[j]) return false
                if (Math.abs(i - j) == Math.abs(queens[i] - queens[j])) return false
            }
        }
        
        return true
    }
    
    // 재귀
    fun subSolution(n: Int, queens: MutableList<Int>): Int {
        
        // 상호 공격 가능하면 종료
        if (!feasible(queens)) return 0
        
        // 상호 공격 불가능하면 subcase 탐색
        
        // 모든 퀸이 배치되었으면
        if (queens.count() == n) return 1
        
        // 일부 퀸이 배치되지 않았으면
        else {
            var result = 0
            for (i in 0..n-1) {
                val newQueens = queens.toMutableList()
                newQueens.add(i)
                result += subSolution(n, newQueens)
            }
            return result
        }
    }
    
    fun solution(n: Int): Int {
        val queens = mutableListOf<Int>()
        return subSolution(n, queens)
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (5.13ms, 57.5MB)
테스트 2 〉	통과 (4.97ms, 57MB)
테스트 3 〉	통과 (5.10ms, 57.8MB)
테스트 4 〉	통과 (5.81ms, 57MB)
테스트 5 〉	통과 (6.97ms, 57.4MB)
테스트 6 〉	통과 (8.99ms, 57.7MB)
테스트 7 〉	통과 (14.02ms, 65MB)
테스트 8 〉	통과 (25.56ms, 71.6MB)
테스트 9 〉	통과 (79.96ms, 86.5MB)
테스트 10 〉	통과 (403.77ms, 235MB)
테스트 11 〉	통과 (2076.40ms, 367MB)
```
