## groupBy function
### groupBy function Basic
```kotlin
list.groupBy { condition }
```
리스트 또는 콜렉션 ```list```의 값들을 조건 ```condition```에 따라 그룹으로 나누어서 다음과 같이 반환한다.

```kotlin
{group1=[value11, value12, ...], group2=[value21, value22, ...], ...}
```

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
    developers.add(Developer("muk", "C++", 36, 42))
    developers.add(Developer("ho", "C++", 24, 48))
    developers.add(Developer("hyun", "Java", 24, 60))
    developers.add(Developer("uk", "Python3", 17, 52))
    developers.add(Developer("jeong", "C++", 10, 51))
    developers.add(Developer("jin", "Python3", 26, 44))
    
    // 사용 프로그래밍 언어로 분류
    val groups1 = developers.groupBy {
        it.language
    }
    println("1. group by language :")
    println(groups1)
    
    // 개별 출력
    println("Kotlin  : " + groups1["Kotlin"])
    println("Java    : " + groups1["Java"])
    println("C++     : " + groups1["C++"])
    println("Python3 : " + groups1["Python3"] + "\n")
    
    // 개발 시간으로 분류
    val groups2 = developers.groupBy {
        it.hoursPerWeek / 10
    }
    println("2. group by working time :")
    println(groups2)
    
    // 개별 출력
    println("40 ~ 49 : " + groups2[4])
    println("50 ~ 59 : " + groups2[5])
    println("60 ~ 69 : " + groups2[6] + "\n")
    
    val strList = listOf("one", "two", "three", "four", "five", "six", "seven", "eleven")
    
    // 첫 글자로 분류
    val groups3 = strList.groupBy {
        it.first()
    }
    println("1. group by first letter :")
    println(groups3.toString() + "\n")
    
    // 마지막 글자로 분류
    val groups4 = strList.groupBy {
        it.last()
    }
    println("2. group by first letter :")
    println(groups4.toString() + "\n")
}
```

## Output
### Output 1
```kotlin
1. group by language :
{Kotlin=[Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45)], Java=[Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)], C++=[Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)], Python3=[Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)]}
Kotlin  : [Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45)]
Java    : [Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)]
C++     : [Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)]
Python3 : [Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)]

2. group by working time :
{4=[Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45), Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)], 6=[Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)], 5=[Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)]}
40 ~ 49 : [Developer(name=hong, language=Kotlin, months=8, hoursPerWeek=45), Developer(name=yoon, language=Java, months=30, hoursPerWeek=40), Developer(name=muk, language=C++, months=36, hoursPerWeek=42), Developer(name=ho, language=C++, months=24, hoursPerWeek=48), Developer(name=jin, language=Python3, months=26, hoursPerWeek=44)]
50 ~ 59 : [Developer(name=uk, language=Python3, months=17, hoursPerWeek=52), Developer(name=jeong, language=C++, months=10, hoursPerWeek=51)]
60 ~ 69 : [Developer(name=hyun, language=Java, months=24, hoursPerWeek=60)]

1. group by first letter :
{o=[one], t=[two, three], f=[four, five], s=[six, seven], e=[eleven]}

2. group by first letter :
{e=[one, three, five], o=[two], r=[four], x=[six], n=[seven, eleven]}
```


