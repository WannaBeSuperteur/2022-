## 요구사항 분석
엑셀의 VLOOKUP 함수는 표의 첫 번째 열에서 값을 찾아서 원하는 열의 값을 반환한다. 여기서는 String의 2차원 배열에 대해 엑셀의 VLOOKUP 함수를 구현한다. 배열의 첫 번째 열부터 A, B, C, ... 열이고, 첫 번째 행부터 1, 2, 3, ... 행이다.
* 수식의 형식이 ```=VLOOKUP(값, 표, N, FALSE)``` 형식이 아니면 ```error: formula malformed```를 출력한다.
* 수식의 형식은 일치하지만 표의 셀 범위를 나타내는 형식이 ```A1:B30```과 같은 올바른 형식이 아니면 ```error: table range malformed```를 출력한다.
* 표의 첫 번째 열의 값이 일치하는 행이 여러 개이면, ```결과1, 결과2, 결과3```과 같이 결과값들을 쉼표로 구분하여 출력한다.
* 표의 첫 번째 열의 값이 일치하는 행이 없으면, ```결과 없음```을 출력한다.
* N의 값이 표의 열의 개수보다 많으면 ```error: N is too large```를 출력한다.
* 표의 범위가 2차원 배열 바깥쪽을 포함하면 ```error: over-ranged```를 출력한다.

단, VLOOKUP 함수의 인수 사이에는 반드시 ```, ``` 형식으로 쉼표 다음에 공백이 들어가며, 인수의 값 자체에는 쉼표가 없다. 또한 셀 범위 형식에서 ```:``` 왼쪽이 가리키는 셀이 그 오른쪽이 가리키는 셀보다 오른쪽 또는 아래에 있으면, 해당 셀 범위 형식을 잘못된 형식으로 간주한다.

### 데이터 클래스
데이터 클래스 없이, String의 2차원 배열과 ```=VLOOKUP(값, 표, N, FALSE)``` 형태의 수식이 주어진다.

## 코드 및 실행 결과
### 코드
```kotlin
// 열 번호의 알파벳을 숫자 값으로
fun getValue(colAlphabet: Char): Int {
    return colAlphabet.code - 'A'.code + 1
}

fun colNumToInt(colNum: String): Int {
    when (colNum.length) {
        1 -> return getValue(colNum[0])
        2 -> return 26 * getValue(colNum[0]) + getValue(colNum[1])
        3 -> return 676 * getValue(colNum[0]) + 26 * getValue(colNum[1]) + getValue(colNum[2])
        else -> return -1
    }
}

// VLOOKUP 함수 처리
fun handleVlookupFormula(formula: String, table: List<List<String>>): String {
    
    // 수식의 형식이 =VLOOKUP(값, 표, N, FALSE) 형식이 아니면
    if (!formula.startsWith("=VLOOKUP(") || !formula.endsWith(")")) {
        return "error: formula malformed"
    }
    if (!formula.contains("(") || !formula.contains(")")) {
        return "error: formula malformed"
    }
    
    val args = formula.substringAfter("(").substringBeforeLast(")").split(", ")
    if (args.size != 4 || args[3] != "FALSE") {
        return "error: formula malformed"
    }
    
    // 수식의 형식은 일치하지만 표의 셀 범위를 나타내는 형식이 A1:B30과 같은 올바른 형식이 아니면
    val range = args[1]
    val rangeCells = range.split(":")
    if (rangeCells.size != 2) {
        return "error: table range malformed"
    }
    println("1. args = ${args}, rangeCells = ${rangeCells}")
    
    val rangeInfo = mutableListOf(0, 0, 0, 0)
    
    // 첫 셀과 마지막 셀의 주소에 대해, 숫자 부분이 시작되는 인덱스 추출
    val startNumIndex = rangeCells[0].indexOfFirst {
        it in "0123456789"
    }
    val endNumIndex = rangeCells[1].indexOfFirst {
        it in "0123456789"
    }
    
    if (startNumIndex == 0 || endNumIndex == 0) {
        return "error: table range malformed"
    }
    
    // 알파벳을 숫자로 변환하고, 숫자 부분을 파싱하면서 오류 발생 시 형식이 잘못된 것임
    try {
        rangeInfo[0] = colNumToInt(rangeCells[0].substring(0, startNumIndex))
        rangeInfo[1] = rangeCells[0].substring(startNumIndex).toInt()
        rangeInfo[2] = colNumToInt(rangeCells[1].substring(0, endNumIndex))
        rangeInfo[3] = rangeCells[1].substring(endNumIndex).toInt()
    } catch (e: Exception) {
        return "error: table range malformed"
    }
    println("2. rangeInfo = ${rangeInfo}")
    
    // 셀 범위 형식에서 : 왼쪽 셀이 오른쪽 셀보다 오른쪽 또는 아래에 있으면 잘못된 형식
    if (rangeInfo[0] > rangeInfo[2] || rangeInfo[1] > rangeInfo[3]) {
        return "error: table range malformed"
    }
    
    // N의 값이 표의 열의 개수보다 많으면
    val N = args[2].toInt()
    if (N > rangeInfo[2] - rangeInfo[0] + 1) {
        return "N is too large"
    }
    
    // 표의 범위가 2차원 배열 바깥쪽을 포함하면
    if (rangeInfo[0] < 1 || rangeInfo[1] < 1 || rangeInfo[2] > table[0].size || rangeInfo[3] > table.size) {
        return "error: over-ranged"
    }
    
    // 일치하는 행의 번호 찾기
    val valueToFind = args[0]
    val startRowIdx = rangeInfo[1] - 1
    val endRowIdx = rangeInfo[3] - 1
    val startColIdx = rangeInfo[0] - 1
    
    val matchedRows = (0..endRowIdx - startRowIdx).filter {
        table[it + startRowIdx][startColIdx] == valueToFind
    }
    val matchedValues = matchedRows.map {
        table[startRowIdx + it][startColIdx + N - 1]
    }
    println("3. matchedRows = ${matchedRows}")
    
    // 최종 결과 반환
    if (matchedValues.size > 0) {
        return matchedValues.joinToString(", ")
    } else {
        return "결과 없음"
    }
}

fun main() {
    
    // 임시 2차원 배열
    val tempArray = listOf(
        listOf("학번", "국어", "수학", "영어", "코딩", "합계"),
        listOf("10301", "80", "90", "100", "100", "370"),
        listOf("10302", "85", "93", "96", "100", "374"),
        listOf("10303", "85", "94", "96", "97", "372"),
        listOf("10304", "78", "94", "99", "100", "371"),
        listOf("10305", "86", "94", "100", "99", "379"),
        listOf("10306", "84", "92", "96", "97", "369"),
        listOf("10307", "80", "88", "97", "100", "365"),
        listOf("10308", "80", "90", "100", "98", "368"),
        listOf("10309", "89", "100", "100", "99", "388"),
        listOf("10310", "86", "96", "96", "99", "387")
    )
    
    // VLOOKUP 함수 처리
    val queries = listOf<String> (
        "=VLOOKUP(10302, A1:F11, 1, FALSE)",
        "=VLOOKUP(10302, A1:F11, 2, FALSE)",
        "=VLOOKUP(10302, A1:F11, 3, FALSE)",
        "=VLOOKUP(10302, A1:F11, 4, FALSE)",
        "=VLOOKUP(10302, A1:F11, 5, FALSE)",
        "=VLOOKUP(10302, A1:F11, 6, FALSE)",
        "VLOOKUP(10302, A1:F11, 3, FALSE)",
        "=VLOOKUP 10302 A1:F11 3 FALSE",
        "=VLOOKUP(10302 A1:F11 3 FALSE)",
        "=VLOOKUP(10302, A1:F11 3 FALSE)",
        "=VLOOKUP(10302, A1:F11, 3 FALSE)",
        "=VLOOKUP[10302, A1:F11, 3, FALSE]",
        "=VLOOKUP(10302, A1~F11, 3, FALSE)",
        "=VLOOKUP(10302, 1A:11F, 3, FALSE)",
        "=VLOOKUP(10302, A1:11F, 3, FALSE)",
        "=VLOOKUP(10302, A1:11, 3, FALSE)",
        "=VLOOKUP(10302, (1.1.6.11), 3, FALSE)",
        "=VLOOKUP(10302, F11:A1, 3, FALSE)",
        "=VLOOKUP(80, B1:F11, 2, FALSE)",
        "=VLOOKUP(80, B1:F11, 4, FALSE)",
        "=VLOOKUP(84, B1:F11, 2, FALSE)",
        "=VLOOKUP(84, B1:F11, 4, FALSE)",
        "=VLOOKUP(90, B1:F11, 2, FALSE)",
        "=VLOOKUP(90, B1:F11, 4, FALSE)",
        "=VLOOKUP(80, B6:F11, 2, FALSE)",
        "=VLOOKUP(80, B6:F11, 4, FALSE)",
        "=VLOOKUP(10302, A1:F11, 10, FALSE)",
        "=VLOOKUP(84, B1:F11, 6, FALSE)",
        "=VLOOKUP(86, B1:B11, 2, FALSE)",
        "=VLOOKUP(10302, A1:F12, 3, FALSE)",
        "=VLOOKUP(10302, A1:G11, 3, FALSE)",
        "=VLOOKUP(10302, A1:G12, 3, FALSE)"
    )
    
    queries.forEach {
        println("${it} -> [${handleVlookupFormula(it, tempArray)}]\n")
    }
}
```

### 실행 결과
```kotlin
1. args = [10302, A1:F11, 1, FALSE], rangeCells = [A1, F11]
2. rangeInfo = [1, 1, 6, 11]
3. matchedRows = [2]
=VLOOKUP(10302, A1:F11, 1, FALSE) -> [10302]

1. args = [10302, A1:F11, 2, FALSE], rangeCells = [A1, F11]
2. rangeInfo = [1, 1, 6, 11]
3. matchedRows = [2]
=VLOOKUP(10302, A1:F11, 2, FALSE) -> [85]

1. args = [10302, A1:F11, 3, FALSE], rangeCells = [A1, F11]
2. rangeInfo = [1, 1, 6, 11]
3. matchedRows = [2]
=VLOOKUP(10302, A1:F11, 3, FALSE) -> [93]

1. args = [10302, A1:F11, 4, FALSE], rangeCells = [A1, F11]
2. rangeInfo = [1, 1, 6, 11]
3. matchedRows = [2]
=VLOOKUP(10302, A1:F11, 4, FALSE) -> [96]

1. args = [10302, A1:F11, 5, FALSE], rangeCells = [A1, F11]
2. rangeInfo = [1, 1, 6, 11]
3. matchedRows = [2]
=VLOOKUP(10302, A1:F11, 5, FALSE) -> [100]

1. args = [10302, A1:F11, 6, FALSE], rangeCells = [A1, F11]
2. rangeInfo = [1, 1, 6, 11]
3. matchedRows = [2]
=VLOOKUP(10302, A1:F11, 6, FALSE) -> [374]

VLOOKUP(10302, A1:F11, 3, FALSE) -> [error: formula malformed]

=VLOOKUP 10302 A1:F11 3 FALSE -> [error: formula malformed]

=VLOOKUP(10302 A1:F11 3 FALSE) -> [error: formula malformed]

=VLOOKUP(10302, A1:F11 3 FALSE) -> [error: formula malformed]

=VLOOKUP(10302, A1:F11, 3 FALSE) -> [error: formula malformed]

=VLOOKUP[10302, A1:F11, 3, FALSE] -> [error: formula malformed]

=VLOOKUP(10302, A1~F11, 3, FALSE) -> [error: table range malformed]

1. args = [10302, 1A:11F, 3, FALSE], rangeCells = [1A, 11F]
=VLOOKUP(10302, 1A:11F, 3, FALSE) -> [error: table range malformed]

1. args = [10302, A1:11F, 3, FALSE], rangeCells = [A1, 11F]
=VLOOKUP(10302, A1:11F, 3, FALSE) -> [error: table range malformed]

1. args = [10302, A1:11, 3, FALSE], rangeCells = [A1, 11]
=VLOOKUP(10302, A1:11, 3, FALSE) -> [error: table range malformed]

=VLOOKUP(10302, (1.1.6.11), 3, FALSE) -> [error: table range malformed]

1. args = [10302, F11:A1, 3, FALSE], rangeCells = [F11, A1]
2. rangeInfo = [6, 11, 1, 1]
=VLOOKUP(10302, F11:A1, 3, FALSE) -> [error: table range malformed]

1. args = [80, B1:F11, 2, FALSE], rangeCells = [B1, F11]
2. rangeInfo = [2, 1, 6, 11]
3. matchedRows = [1, 7, 8]
=VLOOKUP(80, B1:F11, 2, FALSE) -> [90, 88, 90]

1. args = [80, B1:F11, 4, FALSE], rangeCells = [B1, F11]
2. rangeInfo = [2, 1, 6, 11]
3. matchedRows = [1, 7, 8]
=VLOOKUP(80, B1:F11, 4, FALSE) -> [100, 100, 98]

1. args = [84, B1:F11, 2, FALSE], rangeCells = [B1, F11]
2. rangeInfo = [2, 1, 6, 11]
3. matchedRows = [6]
=VLOOKUP(84, B1:F11, 2, FALSE) -> [92]

1. args = [84, B1:F11, 4, FALSE], rangeCells = [B1, F11]
2. rangeInfo = [2, 1, 6, 11]
3. matchedRows = [6]
=VLOOKUP(84, B1:F11, 4, FALSE) -> [97]

1. args = [90, B1:F11, 2, FALSE], rangeCells = [B1, F11]
2. rangeInfo = [2, 1, 6, 11]
3. matchedRows = []
=VLOOKUP(90, B1:F11, 2, FALSE) -> [결과 없음]

1. args = [90, B1:F11, 4, FALSE], rangeCells = [B1, F11]
2. rangeInfo = [2, 1, 6, 11]
3. matchedRows = []
=VLOOKUP(90, B1:F11, 4, FALSE) -> [결과 없음]

1. args = [80, B6:F11, 2, FALSE], rangeCells = [B6, F11]
2. rangeInfo = [2, 6, 6, 11]
3. matchedRows = [2, 3]
=VLOOKUP(80, B6:F11, 2, FALSE) -> [88, 90]

1. args = [80, B6:F11, 4, FALSE], rangeCells = [B6, F11]
2. rangeInfo = [2, 6, 6, 11]
3. matchedRows = [2, 3]
=VLOOKUP(80, B6:F11, 4, FALSE) -> [100, 98]

1. args = [10302, A1:F11, 10, FALSE], rangeCells = [A1, F11]
2. rangeInfo = [1, 1, 6, 11]
=VLOOKUP(10302, A1:F11, 10, FALSE) -> [N is too large]

1. args = [84, B1:F11, 6, FALSE], rangeCells = [B1, F11]
2. rangeInfo = [2, 1, 6, 11]
=VLOOKUP(84, B1:F11, 6, FALSE) -> [N is too large]

1. args = [86, B1:B11, 2, FALSE], rangeCells = [B1, B11]
2. rangeInfo = [2, 1, 2, 11]
=VLOOKUP(86, B1:B11, 2, FALSE) -> [N is too large]

1. args = [10302, A1:F12, 3, FALSE], rangeCells = [A1, F12]
2. rangeInfo = [1, 1, 6, 12]
=VLOOKUP(10302, A1:F12, 3, FALSE) -> [error: over-ranged]

1. args = [10302, A1:G11, 3, FALSE], rangeCells = [A1, G11]
2. rangeInfo = [1, 1, 7, 11]
=VLOOKUP(10302, A1:G11, 3, FALSE) -> [error: over-ranged]

1. args = [10302, A1:G12, 3, FALSE], rangeCells = [A1, G12]
2. rangeInfo = [1, 1, 7, 12]
=VLOOKUP(10302, A1:G12, 3, FALSE) -> [error: over-ranged]
```
