Problem Source : https://www.acmicpc.net/problem/20125

```kotlin
fun main() {
    val N = readLine().toString().toInt()
    
    val info = mutableListOf<List<Int>>()
    for (i in 0 until N) {
        val line = readLine().toString()
        info.add(
            (0..N-1).map {
                when(line[it]) {
                    '*' -> 1
                    '_' -> 0
                    else -> -1
                }
            }
        )
    }
    
    var y = 0
    var x = 0
    for (i in 1 until N-1) {
        for (j in 1 until N-1) {
            if (info[i][j] + info[i-1][j] + info[i+1][j] + info[i][j-1] + info[i][j+1] == 5) {
                y = i
                x = j
                break
            }
        }
        if (x > 0 && y > 0) {
            break
        }
    }
    println("${y+1} ${x+1}")
    
    var len0 = 0
    while (x-len0 >= 0 && info[y][x-len0] == 1) {
        len0++
    }
    
    var len1 = 0
    while (x+len1 < N && info[y][x+len1] == 1) {
        len1++
    }
    
    var len2 = 0
    while (y+len2 < N && info[y+len2][x] == 1) {
        len2++
    }
    
    var len3 = 0
    while (y+len2+len3 < N && info[y+len2+len3][x-1] == 1) {
        len3++
    }
    
    var len4 = 0
    while (y+len2+len4 < N && info[y+len2+len4][x+1] == 1) {
        len4++
    }
    
    println("${len0-1} ${len1-1} ${len2-1} $len3 $len4")
}
```
