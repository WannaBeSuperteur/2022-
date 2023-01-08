## 요구사항 분석
1200부터 1260까지의 각 자연수에 대해서 다음을 구하시오.
* 홀수인 약수의 개수
* 짝수인 약수의 개수
* 홀수인 약수의 합
* 짝수인 약수의 합

또한 이를 이용하여 다음을 각각 구하시오.
* 홀수인 약수의 개수와 짝수인 약수의 합의 최소공배수
* 짝수인 약수의 개수와 홀수인 약수의 합의 최대공약수

## 코드

## 오류 발생 코드
```kotlin
val funcList = listOf(IntArray::sum, IntArray::count)
```
을 이용하면
```kotlin
ERROR: Kotlin reflection is not available
```
오류가 발생한다.


```kotlin
data class Number (
    var value: Int = 0
)

fun getInfo(n: Int, isEvenOrOdd: String, isSum: Boolean): Int {
    val evenOddMod = listOf("even" to listOf(0), "odd" to listOf(1), "all" to listOf(0, 1))
    val funcList = listOf(IntArray::sum, IntArray::count)
    val isSumOrCount = listOf(true, false)
    
    // ERROR: Kotlin reflection is not available
    
    println(funcList.zip(isSumOrCount))
    // isSum이 true이면 sum, false이면 count 함수를 이용
    /*
    funcList.zip(isSumOrCount).forEach { func, isSumOption ->
        println(func)
        if (isSum == isSumOption) {
            (1..n).func {
                return n % it == 0 && (n % 2 in evenOddMod[isEvenOrOdd]!!)
            }
        }
    }
    */
    return -1
}

val Number.divisorList: List<Int> get() = (1..this.value).filter { this.value % it == 0 }
val Number.divisorSum: Int get() = Number::divisorList.get(this).sum()

fun main() {
    println(getInfo(1200, "all", false))
}
```

## 실행 결과
