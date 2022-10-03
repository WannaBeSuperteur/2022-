## 요구사항 분석
워너비슈퍼추어소프트에서 개발한 ```워너비팡```이라는 퍼즐 게임은 같은 동물을 포함한 블럭을 가로, 세로, 또는 대각선으로 3개 이상 연결하면 그 블럭들이 터지는 게임이다. 블럭이 터진 후의 블럭 배치에서도 같은 동물 포함 블럭이 3개 이상 같은 방향으로 연결되면 연쇄적으로 폭발한다.

이 게임의 점수 획득 방식은 다음과 같다.
* ```이동```은 가로 또는 세로로 인접한 서로 다른 2개의 블럭의 위치를 교환하는 것이다.
* ```콤보```는 ```이동```에 의해 같은 종류의 블럭들이 3개 이상 같은 방향으로 연결된 후 한번 터진 다음, 이후의 블럭 배치상에서 특정 종류의 블럭들이 마찬가지로 3개 이상 같은 방향으로 연결되어 터지는 것이다.
* ```그룹```이란, 가로, 세로, 또는 대각선으로 3개 이상 연결된 같은 종류의 블럭의 모임을 말하며, 각 이동에서  예를 들어 다음과 같다.
  * L자 모양으로 5개의 블럭이 터지는 경우, 5개의 블럭이 하나의 그룹에 들어간다.
  * I자, ㅡ자 모양으로 각각 3개의 서로 다른 종류의 블럭이 터지는 경우, 이들 6개의 블럭은 3개씩 서로 다른 2개의 그룹에 들어간다.
* ```이동``` 및 이로 인한 블럭의 폭발에 의해 획득하는 점수는 다음과 같다.
  * ```G```는 해당 이동으로 인해 폭발한 모든 블럭들이 속한 그룹의 총 개수이다.
  * ```콤보```가 없을 시 (현재 누적 2콤보 이상이 아닐 시) 다음과 같이 점수를 획득한다.
    * 각 그룹별로 ```(5 + 2 * G) * (블럭 종류별 가중치) * (폭발한 블럭의 개수) * ((폭발한 블럭의 개수) - 2)```로 계산한 값의 합
  * ```콤보```가 있을 시 (현재 누적 2콤보 이상일 시) 다음과 같이 점수를 획득한다.
    * 각 그룹별로 ```(현재까지의 누적 콤보 수) * (5 + 2 * G) * (블럭 종류별 가중치) * (폭발한 블럭의 개수) * ((폭발한 블럭의 개수) - 2)```로 계산한 값의 합
  * ```블럭 종류별 가중치```는 다음과 같다.
    * ```rabbit```은 ```3```, ```frog```는 ```4```, ```dog```은 ```5```, ```pig```은 ```6```, ```unicorn```은 ```12```

각 이동에 대해서 얻는 점수의 총합을 구하시오.

### 데이터 클래스
```kotlin
// 이동
data class Move (
    val combo: Int, // 현재까지 누적 콤보 수
    val groups: List<Group> // 이동에 의해 폭발하는 블럭들이 속한 그룹의 목록
)

// 블럭의 그룹
data class Group (
    val block_type: String, // 블럭의 종류 (rabbit, frog, dog, pig, unicorn)
    val blocks: Int // 폭발한 블럭의 수
)
```

## 코드 및 출력
### 코드
```kotlin
// 이동
data class Move (
    val combo: Int, // 현재까지 누적 콤보 수
    val groups: List<Group> // 이동에 의해 폭발하는 블럭들이 속한 그룹의 목록
)

// 블럭의 그룹
data class Group (
    val block_type: String, // 블럭의 종류 (rabbit, frog, dog, pig, unicorn)
    val blocks: Int // 폭발한 블럭의 수
)

// 점수 계산 함수
fun getScore(moves: List<Move>): Int {
    var result = 0
    val weightMap = mapOf("rabbit" to 3, "frog" to 4, "dog" to 5, "pig" to 6, "unicorn" to 12)
    
    moves.forEachIndexed { idx, m ->
        val G = m.groups.size
        m.groups.forEach { g ->
            val addScore = m.combo * (5 + 2 * G) * weightMap.get(g.block_type)!! * g.blocks
            result += addScore
            println("combo = ${m.combo}, G = ${G}, type = ${g.block_type}, blocks = ${g.blocks}, score added = ${addScore}")
        }
        println("current index = ${idx}, score = ${result}\n")
    }
    return result
}

fun main() {
    val moves = listOf<Move>(
        Move(1, listOf<Group>(Group("rabbit", 3))),
        Move(1, listOf<Group>(Group("frog", 3), Group("dog", 3))),
        Move(2, listOf<Group>(Group("rabbit", 4))),
        Move(3, listOf<Group>(Group("unicorn", 3))),
        Move(1, listOf<Group>(Group("frog", 5))),
        Move(1, listOf<Group>(Group("pig", 3), Group("pig", 3))),
        Move(2, listOf<Group>(Group("rabbit", 5), Group("frog", 3))),
        Move(1, listOf<Group>(Group("unicorn", 7))),
        Move(1, listOf<Group>(Group("unicorn", 4))),
        Move(2, listOf<Group>(Group("frog", 3), Group("dog", 3))),
        Move(3, listOf<Group>(Group("rabbit", 6), Group("frog", 3), Group("dog", 4))),
        Move(4, listOf<Group>(Group("unicorn", 4)))
    )
    
    // 점수 계산
    println("score: ${getScore(moves)}")
}
```

## 출력
```
combo = 1, G = 1, type = rabbit, blocks = 3, score added = 63
current index = 0, score = 63

combo = 1, G = 2, type = frog, blocks = 3, score added = 108
combo = 1, G = 2, type = dog, blocks = 3, score added = 135
current index = 1, score = 306

combo = 2, G = 1, type = rabbit, blocks = 4, score added = 168
current index = 2, score = 474

combo = 3, G = 1, type = unicorn, blocks = 3, score added = 756
current index = 3, score = 1230

combo = 1, G = 1, type = frog, blocks = 5, score added = 140
current index = 4, score = 1370

combo = 1, G = 2, type = pig, blocks = 3, score added = 162
combo = 1, G = 2, type = pig, blocks = 3, score added = 162
current index = 5, score = 1694

combo = 2, G = 2, type = rabbit, blocks = 5, score added = 270
combo = 2, G = 2, type = frog, blocks = 3, score added = 216
current index = 6, score = 2180

combo = 1, G = 1, type = unicorn, blocks = 7, score added = 588
current index = 7, score = 2768

combo = 1, G = 1, type = unicorn, blocks = 4, score added = 336
current index = 8, score = 3104

combo = 2, G = 2, type = frog, blocks = 3, score added = 216
combo = 2, G = 2, type = dog, blocks = 3, score added = 270
current index = 9, score = 3590

combo = 3, G = 3, type = rabbit, blocks = 6, score added = 594
combo = 3, G = 3, type = frog, blocks = 3, score added = 396
combo = 3, G = 3, type = dog, blocks = 4, score added = 660
current index = 10, score = 5240

combo = 4, G = 1, type = unicorn, blocks = 4, score added = 1344
current index = 11, score = 6584

score: 6584
```
