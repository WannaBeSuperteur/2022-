## 요구사항 분석
코틀린에서는 ```val x```로 선언한 변수에 값을 다시 할당하면 안 되고, 기존 변수의 자료형과 불일치하는 자료형의 값을 새로 할당해서도 안 되고, 없는 변수의 값을 참조해서도 안 된다.

코드가 주어졌을 때, 해당 코드에서 발생하는 위와 같은 오류를 모두 찾아서 출력하시오.
* ```val x``` 재할당 : ```val로 선언된 (변수명)에 재할당할 수 없습니다.```
* 자료형 불일치 : ```(변수명)의 현재 자료형은 (자료형)으로, 새로운 자료형 (자료형)으로 변환할 수 없습니다.```
* 없는 변수의 값 참조 : ```(변수명) 변수가 존재하지 않습니다.```

단, ```toInt()```, ```toDouble()```, ```toString()``` 등 자료형 변환은 하지 않는다.

### 데이터 클래스
데이터 클래스 없이 코드가 ```String``` 자료형으로 주어진다.

## 코드 및 실행 결과
### 코드
```kotlin
// 가정 : 0. 각 라인의 ' = ' 오른쪽을 연결하는 기호는 ' + ' 뿐임
//       1. Int + Double -> Double, Int + String -> String, Double + String -> String
//       2. 오류가 있는 라인은 실행되지 않고 넘어감

// 변수 정보
data class Variable (
    val name: String,
    val value: String,
    val data_type: String,
    val is_val: Boolean
)

// 오류 메시지 (한 줄에 여러 개 가능)
data class ErrorMessage (
    val line_no: Int,
    val message: String
)

// 오류 확인
fun checkErrors(code: String) {
    
    // 변수 정보 및 오류 메시지 저장
    val variables = mutableListOf<Variable>()
    val errorMsgs = mutableListOf<ErrorMessage>() 
    
    // 각 라인의 오류 확인
    val lines = code.split("\n")
    
    for (i in 0 until lines.size) {
        val line = lines[i]
        val left = line.substringBefore(" = ")
        val right = line.substringAfter(" = ")
        val rightSplit = right.split(" + ").toMutableList()
        
        // Int -> Double -> String 이므로
        var dataType = (0 until rightSplit.size).map { "Int" }.toMutableList()
        
        // 오류가 있는 변수의 인덱스는 제외
        val isErrorVariable = (0 until rightSplit.size).map { false }.toMutableList()
        
        // 자료형 검사 (이미 String인 경우 불필요)
        rightSplit.forEachIndexed { idx, v ->
            var newV = v
            
            // 변수인 경우 해당 변수가 없으면 오류
            if (v.all { it.isLetter() } && !v.contains("\"")) {
                val variableInfo = variables.find { it.name == v }
                
                if (variableInfo == null) {
                    isErrorVariable[idx] = true
                    
                    errorMsgs.add(
                        ErrorMessage(i + 1, "${v} 변수가 존재하지 않습니다.")
                    )
                }
                
                // 해당 변수가 있으면 해당 변수의 정보로 교체
                else {
                    rightSplit[idx] = variableInfo.value
                    newV = rightSplit[idx]
                }
            }
            
            // 값인 경우 (변수의 값인 경우 포함)
            if (dataType[idx] != "String") {
                val isInt = newV.toIntOrNull() != null
                val isDouble = newV.toDoubleOrNull() != null
            
                if (dataType[idx] == "Int" && !isInt) {
                    if (isDouble) {
                        dataType[idx] = "Double"
                    } else {
                        dataType[idx] = "String"
                    }
                } else if (dataType[idx] == "Double" && !isDouble) {
                    dataType[idx] = "String"
                }
            }
        }
        
        // 새롭게 할당되는 값 구하기
        var meetErrorVariable = false
        var currentDataType = "Int"
        
        val newValue = rightSplit.foldIndexed ( "" ) { idx, acc, e ->
            
            // 해당 변수가 없는 경우가 한번이라도 있으면 제외
            if (isErrorVariable[idx]) {
                meetErrorVariable = true
            }
            
            // 해당 인덱스까지 계산한 결과
            val result = if (meetErrorVariable) {
                acc
            } else {
                
                // String인 경우 큰따옴표 제거
                val newE = if (dataType[idx] == "String" && e.contains("\"")) {
                    e.replace("\"", "")
                } else {
                    e
                }
                
                // 연산 결과 계산
                if (idx == 0) {
                    newE
                } else {
                    
                    // 현재까지의 데이터 타입과 해당 변수의 데이터 타입을 이용
                    when (currentDataType) {
                        "Int" -> {
                            when (dataType[idx]) {
                                "Int" -> (acc.toInt() + newE.toInt()).toString()
                                "Double" -> (acc.toDouble() + newE.toDouble()).toString()
                                "String" -> acc + newE
                                else -> acc + newE
                            }
                        }
                        "Double" -> {
                            when (dataType[idx]) {
                                "Int", "Double" -> (acc.toDouble() + newE.toDouble()).toString()
                                "String" -> acc + newE
                                else -> acc + newE
                            }
                        }
                        "String" -> {
                            acc + newE
                        }
                        else -> {
                            acc + newE
                        }
                    }
                }
            }
            
            // 현재까지의 데이터 타입을 갱신
            if (dataType[idx] == "Double" && currentDataType == "Int") {
                currentDataType = "Double"
            } else if (dataType[idx] == "String" && currentDataType != "String") {
                currentDataType = "String"
            }
            
            result
        }
        
        // 해당 변수가 없는 경우 처리
        if (newValue.isEmpty()) {
            continue
        }
        
        // 값을 저장할 변수가 존재하는 경우, 자료형 및 val 여부 확인
        val valOrVar = left.substringBefore(" ")
        val argName = left.substringAfter(" ")
        
        val variableInfo = variables.find { it.name == argName }
        
        if (variableInfo != null) {
            
            // 오류 확인
            if (variableInfo.is_val) {
                errorMsgs.add(
                    ErrorMessage(i + 1, "val로 선언된 ${argName}에 재할당할 수 없습니다.")
                )
            } else if (variableInfo.data_type != currentDataType) {
                errorMsgs.add(
                    ErrorMessage(
                        i + 1, 
                        "${argName}의 현재 자료형은 ${variableInfo.data_type}으로, " +
                        "새로운 자료형 ${currentDataType}으로 변환할 수 없습니다."
                    )
                )
            }
            
            // 오류가 없으면 해당 변수의 값만 변경
            else {
                val index = variables.indexOfFirst { it.name == argName }
                val varInfo = variables[index]
                
                variables[index] = Variable (
                    name = varInfo.name,
                    value = newValue,
                    data_type = varInfo.data_type,
                    is_val = varInfo.is_val
                )
            }
        }
        
        // 값을 저장할 변수가 없는 경우
        else {
            
            // 변수를 새로 선언하는 경우
            if (valOrVar in listOf("val", "var")) {
                variables.add (
                    Variable (
                        name = argName,
                        value = newValue,
                        data_type = currentDataType,
                        is_val = valOrVar == "val"
                    )
                )
            }
            
            // 그렇지 않은 경우
            else {
                errorMsgs.add(
                    ErrorMessage(i + 1, "${argName} 변수가 존재하지 않습니다.")
                )
            }
        }
        
        // 변수 정보 출력
        val printValOrVar: ((Variable) -> String) = {
            if (it.is_val) { "val" } else { "var" }
        }
        println("1. ${variables.map { "${printValOrVar(it)} ${it.data_type} ${it.name}=${it.value}" }}")
        println("2. ${currentDataType} ${dataType}\n")
    }
    
    // 오류 메시지 출력
    println("Error of \"${code.replace("\n", "; ")}\" :")
    errorMsgs.forEach {
        println("at line ${it.line_no} : ${it.message}")
    }
    print("\n")
}

fun main() {
    val tempCodes = listOf<String> (
        "val x = 1234\nval y = 2345\nval z = x + y\nz = z + 100",
        "var x = 123.45\nx = 6",
        "val a = 10\nval b = 20\nval c = 30\nvar x = a + b + c\na = 45\nx = a + b + c + 20",
        "val a = 45\nval b = 70\nval x = a + b + c\nval y = x + a + b + d + 20",
        "var x = 123.45\nval y = 12\nval z = y + 100\nx = y + z",
        "var y = 10\nval z = 20\nval x = y + z + 10\ny = a + 100\nx = y + b + 500",
        "val a = 10.12\nval b = 24\nvar c = a + b\nc = 16.24\na = 18.56",
        "val a = 10.12\nval b = 24\nvar c = a + b\nc = 24\nc = \"string_test\"",
        "val a = 10.12\nval b = 24\nvar c = a + \"str_test\"\nc = 24.1628\nc = 30",
        "val x = \"x_is_a_string\"\nvar y = 20\nval z = x + y\ny = \"y_can_be_a_string\"\nz = 987",
        "val aaa = 30\naaa = bbb + 45.12",
        "val aaa = 45\naaa = bbb + \"string_test\" + 34.56",
        "val aaa = 45\naaa = \"string_test\" + 34.56 + bbb",
        "val aaa = 45\naaa = \"string_test\" + 34.56",
        "var aaa = 45\naaa = \"string_test\" + 34.56",
        "val aaa = 56\nbbb = aaa + 30",
        "val aaa = 56\nval bbb = aaa + 30"
    )
    
    // 각 코드에 대해 오류 확인
    tempCodes.forEach {
        checkErrors(it)
    }
}
```

### 실행 결과
```kotlin
1. [val Int x=1234]
2. Int [Int]

1. [val Int x=1234, val Int y=2345]
2. Int [Int]

1. [val Int x=1234, val Int y=2345, val Int z=3579]
2. Int [Int, Int]

1. [val Int x=1234, val Int y=2345, val Int z=3579]
2. Int [Int, Int]

Error of "val x = 1234; val y = 2345; val z = x + y; z = z + 100" :
at line 4 : val로 선언된 z에 재할당할 수 없습니다.

1. [var Double x=123.45]
2. Double [Double]

1. [var Double x=123.45]
2. Int [Int]

Error of "var x = 123.45; x = 6" :
at line 2 : x의 현재 자료형은 Double으로, 새로운 자료형 Int으로 변환할 수 없습니다.

1. [val Int a=10]
2. Int [Int]

1. [val Int a=10, val Int b=20]
2. Int [Int]

1. [val Int a=10, val Int b=20, val Int c=30]
2. Int [Int]

1. [val Int a=10, val Int b=20, val Int c=30, var Int x=60]
2. Int [Int, Int, Int]

1. [val Int a=10, val Int b=20, val Int c=30, var Int x=60]
2. Int [Int]

1. [val Int a=10, val Int b=20, val Int c=30, var Int x=80]
2. Int [Int, Int, Int, Int]

Error of "val a = 10; val b = 20; val c = 30; var x = a + b + c; a = 45; x = a + b + c + 20" :
at line 5 : val로 선언된 a에 재할당할 수 없습니다.

1. [val Int a=45]
2. Int [Int]

1. [val Int a=45, val Int b=70]
2. Int [Int]

1. [val Int a=45, val Int b=70, val String x=115]
2. String [Int, Int, String]

1. [val Int a=45, val Int b=70, val String x=115, val String y=230]
2. String [Int, Int, Int, String, Int]

Error of "val a = 45; val b = 70; val x = a + b + c; val y = x + a + b + d + 20" :
at line 3 : c 변수가 존재하지 않습니다.
at line 4 : d 변수가 존재하지 않습니다.

1. [var Double x=123.45]
2. Double [Double]

1. [var Double x=123.45, val Int y=12]
2. Int [Int]

1. [var Double x=123.45, val Int y=12, val Int z=112]
2. Int [Int, Int]

1. [var Double x=123.45, val Int y=12, val Int z=112]
2. Int [Int, Int]

Error of "var x = 123.45; val y = 12; val z = y + 100; x = y + z" :
at line 4 : x의 현재 자료형은 Double으로, 새로운 자료형 Int으로 변환할 수 없습니다.

1. [var Int y=10]
2. Int [Int]

1. [var Int y=10, val Int z=20]
2. Int [Int]

1. [var Int y=10, val Int z=20, val Int x=40]
2. Int [Int, Int, Int]

1. [var Int y=10, val Int z=20, val Int x=40]
2. String [Int, String, Int]

Error of "var y = 10; val z = 20; val x = y + z + 10; y = a + 100; x = y + b + 500" :
at line 4 : a 변수가 존재하지 않습니다.
at line 5 : b 변수가 존재하지 않습니다.
at line 5 : val로 선언된 x에 재할당할 수 없습니다.

1. [val Double a=10.12]
2. Double [Double]

1. [val Double a=10.12, val Int b=24]
2. Int [Int]

1. [val Double a=10.12, val Int b=24, var Double c=34.12]
2. Double [Double, Int]

1. [val Double a=10.12, val Int b=24, var Double c=16.24]
2. Double [Double]

1. [val Double a=10.12, val Int b=24, var Double c=16.24]
2. Double [Double]

Error of "val a = 10.12; val b = 24; var c = a + b; c = 16.24; a = 18.56" :
at line 5 : val로 선언된 a에 재할당할 수 없습니다.

1. [val Double a=10.12]
2. Double [Double]

1. [val Double a=10.12, val Int b=24]
2. Int [Int]

1. [val Double a=10.12, val Int b=24, var Double c=34.12]
2. Double [Double, Int]

1. [val Double a=10.12, val Int b=24, var Double c=34.12]
2. Int [Int]

1. [val Double a=10.12, val Int b=24, var Double c=34.12]
2. String [String]

Error of "val a = 10.12; val b = 24; var c = a + b; c = 24; c = "string_test"" :
at line 4 : c의 현재 자료형은 Double으로, 새로운 자료형 Int으로 변환할 수 없습니다.
at line 5 : c의 현재 자료형은 Double으로, 새로운 자료형 String으로 변환할 수 없습니다.

1. [val Double a=10.12]
2. Double [Double]

1. [val Double a=10.12, val Int b=24]
2. Int [Int]

1. [val Double a=10.12, val Int b=24, var String c=10.12str_test]
2. String [Double, String]

1. [val Double a=10.12, val Int b=24, var String c=10.12str_test]
2. Double [Double]

1. [val Double a=10.12, val Int b=24, var String c=10.12str_test]
2. Int [Int]

Error of "val a = 10.12; val b = 24; var c = a + "str_test"; c = 24.1628; c = 30" :
at line 4 : c의 현재 자료형은 String으로, 새로운 자료형 Double으로 변환할 수 없습니다.
at line 5 : c의 현재 자료형은 String으로, 새로운 자료형 Int으로 변환할 수 없습니다.

1. [val String x=x_is_a_string]
2. String [String]

1. [val String x=x_is_a_string, var Int y=20]
2. Int [Int]

1. [val String x=x_is_a_string, var Int y=20, val String z=x_is_a_string20]
2. String [String, Int]

1. [val String x=x_is_a_string, var Int y=20, val String z=x_is_a_string20]
2. String [String]

1. [val String x=x_is_a_string, var Int y=20, val String z=x_is_a_string20]
2. Int [Int]

Error of "val x = "x_is_a_string"; var y = 20; val z = x + y; y = "y_can_be_a_string"; z = 987" :
at line 4 : y의 현재 자료형은 Int으로, 새로운 자료형 String으로 변환할 수 없습니다.
at line 5 : val로 선언된 z에 재할당할 수 없습니다.

1. [val Int aaa=30]
2. Int [Int]

Error of "val aaa = 30; aaa = bbb + 45.12" :
at line 2 : bbb 변수가 존재하지 않습니다.

1. [val Int aaa=45]
2. Int [Int]

Error of "val aaa = 45; aaa = bbb + "string_test" + 34.56" :
at line 2 : bbb 변수가 존재하지 않습니다.

1. [val Int aaa=45]
2. Int [Int]

1. [val Int aaa=45]
2. String [String, Double, String]

Error of "val aaa = 45; aaa = "string_test" + 34.56 + bbb" :
at line 2 : bbb 변수가 존재하지 않습니다.
at line 2 : val로 선언된 aaa에 재할당할 수 없습니다.

1. [val Int aaa=45]
2. Int [Int]

1. [val Int aaa=45]
2. String [String, Double]

Error of "val aaa = 45; aaa = "string_test" + 34.56" :
at line 2 : val로 선언된 aaa에 재할당할 수 없습니다.

1. [var Int aaa=45]
2. Int [Int]

1. [var Int aaa=45]
2. String [String, Double]

Error of "var aaa = 45; aaa = "string_test" + 34.56" :
at line 2 : aaa의 현재 자료형은 Int으로, 새로운 자료형 String으로 변환할 수 없습니다.

1. [val Int aaa=56]
2. Int [Int]

1. [val Int aaa=56]
2. Int [Int, Int]

Error of "val aaa = 56; bbb = aaa + 30" :
at line 2 : bbb 변수가 존재하지 않습니다.

1. [val Int aaa=56]
2. Int [Int]

1. [val Int aaa=56, val Int bbb=86]
2. Int [Int, Int]

Error of "val aaa = 56; val bbb = aaa + 30" :
```
