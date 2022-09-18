Problem Source : https://www.acmicpc.net/problem/10986

```kotlin
fun main() {
    val NandM = readLine().toString().split(" ")
    val N = NandM[0].toInt()
    val M = NandM[1].toInt()
    val A = readLine().toString().split(" ").map { it.toInt() }
    
    // count[x] : the number of points that (sum of A[0], A[1], ..., A[k]) % M becomes x
    // for each value, where value = 0, 1, ..., M-1
        
    // initialize count as 0
    var count = (0 until M).map { 0L }.toMutableList()
    count[0] = 1
    var accumSum = 0
    
    A.forEach {
        accumSum = (accumSum + it) % M
        count[accumSum] += 1L
    }
    // println(count)

    // calculate 0+1+...+(n-1) if the value of count[x] is n
    val finalCount = count.map { it -> it * (it - 1) / 2 }
    // println(finalCount)
    
    println(finalCount.sum())
}
```
