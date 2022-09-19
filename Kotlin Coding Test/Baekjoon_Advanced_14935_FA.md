Problem Source : https://www.acmicpc.net/problem/14935

```kotlin
fun main() {
    val initial = readLine().toString()
    var oldTest = initial
    var test = 0
    
    for (i in 0..1000) {
        test = oldTest.length * (oldTest[0].code - 48)
        // println(test)
        
        if (test.toString() == oldTest) {
            println("FA")
            break
        } else if (i == 999) {
            println("NFA")
        }
        
        oldTest = test.toString()
    }
}
```
