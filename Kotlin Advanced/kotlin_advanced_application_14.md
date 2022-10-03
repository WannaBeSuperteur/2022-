## 요구사항 분석
워너비슈퍼추어소프트에서는 사용자가 특정 기간에 주식에 투자한 기록에 근거하여, 해당 사용자의 손익을 계산하려고 한다. (단, 매매 수수료는 없다.)

각 항목은 다음의 정보를 포함한다.
* 데이터 일자: ```2022-09-30 23:59:59``` 형식
* 종목 이름
* 종목 코드 (실제 종목 코드 형식과 무관하며 임의의 형식으로 작성)
* 매수/매도 구분
* 주수
* 1주당 가격

손익은 다음 값들의 합산이다.
* 각 종목별 (사용자의 매도 수익) - (사용자의 매수 금액) 의 합
* 사용자가 보유한 각 종목별로, 해당 종목의 투자 기록상 가장 나중 기록을 기준으로 한 가격에 근거한 현재 가치의 총합

단, 처음에는 어떤 종목도 보유하고 있지 않다고 가정한다.

```데이터 클래스```와 같은 형식으로 데이터가 주어졌을 때, 해당 데이터에 의한 손익을 계산하시오.

### 데이터 클래스
```kotlin
// 주식 거래 기록
data class InvestLog (
    val date: String, // 데이터 일자 ("2022-09-30 23:59:59" 형식)
    val name: String, // 종목 이름
    val code: String, // 종목 코드
    val isBuy: Boolean, // 매수/매도 구분 (매수이면 true, 매도이면 false)
    val cnt: Int, // 주수
    val price: Int // 1주당 가격
)
```

## 코드 및 결과
### 코드
```kotlin
// 주식 거래 기록
data class InvestLog (
    val date: String, // 데이터 일자 ("2022-09-30 23:59:59" 형식)
    val name: String, // 종목 이름
    val code: String, // 종목 코드
    val isBuy: Boolean, // 매수/매도 구분 (매수이면 true, 매도이면 false)
    val cnt: Int, // 주수
    val price: Int // 1주당 가격
)

// 손익 계산 함수
fun computeProfit(investLog: List<InvestLog>): Int {
    var result = 0
    
    // 각 종목별로 그룹화 및 계산
    val logGroups = investLog.groupBy {
        it.code
    }
    
    logGroups.forEach { group ->
        println("\ngroup name = ${group.key}")
        group.value.forEach {
            println("1. ${it}")
        }
        
        // 매수 비용 총합
        val buySum = group.value.filter {
            it.isBuy == true
        }.sumOf {
            it.cnt * it.price
        }
         
        // 매도 수익 총합
        val sellSum = group.value.filter {
            it.isBuy == false
        }.sumOf {
            it.cnt * it.price
        }
         
        // 현재 보유 가치 총합
        val buyCount = group.value.filter { // 매수주수 총합
            it.isBuy == true
        }.sumOf {
            it.cnt
        }
        val sellCount = group.value.filter { // 매도주수 총합
            it.isBuy == false
        }.sumOf {
            it.cnt
        }
        val currentCount = buyCount - sellCount // 현재 보유주수
        
        val maxDate = group.value.maxOf { // 가장 최근 기록
            it.date
        }
        val currentPrice = group.value.find { // 현재 가격
            it.date == maxDate
        }!!.price
        
        val current = currentCount * currentPrice
        println("매수 총합 : ${buySum}, 매도 총합 : ${sellSum}, 현재 보유주수: ${currentCount}, 현재 보유가치 총합: ${current}")
        
        // 종목 손익
        val profit = current - buySum + sellSum
        result += profit
        println("종목 손익 : ${profit}")
    }
    
    return result
}

fun main() {
    val investLog = listOf<InvestLog> (
        InvestLog("2022-08-13 13:59:57", "코틀린", "KTLN", true, 13, 10000),
        InvestLog("2022-08-16 00:13:28", "코틀린", "KTLN", false, 5, 8500),
        InvestLog("2022-08-15 15:00:44", "자바", "JAVA", true, 4, 2750),
        InvestLog("2022-08-12 12:51:45", "자바", "JAVA", true, 10, 2000),
        InvestLog("2022-08-12 12:00:39", "코틀린", "KTLN", true, 10, 9500),
        InvestLog("2022-08-14 07:30:00", "자바", "JAVA", false, 6, 2250),
        InvestLog("2022-08-13 19:35:40", "HTML", "HTML", true, 15, 7500),
        InvestLog("2022-08-13 20:59:18", "파이썬", "PYTN", false, 24, 32000),
        InvestLog("2022-08-13 19:00:22", "코틀린", "KTLN", false, 20, 11000),
        InvestLog("2022-08-14 17:17:16", "자연어처리", "NLP", true, 10, 22000),
        InvestLog("2022-08-14 10:18:29", "머신러닝", "ML", true, 16, 42500),
        InvestLog("2022-08-16 23:45:00", "파이썬", "PYTN", true, 13, 27000),
        InvestLog("2022-08-15 09:07:25", "HTML", "HTML", false, 10, 9000),
        InvestLog("2022-08-12 17:10:28", "자바", "JAVA", true, 2, 1750),
        InvestLog("2022-08-13 11:23:45", "파이썬", "PYTN", true, 30, 30000),
        InvestLog("2022-08-14 00:07:10", "코틀린", "KTLN", true, 12, 9000),
        InvestLog("2022-08-14 09:28:14", "머신러닝", "ML", true, 20, 43500),
        InvestLog("2022-08-14 21:23:48", "자연어처리", "NLP", true, 20, 25000)
    )
    
    // 손익 계산
    println("\n손익 계산 결과 : ${computeProfit(investLog)}")
}
```

## 결과
```kotlin

group name = KTLN
1. InvestLog(date=2022-08-13 13:59:57, name=코틀린, code=KTLN, isBuy=true, cnt=13, price=10000)
1. InvestLog(date=2022-08-16 00:13:28, name=코틀린, code=KTLN, isBuy=false, cnt=5, price=8500)
1. InvestLog(date=2022-08-12 12:00:39, name=코틀린, code=KTLN, isBuy=true, cnt=10, price=9500)
1. InvestLog(date=2022-08-13 19:00:22, name=코틀린, code=KTLN, isBuy=false, cnt=20, price=11000)
1. InvestLog(date=2022-08-14 00:07:10, name=코틀린, code=KTLN, isBuy=true, cnt=12, price=9000)
매수 총합 : 333000, 매도 총합 : 262500, 현재 보유주수: 10, 현재 보유가치 총합: 85000
종목 손익 : 14500

group name = JAVA
1. InvestLog(date=2022-08-15 15:00:44, name=자바, code=JAVA, isBuy=true, cnt=4, price=2750)
1. InvestLog(date=2022-08-12 12:51:45, name=자바, code=JAVA, isBuy=true, cnt=10, price=2000)
1. InvestLog(date=2022-08-14 07:30:00, name=자바, code=JAVA, isBuy=false, cnt=6, price=2250)
1. InvestLog(date=2022-08-12 17:10:28, name=자바, code=JAVA, isBuy=true, cnt=2, price=1750)
매수 총합 : 34500, 매도 총합 : 13500, 현재 보유주수: 10, 현재 보유가치 총합: 27500
종목 손익 : 6500

group name = HTML
1. InvestLog(date=2022-08-13 19:35:40, name=HTML, code=HTML, isBuy=true, cnt=15, price=7500)
1. InvestLog(date=2022-08-15 09:07:25, name=HTML, code=HTML, isBuy=false, cnt=10, price=9000)
매수 총합 : 112500, 매도 총합 : 90000, 현재 보유주수: 5, 현재 보유가치 총합: 45000
종목 손익 : 22500

group name = PYTN
1. InvestLog(date=2022-08-13 20:59:18, name=파이썬, code=PYTN, isBuy=false, cnt=24, price=32000)
1. InvestLog(date=2022-08-16 23:45:00, name=파이썬, code=PYTN, isBuy=true, cnt=13, price=27000)
1. InvestLog(date=2022-08-13 11:23:45, name=파이썬, code=PYTN, isBuy=true, cnt=30, price=30000)
매수 총합 : 1251000, 매도 총합 : 768000, 현재 보유주수: 19, 현재 보유가치 총합: 513000
종목 손익 : 30000

group name = NLP
1. InvestLog(date=2022-08-14 17:17:16, name=자연어처리, code=NLP, isBuy=true, cnt=10, price=22000)
1. InvestLog(date=2022-08-14 21:23:48, name=자연어처리, code=NLP, isBuy=true, cnt=20, price=25000)
매수 총합 : 720000, 매도 총합 : 0, 현재 보유주수: 30, 현재 보유가치 총합: 750000
종목 손익 : 30000

group name = ML
1. InvestLog(date=2022-08-14 10:18:29, name=머신러닝, code=ML, isBuy=true, cnt=16, price=42500)
1. InvestLog(date=2022-08-14 09:28:14, name=머신러닝, code=ML, isBuy=true, cnt=20, price=43500)
매수 총합 : 1550000, 매도 총합 : 0, 현재 보유주수: 36, 현재 보유가치 총합: 1530000
종목 손익 : -20000

손익 계산 결과 : 83500
```
