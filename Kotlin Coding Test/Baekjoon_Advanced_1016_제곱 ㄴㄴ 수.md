Problem Source : https://www.acmicpc.net/problem/1016
```kotlin
fun getPrimes(n: Int, primes: List<Int>): List<Int> {
    val newPrimes = mutableListOf<Int>()
    val maxPrime = primes.maxOrNull() ?: 2
    
    for (i in maxPrime until n) {
        var isPrime = true
        
        for (j in 0 until primes.size) {
            if (i % primes[j] == 0) {
                isPrime = false
                break
            }
            if (primes[j] * primes[j] >= i) {
                break
            }
        }
        
        if (isPrime) {
            newPrimes.add(i)
        }
    }
    
    return primes + newPrimes
}

fun main() {
    val minAndMax = readLine().toString().split(" ")
    val min = minAndMax[0].toLong()
    val max = minAndMax[1].toLong()

    // 1. primes < 31
    val primes0 = listOf(2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31)
    
    // 2. 31 < primes < 1000
    val primes1 = getPrimes(1000, primes0)
    // println(primes1.take(50))
    
    // 3. 1000 < primes < 1000000
    val primes2 = getPrimes(1000000, primes1).map { it.toLong() }
    // println(primes2.take(50))
    
    // 4. remove squared numbers from min to max
    var nums = (min until max + 1).toList()
    var count = max - min + 1
    val check = nums.map { true }.toMutableList()
    
    primes2.forEach { p ->
        val sqp = p.toLong() * p.toLong()
        
        // start position of checking (around min)
        var numToCheck = min / sqp * sqp
        if (numToCheck < min) {
            numToCheck += sqp
        }
        
        // check numbers between min and max
        while (numToCheck <= max) {
            val index = (numToCheck - min).toInt()
            if (check[index]) {
                check[index] = false
                count--
            }
            numToCheck += sqp
        }
    }
    
    println(count)
}
```
