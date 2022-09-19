Problem Source : https://www.acmicpc.net/problem/1022

```kotlin
fun computeNumber(r: Int, c: Int): Int {
    if (r < 0 && c in (r..-r)) { // 4n^2 + 1
        val baseIndex = -r
        val base = 4 * baseIndex * baseIndex + 1
        return base + r - c
    }
    else if (r > 0 && c in (-r..r)) { // (2n + 1)^2
        val baseIndex = r
        val base = (2 * baseIndex + 1) * (2 * baseIndex + 1)
        return base - r + c
    }
    else if (c > 0 && r in (-c..c)) { // 4n^2 - 2n + 1
        val baseIndex = c
        val base = 4 * baseIndex * baseIndex - 2 * baseIndex + 1
        return base - c - r
    }
    else if (c < 0 && r in (c..-c)) { // (2n + 1)^2 - 2n
        val baseIndex = -c
        val base = (2 * baseIndex + 1) * (2 * baseIndex + 1) - 2 * baseIndex
        return base + c + r
    } else { // (0, 0)
        return 1
    }
}

fun getNumString(n: Int, maxLen: Int): String {
    return "${" ".repeat(maxLen - n.toString().length)}${n}"
}

fun main() {
    val info = readLine().toString().split(" ")
    val r1 = info[0].toInt()
    val c1 = info[1].toInt()
    val r2 = info[2].toInt()
    val c2 = info[3].toInt()
    
    val array = mutableListOf<MutableList<Int>>()
    (r1..r2).forEach {
        array.add((c1..c2).map { 0 }.toMutableList())
    }
    
    var maxNum = 0
    for (r in r1..r2) {
        for (c in c1..c2) {
            val result = computeNumber(r, c)
            array[r-r1][c-c1] = result
            maxNum = Math.max(maxNum, result)
        }
    }
    val maxLen = maxNum.toString().length
    
    array.forEach {
        val mapToNumString = it.map { n ->
            getNumString(n, maxLen)
        }
        println(mapToNumString.joinToString(" "))
    }
}
```
