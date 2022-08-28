## Map Function
### Map Function Basic
```kotlin
list.map {
    // 원래 값 it을 변형한 값
}
```
콜렉션 또는 리스트 ```list```의 각 값에 대해서, 원래 값을 ```it```이라고 하고 그것을 각각 변형한 값을 저장한 배열을 반환한다.

## Example
### Example 1
```kotlin
import kotlin.math.pow

data class Developer (
    val name: String,
    val language: String,
    val months: Int,
    val hoursPerWeek: Int
)

fun main() {
    val numbers = listOf<Int>(1, 2, 3, 4, 5, 10)
    
    // 삼각수
    val triangle = numbers.map { it * (it + 1) / 2 }
    println("1. triangular numbers = " + triangle)
    
    // 1부터 n까지의 제곱의 합
    val squareSum = numbers.map { it * (it + 1) * (2 * it + 1) / 6 }
    println("2. sum of 1^2, 2^2, ..., n^2 = " + squareSum)
    
    // 2의 n제곱
    val powerOf2 = numbers.map { 2.0.pow(it).toInt() }
    println("3. power of two = " + powerOf2)
    
    val developers = mutableListOf<Developer>()
    developers.add(Developer("hong", "Kotlin", 8, 45))
    developers.add(Developer("yoon", "Java", 30, 40))
    developers.add(Developer("muk", "Python3", 36, 42))
    developers.add(Developer("ho", "C++", 24, 48))
    
    // map을 통한 이름 출력
    val names = developers.map { it.name }
    println("4. developer names = " + names)
    
    // map을 통한 주당 개발 시간 출력
    val hrsPerWeek = developers.map { it.hoursPerWeek }
    println("5. developer hrs/week = " + hrsPerWeek)
    
    // map을 통한 min(60, 120 - months) / (1.5 * hoursPerWeek)의 값 출력
    val myValue = developers.map { 
        Math.min(60, 120 - it.months) / (1.5 * it.hoursPerWeek).toDouble()
    }
    println("6. test my value = " + myValue)
}
```

## Output
### Output 1
```kotlin
1. triangular numbers = [1, 3, 6, 10, 15, 55]
2. sum of 1^2, 2^2, ..., n^2 = [1, 5, 14, 30, 55, 385]
3. power of two = [2, 4, 8, 16, 32, 1024]
4. developer names = [hong, yoon, muk, ho]
5. developer hrs/week = [45, 40, 42, 48]
6. test my value = [0.8888888888888888, 1.0, 0.9523809523809523, 0.8333333333333334]
```
