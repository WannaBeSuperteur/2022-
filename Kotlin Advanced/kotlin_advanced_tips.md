## 1. list 또는 collection에 대해 true/false 배열 반환하기
```kotlin
list.map { it ->
    condition
}
```
을 적용하면 콜렉션 ```list```의 각 원소가 조건 ```condition```을 만족시키는지의 여부를 담은 ```true```/```false```의 배열을 반환한다.

### 예시 코드
```kotlin
private fun isPrime(n: Int) : Boolean {
    if (n == 0 || n == 1) return false
    else {
        val sqrtn = Math.sqrt(n.toDouble()).toInt()
        
        for (i in 2..sqrtn) {
            if (n % i == 0) {
                return false
            }
        }
    }
    return true
}

fun main() {
    val listNum = listOf(1, 5, 10, 15, 50, 100)
    
    // 짝수인지 판단
	val isEven = listNum.map { it ->
    	it % 2 == 0
    }
    println("isEven = ${isEven}")
    
    // 홀수인지 판단
    val isOdd = listNum.map { it ->
    	it % 2 == 1
    }
    println("isOdd = ${isOdd}") 
    
    // 소수인지 판단
    val isPr = listNum.map { it ->
    	isPrime(it)
    }
    println("isPrime = ${isPr}")
}
```

### 예시 출력
```kotlin
isEven = [false, false, true, false, true, true]
isOdd = [true, true, false, true, false, false]
isPrime = [false, true, false, false, false, false]
```
