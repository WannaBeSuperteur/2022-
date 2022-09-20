Problem Source : https://www.acmicpc.net/problem/1744

```kotlin
fun main() {
    val N = readLine().toString().toInt()
    val numList = mutableListOf<Int>()
    
    for (i in 0..N-1) {
        numList.add(readLine().toString().toInt())
    }
    
    val sortedNumList = numList.sorted()
    // println(sortedNumList)
    
    var result = 0
    var sumIdxMin = 0
    var sumIdxMax = N-1
    
    // for negative numbers
    for (i in 0 until N/2 + 1) {
        if (2*i + 1 >= N || sortedNumList[2*i + 1] > 0) {
            sumIdxMin = 2*i
            break
        }
        result += sortedNumList[2*i] * sortedNumList[2*i + 1]
    }
    // println(result)
    
    // for positive numbers
    for (i in 0 until N/2 + 1) {
        if (2*i + 1 >= N || sortedNumList[(N-1) - (2*i + 1)] < 2) {
            sumIdxMax = (N-1) - 2*i
            break
        }
        result += sortedNumList[(N-1) - 2*i] * sortedNumList[(N-1) - (2*i + 1)]
    }
    // println(result)
    
    // other numbers
    // println("$sumIdxMin, $sumIdxMax")
    (sumIdxMin..sumIdxMax).forEach {
        result += sortedNumList[it]
    }
    
    println(result)
}
```
