## 요구사항 분석
SQL-LIKE 문은 SQL과 유사한 형태의 데이터베이스 언어이다.

여러 개의 테이블들이 주어져 있을 때, 다음의 SQL-LIKE 문을 수행하고 그 결과를 출력하는 코드를 작성하시오.

실행 가능한 SQL-LIKE 문의 예시는 다음과 같다. (단, 괄호는 사용할 수 없으며, ```AND```를 ```OR```보다 먼저 처리한다. 고정된 값을 참조할 때는 양쪽에 작은따옴표를 붙인다.)

* ```SELECT COL1,COL2,COL3 FROM TABLE1 WHERE COL2=VAL1 AND COL3=VAL2```
  * ```TABLE1```에서 ```COL2``` 열의 값이 ```VAL1```이고 ```COL3``` 열의 값이 ```VAL2```인 모든 행에 대해, 그 행의 ```COL1```, ```COL2```, ```COL3``` 열의 값을 출력한다.
* ```SELECT * FROM TABLE1 WHERE COL1 = VAL3 OR COL2 = VAL4```
  * ```TABLE1```에서 ```COL1``` 열의 값이 ```VAL3```이거나 ```COL2``` 열의 값이 ```VAL4```인 모든 행에 대해, 그 행의 모든 열의 값을 출력한다.
* ```SELECT COL4,COL5 FROM TABLE1```
  * ```TABLE1```에서 모든 행에 대해, 그 행의 ```COL4```, ```COL5``` 열의 값을 출력한다.
* ```SELECT * FROM TABLE1 WHERE COL1 = VAL5 OR COL2 = VAL6 AND COL3 = VAL7```
  * ```TABLE1```에서 ```COL1``` 열의 값이 ```VAL5```이거나, ```COL2``` 열의 값이 ```VAL6```이면서 ```COL3``` 열의 값이 ```VAL7```인 모든 행에 대해, 그 행의 모든 열의 값을 출력한다.
* ```SELECT * FROM TABLE1 WHERE COL1 = 'abc' ORDER BY COL2 ASC COL3 DESC```
  * ```TABLE1```에서 ```COL1``` 열의 값이 ```abc```인 모든 행에 대해, 그 행의 모든 열의 값을 출력한다. 단, ```COL2```의 오름차순, ```COL3```의 내림차순으로 정렬 우선 순위를 정한다.
* ```SELECT TABLE1.COL1,TABLE1.COL3,TABLE2.COL4 FROM TABLE1,TABLE2 WHERE TABLE1.COL1 = TABLE2.COL2 AND TABLE1.COL3 = '200'```
  * ```TABLE1```과 ```TABLE2```의 카티션 곱에 대해, ```TABLE1```의 열 ```COL1```의 값이 ```TABLE2```의 열 ```COL2``` 값과 같고, ```TABLE1```의 열 ```COL3```의 값이 ```200```인 모든 행에 대해, ```TABLE1```의 열 ```COL1```, ```COL3```의 값과 ```TABLE2```의 열 ```COL4```의 값을 출력한다.

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
// 데이터베이스
data class DataBase (
    val tables: List<Table>, // 데이터베이스 테이블들 
    val queries: List<String> // SQL-LIKE 쿼리들
)

// 데이터베이스의 테이블
data class Table (
    val name: String, // 테이블 이름
    val column_name: List<String>, // 열 이름
    val rows: List<Row> // 테이블을 이루는 행
)

// 테이블을 이루는 각 행
data class Row (
    val row: List<String> // 각 행 (같은 테이블의 column_name과 원소 개수 일치)
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 데이터베이스
data class DataBase (
    val tables: List<Table>, // 데이터베이스 테이블들 
    val queries: List<String> // SQL-LIKE 쿼리들
)

// 데이터베이스의 테이블
data class Table (
    val name: String, // 테이블 이름
    val column_name: List<String>, // 열 이름
    val rows: List<Row> // 테이블을 이루는 행
)

// 테이블을 이루는 각 행
data class Row (
    val row: List<String> // 각 행 (같은 테이블의 column_name과 원소 개수 일치)
)

// 공백 있는 출력
fun getSpacedOutput(strs: List<String>, n: Int): String {
    return strs.map {
        if (it.length >= n) {
            it
        } else {
            it + " ".repeat(n - it.length)
        }
    }.joinToString("")
}

// SQL-LIKE 문 실행하여 결과 출력
fun executeQuery(tables: List<Table>, query: String) {
    
    // SELECT, FROM, WHERE 파트로 나누기
    val selectPart = query.substringBefore(" FROM ").substringAfter("SELECT ")
    val fromWherePart = query.substringAfter(" FROM ").substringBefore(" ORDER BY ")
    val orderByPart = if (query.contains(" ORDER BY ")) {
        query.substringAfter(" ORDER BY ")
    } else {
        ""
    }
    
    lateinit var fromPart: String
    lateinit var wherePart: String
    
    if (fromWherePart.contains(" WHERE ")) {
        fromPart = fromWherePart.substringBefore(" WHERE ")
        wherePart = fromWherePart.substringAfter(" WHERE ")
    } else {
        fromPart = fromWherePart
        wherePart = ""
    }
    
    println("1. select part = ${selectPart}, from part = ${fromPart}, where part = ${wherePart}")

    // 테이블 이름
    val tableMap = tables.filter { fromPart.contains(it.name) }.map { it.name to it }.toMap()
    
    tableMap.forEach {
        println("2. table name = ${it.key}, columns = ${it.value.column_name}")
    }
    
    // 각 테이블별로 열의 인덱스를 표시
    val tableColIndices = tableMap.map {
        val tableName = it.key
        val colIndices = it.value.column_name.mapIndexed { idx, colName ->
            colName to idx
        }.toMap()
        
        tableName to colIndices
    }.toMap()
    println("3. table column indices = ${tableColIndices}\n")
    
    // 출력할 열만 표시
    val outputTableColIndices = if (selectPart == "*") {
        tableColIndices   
    } else {
        val selectedColNames = selectPart.split(",")
        println("4. select column names = ${selectedColNames}")
        
        val tableFiltered = tableColIndices.filter { table ->
            table.key in tableMap.keys
        }
        println("5. output : table filtered = ${tableFiltered}")
        
        tableFiltered.map { table ->
            val colsToPrint = table.value.filter { col ->
                if (tableFiltered.size == 1) {
                    "${table.key}.${col.key}" in selectedColNames || col.key in selectedColNames
                } else {
                    "${table.key}.${col.key}" in selectedColNames
                }
            }
            table.key to colsToPrint
        }.toMap()
    }
    println("6. output : column filtered = ${outputTableColIndices}\n")
    
    // 전체 테이블 (필터링된 테이블에 없는 조건을 참조하기 위함)
    lateinit var wholeTable: Table
    lateinit var wholeIndex: Map<String, Int>
    
    // 조건에 따라 행을 필터링하기 전의 테이블
    // 대상 테이블이 1개인 경우
    val finalTable = if (outputTableColIndices.size == 1) {
        
        // 테이블 및 행의 개수
        val thisTable = tables.find {
            it.name == fromPart
        }!!
        wholeTable = thisTable
        wholeIndex = tableColIndices.get(thisTable.name)!!
        
        val rowCount = thisTable.rows.size
        println("7. row count : ${rowCount}")
        
        // 열 필터링하기
        val thisTableColIndices = outputTableColIndices.get(thisTable.name)!!
        
        // 최종 테이블
        Table (
            thisTable.name,
            thisTable.column_name.filter {
                it in thisTableColIndices.keys
            },
            thisTable.rows.map { rs ->
                Row (
                    rs.row.filterIndexed { idx, r ->
                        idx in thisTableColIndices.values
                    }
                )
            }
        )
    }
    
    // 대상 테이블이 2개인 경우 (최대 2개)
    else {
        
        // 카티션 곱 만들기
        val firstTable = tables.find {
            it.name == fromPart.split(",")[0]
        }!!
        val secondTable = tables.find {
            it.name == fromPart.split(",")[1]
        }!!
        
        // 최종 테이블
        val rows0 = firstTable.rows.size // 첫번째 테이블의 행 개수
        val rows1 = secondTable.rows.size // 두번째 테이블의 행 개수
        val cols0 = firstTable.column_name.size // 첫번째 테이블의 열 개수
        
        wholeIndex = tableColIndices.get(firstTable.name)!!.map {
            "${firstTable.name}.${it.key}" to it.value
        }.toMap() + tableColIndices.get(secondTable.name)!!.map {
            "${secondTable.name}.${it.key}" to it.value + cols0
        }.toMap()
        
        println("8. table name = ${firstTable.name}, ${secondTable.name}")
        println("9. row count = ${rows0}, ${rows1}")
        println("10. cols count = ${cols0}")
        
        // 최종 테이블의 열 이름 (필터링 안됨)
        val finalTableColumnNamesNotFiltered = listOf(firstTable, secondTable).fold( listOf<String>() ) { acc, e ->
            acc + e.column_name.map {
                "${e.name}.${it}"
            }
        }
        
        // 최종 테이블의 행 이름 (필터링 안됨)
        val finalRows = (0 until rows0 * rows1).map {
            Row(firstTable.rows[it / rows1].row + secondTable.rows[it % rows1].row)
        }
        
        // 최종 테이블 (행, 열 필터링 안됨)
        wholeTable = Table (
            fromPart.replace(",", "_"),
            finalTableColumnNamesNotFiltered,
            finalRows
        )
        
        // 최종 테이블의 열 이름 (필터링됨)
        val finalTableColumnNames = finalTableColumnNamesNotFiltered.filter {
            val tableName = it.substringBefore(".")
            val colName = it.substringAfter(".")
            
            colName in outputTableColIndices.get(tableName)!!.keys
        }
        
        // 최종 테이블의 열 이름의 인덱스
        val finalTableColumnIndices = finalTableColumnNames.map {
            val tableNamePart = it.substringBefore(".")
            val colNamePart = it.substringAfter(".")
            
            // 0이면 첫 번째 테이블, 1이면 두 번째 테이블
            val tableNo = listOf(firstTable, secondTable).indexOfFirst {
                it.name == tableNamePart
            }
            
            if (tableNo == 0) {
                outputTableColIndices.get(tableNamePart)!!.get(colNamePart)!!
            } else {
                outputTableColIndices.get(tableNamePart)!!.get(colNamePart)!! + cols0
            }
        }
        
        // 최종 테이블의 행 이름 (필터링됨)
        val finalTableRows = finalRows.map { rs ->
            Row (
                rs.row.filterIndexed { idx, _ ->
                    idx in finalTableColumnIndices
                }
            )
        }
        
        // 최종 테이블
        Table (
            fromPart.replace(",", "_"),
            finalTableColumnNames,
            finalTableRows
        )
    }
    println("11. final table name : ${finalTable.name}")
    println("12. final table columns : ${finalTable.column_name}")
    println("13. final table row count : ${finalTable.rows.size}\n")
    
    println("14. final table first 10 row values :")
    finalTable.rows.take(10).forEachIndexed { idx, it ->
        println("    at index ${idx} : ${it.row}")
    }
    println("\n15. whole index : ${wholeIndex}")
    println("\n16. whole table column names : ${wholeTable.column_name}")
    println("17. whole table first 5 row values :")
    wholeTable.rows.take(5).forEachIndexed { idx, it ->
        println("    at index ${idx} : ${it.row}")
    }
    print("\n")
    
    // WHERE을 조건 부분과 정렬 부분으로 나누기
    var conditionPart = ""
    
    // WHERE 파트가 없으면 모든 행 출력
    val finalRowsTemp = if (wherePart.isEmpty()) {
        finalTable.rows
    } else {
        
        // 조건 부분과 정렬 부분 나누기
        conditionPart = wherePart.substringBefore(" ORDER BY ")
        println("18. condition part : ${conditionPart}")

        // 조건 부분에 따라 필터링하기
        val finalRows = finalTable.rows.filterIndexed { idx, r ->
            var atLeast1Or = false
            
            conditionPart.split(" OR ").forEach { orCond ->
                var allAnd = true
                
                orCond.split(" AND ").forEach { andCond ->
                    val left = andCond.substringBefore(" = ")
                    val right = andCond.substringAfter(" = ")
                    
                    val leftColIdx = wholeTable.column_name.indexOfFirst {
                        it == left
                    }
                    val leftValue = wholeTable.rows[idx].row[leftColIdx]
                    
                    val rightValue = if (right.contains("'")) {
                        right.substring(1, right.length - 1)
                    } else {
                        val rightColIdx = wholeTable.column_name.indexOfFirst {
                            it == right
                        }
                        wholeTable.rows[idx].row[rightColIdx]
                    }
                    
                    if (leftValue != rightValue) {
                        allAnd = false
                    }
                }
                
                if (allAnd) {
                    atLeast1Or = true
                }
            }
            
            atLeast1Or
        }
        
        finalRows
    }
    
    // 정렬 부분에 따라 정렬하기
    println("19. order by part : ${orderByPart}")
    
    val finalRows = if (orderByPart.isEmpty()) {
        finalRowsTemp
    } else {
        val orderBySplit = orderByPart.split(" ")
        val orderByCnt = orderBySplit.size / 2
    
        val finalRs = (orderByCnt - 1 downTo 0).fold (finalRowsTemp) { acc, e ->
            val colName = orderBySplit[2 * e]
            val option = orderBySplit[2 * e + 1]
            val colIndex = wholeTable.column_name.indexOfFirst {
                it == colName
            }
            
            if (option == "ASC") {
                acc.sortedBy { it.row[colIndex] }
            } else {
                acc.sortedByDescending { it.row[colIndex] }
            }
        }
        finalRs
    }
    
    println("20. final table filtered row count : ${finalRows.size}\n")
    
    // 전체 테이블
    val realFinalTable = Table(
        finalTable.name,
        finalTable.column_name,
        finalRows // 필터링된 행들
    )
    
    // 각 열의 출력 길이는 (열 이름의 길이의 최댓값 + 1)
    val maxColNameLength = realFinalTable.column_name.maxOf {
        it.length
    }
    val colPrintLength = Math.max(12, maxColNameLength + 1)
        
    // 전체 테이블 출력
    println("\n <<< FINAL RESULT of \"${query}\" >>>\n")
    println(getSpacedOutput(realFinalTable.column_name, colPrintLength))
    println("-".repeat(colPrintLength * realFinalTable.column_name.size))
        
    realFinalTable.rows.forEachIndexed { idx, it ->
        println(getSpacedOutput(it.row, colPrintLength))
    }
    println("\n")
}

fun main() {

    // 임시 데이터베이스
    val testDB = DataBase (
        listOf<Table> (
            Table (
                "DEVELOPER",
                listOf("dev_id", "name", "months", "language", "dept_id"),
                listOf<Row> (
                    Row(listOf("200", "hong", "12", "Kotlin", "00")),
                    Row(listOf("201", "seok", "9", "Java", "00")),
                    Row(listOf("202", "sin", "12", "Python3", "02")),
                    Row(listOf("203", "yeon", "10", "Kotlin", "00")),
                    Row(listOf("204", "yun", "18", "C++", "03")),
                    Row(listOf("205", "kang", "18", "Javascript", "02")),
                    Row(listOf("206", "min", "24", "C", "03")),
                    Row(listOf("207", "soon", "12", "Objective-C", "01")),
                    Row(listOf("208", "young", "24", "Python3", "02")),
                    Row(listOf("209", "hye", "30", "C++", "00")),
                    Row(listOf("210", "jin", "36", "Python3", "03")),
                    Row(listOf("211", "suk", "24", "C", "02")),
                    Row(listOf("212", "yoo", "28", "Javascript", "03")),
                    Row(listOf("213", "jeong", "16", "Swift", "01")),
                    Row(listOf("214", "soo", "14", "C", "02"))
                )
            ),
            Table (
                "DEPT",
                listOf("dept_id", "name", "description"),
                listOf<Row> (
                    Row(listOf("00", "Android", "Android_App_Developers")),
                    Row(listOf("01", "iOS", "iOS_App_Developers")),
                    Row(listOf("02", "Server_I", "Server_Development_team_1")),
                    Row(listOf("03", "Server_II", "Server_Development_team_2"))
                )
            )
        ),
        listOf (
            "SELECT * FROM DEVELOPER",
            "SELECT * FROM DEPT",
            "SELECT dev_id,name,months,language FROM DEVELOPER",
            "SELECT dev_id,name,months,language FROM DEVELOPER ORDER BY name ASC",
            "SELECT dev_id,name,months,language FROM DEVELOPER ORDER BY name DESC",
            "SELECT name,description FROM DEPT",
            "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT",
            "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEPT,DEVELOPER",
            "SELECT dev_id,name,months,language FROM DEVELOPER WHERE dev_id = '202'",
            "SELECT dev_id,name,months,language FROM DEVELOPER WHERE name = 'kang'",
            "SELECT dev_id,name,months,language FROM DEVELOPER WHERE months = '24'",
            "SELECT name,language FROM DEVELOPER WHERE months = '24' ORDER BY name ASC",
            "SELECT name,language FROM DEVELOPER WHERE months = '24' ORDER BY name DESC",
            "SELECT name,language FROM DEVELOPER WHERE dev_id = '202' OR months = '24'",
            "SELECT name,language FROM DEVELOPER WHERE name = 'hong' AND months = '12'",
            "SELECT name,language FROM DEVELOPER WHERE months = '24' OR name = 'hong' AND months = '12'",
            "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.dev_id = '202'",
            "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.months = '18'",
            "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.language = 'Kotlin'",
            "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEPT.name = 'Android'"
        )
    )

    // SQL-LIKE 문 실행
    testDB.queries.forEach {
        executeQuery(testDB.tables, it)
    }
}
```

### 실행 결과
```kotlin
1. select part = *, from part = DEVELOPER, where part = 
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

6. output : column filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [dev_id, name, months, language, dept_id]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]
    at index 5 : [205, kang, 18, Javascript, 02]
    at index 6 : [206, min, 24, C, 03]
    at index 7 : [207, soon, 12, Objective-C, 01]
    at index 8 : [208, young, 24, Python3, 02]
    at index 9 : [209, hye, 30, C++, 00]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

19. order by part : 
20. final table filtered row count : 15


 <<< FINAL RESULT of "SELECT * FROM DEVELOPER" >>>

dev_id      name        months      language    dept_id     
------------------------------------------------------------
200         hong        12          Kotlin      00          
201         seok        9           Java        00          
202         sin         12          Python3     02          
203         yeon        10          Kotlin      00          
204         yun         18          C++         03          
205         kang        18          Javascript  02          
206         min         24          C           03          
207         soon        12          Objective-C 01          
208         young       24          Python3     02          
209         hye         30          C++         00          
210         jin         36          Python3     03          
211         suk         24          C           02          
212         yoo         28          Javascript  03          
213         jeong       16          Swift       01          
214         soo         14          C           02          


1. select part = *, from part = DEPT, where part = 
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEPT={dept_id=0, name=1, description=2}}

6. output : column filtered = {DEPT={dept_id=0, name=1, description=2}}

7. row count : 4
11. final table name : DEPT
12. final table columns : [dept_id, name, description]
13. final table row count : 4

14. final table first 10 row values :
    at index 0 : [00, Android, Android_App_Developers]
    at index 1 : [01, iOS, iOS_App_Developers]
    at index 2 : [02, Server_I, Server_Development_team_1]
    at index 3 : [03, Server_II, Server_Development_team_2]

15. whole index : {dept_id=0, name=1, description=2}

16. whole table column names : [dept_id, name, description]
17. whole table first 5 row values :
    at index 0 : [00, Android, Android_App_Developers]
    at index 1 : [01, iOS, iOS_App_Developers]
    at index 2 : [02, Server_I, Server_Development_team_1]
    at index 3 : [03, Server_II, Server_Development_team_2]

19. order by part : 
20. final table filtered row count : 4


 <<< FINAL RESULT of "SELECT * FROM DEPT" >>>

dept_id     name        description 
------------------------------------
00          Android     Android_App_Developers
01          iOS         iOS_App_Developers
02          Server_I    Server_Development_team_1
03          Server_II   Server_Development_team_2


1. select part = dev_id,name,months,language, from part = DEVELOPER, where part = 
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [dev_id, name, months, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [dev_id, name, months, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [200, hong, 12, Kotlin]
    at index 1 : [201, seok, 9, Java]
    at index 2 : [202, sin, 12, Python3]
    at index 3 : [203, yeon, 10, Kotlin]
    at index 4 : [204, yun, 18, C++]
    at index 5 : [205, kang, 18, Javascript]
    at index 6 : [206, min, 24, C]
    at index 7 : [207, soon, 12, Objective-C]
    at index 8 : [208, young, 24, Python3]
    at index 9 : [209, hye, 30, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

19. order by part : 
20. final table filtered row count : 15


 <<< FINAL RESULT of "SELECT dev_id,name,months,language FROM DEVELOPER" >>>

dev_id      name        months      language    
------------------------------------------------
200         hong        12          Kotlin      
201         seok        9           Java        
202         sin         12          Python3     
203         yeon        10          Kotlin      
204         yun         18          C++         
205         kang        18          Javascript  
206         min         24          C           
207         soon        12          Objective-C 
208         young       24          Python3     
209         hye         30          C++         
210         jin         36          Python3     
211         suk         24          C           
212         yoo         28          Javascript  
213         jeong       16          Swift       
214         soo         14          C           


1. select part = dev_id,name,months,language, from part = DEVELOPER, where part = 
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [dev_id, name, months, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [dev_id, name, months, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [200, hong, 12, Kotlin]
    at index 1 : [201, seok, 9, Java]
    at index 2 : [202, sin, 12, Python3]
    at index 3 : [203, yeon, 10, Kotlin]
    at index 4 : [204, yun, 18, C++]
    at index 5 : [205, kang, 18, Javascript]
    at index 6 : [206, min, 24, C]
    at index 7 : [207, soon, 12, Objective-C]
    at index 8 : [208, young, 24, Python3]
    at index 9 : [209, hye, 30, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

19. order by part : name ASC
20. final table filtered row count : 15


 <<< FINAL RESULT of "SELECT dev_id,name,months,language FROM DEVELOPER ORDER BY name ASC" >>>

dev_id      name        months      language    
------------------------------------------------
200         hong        12          Kotlin      
209         hye         30          C++         
213         jeong       16          Swift       
210         jin         36          Python3     
205         kang        18          Javascript  
206         min         24          C           
201         seok        9           Java        
202         sin         12          Python3     
214         soo         14          C           
207         soon        12          Objective-C 
211         suk         24          C           
203         yeon        10          Kotlin      
212         yoo         28          Javascript  
208         young       24          Python3     
204         yun         18          C++         


1. select part = dev_id,name,months,language, from part = DEVELOPER, where part = 
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [dev_id, name, months, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [dev_id, name, months, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [200, hong, 12, Kotlin]
    at index 1 : [201, seok, 9, Java]
    at index 2 : [202, sin, 12, Python3]
    at index 3 : [203, yeon, 10, Kotlin]
    at index 4 : [204, yun, 18, C++]
    at index 5 : [205, kang, 18, Javascript]
    at index 6 : [206, min, 24, C]
    at index 7 : [207, soon, 12, Objective-C]
    at index 8 : [208, young, 24, Python3]
    at index 9 : [209, hye, 30, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

19. order by part : name DESC
20. final table filtered row count : 15


 <<< FINAL RESULT of "SELECT dev_id,name,months,language FROM DEVELOPER ORDER BY name DESC" >>>

dev_id      name        months      language    
------------------------------------------------
204         yun         18          C++         
208         young       24          Python3     
212         yoo         28          Javascript  
203         yeon        10          Kotlin      
211         suk         24          C           
207         soon        12          Objective-C 
214         soo         14          C           
202         sin         12          Python3     
201         seok        9           Java        
206         min         24          C           
205         kang        18          Javascript  
210         jin         36          Python3     
213         jeong       16          Swift       
209         hye         30          C++         
200         hong        12          Kotlin      


1. select part = name,description, from part = DEPT, where part = 
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEPT={dept_id=0, name=1, description=2}}

4. select column names = [name, description]
5. output : table filtered = {DEPT={dept_id=0, name=1, description=2}}
6. output : column filtered = {DEPT={name=1, description=2}}

7. row count : 4
11. final table name : DEPT
12. final table columns : [name, description]
13. final table row count : 4

14. final table first 10 row values :
    at index 0 : [Android, Android_App_Developers]
    at index 1 : [iOS, iOS_App_Developers]
    at index 2 : [Server_I, Server_Development_team_1]
    at index 3 : [Server_II, Server_Development_team_2]

15. whole index : {dept_id=0, name=1, description=2}

16. whole table column names : [dept_id, name, description]
17. whole table first 5 row values :
    at index 0 : [00, Android, Android_App_Developers]
    at index 1 : [01, iOS, iOS_App_Developers]
    at index 2 : [02, Server_I, Server_Development_team_1]
    at index 3 : [03, Server_II, Server_Development_team_2]

19. order by part : 
20. final table filtered row count : 4


 <<< FINAL RESULT of "SELECT name,description FROM DEPT" >>>

name        description 
------------------------
Android     Android_App_Developers
iOS         iOS_App_Developers
Server_I    Server_Development_team_1
Server_II   Server_Development_team_2


1. select part = DEVELOPER.name,DEVELOPER.months,DEPT.name, from part = DEVELOPER,DEPT, where part = 
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}

4. select column names = [DEVELOPER.name, DEVELOPER.months, DEPT.name]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}
6. output : column filtered = {DEVELOPER={name=1, months=2}, DEPT={name=1}}

8. table name = DEVELOPER, DEPT
9. row count = 15, 4
10. cols count = 5
11. final table name : DEVELOPER_DEPT
12. final table columns : [DEVELOPER.name, DEVELOPER.months, DEPT.name]
13. final table row count : 60

14. final table first 10 row values :
    at index 0 : [hong, 12, Android]
    at index 1 : [hong, 12, iOS]
    at index 2 : [hong, 12, Server_I]
    at index 3 : [hong, 12, Server_II]
    at index 4 : [seok, 9, Android]
    at index 5 : [seok, 9, iOS]
    at index 6 : [seok, 9, Server_I]
    at index 7 : [seok, 9, Server_II]
    at index 8 : [sin, 12, Android]
    at index 9 : [sin, 12, iOS]

15. whole index : {DEVELOPER.dev_id=0, DEVELOPER.name=1, DEVELOPER.months=2, DEVELOPER.language=3, DEVELOPER.dept_id=4, DEPT.dept_id=5, DEPT.name=6, DEPT.description=7}

16. whole table column names : [DEVELOPER.dev_id, DEVELOPER.name, DEVELOPER.months, DEVELOPER.language, DEVELOPER.dept_id, DEPT.dept_id, DEPT.name, DEPT.description]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00, 00, Android, Android_App_Developers]
    at index 1 : [200, hong, 12, Kotlin, 00, 01, iOS, iOS_App_Developers]
    at index 2 : [200, hong, 12, Kotlin, 00, 02, Server_I, Server_Development_team_1]
    at index 3 : [200, hong, 12, Kotlin, 00, 03, Server_II, Server_Development_team_2]
    at index 4 : [201, seok, 9, Java, 00, 00, Android, Android_App_Developers]

19. order by part : 
20. final table filtered row count : 60


 <<< FINAL RESULT of "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT" >>>

DEVELOPER.name   DEVELOPER.months DEPT.name        
---------------------------------------------------
hong             12               Android          
hong             12               iOS              
hong             12               Server_I         
hong             12               Server_II        
seok             9                Android          
seok             9                iOS              
seok             9                Server_I         
seok             9                Server_II        
sin              12               Android          
sin              12               iOS              
sin              12               Server_I         
sin              12               Server_II        
yeon             10               Android          
yeon             10               iOS              
yeon             10               Server_I         
yeon             10               Server_II        
yun              18               Android          
yun              18               iOS              
yun              18               Server_I         
yun              18               Server_II        
kang             18               Android          
kang             18               iOS              
kang             18               Server_I         
kang             18               Server_II        
min              24               Android          
min              24               iOS              
min              24               Server_I         
min              24               Server_II        
soon             12               Android          
soon             12               iOS              
soon             12               Server_I         
soon             12               Server_II        
young            24               Android          
young            24               iOS              
young            24               Server_I         
young            24               Server_II        
hye              30               Android          
hye              30               iOS              
hye              30               Server_I         
hye              30               Server_II        
jin              36               Android          
jin              36               iOS              
jin              36               Server_I         
jin              36               Server_II        
suk              24               Android          
suk              24               iOS              
suk              24               Server_I         
suk              24               Server_II        
yoo              28               Android          
yoo              28               iOS              
yoo              28               Server_I         
yoo              28               Server_II        
jeong            16               Android          
jeong            16               iOS              
jeong            16               Server_I         
jeong            16               Server_II        
soo              14               Android          
soo              14               iOS              
soo              14               Server_I         
soo              14               Server_II        


1. select part = DEVELOPER.name,DEVELOPER.months,DEPT.name, from part = DEPT,DEVELOPER, where part = 
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}

4. select column names = [DEVELOPER.name, DEVELOPER.months, DEPT.name]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}
6. output : column filtered = {DEVELOPER={name=1, months=2}, DEPT={name=1}}

8. table name = DEPT, DEVELOPER
9. row count = 4, 15
10. cols count = 3
11. final table name : DEPT_DEVELOPER
12. final table columns : [DEPT.name, DEVELOPER.name, DEVELOPER.months]
13. final table row count : 60

14. final table first 10 row values :
    at index 0 : [Android, hong, 12]
    at index 1 : [Android, seok, 9]
    at index 2 : [Android, sin, 12]
    at index 3 : [Android, yeon, 10]
    at index 4 : [Android, yun, 18]
    at index 5 : [Android, kang, 18]
    at index 6 : [Android, min, 24]
    at index 7 : [Android, soon, 12]
    at index 8 : [Android, young, 24]
    at index 9 : [Android, hye, 30]

15. whole index : {DEPT.dept_id=0, DEPT.name=1, DEPT.description=2, DEVELOPER.dev_id=3, DEVELOPER.name=4, DEVELOPER.months=5, DEVELOPER.language=6, DEVELOPER.dept_id=7}

16. whole table column names : [DEPT.dept_id, DEPT.name, DEPT.description, DEVELOPER.dev_id, DEVELOPER.name, DEVELOPER.months, DEVELOPER.language, DEVELOPER.dept_id]
17. whole table first 5 row values :
    at index 0 : [00, Android, Android_App_Developers, 200, hong, 12, Kotlin, 00]
    at index 1 : [00, Android, Android_App_Developers, 201, seok, 9, Java, 00]
    at index 2 : [00, Android, Android_App_Developers, 202, sin, 12, Python3, 02]
    at index 3 : [00, Android, Android_App_Developers, 203, yeon, 10, Kotlin, 00]
    at index 4 : [00, Android, Android_App_Developers, 204, yun, 18, C++, 03]

19. order by part : 
20. final table filtered row count : 60


 <<< FINAL RESULT of "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEPT,DEVELOPER" >>>

DEPT.name        DEVELOPER.name   DEVELOPER.months 
---------------------------------------------------
Android          hong             12               
Android          seok             9                
Android          sin              12               
Android          yeon             10               
Android          yun              18               
Android          kang             18               
Android          min              24               
Android          soon             12               
Android          young            24               
Android          hye              30               
Android          jin              36               
Android          suk              24               
Android          yoo              28               
Android          jeong            16               
Android          soo              14               
iOS              hong             12               
iOS              seok             9                
iOS              sin              12               
iOS              yeon             10               
iOS              yun              18               
iOS              kang             18               
iOS              min              24               
iOS              soon             12               
iOS              young            24               
iOS              hye              30               
iOS              jin              36               
iOS              suk              24               
iOS              yoo              28               
iOS              jeong            16               
iOS              soo              14               
Server_I         hong             12               
Server_I         seok             9                
Server_I         sin              12               
Server_I         yeon             10               
Server_I         yun              18               
Server_I         kang             18               
Server_I         min              24               
Server_I         soon             12               
Server_I         young            24               
Server_I         hye              30               
Server_I         jin              36               
Server_I         suk              24               
Server_I         yoo              28               
Server_I         jeong            16               
Server_I         soo              14               
Server_II        hong             12               
Server_II        seok             9                
Server_II        sin              12               
Server_II        yeon             10               
Server_II        yun              18               
Server_II        kang             18               
Server_II        min              24               
Server_II        soon             12               
Server_II        young            24               
Server_II        hye              30               
Server_II        jin              36               
Server_II        suk              24               
Server_II        yoo              28               
Server_II        jeong            16               
Server_II        soo              14               


1. select part = dev_id,name,months,language, from part = DEVELOPER, where part = dev_id = '202'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [dev_id, name, months, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [dev_id, name, months, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [200, hong, 12, Kotlin]
    at index 1 : [201, seok, 9, Java]
    at index 2 : [202, sin, 12, Python3]
    at index 3 : [203, yeon, 10, Kotlin]
    at index 4 : [204, yun, 18, C++]
    at index 5 : [205, kang, 18, Javascript]
    at index 6 : [206, min, 24, C]
    at index 7 : [207, soon, 12, Objective-C]
    at index 8 : [208, young, 24, Python3]
    at index 9 : [209, hye, 30, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : dev_id = '202'
19. order by part : 
20. final table filtered row count : 1


 <<< FINAL RESULT of "SELECT dev_id,name,months,language FROM DEVELOPER WHERE dev_id = '202'" >>>

dev_id      name        months      language    
------------------------------------------------
202         sin         12          Python3     


1. select part = dev_id,name,months,language, from part = DEVELOPER, where part = name = 'kang'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [dev_id, name, months, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [dev_id, name, months, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [200, hong, 12, Kotlin]
    at index 1 : [201, seok, 9, Java]
    at index 2 : [202, sin, 12, Python3]
    at index 3 : [203, yeon, 10, Kotlin]
    at index 4 : [204, yun, 18, C++]
    at index 5 : [205, kang, 18, Javascript]
    at index 6 : [206, min, 24, C]
    at index 7 : [207, soon, 12, Objective-C]
    at index 8 : [208, young, 24, Python3]
    at index 9 : [209, hye, 30, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : name = 'kang'
19. order by part : 
20. final table filtered row count : 1


 <<< FINAL RESULT of "SELECT dev_id,name,months,language FROM DEVELOPER WHERE name = 'kang'" >>>

dev_id      name        months      language    
------------------------------------------------
205         kang        18          Javascript  


1. select part = dev_id,name,months,language, from part = DEVELOPER, where part = months = '24'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [dev_id, name, months, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [dev_id, name, months, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [200, hong, 12, Kotlin]
    at index 1 : [201, seok, 9, Java]
    at index 2 : [202, sin, 12, Python3]
    at index 3 : [203, yeon, 10, Kotlin]
    at index 4 : [204, yun, 18, C++]
    at index 5 : [205, kang, 18, Javascript]
    at index 6 : [206, min, 24, C]
    at index 7 : [207, soon, 12, Objective-C]
    at index 8 : [208, young, 24, Python3]
    at index 9 : [209, hye, 30, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : months = '24'
19. order by part : 
20. final table filtered row count : 3


 <<< FINAL RESULT of "SELECT dev_id,name,months,language FROM DEVELOPER WHERE months = '24'" >>>

dev_id      name        months      language    
------------------------------------------------
206         min         24          C           
208         young       24          Python3     
211         suk         24          C           


1. select part = name,language, from part = DEVELOPER, where part = months = '24'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [name, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={name=1, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [name, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [hong, Kotlin]
    at index 1 : [seok, Java]
    at index 2 : [sin, Python3]
    at index 3 : [yeon, Kotlin]
    at index 4 : [yun, C++]
    at index 5 : [kang, Javascript]
    at index 6 : [min, C]
    at index 7 : [soon, Objective-C]
    at index 8 : [young, Python3]
    at index 9 : [hye, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : months = '24'
19. order by part : name ASC
20. final table filtered row count : 3


 <<< FINAL RESULT of "SELECT name,language FROM DEVELOPER WHERE months = '24' ORDER BY name ASC" >>>

name        language    
------------------------
min         C           
suk         C           
young       Python3     


1. select part = name,language, from part = DEVELOPER, where part = months = '24'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [name, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={name=1, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [name, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [hong, Kotlin]
    at index 1 : [seok, Java]
    at index 2 : [sin, Python3]
    at index 3 : [yeon, Kotlin]
    at index 4 : [yun, C++]
    at index 5 : [kang, Javascript]
    at index 6 : [min, C]
    at index 7 : [soon, Objective-C]
    at index 8 : [young, Python3]
    at index 9 : [hye, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : months = '24'
19. order by part : name DESC
20. final table filtered row count : 3


 <<< FINAL RESULT of "SELECT name,language FROM DEVELOPER WHERE months = '24' ORDER BY name DESC" >>>

name        language    
------------------------
young       Python3     
min         C           
suk         C           


1. select part = name,language, from part = DEVELOPER, where part = dev_id = '202' OR months = '24'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [name, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={name=1, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [name, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [hong, Kotlin]
    at index 1 : [seok, Java]
    at index 2 : [sin, Python3]
    at index 3 : [yeon, Kotlin]
    at index 4 : [yun, C++]
    at index 5 : [kang, Javascript]
    at index 6 : [min, C]
    at index 7 : [soon, Objective-C]
    at index 8 : [young, Python3]
    at index 9 : [hye, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : dev_id = '202' OR months = '24'
19. order by part : 
20. final table filtered row count : 4


 <<< FINAL RESULT of "SELECT name,language FROM DEVELOPER WHERE dev_id = '202' OR months = '24'" >>>

name        language    
------------------------
sin         Python3     
min         C           
young       Python3     
suk         C           


1. select part = name,language, from part = DEVELOPER, where part = name = 'hong' AND months = '12'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [name, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={name=1, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [name, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [hong, Kotlin]
    at index 1 : [seok, Java]
    at index 2 : [sin, Python3]
    at index 3 : [yeon, Kotlin]
    at index 4 : [yun, C++]
    at index 5 : [kang, Javascript]
    at index 6 : [min, C]
    at index 7 : [soon, Objective-C]
    at index 8 : [young, Python3]
    at index 9 : [hye, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : name = 'hong' AND months = '12'
19. order by part : 
20. final table filtered row count : 1


 <<< FINAL RESULT of "SELECT name,language FROM DEVELOPER WHERE name = 'hong' AND months = '12'" >>>

name        language    
------------------------
hong        Kotlin      


1. select part = name,language, from part = DEVELOPER, where part = months = '24' OR name = 'hong' AND months = '12'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}

4. select column names = [name, language]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}}
6. output : column filtered = {DEVELOPER={name=1, language=3}}

7. row count : 15
11. final table name : DEVELOPER
12. final table columns : [name, language]
13. final table row count : 15

14. final table first 10 row values :
    at index 0 : [hong, Kotlin]
    at index 1 : [seok, Java]
    at index 2 : [sin, Python3]
    at index 3 : [yeon, Kotlin]
    at index 4 : [yun, C++]
    at index 5 : [kang, Javascript]
    at index 6 : [min, C]
    at index 7 : [soon, Objective-C]
    at index 8 : [young, Python3]
    at index 9 : [hye, C++]

15. whole index : {dev_id=0, name=1, months=2, language=3, dept_id=4}

16. whole table column names : [dev_id, name, months, language, dept_id]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00]
    at index 1 : [201, seok, 9, Java, 00]
    at index 2 : [202, sin, 12, Python3, 02]
    at index 3 : [203, yeon, 10, Kotlin, 00]
    at index 4 : [204, yun, 18, C++, 03]

18. condition part : months = '24' OR name = 'hong' AND months = '12'
19. order by part : 
20. final table filtered row count : 4


 <<< FINAL RESULT of "SELECT name,language FROM DEVELOPER WHERE months = '24' OR name = 'hong' AND months = '12'" >>>

name        language    
------------------------
hong        Kotlin      
min         C           
young       Python3     
suk         C           


1. select part = DEVELOPER.name,DEVELOPER.months,DEPT.name, from part = DEVELOPER,DEPT, where part = DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.dev_id = '202'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}

4. select column names = [DEVELOPER.name, DEVELOPER.months, DEPT.name]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}
6. output : column filtered = {DEVELOPER={name=1, months=2}, DEPT={name=1}}

8. table name = DEVELOPER, DEPT
9. row count = 15, 4
10. cols count = 5
11. final table name : DEVELOPER_DEPT
12. final table columns : [DEVELOPER.name, DEVELOPER.months, DEPT.name]
13. final table row count : 60

14. final table first 10 row values :
    at index 0 : [hong, 12, Android]
    at index 1 : [hong, 12, iOS]
    at index 2 : [hong, 12, Server_I]
    at index 3 : [hong, 12, Server_II]
    at index 4 : [seok, 9, Android]
    at index 5 : [seok, 9, iOS]
    at index 6 : [seok, 9, Server_I]
    at index 7 : [seok, 9, Server_II]
    at index 8 : [sin, 12, Android]
    at index 9 : [sin, 12, iOS]

15. whole index : {DEVELOPER.dev_id=0, DEVELOPER.name=1, DEVELOPER.months=2, DEVELOPER.language=3, DEVELOPER.dept_id=4, DEPT.dept_id=5, DEPT.name=6, DEPT.description=7}

16. whole table column names : [DEVELOPER.dev_id, DEVELOPER.name, DEVELOPER.months, DEVELOPER.language, DEVELOPER.dept_id, DEPT.dept_id, DEPT.name, DEPT.description]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00, 00, Android, Android_App_Developers]
    at index 1 : [200, hong, 12, Kotlin, 00, 01, iOS, iOS_App_Developers]
    at index 2 : [200, hong, 12, Kotlin, 00, 02, Server_I, Server_Development_team_1]
    at index 3 : [200, hong, 12, Kotlin, 00, 03, Server_II, Server_Development_team_2]
    at index 4 : [201, seok, 9, Java, 00, 00, Android, Android_App_Developers]

18. condition part : DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.dev_id = '202'
19. order by part : 
20. final table filtered row count : 1


 <<< FINAL RESULT of "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.dev_id = '202'" >>>

DEVELOPER.name   DEVELOPER.months DEPT.name        
---------------------------------------------------
sin              12               Server_I         


1. select part = DEVELOPER.name,DEVELOPER.months,DEPT.name, from part = DEVELOPER,DEPT, where part = DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.months = '18'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}

4. select column names = [DEVELOPER.name, DEVELOPER.months, DEPT.name]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}
6. output : column filtered = {DEVELOPER={name=1, months=2}, DEPT={name=1}}

8. table name = DEVELOPER, DEPT
9. row count = 15, 4
10. cols count = 5
11. final table name : DEVELOPER_DEPT
12. final table columns : [DEVELOPER.name, DEVELOPER.months, DEPT.name]
13. final table row count : 60

14. final table first 10 row values :
    at index 0 : [hong, 12, Android]
    at index 1 : [hong, 12, iOS]
    at index 2 : [hong, 12, Server_I]
    at index 3 : [hong, 12, Server_II]
    at index 4 : [seok, 9, Android]
    at index 5 : [seok, 9, iOS]
    at index 6 : [seok, 9, Server_I]
    at index 7 : [seok, 9, Server_II]
    at index 8 : [sin, 12, Android]
    at index 9 : [sin, 12, iOS]

15. whole index : {DEVELOPER.dev_id=0, DEVELOPER.name=1, DEVELOPER.months=2, DEVELOPER.language=3, DEVELOPER.dept_id=4, DEPT.dept_id=5, DEPT.name=6, DEPT.description=7}

16. whole table column names : [DEVELOPER.dev_id, DEVELOPER.name, DEVELOPER.months, DEVELOPER.language, DEVELOPER.dept_id, DEPT.dept_id, DEPT.name, DEPT.description]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00, 00, Android, Android_App_Developers]
    at index 1 : [200, hong, 12, Kotlin, 00, 01, iOS, iOS_App_Developers]
    at index 2 : [200, hong, 12, Kotlin, 00, 02, Server_I, Server_Development_team_1]
    at index 3 : [200, hong, 12, Kotlin, 00, 03, Server_II, Server_Development_team_2]
    at index 4 : [201, seok, 9, Java, 00, 00, Android, Android_App_Developers]

18. condition part : DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.months = '18'
19. order by part : 
20. final table filtered row count : 2


 <<< FINAL RESULT of "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.months = '18'" >>>

DEVELOPER.name   DEVELOPER.months DEPT.name        
---------------------------------------------------
yun              18               Server_II        
kang             18               Server_I         


1. select part = DEVELOPER.name,DEVELOPER.months,DEPT.name, from part = DEVELOPER,DEPT, where part = DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.language = 'Kotlin'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}

4. select column names = [DEVELOPER.name, DEVELOPER.months, DEPT.name]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}
6. output : column filtered = {DEVELOPER={name=1, months=2}, DEPT={name=1}}

8. table name = DEVELOPER, DEPT
9. row count = 15, 4
10. cols count = 5
11. final table name : DEVELOPER_DEPT
12. final table columns : [DEVELOPER.name, DEVELOPER.months, DEPT.name]
13. final table row count : 60

14. final table first 10 row values :
    at index 0 : [hong, 12, Android]
    at index 1 : [hong, 12, iOS]
    at index 2 : [hong, 12, Server_I]
    at index 3 : [hong, 12, Server_II]
    at index 4 : [seok, 9, Android]
    at index 5 : [seok, 9, iOS]
    at index 6 : [seok, 9, Server_I]
    at index 7 : [seok, 9, Server_II]
    at index 8 : [sin, 12, Android]
    at index 9 : [sin, 12, iOS]

15. whole index : {DEVELOPER.dev_id=0, DEVELOPER.name=1, DEVELOPER.months=2, DEVELOPER.language=3, DEVELOPER.dept_id=4, DEPT.dept_id=5, DEPT.name=6, DEPT.description=7}

16. whole table column names : [DEVELOPER.dev_id, DEVELOPER.name, DEVELOPER.months, DEVELOPER.language, DEVELOPER.dept_id, DEPT.dept_id, DEPT.name, DEPT.description]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00, 00, Android, Android_App_Developers]
    at index 1 : [200, hong, 12, Kotlin, 00, 01, iOS, iOS_App_Developers]
    at index 2 : [200, hong, 12, Kotlin, 00, 02, Server_I, Server_Development_team_1]
    at index 3 : [200, hong, 12, Kotlin, 00, 03, Server_II, Server_Development_team_2]
    at index 4 : [201, seok, 9, Java, 00, 00, Android, Android_App_Developers]

18. condition part : DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.language = 'Kotlin'
19. order by part : 
20. final table filtered row count : 2


 <<< FINAL RESULT of "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEVELOPER.language = 'Kotlin'" >>>

DEVELOPER.name   DEVELOPER.months DEPT.name        
---------------------------------------------------
hong             12               Android          
yeon             10               Android          


1. select part = DEVELOPER.name,DEVELOPER.months,DEPT.name, from part = DEVELOPER,DEPT, where part = DEVELOPER.dept_id = DEPT.dept_id AND DEPT.name = 'Android'
2. table name = DEVELOPER, columns = [dev_id, name, months, language, dept_id]
2. table name = DEPT, columns = [dept_id, name, description]
3. table column indices = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}

4. select column names = [DEVELOPER.name, DEVELOPER.months, DEPT.name]
5. output : table filtered = {DEVELOPER={dev_id=0, name=1, months=2, language=3, dept_id=4}, DEPT={dept_id=0, name=1, description=2}}
6. output : column filtered = {DEVELOPER={name=1, months=2}, DEPT={name=1}}

8. table name = DEVELOPER, DEPT
9. row count = 15, 4
10. cols count = 5
11. final table name : DEVELOPER_DEPT
12. final table columns : [DEVELOPER.name, DEVELOPER.months, DEPT.name]
13. final table row count : 60

14. final table first 10 row values :
    at index 0 : [hong, 12, Android]
    at index 1 : [hong, 12, iOS]
    at index 2 : [hong, 12, Server_I]
    at index 3 : [hong, 12, Server_II]
    at index 4 : [seok, 9, Android]
    at index 5 : [seok, 9, iOS]
    at index 6 : [seok, 9, Server_I]
    at index 7 : [seok, 9, Server_II]
    at index 8 : [sin, 12, Android]
    at index 9 : [sin, 12, iOS]

15. whole index : {DEVELOPER.dev_id=0, DEVELOPER.name=1, DEVELOPER.months=2, DEVELOPER.language=3, DEVELOPER.dept_id=4, DEPT.dept_id=5, DEPT.name=6, DEPT.description=7}

16. whole table column names : [DEVELOPER.dev_id, DEVELOPER.name, DEVELOPER.months, DEVELOPER.language, DEVELOPER.dept_id, DEPT.dept_id, DEPT.name, DEPT.description]
17. whole table first 5 row values :
    at index 0 : [200, hong, 12, Kotlin, 00, 00, Android, Android_App_Developers]
    at index 1 : [200, hong, 12, Kotlin, 00, 01, iOS, iOS_App_Developers]
    at index 2 : [200, hong, 12, Kotlin, 00, 02, Server_I, Server_Development_team_1]
    at index 3 : [200, hong, 12, Kotlin, 00, 03, Server_II, Server_Development_team_2]
    at index 4 : [201, seok, 9, Java, 00, 00, Android, Android_App_Developers]

18. condition part : DEVELOPER.dept_id = DEPT.dept_id AND DEPT.name = 'Android'
19. order by part : 
20. final table filtered row count : 4


 <<< FINAL RESULT of "SELECT DEVELOPER.name,DEVELOPER.months,DEPT.name FROM DEVELOPER,DEPT WHERE DEVELOPER.dept_id = DEPT.dept_id AND DEPT.name = 'Android'" >>>

DEVELOPER.name   DEVELOPER.months DEPT.name        
---------------------------------------------------
hong             12               Android          
seok             9                Android          
yeon             10               Android          
hye              30               Android          
```
