## 요구사항 분석
워너비슈퍼추어소프트에서는 이번 달에 회의를 위해 ```KTLN 회의실``` 사용을 예약하려고 한다. 단, 이 회의실에는 다음과 같은 제약 조건이 있다.
* 회의실은 월요일부터 금요일까지, 오전 9시부터 오후 6시까지만 사용할 수 있다.
* 공휴일에는 회의실을 사용할 수 없다.
* 회의실에서는 동시에 1개의 회의만 진행할 수 있다. 단, 특정 회의의 종료 시각이 다른 회의의 시작 시각과 같을 수는 있다.
* ```조각```은 회의실이 비어 있어서, 회의실을 연속적으로 사용 가능한 시간대를 의미한다.
  * 예를 들어, 월요일 오전 10시부터 정오까지, 오후 4시부터 5시까지 회의가 있을 때, 해당 월요일 ```오전 9시 ~ 10시```, ```정오 ~ 오후 4시```, ```오후 5시 ~ 6시```가 각각 ```조각```이다.

이번 달의 ```KTLN 회의실``` 사용 가능 시간을 알기 위해, 회의실 예약 기록에 기반하여 각 날짜별로, 각 주간별로, 그리고 전체 기간에 대해 다음과 같은 정보를 구하시오. (단, 이번 달의 1일은 반드시 월요일)
* 전체 ```조각```의 개수
* 길이가 1시간 이상인 ```조각```의 개수
* ```조각```의 길이의 최댓값, 최솟값, 평균값

### 데이터 클래스
```kotlin
// 전체 정보
data class Info (
    val days: Int, // 이번 달의 날짜의 수 (28, 29, 30 또는 31)
    val holidays: List<Int>, // 공휴일 목록
    val reservations: List<Reservation> // 기존 예약 목록
)

// 회의실 예약 기록의 각 항목
data class Reservation (
    val date: Int, // 날짜 (1, 2, ..., 31)
    val start: String, // 회의 시작 시각 (09:30)
    val end: String, // 회의 종료 시각 (10:30)
)
```

## 코드 및 출력
### 코드
```kotlin
// 전체 정보
data class Info (
    val days: Int, // 이번 달의 날짜의 수 (28, 29, 30 또는 31)
    val holidays: List<Int>, // 공휴일 목록
    val reservations: List<Reservation> // 기존 예약 목록
)

// 회의실 예약 기록의 각 항목
data class Reservation (
    val date: Int, // 날짜 (1, 2, ..., 31)
    val start: String, // 회의 시작 시각 (09:30)
    val end: String, // 회의 종료 시각 (10:30)
)

// hh:mm -> 00:00으로부터 지난 시각으로 변환
fun hhmmToMinutes(hhmm: String): Int {
    val hm = hhmm.split(":")
    val h = hm[0].toInt()
    val m = hm[1].toInt()
    return h * 60 + m
}

// 날짜별, 주간별, 전체 기간별로 조각을 찾아야 하므로, 각 날짜의 조각 정보를 저장한 후 여러 번 사용하는 것이
// 각 날짜별, 주간별로 함수를 통해 여러 번 구하는 것보다 효율적임
// 전체 기간(한달) 중 각 날짜의 조각의 길이 정보 구하기
class Fragment (
    val date: Int,
    val length: Int // 단위: 분
)

fun findAllFragments(days: Int, holidays: List<Int>, reservations: List<Reservation>): List<Fragment> {
    val fragments = mutableListOf<Fragment>()
    
    // 각 날짜별로 조각 구하기
    for (d in 1..days) {
        
        // 주말 및 공휴일 제외
        if (d % 7 in listOf(0, 6) || d in holidays) {
            continue
        }
        
        // 해당 날짜의 조각만 구하기
        val dailyReservations = reservations.filter {
            it.date == d
        }
        val dailyRCnt = dailyReservations.size
        
        // 해당일의 회의가 없으면 09:00~18:00의 540분
        if (dailyReservations.isEmpty()) {
            fragments.add(Fragment(d, 9 * 60))
        }
        
        // 해당일의 회의가 있으면
        else {
            fragments.add(Fragment(d, hhmmToMinutes(dailyReservations[0].start) - 9 * 60))
            (0..dailyRCnt-2).forEach {
                val currentEndTimeInt = hhmmToMinutes(dailyReservations[it].end)
                val nextStartTimeInt = hhmmToMinutes(dailyReservations[it + 1].start)
                fragments.add(Fragment(d, nextStartTimeInt - currentEndTimeInt))
            }
            fragments.add(Fragment(d, 18 * 60 - hhmmToMinutes(dailyReservations[dailyRCnt-1].end)))
        }
    }
    
    // 길이가 0을 초과하는 조각만 반환
    return fragments.filter {
        it.length > 0
    }
}

// 전체 조각의 개수, 길이가 1시간 이상인 조각의 개수, 조각의 길이의 최댓값, 최솟값, 평균값 출력
fun printFragmentInfo(startDate: Int, endDate: Int, fragments: List<Fragment>) {
    
    // 대상 기간 출력 (startDate, endDate 변수는 여기서만 사용)
    println("기간 : ${startDate}일 ~ ${endDate}일")
    
    // 중간 출력
    println("조각 목록 :")
    fragments.forEach {
        println("날짜: ${it.date}, 조각 길이: ${it.length}")
    }

    // 전체 조각의 개수
    println("전체 조각의 개수: ${fragments.size}")
    
    // 길이가 1시간 이상인 조각의 개수
    val longFragmentCount = fragments.filter {
        it.length >= 60
    }.size
    println("1시간 이상인 조각의 개수: ${longFragmentCount}")
    
    // 조각의 길이의 최대, 최소, 평균값
    val maxLength = fragments.maxOf {
        it.length
    }
    val minLength = fragments.minOf {
        it.length
    }
    val averageLength = fragments.sumOf {
        it.length
    } / fragments.size
    
    println("조각 길이 최대: ${maxLength}, 최소: ${minLength}, 평균: ${averageLength}\n")
}

// 특정 기간의 조각 정보 출력
fun printFragmentInfoInPeriod(startDate: Int, endDate: Int, allFragments: List<Fragment>) {
    val fragments = allFragments.filter {
        it.date >= startDate && it.date <= endDate
    }
    if (fragments.size > 0) {
        printFragmentInfo(startDate, endDate, fragments)
    }
}

// 조각 정보 출력
fun printInfo(info: Info) {
    
    // 조각 정보 정렬 (회의 시간 중복이 불가능하므로 start, end 중 아무것이나 기준으로 정렬)
    println("시간순 정렬된 조각 정보:")
    val sortedReservations = info.reservations.sortedBy { it.start }
    sortedReservations.forEach {
        println(it)
    }
    print("\n")
    
    // 전체 조각 찾기
    val allFragments = findAllFragments(info.days, info.holidays, sortedReservations)
    
    // 날짜별 조각 정보 출력
    for (i in 1..info.days) {
        printFragmentInfoInPeriod(i, i, allFragments)
    }
    
    // 주간 조각 정보 출력
    for (i in 1..info.days step 7) {
        printFragmentInfoInPeriod(i, Math.min(i + 4, info.days), allFragments)
    }
    
    // 전체 조각 정보 출력
    printFragmentInfoInPeriod(1, info.days, allFragments)
}

fun main() {
    val info = Info(
        days = 31,
        holidays = listOf<Int>(3, 6, 7, 8, 9, 10),
        reservations = listOf<Reservation>(
            Reservation(1, "09:30", "10:45"),
            Reservation(29, "14:00", "18:00"),
            Reservation(31, "09:00", "10:00"),
            Reservation(4, "12:55", "17:15"),
            Reservation(1, "11:00", "12:00"),
            Reservation(18, "13:00", "16:45"),
            Reservation(24, "09:45", "11:00"),
            Reservation(5, "14:55", "15:30"),
            Reservation(11, "09:00", "11:00"),
            Reservation(11, "14:00", "17:00"),
            Reservation(19, "13:00", "15:50"),
            Reservation(31, "11:00", "12:00"),
            Reservation(23, "09:45", "11:00"),
            Reservation(12, "14:00", "17:00"),
            Reservation(24, "11:50", "12:20"),
            Reservation(2, "12:00", "16:50"),
            Reservation(4, "09:55", "12:00"),
            Reservation(17, "13:40", "16:45"),
            Reservation(18, "09:45", "11:00"),
            Reservation(23, "11:50", "12:20"),
            Reservation(19, "09:45", "11:00"),
            Reservation(1, "14:10", "15:00"),
            Reservation(24, "13:00", "16:15"),
            Reservation(31, "13:00", "14:00"),
            Reservation(31, "16:00", "16:50"),
            Reservation(26, "11:50", "12:40"),
            Reservation(16, "12:30", "16:30"),
            Reservation(5, "13:10", "14:00"),
            Reservation(17, "09:45", "11:00"),
            Reservation(23, "13:30", "16:45"),
            Reservation(22, "11:50", "12:20")
        )
    )
    
    // 정보 출력
    printInfo(info)
}
```

### 출력
```kotlin
시간순 정렬된 조각 정보:
Reservation(date=31, start=09:00, end=10:00)
Reservation(date=11, start=09:00, end=11:00)
Reservation(date=1, start=09:30, end=10:45)
Reservation(date=24, start=09:45, end=11:00)
Reservation(date=23, start=09:45, end=11:00)
Reservation(date=18, start=09:45, end=11:00)
Reservation(date=19, start=09:45, end=11:00)
Reservation(date=17, start=09:45, end=11:00)
Reservation(date=4, start=09:55, end=12:00)
Reservation(date=1, start=11:00, end=12:00)
Reservation(date=31, start=11:00, end=12:00)
Reservation(date=24, start=11:50, end=12:20)
Reservation(date=23, start=11:50, end=12:20)
Reservation(date=26, start=11:50, end=12:40)
Reservation(date=22, start=11:50, end=12:20)
Reservation(date=2, start=12:00, end=16:50)
Reservation(date=16, start=12:30, end=16:30)
Reservation(date=4, start=12:55, end=17:15)
Reservation(date=18, start=13:00, end=16:45)
Reservation(date=19, start=13:00, end=15:50)
Reservation(date=24, start=13:00, end=16:15)
Reservation(date=31, start=13:00, end=14:00)
Reservation(date=5, start=13:10, end=14:00)
Reservation(date=23, start=13:30, end=16:45)
Reservation(date=17, start=13:40, end=16:45)
Reservation(date=29, start=14:00, end=18:00)
Reservation(date=11, start=14:00, end=17:00)
Reservation(date=12, start=14:00, end=17:00)
Reservation(date=1, start=14:10, end=15:00)
Reservation(date=5, start=14:55, end=15:30)
Reservation(date=31, start=16:00, end=16:50)

기간 : 1일 ~ 1일
조각 목록 :
날짜: 1, 조각 길이: 30
날짜: 1, 조각 길이: 15
날짜: 1, 조각 길이: 130
날짜: 1, 조각 길이: 180
전체 조각의 개수: 4
1시간 이상인 조각의 개수: 2
조각 길이 최대: 180, 최소: 15, 평균: 88

기간 : 2일 ~ 2일
조각 목록 :
날짜: 2, 조각 길이: 180
날짜: 2, 조각 길이: 70
전체 조각의 개수: 2
1시간 이상인 조각의 개수: 2
조각 길이 최대: 180, 최소: 70, 평균: 125

기간 : 4일 ~ 4일
조각 목록 :
날짜: 4, 조각 길이: 55
날짜: 4, 조각 길이: 55
날짜: 4, 조각 길이: 45
전체 조각의 개수: 3
1시간 이상인 조각의 개수: 0
조각 길이 최대: 55, 최소: 45, 평균: 51

기간 : 5일 ~ 5일
조각 목록 :
날짜: 5, 조각 길이: 250
날짜: 5, 조각 길이: 55
날짜: 5, 조각 길이: 150
전체 조각의 개수: 3
1시간 이상인 조각의 개수: 2
조각 길이 최대: 250, 최소: 55, 평균: 151

기간 : 11일 ~ 11일
조각 목록 :
날짜: 11, 조각 길이: 180
날짜: 11, 조각 길이: 60
전체 조각의 개수: 2
1시간 이상인 조각의 개수: 2
조각 길이 최대: 180, 최소: 60, 평균: 120

기간 : 12일 ~ 12일
조각 목록 :
날짜: 12, 조각 길이: 300
날짜: 12, 조각 길이: 60
전체 조각의 개수: 2
1시간 이상인 조각의 개수: 2
조각 길이 최대: 300, 최소: 60, 평균: 180

기간 : 15일 ~ 15일
조각 목록 :
날짜: 15, 조각 길이: 540
전체 조각의 개수: 1
1시간 이상인 조각의 개수: 1
조각 길이 최대: 540, 최소: 540, 평균: 540

기간 : 16일 ~ 16일
조각 목록 :
날짜: 16, 조각 길이: 210
날짜: 16, 조각 길이: 90
전체 조각의 개수: 2
1시간 이상인 조각의 개수: 2
조각 길이 최대: 210, 최소: 90, 평균: 150

기간 : 17일 ~ 17일
조각 목록 :
날짜: 17, 조각 길이: 45
날짜: 17, 조각 길이: 160
날짜: 17, 조각 길이: 75
전체 조각의 개수: 3
1시간 이상인 조각의 개수: 2
조각 길이 최대: 160, 최소: 45, 평균: 93

기간 : 18일 ~ 18일
조각 목록 :
날짜: 18, 조각 길이: 45
날짜: 18, 조각 길이: 120
날짜: 18, 조각 길이: 75
전체 조각의 개수: 3
1시간 이상인 조각의 개수: 2
조각 길이 최대: 120, 최소: 45, 평균: 80

기간 : 19일 ~ 19일
조각 목록 :
날짜: 19, 조각 길이: 45
날짜: 19, 조각 길이: 120
날짜: 19, 조각 길이: 130
전체 조각의 개수: 3
1시간 이상인 조각의 개수: 2
조각 길이 최대: 130, 최소: 45, 평균: 98

기간 : 22일 ~ 22일
조각 목록 :
날짜: 22, 조각 길이: 170
날짜: 22, 조각 길이: 340
전체 조각의 개수: 2
1시간 이상인 조각의 개수: 2
조각 길이 최대: 340, 최소: 170, 평균: 255

기간 : 23일 ~ 23일
조각 목록 :
날짜: 23, 조각 길이: 45
날짜: 23, 조각 길이: 50
날짜: 23, 조각 길이: 70
날짜: 23, 조각 길이: 75
전체 조각의 개수: 4
1시간 이상인 조각의 개수: 2
조각 길이 최대: 75, 최소: 45, 평균: 60

기간 : 24일 ~ 24일
조각 목록 :
날짜: 24, 조각 길이: 45
날짜: 24, 조각 길이: 50
날짜: 24, 조각 길이: 40
날짜: 24, 조각 길이: 105
전체 조각의 개수: 4
1시간 이상인 조각의 개수: 1
조각 길이 최대: 105, 최소: 40, 평균: 60

기간 : 25일 ~ 25일
조각 목록 :
날짜: 25, 조각 길이: 540
전체 조각의 개수: 1
1시간 이상인 조각의 개수: 1
조각 길이 최대: 540, 최소: 540, 평균: 540

기간 : 26일 ~ 26일
조각 목록 :
날짜: 26, 조각 길이: 170
날짜: 26, 조각 길이: 320
전체 조각의 개수: 2
1시간 이상인 조각의 개수: 2
조각 길이 최대: 320, 최소: 170, 평균: 245

기간 : 29일 ~ 29일
조각 목록 :
날짜: 29, 조각 길이: 300
전체 조각의 개수: 1
1시간 이상인 조각의 개수: 1
조각 길이 최대: 300, 최소: 300, 평균: 300

기간 : 30일 ~ 30일
조각 목록 :
날짜: 30, 조각 길이: 540
전체 조각의 개수: 1
1시간 이상인 조각의 개수: 1
조각 길이 최대: 540, 최소: 540, 평균: 540

기간 : 31일 ~ 31일
조각 목록 :
날짜: 31, 조각 길이: 60
날짜: 31, 조각 길이: 60
날짜: 31, 조각 길이: 120
날짜: 31, 조각 길이: 70
전체 조각의 개수: 4
1시간 이상인 조각의 개수: 4
조각 길이 최대: 120, 최소: 60, 평균: 77

기간 : 1일 ~ 5일
조각 목록 :
날짜: 1, 조각 길이: 30
날짜: 1, 조각 길이: 15
날짜: 1, 조각 길이: 130
날짜: 1, 조각 길이: 180
날짜: 2, 조각 길이: 180
날짜: 2, 조각 길이: 70
날짜: 4, 조각 길이: 55
날짜: 4, 조각 길이: 55
날짜: 4, 조각 길이: 45
날짜: 5, 조각 길이: 250
날짜: 5, 조각 길이: 55
날짜: 5, 조각 길이: 150
전체 조각의 개수: 12
1시간 이상인 조각의 개수: 6
조각 길이 최대: 250, 최소: 15, 평균: 101

기간 : 8일 ~ 12일
조각 목록 :
날짜: 11, 조각 길이: 180
날짜: 11, 조각 길이: 60
날짜: 12, 조각 길이: 300
날짜: 12, 조각 길이: 60
전체 조각의 개수: 4
1시간 이상인 조각의 개수: 4
조각 길이 최대: 300, 최소: 60, 평균: 150

기간 : 15일 ~ 19일
조각 목록 :
날짜: 15, 조각 길이: 540
날짜: 16, 조각 길이: 210
날짜: 16, 조각 길이: 90
날짜: 17, 조각 길이: 45
날짜: 17, 조각 길이: 160
날짜: 17, 조각 길이: 75
날짜: 18, 조각 길이: 45
날짜: 18, 조각 길이: 120
날짜: 18, 조각 길이: 75
날짜: 19, 조각 길이: 45
날짜: 19, 조각 길이: 120
날짜: 19, 조각 길이: 130
전체 조각의 개수: 12
1시간 이상인 조각의 개수: 9
조각 길이 최대: 540, 최소: 45, 평균: 137

기간 : 22일 ~ 26일
조각 목록 :
날짜: 22, 조각 길이: 170
날짜: 22, 조각 길이: 340
날짜: 23, 조각 길이: 45
날짜: 23, 조각 길이: 50
날짜: 23, 조각 길이: 70
날짜: 23, 조각 길이: 75
날짜: 24, 조각 길이: 45
날짜: 24, 조각 길이: 50
날짜: 24, 조각 길이: 40
날짜: 24, 조각 길이: 105
날짜: 25, 조각 길이: 540
날짜: 26, 조각 길이: 170
날짜: 26, 조각 길이: 320
전체 조각의 개수: 13
1시간 이상인 조각의 개수: 8
조각 길이 최대: 540, 최소: 40, 평균: 155

기간 : 29일 ~ 31일
조각 목록 :
날짜: 29, 조각 길이: 300
날짜: 30, 조각 길이: 540
날짜: 31, 조각 길이: 60
날짜: 31, 조각 길이: 60
날짜: 31, 조각 길이: 120
날짜: 31, 조각 길이: 70
전체 조각의 개수: 6
1시간 이상인 조각의 개수: 6
조각 길이 최대: 540, 최소: 60, 평균: 191

기간 : 1일 ~ 31일
조각 목록 :
날짜: 1, 조각 길이: 30
날짜: 1, 조각 길이: 15
날짜: 1, 조각 길이: 130
날짜: 1, 조각 길이: 180
날짜: 2, 조각 길이: 180
날짜: 2, 조각 길이: 70
날짜: 4, 조각 길이: 55
날짜: 4, 조각 길이: 55
날짜: 4, 조각 길이: 45
날짜: 5, 조각 길이: 250
날짜: 5, 조각 길이: 55
날짜: 5, 조각 길이: 150
날짜: 11, 조각 길이: 180
날짜: 11, 조각 길이: 60
날짜: 12, 조각 길이: 300
날짜: 12, 조각 길이: 60
날짜: 15, 조각 길이: 540
날짜: 16, 조각 길이: 210
날짜: 16, 조각 길이: 90
날짜: 17, 조각 길이: 45
날짜: 17, 조각 길이: 160
날짜: 17, 조각 길이: 75
날짜: 18, 조각 길이: 45
날짜: 18, 조각 길이: 120
날짜: 18, 조각 길이: 75
날짜: 19, 조각 길이: 45
날짜: 19, 조각 길이: 120
날짜: 19, 조각 길이: 130
날짜: 22, 조각 길이: 170
날짜: 22, 조각 길이: 340
날짜: 23, 조각 길이: 45
날짜: 23, 조각 길이: 50
날짜: 23, 조각 길이: 70
날짜: 23, 조각 길이: 75
날짜: 24, 조각 길이: 45
날짜: 24, 조각 길이: 50
날짜: 24, 조각 길이: 40
날짜: 24, 조각 길이: 105
날짜: 25, 조각 길이: 540
날짜: 26, 조각 길이: 170
날짜: 26, 조각 길이: 320
날짜: 29, 조각 길이: 300
날짜: 30, 조각 길이: 540
날짜: 31, 조각 길이: 60
날짜: 31, 조각 길이: 60
날짜: 31, 조각 길이: 120
날짜: 31, 조각 길이: 70
전체 조각의 개수: 47
1시간 이상인 조각의 개수: 33
조각 길이 최대: 540, 최소: 15, 평균: 141
```
