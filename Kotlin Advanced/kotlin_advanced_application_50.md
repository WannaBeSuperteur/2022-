## 요구사항 분석
```제목```, ```내용```, ```기호```, ```경계선들```, ```간격들```이 주어졌을 때, 다음 조건을 만족시키도록 출력하시오.
 * 위쪽, 아래쪽, 왼쪽, 오른쪽 테두리는 각각 ```경계선들```의 첫 번째, 두 번째, 세 번째, 네 번째 항목으로 채운다.
 * 경계선의 교점은 모두 ```경계선들```의 다섯 번째 항목과 같다.
 * 위쪽 테두리와 제목 텍스트 사이의 세로 간격은 ```간격들```의 첫 번째 항목과 같다.
 * 제목 텍스트는 ```기호```, ```제목```, ```기호를 거꾸로 뒤집은 것```이 차례대로 이어지며, ```기호```와 ```제목``` 사이의 간격은 ```간격들```의 두 번째 항목과 같다.
 * 제목 텍스트와 내용 텍스트의 맨 윗줄 사이의 세로 간격은 ```간격들```의 세 번째 항목과 같다.
 * 제목 텍스트와 ```내용```의 각 줄 중 가장 긴 것과 왼쪽, 오른쪽 테두리 사이의 간격은 ```간격들```의 네 번째 항목과 같다.
 * 내용 텍스트와 아래쪽 테두리 사이의 간격은 ```간격들```의 다섯 번째 항목과 같다.
 * 제목 텍스트와 ```내용```의 각 줄은 모두 가운데 정렬한다. 단, 정확히 가운데로 정렬할 수 없는 경우 가장 가까운 두 곳 중 왼쪽을 기준으로 정렬한다.

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
// 데이터 클래스
data class Info (
    val title: String, // 제목
    val content: List<String>, // 내용
    val symbol: String, // 기호
    val borders: List<Char>, // 경계선들
    val margins: List<Int> // 간격들
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 데이터 클래스
data class Info (
    val title: String, // 제목
    val content: List<String>, // 내용
    val symbol: String, // 기호
    val borders: List<Char>, // 경계선들
    val margins: List<Int> // 간격들
)

// 내용 작성하여 출력하기
fun printContent(info: Info) {
    
    // 가로 길이 구하기 (왼쪽, 오른쪽 경계 제외)
    val titleLen = info.title.length + 2 * info.symbol.length + 2 * info.margins[1]
    
    val maxWidth = Math.max(
        titleLen,
        info.content.maxOf { it.length }
    ) + 2 * info.margins[3]
    
    // 맨 윗줄 출력
    println("${info.borders[4]}${info.borders[0].toString().repeat(maxWidth)}${info.borders[4]}")
    
    // 제목까지 출력
    repeat (info.margins[0]) {
        println("${info.borders[2]}${" ".repeat(maxWidth)}${info.borders[3]}")
    }
    
    val titleMargin = (maxWidth - titleLen) / 2
    val isLeft = (maxWidth % 2) != (titleLen % 2)
    val symbolR = info.symbol.reversed()
    
    val titlePart = if (isLeft) {
        "${info.symbol}${" ".repeat(info.margins[1])}${info.title}${" ".repeat(info.margins[1])}${symbolR} "
    } else {
        "${info.symbol}${" ".repeat(info.margins[1])}${info.title}${" ".repeat(info.margins[1])}${symbolR}"
    }
    println("${info.borders[2]}${" ".repeat(titleMargin)}${titlePart}${" ".repeat(titleMargin)}${info.borders[3]}")
    
    // 내용까지 출력
    repeat (info.margins[2]) {
        println("${info.borders[2]}${" ".repeat(maxWidth)}${info.borders[3]}")
    }
    
    info.content.forEach {
        val lineMargin = (maxWidth - it.length) / 2
        val isLeft = (maxWidth % 2) != (it.length % 2)
        
        val contentPart = if (isLeft) {
            "${it} "
        } else {
            "${it}"
        }
        
        println("${info.borders[2]}${" ".repeat(lineMargin)}${contentPart}${" ".repeat(lineMargin)}${info.borders[3]}")
    }
    
    // 하단 출력
    repeat (info.margins[4]) {
        println("${info.borders[2]}${" ".repeat(maxWidth)}${info.borders[3]}")
    }
    
    println("${info.borders[4]}${info.borders[1].toString().repeat(maxWidth)}${info.borders[4]}")
}

fun main() {
    
    // 임시 데이터 클래스
    val tempInfo = Info (
        "Kotlin Challenger",
        listOf(
            "You are a Kotlin Challenger.",
            "Currently, you MASTERed 50 Kotlin examples.",
            "You can be the master of Kotlin.",
            "Cheer Up!",
            "",
            "2022. 10. 19.",
            "WannaBeSuperteur"
        ),
        "-=#",
        listOf('^', 'V', '<', '>', '#'),
        listOf(1, 4, 2, 8, 3)
    )
    
    printContent(tempInfo)
}
```

### 실행 결과
```kotlin
#^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^#
<                                                           >
<              -=#    Kotlin Challenger    #=-              >
<                                                           >
<                                                           >
<               You are a Kotlin Challenger.                >
<        Currently, you MASTERed 50 Kotlin examples.        >
<             You can be the master of Kotlin.              >
<                         Cheer Up!                         >
<                                                           >
<                       2022. 10. 19.                       >
<                     WannaBeSuperteur                      >
<                                                           >
<                                                           >
<                                                           >
#VVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVV#
```
