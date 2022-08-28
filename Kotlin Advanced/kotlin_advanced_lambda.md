## Lambda
### Lambda Basic
```kotlin
val funcName : (argDataType1, argDataType2, ...) -> (returnDataType) = { arg1, arg2, ... ->
   ...
}
```
위와 같은 함수의 이름은 ```funcName```, 인수는 ```arg1, arg2, ...```, 인수의 자료형은 각각 ```argDataType1, argDataType2, ...```, 반환값의 자료형은 ```returnDataType```이다.

여기서 ```funcName``` 함수 **자체**를 인수로 하는 함수를 다음과 같이 만들 수 있다.
```kotlin
fun invokeFuncName(lambda : (argDataType1, argDataType2, ...) -> (returnDataType)): invokeFuncReturnDataType {
   ... // lambda를 함수로 하여 lambda(arg1, arg2, ...)와 같이 사용할 수 있다.
}
```
여기서 ```lambda```와 ```funcName```의 인수의 자료형과 반환값의 자료형은 모두 ```(argDataType1, argDataType2, ...) -> (returnDataType)```로 같은 것을 확인할 수 있다.

### Lambda Advanced
```kotlin
val funcName : (DataType).(argDataType1, argDataType2, ...) -> (returnDataType) = { arg1, arg2, ... ->
   ... // this가 DataType의 자료형에 해당하는 값으로 이를 사용할 수 있음
}
```
위와 같은 함수의 이름은 ```funcName```, 인수는 ```arg1, arg2, ...```, 인수의 자료형은 각각 ```argDataType1, argDataType2, ...```, 반환값의 자료형은 ```returnDataType```이다.

위 함수를 실행하려면 다음과 같이 하면 된다.
```kotlin
value.funcName(arg1, arg2, ...)
```
여기서 ```value```의 자료형은 ```DataType```이고, ```arg1, arg2, ...```의 자료형은 각각 ```argDataType1, argDataType2, ...```이다.

## Example
### Example 1
```kotlin
// 함수(인수: 12, 20)의 결과에 3.14를 곱한 값을 반환
fun invokeOperationFunc(lambda : (Int, Int) -> Int): Double {
    return lambda(12, 20) * 3.14
}

// 함수(인수: a, b)의 결과에 c를 곱한 값을 반환
fun invokeOperationFunc_with_abc(lambda : (Int, Int) -> Int, a: Int, b: Int, c: Double): Double {
    return lambda(a, b) * c
}

fun main() {
    val addition : (Int, Int) -> Int = { a, b ->
    	a + b
    }
    
    val multiple : (Int, Int) -> Int = { a, b ->
        a * b
    }
    
    val harmonicMean : (Int, Int) -> Int = { a, b ->
    	2 * a * b / (a + b)
    }
    
    // lambda 함수 호출
    print("20 + 15 = " + addition(20, 15) + "\n")
    print("20 * 15 = " + multiple(20, 15) + "\n")
    print("harmonic mean of 20 and 15 = " + harmonicMean(20, 15) + "\n\n")
    
    // lambda 함수를 invokeOperationFunc의 인수로 넣어서 호출
    print("addition result : " + invokeOperationFunc(addition) + "\n")
    print("multiple result : " + invokeOperationFunc(multiple) + "\n")
    print("harmonicMean result : " + invokeOperationFunc(harmonicMean) + "\n\n")
    
    print("addition result : " + invokeOperationFunc_with_abc(addition, 2, 6, 3.0) + "\n")
    print("multiple result : " + invokeOperationFunc_with_abc(multiple, 2, 6, 3.0) + "\n")
    print("harmonicMean result : " + invokeOperationFunc_with_abc(harmonicMean, 2, 6, 3.0) + "\n\n")
}
```

### Example 2
```kotlin
fun main() {
    val baseOperations : String.(Int, Int) -> Int = { a, b ->
        when (this) {
            "+" -> a + b
            "-" -> a - b
            "*" -> a * b
            "/" -> a / b
            else -> 0
        }
    }
    
    // 함수 실행
    print("+".baseOperations(16, 3).toString() + "\n")
    print("-".baseOperations(12, 7).toString() + "\n")
    print("*".baseOperations(30, 2).toString() + "\n")
    print("/".baseOperations(32, 4).toString() + "\n")
}
```

## Output
### Output 1
```kotlin
20 + 15 = 35
20 * 15 = 300
harmonic mean of 20 and 15 = 17

addition result : 100.48
multiple result : 753.6
harmonicMean result : 47.1

addition result : 24.0
multiple result : 36.0
harmonicMean result : 9.0
```

### Output 2
```kotlin
19
5
60
8
```
