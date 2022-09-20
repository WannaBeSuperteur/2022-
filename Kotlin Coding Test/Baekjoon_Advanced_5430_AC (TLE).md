Problem Source : https://www.acmicpc.net/problem/5430

```kotlin
// TIME LIMIT EXCEEDED SOLUTION

fun main() {
    val N = readLine().toString().toInt()
    
    for (i in 0..N-1) {
        val func = readLine().toString()
        val n = readLine().toString().toInt() // unused
        var arr = readLine().toString().replace("[", "").replace("]", "").let {
            if (it.isEmpty()) {
                listOf<Int>()
            } else {
                it.split(",").map { it.toInt() }
            }
        }
        
        var rCount = 0
        var dropCount = 0
        var dropLastCount = 0
//        var isError = false
        
        // direct check
        val cntD = func.length - func.replace("D", "").length
        if (cntD > n) {
            println("error")
            continue
        }
        
        for (i in 0 until func.length) {
//            if (dropCount + dropLastCount >= arr.size) {
//                println("error")
//                isError = true
//                break
//            }
            when (func[i]) {
                'R' -> {
                    rCount = 1 - rCount
                }
                'D' -> {
                    when (rCount) {
                        0 -> dropCount++
                        1 -> dropLastCount++
                    }
                }
            }
        }
//        if (isError) continue
        
        arr = arr.drop(dropCount)
        arr = arr.dropLast(dropLastCount)
        
        if (rCount == 1) {
            arr = arr.asReversed()
        }
        
        val final = arr.fold ("[") { acc, e ->
            "${acc}${e},"
        }.dropLast(1) + "]"
    
        println(final)
    }
}
```
