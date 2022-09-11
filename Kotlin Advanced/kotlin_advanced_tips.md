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

## 3. ```list.chunked(n)```으로 다차원 배열 만들기
```N```개의 원소가 있는 배열 ```list```에 대해, ```list.chunked(a)```는 다음의 shape를 갖는 2차원 배열을 반환한다. (단, ```N```은 ```a```의 배수)
```kotlin
(N / a, a)
```
이것은 원소가 ```a```개인 배열을 ```N / a```개 포함하는 2차원 배열이다.

```N```개의 원소가 있는 배열 ```list```에 대해, ```list.chunked(a).chunked(b)```는 다음의 shape를 갖는 3차원 배열을 반환한다. (단, ```N```은 ```a * b```의 배수)
```kotlin
(N / (a * b), b, a)
```
이것은 원소가 ```a```개인 배열을 ```b```개 포함하는 2차원 배열을 ```N / (a * b)```개 포함하는 3차원 배열이다.

```N```개의 원소가 있는 배열 ```list```에 대해, ```list.chunked(a).chunked(b).chunked(c)```는 다음의 shape를 갖는 4차원 배열을 반환한다. (단, ```N```은 ```a * b * c```의 배수)
```kotlin
(N / (a * b * c), c, b, a)
```
이것은 원소가 ```a```개인 배열을 ```b```개 포함하는 2차원 배열을 ```c```개 포함하는 3차원 배열을 ```N / (a * b * c)```개 포함하는 4차원 배열이다.

### 예시 코드
```kotlin
fun main() {
    
    // 원소가 42개인 리스트
    val x = listOf(1, 2, 7, 2, 5, 8, 16, 15, 30, 14, 12, 25, 6, 8, 17, 8, 10, 20, 100, 102, 214, 97, 118, 297, 5, 6, 10, 4, 7, 12, 1, 1, 4, 1, 2, 6, 7, 7, 15, 8, 6, 13)
    
    println(" <<< 2d arrays >>>")
    println("42 x 1 = " + x.chunked(1).toString() + "\n") // (42 / 1, 1) = (42, 1)
    println("21 x 2 = " + x.chunked(2).toString() + "\n") // (42 / 2, 2) = (21, 2)
    println("14 x 3 = " + x.chunked(3).toString() + "\n") // (42 / 3, 2) = (14, 3)
    println("7 x 6 = " + x.chunked(6).toString() + "\n") // (42 / 6, 2) = (7, 6)
    print("\n")
    
    println(" <<< 3d arrays >>>")
    println("7 x 1 x 6 = " + x.chunked(6).chunked(1).toString() + "\n") // (42 / (6 * 1), 1, 6) = (7, 1, 6)
    println("7 x 2 x 3 = " + x.chunked(3).chunked(2).toString() + "\n") // (42 / (3 * 2), 2, 3) = (7, 2, 3)
    println("7 x 3 x 2 = " + x.chunked(2).chunked(3).toString() + "\n") // (42 / (2 * 3), 3, 2) = (7, 3, 2)
    println("7 x 6 x 1 = " + x.chunked(1).chunked(6).toString() + "\n") // (42 / (1 * 6), 6, 1) = (7, 6, 1)
    print("\n")
    
    println(" <<< 4d arrays >>>")
    println("7 x 1 x 2 x 3 = " + x.chunked(3).chunked(2).chunked(1).toString() + "\n") // (42 / (3 * 2 * 1), 1, 2, 3) = (7, 1, 2, 3)
    println("7 x 2 x 3 x 1 = " + x.chunked(1).chunked(3).chunked(2).toString() + "\n") // (42 / (1 * 3 * 2), 2, 3, 1) = (7, 2, 3, 1)
    print("\n")
}
```

### 예시 출력
```kotlin
 <<< 2d arrays >>>
42 x 1 = [[1], [2], [7], [2], [5], [8], [16], [15], [30], [14], [12], [25], [6], [8], [17], [8], [10], [20], [100], [102], [214], [97], [118], [297], [5], [6], [10], [4], [7], [12], [1], [1], [4], [1], [2], [6], [7], [7], [15], [8], [6], [13]]

21 x 2 = [[1, 2], [7, 2], [5, 8], [16, 15], [30, 14], [12, 25], [6, 8], [17, 8], [10, 20], [100, 102], [214, 97], [118, 297], [5, 6], [10, 4], [7, 12], [1, 1], [4, 1], [2, 6], [7, 7], [15, 8], [6, 13]]

14 x 3 = [[1, 2, 7], [2, 5, 8], [16, 15, 30], [14, 12, 25], [6, 8, 17], [8, 10, 20], [100, 102, 214], [97, 118, 297], [5, 6, 10], [4, 7, 12], [1, 1, 4], [1, 2, 6], [7, 7, 15], [8, 6, 13]]

7 x 6 = [[1, 2, 7, 2, 5, 8], [16, 15, 30, 14, 12, 25], [6, 8, 17, 8, 10, 20], [100, 102, 214, 97, 118, 297], [5, 6, 10, 4, 7, 12], [1, 1, 4, 1, 2, 6], [7, 7, 15, 8, 6, 13]]


 <<< 3d arrays >>>
7 x 1 x 6 = [[[1, 2, 7, 2, 5, 8]], [[16, 15, 30, 14, 12, 25]], [[6, 8, 17, 8, 10, 20]], [[100, 102, 214, 97, 118, 297]], [[5, 6, 10, 4, 7, 12]], [[1, 1, 4, 1, 2, 6]], [[7, 7, 15, 8, 6, 13]]]

7 x 2 x 3 = [[[1, 2, 7], [2, 5, 8]], [[16, 15, 30], [14, 12, 25]], [[6, 8, 17], [8, 10, 20]], [[100, 102, 214], [97, 118, 297]], [[5, 6, 10], [4, 7, 12]], [[1, 1, 4], [1, 2, 6]], [[7, 7, 15], [8, 6, 13]]]

7 x 3 x 2 = [[[1, 2], [7, 2], [5, 8]], [[16, 15], [30, 14], [12, 25]], [[6, 8], [17, 8], [10, 20]], [[100, 102], [214, 97], [118, 297]], [[5, 6], [10, 4], [7, 12]], [[1, 1], [4, 1], [2, 6]], [[7, 7], [15, 8], [6, 13]]]

7 x 6 x 1 = [[[1], [2], [7], [2], [5], [8]], [[16], [15], [30], [14], [12], [25]], [[6], [8], [17], [8], [10], [20]], [[100], [102], [214], [97], [118], [297]], [[5], [6], [10], [4], [7], [12]], [[1], [1], [4], [1], [2], [6]], [[7], [7], [15], [8], [6], [13]]]


 <<< 4d arrays >>>
7 x 1 x 2 x 3 = [[[[1, 2, 7], [2, 5, 8]]], [[[16, 15, 30], [14, 12, 25]]], [[[6, 8, 17], [8, 10, 20]]], [[[100, 102, 214], [97, 118, 297]]], [[[5, 6, 10], [4, 7, 12]]], [[[1, 1, 4], [1, 2, 6]]], [[[7, 7, 15], [8, 6, 13]]]]

7 x 2 x 3 x 1 = [[[[1], [2], [7]], [[2], [5], [8]]], [[[16], [15], [30]], [[14], [12], [25]]], [[[6], [8], [17]], [[8], [10], [20]]], [[[100], [102], [214]], [[97], [118], [297]]], [[[5], [6], [10]], [[4], [7], [12]]], [[[1], [1], [4]], [[1], [2], [6]]], [[[7], [7], [15]], [[8], [6], [13]]]]
```
