## all, any, count, none of Kotlin
```kotlin
val test = { arg: DataType ->
    condition
}
list.all(test) // list의 모든 값이 test의 조건을 만족시키면 true
list.any(test) // list의 값 중 하나라도 test의 조건을 만족시키면 true
list.count(test) // list의 값 중 test의 조건을 만족시키는 것의 개수 찾기
list.none(test) // list의 모든 값이 test의 조건을 만족시키지 않으면 true
```

여기서 각각은 다음과 같다.
* ```test```: ```list```의 각 값이 조건 ```condition```을 만족시키는지 검사하는 함수
* ```arg```: ```list```의 각 값을 나타내는 인수
* ```DataType```: ```arg```의 자료형이자 ```list```의 각 원소의 자료형

### all
```kotlin
list.all(test)
```
위 함수는 ```list```의 모든 값이 ```test```의 조건을 만족시키면 true, 그렇지 않으면 false를 반환한다.

### any
```kotlin
list.any(test)
```
위 함수는 ```list```의 값이 하나라도 ```test```의 조건을 만족시키면 true, 그렇지 않으면 false를 반환한다.

### count
```kotlin
list.count(test)
```
위 함수는 ```list```의 값 중 ```test```의 조건을 만족시키는 것의 개수를 구한다.

### none
```kotlin
list.none(test)
```
위 함수는 ```list```의 모든 값이 ```test```의 조건을 만족시키지 **않으면** true, 그렇지 않으면 false를 반환한다.

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
    
    val hardWorker = { dev: Developer ->
        dev.hoursPerWeek >= 42 && dev.months >= 12
    }
    
    println("is ALL hard worker? : " + developers.all(hardWorker))
    println("is ANY hard worker? : " + developers.any(hardWorker))
    println("COUNT of hard worker : " + developers.count(hardWorker))
    println("is NONE hard worker? : " + developers.none(hardWorker))
    println("hard workers : " + developers.filter(hardWorker)) // 참고 사항
}
```

## Output
### Output 1
```kotlin
is ALL hard worker? : false
is ANY hard worker? : true
COUNT of hard worker : 2
is NONE hard worker? : false
hard workers : [Developer(name=muk, language=Python3, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48)]
```

