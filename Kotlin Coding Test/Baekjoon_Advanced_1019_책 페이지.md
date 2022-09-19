Problem Source : https://www.acmicpc.net/problem/1019

```kotlin
import kotlin.math.*

fun count(N: Int, place: Int, num: Int): Int {
    val base = (10.0).pow(place).toInt()
    val multiple = N / base
    val remainder = N % base

    return when (multiple % 10) {
        in 0..num-1 -> base * (multiple / 10)
        num -> base * (multiple / 10) + (remainder + 1)
        else -> base * (multiple / 10 + 1)
    }
}

fun countZero(N: Int, place: Int): Int {
    val base = (10.0).pow(place).toInt()
    val multiple = N / base
    val remainder = N % base
    
    if (multiple < 10) {
        return 0
    } else if (multiple % 10 == 0) {
        return base * (multiple / 10 - 1) + (remainder + 1)
    } else {
        return base * (multiple / 10)
    }
}

fun main() {
    val N = readLine().toString().toInt()
    val subN = Math.min(N, 999999999)
    
    val cnts = (0..9).map { num ->
        if (num == 0) {
            (0..8).sumOf { place ->
                countZero(subN, place)
            }
        } else {
            (0..8).sumOf { place ->
                count(subN, place, num)
            }
        }
    }.toMutableList()
    
    if (N == 1000000000) {
        cnts[0] += 9
        cnts[1]++
    }
    
    println(cnts.joinToString(separator=" "))
}
```
