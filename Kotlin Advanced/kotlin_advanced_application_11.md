## 요구 사항 분석
워너비슈퍼추어소프트에서는 재직 중인 개발자들의 MBTI에 따른 능력치를 통계 내려고 한다.

* MBTI별로 다음을 구하는 프로그램을 작성하시오.
  * MBTI가 ```E```와 ```I```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * MBTI가 ```N```과 ```S```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * MBTI가 ```T```와 ```F```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * MBTI가 ```P```와 ```J```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * 16가지 MBTI 유형 각각의 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * ```ST``` 유형과 ```TJ``` 유형 각각의 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * ```근태 및 성실성``` 점수와 ```주당 근로시간```의 평균값 각각이 가장 큰 MBTI 유형
  * ```근태 및 성실성``` 점수와 ```주당 근로시간``` 간, 전체 유형 및 ```ISTJ``` 유형 각각의 상관계수
  * ```총평 등급``` 점수와 ```주당 근로시간``` 간, 전체 유형 및 ```ISTJ``` 유형 각각의 상관계수
  * ```협업 및 커뮤니케이션 능력``` 점수와 ```주당 근로시간``` 간, 전체 유형 및 ```ISTJ``` 유형 각각의 상관계수
* ```전체 정보```는 ```영역별 점수```와 ```주당 근로시간```, ```총평 등급```으로 나누어진다.
  * ```영역별 점수```는 ```기본 업무 능력```, ```기술력```, ```근태 및 성실성```, ```협업 및 커뮤니케이션 능력```의 4가지로 나누어진다.
  * ```주당 근로시간```은 ```시간``` 단위로 나타낸다.
  * ```총평 등급```은 ```S```부터 ```F```까지이며, 높은 순서대로 ```S```, ```A```, ```B```, ```C```, ```F```이다.
    * 이때, ```S```는 10점, ```A```는 9점, ```B```는 8점, ```C```는 7점, ```F```는 0점으로 계산한다. 

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
data class Developer (
    val name: String, // 개발자 이름
    val ID: Int, // 개발자의 고유 ID
    val MBTI: String, // MBTI
    val total_info: TotalInfo // 전체 정보
)

// 전체 정보
data class TotalInfo (
    val scores: Scores, // 영역별 점수
    val working_hours: Int, // 주당 근로시간
    val rating: Char // 등급
)

// 영역별 점수
data class Scores (
    val basic: Int, // 기본 업무 능력
    val skill: Int, // 기술력
    val sincerity: Int, // 근태 및 성실성
    val co_working: Int // 협업 및 커뮤니케이션 능력
)
```

### 상관계수 계산 함수
상관계수를 계산하는 함수는 다음과 같다.
```kotlin
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
```

## 예제 코드
```kotlin
import kotlin.math.*

data class Developer (
   	val name: String, // 개발자 이름
    val ID: Int, // 개발자의 고유 ID
    val MBTI: String, // MBTI
   	val total_info: TotalInfo // 전체 정보
)

// 전체 정보
data class TotalInfo (
   	val scores: Scores, // 영역별 점수
    val working_hours: Int, // 주당 근로시간
    val rating: Char // 등급
)

// 영역별 점수
data class Scores (
   	val basic: Int, // 기본 업무 능력
   	val skill: Int, // 기술력
    val sincerity: Int, // 근태 및 성실성
    val co_working: Int // 협업 및 커뮤니케이션 능력
)

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

// 각 성격 유형에 따른 통계를 출력하는 함수
fun printStatisticsOfDevList(
    devList: Map<String, List<Developer>>,
    matchFuncs: Map<String, ((Developer) -> Int)>
) {
    devList.forEach { type ->
        var resultStr = "${type.key} 유형\n"
            
        matchFuncs.forEach { func ->
            resultStr += "${func.key}: "
                
            val maxValue = type.value.maxOf { dev -> func.value(dev) }
            val minValue = type.value.minOf { dev -> func.value(dev) }
            val avgValue = type.value.sumOf { dev -> func.value(dev) } / type.value.size.toDouble()
            val medDev = type.value.sortedBy { dev -> func.value(dev) }[type.value.size / 2]
            val medValue = func.value(medDev)
                
            resultStr += "최대=${maxValue}, 최소=${minValue}, 평균=${avgValue}, 중앙값=${medValue}\n"
        }
        println(resultStr)
    }
}

fun printStatisticsForEachMBTI(
    devLists: List<Map<String, List<Developer>>>,
    matchFuncs: Map<String, ((Developer) -> Int)>
) {
    devLists.forEachIndexed { idx, it ->
        printStatisticsOfDevList(it, matchFuncs)
    }
}

// 각 질문에 대해 답하는 함수
fun printSolution(developers: List<Developer>) {
    
    val getBasic: ((Developer) -> Int) = { it.total_info.scores.basic }
    val getSkill: ((Developer) -> Int) = { it.total_info.scores.skill }
    val getSincerity: ((Developer) -> Int) = { it.total_info.scores.sincerity }
    val getCoWorking: ((Developer) -> Int) = { it.total_info.scores.co_working }
    
    val getRating: ((Developer) -> Int) = {
        when (it.total_info.rating) {
            'S' -> 10
            'A' -> 9
            'B' -> 8
            'C' -> 7
            else -> 0
        }
    }
    
    val matchFuncs = mapOf("기본" to getBasic, "기술력" to getSkill,
                           "근태" to getSincerity, "협업" to getCoWorking)
    
    // 1. MBTI가 E와 I인 직원들의 전체 정보의 각 항목의 최대, 최소, 평균, 중앙값
   	// 2. MBTI가 N과 S인 직원들의 전체 정보의 각 항목의 최대, 최소, 평균, 중앙값
   	// 3. MBTI가 T와 F인 직원들의 전체 정보의 각 항목의 최대, 최소, 평균, 중앙값
   	// 4. MBTI가 P와 J인 직원들의 전체 정보의 각 항목의 최대, 최소, 평균, 중앙값
   	val EI = developers.groupBy { it.MBTI[0].toString() }
    val NS = developers.groupBy { it.MBTI[1].toString() }
    val TF = developers.groupBy { it.MBTI[2].toString() }
    val PJ = developers.groupBy { it.MBTI[3].toString() }
    
    println("1. ~ 4. 답:\n")
    printStatisticsForEachMBTI(listOf(EI, NS, TF, PJ), matchFuncs)
    
    // 5. 16가지 MBTI 유형 각각의 직원들의 전체 정보의 각 항목의 최대, 최소, 평균, 중앙값
    val eachMBTI = developers.groupBy { it.MBTI }
    
    println("5. 답:\n")
    printStatisticsOfDevList(eachMBTI, matchFuncs)
    
    // 6. ST 유형과 TJ 유형 각각의 직원들의 전체 정보의 각 항목의 최대, 최소, 평균, 중앙값
    val ST = mapOf("ST" to developers.filter { it.MBTI.contains("ST") })
    val TJ = mapOf("TJ" to developers.filter { it.MBTI.contains("TJ") })
    
    println("6. 답:\n")
    printStatisticsForEachMBTI(listOf(ST, TJ), matchFuncs)
    
    // 7. 근태 및 성실성 점수와 주당 근로시간의 평균값 각각이 가장 큰 MBTI 유형
    var sincerityMax = 0.0
    var sincerityMaxMBTI = ""
    var workingHoursMax = 0.0
    var workingHoursMaxMBTI = ""
    
    println("7. 중간 출력\n")
    eachMBTI.forEach {
        val sincerity = it.value.sumOf { dev -> getSincerity(dev) } / it.value.size.toDouble()
        val workingHours = it.value.sumOf { dev -> dev.total_info.working_hours } / it.value.size.toDouble()
        
        if (sincerity > sincerityMax) {
            sincerityMax = sincerity
            sincerityMaxMBTI = it.key
        }
        if (workingHours > workingHoursMax) {
            workingHoursMax = workingHours
            workingHoursMaxMBTI = it.key
        }
        
        println("${it.key} 근태 및 성실성=${sincerity}, 주당 근로시간=${workingHours}")
    }
    
    println("\n7. 답\n")
    println("근태 및 성실성: ${sincerityMaxMBTI}, 주당 근로시간: ${workingHoursMaxMBTI}\n")
    
    // 8. 근태 및 성실성 점수와 주당 근로시간 간, 전체 유형 및 ISTJ 유형 각각의 상관계수
    // 9. 총평 등급 점수와 주당 근로시간 간, 전체 유형 및 ISTJ 유형 각각의 상관계수
    // 10. 협업 및 커뮤니케이션 능력 점수와 주당 근로시간 간, 전체 유형 및 ISTJ 유형 각각의 상관계수
    val devsISTJ = eachMBTI.get("ISTJ")
   	val qNumToFunc = mapOf(listOf(8, "근태 및 성실성") to getSincerity,
                           listOf(9, "총평 등급") to getRating,
                           listOf(10, "협업 및 커뮤니케이션") to getCoWorking)
    
    qNumToFunc.forEach {
        val workingHours = developers.map { dev -> dev.total_info.working_hours }
        val workingHoursISTJ = devsISTJ!!.map { dev -> dev.total_info.working_hours }
        val scores = developers.map { dev -> it.value(dev) }
        val scoresISTJ = devsISTJ.map { dev -> it.value(dev) }

        println("${it.key[0]}. 중간 출력\n")
        println("전체 주당 근로시간: ${workingHours}")
        println("ISTJ 주당 근로시간: ${workingHoursISTJ}")
        println("전체 점수 (${it.key[1]}): ${scores}")
        println("ISTJ 점수 (${it.key[1]}): ${scoresISTJ}\n")
        
        // 상관계수 계산
        val corr = getCorrCoef(workingHours.map { it.toDouble() }, scores.map { it.toDouble() })
        val corrISTJ = getCorrCoef(workingHoursISTJ.map { it.toDouble() }, scoresISTJ.map { it.toDouble() })
        
        println("${it.key[0]}. 답:\n")
        println("전체 유형 상관계수: ${corr}")
        println("ISTJ 유형 상관계수: ${corrISTJ}\n")
    }
}

fun main() {
    
    // 임시 개발자 목록
    val developers = listOf<Developer> (
       	Developer("hong", 100, "ISTJ", TotalInfo(Scores(8, 7, 10, 0), 84, 'F')),
        Developer("min", 101, "ISTP", TotalInfo(Scores(8, 9, 9, 6), 45, 'A')),
        Developer("joon", 102, "INTJ", TotalInfo(Scores(8, 6, 10, 8), 43, 'B')),
        Developer("king", 103, "ISTJ", TotalInfo(Scores(10, 10, 10, 4), 60, 'C')),
        Developer("jin", 104, "ENFP", TotalInfo(Scores(8, 7, 5, 10), 36, 'S')),
        Developer("young", 105, "ENTP", TotalInfo(Scores(8, 8, 8, 9), 41, 'A')),
        Developer("do", 106, "ESTJ", TotalInfo(Scores(9, 9, 10, 7), 43, 'A')),
        Developer("hye", 107, "ESTJ", TotalInfo(Scores(8, 8, 10, 10), 44, 'S')),
        Developer("so", 108, "ENTJ", TotalInfo(Scores(6, 9, 10, 9), 40, 'A')),
        Developer("bin", 109, "ISFP", TotalInfo(Scores(9, 7, 9, 10), 39, 'A')),
        Developer("hong", 110, "INFP", TotalInfo(Scores(9, 9, 7, 7), 42, 'B')),
        Developer("myoung", 111, "INTJ", TotalInfo(Scores(9, 7, 7, 6), 41, 'B')),
        Developer("hyun", 112, "INTP", TotalInfo(Scores(10, 10, 8, 6), 41, 'A')),
        Developer("hyuk", 113, "ISTJ", TotalInfo(Scores(9, 7, 10, 0), 87, 'F')),
        Developer("soo", 114, "ISTP", TotalInfo(Scores(6, 6, 9, 10), 44, 'B')),
        Developer("jeong", 115, "ISFP", TotalInfo(Scores(7, 7, 7, 7), 49, 'C')),
        Developer("hwa", 116, "ESFP", TotalInfo(Scores(9, 7, 7, 9), 46, 'A')),
        Developer("in", 117, "ENFP", TotalInfo(Scores(8, 8, 5, 10), 34, 'A')),
        Developer("jack", 118, "ENFP", TotalInfo(Scores(8, 9, 8, 9), 38, 'S')),
        Developer("hee", 119, "ISTJ", TotalInfo(Scores(7, 7, 7, 0), 90, 'F')),
    )
    
    // 각각의 질문에 대해 대답하는 함수
    printSolution(developers)
}
```

## 예제 출력
```kotlin
1. ~ 4. 답:

I 유형
기본: 최대=10, 최소=6, 평균=8.333333333333334, 중앙값=9
기술력: 최대=10, 최소=6, 평균=7.666666666666667, 중앙값=7
근태: 최대=10, 최소=7, 평균=8.583333333333334, 중앙값=9
협업: 최대=10, 최소=0, 평균=5.333333333333333, 중앙값=6

E 유형
기본: 최대=9, 최소=6, 평균=8.0, 중앙값=8
기술력: 최대=9, 최소=7, 평균=8.125, 중앙값=8
근태: 최대=10, 최소=5, 평균=7.875, 중앙값=8
협업: 최대=10, 최소=7, 평균=9.125, 중앙값=9

S 유형
기본: 최대=10, 최소=6, 평균=8.181818181818182, 중앙값=8
기술력: 최대=10, 최소=6, 평균=7.636363636363637, 중앙값=7
근태: 최대=10, 최소=7, 평균=8.909090909090908, 중앙값=9
협업: 최대=10, 최소=0, 평균=5.7272727272727275, 중앙값=7

N 유형
기본: 최대=10, 최소=6, 평균=8.222222222222221, 중앙값=8
기술력: 최대=10, 최소=6, 평균=8.11111111111111, 중앙값=8
근태: 최대=10, 최소=5, 평균=7.555555555555555, 중앙값=8
협업: 최대=10, 최소=6, 평균=8.222222222222221, 중앙값=9

T 유형
기본: 최대=10, 최소=6, 평균=8.153846153846153, 중앙값=8
기술력: 최대=10, 최소=6, 평균=7.923076923076923, 중앙값=8
근태: 최대=10, 최소=7, 평균=9.076923076923077, 중앙값=10
협업: 최대=10, 최소=0, 평균=5.769230769230769, 중앙값=6

F 유형
기본: 최대=9, 최소=7, 평균=8.285714285714286, 중앙값=8
기술력: 최대=9, 최소=7, 평균=7.714285714285714, 중앙값=7
근태: 최대=9, 최소=5, 평균=6.857142857142857, 중앙값=7
협업: 최대=10, 최소=7, 평균=8.857142857142858, 중앙값=9

J 유형
기본: 최대=10, 최소=6, 평균=8.222222222222221, 중앙값=8
기술력: 최대=10, 최소=6, 평균=7.777777777777778, 중앙값=7
근태: 최대=10, 최소=7, 평균=9.333333333333334, 중앙값=10
협업: 최대=10, 최소=0, 평균=4.888888888888889, 중앙값=6

P 유형
기본: 최대=10, 최소=6, 평균=8.181818181818182, 중앙값=8
기술력: 최대=10, 최소=6, 평균=7.909090909090909, 중앙값=8
근태: 최대=9, 최소=5, 평균=7.454545454545454, 중앙값=8
협업: 최대=10, 최소=6, 평균=8.454545454545455, 중앙값=9

5. 답:

ISTJ 유형
기본: 최대=10, 최소=7, 평균=8.5, 중앙값=9
기술력: 최대=10, 최소=7, 평균=7.75, 중앙값=7
근태: 최대=10, 최소=7, 평균=9.25, 중앙값=10
협업: 최대=4, 최소=0, 평균=1.0, 중앙값=0

ISTP 유형
기본: 최대=8, 최소=6, 평균=7.0, 중앙값=8
기술력: 최대=9, 최소=6, 평균=7.5, 중앙값=9
근태: 최대=9, 최소=9, 평균=9.0, 중앙값=9
협업: 최대=10, 최소=6, 평균=8.0, 중앙값=10

INTJ 유형
기본: 최대=9, 최소=8, 평균=8.5, 중앙값=9
기술력: 최대=7, 최소=6, 평균=6.5, 중앙값=7
근태: 최대=10, 최소=7, 평균=8.5, 중앙값=10
협업: 최대=8, 최소=6, 평균=7.0, 중앙값=8

ENFP 유형
기본: 최대=8, 최소=8, 평균=8.0, 중앙값=8
기술력: 최대=9, 최소=7, 평균=8.0, 중앙값=8
근태: 최대=8, 최소=5, 평균=6.0, 중앙값=5
협업: 최대=10, 최소=9, 평균=9.666666666666666, 중앙값=10

ENTP 유형
기본: 최대=8, 최소=8, 평균=8.0, 중앙값=8
기술력: 최대=8, 최소=8, 평균=8.0, 중앙값=8
근태: 최대=8, 최소=8, 평균=8.0, 중앙값=8
협업: 최대=9, 최소=9, 평균=9.0, 중앙값=9

ESTJ 유형
기본: 최대=9, 최소=8, 평균=8.5, 중앙값=9
기술력: 최대=9, 최소=8, 평균=8.5, 중앙값=9
근태: 최대=10, 최소=10, 평균=10.0, 중앙값=10
협업: 최대=10, 최소=7, 평균=8.5, 중앙값=10

ENTJ 유형
기본: 최대=6, 최소=6, 평균=6.0, 중앙값=6
기술력: 최대=9, 최소=9, 평균=9.0, 중앙값=9
근태: 최대=10, 최소=10, 평균=10.0, 중앙값=10
협업: 최대=9, 최소=9, 평균=9.0, 중앙값=9

ISFP 유형
기본: 최대=9, 최소=7, 평균=8.0, 중앙값=9
기술력: 최대=7, 최소=7, 평균=7.0, 중앙값=7
근태: 최대=9, 최소=7, 평균=8.0, 중앙값=9
협업: 최대=10, 최소=7, 평균=8.5, 중앙값=10

INFP 유형
기본: 최대=9, 최소=9, 평균=9.0, 중앙값=9
기술력: 최대=9, 최소=9, 평균=9.0, 중앙값=9
근태: 최대=7, 최소=7, 평균=7.0, 중앙값=7
협업: 최대=7, 최소=7, 평균=7.0, 중앙값=7

INTP 유형
기본: 최대=10, 최소=10, 평균=10.0, 중앙값=10
기술력: 최대=10, 최소=10, 평균=10.0, 중앙값=10
근태: 최대=8, 최소=8, 평균=8.0, 중앙값=8
협업: 최대=6, 최소=6, 평균=6.0, 중앙값=6

ESFP 유형
기본: 최대=9, 최소=9, 평균=9.0, 중앙값=9
기술력: 최대=7, 최소=7, 평균=7.0, 중앙값=7
근태: 최대=7, 최소=7, 평균=7.0, 중앙값=7
협업: 최대=9, 최소=9, 평균=9.0, 중앙값=9

6. 답:

ST 유형
기본: 최대=10, 최소=6, 평균=8.125, 중앙값=8
기술력: 최대=10, 최소=6, 평균=7.875, 중앙값=8
근태: 최대=10, 최소=7, 평균=9.375, 중앙값=10
협업: 최대=10, 최소=0, 평균=4.625, 중앙값=6

TJ 유형
기본: 최대=10, 최소=6, 평균=8.222222222222221, 중앙값=8
기술력: 최대=10, 최소=6, 평균=7.777777777777778, 중앙값=7
근태: 최대=10, 최소=7, 평균=9.333333333333334, 중앙값=10
협업: 최대=10, 최소=0, 평균=4.888888888888889, 중앙값=6

7. 중간 출력

ISTJ 근태 및 성실성=9.25, 주당 근로시간=80.25
ISTP 근태 및 성실성=9.0, 주당 근로시간=44.5
INTJ 근태 및 성실성=8.5, 주당 근로시간=42.0
ENFP 근태 및 성실성=6.0, 주당 근로시간=36.0
ENTP 근태 및 성실성=8.0, 주당 근로시간=41.0
ESTJ 근태 및 성실성=10.0, 주당 근로시간=43.5
ENTJ 근태 및 성실성=10.0, 주당 근로시간=40.0
ISFP 근태 및 성실성=8.0, 주당 근로시간=44.0
INFP 근태 및 성실성=7.0, 주당 근로시간=42.0
INTP 근태 및 성실성=8.0, 주당 근로시간=41.0
ESFP 근태 및 성실성=7.0, 주당 근로시간=46.0

7. 답

근태 및 성실성: ESTJ, 주당 근로시간: ISTJ

8. 중간 출력

전체 주당 근로시간: [84, 45, 43, 60, 36, 41, 43, 44, 40, 39, 42, 41, 41, 87, 44, 49, 46, 34, 38, 90]
ISTJ 주당 근로시간: [84, 60, 87, 90]
전체 점수 (근태 및 성실성): [10, 9, 10, 10, 5, 8, 10, 10, 10, 9, 7, 7, 8, 10, 9, 7, 7, 5, 8, 7]
ISTJ 점수 (근태 및 성실성): [10, 10, 10, 7]

8. 답:

전체 유형 상관계수: 0.28957737349464524
ISTJ 유형 상관계수: -0.47374633610135236

9. 중간 출력

전체 주당 근로시간: [84, 45, 43, 60, 36, 41, 43, 44, 40, 39, 42, 41, 41, 87, 44, 49, 46, 34, 38, 90]
ISTJ 주당 근로시간: [84, 60, 87, 90]
전체 점수 (총평 등급): [0, 9, 8, 7, 10, 9, 9, 10, 9, 9, 8, 8, 9, 0, 8, 7, 9, 9, 10, 0]
ISTJ 점수 (총평 등급): [0, 7, 0, 0]

9. 답:

전체 유형 상관계수: -0.9703497691694142
ISTJ 유형 상관계수: -0.9839346980566549

10. 중간 출력

전체 주당 근로시간: [84, 45, 43, 60, 36, 41, 43, 44, 40, 39, 42, 41, 41, 87, 44, 49, 46, 34, 38, 90]
ISTJ 주당 근로시간: [84, 60, 87, 90]
전체 점수 (협업 및 커뮤니케이션): [0, 6, 8, 4, 10, 9, 7, 10, 9, 10, 7, 6, 6, 0, 10, 7, 9, 10, 9, 0]
ISTJ 점수 (협업 및 커뮤니케이션): [0, 4, 0, 0]

10. 답:

전체 유형 상관계수: -0.9234850686169522
ISTJ 유형 상관계수: -0.9839346980566548
```
