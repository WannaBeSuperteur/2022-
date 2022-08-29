## find, findLast
### find, findLast Basic
```kotlin
list.find {
    condition // it으로 각각의 원소를 표현
}
```
위 함수는 콜렉션 ```list```에서 조건 ```condition```이 참인 (인덱스상의) **첫 번째** 원소를 반환한다.

```kotlin
list.findLast {
    condition // it으로 각각의 원소를 표현
}
```
위 함수는 콜렉션 ```list```에서 조건 ```condition```이 참인 (인덱스상의) **마지막** 원소를 반환한다.

## Example
### Example 1
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
    
    // find the FIRST developer whose months is at least 20
   	val firstRealProDeveloper = developers.find {
        it.months >= 20
    }
    println("first dev with months >= 20 : " + firstRealProDeveloper?.name)
    
    // find the LAST developer whose months is at least 20
   	val lastRealProDeveloper = developers.findLast {
        it.months >= 20
    }
    println("last dev with months >= 20 : " + lastRealProDeveloper?.name)

    // find the FIRST developer whose hours per week is at least 45
    val firstHardWorkingDeveloper = developers.find {
        it.hoursPerWeek >= 45
    }
    println("first hard working dev : " + firstHardWorkingDeveloper?.name)
    
    // find the LAST developer whose hours per week is at least 45
    val lastHardWorkingDeveloper = developers.findLast {
        it.hoursPerWeek >= 45
    }
    println("last hard working dev : " + lastHardWorkingDeveloper?.name)
    
    // find the FIRST developer whose hours per week is at least 60
    val firstVeryHardWorkingDeveloper = developers.find {
        it.hoursPerWeek >= 60
    }
    println("first very hard working dev : " + firstVeryHardWorkingDeveloper?.name)
    
    val arr = listOf(20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35)
    
    // find the first prime number
    val firstPrime = arr.find {
        (it % 2 != 0) && (it % 3 != 0) && (it % 5 != 0)
    }
    println("the first prime number = " + firstPrime)
    
    // find the last prime number
    val lastPrime = arr.findLast {
        (it % 2 != 0) && (it % 3 != 0) && (it % 5 != 0)
    }
    println("the last prime number = " + lastPrime)
    
    // find the first prime number less than 23 or greather than 32
    val firstPrimeNotBetween = arr.find {
        (it % 2 != 0) && (it % 3 != 0) && (it % 5 != 0) && (it < 23 || it > 32)
    }
    println("the first prime number less than 23 or greather than 32 = " + firstPrimeNotBetween)
}
```

## Output
### Output 1
```
first dev with months >= 20 : yoon
last dev with months >= 20 : ho
first hard working dev : hong
last hard working dev : ho
first very hard working dev : null
the first prime number = 23
the last prime number = 31
the first prime number less than 23 or greather than 32 = null
```
