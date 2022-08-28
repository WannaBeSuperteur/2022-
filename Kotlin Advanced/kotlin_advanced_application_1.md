## refer to
* ```kotlin_advanced_lambda.md```
* ```kotlin_advanced_map.md```
* ```kotlin_advanced_groupby.md```

## Example 1
```kotlin
data class Developer (
    val name: String,
    val language: String,
    val months: Int,
    val hoursPerWeek: Int
)

fun main() {
    
    // 배열의 평균 구하기 (참고: kotlin_advanced_lambda.md)
    val meanWorkTime : IntArray.() -> Double = {
        this.sum() / this.size.toDouble()
    }
    
    val developers = mutableListOf<Developer>()
    developers.add(Developer("hong", "Kotlin", 8, 45))
    developers.add(Developer("yoon", "Java", 30, 40))
    developers.add(Developer("muk", "C++", 36, 42))
    developers.add(Developer("ho", "C++", 24, 48))
    developers.add(Developer("hyun", "Java", 24, 60))
    developers.add(Developer("uk", "Python3", 17, 52))
    developers.add(Developer("jeong", "C++", 10, 51))
    developers.add(Developer("jin", "Python3", 26, 44))
    
    // 사용 프로그래밍 언어로 분류 (참고: kotlin_advanced_groupby.md)
    val groups1 = developers.groupBy {
        it.language
    }
    println("1. group by language :")
    println(groups1.toString() + "\n")
    
    // 개별 출력
    println("Kotlin  : " + groups1["Kotlin"])
    println("Java    : " + groups1["Java"])
    println("C++     : " + groups1["C++"])
    println("Python3 : " + groups1["Python3"] + "\n")
    
    // 개발 시간 (참고: kotlin_advanced_map.md)
    val kotlinTime = groups1["Kotlin"]!!.map { it -> it.hoursPerWeek }.toIntArray()
    val javaTime = groups1["Java"]!!.map { it -> it.hoursPerWeek }.toIntArray()
    val cppTime = groups1["C++"]!!.map { it -> it.hoursPerWeek }.toIntArray()
    val python3Time = groups1["Python3"]!!.map { it -> it.hoursPerWeek }.toIntArray()
    
    // 평균 개발 시간
    println("Kotlin  : " + kotlinTime.meanWorkTime().toString())
    println("Java    : " + javaTime.meanWorkTime().toString())
    println("C++     : " + cppTime.meanWorkTime().toString())
    println("Python3 : " + python3Time.meanWorkTime().toString() + "\n") 
    
    // 개발 시간으로 분류 (참고: kotlin_advanced_groupby.md)
    val groups2 = developers.groupBy {
        it.hoursPerWeek / 10
    }
    println("2. group by working time :")
    println(groups2.toString() + "\n")
    
    // 개별 출력
    println("40 ~ 49 : " + groups2[4])
    println("50 ~ 59 : " + groups2[5])
    println("60 ~ 69 : " + groups2[6] + "\n")
    
    // 개발 시간 (참고: kotlin_advanced_map.md)
    val fortyTime = groups2[4]!!.map { it -> it.hoursPerWeek }.toIntArray()
    val fiftyTime = groups2[5]!!.map { it -> it.hoursPerWeek }.toIntArray()
    val sixtyTime = groups2[6]!!.map { it -> it.hoursPerWeek }.toIntArray()
    
    // 평균 개발 시간
    println("40 ~ 49 : " + fortyTime.meanWorkTime().toString())
    println("50 ~ 59 : " + fiftyTime.meanWorkTime().toString())
    println("60 ~ 69 : " + sixtyTime.meanWorkTime().toString() + "\n")
}
```

## Output 1
```kotlin
1. group by language :
{Kotlin=[Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45)], Java=[Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)], C++=[Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)], Python3=[Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)]}

Kotlin  : [Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45)]
Java    : [Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)]
C++     : [Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)]
Python3 : [Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)]

Kotlin  : 45.0
Java    : 50.0
C++     : 47.0
Python3 : 48.0

2. group by working time :
{4=[Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45), Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)], 6=[Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)], 5=[Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)]}

40 ~ 49 : [Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45), Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)]
50 ~ 59 : [Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)]
60 ~ 69 : [Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)]

40 ~ 49 : 43.8
50 ~ 59 : 51.5
60 ~ 69 : 60.0
```
