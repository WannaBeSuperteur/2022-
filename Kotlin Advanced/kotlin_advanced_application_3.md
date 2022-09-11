## 요구 사항 분석
워너비슈퍼추어소프트에서 개발한 퍼즐 게임 "워너비퍼즐킹"에는 다음과 같은 게임 시스템이 존재한다.
* ```도전 모드``` : 일정 시간 안에 정해진 개수의 블럭을 제거하는 것을 하나의 게임으로 한, 여러 개의 게임의 집합
* ```vs 모드``` : 다른 플레이어와 정해진 색의 블럭을 일정 개수만큼 얼마나 빨리 제거하는지를 가지고 경쟁하는 모드
* ```보석과의 대결``` : 주어진 미션을 얼마나 빨리 달성하는지를 인공지능을 탑재한 보석 캐릭터와 경쟁하여, 승리하는 경우 일정 개수의 보석을 얻고, 패배하는 경우 잃는 모드

이 게임에는 다음과 같은 업적이 존재하며, 각 업적에는 번호가 매겨져 있다. 단계별로 ```일정 개수 (횟수)```가 서로 다르다.
* ```1. 도전왕``` : 도전 모드의 각 게임의 제한시간의 ```75%``` 이하의 시간으로 클리어한 게임의 개수가 ```일정 개수``` 이상
* ```2. 블럭 제거자``` : 도전 모드 및 vs 모드에서 누적하여 제거한 블럭의 개수가 ```일정 개수``` 이상
* ```3. 블럭 초과 제거자``` : 도전 모드에서 클리어 기준을 초과하여 추가 제거한 블럭의 개수와, vs 모드에서 클리어 기준을 초과하여 추가 제거한 **지정된 색의** 블럭의 개수의 누적 합계가 ```일정 개수``` 이상 (단, 실패하거나 패배한 게임의 경우 추가 제거한 블럭의 개수는 0개)
* ```4. 파죽지세``` : 최근 ```7일``` (=```168시간```) 이내에 도전 모드에서 클리어한 게임의 개수의 최다 기록이 ```일정 개수``` 이상
* ```5. 승리자``` : vs 모드 누적 승률이 ```60%``` 이상이면서 vs 모드 ELO 레이팅이 ```1200점``` 이상인 플레이어를 상대로 이긴 횟수가 누적 ```일정 횟수``` 이상
* ```6. vs의 왕``` : vs 모드에서  ```빨간색 블럭```을 누적 ```일정 개수``` 이상 제거 (단, 제거해야 하는 블럭의 지정된 색이 빨간색인 경우로 한정)
* ```7. 보석을 이기는 자``` : 보석과의 대결 모드에서 파란색 보석 캐릭터 ```Tommy```을 상대로 이긴 횟수가 누적 ```일정 횟수``` 이상
* ```8. 보석이 두려워하는 자``` : 보석과의 대결 모드에서 빨간색 보석 캐릭터 ```Enna```를 상대로 이긴 횟수가 누적 ```일정 횟수``` 이상

당신은 다음의 데이터 클래스와 같은 유저 정보를 토대로, 해당 유저의 업적 레벨이 각 업적별로 몇 단계인지를 구해야 한다. 이를 위해 각 업적별로 현재 달성한 ```개수 (횟수)```를 구하려고 한다.

출력 예시는 다음과 같다. 예를 들어 현재 달성 개수(횟수)가 ```1. 도전왕```이 2개, ```2. 블럭 제거자```가 35개, ```3. 블럭 초과 제거자```가 4개, ```4. 파죽지세```가 2개, ```5. 승리자```와 ```6. vs의 왕```이 각각 1회, 1개, 나머지 7, 8번이 모두 0회일 때를 가정한다.
```kotlin
2 35 4 2 1 1 0 0
```

### 데이터 클래스
```kotlin
data class Player (
    val ID: Int, // 플레이어 ID
    val name: String, // 플레이어 이름
    val wins: Int, // 누적 승리 횟수
    val loses: Int, // 누적 패배 횟수
    val ELO: Int, // 플레이어의 vs 모드 ELO 레이팅
    val challenge_mode_info: List<ChallengeGame>, // 도전 모드 정보
    val vs_mode_info: List<VsGame>, // vs 모드 정보
    val vs_gem_mode_info: List<VsGemsGame>, // 보석과의 대결 모드 정보
    val currentTime: Int // 현재시각의 유닉스 타임스탬프
)

// 도전 모드의 각 게임 정보 (index는 해당 게임의 레벨을 나타냄)
data class ChallengeGame (
    val time_limit: Int, // 제한시간
    val clear_time: Int, // 클리어하는 데 걸린 시간
    val goal: Int, // 제거해야 하는 블럭의 개수
    val blocks: Int, // 제거한 블럭의 개수
    val timestamp: Int // 유닉스 시간 (초 단위)
)

// vs 모드의 각 게임 정보
data class VsGame (
    val opposite_id: Int, // 상대방의 플레이어 ID
    val winner_id: Int, // 승리자의 플레이어 ID
    val color: Int, // 제거해야 하는 블럭의 색 (red:0, yellow:1, green:2, blue:3, purple:4)
    val goal: Int, // 제거해야 하는 지정된 색의 블럭의 개수
    val removed_blocks: List<Int>, // 제거한 블럭의 개수 목록 ([red 제거 개수, yellow 제거 개수, green 제거 개수, blue 제거 개수, purple 제거 개수])
) 

// 보석과의 대결 모드의 각 게임 정보
data class VsGemsGame (
    val gem_name: String, // 대결 상대인 보석의 이름
    val is_win: Boolean // 플레이어의 승리 여부
)
```

## 예제 코드
```kotlin
data class Player (
    val ID: Int, // 플레이어 ID
    val name: String, // 플레이어 이름
    val wins: Int, // 누적 승리 횟수
    val loses: Int, // 누적 패배 횟수
    val ELO: Int, // 플레이어의 vs 모드 ELO 레이팅
    val challenge_mode_info: List<ChallengeGame>, // 도전 모드 정보
    val vs_mode_info: List<VsGame>, // vs 모드 정보
    val vs_gem_mode_info: List<VsGemsGame>, // 보석과의 대결 모드 정보
    val currentTime: Int // 현재시각의 유닉스 타임스탬프
)

// 도전 모드의 각 게임 정보 (index는 해당 게임의 레벨을 나타냄)
data class ChallengeGame (
	val time_limit: Int, // 제한시간
    val clear_time: Int, // 클리어하는 데 걸린 시간
    val goal: Int, // 제거해야 하는 블럭의 개수
    val blocks: Int, // 제거한 블럭의 개수
    val timestamp: Int // 유닉스 시간 (초 단위)
)

// vs 모드의 각 게임 정보
data class VsGame (
    val opposite_id: Int, // 상대방의 플레이어 ID
    val winner_id: Int, // 승리자의 플레이어 ID
    val color: Int, // 제거해야 하는 블럭의 색 (red:0, yellow:1, green:2, blue:3, purple:4)
    val goal: Int, // 제거해야 하는 지정된 색의 블럭의 개수
    val removed_blocks: List<Int>, // 제거한 블럭의 개수 목록 ([red 제거 개수, yellow 제거 개수, green 제거 개수, blue 제거 개수, purple 제거 개수])
) 

// 보석과의 대결 모드의 각 게임 정보
data class VsGemsGame (
    val gem_name: String, // 대결 상대인 보석의 이름
    val is_win: Boolean // 플레이어의 승리 여부
)

// 최근 7일간 클리어한 게임의 최다 기록 구하기
fun getBestWeeklyClearedCount(clearedChallenges: List<ChallengeGame>): Int {
    var i = -1
    var j = 0
    var maxGames = 0
    while (true) {
        i++
        while (j < clearedChallenges.size - 1) {
            if (clearedChallenges[j+1].timestamp - clearedChallenges[i].timestamp <= 7 * 24 * 60 * 60) {
                j++
            } else {
                break
            }
        }
        if (j - i + 1 > maxGames) {
            maxGames = j - i + 1
        }
        println("try to get best record : " + i.toString() + ", " + j.toString())
        
        if (i == clearedChallenges.size - 1 && j == clearedChallenges.size - 1) {
            break
        }
    }
    return maxGames
}

// 플레이어의 업적 정보 반환
fun getAchievementInfoOfPlayer(player: Player, playerInfo: Map<Int, List<Int>>): List<Int> {
    
    // 1. 도전왕
    val achieve1Count = player.challenge_mode_info.filter { it ->
        it.clear_time.toDouble() <= it.time_limit.toDouble() * 0.75
    }.size
    println("1. " + achieve1Count)
    
    // 2. 블럭 제거자 (fold 함수를 이용하여 property를 합산)
    val removedInChallenge = player.challenge_mode_info.fold (0) { acc, e -> acc + e.blocks }
    val removedInVs = player.vs_mode_info.fold (0) { acc, e -> acc + e.removed_blocks.sum() }
    val achieve2Count = removedInChallenge + removedInVs
    println("2. " + achieve2Count)
    
    // 3. 블럭 초과 제거자 (역시 fold 함수를 이용하여 property를 합산)
    val exceedRemovedInChallenge = player.challenge_mode_info.fold (0) { acc, e ->
        acc + Math.max(e.blocks - e.goal, 0)
    }
    val exceedRemovedInVs = player.vs_mode_info.fold (0) { acc, e ->
        acc + Math.max(e.removed_blocks[e.color] - e.goal, 0)
    }
    val achieve3Count = exceedRemovedInChallenge + exceedRemovedInVs
    println("3. " + achieve3Count)
    
    // 4. 파죽지세
    val clearedChallenges = player.challenge_mode_info.filter { it ->
        it.blocks >= it.goal
    }
    val achieve4Count = getBestWeeklyClearedCount(clearedChallenges)
    println("4. " + achieve4Count)
    
    // 5. 승리자
    var achieve5Count = player.vs_mode_info.fold (0) { acc, e ->
        if (e.opposite_id != e.winner_id) { // 본인 승리 여부 검사
            val opposite = playerInfo.get(e.opposite_id)!!
            val opposite_wins = opposite[0].toDouble()
            val opposite_loses = opposite[1].toDouble()
            val opposite_ELO = opposite[2]
            
            println(opposite_wins.toString() + " " + opposite_loses.toString() + " " + opposite_ELO.toString())
            
            if (opposite_wins / (opposite_wins + opposite_loses) >= 0.6 && opposite_ELO >= 1200) {
                acc + 1
            } else {
                acc
            }
        } else {
            acc
        }
    }
    println("5. " + achieve5Count)
    
    // 6. vs의 왕
    var achieve6Count = player.vs_mode_info.fold (0) { acc, e ->
        if (e.color == 0) {
            acc + e.removed_blocks[0]
        } else {
            acc
        }
    }
    println("6. " + achieve6Count)
    
    // 7. 보석을 이기는 자
    val achieve7Count = player.vs_gem_mode_info.filter { it ->
        it.gem_name == "Tommy" && it.is_win
    }.size
    println("7. " + achieve7Count)
    
    // 8. 보석이 두려워하는 자
    val achieve8Count = player.vs_gem_mode_info.filter { it ->
        it.gem_name == "Enna" && it.is_win
    }.size
    println("8. " + achieve8Count)
    
    // 최종 반환
    return listOf(achieve1Count, achieve2Count, achieve3Count, achieve4Count,
                  achieve5Count, achieve6Count, achieve7Count, achieve8Count)
}

fun main() {
    
    // 임시 챌린지 모드 정보
    val challengeModeInfo = listOf<ChallengeGame> (
        ChallengeGame(80, 70, 25, 28, 1662345176),
        ChallengeGame(90, 67, 30, 30, 1663018722),
        ChallengeGame(90, 65, 30, 31, 1663018818),
        ChallengeGame(60, 50, 35, 36, 1663018896),
        ChallengeGame(40, 25, 10, 15, 1663018935),
        ChallengeGame(90, 86, 40, 41, 1663019072),
        ChallengeGame(120, 84, 40, 42, 1663019923),
        ChallengeGame(100, 83, 20, 20, 1665349047),
        ChallengeGame(100, 77, 35, 35, 1665349139),
        ChallengeGame(90, 86, 40, 42, 1665349250),
        ChallengeGame(90, 91, 40, 34, 1665349360) // 클리어하지 못한 게임
    )
    
    // 임시 vs 모드 정보
    val vsModeInfo = listOf<VsGame> (
        VsGame(26, 20, 2, 30, listOf(3, 2, 32, 4, 6)),
        VsGame(28, 28, 1, 36, listOf(3, 30, 7, 1, 5)),
        VsGame(26, 26, 2, 30, listOf(0, 0, 14, 3, 5)),
        VsGame(54, 20, 0, 10, listOf(11, 2, 6, 10, 6)),
        VsGame(61, 20, 3, 40, listOf(3, 8, 2, 44, 6)),
        VsGame(50, 20, 0, 12, listOf(14, 9, 8, 17, 10)),
        VsGame(98, 98, 0, 12, listOf(10, 10, 12, 14, 9))
    )
    
    // 임시 보석과의 대결 모드 정보
    val vsGemsInfo = listOf<VsGemsGame> (
        VsGemsGame("Motiss", true),
        VsGemsGame("Tommy", true),
        VsGemsGame("Tommy", false),
        VsGemsGame("Enna", false),
        VsGemsGame("Tommy", true),
        VsGemsGame("Tommy", true),
        VsGemsGame("Tommy", false),
        VsGemsGame("Elia", true),
        VsGemsGame("Enna", true)
    )
    
    // 임시 유저 정보
    val player1 = Player(20, "hong", 8, 2, 1405, challengeModeInfo, vsModeInfo, vsGemsInfo, 1667232000)
    val player2 = Player(26, "min", 17, 5, 1120, listOf<ChallengeGame>(), listOf<VsGame>(), listOf<VsGemsGame>(), 1667232000)
    val player3 = Player(28, "seok", 12, 6, 1335, listOf<ChallengeGame>(), listOf<VsGame>(), listOf<VsGemsGame>(), 1667232000)
    val player4 = Player(50, "yeon", 13, 10, 1975, listOf<ChallengeGame>(), listOf<VsGame>(), listOf<VsGemsGame>(), 1667232000)
    val player5 = Player(54, "jin", 7, 4, 1201, listOf<ChallengeGame>(), listOf<VsGame>(), listOf<VsGemsGame>(), 1667232000)
    val player6 = Player(61, "su", 10, 6, 1234, listOf<ChallengeGame>(), listOf<VsGame>(), listOf<VsGemsGame>(), 1667232000)
    val player7 = Player(98, "hyuk", 18, 14, 1179, listOf<ChallengeGame>(), listOf<VsGame>(), listOf<VsGemsGame>(), 1667232000)
    
    val players = listOf<Player> (
        player1, player2, player3, player4, player5, player6, player7
    )
    
    // "5. 승리자" 업적 판정용으로 플레이어의 승수, 패수, ELO 레이팅을 미리 저장
    val playerInfo = mutableMapOf<Int, List<Int>>()
    players.forEach { it ->
        playerInfo.put(it.ID, listOf(it.wins, it.loses, it.ELO))
    }
    println("info : " + playerInfo.toString())
    
    // player1의 달성 개수 (횟수) 계산
    val achievementInfoOfPlayer1 = getAchievementInfoOfPlayer(player1, playerInfo)
    println("\n" + achievementInfoOfPlayer1.joinToString(separator=" "))
}
```

## 예제 출력
```kotlin
info : {20=[8, 2, 1405], 26=[17, 5, 1120], 28=[12, 6, 1335], 50=[13, 10, 1975], 54=[7, 4, 1201], 61=[10, 6, 1234], 98=[18, 14, 1179]}
1. 4
2. 680
3. 24
try to get best record : 0, 0
try to get best record : 1, 6
try to get best record : 2, 6
try to get best record : 3, 6
try to get best record : 4, 6
try to get best record : 5, 6
try to get best record : 6, 6
try to get best record : 7, 9
try to get best record : 8, 9
try to get best record : 9, 9
4. 6
17.0 5.0 1120
7.0 4.0 1201
10.0 6.0 1234
13.0 10.0 1975
5. 2
6. 35
7. 3
8. 1

4 680 24 6 2 35 3 1
```
