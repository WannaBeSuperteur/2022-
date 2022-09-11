## 요구사항 분석
```P x Q``` 행렬과 ```Q x R``` 행렬을 곱한 ```P x R``` 행렬을 계산하고, 일반적인 방법과 ```coroutine```을 이용한 방법의 수행 시간을 비교한다.

## 예제 코드
```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.channels.*
import java.util.Random

fun main() {
    val X = Array<Array<Int>>(500){ i ->
        Array<Int>(500){ j ->
        	(1234567890 / ((i + 3) * (j + 4))) % 5
        }
    }
    
    val Y = Array<Array<Int>>(500){ i ->
        Array<Int>(500){ j ->
        	(2109876543 / ((2 * i + 2) * (3 * j + 5))) % 5
        }
    }
    
    // 일반적인 행렬의 곱셈 알고리즘
    multiply(X, Y)
    
    // 코루틴을 이용한 곱셈 알고리즘
    multiplyUsingCoroutine(X, Y)
}

// 일반적인 곱셈
fun multiply(X: Array<Array<Int>>, Y: Array<Array<Int>>) {
    val start = System.currentTimeMillis()
    
    val product = Array(500) { IntArray(500) }
    for (i in 0..499) {
        for (j in 0..499) {
            for (k in 0..499) {
                product[i][j] += X[i][k] * Y[k][j]
            }
        }
    }
    
    val end = System.currentTimeMillis()
    println("\n\ntime = ${end - start} msecs\n")
    
    for (i in 0..4) {
        for (j in 0..4) {
            println("element at ${i},${j} = ${product[i][j]}")
        }
    }
}

// 코루틴을 이용한 곱셈
suspend fun multiply(X: Array<Array<Int>>, Y: Array<Array<Int>>, product: Array<IntArray>) = coroutineScope {
    for (i in 0..499) {
        launch {
            for (j in 0..499) {
                for (k in 0..499) {
                    product[i][j] += X[i][k] * Y[k][j]
                }
            }
        }
    }
}

fun multiplyUsingCoroutine(X: Array<Array<Int>>, Y: Array<Array<Int>>) = runBlocking {
    val start = System.currentTimeMillis()
    val product = Array(500) { IntArray(500) }
    
    // 이 함수가 종료될 때까지 대기한 후 다음이 실행됨
    multiply(X, Y, product)
    
    val end = System.currentTimeMillis()
    println("\n\ntime with coroutine = ${end - start} msecs\n")
    
    for (i in 0..4) {
        for (j in 0..4) {
            println("element at ${i},${j} = ${product[i][j]}")
        }
    }
}
```

## 예제 출력
```kotlin
time = 656 msecs

element at 0,0 = 2134
element at 0,1 = 1965
element at 0,2 = 2127
element at 0,3 = 2023
element at 0,4 = 2046
element at 1,0 = 2055
element at 1,1 = 1800
element at 1,2 = 2011
element at 1,3 = 1877
element at 1,4 = 1986
element at 2,0 = 2207
element at 2,1 = 2097
element at 2,2 = 2148
element at 2,3 = 2134
element at 2,4 = 2019
element at 3,0 = 2115
element at 3,1 = 1871
element at 3,2 = 2109
element at 3,3 = 2006
element at 3,4 = 2077
element at 4,0 = 2142
element at 4,1 = 2012
element at 4,2 = 2023
element at 4,3 = 1981
element at 4,4 = 1969


time with coroutine = 353 msecs

element at 0,0 = 2134
element at 0,1 = 1965
element at 0,2 = 2127
element at 0,3 = 2023
element at 0,4 = 2046
element at 1,0 = 2055
element at 1,1 = 1800
element at 1,2 = 2011
element at 1,3 = 1877
element at 1,4 = 1986
element at 2,0 = 2207
element at 2,1 = 2097
element at 2,2 = 2148
element at 2,3 = 2134
element at 2,4 = 2019
element at 3,0 = 2115
element at 3,1 = 1871
element at 3,2 = 2109
element at 3,3 = 2006
element at 3,4 = 2077
element at 4,0 = 2142
element at 4,1 = 2012
element at 4,2 = 2023
element at 4,3 = 1981
element at 4,4 = 1969
```
