## 요구사항 분석
행과 열, 그리고 데이터로 이루어진 테이블이 있다. 이 테이블에서 ```카비스```는 다음과 같은 정보를 찾아 준다. 이때 ```호칭``` 다음에 ```본문```에 해당하는 형식대로 말해 준다.
* 전체 행의 개수
  * 본문 : (```항목``` 또는 ```행```)과 (```개수``` 또는 ```몇 개```)를 포함해서 말한다. 단, ```열```은 말하지 않는다.
  * 답변 : ```전체 행의 개수는 N개입니다.```
* 전체 열의 개수
  * 본문 : ```열```과 (```개수``` 또는 ```몇 개```)를 포함해서 말한다. 단, ```항목```이나 ```행```, ```평균```은 말하지 않는다.
  * 답변 : ```전체 열의 개수는 N개입니다.```
* 특정 열의 평균
  * 본문 : ```(열 이름)```과 ```평균```을 포함해서 말한다. 단, ```개수```나 ```몇 개```, 다른 열 이름은 말하지 않는다.
  * 답변 : ```열 (열 이름)의 평균은 ~입니다.```
* 특정 열에서 N번째로 큰 값
  * 본문 : ```(열 이름)```과 ```(N)번째```와 ```큰```을 포함해서 말한다. 단, 다른 숫자 값 또는 다른 열 이름은 말하지 않는다.
  * 답변 : ```열 (열 이름)에서 (N)번째로 큰 값은 ~입니다.```
* 특정 열(A)의 값이 특정 값(x)인 항목의 다른 열(B)의 값
  * 본문 : ```(열 A의 이름)```과 ```(x의 값)```, ```(열 B의 이름)```을 포함시킨다. 이때 ```(열 A의 이름)```을 ```(열 B의 이름)```보다 먼저 말하며, 다른 열의 이름은 말하지 않는다.
  * 답변 :
    * 특정 열(A)의 값이 특정 값(x)인 항목이 1개인 경우 : ```(열 A의 이름)이 (x)인 항목의 (열 B의 이름)의 값은 ~입니다.```
    * 특정 열(A)의 값이 특정 값(x)인 항목이 여러 개인 경우 : ```(열 A의 이름)이 (x)인 항목의 (열 B의 이름)의 값은 위쪽부터 차례대로 ~, ~, ..., ~입니다.```
    * 특정 열(A)의 값이 특정 값(x)인 항목이 없는 경우 : ```앗! (열 A의 이름)이 (x)인 항목이 없네요. 다른 항목을 찾으시는 것은 아닌가요?```
* 행 추가하기
  * 본문 : ```(열 이름 1)```, ```(값 1)```, ..., ```(열 이름 N)```, ```(값 N)```을 그 순서대로 말한다.
  * 답변 : ```알려주신 대로 항목을 추가했습니다. 감사합니다.```
* 특정 열(A)의 값이 특정 값(x)인 행을 모두 삭제하기
  * 본문 : ```(열 A의 이름)```과 ```(x의 값)```, ```삭제```를 그 순서대로 말한다.
  * 답변 :
    * 특정 열(A)의 값이 특정 값(x)인 항목이 1개인 경우 : ```알려주신 대로 항목을 삭제했습니다. 감사합니다.```
    * 특정 열(A)의 값이 특정 값(x)인 항목이 여러 개인 경우 : ```알려주신 대로 항목을 삭제했습니다. 감사합니다. 많이 지우느라 힘들었어요.```
    * 특정 열(A)의 값이 특정 값(x)인 항목이 없는 경우 : ```앗! (열 A의 이름)이 (x)인 항목이 없네요. 다른 항목을 삭제하시려는 것은 아닌가요?```
* 본문이 위의 어느 것에도 일치하지 않는 경우
  * 답변 : ```제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?``` 

```호칭```은 ```카비스```라는 단어가 포함된 어절(공백으로 구분)까지이고, 그 다음부터 ```본문```이다.

단, ```카비스```라는 단어가 어디에도 없으면 다음과 같이 답변한다.
* ```앗! 혹시 저를 부르셨나요?```

또한, 호칭 부분이 ```알렉사```, ```시리```, ```자비스``` 또는 ```코타나```를 포함하면 다음과 같이 답변한다.
* ```저랑 다른 AI랑 제발 헷갈리지 말아 주세요.```

### 데이터 클래스
```kotlin
// 데이터 테이블
data class DataTable (
    val names: List<String>, // 테이블의 열 이름 목록
    val rows: List<Row> // 테이블의 행 (항목) 목록
)

// 테이블의 행 (항목)
data class Row (
    val values: List<Double> // 해당 항목의 각 열의 값
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 데이터 테이블
data class DataTable (
    val names: List<String>, // 테이블의 열 이름 목록
    val rows: MutableList<Row> // 테이블의 행 (항목) 목록
)

// 테이블의 행 (항목)
data class Row (
    val values: List<Double> // 해당 항목의 각 열의 값
)

// 질문이 "A" 또는 "B"를 포함 -> "A|B"를 포함
fun qc(query: String, substr: String): Boolean {
    return substr.split("|").count {
        query.contains(it)
    } > 0
}

// 쿼리에 "~번째"가 있는지 확인하고, 다른 숫자가 없으면 ~에 해당하는 숫자를 반환
fun findNthFromQuery(query: String): Int? {

    // 숫자가 연속되는 부분이 2곳 이상이면 실패
    val numStartIdx = (0 until query.length - 1).filter {
        !query[it].isDigit() && query[it + 1].isDigit()
    }
    val numEndIdx = (0 until query.length - 1).filter {
        query[it].isDigit() && !query[it + 1].isDigit()
    }
    
    if (
        numStartIdx.size > 0 && numEndIdx.size > 0 &&
        numStartIdx.max() > numEndIdx.min()
    ) {
        return null
    }
    
    // "번째" 직전의 숫자 값 구하기
    val num = query.substringBefore("번째").takeLastWhile {
        it.isDigit()
    }
    if (num.length > 0) {
        return num.toInt()
    } else {
        return null
    }
}

// 쿼리에서 숫자 값 찾기
fun findNumberFromQuery(query: String): Double? {
    val firstNumIndex = query.indexOfFirst {
        it in "0123456789"
    }
    if (firstNumIndex < 0) {
        return null
    }
    
    val firstNotNumIndexAfterNum = query.substring(firstNumIndex).indexOfFirst {
        !(it in "0123456789.")
    } + firstNumIndex
    
    if (firstNotNumIndexAfterNum >= 0) {
        return query.substring(firstNumIndex, firstNotNumIndexAfterNum).toDouble()
    } else {
        return null
    }
}

// 열 이름과 인덱스의 class
data class ColNameAndIndex (
    val colName: String,
    val index: Int
)

// 질문 사항 처리하고 답변하기
fun answerQueries(table: DataTable, query: String) {
    println("질문 : ${query}")
    
    // "카비스"가 없을 때 처리
    if (!query.contains("카비스")) {
        println("\n앗! 혹시 저를 부르셨나요?\n")
        return
    }
    
    // 호칭 및 본문 찾기
    val qcBeforeCallLength = query.substringBefore("카비스").length + 3
    val qcAfterCallLength = query.substringAfter("카비스").substringBefore(" ").length
    val qcLength = qcBeforeCallLength + qcAfterCallLength
    
    val qcall = query.substring(0, qcLength)
    val qm = if (qcLength < query.length) {
        query.substring(qcLength + 1)
    } else {
        ""
    }
    
    // 호칭이 알렉사, 시리, 자비스, 코타나 포함 시 처리
    if (qc(qcall, "알렉사|시리|자비스|코타나")) {
        println("\n저랑 다른 AI랑 제발 헷갈리지 말아 주세요.\n")
        return
    }
    
    println("호칭 : ${qcall}")
    println("본문 : ${qm}")
    
    // 열의 인덱스 (열을 추가하는 명령이 추가될 수도 있으므로 여기에 위치)
    val columnIndices = mutableMapOf<String, Int>()
    table.names.forEachIndexed { idx, it ->
        columnIndices.put(it, idx)
    }
    println("열의 인덱스: ${columnIndices}")
    
    // 쿼리된 열
    val queriedColumns = table.names.filter {
        qm.indexOf(it) >= 0
    }.map {
        ColNameAndIndex(it, qm.indexOf(it))
    }.sortedBy {
        it.index
    }.map {
        it.colName
    }
    println("쿼리된 열: ${queriedColumns}\n")
    
    // 행과 열의 개수는 자주 사용되므로 미리 저장
    val n = table.rows.size
    val nCols = table.names.size
    
    // 전체 행의 개수
    if (qc(qm, "항목|행") && qc(qm, "개수|몇 개") && !(qm.contains("열"))) {
        println("전체 행의 개수는 ${n}개입니다.")
    }
    
    // 전체 열의 개수
    else if (qm.contains("열") && qc(qm, "개수|몇 개") && !(qc(qm, "항목|행|평균"))) {
        println("전체 열의 개수는 ${nCols}개입니다.")
    }
    
    // 특정 열의 평균
    else if (queriedColumns.size == 1 && qm.contains("평균") && !(qc(qm, "개수|몇 개"))) {
        val queriedCol = queriedColumns[0]
        val colIndex = columnIndices[queriedCol]!!
        
        val result = table.rows.sumOf {
            it.values[colIndex]
        } / n.toDouble()
        
        println("열 ${queriedCol}의 평균은 ${result}입니다.")
    }
    
    // 특정 열에서 N번째로 큰 값
    else if (queriedColumns.size == 1 && qm.contains("번째") && qm.contains("큰")) {
        val n = findNthFromQuery(qm)
        
        if (n != null) {
            val queriedCol = queriedColumns[0]
            val queriedColIndex = columnIndices[queriedCol]!!
        
            val result = table.rows.map {
                it.values[queriedColIndex]
            }.sortedDescending()[n - 1]
            
            println("열 ${queriedCol}에서 ${n}번째로 큰 값은 ${result}입니다.")
        } else {
            println("제가 이해하기 어려운 질문이네요. 몇 번째로 큰 값인지 더 정확히 질문해 주실 수 있나요?")
        }
    }
    
    // 특정 열(A)의 값이 특정 값(x)인 항목의 다른 열(B)의 값
    else if (queriedColumns.size == 2) {
        
        // x의 값 찾기
        val x = findNumberFromQuery(qm)
        
        if (x != null) {
            val queriedCol = queriedColumns[0]
            val queriedColIndex = columnIndices[queriedCol]!!
            val answerCol = queriedColumns[1]
            val answerColIndex = columnIndices[answerCol]!!
            
            val results = table.rows.filter {
                it.values[queriedColIndex] == x
            }
            
            // 일치하는 항목의 개수에 따라 처리
            if (results.size == 1) {
                val result = results[0].values[answerColIndex]
                println("${queriedCol}이 ${x}인 항목의 ${answerCol}의 값은 ${result}입니다.")
            } else if (results.size > 1) {
                val result = results.map {
                    it.values[answerColIndex]
                }.joinToString(", ")
                println("${queriedCol}이 ${x}인 항목의 ${answerCol}의 값은 ${result}입니다.")
            } else {
                println("앗! ${queriedCol}이 ${x}인 항목이 없네요. 다른 항목을 찾으시는 것은 아닌가요?")
            }
        } else {
            println("제가 이해하기 어려운 질문이네요. 해당 열의 값이 무엇인지 더 정확히 질문해 주실 수 있나요?")
        }
    }
    
    // 행 추가하기
    else if (queriedColumns.size == nCols) {
        
        // 값 파싱하기
        val values = (0 until nCols).map {
            val after = if (it == nCols - 1) {
                qm.substringAfter(queriedColumns[it])
            } else {
                qm.substringAfter(queriedColumns[it]).substringBefore(queriedColumns[it + 1])
            }
            findNumberFromQuery(after)
        }
        
        if (values.count { it == null } > 0) {
            println("제가 이해하기 어려운 질문이네요. 추가할 항목의 값을 더 정확히 알려주실 수 있나요?")
        } else {
            // 해당하는 행 추가하기
            val queriedColIndices = (0 until nCols).map {
                columnIndices.get(queriedColumns[it])!!
            }
            println("queriedColumns: ${queriedColumns}")
            println("queriedColIndices: ${queriedColIndices}")
            println("values: ${values}")
        
            val result = (0 until nCols).map { 0.0 }.toMutableList()
            (0 until nCols).forEach {
                result[queriedColIndices[it]] = values[it]!!
            }
            println("result: ${result}\n")
        
            table.rows.add(Row(result))
            println("알려주신 대로 항목을 추가했습니다. 감사합니다.")
        }
    }
    
    // 행 삭제하기
    else if (queriedColumns.size == 1 && qm.contains("삭제")) {
        
        // x의 값 찾기
        val x = findNumberFromQuery(qm)
        
        if (x != null) {
            val queriedCol = queriedColumns[0]
            val queriedColIndex = columnIndices[queriedCol]!!
            
            // 삭제하기
            var deleteCount = 0
            for (i in n - 1 downTo 0) {
                if (table.rows[i].values[queriedColIndex] == x) {
                    table.rows.removeAt(i)
                    deleteCount++
                }
            }
            println("deleted rows: ${deleteCount}\n")
            
            // 삭제된 항목의 개수에 따라 처리
            if (deleteCount == 1) {
                println("알려주신 대로 항목을 삭제했습니다. 감사합니다.")
            } else if (deleteCount > 1) {
                println("알려주신 대로 항목을 삭제했습니다. 감사합니다. 많이 지우느라 힘들었어요.")
            } else {
                println("앗! ${queriedCol}이 ${x}인 항목이 없네요. 다른 항목을 삭제하시려는 것은 아닌가요?")
            }
        } else {
            println("제가 이해하기 어려운 질문이네요. 어떤 항목을 삭제할지 더 정확히 질문해 주실 수 있나요?")
        }
    }
    
    // 본문이 위의 어느 것에도 일치하지 않는 경우
    else {
        println("제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?")
    }
    
    print("\n")
}

fun main() {
    
    // 임시 데이터 테이블
    val testTable = DataTable (
        listOf("알렉사 파워", "AI 역량", "코딩 실력"),
        mutableListOf(
            Row(listOf(13.7, 69.5, 45.2)),
            Row(listOf(18.9, 84.6, 44.7)),
            Row(listOf(13.7, 79.5, 50.2)),
            Row(listOf(20.0, 95.7, 76.5)),
            Row(listOf(19.6, 96.0, 77.2)),
            Row(listOf(13.7, 71.2, 45.4)),
            Row(listOf(10.8, 39.5, 14.5)),
            Row(listOf(12.5, 68.7, 37.0)),
            Row(listOf(18.8, 84.0, 61.2)),
            Row(listOf(17.0, 77.7, 60.5))
        )
    )
    
    // 질문 목록
    val queries = listOf<String> (
        "카비스야, 행 개수 알려줘",
        "카비스! 행 몇 개야?",
        "카비스, 항목 개수 몇 개인지 알려줘",
        "헤이, 카비스! 행 개수하고 열은 몇 개?",
        "카비스, 열 개수!",
        "카비스, 여기서 열 몇 개?",
        "카비스, 여기서 열 개수는? 그리고 평균은?",
        "카비스! AI 역량 평균 알려줘.",
        "야, 카비스! AI 역량 평균 개수 알려줘",
        "카비스, 알렉사 파워 평균이 몇이야?",
        "카비스, 코딩 실력하고 AI 역량 평균 각각 알려줘!",
        "카비스, 알렉사 파워 2번째로 큰 거 얼마야?",
        "헤이 카비스! AI 역량 3번째로 큰 값은?",
        "헤이 카비스, 코딩 실력 4번째, 아니 2번째로 큰 값!",
        "카비스, 코딩 실력 2번째 큰 거, 아니 AI 역량 말이야",
        "헤이 카비스, 알렉사 파워 19.6인 사람들 AI 역량은?",
        "카비스, 코딩 실력 61.2이면 AI 역량은?",
        "헤이 카비스, 알렉사 파워 19.6인 사람들 AI 역량하고 코딩 실력은?",
        "카비스!, 알렉사 파워 13.7이면 AI 역량은 얼마?",
        "카비스!, AI 역량 84.6이면 알렉사 파워 얼마?",
        "카비스, 알렉사 파워 19.8일 때 코딩 실력 알려줘!",
        "카비스, AI 역량 97.0, 코딩 실력 81.2, 알렉사 파워 20.0 추가시켜 줘!",
        "카비스, AI 역량 40.0 삭제해 줘!",
        "카비스, AI 역량 이번에는 39.5 삭제해 줘!",
        "카비스, 알렉사 파워 13.7밖에 안 되는 인간들은 여기서 삭제해 줘!",
        "카비스, 여기서 행 몇 개야?",
        "여기서 행 몇 개지, 카비스?",
        "여기서 행 몇 개야, 궁금해!",
        "알렉사, 아니 카비스, 여기서 행 몇 개야?",
        "시리야 알렉사 파워 평균이 얼마인지 궁금해!",
        "카비스, 혹시 시리하고 너하고 사이 좋아?",
        "카비스, 자비스 파워가 3번째로 큰 값은 얼마야?"
    )
    
    // 질문 사항 처리
    queries.forEach {
        answerQueries(testTable, it)
    }
}
```

### 실행 결과
```kotlin
질문 : 카비스야, 행 개수 알려줘
호칭 : 카비스야,
본문 : 행 개수 알려줘
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

전체 행의 개수는 10개입니다.

질문 : 카비스! 행 몇 개야?
호칭 : 카비스!
본문 : 행 몇 개야?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

전체 행의 개수는 10개입니다.

질문 : 카비스, 항목 개수 몇 개인지 알려줘
호칭 : 카비스,
본문 : 항목 개수 몇 개인지 알려줘
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

전체 행의 개수는 10개입니다.

질문 : 헤이, 카비스! 행 개수하고 열은 몇 개?
호칭 : 헤이, 카비스!
본문 : 행 개수하고 열은 몇 개?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?

질문 : 카비스, 열 개수!
호칭 : 카비스,
본문 : 열 개수!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

전체 열의 개수는 3개입니다.

질문 : 카비스, 여기서 열 몇 개?
호칭 : 카비스,
본문 : 여기서 열 몇 개?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

전체 열의 개수는 3개입니다.

질문 : 카비스, 여기서 열 개수는? 그리고 평균은?
호칭 : 카비스,
본문 : 여기서 열 개수는? 그리고 평균은?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?

질문 : 카비스! AI 역량 평균 알려줘.
호칭 : 카비스!
본문 : AI 역량 평균 알려줘.
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [AI 역량]

열 AI 역량의 평균은 76.64000000000001입니다.

질문 : 야, 카비스! AI 역량 평균 개수 알려줘
호칭 : 야, 카비스!
본문 : AI 역량 평균 개수 알려줘
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [AI 역량]

제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?

질문 : 카비스, 알렉사 파워 평균이 몇이야?
호칭 : 카비스,
본문 : 알렉사 파워 평균이 몇이야?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [알렉사 파워]

열 알렉사 파워의 평균은 15.870000000000001입니다.

질문 : 카비스, 코딩 실력하고 AI 역량 평균 각각 알려줘!
호칭 : 카비스,
본문 : 코딩 실력하고 AI 역량 평균 각각 알려줘!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [코딩 실력, AI 역량]

제가 이해하기 어려운 질문이네요. 해당 열의 값이 무엇인지 더 정확히 질문해 주실 수 있나요?

질문 : 카비스, 알렉사 파워 2번째로 큰 거 얼마야?
호칭 : 카비스,
본문 : 알렉사 파워 2번째로 큰 거 얼마야?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [알렉사 파워]

열 알렉사 파워에서 2번째로 큰 값은 19.6입니다.

질문 : 헤이 카비스! AI 역량 3번째로 큰 값은?
호칭 : 헤이 카비스!
본문 : AI 역량 3번째로 큰 값은?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [AI 역량]

열 AI 역량에서 3번째로 큰 값은 84.6입니다.

질문 : 헤이 카비스, 코딩 실력 4번째, 아니 2번째로 큰 값!
호칭 : 헤이 카비스,
본문 : 코딩 실력 4번째, 아니 2번째로 큰 값!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [코딩 실력]

제가 이해하기 어려운 질문이네요. 몇 번째로 큰 값인지 더 정확히 질문해 주실 수 있나요?

질문 : 카비스, 코딩 실력 2번째 큰 거, 아니 AI 역량 말이야
호칭 : 카비스,
본문 : 코딩 실력 2번째 큰 거, 아니 AI 역량 말이야
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [코딩 실력, AI 역량]

앗! 코딩 실력이 2.0인 항목이 없네요. 다른 항목을 찾으시는 것은 아닌가요?

질문 : 헤이 카비스, 알렉사 파워 19.6인 사람들 AI 역량은?
호칭 : 헤이 카비스,
본문 : 알렉사 파워 19.6인 사람들 AI 역량은?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [알렉사 파워, AI 역량]

알렉사 파워이 19.6인 항목의 AI 역량의 값은 96.0입니다.

질문 : 카비스, 코딩 실력 61.2이면 AI 역량은?
호칭 : 카비스,
본문 : 코딩 실력 61.2이면 AI 역량은?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [코딩 실력, AI 역량]

코딩 실력이 61.2인 항목의 AI 역량의 값은 84.0입니다.

질문 : 헤이 카비스, 알렉사 파워 19.6인 사람들 AI 역량하고 코딩 실력은?
호칭 : 헤이 카비스,
본문 : 알렉사 파워 19.6인 사람들 AI 역량하고 코딩 실력은?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [알렉사 파워, AI 역량, 코딩 실력]

제가 이해하기 어려운 질문이네요. 추가할 항목의 값을 더 정확히 알려주실 수 있나요?

질문 : 카비스!, 알렉사 파워 13.7이면 AI 역량은 얼마?
호칭 : 카비스!,
본문 : 알렉사 파워 13.7이면 AI 역량은 얼마?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [알렉사 파워, AI 역량]

알렉사 파워이 13.7인 항목의 AI 역량의 값은 69.5, 79.5, 71.2입니다.

질문 : 카비스!, AI 역량 84.6이면 알렉사 파워 얼마?
호칭 : 카비스!,
본문 : AI 역량 84.6이면 알렉사 파워 얼마?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [AI 역량, 알렉사 파워]

AI 역량이 84.6인 항목의 알렉사 파워의 값은 18.9입니다.

질문 : 카비스, 알렉사 파워 19.8일 때 코딩 실력 알려줘!
호칭 : 카비스,
본문 : 알렉사 파워 19.8일 때 코딩 실력 알려줘!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [알렉사 파워, 코딩 실력]

앗! 알렉사 파워이 19.8인 항목이 없네요. 다른 항목을 찾으시는 것은 아닌가요?

질문 : 카비스, AI 역량 97.0, 코딩 실력 81.2, 알렉사 파워 20.0 추가시켜 줘!
호칭 : 카비스,
본문 : AI 역량 97.0, 코딩 실력 81.2, 알렉사 파워 20.0 추가시켜 줘!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [AI 역량, 코딩 실력, 알렉사 파워]

queriedColumns: [AI 역량, 코딩 실력, 알렉사 파워]
queriedColIndices: [1, 2, 0]
values: [97.0, 81.2, 20.0]
result: [20.0, 97.0, 81.2]

알려주신 대로 항목을 추가했습니다. 감사합니다.

질문 : 카비스, AI 역량 40.0 삭제해 줘!
호칭 : 카비스,
본문 : AI 역량 40.0 삭제해 줘!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [AI 역량]

deleted rows: 0

앗! AI 역량이 40.0인 항목이 없네요. 다른 항목을 삭제하시려는 것은 아닌가요?

질문 : 카비스, AI 역량 이번에는 39.5 삭제해 줘!
호칭 : 카비스,
본문 : AI 역량 이번에는 39.5 삭제해 줘!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [AI 역량]

deleted rows: 1

알려주신 대로 항목을 삭제했습니다. 감사합니다.

질문 : 카비스, 알렉사 파워 13.7밖에 안 되는 인간들은 여기서 삭제해 줘!
호칭 : 카비스,
본문 : 알렉사 파워 13.7밖에 안 되는 인간들은 여기서 삭제해 줘!
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: [알렉사 파워]

deleted rows: 3

알려주신 대로 항목을 삭제했습니다. 감사합니다. 많이 지우느라 힘들었어요.

질문 : 카비스, 여기서 행 몇 개야?
호칭 : 카비스,
본문 : 여기서 행 몇 개야?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

전체 행의 개수는 7개입니다.

질문 : 여기서 행 몇 개지, 카비스?
호칭 : 여기서 행 몇 개지, 카비스?
본문 : 
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?

질문 : 여기서 행 몇 개야, 궁금해!

앗! 혹시 저를 부르셨나요?

질문 : 알렉사, 아니 카비스, 여기서 행 몇 개야?

저랑 다른 AI랑 제발 헷갈리지 말아 주세요.

질문 : 시리야 알렉사 파워 평균이 얼마인지 궁금해!

앗! 혹시 저를 부르셨나요?

질문 : 카비스, 혹시 시리하고 너하고 사이 좋아?
호칭 : 카비스,
본문 : 혹시 시리하고 너하고 사이 좋아?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?

질문 : 카비스, 자비스 파워가 3번째로 큰 값은 얼마야?
호칭 : 카비스,
본문 : 자비스 파워가 3번째로 큰 값은 얼마야?
열의 인덱스: {알렉사 파워=0, AI 역량=1, 코딩 실력=2}
쿼리된 열: []

제가 이해하기 어려운 질문이네요. 더 구체적으로 질문해 주실 수 있나요?
```
