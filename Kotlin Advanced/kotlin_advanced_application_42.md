## 요구사항 분석
4개의 집합 ```A```, ```B```, ```C```, ```D``` 에 대해, ```A```와 ```B```의 교집합을 ```A ^ B```, 합집합을 ``` A U B```와 같은 식으로 나타낸다.

이들 4개의 집합에 대해, 각 집합에 속하는지의 여부에 따라, ```집합식```이 나타내는 집합에 속하는지를 출력하시오.

단, 집합식에서는 교집합을 합집합보다 먼저 계산하며, 괄호나 여집합, 차집합 등의 개념은 사용하지 않는다.

예를 들어 집합식이
```kotlin
A ^ B U C ^ D
```
일 때, 각 집합에 속하는지의 여부에 따라 이 집합식이 나타내는 집합에 속하는지의 여부를 나타내면 다음과 같다.

```kotlin
 A  B  C  D -> true
 A  B  C ~D -> true
 A  B ~C  D -> true
 A  B ~C ~D -> true
 A ~B  C  D -> true
 A ~B  C ~D -> false
 A ~B ~C  D -> false
 A ~B ~C ~D -> false
~A  B  C  D -> true
~A  B  C ~D -> false
~A  B ~C  D -> false
~A  B ~C ~D -> false
~A ~B  C  D -> true
~A ~B  C ~D -> false
~A ~B ~C  D -> false
~A ~B ~C ~D -> false
```

### 데이터 클래스
데이터 클래스 없이 ```집합식```이 문자열로 주어진다.

## 코드 및 실행 결과
### 코드
```kotlin
// 각 집합 A, B, C, D에 속하는지의 여부에 따라 집합식이 나타내는 집합에 속하는지를 반환
fun printInSet(formula: String, inA: Boolean, inB: Boolean, inC: Boolean, inD: Boolean): String {
    
    // 출력 결과의 앞부분
    val isIn = listOf(inA, inB, inC, inD)
    val marks = listOf("A", "B", "C", "D")
    
    val inStrMain = (0..3).fold("") { acc, e ->
        if (isIn[e]) {
            "${acc} ${marks[e]} "
        } else {
            "${acc}~${marks[e]} "
        }
    }
    val inStr = "${inStrMain}->"
    
    // 집합식의 A, B, C, D를 T, F로 치환
    val replacedFormula = (0..3).fold (formula) { acc, e ->
        if (isIn[e]) {
            acc.replace(marks[e], "T")
        } else {
            acc.replace(marks[e], "F")
        }
    }
    println("1. replaced formula = ${replacedFormula}\n")
    
    // 교집합 계산 (T ^ T -> T, T ^ F -> F, F ^ T -> F, F ^ F -> F)
    var idx = 0
    var formula = replacedFormula
    
    while (idx < formula.length) {
        if (formula[idx] == '^') {
            val result = when (formula.substring(idx - 2, idx + 3)) {
                "T ^ T" -> "T"
                else -> "F"
            }
            formula = formula.substring(0, idx - 2) + result + formula.substring(idx + 3)
            idx -= 3
            println("2. formula = ${formula}")
        }
        idx++
    }
    println("3. replaced formula = ${formula}\n")
    
    // 합집합 계산
    idx = 0
    
    while (idx < formula.length) {
        if (formula[idx] == 'U') {
            val result = when (formula.substring(idx - 2, idx + 3)) {
                "F U F" -> "F"
                else -> "T"
            }
            formula = formula.substring(0, idx - 2) + result + formula.substring(idx + 3)
            idx -= 3
            println("4. formula = ${formula}")
        }
        idx++
    }
    println("5. replaced formula = ${formula}\n")
    
    // 최종 출력 반환
    return "${inStr} ${formula == "T"}"
}

fun main() {
    
    // 집합식
    val formula = "A ^ B ^ C U C ^ D U A ^ D"
    val results = mutableListOf<String>()
    
    // 각 집합에 속하는지의 여부에 따라 집합식이 나타내는 집합에 속하는지를 반환
    for (inA in listOf(true, false)) {
        for (inB in listOf(true, false)) {
            for (inC in listOf(true, false)) {
                for (inD in listOf(true, false)) {
                    results.add(printInSet(formula, inA, inB, inC, inD))
                }
            }
        }
    }
    
    // 최종 출력 표시
    results.forEach {
        println(it)
    }
}
```

### 실행 결과
```kotlin
1. replaced formula = T ^ T ^ T U T ^ T U T ^ T

2. formula = T ^ T U T ^ T U T ^ T
2. formula = T U T ^ T U T ^ T
2. formula = T U T U T ^ T
2. formula = T U T U T
3. replaced formula = T U T U T

4. formula = T U T
4. formula = T
5. replaced formula = T

1. replaced formula = T ^ T ^ T U T ^ F U T ^ F

2. formula = T ^ T U T ^ F U T ^ F
2. formula = T U T ^ F U T ^ F
2. formula = T U F U T ^ F
2. formula = T U F U F
3. replaced formula = T U F U F

4. formula = T U F
4. formula = T
5. replaced formula = T

1. replaced formula = T ^ T ^ F U F ^ T U T ^ T

2. formula = T ^ F U F ^ T U T ^ T
2. formula = F U F ^ T U T ^ T
2. formula = F U F U T ^ T
2. formula = F U F U T
3. replaced formula = F U F U T

4. formula = F U T
4. formula = T
5. replaced formula = T

1. replaced formula = T ^ T ^ F U F ^ F U T ^ F

2. formula = T ^ F U F ^ F U T ^ F
2. formula = F U F ^ F U T ^ F
2. formula = F U F U T ^ F
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = T ^ F ^ T U T ^ T U T ^ T

2. formula = F ^ T U T ^ T U T ^ T
2. formula = F U T ^ T U T ^ T
2. formula = F U T U T ^ T
2. formula = F U T U T
3. replaced formula = F U T U T

4. formula = T U T
4. formula = T
5. replaced formula = T

1. replaced formula = T ^ F ^ T U T ^ F U T ^ F

2. formula = F ^ T U T ^ F U T ^ F
2. formula = F U T ^ F U T ^ F
2. formula = F U F U T ^ F
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = T ^ F ^ F U F ^ T U T ^ T

2. formula = F ^ F U F ^ T U T ^ T
2. formula = F U F ^ T U T ^ T
2. formula = F U F U T ^ T
2. formula = F U F U T
3. replaced formula = F U F U T

4. formula = F U T
4. formula = T
5. replaced formula = T

1. replaced formula = T ^ F ^ F U F ^ F U T ^ F

2. formula = F ^ F U F ^ F U T ^ F
2. formula = F U F ^ F U T ^ F
2. formula = F U F U T ^ F
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = F ^ T ^ T U T ^ T U F ^ T

2. formula = F ^ T U T ^ T U F ^ T
2. formula = F U T ^ T U F ^ T
2. formula = F U T U F ^ T
2. formula = F U T U F
3. replaced formula = F U T U F

4. formula = T U F
4. formula = T
5. replaced formula = T

1. replaced formula = F ^ T ^ T U T ^ F U F ^ F

2. formula = F ^ T U T ^ F U F ^ F
2. formula = F U T ^ F U F ^ F
2. formula = F U F U F ^ F
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = F ^ T ^ F U F ^ T U F ^ T

2. formula = F ^ F U F ^ T U F ^ T
2. formula = F U F ^ T U F ^ T
2. formula = F U F U F ^ T
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = F ^ T ^ F U F ^ F U F ^ F

2. formula = F ^ F U F ^ F U F ^ F
2. formula = F U F ^ F U F ^ F
2. formula = F U F U F ^ F
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = F ^ F ^ T U T ^ T U F ^ T

2. formula = F ^ T U T ^ T U F ^ T
2. formula = F U T ^ T U F ^ T
2. formula = F U T U F ^ T
2. formula = F U T U F
3. replaced formula = F U T U F

4. formula = T U F
4. formula = T
5. replaced formula = T

1. replaced formula = F ^ F ^ T U T ^ F U F ^ F

2. formula = F ^ T U T ^ F U F ^ F
2. formula = F U T ^ F U F ^ F
2. formula = F U F U F ^ F
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = F ^ F ^ F U F ^ T U F ^ T

2. formula = F ^ F U F ^ T U F ^ T
2. formula = F U F ^ T U F ^ T
2. formula = F U F U F ^ T
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

1. replaced formula = F ^ F ^ F U F ^ F U F ^ F

2. formula = F ^ F U F ^ F U F ^ F
2. formula = F U F ^ F U F ^ F
2. formula = F U F U F ^ F
2. formula = F U F U F
3. replaced formula = F U F U F

4. formula = F U F
4. formula = F
5. replaced formula = F

 A  B  C  D -> true
 A  B  C ~D -> true
 A  B ~C  D -> true
 A  B ~C ~D -> false
 A ~B  C  D -> true
 A ~B  C ~D -> false
 A ~B ~C  D -> true
 A ~B ~C ~D -> false
~A  B  C  D -> true
~A  B  C ~D -> false
~A  B ~C  D -> false
~A  B ~C ~D -> false
~A ~B  C  D -> true
~A ~B  C ~D -> false
~A ~B ~C  D -> false
~A ~B ~C ~D -> false
```
