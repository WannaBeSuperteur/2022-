## getOrElse
### getOrElse Basic
```kotlin
list.getOrElse(index) { value }
```
위 코드는 콜렉션 ```list```의 ```index```번째 인덱스에 있는 값을 반환한다. 만약 그 값이 없으면 ```value```를 반환한다.

## Example
### Example 1
```kotlin
fun main() {
    val primes = listOf(2, 3, 5, 7, 11, 13, 17, 19, 23, 29)
    
    val firstPrime = primes.getOrElse(0) {-1}
    val secondPrime = primes.getOrElse(1) {-1}
    val thirdPrime = primes.getOrElse(2) {-1}
    val largePrime = primes.getOrElse(100) {-1}
    val veryLargePrime = primes.getOrElse(10000) {-1}
    
    println("first      prime number = ${firstPrime}")
    println("second     prime number = ${secondPrime}")
    println("third      prime number = ${thirdPrime}")
    println("large      prime number = ${largePrime}")
    println("very large prime number = ${veryLargePrime}")
}
```

### Example 2
```kotlin
data class Developer (
    val name: String,
    val language: String,
    val months: Int,
    val hoursPerWeek: Int
)

fun main() {
    val developers = mutableListOf<Developer>()
    developers.add(Developer("hong", "Kotlin", 8, 45))
    developers.add(Developer("yoon", "Java", 30, 40))
    developers.add(Developer("muk", "Python3", 36, 42))
    developers.add(Developer("ho", "C++", 24, 48))
    developers.add(Developer("won", "C", 16, 44))
    
    for (i in 0..9) {
        
        // 개발자를 찾고, 없으면 "no more developers"를 반환
        val dev = developers.getOrElse(i) {"no more developers"}
        
        when (dev) {
            "no more developers" -> { // 해당 인덱스의 개발자가 없음
                println("no such developer")
            }
            else -> { // 개발자가 있음
                println("developer name : ${(dev as Developer).name}")
            }
        }
        
    }
}
```

## Output
### Output 1
```kotlin
first      prime number = 2
second     prime number = 3
third      prime number = 5
large      prime number = -1
very large prime number = -1
```

### Output 2
```
developer name : hong
developer name : yoon
developer name : muk
developer name : ho
developer name : won
no such developer
no such developer
no such developer
no such developer
no such developer
```
