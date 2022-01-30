## 문제 정보
 * https://www.acmicpc.net/problem/8976
 * Baekjoon - 구현, 브루트포스 알고리즘, 시뮬레이션

## 문제 풀이
```kotlin
fun simulateDirection(board: MutableList<String>, i: Int, j: Int, ii: Int, jj: Int) : Int {
    var result = 0
    for(n in 1..7) {
        try {
            when (board[i + n*ii][j + n*jj]) {
                'W' -> result++
                'B' -> return result
                else -> return 0
            }
        } catch (e: Exception) {
            return 0
        }
    }
    return 0
}

fun simulate(board: MutableList<String>, i: Int, j: Int) : Int {
    if (board[i][j] != '.') return 0
    
    var result = 0
    result += simulateDirection(board, i, j, 1, 1)
    result += simulateDirection(board, i, j, 1, 0)
    result += simulateDirection(board, i, j, 1, -1)
    result += simulateDirection(board, i, j, 0, -1)
    result += simulateDirection(board, i, j, -1, -1)
    result += simulateDirection(board, i, j, -1, 0)
    result += simulateDirection(board, i, j, -1, 1)
    result += simulateDirection(board, i, j, 0, 1)

    return result
}

fun main() {
  	val board = mutableListOf<String>()
    for (i in 0..7) board.add(readLine().toString())

    // simulation
    var result = 0
    for (i in 0..7) {
        for (j in 0..7) {
            result = Math.max(result, simulate(board, i, j))
        }
    }
    print(result)
}
```

## 채점 결과
```
맞았습니다!!
메모리 : 12824 KB
시간 : 104 ms
```
