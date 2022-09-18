Problem Source : https://www.acmicpc.net/problem/2225

```kotlin
fun main() {
    val NandK = readLine().toString().split(" ")
    val N = NandK[0].toInt()
    val K = NandK[1].toInt()
    
    // init 2D array with shape (N+1, K) as 1
    val table = mutableListOf<MutableList<Int>>()
    for (i in 0 until N+1) {
        table.add((0 until K).map { 1 }.toMutableList())
    }
    
    // compute the array
    for (i in 1 until N+1) {
        for (j in 1 until K) {
            table[i][j] = (table[i-1][j] + table[i][j-1]) % 1000000000
        }
    }
    
    println(table[N][K-1])
}
```
