## 문제 정보
 * https://www.acmicpc.net/problem/1347
 * Baekjoon - 구현, 문자열, 시뮬레이션

## 문제 풀이
```kotlin
fun main() {
    val n = readLine().toString().toInt()
    val moves = readLine().toString()
        
    var y = 0
    var x = 0
    var y_min = 0
    var x_min = 0
    var y_max = 0
    var x_max = 0
    var direction = 0
    val positions = mutableListOf<IntArray>(intArrayOf(0, 0))
        
    // use original position
    for (move in moves) {
        when (move) {
            'R' -> direction = (direction + 1) % 4
            'L' -> direction = (direction + 3) % 4
            'F' -> {
                when (direction) {
                    0 -> y++
                    1 -> x--
                    2 -> y--
                    3 -> x++
                }
            }
        }
        positions.add(intArrayOf(y, x))
        
        y_min = Math.min(y, y_min)
        x_min = Math.min(x, x_min)
        y_max = Math.max(y, y_max)
        x_max = Math.max(x, x_max)
    }
    
    // convert position
    val positions_new = mutableListOf<IntArray>()
    for (position in positions) {
        positions_new.add(intArrayOf(position[0] - y_min, position[1] - x_min))
    }
    y_max -= y_min
    x_max -= x_min
    
    // draw maze using coverted position
    val maze = Array(y_max + 1, {IntArray(x_max + 1)})
    for (i in 0..y_max) {
        for (j in 0..x_max) {
            maze[i][j] = 1
        }
    }
    
    for (position in positions_new) {
        maze[position[0]][position[1]] = 0
    }
    
    // print
    for (i in 0..y_max) {
        for (j in 0..x_max) {
            when (maze[i][j]) {
                0 -> print(".")
                1 -> print("#")
            }
        }
        print("\n")
    }
}
```

## 채점 결과
```
맞았습니다!!
메모리 : 14436 KB
시간 : 128 ms
```
