Problem Source : https://www.acmicpc.net/problem/4673

```kotlin
fun main() {
    val isSelfNumber = (0..10000).map { true }.toMutableList()
    
    for (i in 0..10000) {
        val dn = i + i.toString().sumOf { it.code - 48 }
        if (dn <= 10000) {
            isSelfNumber[dn] = false
        }
    }
    
    for (i in 0..10000) {
        if (isSelfNumber[i]) {
            println(i)
        }
    }
}
```
