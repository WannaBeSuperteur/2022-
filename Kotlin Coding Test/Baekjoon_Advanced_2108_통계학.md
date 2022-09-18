Problem Source : https://www.acmicpc.net/problem/2108

```kotlin
fun main() {
    val N = readLine().toString().toInt()
    val nums = mutableListOf<Int>()
    
    for (i in 0 until N) {
        nums.add(readLine().toString().toInt())
    }
    
    val sign : Int.() -> Int = {
        if (this > 0) 1
        else if (this == 0) 0
        else -1
    }
    
    // avg
    println((nums.sum() + (nums.size / 2) * nums.sum().sign()) / nums.size)
    
    // mid
    val numsSorted = nums.sorted()
    println(numsSorted[nums.size / 2])
    
    // mode
    val cntsGroup = nums.groupBy { it }
    // println(cntsGroup)
    val cntsMax = cntsGroup.map { it.value.size }.maxOrNull()!!
    val cntsMaxKey = cntsGroup.filter { it.value.size == cntsMax }.map { it.key }
    val cntsMaxKeySorted = cntsMaxKey.sorted()
    println(cntsMaxKeySorted[Math.min(1, cntsMaxKeySorted.size - 1)])
    
    // range
    println(nums.maxOrNull()!! - nums.minOrNull()!!)
}
```
