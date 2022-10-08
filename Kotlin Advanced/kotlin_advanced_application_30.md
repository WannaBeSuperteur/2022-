## 요구사항 분석
워너비슈퍼추어소프트에서 개발 중인 금융 앱 워너비파이낸스에서는 특정한 두 종목의 가격을 비교하는 기능이 있다. 세부적으로는 다음과 같다.
* 두 종목의 최근 240 거래일간의 주가 비교 및 이들의 주가의 연결선의 교차 지점 표시
* 두 종목 각각에 대해, 최근 A 거래일 평균 주가의 연결선과 최근 B 거래일 평균 주가의 연결선의 교차 지점 표시

아래 **데이터 클래스**와 같은 형식의 데이터를 이용하여 위 기능을 구현하시오.

### 데이터 클래스
```kotlin
// 주가 데이터
data class TwoStocks (
    val stock_1: Stock, // 종목 1
    val stock_2: Stock // 종목 2
)

// 종목 (최근 A일간 평균 가격 연결선과 B일간 평균 가격 연결선 비교)
data class Stock (
    val price: List<Int>, // 주가 (최근 240일이지만 일부 생략됨)
    val compare_recent_1: Int, // 최근 A일 평균선
    val compare_recent_2: Int // 최근 B일 평균선
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 주가 데이터
data class TwoStocks (
    val stock_1: Stock, // 종목 1
    val stock_2: Stock // 종목 2
)

// 종목 (최근 A일간 평균 가격 연결선과 B일간 평균 가격 연결선 비교)
data class Stock (
    val code: String, // 주식 코드
    val name: String, // 주식 이름
    val price: List<Int>, // 주가 (최근 240일이지만 일부 생략됨)
    val compare_recent_1: Int, // 최근 A일 평균선
    val compare_recent_2: Int // 최근 B일 평균선
)

// 최근 A 거래일 평균 주가의 연결선과 최근 B 거래일 평균 주가의 연결선의 교차 지점 표시
fun findCrossOfAverage(stock: Stock) {
    val A = stock.compare_recent_1
    val B = stock.compare_recent_2
    println("\n <<< 종목 ${stock.name}의 최근 ${A} 거래일 평균 주가와 ${B} 거래일 평균 주가의 교차점 >>>\n")
    
    // 평균 주가 계산
    val n = stock.price.size
    val avgPriceA = (0 until n - A + 1).map {
        stock.price.subList(it, it + A).sum() / A
    }
    val avgPriceB = (0 until n - B + 1).map {
        stock.price.subList(it, it + B).sum() / B
    }
    
    println("${A}일간 평균 주가 추이 :")
    println(avgPriceA)
    println("${B}일간 평균 주가 추이 :")
    println(avgPriceB)
    
    // 평균 주가의 공통부분 맞추기
    val nCommon = Math.min(avgPriceA.size, avgPriceB.size)
    val avgPriceCommonA = avgPriceA.takeLast(nCommon)
    val avgPriceCommonB = avgPriceB.takeLast(nCommon)
    
    println("\n${A}일간 평균 주가 추이 (공통) :")
    println(avgPriceCommonA)
    println("${B}일간 평균 주가 추이 (공통) :")
    println(avgPriceCommonB)
    print("\n")
    
    // 교차 지점 표시
    val crossSurpass = (0 until nCommon-1).map {
        val beforeIsA = avgPriceCommonA[it] > avgPriceCommonB[it]
        val afterIsA = avgPriceCommonA[it + 1] > avgPriceCommonB[it + 1]
        
        if (beforeIsA && !afterIsA) "B"
        else if (!beforeIsA && afterIsA) "A"
        else ""
    }
    
    crossSurpass.forEachIndexed { idx, it ->
        val trueIdx = idx + n - nCommon
        
        if (it == "A") {
            println("거래일 ${trueIdx} ~ ${trueIdx + 1} : ${A}일간 평균 주가 추이가 ${B}일간 추이를 앞서감")
        } else if (it == "B") {
            println("거래일 ${trueIdx} ~ ${trueIdx + 1} : ${B}일간 평균 주가 추이가 ${A}일간 추이를 앞서감")
        }
    }
}

// 종목 데이터 비교
fun compareStocks(stock1: Stock, stock2: Stock) {
    
    // 각 종목의 주가 비교 및 연결선의 교차 지점 표시
    val n = stock1.price.size
    
    val crossDates = (0 until n-1).filter {
        val firstSurpass = stock1.price[it] < stock2.price[it] && stock1.price[it + 1] > stock2.price[it + 1]
        val secondSurpass = stock1.price[it] > stock2.price[it] && stock1.price[it + 1] < stock2.price[it + 1]
        firstSurpass || secondSurpass
    }
    
    // 기본 비교 정보 출력
    println(" <<< 기본 비교 정보 >>>\n")
    (0 until n).forEach { it ->
        val isFirstHigher = stock1.price[it] > stock2.price[it]
        val isSecondHigher = stock1.price[it] < stock2.price[it]
        
        if (isFirstHigher) {
            println("거래일 ${it} : ${stock1.name}이 ${stock1.price[it] - stock2.price[it]}만큼 앞섬")
        } else if (isSecondHigher) {
            println("거래일 ${it} : ${stock2.name}이 ${stock2.price[it] - stock1.price[it]}만큼 앞섬")
        } else {
            println("거래일 ${it} : ${stock1.name}과 ${stock2.name}의 주가가 서로 같음")
        }
    }
    
    // 주가의 연결선의 교차 지점 표시
    println("\n <<< 주가의 연결선의 교차 지점 >>>\n")
    var lastCrossedDate = 0
    var isSecondLastWinner = stock1.price[0] < stock2.price[0]
    
    crossDates.forEach { cd ->
        val secondSurpass = stock1.price[lastCrossedDate] > stock2.price[lastCrossedDate]
        
        if (secondSurpass) {
            println("${lastCrossedDate} ~ ${cd} : [${stock1.name}]이 앞서감")
            println("${cd} ~ ${cd + 1} : [${stock2.name}]이 앞서기 시작")
            isSecondLastWinner = true
        } else {
            println("${lastCrossedDate} ~ ${cd} : [${stock2.name}]이 앞서감")
            println("${cd} ~ ${cd + 1} : [${stock1.name}]이 앞서기 시작")
            isSecondLastWinner = false
        }
        lastCrossedDate = cd + 1
    }
    
    if (isSecondLastWinner) {
        println("${lastCrossedDate} ~ ${n-1} : [${stock2.name}]이 앞서감")
    } else {
        println("${lastCrossedDate} ~ ${n-1} : [${stock1.name}]이 앞서감")
    }
    
    // 각 종목별 최근 A 거래일 평균 주가의 연결선과 최근 B 거래일 평균 주가의 연결선의 교차 지점 표시
    findCrossOfAverage(stock1)
    findCrossOfAverage(stock2)
}

fun main() {
    
    // 임시 종목 데이터
    val tempTwoStocks = TwoStocks(
        Stock( // 종목 1 (코틀린소프트)
            "ABC200", "코틀린소프트",
            listOf(
                2400, 2700, 2550, 2650, 2850, 3000, 3100, 3050, 3100, 3300,
                3150, 3300, 3500, 3350, 3450, 3700, 3750, 3300, 2850, 2800,
                2750, 2750, 2800, 2800, 3000, 2750, 2600, 2550, 2500, 2450
            ),
            10, 3
        ),
        Stock( // 종목 2 (파이썬소프트)
            "ABC300", "파이썬소프트",
            listOf(
                1950, 2000, 1950, 2050, 2100, 2400, 2450, 2700, 2750, 2600,
                2450, 2400, 2400, 2700, 2650, 2600, 2550, 3000, 3100, 2900,
                2800, 2650, 2650, 2700, 2950, 3100, 3400, 3750, 4250, 4500
            ),
            2, 8
        )
    )
    
    // 종목 데이터 비교
    compareStocks(tempTwoStocks.stock_1, tempTwoStocks.stock_2)
}
```

### 실행 결과
```kotlin
 <<< 기본 비교 정보 >>>

거래일 0 : 코틀린소프트이 450만큼 앞섬
거래일 1 : 코틀린소프트이 700만큼 앞섬
거래일 2 : 코틀린소프트이 600만큼 앞섬
거래일 3 : 코틀린소프트이 600만큼 앞섬
거래일 4 : 코틀린소프트이 750만큼 앞섬
거래일 5 : 코틀린소프트이 600만큼 앞섬
거래일 6 : 코틀린소프트이 650만큼 앞섬
거래일 7 : 코틀린소프트이 350만큼 앞섬
거래일 8 : 코틀린소프트이 350만큼 앞섬
거래일 9 : 코틀린소프트이 700만큼 앞섬
거래일 10 : 코틀린소프트이 700만큼 앞섬
거래일 11 : 코틀린소프트이 900만큼 앞섬
거래일 12 : 코틀린소프트이 1100만큼 앞섬
거래일 13 : 코틀린소프트이 650만큼 앞섬
거래일 14 : 코틀린소프트이 800만큼 앞섬
거래일 15 : 코틀린소프트이 1100만큼 앞섬
거래일 16 : 코틀린소프트이 1200만큼 앞섬
거래일 17 : 코틀린소프트이 300만큼 앞섬
거래일 18 : 파이썬소프트이 250만큼 앞섬
거래일 19 : 파이썬소프트이 100만큼 앞섬
거래일 20 : 파이썬소프트이 50만큼 앞섬
거래일 21 : 코틀린소프트이 100만큼 앞섬
거래일 22 : 코틀린소프트이 150만큼 앞섬
거래일 23 : 코틀린소프트이 100만큼 앞섬
거래일 24 : 코틀린소프트이 50만큼 앞섬
거래일 25 : 파이썬소프트이 350만큼 앞섬
거래일 26 : 파이썬소프트이 800만큼 앞섬
거래일 27 : 파이썬소프트이 1200만큼 앞섬
거래일 28 : 파이썬소프트이 1750만큼 앞섬
거래일 29 : 파이썬소프트이 2050만큼 앞섬

 <<< 주가의 연결선의 교차 지점 >>>

0 ~ 17 : [코틀린소프트]이 앞서감
17 ~ 18 : [파이썬소프트]이 앞서기 시작
18 ~ 20 : [파이썬소프트]이 앞서감
20 ~ 21 : [코틀린소프트]이 앞서기 시작
21 ~ 24 : [코틀린소프트]이 앞서감
24 ~ 25 : [파이썬소프트]이 앞서기 시작
25 ~ 29 : [파이썬소프트]이 앞서감

 <<< 종목 코틀린소프트의 최근 10 거래일 평균 주가와 3 거래일 평균 주가의 교차점 >>>

10일간 평균 주가 추이 :
[2870, 2945, 3005, 3100, 3170, 3230, 3300, 3365, 3390, 3365, 3315, 3275, 3220, 3150, 3095, 3050, 2955, 2840, 2765, 2730, 2695]
3일간 평균 주가 추이 :
[2550, 2633, 2683, 2833, 2983, 3050, 3083, 3150, 3183, 3250, 3316, 3383, 3433, 3500, 3633, 3583, 3300, 2983, 2800, 2766, 2766, 2783, 2866, 2850, 2783, 2633, 2550, 2500]

10일간 평균 주가 추이 (공통) :
[2870, 2945, 3005, 3100, 3170, 3230, 3300, 3365, 3390, 3365, 3315, 3275, 3220, 3150, 3095, 3050, 2955, 2840, 2765, 2730, 2695]
3일간 평균 주가 추이 (공통) :
[3150, 3183, 3250, 3316, 3383, 3433, 3500, 3633, 3583, 3300, 2983, 2800, 2766, 2766, 2783, 2866, 2850, 2783, 2633, 2550, 2500]

거래일 17 ~ 18 : 10일간 평균 주가 추이가 3일간 추이를 앞서감

 <<< 종목 파이썬소프트의 최근 2 거래일 평균 주가와 8 거래일 평균 주가의 교차점 >>>

2일간 평균 주가 추이 :
[1975, 1975, 2000, 2075, 2250, 2425, 2575, 2725, 2675, 2525, 2425, 2400, 2550, 2675, 2625, 2575, 2775, 3050, 3000, 2850, 2725, 2650, 2675, 2825, 3025, 3250, 3575, 4000, 4375]
8일간 평균 주가 추이 :
[2200, 2300, 2375, 2437, 2481, 2518, 2556, 2581, 2568, 2543, 2593, 2675, 2737, 2787, 2781, 2781, 2793, 2843, 2856, 2893, 3000, 3181, 3412]

2일간 평균 주가 추이 (공통) :
[2575, 2725, 2675, 2525, 2425, 2400, 2550, 2675, 2625, 2575, 2775, 3050, 3000, 2850, 2725, 2650, 2675, 2825, 3025, 3250, 3575, 4000, 4375]
8일간 평균 주가 추이 (공통) :
[2200, 2300, 2375, 2437, 2481, 2518, 2556, 2581, 2568, 2543, 2593, 2675, 2737, 2787, 2781, 2781, 2793, 2843, 2856, 2893, 3000, 3181, 3412]

거래일 10 ~ 11 : 8일간 평균 주가 추이가 2일간 추이를 앞서감
거래일 13 ~ 14 : 2일간 평균 주가 추이가 8일간 추이를 앞서감
거래일 20 ~ 21 : 8일간 평균 주가 추이가 2일간 추이를 앞서감
거래일 24 ~ 25 : 2일간 평균 주가 추이가 8일간 추이를 앞서감
```
