Problem Source : https://www.acmicpc.net/problem/1032

```kotlin
fun main() {
    val N = readLine().toString().toInt()
    lateinit var letters: MutableList<Char>
    var nameLen = 0
    
    for (i in 0 until N) {
        val fileName = readLine().toString()
        
        if (i == 0) {
            nameLen = fileName.length
            letters = (0 until nameLen).map {' '}.toMutableList()
            
            (0 until nameLen).forEach { idx ->
                letters[idx] = fileName[idx]
            }
        } else {
            (0 until nameLen).forEach { idx ->
                if (fileName[idx] != letters[idx]) {
                    letters[idx] = '?'
                }
            }
        }
        
        if (i == N-1) {
            println(letters.joinToString(""))
        }
    }
}
```
