## 요구사항 분석
어떤 해의 1월 1일의 요일과 윤년인지의 여부, 공휴일 목록과 각 공휴일별 대체공휴일 지정 여부(없음/일요일 및 다른 공휴일/주말 및 다른 공휴일)가 주어졌을 때, 그 해의 다음에 해당하는 연휴를 모두 출력하시오.
* 3일 이상의, 두 휴일 사이에 평일을 최대 1일까지 포함한 연휴
* 단, 토요일은 휴일로 간주한다.

단, 대체공휴일을 지정하는 규칙은 다음과 같다.
* 대체공휴일 지정 규칙을 만족시키는 날짜의 중복이 있는 대로, 뒤쪽으로 대체휴일을 붙인다.
  * 예를 들어 10월 3일 토요일이 추석인 경우, 10월 3일은 토요일+개천절+추석, 10월 4일은 일요일+추석이므로 10월 3일은 (토요일+개천절), (개천절+추석)이, 10월 4일은 (일요일+추석)이 겹친다. 따라서 10월 5일부터 7일까지가 대체휴일이다.
* 토요일은 대체공휴일 지정에서 제외한다.

정보의 형태는 아래 **데이터 클래스**와 같다.

### 데이터 클래스
```kotlin
// 달력 데이터
data class Calendar (
    val first_day_of_week: Int, // 1월 1일의 요일 (0: 일요일, 1: 월요일, ..., 6: 토요일)
    val is_leap_year: Boolean, // 윤년 여부
    val holidays: List<Holiday> // 공휴일 목록
)

// 공휴일
data class Holiday (
    val date: String, // 날짜 (예: 03/01)
    val substitution_option: Int // 대체휴일 지정 여부 (0: 없음, 1: 일요일+공휴일, 2: 주말+공휴일)
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 달력 데이터
data class Calendar (
    val first_day_of_week: Int, // 1월 1일의 요일 (0: 일요일, 1: 월요일, ..., 6: 토요일)
    val is_leap_year: Boolean, // 윤년 여부
    val holidays: List<Holiday> // 공휴일 목록
)

// 공휴일
data class Holiday (
    val date: String, // 날짜 (예: 03/01)
    val substitution_option: Int // 대체휴일 지정 여부 (0: 없음, 1: 일요일+공휴일, 2: 주말+공휴일)
)

// 대체휴일 표시
fun markSubHoliday(
    isHoliday: MutableList<Boolean>,
    isSundayOrPublicHoliday: MutableList<Boolean>,
    startIdx: Int,
    monthDays: List<Int> // 대체휴일 출력용
) {
    var substituteIdx = startIdx + 1
    while (isHoliday[substituteIdx]) {
        substituteIdx++
    }
    isHoliday[substituteIdx] = true
    isSundayOrPublicHoliday[substituteIdx] = true
    
    println("${findDate(startIdx, monthDays)}의 대체 휴일: ${findDate(substituteIdx, monthDays)}")
}

// 인덱스에 해당하는 날짜 표시
fun findDate(idx: Int, monthDays: List<Int>): String {
    var sumMonthDays = 0
    var sumMonthDaysUntilLastMonth = 0
    var month = 0
    
    while (sumMonthDays <= idx) {
        sumMonthDaysUntilLastMonth = sumMonthDays
        sumMonthDays += monthDays[month]
        month++
    }
    return "${month}/${idx - sumMonthDaysUntilLastMonth + 1}"
}

// 특정 연휴 출력
fun printHs(isHoliday: List<Boolean>, startIndex: Int, endIndex: Int, monthDays: List<Int>) {
    println("연휴 기간 : ${findDate(startIndex, monthDays)} ~ ${findDate(endIndex, monthDays)}")
    println("연휴 일수 : ${endIndex - startIndex + 1}일")
    (startIndex..endIndex).forEach {
        println("날짜: ${findDate(it, monthDays)}, 휴일 여부: ${isHoliday[it]}")
    }
    print("\n")
}

// 연휴 출력
fun printConsecutiveHolidays(calendar: Calendar) {
    val monthDays = if (calendar.is_leap_year) {
        listOf(31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31)
    } else {
        listOf(31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31)
    }
    
    // 휴일 및 공휴일 목록
    val yearDates = monthDays.sum()
    val isHoliday = (0 until yearDates).map {
        val mod = (it + calendar.first_day_of_week) % 7
        mod == 0 || mod == 6
    }.toMutableList()
    
    val isSundayOrPublicHoliday = (0 until monthDays.sum()).map {
        (it + calendar.first_day_of_week) % 7 == 0
    }.toMutableList()
    
    // 첫 공휴일부터 순서대로 휴일 구하기
    val sortedHolidays = calendar.holidays.sortedBy { it.date }
    
    // 중복 공휴일이 여러 개인 경우
    val multiplePublicHolidays = sortedHolidays.filter { h0 ->
        sortedHolidays.count { h1 ->
            h0.date == h1.date
        } >= 2
    }.map {
        Holiday(it.date, it.substitution_option)
    }
    
    val multiplePublicHolidaysDate = multiplePublicHolidays.map {
        it.date
    }.toSet()
    
    println("중복 공휴일이 여러 개인 경우 :")
    println(multiplePublicHolidaysDate)
    print("\n")
    
    // 중복 공휴일이 여러 개일 때의 휴일 체크리스트 (중복 휴일 방지)
    val checkList = mutableSetOf<String>()
    
    // 휴일 표시
    sortedHolidays.forEach {
        val month = it.date.split("/")[0].toInt()
        val day = it.date.split("/")[1].toInt()
        val idx = monthDays.take(month-1).sum() + day - 1
        
        // 대체공휴일 적용 (해당일의 중복 공휴일이 여러 개인 경우)
        if (it.date in multiplePublicHolidaysDate && !(it.date in checkList)) {
            val matchedHolidays = multiplePublicHolidays.filter { mph ->
                mph.date == it.date
            }
            val matchedHolidaysCount = matchedHolidays.count()
            val maxSub = matchedHolidays.map { it.substitution_option }.max()
            
            // 중간에 휴일 여부 갱신 방지를 위해 미리 저장한 값을 이용
            val isH = isHoliday[idx]
            val isSPH = isSundayOrPublicHoliday[idx]
            
            // n일 추가: 최대 대체휴일 옵션이 2 + 주말 / 최대 대체휴일 옵션이 1 + 일요일인 경우
            if ((maxSub == 2 && isH) || (maxSub == 1 && isSPH)) {
                (0 until matchedHolidaysCount).forEach {
                    markSubHoliday(isHoliday, isSundayOrPublicHoliday, idx, monthDays)
                }
            }
            
            // (n-1)일 추가: 최대 대체휴일 옵션이 1 이상인 경우
            else if (maxSub >= 1) {
                (0 until matchedHolidaysCount - 1).forEach {
                    markSubHoliday(isHoliday, isSundayOrPublicHoliday, idx, monthDays)
                }
            }
            
            // 휴일 중복 추가 방지를 위해 해당 날짜를 체크리스트에 추가
            checkList.add(it.date)
        }
        
        // 대체공휴일 적용 (해당일의 공휴일이 1개인 경우)
        else if (!(it.date in checkList)) {
            when (it.substitution_option) {
                1 -> {
                    if (isSundayOrPublicHoliday[idx]) {
                        markSubHoliday(isHoliday, isSundayOrPublicHoliday, idx, monthDays)
                    }
                }
                2 -> {
                    if (isHoliday[idx]) {
                        markSubHoliday(isHoliday, isSundayOrPublicHoliday, idx, monthDays)
                    }
                }
            }
        }
        
        // 공휴일 적용
        isHoliday[idx] = true
        isSundayOrPublicHoliday[idx] = true
    }
    print("\n")
    
    // 휴일 출력
    var consecHolidays = 0
    var consecWorkdays = 0
    
    isHoliday.forEachIndexed { idx, it ->
        if (it) {
            consecHolidays++
            consecWorkdays = 0
            
            // 해당 연도의 마지막 날이면
            if (consecHolidays >= 3 && idx == yearDates - 1) {
                printHs(isHoliday, idx - consecHolidays + 1, idx, monthDays)
            }
        } else {
            consecWorkdays++
            if (consecWorkdays == 2) { // 연휴 종료
                if (consecHolidays >= 3) {
                    
                    // 마지막 휴일은 2일 전임
                    printHs(isHoliday, idx - consecHolidays - 1, idx - 2, monthDays)
                }
                consecHolidays = 0
            } else if (consecWorkdays <= 1) {
                
                // 연휴가 지속되는 경우에만 휴일 수 증가
                if (it || (idx < yearDates - 1 && isHoliday[idx + 1])) {
                    consecHolidays++
                }
            }
        }
    }
}

fun main() {
    
    // 임시 달력 데이터
    val tempCalendar = Calendar(
        1, true,
        listOf<Holiday>(
            Holiday("01/01", 0),
            Holiday("01/26", 0),
            Holiday("01/26", 1),
            Holiday("02/09", 1),
            Holiday("02/10", 1),
            Holiday("02/11", 1),
            Holiday("02/28", 0),
            Holiday("03/01", 2),
            Holiday("03/06", 1),
            Holiday("03/21", 0),
            Holiday("03/30", 2),
            Holiday("04/13", 1),
            Holiday("04/28", 0),
            Holiday("05/05", 2),
            Holiday("05/05", 0),
            Holiday("05/25", 0),
            Holiday("05/25", 1),
            Holiday("06/06", 0),
            Holiday("06/08", 0),
            Holiday("06/08", 2),
            Holiday("07/06", 0),
            Holiday("07/06", 0),
            Holiday("07/13", 1),
            Holiday("07/13", 0),
            Holiday("07/20", 1),
            Holiday("07/20", 2),
            Holiday("07/27", 2),
            Holiday("07/27", 0),
            Holiday("08/15", 2),
            Holiday("09/01", 1),
            Holiday("09/04", 1),
            Holiday("09/16", 1),
            Holiday("09/18", 0),
            Holiday("09/18", 1),
            Holiday("10/02", 1),
            Holiday("10/03", 1),
            Holiday("10/03", 2),
            Holiday("10/04", 1),
            Holiday("10/09", 2),
            Holiday("10/25", 1),
            Holiday("10/26", 1),
            Holiday("10/26", 1),
            Holiday("10/27", 1),
            Holiday("10/27", 0),
            Holiday("11/13", 0),
            Holiday("11/28", 2),
            Holiday("11/29", 2),
            Holiday("11/29", 1),
            Holiday("11/30", 2),
            Holiday("11/30", 1),
            Holiday("12/12", 0),
            Holiday("12/24", 1),
            Holiday("12/25", 2),
            Holiday("12/31", 1)
        )
    )
    
    // 종목 데이터 비교
    printConsecutiveHolidays(tempCalendar)
}
```

### 실행 결과
```kotlin
중복 공휴일이 여러 개인 경우 :
[01/26, 05/05, 05/25, 06/08, 07/06, 07/13, 07/20, 07/27, 09/18, 10/03, 10/26, 10/27, 11/29, 11/30]

1/26의 대체 휴일: 1/29
2/11의 대체 휴일: 2/12
3/30의 대체 휴일: 4/1
5/5의 대체 휴일: 5/6
5/5의 대체 휴일: 5/7
5/25의 대체 휴일: 5/27
6/8의 대체 휴일: 6/10
6/8의 대체 휴일: 6/11
7/13의 대체 휴일: 7/15
7/20의 대체 휴일: 7/22
7/20의 대체 휴일: 7/23
7/27의 대체 휴일: 7/29
7/27의 대체 휴일: 7/30
9/1의 대체 휴일: 9/2
9/18의 대체 휴일: 9/19
10/3의 대체 휴일: 10/4
10/4의 대체 휴일: 10/7
10/26의 대체 휴일: 10/28
10/27의 대체 휴일: 10/29
10/27의 대체 휴일: 10/30
11/29의 대체 휴일: 12/2
11/30의 대체 휴일: 12/3
11/30의 대체 휴일: 12/4

연휴 기간 : 1/26 ~ 1/29
연휴 일수 : 4일
날짜: 1/26, 휴일 여부: true
날짜: 1/27, 휴일 여부: true
날짜: 1/28, 휴일 여부: true
날짜: 1/29, 휴일 여부: true

연휴 기간 : 2/9 ~ 2/12
연휴 일수 : 4일
날짜: 2/9, 휴일 여부: true
날짜: 2/10, 휴일 여부: true
날짜: 2/11, 휴일 여부: true
날짜: 2/12, 휴일 여부: true

연휴 기간 : 2/28 ~ 3/3
연휴 일수 : 5일
날짜: 2/28, 휴일 여부: true
날짜: 2/29, 휴일 여부: false
날짜: 3/1, 휴일 여부: true
날짜: 3/2, 휴일 여부: true
날짜: 3/3, 휴일 여부: true

연휴 기간 : 3/21 ~ 3/24
연휴 일수 : 4일
날짜: 3/21, 휴일 여부: true
날짜: 3/22, 휴일 여부: false
날짜: 3/23, 휴일 여부: true
날짜: 3/24, 휴일 여부: true

연휴 기간 : 3/30 ~ 4/1
연휴 일수 : 3일
날짜: 3/30, 휴일 여부: true
날짜: 3/31, 휴일 여부: true
날짜: 4/1, 휴일 여부: true

연휴 기간 : 5/4 ~ 5/7
연휴 일수 : 4일
날짜: 5/4, 휴일 여부: true
날짜: 5/5, 휴일 여부: true
날짜: 5/6, 휴일 여부: true
날짜: 5/7, 휴일 여부: true

연휴 기간 : 5/25 ~ 5/27
연휴 일수 : 3일
날짜: 5/25, 휴일 여부: true
날짜: 5/26, 휴일 여부: true
날짜: 5/27, 휴일 여부: true

연휴 기간 : 6/6 ~ 6/11
연휴 일수 : 6일
날짜: 6/6, 휴일 여부: true
날짜: 6/7, 휴일 여부: false
날짜: 6/8, 휴일 여부: true
날짜: 6/9, 휴일 여부: true
날짜: 6/10, 휴일 여부: true
날짜: 6/11, 휴일 여부: true

연휴 기간 : 7/13 ~ 7/15
연휴 일수 : 3일
날짜: 7/13, 휴일 여부: true
날짜: 7/14, 휴일 여부: true
날짜: 7/15, 휴일 여부: true

연휴 기간 : 7/20 ~ 7/23
연휴 일수 : 4일
날짜: 7/20, 휴일 여부: true
날짜: 7/21, 휴일 여부: true
날짜: 7/22, 휴일 여부: true
날짜: 7/23, 휴일 여부: true

연휴 기간 : 7/27 ~ 7/30
연휴 일수 : 4일
날짜: 7/27, 휴일 여부: true
날짜: 7/28, 휴일 여부: true
날짜: 7/29, 휴일 여부: true
날짜: 7/30, 휴일 여부: true

연휴 기간 : 8/15 ~ 8/18
연휴 일수 : 4일
날짜: 8/15, 휴일 여부: true
날짜: 8/16, 휴일 여부: false
날짜: 8/17, 휴일 여부: true
날짜: 8/18, 휴일 여부: true

연휴 기간 : 8/31 ~ 9/4
연휴 일수 : 5일
날짜: 8/31, 휴일 여부: true
날짜: 9/1, 휴일 여부: true
날짜: 9/2, 휴일 여부: true
날짜: 9/3, 휴일 여부: false
날짜: 9/4, 휴일 여부: true

연휴 기간 : 9/14 ~ 9/22
연휴 일수 : 9일
날짜: 9/14, 휴일 여부: true
날짜: 9/15, 휴일 여부: true
날짜: 9/16, 휴일 여부: true
날짜: 9/17, 휴일 여부: false
날짜: 9/18, 휴일 여부: true
날짜: 9/19, 휴일 여부: true
날짜: 9/20, 휴일 여부: false
날짜: 9/21, 휴일 여부: true
날짜: 9/22, 휴일 여부: true

연휴 기간 : 10/2 ~ 10/9
연휴 일수 : 8일
날짜: 10/2, 휴일 여부: true
날짜: 10/3, 휴일 여부: true
날짜: 10/4, 휴일 여부: true
날짜: 10/5, 휴일 여부: true
날짜: 10/6, 휴일 여부: true
날짜: 10/7, 휴일 여부: true
날짜: 10/8, 휴일 여부: false
날짜: 10/9, 휴일 여부: true

연휴 기간 : 10/25 ~ 10/30
연휴 일수 : 6일
날짜: 10/25, 휴일 여부: true
날짜: 10/26, 휴일 여부: true
날짜: 10/27, 휴일 여부: true
날짜: 10/28, 휴일 여부: true
날짜: 10/29, 휴일 여부: true
날짜: 10/30, 휴일 여부: true

연휴 기간 : 11/28 ~ 12/4
연휴 일수 : 7일
날짜: 11/28, 휴일 여부: true
날짜: 11/29, 휴일 여부: true
날짜: 11/30, 휴일 여부: true
날짜: 12/1, 휴일 여부: true
날짜: 12/2, 휴일 여부: true
날짜: 12/3, 휴일 여부: true
날짜: 12/4, 휴일 여부: true

연휴 기간 : 12/12 ~ 12/15
연휴 일수 : 4일
날짜: 12/12, 휴일 여부: true
날짜: 12/13, 휴일 여부: false
날짜: 12/14, 휴일 여부: true
날짜: 12/15, 휴일 여부: true

연휴 기간 : 12/21 ~ 12/25
연휴 일수 : 5일
날짜: 12/21, 휴일 여부: true
날짜: 12/22, 휴일 여부: true
날짜: 12/23, 휴일 여부: false
날짜: 12/24, 휴일 여부: true
날짜: 12/25, 휴일 여부: true

연휴 기간 : 12/28 ~ 12/31
연휴 일수 : 4일
날짜: 12/28, 휴일 여부: true
날짜: 12/29, 휴일 여부: true
날짜: 12/30, 휴일 여부: false
날짜: 12/31, 휴일 여부: true
```
