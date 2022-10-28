## 요구사항 분석
```sin```, ```cos``` 함수와 ```+``` 연산으로 된 합성함수를 나타낸 문자열 ```function```에 대해, 이 함수가 0이 되는 ```x```의 해를 ```[0, 6.4]``` 범위 내에서 하나 구해서 출력하는 함수 ```solveFunc(function)```를 작성하시오.

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// sin(x) 또는 cos(x) 꼴 계산
fun computeSinCos(part: String, x: Double): Double {
    val isSin = part.startsWith("sin")
    val xPart = part.substring(4, part.length - 1)
                    
    val partResult = if (xPart == "x") {
        if (isSin) { Math.sin(x) } else { Math.cos(x) }
    } else {
        val xToDouble = xPart.toDouble()
        if (isSin) { Math.sin(xToDouble) } else { Math.cos(xToDouble) }
    }
    return partResult
}

// x에 대한 함수의 값 계산하기
fun computeFunc(function: String, x: Double, printProcess: Boolean = false): Double {
    if (printProcess) {
        println("0. function = ${function}")
    }
    
    // 괄호 처리를 쉽게 하기 위해 양쪽에 괄호 추가
    var result = "(${function})"
    var count = 0
    
    // (x)를 제외하고 (...) 부분을 계산해 나가는 식으로 처리
    while (result.contains("sin") || result.contains("cos")) {
        
        val n = result.length
        var closingIndices = (0..99).map { -1 }.toMutableList()
        var depth = 0
        
        for (i in n - 1 downTo 0) {
            
            // depth 처리
            if (result[i] == ')') { depth++ } else if (result[i] == '(') { depth-- }
            
            // 오른쪽 괄호 처리
            if (result[i] == ')') {
                closingIndices[depth] = i
            }
            
            // 왼쪽 괄호일 때 (x)가 아닌 경우 해당 부분 계산
            else if (result[i] == '(' && closingIndices[depth + 1] - i > 2) {
                val part = result.substring(i + 1, closingIndices[depth + 1])
                var partLength = part.length
                
                // sin(x) 또는 cos(x) 꼴
                val partResult = if (
                    (part.matches(Regex("sin(.*)")) || part.matches(Regex("cos(.*)"))) &&
                    !part.contains("+")
                ) {
                    computeSinCos(part, x)
                }
                
                // 그렇지 않으면 항상 덧셈 꼴
                else {
                    part.split(" + ").sumOf {
                        if (it.contains("(")) {
                            computeSinCos(it, x)
                        } else {
                            it.toDouble()
                        }
                    }
                }
                val partResultLength = partResult.toString().length
                
                // 계산 결과 처리 및 오른쪽 괄호의 인덱스 변경
                result = result.substring(0, i + 1) + partResult.toString() + result.substring(closingIndices[depth + 1])
                closingIndices.forEachIndexed { idx, it ->
                    if (it > i + 1) {
                        closingIndices[idx] -= (partLength - partResultLength)
                    }
                }
                if (printProcess) {
                    println("1. result = ${result}")
                }
            }
        }
    }
    
    // 실제 출력은 괄호 제외
    val finalResult = result.substring(1, result.length - 1).toDouble()
    if (printProcess) {
        println("2. final result = ${finalResult}\n")
    }
    return finalResult
}

// function의 함수의 해 출력
fun solveFunc(function: String) {
    
    // 테스트
    computeFunc(function, 0.0, true)
    var isFound = false
    var count = 0
    
    while (!isFound && count < 1000) {
        val x0 = Math.random() * 6.4
        val x1 = Math.random() * 6.4
        
        val x0Result = computeFunc(function, x0)
        val x1Result = computeFunc(function, x1)
        
        // 하나가 양수, 하나가 음수이면 그 사이에 해가 있음
        if (x0Result * x1Result < 0) {
            var xMax = Math.max(x0, x1)
            var xMin = Math.min(x0, x1)
            
            while (true) {
                val xMid = (xMax + xMin) / 2.0
                val toStr: ((Double) -> String) = {
                    it.toString().substring(0, 6)
                }
                println("xMax = ${toStr(xMax)}, xMid = ${toStr(xMid)}, xMin = ${toStr(xMin)}")
                
                // 일정 간격 이내이면 해를 반환
                if (xMax - xMin < 0.001) {
                    println("\n3. solution of ${function} = 0 : ${xMid}\n")
                    isFound = true
                    break
                }
                
                // 해를 계속 찾기
                val xMaxResult = computeFunc(function, xMax)
                val xMidResult = computeFunc(function, xMid)
                val xMinResult = computeFunc(function, xMin)
                
                if (xMaxResult * xMidResult < 0) {
                    xMin = xMid
                } else if (xMidResult * xMinResult < 0) {
                    xMax = xMid
                }
            }
        }
        count++
        
        if (count >= 1000) {
            println("4. tried 1,000 times for ${function} = 0 but failed\n")
        }
    }
}

fun main() {
    
    // 함수 목록
    val funcs = listOf(
        "sin(x)",
        "cos(x)",
        "sin(x) + cos(x)",
        "sin(cos(x))",
        "sin(sin(x))",
        "sin(cos(x)) + cos(sin(x))",
        "sin(sin(x)) + cos(cos(x))",
        "sin(x) + cos(sin(x) + cos(x))",
        "sin(cos(sin(x) + cos(x))) + cos(cos(sin(x)))",
        "sin(cos(cos(sin(x)) + sin(x)) + cos(x))",
        "cos(cos(sin(x) + cos(x) + sin(cos(x)))) + sin(cos(cos(x)))"
    )
    
    // 함수의 값이 0이 되는 x 구하기
    funcs.forEach {
        solveFunc(it)
    }
}
```

### 실행 결과
```kotlin
0. function = sin(x)
1. result = (0.0)
2. final result = 0.0

xMax = 4.1760, xMid = 2.5307, xMin = 0.8854
xMax = 4.1760, xMid = 3.3533, xMin = 2.5307
xMax = 3.3533, xMid = 2.9420, xMin = 2.5307
xMax = 3.3533, xMid = 3.1477, xMin = 2.9420
xMax = 3.1477, xMid = 3.0448, xMin = 2.9420
xMax = 3.1477, xMid = 3.0963, xMin = 3.0448
xMax = 3.1477, xMid = 3.1220, xMin = 3.0963
xMax = 3.1477, xMid = 3.1348, xMin = 3.1220
xMax = 3.1477, xMid = 3.1412, xMin = 3.1348
xMax = 3.1477, xMid = 3.1445, xMin = 3.1412
xMax = 3.1445, xMid = 3.1429, xMin = 3.1412
xMax = 3.1429, xMid = 3.1420, xMin = 3.1412
xMax = 3.1420, xMid = 3.1416, xMin = 3.1412

3. solution of sin(x) = 0 : 3.1416952245751033

0. function = cos(x)
1. result = (1.0)
2. final result = 1.0

xMax = 1.7593, xMid = 0.9558, xMin = 0.1524
xMax = 1.7593, xMid = 1.3575, xMin = 0.9558
xMax = 1.7593, xMid = 1.5584, xMin = 1.3575
xMax = 1.7593, xMid = 1.6588, xMin = 1.5584
xMax = 1.6588, xMid = 1.6086, xMin = 1.5584
xMax = 1.6086, xMid = 1.5835, xMin = 1.5584
xMax = 1.5835, xMid = 1.5710, xMin = 1.5584
xMax = 1.5710, xMid = 1.5647, xMin = 1.5584
xMax = 1.5710, xMid = 1.5678, xMin = 1.5647
xMax = 1.5710, xMid = 1.5694, xMin = 1.5678
xMax = 1.5710, xMid = 1.5702, xMin = 1.5694
xMax = 1.5710, xMid = 1.5706, xMin = 1.5702

3. solution of cos(x) = 0 : 1.570612122411995

0. function = sin(x) + cos(x)
1. result = (1.0)
2. final result = 1.0

xMax = 5.8840, xMid = 4.9329, xMin = 3.9817
xMax = 5.8840, xMid = 5.4085, xMin = 4.9329
xMax = 5.8840, xMid = 5.6463, xMin = 5.4085
xMax = 5.6463, xMid = 5.5274, xMin = 5.4085
xMax = 5.5274, xMid = 5.4679, xMin = 5.4085
xMax = 5.5274, xMid = 5.4976, xMin = 5.4679
xMax = 5.5274, xMid = 5.5125, xMin = 5.4976
xMax = 5.5125, xMid = 5.5051, xMin = 5.4976
xMax = 5.5051, xMid = 5.5014, xMin = 5.4976
xMax = 5.5014, xMid = 5.4995, xMin = 5.4976
xMax = 5.4995, xMid = 5.4986, xMin = 5.4976
xMax = 5.4986, xMid = 5.4981, xMin = 5.4976

3. solution of sin(x) + cos(x) = 0 : 5.498150404037933

0. function = sin(cos(x))
1. result = (sin(1.0))
1. result = (0.8414709848078965)
2. final result = 0.8414709848078965

xMax = 2.1145, xMid = 1.3464, xMin = 0.5783
xMax = 2.1145, xMid = 1.7304, xMin = 1.3464
xMax = 1.7304, xMid = 1.5384, xMin = 1.3464
xMax = 1.7304, xMid = 1.6344, xMin = 1.5384
xMax = 1.6344, xMid = 1.5864, xMin = 1.5384
xMax = 1.5864, xMid = 1.5624, xMin = 1.5384
xMax = 1.5864, xMid = 1.5744, xMin = 1.5624
xMax = 1.5744, xMid = 1.5684, xMin = 1.5624
xMax = 1.5744, xMid = 1.5714, xMin = 1.5684
xMax = 1.5714, xMid = 1.5699, xMin = 1.5684
xMax = 1.5714, xMid = 1.5707, xMin = 1.5699
xMax = 1.5714, xMid = 1.5711, xMin = 1.5707

3. solution of sin(cos(x)) = 0 : 1.571105445878977

0. function = sin(sin(x))
1. result = (sin(0.0))
1. result = (0.0)
2. final result = 0.0

xMax = 4.2161, xMid = 3.0539, xMin = 1.8918
xMax = 4.2161, xMid = 3.6350, xMin = 3.0539
xMax = 3.6350, xMid = 3.3445, xMin = 3.0539
xMax = 3.3445, xMid = 3.1992, xMin = 3.0539
xMax = 3.1992, xMid = 3.1266, xMin = 3.0539
xMax = 3.1992, xMid = 3.1629, xMin = 3.1266
xMax = 3.1629, xMid = 3.1447, xMin = 3.1266
xMax = 3.1447, xMid = 3.1356, xMin = 3.1266
xMax = 3.1447, xMid = 3.1402, xMin = 3.1356
xMax = 3.1447, xMid = 3.1424, xMin = 3.1402
xMax = 3.1424, xMid = 3.1413, xMin = 3.1402
xMax = 3.1424, xMid = 3.1419, xMin = 3.1413
xMax = 3.1419, xMid = 3.1416, xMin = 3.1413

3. solution of sin(sin(x)) = 0 : 3.141643085632732

0. function = sin(cos(x)) + cos(sin(x))
1. result = (sin(cos(x)) + cos(0.0))
1. result = (sin(1.0) + cos(0.0))
1. result = (1.8414709848078965)
2. final result = 1.8414709848078965

4. tried 1,000 times for sin(cos(x)) + cos(sin(x)) = 0 but failed

0. function = sin(sin(x)) + cos(cos(x))
1. result = (sin(sin(x)) + cos(1.0))
1. result = (sin(0.0) + cos(1.0))
1. result = (0.5403023058681398)
2. final result = 0.5403023058681398

4. tried 1,000 times for sin(sin(x)) + cos(cos(x)) = 0 but failed

0. function = sin(x) + cos(sin(x) + cos(x))
1. result = (sin(x) + cos(1.0))
1. result = (0.5403023058681398)
2. final result = 0.5403023058681398

xMax = 5.2810, xMid = 5.0391, xMin = 4.7971
xMax = 5.2810, xMid = 5.1600, xMin = 5.0391
xMax = 5.2810, xMid = 5.2205, xMin = 5.1600
xMax = 5.2205, xMid = 5.1902, xMin = 5.1600
xMax = 5.1902, xMid = 5.1751, xMin = 5.1600
xMax = 5.1751, xMid = 5.1676, xMin = 5.1600
xMax = 5.1751, xMid = 5.1713, xMin = 5.1676
xMax = 5.1713, xMid = 5.1695, xMin = 5.1676
xMax = 5.1695, xMid = 5.1685, xMin = 5.1676
xMax = 5.1695, xMid = 5.1690, xMin = 5.1685

3. solution of sin(x) + cos(sin(x) + cos(x)) = 0 : 5.1690346221057855

0. function = sin(cos(sin(x) + cos(x))) + cos(cos(sin(x)))
1. result = (sin(cos(sin(x) + cos(x))) + cos(cos(0.0)))
1. result = (sin(cos(sin(x) + cos(x))) + cos(1.0))
1. result = (sin(cos(1.0)) + cos(1.0))
1. result = (sin(0.5403023058681398) + cos(1.0))
1. result = (1.054697564391689)
2. final result = 1.054697564391689

4. tried 1,000 times for sin(cos(sin(x) + cos(x))) + cos(cos(sin(x))) = 0 but failed

0. function = sin(cos(cos(sin(x)) + sin(x)) + cos(x))
1. result = (sin(cos(cos(0.0) + sin(x)) + cos(x)))
1. result = (sin(cos(1.0) + cos(x)))
1. result = (sin(1.5403023058681398))
1. result = (0.9995350933713548)
2. final result = 0.9995350933713548

xMax = 5.9922, xMid = 4.1109, xMin = 2.2295
xMax = 4.1109, xMid = 3.1702, xMin = 2.2295
xMax = 4.1109, xMid = 3.6405, xMin = 3.1702
xMax = 3.6405, xMid = 3.4054, xMin = 3.1702
xMax = 3.6405, xMid = 3.5230, xMin = 3.4054
xMax = 3.6405, xMid = 3.5817, xMin = 3.5230
xMax = 3.6405, xMid = 3.6111, xMin = 3.5817
xMax = 3.6111, xMid = 3.5964, xMin = 3.5817
xMax = 3.6111, xMid = 3.6038, xMin = 3.5964
xMax = 3.6038, xMid = 3.6001, xMin = 3.5964
xMax = 3.6038, xMid = 3.6020, xMin = 3.6001
xMax = 3.6020, xMid = 3.6010, xMin = 3.6001
xMax = 3.6020, xMid = 3.6015, xMin = 3.6010

3. solution of sin(cos(cos(sin(x)) + sin(x)) + cos(x)) = 0 : 3.6015440017343274

0. function = cos(cos(sin(x) + cos(x) + sin(cos(x)))) + sin(cos(cos(x)))
1. result = (cos(cos(sin(x) + cos(x) + sin(cos(x)))) + sin(cos(1.0)))
1. result = (cos(cos(sin(x) + cos(x) + sin(cos(x)))) + sin(0.5403023058681398))
1. result = (cos(cos(sin(x) + cos(x) + sin(1.0))) + sin(0.5403023058681398))
1. result = (cos(cos(1.8414709848078965)) + sin(0.5403023058681398))
1. result = (cos(-0.26738159169423864) + sin(0.5403023058681398))
1. result = (1.4788612620659656)
2. final result = 1.4788612620659656

4. tried 1,000 times for cos(cos(sin(x) + cos(x) + sin(cos(x)))) + sin(cos(cos(x))) = 0 but failed
```
