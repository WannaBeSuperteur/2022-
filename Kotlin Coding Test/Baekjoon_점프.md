## 문제 정보
 * https://www.acmicpc.net/problem/1890
 * Baekjoon - 다이나믹 프로그래밍

## 문제 풀이
```kotlin
fun findCasesUsingDP(dp: Array<LongArray>, board: MutableList<List<String>>): Long {
    val n = dp.size

	  dp[0][0] = 1
    
    for (i in 0..n-1) {
        for (j in 0..n-1) {
            if (i == 0 && j == 0) continue
            
            var totalCases = 0L
            for (k in 1..9) {
                if (i >= k && board[i-k][j].toInt() == k) totalCases += dp[i-k][j]
                if (j >= k && board[i][j-k].toInt() == k) totalCases += dp[i][j-k]
            }
           	dp[i][j] = totalCases
        }
    }

    return dp[n-1][n-1]
}

fun main() {
    
    val n = readLine().toString().toInt()
    val board = mutableListOf<List<String>>()  
    for (i in 0..n-1) board.add(readLine().toString().split(" "))
    
    // dynamic programming
   	val dp = Array(n, {LongArray(n)})
    
    println(findCasesUsingDP(dp, board))
}
```

## 채점 결과
```
맞았습니다!!
메모리 : 14772 KB
시간 : 148 ms
```
