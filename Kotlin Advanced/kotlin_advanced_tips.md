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

## 2. data class
data class는 다음을 기본적으로 지원한다.
* getter, setter (```instance.property = ...```, ```... = instance.property```)
* ```equals()``` : 두 instance가 서로 같은지 비교하는 함수
* ```hashCode()```
* ```toString()``` : instance를 ```String``` 자료형의 형태로 표현하는 함수

### 예시 코드
```kotlin
fun main() {
    val dev0 = Developer("hong", "Kotlin", "Android", 350)
    val dev1 = Developer("yoon", "Java", "Android", 335)
    val dev2 = Developer("god_dev", "Kotlin", "Android", 400)
    val dev3 = Developer("wow", "Java", "Android", 335)
    
    println("test 1 : dev1 == dev3, ${dev1.equals(dev3)}, ${dev3.equals(dev1)}")
    println("test 2 : dev1.hashCode(), ${dev1.hashCode()}")
    println("test 3 : dev3.hashCode(), ${dev3.hashCode()}")
    dev1.name = "wow"
    println("test 4 : dev1 == dev3, ${dev1.equals(dev3)}, ${dev3.equals(dev1)}")
    println("test 5 : dev0.toString(), ${dev0}, ${dev0.toString()}")
    println("test 6 : dev0.hashCode(), ${dev0.hashCode()}")
    println("test 7 : dev1.hashCode(), ${dev1.hashCode()}")
    println("test 8 : dev3.hashCode(), ${dev3.hashCode()}")
    println("test 9 : dev2.name, ${dev2.name}")
    println("test 10 : dev3.language, ${dev3.language}")
}
```

### 예시 출력
```kotlin
test 1 : dev1 == dev3, false, false
test 2 : dev1.hashCode(), 345486093
test 3 : dev3.hashCode(), 561024419
test 4 : dev1 == dev3, true, true
test 5 : dev0.toString(), Developer(name=hong, language=Kotlin, team_name=Android, coding_test_score=350), Developer(name=hong, language=Kotlin, team_name=Android, coding_test_score=350)
test 6 : dev0.hashCode(), 944318672
test 7 : dev1.hashCode(), 561024419
test 8 : dev3.hashCode(), 561024419
test 9 : dev2.name, god_dev
test 10 : dev3.language, Java
```
