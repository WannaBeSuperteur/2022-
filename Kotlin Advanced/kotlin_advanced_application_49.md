## 요구사항 분석
두 날짜 ```date1```, ```date2``` 사이의 시간 간격을 다음과 같은 형식으로 출력하시오. (단, 2월은 무조건 28일까지 있다.)
```kotlin
12 days 3 hours 4 minutes 5 seconds
1 day 1 hour 1 minute 1 second
1 day 0 hours 0 minutes 0 seconds
```

```date1```, ```date2```의 가능한 형식은 다음의 각 줄과 같다. (2022년 10월 19일 18시 7분 26초)
```kotlin
2022-10-19 18:07:26
2022/10/19 18:07:26
18:07:26 10/19/2022
18:07:26 19/10/2022
20221019180726
18072620221019
18072610192022
2022년 10월 19일 오후 6시 7분 26초
2022년 10월 19일 18시 7분 26초
2022년 10월 19일 오후 06시 07분 26초
2022년 10월 19일 18시 07분 26초
```

단, 형식 오류는 다음과 같이 처리한다.
* ```date1```의 일치하는 형식이 없는 경우, ```date1 malformed```를 출력한다.
* ```date1```의 일치하는 형식이 있지만 모두 불가능한 날짜 및 시각인 경우, ```date1 impossible```를 출력한다.
* ```date1```의 "가능한 날짜 및 시각을 나타내는" 일치하는 형식이 여러 개인 경우, ```date1 ambiguous```를 출력한다.
* ```date2```에 대해서도 ```date1```과 마찬가지로 출력한다. (```date2 malformed``` 등으로)
* ```date1```과 ```date2```에 모두 형식 오류가 있는 경우는 각각 출력한다. (```date1 malformed date2 ambiguous``` 등으로)

### 데이터 클래스
데이터 클래스 없이 두 날짜 ```date1```, ```date2```가 위와 같은 형식으로 주어진다.

## 코드 및 실행 결과
### 코드
```kotlin
// 가능한 형식인지 판단
fun isPossible(year: Int, month: Int, day: Int, hour: Int, minute: Int, second: Int): Boolean {
    
    // 연, 월, 일에 의한 가능 여부 판단
    if (year < 0 || year > 9999) {
        return false
    }
    if (month < 1 || month > 12) {
        return false
    }
    when (month) {
        1, 3, 5, 7, 8, 10, 12 -> {
            if (day < 1 || day > 31) {
                return false
            }
        }
        4, 6, 9, 11 -> {
            if (day < 1 || day > 30) {
                return false
            }
        }
        2 -> {
            if (day < 1 || day > 28) {
                return false
            }
        }
    }
    
    // 시, 분, 초에 의한 가능 여부 판단
    if (hour < 0 || hour >= 24) {
        return false
    }
    if (minute < 0 || minute >= 60) {
        return false
    }
    if (second < 0 || second >= 60) {
        return false
    }
    
    return true
}

// 가능한 형식이면 가능한 결과 목록에 추가
fun addToListIfPossible(
    formats: MutableList<String>,
    year: Int, month: Int, day: Int, hour: Int, minute: Int, second: Int
) {
    if (isPossible(year, month, day, hour, minute, second)) {
        formats.add("${year} ${month} ${day} ${hour} ${minute} ${second}")
    }
}

// 가능한 형식에 의한 연, 월, 일, 시, 분, 초 및 일치하는 형식의 개수
data class DateTimeInfo (
    val possibleFormats: List<String>,
    val count: Int
)

// 날짜의 형식에 의한 연, 월, 일, 시, 분, 초 구하기
fun findDateTime(date: String): DateTimeInfo {
    
    // 최종 결과
    val formats = mutableListOf<String>()
    var count = 0
    
    // 2022-10-19 18:07:26
    // 2022/10/19 18:07:26
    listOf("-", "/").forEach {
        val re0 = Regex("[0-9]{4}${it}[0-9]{2}${it}[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}")
        
        if (re0.matches(date)) {
            val year = date.substringBefore(" ").split(it)[0].toInt()
            val month = date.substringBefore(" ").split(it)[1].toInt()
            val day = date.substringBefore(" ").split(it)[2].toInt()
        
            val hour = date.substringAfter(" ").split(":")[0].toInt()
            val minute = date.substringAfter(" ").split(":")[1].toInt()
            val second = date.substringAfter(" ").split(":")[2].toInt()
        
            addToListIfPossible(formats, year, month, day, hour, minute, second)
            count++
        }
    }
    
    // 18:07:26 10/19/2022
    // 18:07:26 19/10/2022
    val re1 = Regex("[0-9]{2}:[0-9]{2}:[0-9]{2} [0-9]{2}/[0-9]{2}/[0-9]{4}")
        
    if (re1.matches(date)) {
        val hour = date.substringBefore(" ").split(":")[0].toInt()
        val minute = date.substringBefore(" ").split(":")[1].toInt()
        val second = date.substringBefore(" ").split(":")[2].toInt()
        
        val monthDay = date.substringAfter(" ").split("/")[0].toInt()
        val dayMonth = date.substringAfter(" ").split("/")[1].toInt()
        val year = date.substringAfter(" ").split("/")[2].toInt()
        
        // 월과 일이 같은 경우
        if (monthDay == dayMonth) {
            addToListIfPossible(formats, year, monthDay, monthDay, hour, minute, second)
        }
        
        // 월과 일이 다른 경우
        else {
            addToListIfPossible(formats, year, monthDay, dayMonth, hour, minute, second)
            addToListIfPossible(formats, year, dayMonth, monthDay, hour, minute, second)
        }
        
        count++
    }
    
    // 20221019180726
    // 18072620221019
    // 18072610192022
    if (Regex("[0-9]{14}").matches(date)) {
        
        // 2자리씩 나누어서 보다 편리하게
        val dateFrags = (0..6).map {
            date.substring(2 * it, 2 * (it + 1))
        }.map {
            it.toInt()
        }
        
        // 20221019180726
        addToListIfPossible(
            formats,
            "${dateFrags[0]}${dateFrags[1]}".toInt(), dateFrags[2], dateFrags[3],
            dateFrags[4], dateFrags[5], dateFrags[6]
        )
        
        // 18072620221019
        addToListIfPossible(
            formats,
            "${dateFrags[3]}${dateFrags[4]}".toInt(), dateFrags[5], dateFrags[6],
            dateFrags[0], dateFrags[1], dateFrags[2]
        )
        
        // 18072610192022
        addToListIfPossible(
            formats,
            "${dateFrags[5]}${dateFrags[6]}".toInt(), dateFrags[3], dateFrags[4],
            dateFrags[0], dateFrags[1], dateFrags[2]
        )
        
        count++
    }
    
    // 2022년 10월 19일 오후 6시 7분 26초
    // 2022년 10월 19일 18시 7분 26초
    // 2022년 10월 19일 오후 06시 07분 26초
    // 2022년 10월 19일 18시 07분 26초
    val re2 = Regex("[0-9]{4}년 [0-9]{1,2}월 [0-9]{1,2}일 오[전후] [0-9]{1,2}시 [0-9]{1,2}분 [0-9]{1,2}초")
    val re3 = Regex("[0-9]{4}년 [0-9]{1,2}월 [0-9]{1,2}일 [0-9]{1,2}시 [0-9]{1,2}분 [0-9]{1,2}초")
    
    // re2, re3은 모두 매칭될 수는 없고 1개만 매칭 가능
    listOf(re2, re3).forEach {
        if (it.matches(date)) {
            val year = date.substringBefore("년").toInt()
            val month = date.substringBefore("월").substringAfterLast(" ").toInt()
            val day = date.substringBefore("일").substringAfterLast(" ").toInt()
        
            val hour = if (date.contains("오후")) {
                12
            } else {
                0
            } + date.substringBefore("시").substringAfterLast(" ").toInt()
            
            val minute = date.substringBefore("분").substringAfterLast(" ").toInt()
            val second = date.substringBefore("초").substringAfterLast(" ").toInt()
            
            addToListIfPossible(formats, year, month, day, hour, minute, second)
            count++
        }
    }
    
    return DateTimeInfo(formats, count)
}

// 각 날짜의 값 구하기 [year, month, day, hour, month, second]
fun computeValue(date_parsed: List<Int>, days: List<Int>): Int {
    
    // 오버플로 방지를 위해 연도에서 2000을 빼고 계산
    val yearsValue = (date_parsed[0] - 2000) * 365 * 24 * 60 * 60
    val monthsValue = (0 until date_parsed[1] - 1).sumOf {
        days[it]
    } * 24 * 60 * 60
    val daysValue = date_parsed[2] * 24 * 60 * 60
    
    val hoursValue = date_parsed[3] * 60 * 60
    val minutesValue = date_parsed[4] * 60
    val secondsValue = date_parsed[5]
    
    return yearsValue + monthsValue + daysValue + hoursValue + minutesValue + secondsValue
}

// 두 날짜 간의 차이 계산 [year, month, day, hour, month, second]
fun computeDif(date1_parsed: List<Int>, date2_parsed: List<Int>): String {
    val days = listOf(31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31)
    
    val date1_value = computeValue(date1_parsed, days)
    val date2_value = computeValue(date2_parsed, days)
    val diffSeconds = Math.max(date1_value - date2_value, date2_value - date1_value)
    
    val diffDay = diffSeconds / (24 * 60 * 60)
    val diffHour = (diffSeconds / (60 * 60)) % 24
    val diffMinute = (diffSeconds / 60) % 60
    val diffSecond = diffSeconds % 60
    
    val diffDayStr = "${diffDay} ${if (diffDay == 1) { "day" } else { "days" }}"
    val diffHourStr = "${diffHour} ${if (diffHour == 1) { "hour" } else { "hours" }}"
    val diffMinuteStr = "${diffMinute} ${if (diffMinute == 1) { "minute" } else { "minutes" }}"
    val diffSecondStr = "${diffSecond} ${if (diffSecond == 1) { "second" } else { "seconds" }}"
    
    return "${diffDayStr} ${diffHourStr} ${diffMinuteStr} ${diffSecondStr}"
}

// 형식 오류 정보 출력
fun findMalformedInfo(dateInfo: DateTimeInfo, name: String): String {
    return when {
        dateInfo.count == 0 -> {
            "${name} malformed"
        }
        dateInfo.possibleFormats.size == 0 -> {
            "${name} impossible"
        }
        dateInfo.possibleFormats.size >= 2 -> {
            "${name} ambiguous"
        }
        else -> {
            ""
        }
    }
}

// 두 날짜 간의 차이 구하기
fun findDifDateTime(date1: String, date2: String): String {
    val date1_info = findDateTime(date1)
    val date2_info = findDateTime(date2)
    
    val date1_malformedInfo = findMalformedInfo(date1_info, "date1")
    val date2_malformedInfo = findMalformedInfo(date2_info, "date2")
    
    println("1. date1 possible formats = ${date1_info.possibleFormats}")
    println("2. date1 count = ${date1_info.count}")
    println("3. date2 possible formats = ${date2_info.possibleFormats}")
    println("4. date2 count = ${date2_info.count}")
    
    // 형식 오류 발생
    if (date1_malformedInfo.isNotEmpty() || date2_malformedInfo.isNotEmpty()) {
        if (date1_malformedInfo.isNotEmpty() && date2_malformedInfo.isNotEmpty()) {
            return "${date1_malformedInfo} ${date2_malformedInfo}"
        } else {
            return "${date1_malformedInfo}${date2_malformedInfo}"
        }
    }
    else {
        
        // 날짜의 차이 구하기
        val date1_parsed = date1_info.possibleFormats[0].split(" ").map {
            it.toInt()
        }
        val date2_parsed = date2_info.possibleFormats[0].split(" ").map {
            it.toInt()
        }
        
        return computeDif(date1_parsed, date2_parsed)
    }
}

data class TwoDates (
    val date1: String,
    val date2: String
)

fun main() {
    
    // 임시 날짜 및 시각
    val tempDifDateTimes = listOf<TwoDates> (
        TwoDates("2022-01-10 18:13:28", "2022-10-19 19:44:12"),
        TwoDates("2022-03-10 18:13:28", "2022-10-19 19:44:12"),
        TwoDates("2022-05-10 18:13:28", "2022-10-19 19:44:12"),
        TwoDates("2022-07-10 18:13:28", "2022-10-19 19:44:12"),
        TwoDates("2022-09-10 18:13:28", "2022-10-19 19:44:12"),
        TwoDates("2022-09-10 18:13:28", "2023-08-19 19:44:12"),
        TwoDates("2022-01-24 21:46:39", "2022-10-19 19:44:12"),
        TwoDates("2022-03-24 21:46:39", "2022-10-19 19:44:12"),
        TwoDates("2022-05-24 21:46:39", "2022-10-19 19:44:12"),
        TwoDates("2022-07-24 21:46:39", "2022-10-19 19:44:12"),
        TwoDates("2022-09-24 21:46:39", "2022-10-19 19:44:12"),
        TwoDates("2022-09-24 21:46:39", "2023-08-19 19:44:12"),
        TwoDates("2022-10-19 19:44:12", "2022-09-10 18:13:28"),
        TwoDates("2022-10-19 19:44:12", "2022-09-24 21:46:39"),
        TwoDates("2022-02-30 23:28:56", "2022-09-10 18:13:28"),
        TwoDates("2022-02-28 17:60:41", "2022-09-10 18:13:28"),
        TwoDates("2022-02-28 09:13:22", "2022-09-31 18:13:28"),
        TwoDates("2022-02-28 09:13:22", "2022-09-10 25:13:28"),
        TwoDates("2022/01/10 18:13:28", "2022-10-19 19:44:12"),
        TwoDates("2022/01/10 18:13:28", "2022/10/19 19:44:12"),
        TwoDates("18:13:28 01/10/2022", "19:44:12 10/19/2022"),
        TwoDates("18:13:28 10/01/2022", "19:44:12 19/10/2022"),
        TwoDates("18:13:28 09/25/2021", "19:44:12 11/27/2022"),
        TwoDates("18:13:28 25/09/2021", "19:44:12 27/11/2022"),
        TwoDates("18:13:28 09/25/2024", "19:44:12 11/27/2026"),
        TwoDates("18:13:28 25/09/2024", "19:44:12 27/11/2026"),
        TwoDates("20220110181328", "20221019194412"),
        TwoDates("18132820220110", "19441220221019"),
        TwoDates("18132801102022", "19441210192022"),
        TwoDates("20210925181328", "20211127194412"),
        TwoDates("18132820210925", "19441220211127"),
        TwoDates("18132809252021", "19441211272021"),
        TwoDates("20240925181328", "20261127194412"),
        TwoDates("18132820240925", "19441220261127"),
        TwoDates("18132809252024", "19441211272026"),
        TwoDates("2022년 1월 10일 오후 6시 3분 28초", "2022년 10월 19일 오후 7시 44분 2초"),
        TwoDates("2022년 1월 10일 18시 3분 28초", "2022년 10월 19일 오후 7시 44분 2초"),
        TwoDates("2022년 1월 10일 오후 06시 03분 28초", "2022년 10월 19일 오후 07시 44분 02초"),
        TwoDates("2022년 1월 10일 18시 03분 28초", "2022년 10월 19일 19시 44분 02초"),
        TwoDates("18:13:28 11/11/2022", "19:44:12 11/12/2022"),
        TwoDates("19:44:12 11/12/2022", "18:13:28 11/11/2022"),
        TwoDates("18:13:28 11/11/2022", "19:44:12 11/13/2022"),
        TwoDates("19:44:12 11/13/2022", "18:13:28 11/11/2022"),
        TwoDates("20221020221022", "20221231235959"),
        TwoDates("20221231235959", "20231020231023"),
        TwoDates("20221231235959", "20221231235959"),
        TwoDates("2022-01.10 18:13:28", "2022-10-19 19:44:12"),
        TwoDates("2022-01-10 18:13:28", "2022-10/19 19:44:12"),
        TwoDates("2022-01-10 18.13.28", "2022-10-19 19:44:12"),
        TwoDates("2022-01-10 18:13:28", "2022 10 19 19:44:12")
    )
    
    tempDifDateTimes.forEach {
        val result = findDifDateTime(it.date1, it.date2)
        println("${it.date1} 과 ${it.date2} 의 차이 : ${result}\n")
    }
}
```

### 실행 결과
```kotlin
1. date1 possible formats = [2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-01-10 18:13:28 과 2022-10-19 19:44:12 의 차이 : 282 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 3 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-03-10 18:13:28 과 2022-10-19 19:44:12 의 차이 : 223 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 5 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-05-10 18:13:28 과 2022-10-19 19:44:12 의 차이 : 162 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 7 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-07-10 18:13:28 과 2022-10-19 19:44:12 의 차이 : 101 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 9 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-09-10 18:13:28 과 2022-10-19 19:44:12 의 차이 : 39 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 9 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2023 8 19 19 44 12]
4. date2 count = 1
2022-09-10 18:13:28 과 2023-08-19 19:44:12 의 차이 : 343 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 1 24 21 46 39]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-01-24 21:46:39 과 2022-10-19 19:44:12 의 차이 : 267 days 21 hours 57 minutes 33 seconds

1. date1 possible formats = [2022 3 24 21 46 39]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-03-24 21:46:39 과 2022-10-19 19:44:12 의 차이 : 208 days 21 hours 57 minutes 33 seconds

1. date1 possible formats = [2022 5 24 21 46 39]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-05-24 21:46:39 과 2022-10-19 19:44:12 의 차이 : 147 days 21 hours 57 minutes 33 seconds

1. date1 possible formats = [2022 7 24 21 46 39]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-07-24 21:46:39 과 2022-10-19 19:44:12 의 차이 : 86 days 21 hours 57 minutes 33 seconds

1. date1 possible formats = [2022 9 24 21 46 39]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-09-24 21:46:39 과 2022-10-19 19:44:12 의 차이 : 24 days 21 hours 57 minutes 33 seconds

1. date1 possible formats = [2022 9 24 21 46 39]
2. date1 count = 1
3. date2 possible formats = [2023 8 19 19 44 12]
4. date2 count = 1
2022-09-24 21:46:39 과 2023-08-19 19:44:12 의 차이 : 328 days 21 hours 57 minutes 33 seconds

1. date1 possible formats = [2022 10 19 19 44 12]
2. date1 count = 1
3. date2 possible formats = [2022 9 10 18 13 28]
4. date2 count = 1
2022-10-19 19:44:12 과 2022-09-10 18:13:28 의 차이 : 39 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 10 19 19 44 12]
2. date1 count = 1
3. date2 possible formats = [2022 9 24 21 46 39]
4. date2 count = 1
2022-10-19 19:44:12 과 2022-09-24 21:46:39 의 차이 : 24 days 21 hours 57 minutes 33 seconds

1. date1 possible formats = []
2. date1 count = 1
3. date2 possible formats = [2022 9 10 18 13 28]
4. date2 count = 1
2022-02-30 23:28:56 과 2022-09-10 18:13:28 의 차이 : date1 impossible

1. date1 possible formats = []
2. date1 count = 1
3. date2 possible formats = [2022 9 10 18 13 28]
4. date2 count = 1
2022-02-28 17:60:41 과 2022-09-10 18:13:28 의 차이 : date1 impossible

1. date1 possible formats = [2022 2 28 9 13 22]
2. date1 count = 1
3. date2 possible formats = []
4. date2 count = 1
2022-02-28 09:13:22 과 2022-09-31 18:13:28 의 차이 : date2 impossible

1. date1 possible formats = [2022 2 28 9 13 22]
2. date1 count = 1
3. date2 possible formats = []
4. date2 count = 1
2022-02-28 09:13:22 과 2022-09-10 25:13:28 의 차이 : date2 impossible

1. date1 possible formats = [2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022/01/10 18:13:28 과 2022-10-19 19:44:12 의 차이 : 282 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022/01/10 18:13:28 과 2022/10/19 19:44:12 의 차이 : 282 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 1 10 18 13 28, 2022 10 1 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
18:13:28 01/10/2022 과 19:44:12 10/19/2022 의 차이 : date1 ambiguous

1. date1 possible formats = [2022 10 1 18 13 28, 2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
18:13:28 10/01/2022 과 19:44:12 19/10/2022 의 차이 : date1 ambiguous

1. date1 possible formats = [2021 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 11 27 19 44 12]
4. date2 count = 1
18:13:28 09/25/2021 과 19:44:12 11/27/2022 의 차이 : 428 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2021 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 11 27 19 44 12]
4. date2 count = 1
18:13:28 25/09/2021 과 19:44:12 27/11/2022 의 차이 : 428 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2024 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2026 11 27 19 44 12]
4. date2 count = 1
18:13:28 09/25/2024 과 19:44:12 11/27/2026 의 차이 : 793 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2024 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2026 11 27 19 44 12]
4. date2 count = 1
18:13:28 25/09/2024 과 19:44:12 27/11/2026 의 차이 : 793 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 1 10 18 13 28, 1328 10 18 20 22 1]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
20220110181328 과 20221019194412 의 차이 : date1 ambiguous

1. date1 possible formats = [2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [1944 12 20 22 10 19, 2022 10 19 19 44 12]
4. date2 count = 1
18132820220110 과 19441220221019 의 차이 : date2 ambiguous

1. date1 possible formats = [2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = [1944 12 10 19 20 22, 2022 10 19 19 44 12]
4. date2 count = 1
18132801102022 과 19441210192022 의 차이 : date2 ambiguous

1. date1 possible formats = [2021 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2021 11 27 19 44 12]
4. date2 count = 1
20210925181328 과 20211127194412 의 차이 : 63 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2021 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [1944 12 20 21 11 27, 2021 11 27 19 44 12]
4. date2 count = 1
18132820210925 과 19441220211127 의 차이 : date2 ambiguous

1. date1 possible formats = [2021 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2021 11 27 19 44 12]
4. date2 count = 1
18132809252021 과 19441211272021 의 차이 : 63 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2024 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2026 11 27 19 44 12]
4. date2 count = 1
20240925181328 과 20261127194412 의 차이 : 793 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2024 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2026 11 27 19 44 12]
4. date2 count = 1
18132820240925 과 19441220261127 의 차이 : 793 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2024 9 25 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2026 11 27 19 44 12]
4. date2 count = 1
18132809252024 과 19441211272026 의 차이 : 793 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 1 10 18 3 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 2]
4. date2 count = 1
2022년 1월 10일 오후 6시 3분 28초 과 2022년 10월 19일 오후 7시 44분 2초 의 차이 : 282 days 1 hour 40 minutes 34 seconds

1. date1 possible formats = [2022 1 10 18 3 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 2]
4. date2 count = 1
2022년 1월 10일 18시 3분 28초 과 2022년 10월 19일 오후 7시 44분 2초 의 차이 : 282 days 1 hour 40 minutes 34 seconds

1. date1 possible formats = [2022 1 10 18 3 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 2]
4. date2 count = 1
2022년 1월 10일 오후 06시 03분 28초 과 2022년 10월 19일 오후 07시 44분 02초 의 차이 : 282 days 1 hour 40 minutes 34 seconds

1. date1 possible formats = [2022 1 10 18 3 28]
2. date1 count = 1
3. date2 possible formats = [2022 10 19 19 44 2]
4. date2 count = 1
2022년 1월 10일 18시 03분 28초 과 2022년 10월 19일 19시 44분 02초 의 차이 : 282 days 1 hour 40 minutes 34 seconds

1. date1 possible formats = [2022 11 11 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 11 12 19 44 12, 2022 12 11 19 44 12]
4. date2 count = 1
18:13:28 11/11/2022 과 19:44:12 11/12/2022 의 차이 : date2 ambiguous

1. date1 possible formats = [2022 11 12 19 44 12, 2022 12 11 19 44 12]
2. date1 count = 1
3. date2 possible formats = [2022 11 11 18 13 28]
4. date2 count = 1
19:44:12 11/12/2022 과 18:13:28 11/11/2022 의 차이 : date1 ambiguous

1. date1 possible formats = [2022 11 11 18 13 28]
2. date1 count = 1
3. date2 possible formats = [2022 11 13 19 44 12]
4. date2 count = 1
18:13:28 11/11/2022 과 19:44:12 11/13/2022 의 차이 : 2 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 11 13 19 44 12]
2. date1 count = 1
3. date2 possible formats = [2022 11 11 18 13 28]
4. date2 count = 1
19:44:12 11/13/2022 과 18:13:28 11/11/2022 의 차이 : 2 days 1 hour 30 minutes 44 seconds

1. date1 possible formats = [2022 10 20 22 10 22, 2022 10 22 20 22 10]
2. date1 count = 1
3. date2 possible formats = [2022 12 31 23 59 59]
4. date2 count = 1
20221020221022 과 20221231235959 의 차이 : date1 ambiguous

1. date1 possible formats = [2022 12 31 23 59 59]
2. date1 count = 1
3. date2 possible formats = [2023 10 20 23 10 23, 2023 10 23 20 23 10]
4. date2 count = 1
20221231235959 과 20231020231023 의 차이 : date2 ambiguous

1. date1 possible formats = [2022 12 31 23 59 59]
2. date1 count = 1
3. date2 possible formats = [2022 12 31 23 59 59]
4. date2 count = 1
20221231235959 과 20221231235959 의 차이 : 0 days 0 hours 0 minutes 0 seconds

1. date1 possible formats = []
2. date1 count = 0
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-01.10 18:13:28 과 2022-10-19 19:44:12 의 차이 : date1 malformed

1. date1 possible formats = [2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = []
4. date2 count = 0
2022-01-10 18:13:28 과 2022-10/19 19:44:12 의 차이 : date2 malformed

1. date1 possible formats = []
2. date1 count = 0
3. date2 possible formats = [2022 10 19 19 44 12]
4. date2 count = 1
2022-01-10 18.13.28 과 2022-10-19 19:44:12 의 차이 : date1 malformed

1. date1 possible formats = [2022 1 10 18 13 28]
2. date1 count = 1
3. date2 possible formats = []
4. date2 count = 0
2022-01-10 18:13:28 과 2022 10 19 19:44:12 의 차이 : date2 malformed
```
