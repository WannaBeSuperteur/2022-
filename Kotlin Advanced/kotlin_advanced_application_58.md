## 요구사항 분석
다항함수 ```y = ax^3 + bx^2 + cx + d```를 미분하면 ```y' = 3ax^2 + 2bx + c```가 된다. 이와 같이 다항함수가 주어졌을 때, 해당 함수를 미분한 결과를 나타내시오.

예를 들어 ```y = 4abx^5 + 2x^4 + cx^3 + x^2 + dx + 5```를 미분하면 ```y' = 20abx^4 + 8x^3 + 3cx^2 + 2x + d```이다.

### 데이터 클래스
데이터 클래스는 주어지지 않는다.

## 코드 및 실행 결과
### 코드
```kotlin
// 미분 결과 나타내기
fun findDerivative(formula: String): String {
    val right = formula.substringAfter(" = ")
    
    // 각 항을 미분하기
    val rightDerivative = right.split(" + ").map {
        
        // 계수와 x^n 부분으로 분리
        val coefficient = it.substringBefore("x")
        val xPart = if (it.contains("x")) {
            "x${it.substringAfter("x")}"
        } else {
            ""
        }
        
        // 각 항의 미분 결과
        if (xPart.isEmpty()) {
            ""
        } else {
            
            // 계수 부분 처리
            val coeffDigits = coefficient.count { it.isDigit() }
            val coeffNum = coefficient.substring(0, coeffDigits)
            val coeffAlphabets = coefficient.substring(coeffDigits)
            
            val multiple = if (xPart == "x") {
                1
            } else {
                xPart.substringAfter("^").toInt()
            }
            
            // x^n 부분 처리
            val newXPart = if (xPart == "x") {
                ""
            } else {
                val xPartRight = xPart.substringAfter("^")
                
                val newPower = if (xPartRight == "2") {
                    ""
                } else {
                    "^${xPartRight.toInt() - 1}"
                }
                "x${newPower}"
            }
            
            // 최종 결과
            val newMultiple = if (coeffNum.isEmpty()) {
                multiple
            } else {
                multiple * coeffNum.toInt()
            }
            
            val newMultipleStr = if (newMultiple == 1 && "${coeffAlphabets}${newXPart}".isNotEmpty()) {
                ""
            } else {
                newMultiple.toString()
            }
            
            "${newMultipleStr}${coeffAlphabets}${newXPart}"
        }
    }
    
    // 최종 결과
    val result = rightDerivative.fold ("y' = ") { acc, e ->
        if (e.isEmpty()) {
            acc
        } else {
            acc + e + " + "
        }
    }
    
    // 결과가 아무것도 없을 시에는 "0"을 반환
    if (result == "y' = ") {
        return "0"
    } else {
        return result.dropLast(3)
    }
}

fun main() {
    
    // 임시 수식 목록
    val tempFormulaList = listOf<String> (
        "y = 13",
        "y = x",
        "y = 7x + 12",
        "y = 3abx + 4c",
        "y = 3x^3 + ax^2 + bx + 4",
        "y = 4abx^5 + 2x^4 + cx^3 + x^2 + dx + 5",
        "y = abx^4 + 20x^2 + x + cd",
        "y = abx^12 + 10cx^2 + x",
        "y = 300abx^16 + 5cdx^2 + 20x",
        "y = 16ax^4 + 8bx^3 + cdx",
        "y = 9x^9 + ax^8 + 7bx^7 + 6cdx^6 + 400e + 3f + 64"
    )
    
    // 각 수식의 미분 결과 나타내기
    tempFormulaList.forEach {
        println("${it} -> ${findDerivative(it)}")
    }
}
```

### 실행 결과
```kotlin
y = 13 -> 0
y = x -> y' = 1
y = 7x + 12 -> y' = 7
y = 3abx + 4c -> y' = 3ab
y = 3x^3 + ax^2 + bx + 4 -> y' = 9x^2 + 2ax + b
y = 4abx^5 + 2x^4 + cx^3 + x^2 + dx + 5 -> y' = 20abx^4 + 8x^3 + 3cx^2 + 2x + d
y = abx^4 + 20x^2 + x + cd -> y' = 4abx^3 + 40x + 1
y = abx^12 + 10cx^2 + x -> y' = 12abx^11 + 20cx + 1
y = 300abx^16 + 5cdx^2 + 20x -> y' = 4800abx^15 + 10cdx + 20
y = 16ax^4 + 8bx^3 + cdx -> y' = 64ax^3 + 24bx^2 + cd
y = 9x^9 + ax^8 + 7bx^7 + 6cdx^6 + 400e + 3f + 64 -> y' = 81x^8 + 8ax^7 + 49bx^6 + 36cdx^5
```
