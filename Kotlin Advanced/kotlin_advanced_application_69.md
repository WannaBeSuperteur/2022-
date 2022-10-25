## 요구사항 분석
1일의 요일 ```firstDayOfWeek```, 각 날짜의 커밋 양 ```commitCountInfo```가 주어졌을 때, 다음과 같이 커밋의 양을 비교해서 보여주는 함수 ```showCommitCount(firstDayOfWeek, commitCountInfo)```를 작성하시오.
```kotlin
 Su  Mo  Tu  We  Th  Fr  Sa
          1 . 2.  3   4 . 5.
  6   7   8 # 9#[10].11..12.
:13::14: 15 :16:[17] 18  19
 20  21 #22# 23  24 :25: 26
 27 [28][29].30. 31
 ```
 단, 각 날짜의 커밋 양의 최댓값의 ```80%``` 이상은 ```##```, ```60%``` 이상 ```80%``` 미만은 ```[]```, ```35%``` 이상 ```60%``` 미만은 ```::```, ```0%``` 초과 ```35%``` 미만은 ```..```로 표시한다.

## 코드 및 실행 결과
### 코드
```kotlin
// 커밋의 양을 비교해서 보여주기
fun showCommitCount(firstDayOfWeek: String, commitCountInfo: List<Int>) {
    
    // 달력이 들어갈 7 x 6의 영역을 flatten시킨 것 만들기
    val flattenCalendar = (0..41).map { "    " }.toMutableList()
    
    val startIndex = when (firstDayOfWeek) {
        "일", "일요일", "Sun", "Sunday" -> 0
        "월", "월요일", "Mon", "Monday" -> 1
        "화", "화요일", "Tue", "Tuesday" -> 2
        "수", "수요일", "Wed", "Wednesday" -> 3
        "목", "목요일", "Thu", "Thur", "Thursday" -> 4
        "금", "금요일", "Fri", "Friday" -> 5
        "토", "토요일", "Sat", "Saturday" -> 6
        else -> -1
    }
    
    // 달력 채우기
    val maxCount = commitCountInfo.max()
    val days = commitCountInfo.size
    
    val cutlines = listOf(
        Math.ceil(0.35 * maxCount).toInt(), Math.ceil(0.6 * maxCount).toInt(), Math.ceil(0.8 * maxCount).toInt()
    )
    
    (0 until days).map {
        val date = "%2d".format(it + 1)
        
        flattenCalendar[startIndex + it] = when (commitCountInfo[it]) {
            0 -> " ${date} "
            in 1 .. cutlines[0] - 1 -> ".${date}."
            in cutlines[0] .. cutlines[1] - 1 -> ":${date}:"
            in cutlines[1] .. cutlines[2] - 1 -> "[${date}]"
            else -> "#${date}#"
        }
    }
    
    // 달력 출력하기
    println(" Su  Mo  Tu  We  Th  Fr  Sa")
    flattenCalendar.forEachIndexed { idx, it ->
        print(it)
        if (idx % 7 == 6) print("\n")
    }
}

fun main() {
    showCommitCount(
        "Tuesday",
        listOf(
            0, 5, 0, 0, 4, 0, 0, 0, 20, 15,
            6, 2, 7, 11, 0, 8, 12, 0, 0, 0,
            0, 16, 0, 0, 11, 0, 0, 15, 13, 4, 0
        )
    )
    showCommitCount(
        "Saturday",
        listOf(
            0, 1, 2, 3, 4, 5, 6, 7, 8, 9,
            10, 12, 14, 16, 18, 11, 13, 15, 17, 19,
            20, 22, 24, 26, 28, 21, 23, 25, 27, 29
        )
    )
}
```

### 실행 결과
```kotlin
 Su  Mo  Tu  We  Th  Fr  Sa
          1 . 2.  3   4 . 5.
  6   7   8 # 9#[10].11..12.
:13::14: 15 :16:[17] 18  19 
 20  21 #22# 23  24 :25: 26 
 27 [28][29].30. 31         
                            
 Su  Mo  Tu  We  Th  Fr  Sa
                          1 
. 2.. 3.. 4.. 5.. 6.. 7.. 8.
. 9..10..11.:12::13::14:[15]
:16::17::18::19:[20][21][22]
#23##24##25#[26][27]#28##29#
#30#                        
```
