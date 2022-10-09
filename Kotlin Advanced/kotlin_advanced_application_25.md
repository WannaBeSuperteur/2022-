## 요구사항 분석
워너비슈퍼추어소프트에서는 특정 앱들에 대해, 승리 시 매일 한번 보상을 획득할 수 있는 간단한 틱택토 게임을 만들려고 한다. 게임판의 각 칸에는 다음과 같이 번호가 매겨진다.
```
1 2 3
4 5 6
7 8 9
```

이 게임의 인공지능은 다음과 같이 작동한다.
* ```option```의 값이 ```0```일 때
  * 이후에 유저가 착수하면 패배하는 곳이 존재하는 경우, 해당하는 곳 중 ```5 > 1 > 3 > 7 > 9 > 2 > 4 > 6 > 8```의 우선 순위가 가장 높은 곳에 착수한다.
  * ```5```가 비어 있으면 ```5```에 착수한다.
  * ```5```가 비어 있지 않고 ```1```, ```3```, ```7```, ```9``` 중 비어 있는 곳이 있으면, 해당하는 곳 주변 3칸 중 유저의 착수 지점이 가장 적은 곳에 착수한다.
    * 이때 우선순위는 ```1 > 3 > 7 > 9```이다.
  * ```1```, ```3```, ```5```, ```7```, ```9``` 중 비어 있는 곳이 없으면 다음과 같이 한다.
    * ```2```, ```4```, ```6```, ```8``` 가운데 비어 있는 곳 중, 주변 5칸 중 유저의 착수 지점이 가장 적은 곳에 착수한다.
    * 이때 우선순위는 ```2 > 4 > 6 > 8```이다.
* ```option```의 값이 ```1```일 때
  * 이후에 유저가 착수하면 패배하는 곳이 존재하는 경우, 해당하는 곳 중 ```1 > 3 > 7 > 9 > 5 > 2 > 4 > 6 > 8```의 우선 순위가 가장 높은 곳에 착수한다.
  * 나머지는 ```option```의 값이 ```0```일 때와 동일하다.
* ```option```의 값이 ```2```일 때
  * 이후에 유저가 착수하면 패배하는 곳이 존재하는 경우, ```option```의 값이 ```1```일 때와 동일하게 착수한다.
  * ```1```, ```3```, ```7```, ```9``` 중 비어 있는 곳이 있으면, 해당하는 곳 주변 3칸 중 유저의 착수 지점이 가장 적은 곳에 착수한다.
    * 이때 우선순위는 ```9 > 7 > 3 > 1```이다.
  * 그렇지 않고 ```5```가 비어 있으면 ```5```에 착수한다.
  * 나머지는 ```option```의 값이 ```0```일 때와 동일하다.

이 게임에서 유저는 항상 다음과 같이 플레이한다.
* 이후에 AI가 착수하면 유저가 패배하는 곳이 존재하는 경우, 해당하는 곳 중 우선 순위가 가장 높은 곳에 착수한다.
* 그렇지 않은 경우, ```유저 착수 위치 우선 순위```의 순서에 따라 착수 가능한 곳 중 우선 순위가 가장 높은 곳에 착수한다.

유저의 착수 우선 순위에 따라 유저가 승리하는지, 아니면 AI가 승리하는지를 출력하시오. (단, 첫 착수는 유저가 한다.)

### 데이터 클래스
```kotlin
// 게임 정보
data class Game (
    val ai_option: Int, // 인공지능 적용 옵션
    val user_priority: List<Int> // 유저의 착수 우선순위
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 게임 정보
data class Game (
    val ai_option: Int, // 인공지능 적용 옵션
    val user_priority: List<Int> // 유저의 착수 우선순위
)

// 해당하는 지점에 모두 A가 착수했는지 검사
fun isAllPlaced(board: List<Char>, places: List<Int>, A: Char): Boolean {
    return places.all {
        board[it - 1] == A
    }
}

// A가 착수하면 B가 패배하게 되는 지점들의 집합이 여러 개일 때, 하나라도 해당 지점들에 모두 A가 착수했는지 검사
fun isAllPlacedAny(board: List<Char>, places: List<List<Int>>, A: Char): Boolean {
    return places.any {
        isAllPlaced(board, it, A)
    }
}

// A가 착수하면 B가 패배하는 곳을 반환
fun findUrgent(board: List<Char>, A: Char): List<Int> {
    val emptyPlaces = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9).filter {
        board[it - 1] == ' '
    }
    return emptyPlaces.filter {
        when (it) {
            1 -> isAllPlacedAny(board, listOf(listOf(2, 3), listOf(4, 7), listOf(5, 9)), A)
            2 -> isAllPlacedAny(board, listOf(listOf(1, 3), listOf(5, 8)), A)
            3 -> isAllPlacedAny(board, listOf(listOf(1, 2), listOf(5, 7), listOf(6, 9)), A)
            4 -> isAllPlacedAny(board, listOf(listOf(1, 7), listOf(5, 6)), A)
            5 -> isAllPlacedAny(board, listOf(listOf(1, 9), listOf(2, 8), listOf(3, 7), listOf(4, 6)), A)
            6 -> isAllPlacedAny(board, listOf(listOf(3, 9), listOf(4, 5)), A)
            7 -> isAllPlacedAny(board, listOf(listOf(1, 4), listOf(3, 5), listOf(8, 9)), A)
            8 -> isAllPlacedAny(board, listOf(listOf(2, 5), listOf(7, 9)), A)
            9 -> isAllPlacedAny(board, listOf(listOf(3, 6), listOf(1, 5), listOf(7, 8)), A)
            else -> false
        }
    }
}

// 주변 8칸의 목록 반환
fun findNearbyPlaces(place: Int): List<Int> {
    return when (place) {
        1 -> listOf(2, 4, 5)
        2 -> listOf(1, 3, 4, 5, 6)
        3 -> listOf(2, 5, 6)
        4 -> listOf(1, 2, 5, 7, 8)
        6 -> listOf(2, 3, 5, 8, 9)
        7 -> listOf(4, 5, 8)
        8 -> listOf(4, 5, 6, 7, 9)
        9 -> listOf(5, 6, 8)
        else -> listOf()
    }
}

// 주변 8칸의 유저의 착수 지점 개수가 적은 순으로 정렬
fun sortByNearbyUserPlaces(priority: List<Int>, board: List<Char>): List<Int> {
    println("[AI] priority    : ${priority}")
    val result = priority.sortedBy {
        findNearbyPlaces(it).filter { board[it - 1] == 'O' }.count()
    }
    println("[AI] sort result : ${result}")
    return result
}

// 승리 판정
fun checkAllSame(board: List<Char>, indices: List<Int>): Char {
    val mapping = indices.map { board[it - 1] }
    
    if (mapping.all { it == mapping[0] }) {
        return mapping[0]
    } else {
        return ' '
    }
}

fun checkVictory(board: List<Char>): Char {
    val indicesList = listOf(
        listOf(1, 2, 3), listOf(4, 5, 6), listOf(7, 8, 9),
        listOf(1, 4, 7), listOf(2, 5, 8), listOf(3, 6, 9),
        listOf(1, 5, 9), listOf(3, 5, 7)
    )
    
    indicesList.forEach {
        val cas = checkAllSame(board, it)
        if (cas != ' ') {
            return cas
        }
    }
    
    return ' '
}

// 게임 시뮬레이션
fun simulateGame(game: Game): Char {
    val aiOption = game.ai_option
    val userPriority = game.user_priority
    val board = (0..8).map {' '}.toMutableList()
    
    (0..8).forEach {
        val isUserTurn = it % 2 == 0
        var finished = false
        val oddPlaced = listOf(1, 3, 5, 7, 9).count {
            board[it - 1] != ' '
        }
        
        // 유저 (O) 의 차례
        if (isUserTurn) {
            val userUrgent = findUrgent(board, 'X')
            
            if (userUrgent.size > 0) { // 다음에 착수하지 않으면 패배 시
                userPriority.forEach {
                    if (it in userUrgent && !finished) {
                        board[it - 1] = 'O'
                        finished = true
                    }
                }
            } else {
                userPriority.forEach {
                    if (!finished && board[it - 1] == ' ') {
                        board[it - 1] = 'O'
                        finished = true
                    }
                }
            }
        }
        
        // AI (X) 의 차례
        else {
            val aiUrgent = findUrgent(board, 'O')
            
            if (aiUrgent.size > 0) { // 다음에 착수하지 않으면 패배 시
                val urgentPriority = when (aiOption) {
                    0 -> listOf(5, 1, 3, 7, 9, 2, 4, 6, 8)
                    else -> listOf(1, 3, 7, 9, 5, 2, 4, 6, 8)
                }
                urgentPriority.forEach {
                    if (it in aiUrgent && !finished) {
                        board[it - 1] = 'X'
                        finished = true
                    }
                }
            } else {
                
                // 1, 3, 5, 7, 9 중 착수되지 않은 곳이 있음
                // -> 1, 3, 5, 7, 9 중 비어 있는 곳에 착수하는 방법이 서로 다름
                if (oddPlaced < 5) {
                    if (aiOption == 0 || aiOption == 1) {
                        val cornerPrority = sortByNearbyUserPlaces(listOf(1, 3, 7, 9), board)
                    
                        if (board[4] == ' ') {
                            board[4] = 'X'
                            finished = true
                        } else if (cornerPrority.any { board[it - 1] == ' ' }) {
                            cornerPrority.forEach {
                                if (!finished) {
                                    board[it - 1] = 'X'
                                    finished = true
                                }
                            }
                        }
                    } else {
                        val cornerPrority = sortByNearbyUserPlaces(listOf(9, 7, 3, 1), board)
                    
                        if (cornerPrority.any { board[it - 1] == ' ' }) {
                            cornerPrority.forEach {
                                if (!finished) {
                                    board[it - 1] = 'X'
                                    finished = true
                                }
                            }
                        } else if (board[4] == ' ') {
                            board[4] = 'X'
                            finished = true
                        }
                    }
                }
                
                // 1, 3, 5, 7, 9가 모두 착수됨 -> 2, 4, 6, 8 중 비어 있는 곳에 착수
                else {
                    val evenPriority = sortByNearbyUserPlaces(listOf(2, 4, 6, 8), board)
                    evenPriority.forEach {
                        if (!finished && board[it - 1] == ' ') {
                            board[it - 1] = 'X'
                            finished = true
                        }
                    }
                }
            }
        }
        
        // 보드 출력
        println("turn ${it}, ${board.take(3)}, ${board.take(6).takeLast(3)}, ${board.takeLast(3)}, user=${isUserTurn}")
        
        // 승리 판정
        val victory = checkVictory(board)
        if (victory != ' ') {
            println("${victory}의 승리!!")
            return victory
        }
    }
    
    // 무승부
    return ' '
}

fun main() {
    val games = listOf<Game> (
        Game(0, listOf(5, 1, 3, 7, 9, 2, 4, 6, 8)),
        Game(1, listOf(5, 1, 3, 7, 9, 2, 4, 6, 8)),
        Game(2, listOf(5, 1, 3, 7, 9, 2, 4, 6, 8)),
        Game(0, listOf(1, 5, 3, 2, 7, 9, 6, 8, 4)),
        Game(1, listOf(1, 5, 3, 2, 7, 9, 6, 8, 4)),
        Game(2, listOf(1, 5, 3, 2, 7, 9, 6, 8, 4)),
        Game(0, listOf(5, 4, 6, 8, 2, 1, 7, 3, 9)),
        Game(1, listOf(5, 4, 6, 8, 2, 1, 7, 3, 9)),
        Game(2, listOf(5, 4, 6, 8, 2, 1, 7, 3, 9)),
        Game(0, listOf(2, 4, 8, 6, 1, 9, 3, 7, 5)),
        Game(1, listOf(2, 4, 8, 6, 1, 9, 3, 7, 5)),
        Game(2, listOf(2, 4, 8, 6, 1, 9, 3, 7, 5))
    )
    
    games.forEachIndexed { idx, g ->
        val simulationResult = simulateGame(g)
        val winner = if (simulationResult == ' ') {
            "무승부"
        } else {
            simulationResult
        }
        
        println("게임 ${idx} 승리 : ${winner}\n")
    }
}
```

### 실행 결과
```kotlin
turn 0, [ ,  ,  ], [ , O,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 1, [X,  ,  ], [ , O,  ], [ ,  ,  ], user=false
turn 2, [X,  , O], [ , O,  ], [ ,  ,  ], user=true
turn 3, [X,  , O], [ , O,  ], [X,  ,  ], user=false
turn 4, [X,  , O], [O, O,  ], [X,  ,  ], user=true
turn 5, [X,  , O], [O, O, X], [X,  ,  ], user=false
turn 6, [X,  , O], [O, O, X], [X,  , O], user=true
[AI] priority    : [2, 4, 6, 8]
[AI] sort result : [4, 2, 6, 8]
turn 7, [X, X, O], [O, O, X], [X,  , O], user=false
turn 8, [X, X, O], [O, O, X], [X, O, O], user=true
게임 0 승리 : 무승부

turn 0, [ ,  ,  ], [ , O,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 1, [X,  ,  ], [ , O,  ], [ ,  ,  ], user=false
turn 2, [X,  , O], [ , O,  ], [ ,  ,  ], user=true
turn 3, [X,  , O], [ , O,  ], [X,  ,  ], user=false
turn 4, [X,  , O], [O, O,  ], [X,  ,  ], user=true
turn 5, [X,  , O], [O, O, X], [X,  ,  ], user=false
turn 6, [X,  , O], [O, O, X], [X,  , O], user=true
[AI] priority    : [2, 4, 6, 8]
[AI] sort result : [4, 2, 6, 8]
turn 7, [X, X, O], [O, O, X], [X,  , O], user=false
turn 8, [X, X, O], [O, O, X], [X, O, O], user=true
게임 1 승리 : 무승부

turn 0, [ ,  ,  ], [ , O,  ], [ ,  ,  ], user=true
[AI] priority    : [9, 7, 3, 1]
[AI] sort result : [9, 7, 3, 1]
turn 1, [ ,  ,  ], [ , O,  ], [ ,  , X], user=false
turn 2, [O,  ,  ], [ , O,  ], [ ,  , X], user=true
[AI] priority    : [9, 7, 3, 1]
[AI] sort result : [9, 7, 3, 1]
turn 3, [O,  ,  ], [ , O,  ], [ ,  , X], user=false
turn 4, [O,  , O], [ , O,  ], [ ,  , X], user=true
turn 5, [O,  , O], [ , O,  ], [X,  , X], user=false
turn 6, [O,  , O], [ , O,  ], [X, O, X], user=true
turn 7, [O, X, O], [ , O,  ], [X, O, X], user=false
turn 8, [O, X, O], [O, O,  ], [X, O, X], user=true
게임 2 승리 : 무승부

turn 0, [O,  ,  ], [ ,  ,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 1, [O,  ,  ], [ , X,  ], [ ,  ,  ], user=false
turn 2, [O,  , O], [ , X,  ], [ ,  ,  ], user=true
turn 3, [O, X, O], [ , X,  ], [ ,  ,  ], user=false
turn 4, [O, X, O], [ , X,  ], [ , O,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 5, [X, X, O], [ , X,  ], [ , O,  ], user=false
turn 6, [X, X, O], [ , X,  ], [ , O, O], user=true
turn 7, [X, X, O], [ , X,  ], [X, O, O], user=false
turn 8, [X, X, O], [O, X,  ], [X, O, O], user=true
게임 3 승리 : 무승부

turn 0, [O,  ,  ], [ ,  ,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 1, [O,  ,  ], [ , X,  ], [ ,  ,  ], user=false
turn 2, [O,  , O], [ , X,  ], [ ,  ,  ], user=true
turn 3, [O, X, O], [ , X,  ], [ ,  ,  ], user=false
turn 4, [O, X, O], [ , X,  ], [ , O,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 5, [X, X, O], [ , X,  ], [ , O,  ], user=false
turn 6, [X, X, O], [ , X,  ], [ , O, O], user=true
turn 7, [X, X, O], [ , X,  ], [X, O, O], user=false
turn 8, [X, X, O], [O, X,  ], [X, O, O], user=true
게임 4 승리 : 무승부

turn 0, [O,  ,  ], [ ,  ,  ], [ ,  ,  ], user=true
[AI] priority    : [9, 7, 3, 1]
[AI] sort result : [9, 7, 3, 1]
turn 1, [O,  ,  ], [ ,  ,  ], [ ,  , X], user=false
turn 2, [O,  ,  ], [ , O,  ], [ ,  , X], user=true
[AI] priority    : [9, 7, 3, 1]
[AI] sort result : [9, 7, 3, 1]
turn 3, [O,  ,  ], [ , O,  ], [ ,  , X], user=false
turn 4, [O,  , O], [ , O,  ], [ ,  , X], user=true
turn 5, [O,  , O], [ , O,  ], [X,  , X], user=false
turn 6, [O,  , O], [ , O,  ], [X, O, X], user=true
turn 7, [O, X, O], [ , O,  ], [X, O, X], user=false
turn 8, [O, X, O], [ , O, O], [X, O, X], user=true
게임 5 승리 : 무승부

turn 0, [ ,  ,  ], [ , O,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 1, [X,  ,  ], [ , O,  ], [ ,  ,  ], user=false
turn 2, [X,  ,  ], [O, O,  ], [ ,  ,  ], user=true
turn 3, [X,  ,  ], [O, O, X], [ ,  ,  ], user=false
turn 4, [X,  ,  ], [O, O, X], [ , O,  ], user=true
turn 5, [X, X,  ], [O, O, X], [ , O,  ], user=false
turn 6, [X, X, O], [O, O, X], [ , O,  ], user=true
turn 7, [X, X, O], [O, O, X], [X, O,  ], user=false
turn 8, [X, X, O], [O, O, X], [X, O, O], user=true
게임 6 승리 : 무승부

turn 0, [ ,  ,  ], [ , O,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [1, 3, 7, 9]
turn 1, [X,  ,  ], [ , O,  ], [ ,  ,  ], user=false
turn 2, [X,  ,  ], [O, O,  ], [ ,  ,  ], user=true
turn 3, [X,  ,  ], [O, O, X], [ ,  ,  ], user=false
turn 4, [X,  ,  ], [O, O, X], [ , O,  ], user=true
turn 5, [X, X,  ], [O, O, X], [ , O,  ], user=false
turn 6, [X, X, O], [O, O, X], [ , O,  ], user=true
turn 7, [X, X, O], [O, O, X], [X, O,  ], user=false
turn 8, [X, X, O], [O, O, X], [X, O, O], user=true
게임 7 승리 : 무승부

turn 0, [ ,  ,  ], [ , O,  ], [ ,  ,  ], user=true
[AI] priority    : [9, 7, 3, 1]
[AI] sort result : [9, 7, 3, 1]
turn 1, [ ,  ,  ], [ , O,  ], [ ,  , X], user=false
turn 2, [ ,  ,  ], [O, O,  ], [ ,  , X], user=true
turn 3, [ ,  ,  ], [O, O, X], [ ,  , X], user=false
turn 4, [ ,  , O], [O, O, X], [ ,  , X], user=true
turn 5, [ ,  , O], [O, O, X], [X,  , X], user=false
turn 6, [ ,  , O], [O, O, X], [X, O, X], user=true
turn 7, [ , X, O], [O, O, X], [X, O, X], user=false
turn 8, [O, X, O], [O, O, X], [X, O, X], user=true
게임 8 승리 : 무승부

turn 0, [ , O,  ], [ ,  ,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [7, 9, 1, 3]
turn 1, [ , O,  ], [ , X,  ], [ ,  ,  ], user=false
turn 2, [ , O,  ], [O, X,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [9, 3, 7, 1]
turn 3, [ , O,  ], [O, X,  ], [ ,  , X], user=false
turn 4, [O, O,  ], [O, X,  ], [ ,  , X], user=true
turn 5, [O, O, X], [O, X,  ], [ ,  , X], user=false
turn 6, [O, O, X], [O, X, O], [ ,  , X], user=true
turn 7, [O, O, X], [O, X, O], [X,  , X], user=false
X의 승리!!
게임 9 승리 : X

turn 0, [ , O,  ], [ ,  ,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [7, 9, 1, 3]
turn 1, [ , O,  ], [ , X,  ], [ ,  ,  ], user=false
turn 2, [ , O,  ], [O, X,  ], [ ,  ,  ], user=true
[AI] priority    : [1, 3, 7, 9]
[AI] sort result : [9, 3, 7, 1]
turn 3, [ , O,  ], [O, X,  ], [ ,  , X], user=false
turn 4, [O, O,  ], [O, X,  ], [ ,  , X], user=true
turn 5, [O, O, X], [O, X,  ], [ ,  , X], user=false
turn 6, [O, O, X], [O, X, O], [ ,  , X], user=true
turn 7, [O, O, X], [O, X, O], [X,  , X], user=false
X의 승리!!
게임 10 승리 : X

turn 0, [ , O,  ], [ ,  ,  ], [ ,  ,  ], user=true
[AI] priority    : [9, 7, 3, 1]
[AI] sort result : [9, 7, 3, 1]
turn 1, [ , O,  ], [ ,  ,  ], [ ,  , X], user=false
turn 2, [ , O,  ], [O,  ,  ], [ ,  , X], user=true
[AI] priority    : [9, 7, 3, 1]
[AI] sort result : [9, 7, 3, 1]
turn 3, [ , O,  ], [O,  ,  ], [ ,  , X], user=false
turn 4, [ , O,  ], [O,  ,  ], [ , O, X], user=true
turn 5, [ , O,  ], [O, X,  ], [ , O, X], user=false
turn 6, [O, O,  ], [O, X,  ], [ , O, X], user=true
turn 7, [O, O, X], [O, X,  ], [ , O, X], user=false
turn 8, [O, O, X], [O, X, O], [ , O, X], user=true
게임 11 승리 : 무승부
```
