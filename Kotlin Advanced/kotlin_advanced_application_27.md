## 요구 사항 분석
문자열을 수식 형태로 입력하면 그것을 계산하는 프로그램을 작성하려고 한다. 요구 사항은 다음과 같다.
* 문자열은 ```+```, ```-```, ```*```, ```/```의 기호, 숫자 및 공백(``` ```)으로만 구성되어 있다.
  * 의미는 프로그래밍 언어에서 일반적으로 사용되는 의미와 같다.
  * 계산 순서는 공백과 기호를 포함한 문자열로 주어지고, 공백을 기준으로 나누어진 부분들 중 앞쪽 부분에 있는 기호가 뒤쪽 부분에 있는 기호보다 우선한다.
    * 예를 들어 계산 순서가 ```*/ +-```이면 곱셈과 나눗셈을 덧셈과 뺄셈보다 우선적으로 계산한다.
  * 단, ```/``` 기호의 나눗셈 결과에서 소수점 이하는 버린다.
* 공백은 계산 과정에 영향을 미치지 않는다.

### 데이터 클래스
데이터 클래스는 다음과 같다.
```kotlin
// 수식
data class Formula (
    var formula_str: String, // 수식 내용
    var priority: String // 연산자 우선순위
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 수식
data class Formula (
    var formula_str: String, // 수식 내용
    var priority: String // 연산자 우선순위
)

// 수식 계산 및 결과 표시
fun computeFormula(formula: Formula) {
    val fs = formula.formula_str.replace(" ", "")
    val priority = formula.priority.split(" ")
    
    // 연산자만 추출하여 배열화
    val operators = Regex("[0-9]").replace(fs, "").toMutableList()
    
    // 숫자 추출
    val numbers = listOf("+", "-", "*", "/").fold(fs) { acc, e ->
        acc.replace(e, " ")
    }.split(" ").map { it.toInt() }.toMutableList()
    
    println("1. operators=${operators}")
    println("2. numbers=${numbers}")
    println("3. priority=${priority}\n")
    
    // 각 우선순위별로 같은 우선순위는 왼쪽부터 계산
    priority.forEach { p ->
        val pl = p.toList()
        println("priority: ${pl}")
        
        var idx = 0
        (0 until operators.size).forEach {
            if (operators[idx] in pl) {
                val op = operators.removeAt(idx)
                val num0 = numbers.removeAt(idx)
                val num1 = numbers.removeAt(idx) // idx + 1 -> idx
                
                when (op) {
                    '+' -> numbers.add(idx, num0 + num1)
                    '-' -> numbers.add(idx, num0 - num1)
                    '*' -> numbers.add(idx, num0 * num1)
                    '/' -> numbers.add(idx, num0 / num1)
                }
                println("numbers = ${numbers}")
                
                // 수식 갱신으로 인한 기호 1개 감소 반영
                idx--
            }
            idx++
        }
        println("remaining numbers = ${numbers}\n")
    }
    
    println("final result : ${numbers[0]}\n")
}

fun main() {
    
    // 임시 수식 목록
    val tempFormula = listOf<Formula>(
        Formula("2 + 3 * 7 - 16 / 4", "*/ +-"),
        Formula("2 + 3 * 7 - 16 / 4", "+-*/"),
        Formula("100 - 25 + 45", "+ -*/"),
        Formula("30 * 30 + 20 * 20", "*/ +-"),
        Formula("30 * 30 + 20 * 20", "+ -*/"),
        Formula("30 * 30 + 20 * 20", "+-*/"),
        Formula("10 + 20 * 16 - 45 / 5", "/ - * +"),
        Formula("7 - 3 * 10 - 4", "+- */"),
        Formula("8 + 4 * 16 + 6", "+- */"),
        Formula("8 + 3 * 4 - 24 / 2", "+*/ -")
    )
    
    tempFormula.forEach {
        computeFormula(it)
    }
}
```

### 실행 결과
```kotlin
1. operators=[+, *, -, /]
2. numbers=[2, 3, 7, 16, 4]
3. priority=[*/, +-]

priority: [*, /]
numbers = [2, 21, 16, 4]
numbers = [2, 21, 4]
remaining numbers = [2, 21, 4]

priority: [+, -]
numbers = [23, 4]
numbers = [19]
remaining numbers = [19]

final result : 19

1. operators=[+, *, -, /]
2. numbers=[2, 3, 7, 16, 4]
3. priority=[+-*/]

priority: [+, -, *, /]
numbers = [5, 7, 16, 4]
numbers = [35, 16, 4]
numbers = [19, 4]
numbers = [4]
remaining numbers = [4]

final result : 4

1. operators=[-, +]
2. numbers=[100, 25, 45]
3. priority=[+, -*/]

priority: [+]
numbers = [100, 70]
remaining numbers = [100, 70]

priority: [-, *, /]
numbers = [30]
remaining numbers = [30]

final result : 30

1. operators=[*, +, *]
2. numbers=[30, 30, 20, 20]
3. priority=[*/, +-]

priority: [*, /]
numbers = [900, 20, 20]
numbers = [900, 400]
remaining numbers = [900, 400]

priority: [+, -]
numbers = [1300]
remaining numbers = [1300]

final result : 1300

1. operators=[*, +, *]
2. numbers=[30, 30, 20, 20]
3. priority=[+, -*/]

priority: [+]
numbers = [30, 50, 20]
remaining numbers = [30, 50, 20]

priority: [-, *, /]
numbers = [1500, 20]
numbers = [30000]
remaining numbers = [30000]

final result : 30000

1. operators=[*, +, *]
2. numbers=[30, 30, 20, 20]
3. priority=[+-*/]

priority: [+, -, *, /]
numbers = [900, 20, 20]
numbers = [920, 20]
numbers = [18400]
remaining numbers = [18400]

final result : 18400

1. operators=[+, *, -, /]
2. numbers=[10, 20, 16, 45, 5]
3. priority=[/, -, *, +]

priority: [/]
numbers = [10, 20, 16, 9]
remaining numbers = [10, 20, 16, 9]

priority: [-]
numbers = [10, 20, 7]
remaining numbers = [10, 20, 7]

priority: [*]
numbers = [10, 140]
remaining numbers = [10, 140]

priority: [+]
numbers = [150]
remaining numbers = [150]

final result : 150

1. operators=[-, *, -]
2. numbers=[7, 3, 10, 4]
3. priority=[+-, */]

priority: [+, -]
numbers = [4, 10, 4]
numbers = [4, 6]
remaining numbers = [4, 6]

priority: [*, /]
numbers = [24]
remaining numbers = [24]

final result : 24

1. operators=[+, *, +]
2. numbers=[8, 4, 16, 6]
3. priority=[+-, */]

priority: [+, -]
numbers = [12, 16, 6]
numbers = [12, 22]
remaining numbers = [12, 22]

priority: [*, /]
numbers = [264]
remaining numbers = [264]

final result : 264

1. operators=[+, *, -, /]
2. numbers=[8, 3, 4, 24, 2]
3. priority=[+*/, -]

priority: [+, *, /]
numbers = [11, 4, 24, 2]
numbers = [44, 24, 2]
numbers = [44, 12]
remaining numbers = [44, 12]

priority: [-]
numbers = [32]
remaining numbers = [32]

final result : 32
```
