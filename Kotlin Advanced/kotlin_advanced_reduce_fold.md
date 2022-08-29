## reduce, fold
### reduce, fold Basic
```kotlin
list.reduce { acc, i ->
    value // acc, i로 표현됨
}
```
위 코드는 다음과 같이 실행된다.
* 콜렉션 ```list```의 첫 번째 원소를 ```acc```라고 하고, 두 번째 원소를 ```i```라고 한다. 이것을 연산한 ```value``` 값을 ```acc```에 저장한다.
* ```acc```의 값이 저장되어 갱신되었고, ```list```의 세 번째 원소를 ```i```라고 한다. 이것을 연산한 ```value``` 값을 또다시 ```acc```에 저장한다.
* ```acc```의 값이 또다시 저장되어 갱신되었고, ```list```의 네 번째 원소를 ```i```라고 한다. 이것을 연산한 ```value``` 값을 또다시 ```acc```에 저장한다.
* 이렇게 해서 이것을 ```list```의 마지막 원소 ```i```까지 반복한다.

```kotlin
list.fold (initValue) { acc, i ->
    value // acc, i로 표현됨
}
```
위 코드는 다음과 같이 실행된다.
* 콜렉션 ```list```의 첫 번째 원소를 ```i```라고 한다. 이것을 ```acc```의 초기값 ```initValue```와 연산한 ```value``` 값을 ```acc```에 저장한다.
* ```acc```의 값이 저장되어 갱신되었고, ```list```의 두 번째 원소를 ```i```라고 한다. 이것을 연산한 ```value``` 값을 또다시 ```acc```에 저장한다.
* ```acc```의 값이 또다시 저장되어 갱신되었고, ```list```의 세 번째 원소를 ```i```라고 한다. 이것을 연산한 ```value``` 값을 또다시 ```acc```에 저장한다.
* 이렇게 해서 이것을 ```list```의 마지막 원소 ```i```까지 반복한다.

## Example
### Example 1
```kotlin
fun main() {
    val listNum = listOf(1, 5, 10, 15, 50, 100)
    
    // 1 + 5 = 6,      6 + 10 = 16,     16 + 15 = 31,
    // 31 + 50 = 81,   81 + 100 = 181
    val reduce1 = listNum.reduce { acc, i ->
        acc + i
    }
    println("reduce test result 1 = ${reduce1}")
    
    // 1 + 5 * 5 = 26,       26 + 5 * 10 = 76,     76 + 5 * 15 = 151,
    // 151 + 5 * 50 = 401,   401 + 5 * 100 = 901
    val reduce2 = listNum.reduce { acc, i ->
        acc + 5 * i
    }
    println("reduce test result 2 = ${reduce2}")
    
    // 1000 + 1 = 1001,    1001 + 5 = 1006,    1006 + 10 = 1016,
    // 1016 + 15 = 1031,   1031 + 50 = 1081,   1081 + 100 = 1181
    val fold1 = listNum.fold (1000) { acc, i ->
        acc + i
    }
    println("fold test result 1 = ${fold1}")
    
    // 1000 + 5 * 1 = 1005,    1005 + 5 * 5 = 1030,    1030 + 5 * 10 = 1080,
    // 1080 + 5 * 15 = 1155,   1155 + 5 * 50 = 1405,   1405 + 5 * 100 = 1905
    val fold2 = listNum.fold (1000) { acc, i ->
        acc + 5 * i
    }
    println("fold test result 2 = ${fold2}")
    
    val listColors = listOf("red", "orange", "yellow", "green", "blue", "pink")
    
    // "red"                 + ", orange" = "red, orange"
    // "red, orange"         + ", yellow" = "red, orange, yellow"
    // "red, orange, yellow" + ", green"  = "red, orange, yellow, green"
    // ...
    val colors1 = listColors.reduce { acc, i ->
        "${acc}, ${i}"
    }
    println("reduce test result 3 (colors) = ${colors1}")
    
    // "colors : "              + "red, "    = "colors : red, "
    // "colors : red, "         + "orange, " = "colors : red, orange, "
    // "colors : red, orange, " + "yellow, " = "colors : red, orange, yellow, "
    // ...
    val colors2 = listColors.fold ("colors : ") { acc, i ->
        "${acc}${i}, "
    }
    println("fold test result 3 (colors) = ${colors2}")
}
```

## Output
### Output 1
```kotlin
reduce test result 1 = 181
reduce test result 2 = 901
fold test result 1 = 1181
fold test result 2 = 1905
reduce test result 3 (colors) = red, orange, yellow, green, blue, pink
fold test result 3 (colors) = colors : red, orange, yellow, green, blue, pink, 
```
