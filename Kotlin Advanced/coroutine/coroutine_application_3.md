## 요구사항 분석
```P x Q``` 행렬과 ```Q x R``` 행렬을 곱한 ```P x R``` 행렬을 계산하고, 일반적인 방법과 ```coroutine```을 이용한 방법의 수행 시간을 비교한다.

## 예제 코드
```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.channels.*
import java.util.Random

val SIZE = 500
val COROUTINES = 20

fun main() {
    val X = Array<Array<Int>>(SIZE){ i ->
        Array<Int>(SIZE){ j ->
            (1234567890 / ((i + 3) * (j + 4))) % 5
        }
    }
    
    val Y = Array<Array<Int>>(SIZE){ i ->
        Array<Int>(SIZE){ j ->
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
    
    val product = Array(SIZE) { IntArray(SIZE) }
    for (i in 0..SIZE-1) {
        for (j in 0..SIZE-1) {
            for (k in 0..SIZE-1) {
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
    val sizePerCoroutine = SIZE / COROUTINES
    println("\n\nsize = ${SIZE}, coroutines = ${COROUTINES}, size per coroutine = ${sizePerCoroutine}")
    
    for (c in 0..COROUTINES-1) {
        launch {
            val start = c * sizePerCoroutine
            val end = start + sizePerCoroutine - 1
            
            for (i in start..end) {   
                for (j in 0..SIZE-1) {
                    for (k in 0..SIZE-1) {
                        product[i][j] += X[i][k] * Y[k][j]
                    }
                }
            }
        }
    }
}

fun multiplyUsingCoroutine(X: Array<Array<Int>>, Y: Array<Array<Int>>) = runBlocking {
    val start = System.currentTimeMillis()
    val product = Array(SIZE) { IntArray(SIZE) }
    
    // 이 함수가 종료될 때까지 대기한 후 다음이 실행됨
    multiply(X, Y, product)
    
    val end = System.currentTimeMillis()
    println("time with coroutine = ${end - start} msecs\n")
    
    for (i in 0..4) {
        for (j in 0..4) {
            println("element at ${i},${j} = ${product[i][j]}")
        }
    }
}
```

## 예제 출력
SIZE=```500```, COROUTINES=```20```일 때
```kotlin
time = 370 msecs

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


size = 500, coroutines = 20, size per coroutine = 25
time with coroutine = 300 msecs

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

SIZE=```1000```, COROUTINES=```25```일 때
```kotlin
time = 2952 msecs

element at 0,0 = 4059
element at 0,1 = 3805
element at 0,2 = 4006
element at 0,3 = 3956
element at 0,4 = 3903
element at 1,0 = 4176
element at 1,1 = 3779
element at 1,2 = 4000
element at 1,3 = 3989
element at 1,4 = 3876
element at 2,0 = 4280
element at 2,1 = 4170
element at 2,2 = 4143
element at 2,3 = 4132
element at 2,4 = 3981
element at 3,0 = 4107
element at 3,1 = 3806
element at 3,2 = 4020
element at 3,3 = 3967
element at 3,4 = 3924
element at 4,0 = 4309
element at 4,1 = 3942
element at 4,2 = 3993
element at 4,3 = 4048
element at 4,4 = 3751


size = 1000, coroutines = 25, size per coroutine = 40
time with coroutine = 2173 msecs

element at 0,0 = 4059
element at 0,1 = 3805
element at 0,2 = 4006
element at 0,3 = 3956
element at 0,4 = 3903
element at 1,0 = 4176
element at 1,1 = 3779
element at 1,2 = 4000
element at 1,3 = 3989
element at 1,4 = 3876
element at 2,0 = 4280
element at 2,1 = 4170
element at 2,2 = 4143
element at 2,3 = 4132
element at 2,4 = 3981
element at 3,0 = 4107
element at 3,1 = 3806
element at 3,2 = 4020
element at 3,3 = 3967
element at 3,4 = 3924
element at 4,0 = 4309
element at 4,1 = 3942
element at 4,2 = 3993
element at 4,3 = 4048
element at 4,4 = 3751
```

SIZE=```1000```, COROUTINES=```100```일 때,
```kotlin
time = 3762 msecs

element at 0,0 = 4059
element at 0,1 = 3805
element at 0,2 = 4006
element at 0,3 = 3956
element at 0,4 = 3903
element at 1,0 = 4176
element at 1,1 = 3779
element at 1,2 = 4000
element at 1,3 = 3989
element at 1,4 = 3876
element at 2,0 = 4280
element at 2,1 = 4170
element at 2,2 = 4143
element at 2,3 = 4132
element at 2,4 = 3981
element at 3,0 = 4107
element at 3,1 = 3806
element at 3,2 = 4020
element at 3,3 = 3967
element at 3,4 = 3924
element at 4,0 = 4309
element at 4,1 = 3942
element at 4,2 = 3993
element at 4,3 = 4048
element at 4,4 = 3751


size = 1000, coroutines = 100, size per coroutine = 10
time with coroutine = 2340 msecs

element at 0,0 = 4059
element at 0,1 = 3805
element at 0,2 = 4006
element at 0,3 = 3956
element at 0,4 = 3903
element at 1,0 = 4176
element at 1,1 = 3779
element at 1,2 = 4000
element at 1,3 = 3989
element at 1,4 = 3876
element at 2,0 = 4280
element at 2,1 = 4170
element at 2,2 = 4143
element at 2,3 = 4132
element at 2,4 = 3981
element at 3,0 = 4107
element at 3,1 = 3806
element at 3,2 = 4020
element at 3,3 = 3967
element at 3,4 = 3924
element at 4,0 = 4309
element at 4,1 = 3942
element at 4,2 = 3993
element at 4,3 = 4048
element at 4,4 = 3751
```
