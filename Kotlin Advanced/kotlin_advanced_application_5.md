## 요구사항 분석
워너비슈퍼추어소프트에서는 서버의 성능 분석을 위해 서버 api를 호출했을 때의 응답 시간을 측정하고, 그 결과를 기록하여 통계를 내려고 한다. 여기서는 다음과 같은 정의를 이용한다.
* api : 서버와 클라이언트(앱) 간의 통신을 위한 일종의 시스템으로, 클라이언트의 서버 요청 사항별로 개발되어 있다.
* api 기록 : 클라이언트와 서버가 api를 통해 통신한 개별 기록을 의미하며, 다음과 같은 정보로 구성된다.
  * 클라이언트의 호출 시점 (유닉스 시간의 밀리초)
  * 서버의 응답 시점 (유닉스 시간의 밀리초)
  * api 이름 (api는 api 이름으로 구분하므로 api 이름은 api의 고유 식별자 역할을 한다.)
* 응답 시간 : ```서버의 응답 시점```에서 ```클라이언트의 호출 시점```을 뺀 값을 밀리초로 나타낸 값 (단위: 밀리초)

우리는 api 기록을 통해서 다음을 도출하여 출력하려고 한다.
* 평균 응답 시간이 가장 긴 ```5개```의 api에 대해, api 이름, 최대/최소/평균 응답 시간
  * 이때 api의 출력 순서는 평균 응답 시간이 긴 순으로 한다.
* 모든 api에 대해, 각 api별로 응답 시간이 ```400ms```를 초과하는 응답의 비율
* 모든 api에 대해, 각 api별로 ```1000ms``` 안에 클라이언트로부터 ```3번``` 이상 호출된 횟수
  * 매 호출마다 따로 고려한다. 예를 들어 특정 api가 ```1000ms``` 안에 ```3+n```회 호출된 경우, 해당 api에 대해 이 구간만 고려했을 때 이 횟수는 ```1+n```회이다.
* 모든 api에 대해, 각 api의 호출을 위해 클라이언트에서 추가한 인수의 개수의 평균값과 서버의 평균 응답 시간 사이의 상관계수
* 모든 api에 대해, 각 api의 호출을 위해 클라이언트에서 추가한 각 인수에 대해, 다음과 같이 계산한 값의 합의 해당 api에 대한 평균값과 서버의 평균 응답 시간 사이의 상관계수
  * 자료형이 ```Int```이면 ```1```의 값을 부여한다.
  * 자료형이 ```String```이면 다음과 같이 값을 부여한다.
    * 해당 문자열 (string) 의 길이가 ```8``` 이하이면 ```2```의 값을 부여한다.
    * 해당 문자열의 길이가 ```8``` 초과 ```16``` 이하이면 ```4```의 값을 부여한다.
    * 해당 문자열의 길이가 ```16``` 초과이면 ```16```의 값을 부여한다.

단, 클라이언트에서 추가한 인수의 자료형은 항상 ```Int``` 또는 ```String```이며, 타임아웃 때문에 응답 시간의 최댓값은 ```100,000ms```를 초과할 수 없다.

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
// API 호출 로그 목록
data class APILogList (
    val created_time: Long, // API 호출 로그 목록의 생성 시점 (밀리초)
    val api_call_log: List<APILog> // API 호출 로그
)

// 개별적인 API 호출 로그
data class APILog (
    val name: String, // API 이름
    val client_call: Long, // 클라이언트의 호출 시점 (밀리초)
    val server_response: Long, // 서버의 응답 시점 (밀리초)
    val arguments: List<APIArgumentItem> // 클라이언트에서 추가한 인수 목록
)

// 클라이언트에서 추가한 인수 목록의 각 항목
data class APIArgumentItem (
    val name: String, // 인수 이름
    val is_int: Boolean, // 인수의 자료형이 Int이면 true, 그렇지 않으면 false
    val value: Any? // 인수의 값 (is_int가 true이면 항상 Int 자료형이고, false이면 항상 String 자료형임)
)
```

## 예제 코드
```kotlin
import kotlin.math.*

// API 호출 로그 목록
data class APILogList (
    val created_time: Long, // API 호출 로그 목록의 생성 시점 (밀리초)
    val api_call_log: List<APILog> // API 호출 로그
)

// 개별적인 API 호출 로그
data class APILog (
    val name: String, // API 이름
    val client_call: Long, // 클라이언트의 호출 시점 (밀리초)
    val server_response: Long, // 서버의 응답 시점 (밀리초)
    val arguments: List<APIArgumentItem> // 클라이언트에서 추가한 인수 목록
)

// 클라이언트에서 추가한 인수 목록의 각 항목
data class APIArgumentItem (
    val name: String, // 인수 이름
    val is_int: Boolean, // 인수의 자료형이 Int이면 true, 그렇지 않으면 false
    val value: Any? // 인수의 값 (is_int가 true이면 항상 Int 자료형이고, false이면 항상 String 자료형임)
)

// 평균 응답 시간이 가장 긴 5개의 api에 대해, api 이름, 최대/최소/평균 응답 시간
data class APIMaxMinAvg (
    val name: String, // API 이름
    val maxTime: Int, // 최대 응답 시간
    val minTime: Int, // 최소 응답 시간
    val avgTime: Int // 평균 응답 시간
)

fun getStatsForLongestFive(logOfEachApi: Map<String, List<APILog>>) {
    val apiMaxMinAvg = mutableListOf<APIMaxMinAvg>()
    
    // 각 api의 응답 시간의 평균, 최대, 최솟값을 저장
    logOfEachApi.forEach {
        val avgRespTime = it.value.fold(0) { acc, e ->
            acc + (e.server_response - e.client_call).toInt()
        } / it.value.size
        
        val maxRespTime = it.value.fold(0) { acc, e ->
            Math.max(acc, (e.server_response - e.client_call).toInt())
        }
        
        val minRespTime = it.value.fold(100001) { acc, e ->
            Math.min(acc, (e.server_response - e.client_call).toInt())
        }
        
        apiMaxMinAvg.add(APIMaxMinAvg(it.key, maxRespTime, minRespTime, avgRespTime))
    }
    
    // 응답 시간이 긴 순으로 정렬
    apiMaxMinAvg.sortByDescending { it.avgTime }
    
    // 요구사항 1 최종 출력 (take 함수를 이용하여 상위 5위만 출력)
    println("\n요구 사항 1 출력 :")
    apiMaxMinAvg.take(5).forEachIndexed { index, value ->
        println("${index+1}위, API 이름: ${value.name}, 응답 시간(ms): 평균 ${value.avgTime}, 최대 ${value.maxTime}, 최소 ${value.minTime}")
    }
}

// 모든 api에 대해, 각 api별로 응답 시간이 400ms를 초과하는 응답의 비율
fun getProportionOfLongerThan400Ms(logOfEachApi: Map<String, List<APILog>>) {
    println("\n요구 사항 2 출력 :")
    
    logOfEachApi.forEach {
        val total = it.value.size
        val exceed = it.value.filter {
            it.server_response - it.client_call > 400
        }.size
        val ratio = (exceed.toDouble() / total.toDouble() * 10000.0).roundToInt() / 100.0
        
        println("API 이름: ${it.key}, 400ms 초과 비율: ${ratio}%")
    }
}

// 모든 api에 대해, 각 api별로 1000ms 안에 클라이언트로부터 3번 이상 호출된 횟수
fun getCountOfCalledFromClientThreeTimesIn1000Ms(logOfEachApi: Map<String, List<APILog>>) {
    println("\n요구 사항 3 출력 :")
    
    logOfEachApi.forEach {
        val mutableLogOfEachApi = it.value.toMutableList()
        
        // 로그 목록이 정렬되어 있지 않으므로, 먼저 로그 목록을 클라이언트 호출 시간 오름차순으로 정렬
        mutableLogOfEachApi.sortBy { it.client_call }
        
        // 클라이언트로부터 3회 이상 호출 횟수 계산
        var count = 0
        mutableLogOfEachApi.forEachIndexed { index, value ->
            if (index < mutableLogOfEachApi.size - 2) {
                if (mutableLogOfEachApi[index + 2].client_call - value.client_call < 1000) {
                    count++
                }
            }
        }
        
        println("API 이름: ${it.key}, 1000ms 동안 클라이언트 3회 호출 횟수: ${count}")
    }
}

// 상관계수 계산하기 (단, first와 second의 항목 개수는 서로 같음)
fun getCorrCoef(first: List<Double>, second: List<Double>): Double {
    
    // 항목이 1개이면 계산 불가능
    val n = first.size
    if (n == 1) {
        return 0.0
    }

    val avgFirst = first.sum() / n
    val avgSecond = second.sum() / n
    
    val sumFirstSecond = (0..n-1).sumOf { it ->
        (first[it] - avgFirst) * (second[it] - avgSecond)
    }
    val sumFirstSqaure = (0..n-1).sumOf { it ->
        (first[it] - avgFirst) * (first[it] - avgFirst)
    }
    val sumSecondSqaure = (0..n-1).sumOf { it ->
        (second[it] - avgSecond) * (second[it] - avgSecond)
    }
    
    val cov = sumFirstSecond / (n - 1)
    val varFirst = sumFirstSqaure / (n - 1)
    val varSecond = sumSecondSqaure / (n - 1)
    
    return cov / Math.sqrt(varFirst * varSecond)
}

// 요구 사항 4, 5번을 수행하는 함수는 향후 적절한 리팩토링이 필요할 수 있음

// 모든 api에 대해, 각 api의 호출을 위해 클라이언트에서 추가한 인수의 개수의 평균값과 서버의 평균 응답 시간 사이의 상관계수
fun getCorrCoefArgumentsAndResponseTime(logOfEachApi: Map<String, List<APILog>>) {
    val avgArgCnt = mutableListOf<Double>()
    val avgRespTime = mutableListOf<Double>()
    
    // 상관계수를 구하기 위해 인수 개수, 응답 시간 데이터를 추가
    logOfEachApi.forEach {
        
        // 평균 인수 개수
        val averageArgs = it.value.sumOf {
            it.arguments.size
        }.toDouble() / it.value.size.toDouble()
        
        // 평균 응답 시간
        val averageRespTime = it.value.sumOf {
            it.server_response - it.client_call
        }.toDouble() / it.value.size.toDouble()
        
        avgArgCnt.add(averageArgs)
        avgRespTime.add(averageRespTime)
    }
    
    println("\n2. ${avgArgCnt}")
    println("3. ${avgRespTime}")
    
    // 상관계수 계산 및 출력
    val corrCoef = getCorrCoef(avgArgCnt, avgRespTime)
    println("\n요구 사항 4 출력 : ${corrCoef}")
}

// 모든 api에 대해, 각 api의 호출을 위해 클라이언트에서 추가한 각 인수에 대해, 다음과 같이 계산한 값의 합의 해당 api에 대한 평균값과 서버의 평균 응답 시간 사이의 상관계수
fun getArgScore(arguments: List<APIArgumentItem>): Int {
    return arguments.fold(0) { acc, e ->
        if (e.is_int) {
            acc + 1
        } else {
            when ((e.value as String).length) {
                in 0..8 -> acc + 2
                in 9..16 -> acc + 4
                else -> acc + 16
            }
        }
    }
}

fun getCorrCoefArgumentsScoreAndResponseTime(logOfEachApi: Map<String, List<APILog>>) {
    val avgArgScore = mutableListOf<Double>()
    val avgRespTime = mutableListOf<Double>()
    
    // 상관계수를 구하기 위해 인수 개수, 응답 시간 데이터를 추가
    logOfEachApi.forEach {
        
        // 평균 인수 score (Int인 경우 1, String인 경우 8자 이하는 2, 8자 초과 16자 이하는 4, 16자 초과는 16)
        val averageArgScore = it.value.sumOf {
            getArgScore(it.arguments)
        }.toDouble() / it.value.size.toDouble()
        
        // 평균 응답 시간
        val averageRespTime = it.value.sumOf {
            it.server_response - it.client_call
        }.toDouble() / it.value.size.toDouble()
        
        avgArgScore.add(averageArgScore)
        avgRespTime.add(averageRespTime)
    }
    
    println("\n4. ${avgArgScore}")
    println("5. ${avgRespTime}")
    
    // 상관계수 계산 및 출력
    val corrCoef = getCorrCoef(avgArgScore, avgRespTime)
    println("\n요구 사항 5 출력 : ${corrCoef}")
}

fun main() {
    
    // 임시 API 호출 로그
    val testAPILog = listOf<APILog> (
        APILog("getRoutine", 1663827354000, 1663827354135, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "hong"),
            APIArgumentItem("userId", true, 2662)
        )),
        APILog("getRoutine", 1663827354500, 1663827355270, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "hong"),
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userState", true, 1)
        )),
        APILog("getRoutine", 1663827354900, 1663827355318, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "hong"),
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userState", true, 0)
        )),
        APILog("getRoutine", 1663827712100, 1663827712345, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "hong"),
            APIArgumentItem("userId", true, 2662)
        )),
        APILog("getRoutine", 1663827712400, 1663827712460, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "hong"),
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userState", true, 1)
        )),
        APILog("getRoutine", 1663827712900, 1663827713902, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "hong"),
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userState", true, 1)
        )),
        APILog("getRoutine", 1663827713200, 1663827713918, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "hong"),
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userState", true, 0)
        )),
        APILog("openBox", 1663827500120, 1663827500400, listOf<APIArgumentItem>(
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userBonusCnt", true, 200),
            APIArgumentItem("userClass", false, "loyal user"),
            APIArgumentItem("userState", true, 0)
        )),
        APILog("openBox", 1663827501720, 1663827502100, listOf<APIArgumentItem>(
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userBonusCnt", true, 250),
            APIArgumentItem("userClass", false, "loyal user"),
            APIArgumentItem("userState", true, 0)
        )),
        APILog("openBox", 1663827506220, 1663827507020, listOf<APIArgumentItem>(
            APIArgumentItem("userId", true, 2662),
            APIArgumentItem("userBonusCnt", true, 300),
            APIArgumentItem("userClass", false, "the grand loyal user"),
            APIArgumentItem("userState", true, 0)
        )),
        APILog("getRoutine", 1663827645621, 1663827646024, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "min"),
            APIArgumentItem("userId", true, 2712),
            APIArgumentItem("userState", true, 1)
        )),
        APILog("loadUserInfo", 1663828011455, 1663828012578, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "yoo"),
            APIArgumentItem("userId", true, 1102)
        )),
        APILog("loadUserInfo", 1663828017045, 1663828018222, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "jinnie_master"),
            APIArgumentItem("userId", true, 150)
        )),
        APILog("getBonusInfo", 1663827000120, 1663827000600, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "teemo_of_lol"),
            APIArgumentItem("userId", true, 1027)
        )),
        APILog("getBonusInfo", 1663827001123, 1663827001543, listOf<APIArgumentItem>(
            APIArgumentItem("userName", false, "jinnie_master"),
            APIArgumentItem("userId", true, 150)
        ))
    )
    
    // 임시 API 호출 목록 리스트
    val APILogList = APILogList(1663901024500, testAPILog)
    
    // 결과 출력
    val apiLogs = APILogList.api_call_log
    val logOfEachApi = apiLogs.groupBy { it.name }
    
    println("1. log of each API")
    
    var index = 0
    logOfEachApi.forEach {
        println("1-${index+1}. ${it}")
        index++
    }
    
    // 각 요구 사항별 출력
    getStatsForLongestFive(logOfEachApi)
    getProportionOfLongerThan400Ms(logOfEachApi)
    getCountOfCalledFromClientThreeTimesIn1000Ms(logOfEachApi)
    getCorrCoefArgumentsAndResponseTime(logOfEachApi)
    getCorrCoefArgumentsScoreAndResponseTime(logOfEachApi)
}
```

## 예제 출력
```kotlin
1. log of each API
1-1. getRoutine=[APILog(name=getRoutine, client_call=1663827354000, server_response=1663827354135, arguments=[APIArgumentItem(name=userName, is_int=false, value=hong), APIArgumentItem(name=userId, is_int=true, value=2662)]), APILog(name=getRoutine, client_call=1663827354500, server_response=1663827355270, arguments=[APIArgumentItem(name=userName, is_int=false, value=hong), APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userState, is_int=true, value=1)]), APILog(name=getRoutine, client_call=1663827354900, server_response=1663827355318, arguments=[APIArgumentItem(name=userName, is_int=false, value=hong), APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userState, is_int=true, value=0)]), APILog(name=getRoutine, client_call=1663827712100, server_response=1663827712345, arguments=[APIArgumentItem(name=userName, is_int=false, value=hong), APIArgumentItem(name=userId, is_int=true, value=2662)]), APILog(name=getRoutine, client_call=1663827712400, server_response=1663827712460, arguments=[APIArgumentItem(name=userName, is_int=false, value=hong), APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userState, is_int=true, value=1)]), APILog(name=getRoutine, client_call=1663827712900, server_response=1663827713902, arguments=[APIArgumentItem(name=userName, is_int=false, value=hong), APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userState, is_int=true, value=1)]), APILog(name=getRoutine, client_call=1663827713200, server_response=1663827713918, arguments=[APIArgumentItem(name=userName, is_int=false, value=hong), APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userState, is_int=true, value=0)]), APILog(name=getRoutine, client_call=1663827645621, server_response=1663827646024, arguments=[APIArgumentItem(name=userName, is_int=false, value=min), APIArgumentItem(name=userId, is_int=true, value=2712), APIArgumentItem(name=userState, is_int=true, value=1)])]
1-2. openBox=[APILog(name=openBox, client_call=1663827500120, server_response=1663827500400, arguments=[APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userBonusCnt, is_int=true, value=200), APIArgumentItem(name=userClass, is_int=false, value=loyal user), APIArgumentItem(name=userState, is_int=true, value=0)]), APILog(name=openBox, client_call=1663827501720, server_response=1663827502100, arguments=[APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userBonusCnt, is_int=true, value=250), APIArgumentItem(name=userClass, is_int=false, value=loyal user), APIArgumentItem(name=userState, is_int=true, value=0)]), APILog(name=openBox, client_call=1663827506220, server_response=1663827507020, arguments=[APIArgumentItem(name=userId, is_int=true, value=2662), APIArgumentItem(name=userBonusCnt, is_int=true, value=300), APIArgumentItem(name=userClass, is_int=false, value=the grand loyal user), APIArgumentItem(name=userState, is_int=true, value=0)])]
1-3. loadUserInfo=[APILog(name=loadUserInfo, client_call=1663828011455, server_response=1663828012578, arguments=[APIArgumentItem(name=userName, is_int=false, value=yoo), APIArgumentItem(name=userId, is_int=true, value=1102)]), APILog(name=loadUserInfo, client_call=1663828017045, server_response=1663828018222, arguments=[APIArgumentItem(name=userName, is_int=false, value=jinnie_master), APIArgumentItem(name=userId, is_int=true, value=150)])]
1-4. getBonusInfo=[APILog(name=getBonusInfo, client_call=1663827000120, server_response=1663827000600, arguments=[APIArgumentItem(name=userName, is_int=false, value=teemo_of_lol), APIArgumentItem(name=userId, is_int=true, value=1027)]), APILog(name=getBonusInfo, client_call=1663827001123, server_response=1663827001543, arguments=[APIArgumentItem(name=userName, is_int=false, value=jinnie_master), APIArgumentItem(name=userId, is_int=true, value=150)])]

요구 사항 1 출력 :
1위, API 이름: loadUserInfo, 응답 시간(ms): 평균 1150, 최대 1177, 최소 1123
2위, API 이름: openBox, 응답 시간(ms): 평균 486, 최대 800, 최소 280
3위, API 이름: getRoutine, 응답 시간(ms): 평균 468, 최대 1002, 최소 60
4위, API 이름: getBonusInfo, 응답 시간(ms): 평균 450, 최대 480, 최소 420

요구 사항 2 출력 :
API 이름: getRoutine, 400ms 초과 비율: 62.5%
API 이름: openBox, 400ms 초과 비율: 33.33%
API 이름: loadUserInfo, 400ms 초과 비율: 100.0%
API 이름: getBonusInfo, 400ms 초과 비율: 100.0%

요구 사항 3 출력 :
API 이름: getRoutine, 1000ms 동안 클라이언트 3회 호출 횟수: 3
API 이름: openBox, 1000ms 동안 클라이언트 3회 호출 횟수: 0
API 이름: loadUserInfo, 1000ms 동안 클라이언트 3회 호출 횟수: 0
API 이름: getBonusInfo, 1000ms 동안 클라이언트 3회 호출 횟수: 0

2. [2.75, 4.0, 2.0, 2.0]
3. [468.875, 486.6666666666667, 1150.0, 450.0]

요구 사항 4 출력 : -0.44731582552073335

4. [3.75, 11.0, 4.0, 5.0]
5. [468.875, 486.6666666666667, 1150.0, 450.0]

요구 사항 5 출력 : -0.3465299222843096
```
