## FlatMap
### FlatMap Basic
```kotlin
list1.flatMap {
    list2 // it을 이용하여 list1의 각 항목을 표현
}
```
위 코드는 ```list1```의 첫 번째 원소부터 마지막 원소까지 순서대로, 그 원소를 ```it```으로 하여 만든 ```list2```를 순서대로 이어붙인 배열을 구한다.

예를 들어
```kotlin
val list1 = listOf(1, 2, 3, 4, 5)
val list2 = list1.flatMap {
    listOf(it, it * 2, it * 3)
}
```
이라고 한 경우,
* 먼저 ```list1```의 첫 번째 원소 ```1```을 ```it```으로 하여 ```listOf(it, it * 2, it * 3)```을 하면 ```listOf(1, 2, 3)```이 된다.
* ```list1```의 두 번째 원소 ```2```을 ```it```으로 하여 ```listOf(it, it * 2, it * 3)```을 하면 ```listOf(2, 4, 6)```이 된다.
* ...
* ```list1```의 마지막 다섯 번째 원소 ```5```을 ```it```으로 하여 ```listOf(it, it * 2, it * 3)```을 하면 ```listOf(5, 10, 15)```이 된다.

이것을 모두 이어붙이면 ```listOf(1, 2, 3, 2, 4, 6, ..., 5, 10, 15)```가 된다.

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

    // months and hours per week
    val numerics = developers.flatMap {
        listOf(it.months, it.hoursPerWeek)
    }
    println("numerics : " + numerics)
    
    // app projects
    val projects = developers.flatMap {
        listOf("${it.name}'s private app", "${it.name}'s company app using ${it.language}")
    }
    println("projects : " + projects)
    
    // test 1
    val messages = developers.flatMap {
        listOf("I love ${it.language}.", "[${it.name}] language: ${it.language}")
    }
    println("messages : " + messages)
    
    // test 2
    val x = listOf(1, 10, 100, 1000, 10000)
    val xy = x.flatMap {
        listOf(it, (2.5 * it).toInt(), 5 * it)
    }
    println("xy : ${xy}")
    
    // test 3
    val letters = listOf("A", "B", "C", "D")
    val allLetters = letters.flatMap {
        listOf("${it}+", it, "${it}-")
    }
    println("all letters : ${allLetters}")
}
```

## Output
### Output 1
```
numerics : [8, 45, 30, 40, 36, 42, 24, 48]
projects : [hong's private app, hong's company app using Kotlin, yoon's private app, yoon's company app using Java, muk's private app, muk's company app using Python3, ho's private app, ho's company app using C++]
messages : [I love Kotlin., [hong] language: Kotlin, I love Java., [yoon] language: Java, I love Python3., [muk] language: Python3, I love C++., [ho] language: C++]
xy : [1, 2, 5, 10, 25, 50, 100, 250, 500, 1000, 2500, 5000, 10000, 25000, 50000]
all letters : [A+, A, A-, B+, B, B-, C+, C, C-, D+, D, D-]
```
