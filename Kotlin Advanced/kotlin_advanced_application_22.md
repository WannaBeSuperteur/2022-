## 요구사항 분석
워너비슈퍼추어소프트에서는 코딩 테스트 학습 목표 달성 점검 앱을 개발한다. 이 앱에는 전체 기간 동안의 코딩 테스트 학습 목표 달성 점수를 보여주는 기능이 있다. 전체 기간 목표 달성 점수는 다음과 같이 계산한다.
* 전체 목표 달성 기록을 각 달별로 구분한다.
* 각 달별로 구분된 목표 달성 기록을 다시 각 날짜별로 구분하고, 각 날짜별 해당 목표의 목표량(코딩 테스트 문제 개수) 및 달성률에 따라 다음과 같이 판정한다.
  * 목표량이 ```5문항 이상```인 경우
    * 달성률 ```80%``` 이상 : ```10점```
    * 달성률 ```60%``` 이상 ```80%``` 미만 : ```6점```
    * 달성률 ```60%``` 미만 : ```0점```
  * 목표량이 ```2~4문항```인 경우
    * 달성률 ```100%```인 경우 : ```2문항```, ```3문항```, ```4문항``` 각각 ```3점```, ```5점```, ```7점```
    * 달성률 ```0%``` 초과 ```100% 미만```인 경우 : 목표 문항 수와 관계없이 ```1점```
    * 달성률 ```0%```인 경우 : 목표 문항 수와 관계없이 ```0점```
  * 목표량이 ```1문항```인 경우
    * 달성 시 ```2점```, 미달성 시 ```0점```
* 각 달별 총점은 해당월의 각 날짜별 판정 점수의 총합을 기본 점수로 하며, 다음과 같이 가산점을 준다. (각 가산점은 중복 가능)
  * ```10점``` 달성일이 ```7일``` 이상인 경우 : ```30점``` 가산점
  * ```6점 이상``` 달성일이 ```15일``` 이상인 경우 : ```20점``` 가산점
  * ```3점 이상``` 달성일이 ```20일``` 이상인 경우 : ```10점``` 가산점
  * ```1점 이상``` 달성일이 ```25일``` 이상인 경우 : ```5점``` 가산점
* 전체 기간 동안의 달성 점수는 월별 총점 중 상위 5개 기록의 평균으로 한다. (단, 기록이 5개 미만일 시 전체 기록의 평균)

데이터 클래스가 다음과 같을 때, 전체 기간 동안의 코딩 테스트 학습 목표 달성 점수를 보여주는 기능을 구현하시오.

### 데이터 클래스
```kotlin
// 목표 달성 기록 정보
data class CodingStudyRecord (
    val date: String, // "2022-10-01" 형식
    val goal: Int, // 목표 문항 수
    val achieved: Int // 실제 달성한 문항 수
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 목표 달성 기록 정보
data class CodingStudyRecord (
    val date: String, // "2022-10-01" 형식
    val goal: Int, // 목표 문항 수
    val achieved: Int // 실제 달성한 문항 수
)

// 월별 목표 달성 점수 계산
fun computeMonthlyScore(records: List<CodingStudyRecord>): Int {
    val results = records.map {
        val result = when (it.goal) {
            1 -> {
                if (it.achieved == 1) 2
                else 0
            }
            in 2..4 -> {
                if (it.achieved == it.goal) it.goal * 2 - 1
                else if (it.achieved > 0) 1
                else 0
            }
            else -> { // 5문항 이상
                if (it.achieved >= it.goal * 0.8) 10
                else if (it.achieved >= it.goal * 0.6) 6
                else 0
            }
        }
        println("${it.date} -> ${it.achieved}/${it.goal}, score = ${result}")
        result
    }
    
    // 기본 점수
    val base = results.sum()
    
    // 10점 달성일이 7일 이상인 경우 : 30점 가산점
    // 6점 이상 달성일이 15일 이상인 경우 : 20점 가산점
    // 3점 이상 달성일이 20일 이상인 경우 : 10점 가산점
    // 1점 이상 달성일이 25일 이상인 경우 : 5점 가산점
    val bonus0 = if (results.count { it >= 10 } >= 7) { 30 } else { 0 }
    val bonus1 = if (results.count { it >= 6 } >= 15) { 20 } else { 0 }
    val bonus2 = if (results.count { it >= 3 } >= 20) { 10 } else { 0 }
    val bonus3 = if (results.count { it >= 1 } >= 25) { 5 } else { 0 }
    
    println("base: ${base}, bonus: ${bonus0}, ${bonus1}, ${bonus2}, ${bonus3}")
    return base + bonus0 + bonus1 + bonus2 + bonus3
}

// 전체 기간의 목표 달성 점수 계산
fun computeTotalScore(records: List<CodingStudyRecord>): Int {
    
    // 월별로 나누기
    val groupByMonth = records.groupBy {
        it.date.substringBeforeLast("-")
    }
    val monthlyScores = groupByMonth.map {
        println("${it.key} : ${it.value}")
        
        val monthlyScore = computeMonthlyScore(it.value)
        println("monthly total score: ${monthlyScore}\n")
        monthlyScore
    }
    println("monthly scores : ${monthlyScores}")
    
    // 월별 TOP 5 구하기
    val monthlyScoresTop5 = monthlyScores.sortedDescending().take(5)
    println("monthly scores top 5 : ${monthlyScoresTop5}")
    return monthlyScoresTop5.sum() / monthlyScoresTop5.size
}

fun main() {
    
    // 임시 목표 달성 기록
    val tempCodingStudyRecords = listOf<CodingStudyRecord> (
        CodingStudyRecord("2022-05-04", 1, 0),
        CodingStudyRecord("2022-05-05", 4, 3),
        CodingStudyRecord("2022-05-06", 3, 2),
        CodingStudyRecord("2022-05-07", 4, 0),
        CodingStudyRecord("2022-05-13", 4, 2),
        CodingStudyRecord("2022-05-14", 7, 0),
        CodingStudyRecord("2022-05-15", 2, 1),
        CodingStudyRecord("2022-05-16", 5, 0),
        CodingStudyRecord("2022-05-27", 5, 4),
        CodingStudyRecord("2022-05-28", 7, 3),
        CodingStudyRecord("2022-05-29", 4, 2),
        CodingStudyRecord("2022-05-30", 1, 1),
        CodingStudyRecord("2022-06-01", 2, 1),
        CodingStudyRecord("2022-06-02", 5, 4),
        CodingStudyRecord("2022-06-03", 1, 0),
        CodingStudyRecord("2022-06-05", 7, 6),
        CodingStudyRecord("2022-06-15", 10, 10),
        CodingStudyRecord("2022-06-16", 8, 8),
        CodingStudyRecord("2022-06-18", 8, 7),
        CodingStudyRecord("2022-06-21", 6, 6),
        CodingStudyRecord("2022-06-22", 8, 7),
        CodingStudyRecord("2022-06-23", 6, 2),
        CodingStudyRecord("2022-06-28", 6, 4),
        CodingStudyRecord("2022-07-01", 5, 4),
        CodingStudyRecord("2022-07-04", 13, 11),
        CodingStudyRecord("2022-07-05", 10, 9),
        CodingStudyRecord("2022-07-06", 10, 9),
        CodingStudyRecord("2022-07-09", 4, 4),
        CodingStudyRecord("2022-07-10", 3, 3),
        CodingStudyRecord("2022-07-11", 4, 4),
        CodingStudyRecord("2022-07-12", 4, 4),
        CodingStudyRecord("2022-07-13", 5, 3),
        CodingStudyRecord("2022-07-16", 6, 4),
        CodingStudyRecord("2022-07-17", 5, 2),
        CodingStudyRecord("2022-07-21", 6, 5),
        CodingStudyRecord("2022-07-22", 5, 5),
        CodingStudyRecord("2022-07-23", 5, 5),
        CodingStudyRecord("2022-07-24", 6, 4),
        CodingStudyRecord("2022-07-25", 7, 5),
        CodingStudyRecord("2022-07-27", 5, 5),
        CodingStudyRecord("2022-07-28", 5, 3),
        CodingStudyRecord("2022-07-30", 4, 4),
        CodingStudyRecord("2022-07-31", 2, 2),
        CodingStudyRecord("2022-08-13", 4, 4),
        CodingStudyRecord("2022-08-14", 4, 4),
        CodingStudyRecord("2022-08-16", 4, 4),
        CodingStudyRecord("2022-08-17", 5, 3),
        CodingStudyRecord("2022-08-18", 5, 3),
        CodingStudyRecord("2022-08-20", 6, 4),
        CodingStudyRecord("2022-08-22", 6, 5),
        CodingStudyRecord("2022-08-26", 4, 2),
        CodingStudyRecord("2022-08-28", 3, 1),
        CodingStudyRecord("2022-09-14", 1, 0),
        CodingStudyRecord("2022-09-15", 2, 0),
        CodingStudyRecord("2022-10-03", 3, 1),
        CodingStudyRecord("2022-10-07", 3, 3)
    )
    
    println("전체 기간 점수: ${computeTotalScore(tempCodingStudyRecords)}")
}
```

### 실행 결과
```kotlin
2022-05 : [CodingStudyRecord(date=2022-05-04, goal=1, achieved=0), CodingStudyRecord(date=2022-05-05, goal=4, achieved=3), CodingStudyRecord(date=2022-05-06, goal=3, achieved=2), CodingStudyRecord(date=2022-05-07, goal=4, achieved=0), CodingStudyRecord(date=2022-05-13, goal=4, achieved=2), CodingStudyRecord(date=2022-05-14, goal=7, achieved=0), CodingStudyRecord(date=2022-05-15, goal=2, achieved=1), CodingStudyRecord(date=2022-05-16, goal=5, achieved=0), CodingStudyRecord(date=2022-05-27, goal=5, achieved=4), CodingStudyRecord(date=2022-05-28, goal=7, achieved=3), CodingStudyRecord(date=2022-05-29, goal=4, achieved=2), CodingStudyRecord(date=2022-05-30, goal=1, achieved=1)]
2022-05-04 -> 0/1, score = 0
2022-05-05 -> 3/4, score = 1
2022-05-06 -> 2/3, score = 1
2022-05-07 -> 0/4, score = 0
2022-05-13 -> 2/4, score = 1
2022-05-14 -> 0/7, score = 0
2022-05-15 -> 1/2, score = 1
2022-05-16 -> 0/5, score = 0
2022-05-27 -> 4/5, score = 10
2022-05-28 -> 3/7, score = 0
2022-05-29 -> 2/4, score = 1
2022-05-30 -> 1/1, score = 2
base: 17, bonus: 0, 0, 0, 0
monthly total score: 17

2022-06 : [CodingStudyRecord(date=2022-06-01, goal=2, achieved=1), CodingStudyRecord(date=2022-06-02, goal=5, achieved=4), CodingStudyRecord(date=2022-06-03, goal=1, achieved=0), CodingStudyRecord(date=2022-06-05, goal=7, achieved=6), CodingStudyRecord(date=2022-06-15, goal=10, achieved=10), CodingStudyRecord(date=2022-06-16, goal=8, achieved=8), CodingStudyRecord(date=2022-06-18, goal=8, achieved=7), CodingStudyRecord(date=2022-06-21, goal=6, achieved=6), CodingStudyRecord(date=2022-06-22, goal=8, achieved=7), CodingStudyRecord(date=2022-06-23, goal=6, achieved=2), CodingStudyRecord(date=2022-06-28, goal=6, achieved=4)]
2022-06-01 -> 1/2, score = 1
2022-06-02 -> 4/5, score = 10
2022-06-03 -> 0/1, score = 0
2022-06-05 -> 6/7, score = 10
2022-06-15 -> 10/10, score = 10
2022-06-16 -> 8/8, score = 10
2022-06-18 -> 7/8, score = 10
2022-06-21 -> 6/6, score = 10
2022-06-22 -> 7/8, score = 10
2022-06-23 -> 2/6, score = 0
2022-06-28 -> 4/6, score = 6
base: 77, bonus: 30, 0, 0, 0
monthly total score: 107

2022-07 : [CodingStudyRecord(date=2022-07-01, goal=5, achieved=4), CodingStudyRecord(date=2022-07-04, goal=13, achieved=11), CodingStudyRecord(date=2022-07-05, goal=10, achieved=9), CodingStudyRecord(date=2022-07-06, goal=10, achieved=9), CodingStudyRecord(date=2022-07-09, goal=4, achieved=4), CodingStudyRecord(date=2022-07-10, goal=3, achieved=3), CodingStudyRecord(date=2022-07-11, goal=4, achieved=4), CodingStudyRecord(date=2022-07-12, goal=4, achieved=4), CodingStudyRecord(date=2022-07-13, goal=5, achieved=3), CodingStudyRecord(date=2022-07-16, goal=6, achieved=4), CodingStudyRecord(date=2022-07-17, goal=5, achieved=2), CodingStudyRecord(date=2022-07-21, goal=6, achieved=5), CodingStudyRecord(date=2022-07-22, goal=5, achieved=5), CodingStudyRecord(date=2022-07-23, goal=5, achieved=5), CodingStudyRecord(date=2022-07-24, goal=6, achieved=4), CodingStudyRecord(date=2022-07-25, goal=7, achieved=5), CodingStudyRecord(date=2022-07-27, goal=5, achieved=5), CodingStudyRecord(date=2022-07-28, goal=5, achieved=3), CodingStudyRecord(date=2022-07-30, goal=4, achieved=4), CodingStudyRecord(date=2022-07-31, goal=2, achieved=2)]
2022-07-01 -> 4/5, score = 10
2022-07-04 -> 11/13, score = 10
2022-07-05 -> 9/10, score = 10
2022-07-06 -> 9/10, score = 10
2022-07-09 -> 4/4, score = 7
2022-07-10 -> 3/3, score = 5
2022-07-11 -> 4/4, score = 7
2022-07-12 -> 4/4, score = 7
2022-07-13 -> 3/5, score = 6
2022-07-16 -> 4/6, score = 6
2022-07-17 -> 2/5, score = 0
2022-07-21 -> 5/6, score = 10
2022-07-22 -> 5/5, score = 10
2022-07-23 -> 5/5, score = 10
2022-07-24 -> 4/6, score = 6
2022-07-25 -> 5/7, score = 6
2022-07-27 -> 5/5, score = 10
2022-07-28 -> 3/5, score = 6
2022-07-30 -> 4/4, score = 7
2022-07-31 -> 2/2, score = 3
base: 146, bonus: 30, 20, 0, 0
monthly total score: 196

2022-08 : [CodingStudyRecord(date=2022-08-13, goal=4, achieved=4), CodingStudyRecord(date=2022-08-14, goal=4, achieved=4), CodingStudyRecord(date=2022-08-16, goal=4, achieved=4), CodingStudyRecord(date=2022-08-17, goal=5, achieved=3), CodingStudyRecord(date=2022-08-18, goal=5, achieved=3), CodingStudyRecord(date=2022-08-20, goal=6, achieved=4), CodingStudyRecord(date=2022-08-22, goal=6, achieved=5), CodingStudyRecord(date=2022-08-26, goal=4, achieved=2), CodingStudyRecord(date=2022-08-28, goal=3, achieved=1)]
2022-08-13 -> 4/4, score = 7
2022-08-14 -> 4/4, score = 7
2022-08-16 -> 4/4, score = 7
2022-08-17 -> 3/5, score = 6
2022-08-18 -> 3/5, score = 6
2022-08-20 -> 4/6, score = 6
2022-08-22 -> 5/6, score = 10
2022-08-26 -> 2/4, score = 1
2022-08-28 -> 1/3, score = 1
base: 51, bonus: 0, 0, 0, 0
monthly total score: 51

2022-09 : [CodingStudyRecord(date=2022-09-14, goal=1, achieved=0), CodingStudyRecord(date=2022-09-15, goal=2, achieved=0)]
2022-09-14 -> 0/1, score = 0
2022-09-15 -> 0/2, score = 0
base: 0, bonus: 0, 0, 0, 0
monthly total score: 0

2022-10 : [CodingStudyRecord(date=2022-10-03, goal=3, achieved=1), CodingStudyRecord(date=2022-10-07, goal=3, achieved=3)]
2022-10-03 -> 1/3, score = 1
2022-10-07 -> 3/3, score = 5
base: 6, bonus: 0, 0, 0, 0
monthly total score: 6

monthly scores : [17, 107, 196, 51, 0, 6]
monthly scores top 5 : [196, 107, 51, 17, 6]
전체 기간 점수: 75
```
