## 요구사항 분석
워너비슈퍼추어소프트에서 개발한 습관 관리 앱 "워너비루틴"은 최근 안드로이드 버전의 구글 플레이 스토어 50만 다운로드를 기념하여 새로운 기능을 준비하려고 한다. 바로 "인공지능을 이용한 맞춤형 응원 캐릭터 추천".

이 기능은 사용자의 습관 기록을 인공지능을 통해 분석한 결과를 서버로부터 받아와서, 그 결과로부터 사용자의 습관 달성률을 극대화할 수 있는 "응원 캐릭터"를 추천해 주는 기능이다.

추천 시에는 다음과 같은 사항들을 고려한다.
* 각 캐릭터별 AI의 추천 점수 : {```이름1```: ```점수1```, ```이름2```: ```점수2```, ..., ```이름N```, ```점수N```} 의 dictionary 형태로 구성된다.
* 각 캐릭터의 매력 요소 : 각 캐릭터별로 ```친근함```, ```귀여움```, ```꼼꼼함```, ```힘이 남```의 4가지로 구성된다. 전체 유저의 각 캐릭터별 평가 값의 평균으로, 서버에서 반환해 주므로 계산할 필요는 없다.
* 유저가 원하는 매력 요소 : 유저가 설정할 수 있는 값으로, ```친근함```, ```귀여움```, ```꼼꼼함```, ```힘이 남```의 4가지 각각에 대한 가중치이다.
* 제외 캐릭터 : 유저가 추천에서 제외한 캐릭터 목록을 나타내는 [```이름1```, ```이름2```, ..., ```이름N```] 형태의 배열이다.

추천 알고리즘은 다음과 같다.
* 1. ```제외 캐릭터```에 해당하는 캐릭터는 일단 제외한다.
* 2. 나머지 캐릭터들 중, ```(AI의 추천 점수) * {(캐릭터의 해당 매력 요소 값) * (유저가 원하는 매력 요소의 해당 값) 의 매력 요소별 합산}```을 계산하여, 해당 점수가 가장 높은 캐릭터를 반환한다.
* 3. 점수가 가장 높은 캐릭터가 여러 개인 경우에는 이들 중 ```(캐릭터의 해당 매력 요소 값) * (유저가 원하는 매력 요소의 해당 값) 의 매력 요소별 합산```의 값이 가장 큰 것을 선택한다.
* 4. 3번에서도 점수가 가장 높은 캐릭터가 여러 개인 경우에는 알파벳순으로 이름이 가장 먼저 오는 캐릭터를 선택한다.

### 데이터 클래스
유저 데이터 클래스는 다음과 같다.

```kotlin
data class User (
    val ID: Int, // 사용자 ID
    val name: String, // 사용자 이름
    val attractions_want: List<Int>, // 유저가 원하는 매력 요소 인덱스별 [0:친근함, 1:귀여움, 2:꼼꼼함, 3:힘이 남]
    val ai_recommend_score: Map<String, Int>, // 각 캐릭터별 AI의 추천 점수
    val do_not_want: List<String> // 제외 캐릭터
)

data class CharacterInfo (
    val user_count: Int, // 캐릭터별 정보에 참여한 유저 수
    val char_info: List<CharacterItem> // 각 캐릭터별 정보
)

data class CharacterItem (
    val name: String, // 캐릭터 이름
    val attractions: List<Int> // 해당 캐릭터의 매력 요소 인덱스별 [0:친근함, 1:귀여움, 2:꼼꼼함, 3:힘이 남]
)
```

## 예제 코드
```kotlin
data class User (
    val ID: Int, // 사용자 ID
    val name: String, // 사용자 이름
    val attractions_want: List<Int>, // 유저가 원하는 매력 요소 인덱스별 [0:친근함, 1:귀여움, 2:꼼꼼함, 3:힘이 남]
    val ai_recommend_score: Map<String, Int>, // 각 캐릭터별 AI의 추천 점수
    val do_not_want: List<String> // 제외 캐릭터
)

data class CharacterInfo (
    val user_count: Int, // 캐릭터별 정보에 참여한 유저 수
    val char_info: List<CharacterItem> // 각 캐릭터별 정보
)

data class CharacterItem (
    val name: String, // 캐릭터 이름
    val attractions: List<Int> // 해당 캐릭터의 매력 요소 인덱스별 [0:친근함, 1:귀여움, 2:꼼꼼함, 3:힘이 남]
)

// 캐릭터 점수 데이터 클래스를 추가로 정의
data class characterScore (
    val name: String, // 캐릭터 이름
    val score: Int, // 캐릭터 점수
    val attractiveScoreSum: Int // (캐릭터의 해당 매력 요소 값) * (유저가 원하는 매력 요소의 해당 값) 의 매력 요소별 합산
)

// 캐릭터 추천 함수
fun recommendCharacter(user: User, characterInfo: CharacterInfo): String {
    
    // 캐릭터 점수 저장용 map (최초: AI의 추천 점수)
    // 변경 가능한 map으로 만들기 위해 toMutableMap() 함수를 이용
    val charScore = user.ai_recommend_score.toMutableMap()
    println("1. ${charScore}")
    
    // 각 캐릭터별 점수를 미리 정의한 데이터 클래스에 추가하기 위해 준비
    var characterScores = mutableListOf<characterScore>()
    
    // 각 캐릭터별 (매력 요소 * 유저가 원하는 매력 요소) 의 점수 계산 및 기존 점수에 곱함
    for (entry in user.ai_recommend_score.entries.iterator()) {
        val attractiveScores = characterInfo.char_info.find {
            it.name == entry.key
        }!!.attractions
        println("2. ${attractiveScores}")
        
        val userAttractiveScoresSum = (0..3).map { it ->
            user.attractions_want[it] * attractiveScores[it]
        }.sum()
        println("3. ${userAttractiveScoresSum}")
        
        // 각 캐릭터별 점수를 미리 정의한 데이터 클래스에 추가
        characterScores.add(
            characterScore(
                entry.key,
                entry.value * userAttractiveScoresSum,
                userAttractiveScoresSum
            )
        )
    }
    println("4. ${charScore}")
    println("5. ${characterScores}")
    
    // 이름순으로 정렬
    characterScores.sortBy { it.name }
    println("6. ${characterScores}")
    
    // 제외 캐릭터에 해당하는 캐릭터를 제외
    characterScores = characterScores.filterNot {
        it.name in user.do_not_want
    }.toMutableList()
    println("7. ${characterScores}")
    
    // 점수가 가장 높은 캐릭터
    val maxScore = characterScores.fold(0) { acc, e ->
        Math.max(acc, e.score)
    }
    characterScores = characterScores.filter {
        it.score == maxScore
    }.toMutableList()
    println("8. ${characterScores}")
    
    // 점수가 가장 높은 캐릭터가 여러 개이면, 이들 중 매력 요소별 합산이 가장 큰 캐릭터
    val maxAttractiveScoreSum = characterScores.fold(0) { acc, e ->
        Math.max(acc, e.attractiveScoreSum)
    }
    characterScores = characterScores.filter {
        it.attractiveScoreSum == maxAttractiveScoreSum
    }.toMutableList()
    println("9. ${characterScores}")
    
    // 그래도 여러 개이면, 가장 먼저 오는 캐릭터
    return characterScores[0].name
}

fun main() {
    
    // 임시 유저
    val user = User(500012,
                    "hong",
                    listOf(30, 15, 40, 90),
                    mapOf("Ann" to 29, "Bob" to 24, "Chris" to 50, "David" to 65, "Elsa" to 99, "Frank" to 50, "Ginny" to 75, "Hestia" to 80),
                    listOf("Bob", "Ginny"))
    
    // 임시 캐릭터 목록
    val characterList = listOf<CharacterItem>(
        CharacterItem("Ann", listOf(60, 95, 45, 40)),
        CharacterItem("Bob", listOf(75, 34, 39, 80)),
        CharacterItem("Chris", listOf(50, 60, 62, 100)),
        CharacterItem("David", listOf(100, 85, 20, 25)),
        CharacterItem("Elsa", listOf(85, 100, 75, 70)),
        CharacterItem("Frank", listOf(100, 92, 92, 90)),
        CharacterItem("Ginny", listOf(52, 54, 60, 39)),
        CharacterItem("Hestia", listOf(96, 94, 100, 85))
    )
    
    // 임시 캐릭터 정보
    val characterInfo = CharacterInfo(3200, characterList)
    
    // 추천 캐릭터 이름 반환
    println("\nrecommended character = ${recommendCharacter(user, characterInfo)}")
}
```

## 예제 코드 출력
```kotlin
1. {Ann=29, Bob=24, Chris=50, David=65, Elsa=99, Frank=50, Ginny=75, Hestia=80}
2. [60, 95, 45, 40]
3. 8625
2. [75, 34, 39, 80]
3. 11520
2. [50, 60, 62, 100]
3. 13880
2. [100, 85, 20, 25]
3. 7325
2. [85, 100, 75, 70]
3. 13350
2. [100, 92, 92, 90]
3. 16160
2. [52, 54, 60, 39]
3. 8280
2. [96, 94, 100, 85]
3. 15940
4. {Ann=29, Bob=24, Chris=50, David=65, Elsa=99, Frank=50, Ginny=75, Hestia=80}
5. [characterScore(name=Ann, score=250125, attractiveScoreSum=8625), characterScore(name=Bob, score=276480, attractiveScoreSum=11520), characterScore(name=Chris, score=694000, attractiveScoreSum=13880), characterScore(name=David, score=476125, attractiveScoreSum=7325), characterScore(name=Elsa, score=1321650, attractiveScoreSum=13350), characterScore(name=Frank, score=808000, attractiveScoreSum=16160), characterScore(name=Ginny, score=621000, attractiveScoreSum=8280), characterScore(name=Hestia, score=1275200, attractiveScoreSum=15940)]
6. [characterScore(name=Ann, score=250125, attractiveScoreSum=8625), characterScore(name=Bob, score=276480, attractiveScoreSum=11520), characterScore(name=Chris, score=694000, attractiveScoreSum=13880), characterScore(name=David, score=476125, attractiveScoreSum=7325), characterScore(name=Elsa, score=1321650, attractiveScoreSum=13350), characterScore(name=Frank, score=808000, attractiveScoreSum=16160), characterScore(name=Ginny, score=621000, attractiveScoreSum=8280), characterScore(name=Hestia, score=1275200, attractiveScoreSum=15940)]
7. [characterScore(name=Ann, score=250125, attractiveScoreSum=8625), characterScore(name=Chris, score=694000, attractiveScoreSum=13880), characterScore(name=David, score=476125, attractiveScoreSum=7325), characterScore(name=Elsa, score=1321650, attractiveScoreSum=13350), characterScore(name=Frank, score=808000, attractiveScoreSum=16160), characterScore(name=Hestia, score=1275200, attractiveScoreSum=15940)]
8. [characterScore(name=Elsa, score=1321650, attractiveScoreSum=13350)]
9. [characterScore(name=Elsa, score=1321650, attractiveScoreSum=13350)]

recommended character = Elsa
```
