## 요구사항 분석
워너비슈퍼추어소프트에서는 이번에 엑셀과 비슷한 간단한 스프레드시트 작성 소프트웨어를 개발한다. 이 소프트웨어는 테이블에 대해 적용할 수 있는 다음의 함수를 포함한다.
* ```조건합계```: 사용 방법은 ```조건합계((열1, 열2, ..., 열N), (조건1, 조건2, ..., 조건N), 합계열)```
  * ```열1```의 값이 ```조건1```을 만족시키고, ```열2```의 값이 ```조건2```를 만족시키고, ..., ```열N```의 값이 ```조건N```을 모두 만족시키는 행들에 대해, ```합계열``` 열의 값들의 합을 구한다.
* ```조건개수```: 사용 방법은 ```조건개수((열1, 열2, ..., 열N), (조건1, 조건2, ..., 조건N))```
  * ```조건합계``` 함수와 사용법이 비슷하지만, ```조건합계``` 함수가 그 행의 해당 값들의 합계를 구하는 대신, ```조건개수```는 그 개수를 구한다.
* ```조건판정```: 사용 방법은 ```조건판정(행번호, 열이름, 조건)```
  * 해당하는 ```행번호```와 ```열이름```의 값이 ```조건```을 만족시키면 ```true```, 그렇지 않으면 ```false```를 반환한다.
* ```조건NOT```: 사용 방법은 ```조건NOT(행번호, 열이름, 조건)```
  * ```조건판정``` 함수와는 반대로, 해당하는 ```행번호```와 ```열이름```의 값이 ```조건```을 만족시키면 ```false```, 그렇지 않으면 ```true```를 반환한다.

여기서 ```열1```, ```열2```, ..., ```열N```은 해당 열의 이름이고, ```조건1```, ```조건2```, ..., ```조건N```, ```조건```과 같은 조건은 다음과 같은 형식을 따른다.
* ```>N```, ```>=N```, ```<N```, ```<=N```: ```N```보다 큰/크거나 같은/작은/작거나 같은 값인지 검사
* ```[A]```: 셀의 값이 A를 포함하는지 검사

```데이터 클래스```와 같은 형태로 정의된 테이블에 대해, 위의 각각의 함수를 만드시오.

### 데이터 클래스
```kotlin
// 테이블
data class Table (
    val name: String, // 테이블 이름
    val columns: List<String>, // 열 이름들
    val rows: List<Row> // 테이블의 각 행
)

// 테이블의 각 행
data class Row (
    val values: List<Any> // 해당 행의 각 열의 값
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 열 이름 및 값에 특수문자 및 공백 포함 불가능

// 테이블
data class Table (
    val name: String, // 테이블 이름
    val columns: List<String>, // 열 이름들
    val rows: List<Row> // 테이블의 각 행
)

// 테이블의 각 행
data class Row (
    val values: List<Any> // 해당 행의 각 열의 값
)

// 조건 판정
fun checkCond(value: String, condition: String): Boolean {

    // >N, >=N, <N, <=N: N보다 큰/크거나 같은/작은/작거나 같은 값인지 검사
    if (condition.contains(">") || condition.contains("<")) {
        val valInt = value.toInt()
        
        if (condition.contains(">=")) { // >=
            val toCompare = condition.substring(2).toInt()
            return valInt >= toCompare
        }
        else if (condition.contains("<=")) { // <=
            val toCompare = condition.substring(2).toInt()
            return valInt <= toCompare
        }
        else if (condition.contains(">")) { // >
            val toCompare = condition.substring(1).toInt()
            return valInt > toCompare
        }
        else { // <
            val toCompare = condition.substring(1).toInt()
            return valInt < toCompare
        }
    }
    
    // [A]: 셀의 값이 A를 포함하는지 검사
    else if (condition.contains("[") && condition.contains("]")) {
        with (condition) {
            val toCompare = this.substring(1, this.length - 1)
            return value.contains(toCompare)
        }
    }
    
    // 조건 일치 검사
    else {
        return value == condition
    }
}

// 테이블에서 (열1, 열2, ..., 열N), (조건1, 조건2, ..., 조건N)에 맞는 부분 추출
fun findMatchedRows(table: Table, funcStr: String, colNameToIndex: Map<String, Int>): List<Row> {
    val cols = funcStr.split(")")[0].split("(")[1].split(",")
    val conds = funcStr.split(")")[1].split("(")[1].split(",")
    val n = cols.size
    
    // 조건에 맞는 행만 필터링
    var matchedRows = table.rows
    (0..n-1).forEach { i ->
        matchedRows = matchedRows.filter { row ->
            val colIndex = colNameToIndex.get(cols[i])!!
            val value = row.values[colIndex]
            val condition = conds[i]
            
            checkCond(value.toString(), condition)
        }
    }
    
    return matchedRows
}

// 조건합계((열1, 열2, ..., 열N), (조건1, 조건2, ..., 조건N), 합계열)
fun sumCondition(table: Table, funcStr: String, colNameToIndex: Map<String, Int>): Int {
    val matchedRows = findMatchedRows(table, funcStr, colNameToIndex)
    val valueColName = funcStr.substringAfterLast(",")
    val valueColIndex = colNameToIndex.get(valueColName)!!
    
    println("1. matchedRows = ${matchedRows}")
    println("2. valueColName = ${valueColName}")
    println("3. valueColIndex = ${valueColIndex}")
    
    val result = matchedRows.sumOf {
        it.values[valueColIndex] as Int
    }
    return result
}

// 조건개수((열1, 열2, ..., 열N), (조건1, 조건2, ..., 조건N))
fun countCondition(table: Table, funcStr: String, colNameToIndex: Map<String, Int>): Int {
    val matchedRows = findMatchedRows(table, funcStr, colNameToIndex)
    
    println("4. matchedRows = ${matchedRows}")
    
    return matchedRows.size
}

// 조건판정(행번호, 열이름, 조건) 공통함수
fun checkConditionCommon(table: Table, funcStr: String, colNameToIndex: Map<String, Int>): Boolean {
    val rowIndex = funcStr.split(",")[0].toInt()
    val colName = funcStr.split(",")[1]
    val colIndex = colNameToIndex.get(colName)!!
    val condition = funcStr.split(",")[2]
    
    val targetValue = table.rows[rowIndex-1].values[colIndex].toString()
    
    println("5. rowIndex = ${rowIndex}")
    println("6. colName = ${colName}")
    println("7. colIndex = ${colIndex}")
    println("8. condition = ${condition}")
    println("9. targetValue = ${targetValue}")
    
    return checkCond(targetValue, condition)
}

// 조건판정(행번호, 열이름, 조건)
fun checkCondition(table: Table, funcStr: String, colNameToIndex: Map<String, Int>): Boolean {
    return checkConditionCommon(table, funcStr, colNameToIndex)
}

// 조건NOT(행번호, 열이름, 조건)
fun checkNotCondition(table: Table, funcStr: String, colNameToIndex: Map<String, Int>): Boolean {
    return !checkConditionCommon(table, funcStr, colNameToIndex)
}

fun main() {
    
    // 임시 테이블
    val testTable = Table(
        "example_table",
        listOf("bldg", "floor", "name", "type", "value"),
        listOf(
            Row(listOf("KAO", 3, "king", "A", 12)),
            Row(listOf("KAO", 2, "yellow", "B", 25)),
            Row(listOf("NAV", 13, "green", "B", 20)),
            Row(listOf("KAO", 7, "brown", "B", 7)),
            Row(listOf("NAV", 2, "darkGreen", "A", 39)),
            Row(listOf("GGL", 3, "blue", "A", 32)),
            Row(listOf("KAO", 12, "creative", "C", 16)),
            Row(listOf("NAV", 4, "AI-based", "C", 11)),
            Row(listOf("GGL", 6, "Alpha", "C", 7)),
            Row(listOf("GGL", 4, "Go Go!!", "A", 10))
        )
    )
    
    // 함수 처리
    val funcs = listOf(
        "조건합계((bldg, floor), (KAO, >=3), value)",
        "조건개수((bldg, type), ([A], B))",
        "조건판정(4, value, >5)",
        "조건NOT(5, name, [Green])"
    )
    
    // 열 이름을 번호로 매핑
    val colNameToIndex = mutableMapOf<String, Int>()
    testTable.columns.forEachIndexed { idx, value ->
        colNameToIndex.put(value, idx)
    }
    
    for (func in funcs) {
        
        // 함수 내용 부분 추출
        val funcStr = func.replace(" ", "").substringAfter("(").substringBeforeLast(")")

        val result = when (func.split("(")[0]) {
            "조건합계" -> sumCondition(testTable, funcStr, colNameToIndex)
            "조건개수" -> countCondition(testTable, funcStr, colNameToIndex)
            "조건판정" -> checkCondition(testTable, funcStr, colNameToIndex)
            "조건NOT" -> checkNotCondition(testTable, funcStr, colNameToIndex)
            else -> null
        }
        
        println("func str = ${funcStr}")
        println("result of \"${func}\" = ${result}\n")
    }
}
```

### 실행 결과
```kotlin
1. matchedRows = [Row(values=[KAO, 3, king, A, 12]), Row(values=[KAO, 7, brown, B, 7]), Row(values=[KAO, 12, creative, C, 16])]
2. valueColName = value
3. valueColIndex = 4
func str = (bldg,floor),(KAO,>=3),value
result of "조건합계((bldg, floor), (KAO, >=3), value)" = 35

4. matchedRows = [Row(values=[KAO, 2, yellow, B, 25]), Row(values=[NAV, 13, green, B, 20]), Row(values=[KAO, 7, brown, B, 7])]
func str = (bldg,type),([A],B)
result of "조건개수((bldg, type), ([A], B))" = 3

5. rowIndex = 4
6. colName = value
7. colIndex = 4
8. condition = >5
9. targetValue = 7
func str = 4,value,>5
result of "조건판정(4, value, >5)" = true

5. rowIndex = 5
6. colName = name
7. colIndex = 2
8. condition = [Green]
9. targetValue = darkGreen
func str = 5,name,[Green]
result of "조건NOT(5, name, [Green])" = false
```
