## 요구사항 분석
날짜가 ```yyyy-mm-dd``` 형식으로 주어질 때, 그 날의 요일을 구하시오.

단, 4의 배수인 해는 윤년이고, 그 중 100의 배수인 해는 윤년이 아니며, 또 그 중 400의 배수인 해는 윤년이다.

오늘은 2022년 10월 20일 목요일이다.

### 데이터 클래스
데이터 클래스는 주어지지 않는다.

## 코드 및 실행 결과
### 코드
```kotlin
// 0년부터 특정 연도까지의 2월 29일의 수 구하기
fun countFeb29(year: Int): Int {
    val fourHundreds = year / 400
    val rest = year % 400
    
    return 97 * fourHundreds + (rest + 4) / 4 - rest / 100
}

// 윤년 여부 판정
fun isLeapYear(year: Int): Boolean {
    if (year % 4 == 0) {
        if (year % 100 == 0) {
            return year % 400 == 0
        }
        return true
    }
    return false
}

// 0년 1월 1일부터 특정일까지의 날짜 수 구하기
fun countPassedDays(year: Int, month: Int, day: Int): Int {
    
    // 지난해까지의 2월 29일의 개수
    val Feb29sInPrevYears = countFeb29(year - 1)
    
    // 지난해까지의 나머지 날짜의 개수
    val otherDaysInPrevYears = year * 365
    
    // 올해의 날짜의 개수
    val daysInFeb = if (isLeapYear(year)) { 29 } else { 28 }
    val days = listOf(31, daysInFeb, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31)
    
    val daysInPrevMonths = (0 until month - 1).sumOf { days[it] }
    val daysInCurrentYear = daysInPrevMonths + day
    
    return Feb29sInPrevYears + otherDaysInPrevYears + daysInCurrentYear
}

// 요일 구하기
fun findDayOfWeek(date: String): String {
    
    val year = date.split("-")[0].toInt()
    val month = date.split("-")[1].toInt()
    val day = date.split("-")[2].toInt()
    
    // 2022년 10월 20일은 목요일임
    val daysUntilToday = countPassedDays(2022, 10, 20)
    val days = countPassedDays(year, month, day)
    val diff = days - daysUntilToday
    
    // 0:일요일, 1:월요일, ..., 4: 목요일, ..., 6:토요일
    var dayOfWeekNum = (4 + diff) % 7
    if (dayOfWeekNum < 0) {
        dayOfWeekNum += 7
    }
    
    return when (dayOfWeekNum) {
        0 -> "일요일"
        1 -> "월요일"
        2 -> "화요일"
        3 -> "수요일"
        4 -> "목요일"
        5 -> "금요일"
        6 -> "토요일"
        else -> ""
    }
}

fun main() {
    
    // 날짜 목록
    val tempDates = listOf<String> (
        "1919-03-01", "1945-08-15", "1950-06-25", "1980-05-18", "1987-06-10",
        "2022-10-13", "2022-10-14", "2022-10-15", "2022-10-16", "2022-10-17",
        "2022-10-18", "2022-10-19", "2022-10-20", "2022-10-21", "2022-10-22",
        "2022-10-23", "2022-10-24", "2022-10-25", "2022-10-26", "2022-10-27",
        "2022-07-20", "2022-08-20", "2022-09-20",
        "2022-11-20", "2022-12-20", "2023-01-20",
        "2020-10-20", "2021-10-20", "2023-10-20", "2024-10-20",
        "2014-10-20", "2018-10-20", "2026-10-20", "2030-10-20",
        "2002-10-20", "2012-10-20", "2032-10-20", "2042-10-20",
        "1822-10-20", "1922-10-20", "2122-10-20", "2222-10-20",
        "1222-10-20", "1622-10-20", "2422-10-20", "2822-10-20",
        "2016-02-28", "2020-02-28", "2024-02-28", "2028-02-28", "2029-02-28",
        "2016-02-29", "2020-02-29", "2024-02-29", "2028-02-29",
        "2016-03-01", "2020-03-01", "2024-03-01", "2028-03-01", "2029-03-01"
    )
    
    // 해당 날짜들의 요일 구하기
    tempDates.forEach {
        println("${it} 의 요일 : ${findDayOfWeek(it)}")
    }
}
```

### 실행 결과
```kotlin
1919-03-01 의 요일 : 토요일
1945-08-15 의 요일 : 수요일
1950-06-25 의 요일 : 일요일
1980-05-18 의 요일 : 일요일
1987-06-10 의 요일 : 수요일
2022-10-13 의 요일 : 목요일
2022-10-14 의 요일 : 금요일
2022-10-15 의 요일 : 토요일
2022-10-16 의 요일 : 일요일
2022-10-17 의 요일 : 월요일
2022-10-18 의 요일 : 화요일
2022-10-19 의 요일 : 수요일
2022-10-20 의 요일 : 목요일
2022-10-21 의 요일 : 금요일
2022-10-22 의 요일 : 토요일
2022-10-23 의 요일 : 일요일
2022-10-24 의 요일 : 월요일
2022-10-25 의 요일 : 화요일
2022-10-26 의 요일 : 수요일
2022-10-27 의 요일 : 목요일
2022-07-20 의 요일 : 수요일
2022-08-20 의 요일 : 토요일
2022-09-20 의 요일 : 화요일
2022-11-20 의 요일 : 일요일
2022-12-20 의 요일 : 화요일
2023-01-20 의 요일 : 금요일
2020-10-20 의 요일 : 화요일
2021-10-20 의 요일 : 수요일
2023-10-20 의 요일 : 금요일
2024-10-20 의 요일 : 일요일
2014-10-20 의 요일 : 월요일
2018-10-20 의 요일 : 토요일
2026-10-20 의 요일 : 화요일
2030-10-20 의 요일 : 일요일
2002-10-20 의 요일 : 일요일
2012-10-20 의 요일 : 토요일
2032-10-20 의 요일 : 수요일
2042-10-20 의 요일 : 월요일
1822-10-20 의 요일 : 일요일
1922-10-20 의 요일 : 금요일
2122-10-20 의 요일 : 화요일
2222-10-20 의 요일 : 일요일
1222-10-20 의 요일 : 목요일
1622-10-20 의 요일 : 목요일
2422-10-20 의 요일 : 목요일
2822-10-20 의 요일 : 목요일
2016-02-28 의 요일 : 일요일
2020-02-28 의 요일 : 금요일
2024-02-28 의 요일 : 수요일
2028-02-28 의 요일 : 월요일
2029-02-28 의 요일 : 수요일
2016-02-29 의 요일 : 월요일
2020-02-29 의 요일 : 토요일
2024-02-29 의 요일 : 목요일
2028-02-29 의 요일 : 화요일
2016-03-01 의 요일 : 화요일
2020-03-01 의 요일 : 일요일
2024-03-01 의 요일 : 금요일
2028-03-01 의 요일 : 수요일
2029-03-01 의 요일 : 목요일
```