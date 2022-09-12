## 요구사항 분석
워너비슈퍼추어소프트에서 개발한 개발팀 육성 시뮬레이션 게임 "워너비디벨로퍼즈"에서는 각 유저가 자신이 보유한 개발자들을 투입하여 다른 유저와 경쟁할 수 있는 "대결 모드"가 있다. 대결 모드의 규칙은 다음과 같다.
* 자신이 보유한 개발자들의 능력은 ```깔끔한 코딩력```, ```알고리즘 역량```, ```팀워크 능력```, ```분위기 메이킹 능력```, ```반짝반짝 창의력```의 5가지 역량 각각에 대해서 수치화된다.
* 5가지 역량 중 수치화된 점수가 가장 높은 능력이 무엇인지에 따라 다음과 같이 적절한 종목에 투입된다.
  * 점수가 가장 높은 능력이 ```깔끔한 코딩력```인 경우, ```1. 리팩토링 대결``` 종목에 투입
  * 점수가 가장 높은 능력이 ```알고리즘 역량```인 경우, ```2. 코딩 테스트``` 종목에 투입
  * 점수가 가장 높은 능력이 ```팀워크 능력```인 경우, ```3. 하나가 되는 순간``` 종목에 투입
  * 점수가 가장 높은 능력이 ```분위기 메이킹 능력```인 경우, ```4. 100만 유저 가즈아~!``` 종목에 투입
  * 점수가 가장 높은 능력이 ```반짝반짝 창의력```인 경우, ```5. 아이디어 대격돌``` 종목에 투입
  * 점수가 가장 높은 능력이 여러 개인 경우는 이들 능력에 맞는 종목 중 가장 앞 번호의 종목에 투입된다.
* 각 종목별 ```전체 역량```은 다음과 같이 계산한다.
  * 해당 종목에 투입된 개발자가 ```4명 이상```인 경우, 해당 종목에 투입된 개발자들 중 해당 종목에 대응되는 역량을 수치화한 점수가 상위 4위 이내에 해당하는 개발자들의 이들 점수의 제곱의 합과 같다.
  * 해당 종목에 투입된 개발자가 ```4명 미만```인 경우, 다음의 합계와 같다.
    * 해당 종목에 투입된 개발자들의 해당 종목에 대응되는 역량을 수치화한 점수의 제곱의 합
    * ```(4 - (해당 종목에 투입된 개발자의 수)) * 25```의 값
* 각 종목별 ```승리 확률```은 다음과 같이 계산한다.
  * (자신의 팀의 해당 종목의 ```전체 역량```의 제곱) / { (자신의 팀의 해당 종목의 ```전체 역량```의 제곱) + (상대방 팀의 해당 종목의 ```전체 역량```의 제곱) }
* ```최종 승리 확률```은 다음과 같이 계산한다.
  * (자신의 팀의 각 종목별 ```승리 확률```의 제곱의 합) / { (자신의 팀의 각 종목별 ```승리 확률```의 제곱의 합) + (상대방 팀의 각 종목별 ```승리 확률```의 제곱의 합) }
* 단, 역량이 매우 낮아서 해당 개발자가 없는 것이 더 나은 정도라면 해고되거나 적어도 좌천되기 때문에, 개발자의 각 역량을 수치화한 점수의 값은 항상 ```6 이상```이다.

아래 데이터 클래스를 이용하여, 자신과 상대방의 스펙이 주여졌을 때 대결 모드에서의 ```최종 승리 확률```을 계산하시오.

### 데이터 클래스
```kotlin
// 자신과 상대방에 대한 정보
data class BattleInfo (
    val me: User, // 자신
    val opposite: User // 상대방
)

// 사용자 정보
data class User (
    val user_id: Int, // 사용자 ID
    val name: String, // 사용자 이름
    val developers: List<Developer> // 보유한 개발자들
)

// 개발자 정보
data class Developer (
    val dev_id: Int, // 개발자 ID
    val dev_name: String, // 개발자 이름
    val ability_scores: List<Int> // 능력치 점수 배열, [깔끔한 코딩력, 알고리즘 역량, 팀워크 능력, 분위기 메이킹 능력, 반짝반짝 창의력]
)
```

## 예제 코드
```kotlin
// 자신과 상대방에 대한 정보
data class BattleInfo (
    val me: User, // 자신
    val opposite: User // 상대방
)

// 사용자 정보
data class User (
    val user_id: Int, // 사용자 ID
    val name: String, // 사용자 이름
    val developers: List<Developer> // 보유한 개발자들
)

// 개발자 정보
data class Developer (
    val dev_id: Int, // 개발자 ID
    val dev_name: String, // 개발자 이름
    val ability_scores: List<Int> // 능력치 점수 배열, [깔끔한 코딩력, 알고리즘 역량, 팀워크 능력, 분위기 메이킹 능력, 반짝반짝 창의력]
)

// 유저의 종목별 전체 역량 계산
fun getTotalAbility(user: User): List<Int> {
    
    // 각 종목에 투입될 개발자의 그룹 구하기
    val groupsByAbility = user.developers.groupBy {
        with (it.ability_scores) {
            this.indexOf ( this.max() )
        }
    }
    
    println("\n1. group by ability, user id = ${user.user_id}")
    groupsByAbility.forEach {
        println("${it.key}, ${it.value}")
    }
    
    // 역량의 내림차순으로 각 그룹을 정렬
    // 향후 정렬 가능하도록 MutableList를 이용
    val newGroupsByAbility = mutableMapOf<Int, List<Developer>>()
    groupsByAbility.forEach {
        val sortedDevs = it.value.toMutableList().sortedByDescending { dev ->
            dev.ability_scores[it.key]
        }
        newGroupsByAbility.put(it.key, sortedDevs)
    }
    
    println("\n2. group by ability, user id = ${user.user_id}")
    newGroupsByAbility.forEach {
        println("${it.key}, ${it.value}")
    }
    
    // 종목별 전체 역량 계산
    val totalAbility = (0..4).flatMap { it ->
        listOf (
            newGroupsByAbility.get(it)!!.take(4).sumOf { dev ->
                dev.ability_scores[it] * dev.ability_scores[it]
            } + Math.max(0, 4 - newGroupsByAbility.get(it)!!.size) * 25
        )
    }
    println("\n3. ${totalAbility}")
    
    return totalAbility
}

// 자신의 최종 승리 확률 계산
fun getFinalWinProb(me: User, opposite: User): Double {
    
    // 종목별 전체 역량 계산
    val myTotalAbility = getTotalAbility(me)
    val oppositeTotalAbility = getTotalAbility(opposite)
    
    println("\n4. ${myTotalAbility}")
    println("5. ${oppositeTotalAbility}")
    
    // 종목별 전체 역량의 제곱 계산
    val myTotalSquare = (0..4).map { myTotalAbility[it] * myTotalAbility[it] }
    val oppositeTotalSquare = (0..4).map { oppositeTotalAbility[it] * oppositeTotalAbility[it] }
    
    println("6. ${myTotalSquare}")
    println("7. ${oppositeTotalSquare}")
    
    // 종목별 승리 확률 계산
    val myWinProb = (0..4).map {
        myTotalSquare[it] / (myTotalSquare[it] + oppositeTotalSquare[it]).toDouble()
    }
    val oppositeWinProb = (0..4).map {
        oppositeTotalSquare[it] / (myTotalSquare[it] + oppositeTotalSquare[it]).toDouble()
    }
    
    println("8. ${myWinProb}")
    println("9. ${oppositeWinProb}")
    
    // 각 종목별 승리 확률의 제곱의 합 및 이를 이용한 최종 승리 확률 계산
    val sumMyWinProbSquare = myWinProb.map { it -> it * it }.sum()
    val sumOppositeWinProbSqaure = oppositeWinProb.map { it -> it * it }.sum()
    val myFinalWinProb = sumMyWinProbSquare / (sumMyWinProbSquare + sumOppositeWinProbSqaure)
    
    println("10. ${sumMyWinProbSquare}")
    println("11. ${sumOppositeWinProbSqaure}")
    
    return myFinalWinProb
}

fun main() {
    
    // 임시 유저 정보
    val tempMe = User (
        1372, "hong",
        listOf<Developer> (
            Developer(10, "Bob", listOf(20, 10, 15, 15, 10)),
            Developer(11, "Andy", listOf(20, 20, 15, 15, 10)),
            Developer(22, "Minsu", listOf(15, 20, 10, 15, 15)),
            Developer(26, "Chris", listOf(10, 15, 10, 20, 20)),
            Developer(30, "Elsa", listOf(10, 10, 15, 10, 15)),
            Developer(31, "Anna", listOf(10, 10, 10, 15, 20)),
            Developer(32, "Kelly", listOf(15, 10, 20, 15, 15)),
            Developer(90, "Yulia", listOf(15, 10, 15, 15, 15)),
            Developer(92, "Hong", listOf(15, 20, 20, 15, 10)),
            Developer(93, "Kinda", listOf(20, 20, 15, 15, 20)),
            Developer(95, "Backboy", listOf(15, 15, 15, 15, 15)),
            Developer(99, "Taejun", listOf(10, 20, 20, 15, 20))
        )
    )
    
    val tempOpposite = User (
        1403, "juk",
        listOf<Developer> (
            Developer(13, "Baehyun", listOf(10, 20, 20, 10, 10)),
            Developer(14, "Jeong", listOf(15, 10, 15, 20, 15)),
            Developer(72, "Hyein", listOf(20, 20, 10, 20, 15)),
            Developer(73, "Chanho", listOf(15, 15, 20, 20, 15)),
            Developer(82, "Elia", listOf(15, 10, 15, 15, 15)),
            Developer(83, "Frank", listOf(10, 15, 15, 15, 20)),
            Developer(89, "Gazua", listOf(20, 10, 10, 15, 15)),
            Developer(100, "Sparta", listOf(15, 15, 15, 10, 10)),
            Developer(112, "Hillton", listOf(10, 10, 15, 20, 10)),
            Developer(113, "King", listOf(20, 20, 20, 15, 20)),
            Developer(120, "Jack", listOf(20, 20, 20, 20, 15)),
            Developer(124, "Miranda", listOf(15, 15, 15, 10, 20))
        )
    )
    
    // 자신의 최종 승리 확률 출력
    println("\nfinal win probability : ${getFinalWinProb(tempMe, tempOpposite)}")
}
```

## 예제 출력
```kotlin
1. group by ability, user id = 1372
0, [Developer(dev_id=10, dev_name=Bob, ability_scores=[20, 10, 15, 15, 10]), Developer(dev_id=11, dev_name=Andy, ability_scores=[20, 20, 15, 15, 10]), Developer(dev_id=90, dev_name=Yulia, ability_scores=[15, 10, 15, 15, 15]), Developer(dev_id=93, dev_name=Kinda, ability_scores=[20, 20, 15, 15, 20]), Developer(dev_id=95, dev_name=Backboy, ability_scores=[15, 15, 15, 15, 15])]
1, [Developer(dev_id=22, dev_name=Minsu, ability_scores=[15, 20, 10, 15, 15]), Developer(dev_id=92, dev_name=Hong, ability_scores=[15, 20, 20, 15, 10]), Developer(dev_id=99, dev_name=Taejun, ability_scores=[10, 20, 20, 15, 20])]
3, [Developer(dev_id=26, dev_name=Chris, ability_scores=[10, 15, 10, 20, 20])]
2, [Developer(dev_id=30, dev_name=Elsa, ability_scores=[10, 10, 15, 10, 15]), Developer(dev_id=32, dev_name=Kelly, ability_scores=[15, 10, 20, 15, 15])]
4, [Developer(dev_id=31, dev_name=Anna, ability_scores=[10, 10, 10, 15, 20])]

2. group by ability, user id = 1372
0, [Developer(dev_id=10, dev_name=Bob, ability_scores=[20, 10, 15, 15, 10]), Developer(dev_id=11, dev_name=Andy, ability_scores=[20, 20, 15, 15, 10]), Developer(dev_id=93, dev_name=Kinda, ability_scores=[20, 20, 15, 15, 20]), Developer(dev_id=90, dev_name=Yulia, ability_scores=[15, 10, 15, 15, 15]), Developer(dev_id=95, dev_name=Backboy, ability_scores=[15, 15, 15, 15, 15])]
1, [Developer(dev_id=22, dev_name=Minsu, ability_scores=[15, 20, 10, 15, 15]), Developer(dev_id=92, dev_name=Hong, ability_scores=[15, 20, 20, 15, 10]), Developer(dev_id=99, dev_name=Taejun, ability_scores=[10, 20, 20, 15, 20])]
3, [Developer(dev_id=26, dev_name=Chris, ability_scores=[10, 15, 10, 20, 20])]
2, [Developer(dev_id=32, dev_name=Kelly, ability_scores=[15, 10, 20, 15, 15]), Developer(dev_id=30, dev_name=Elsa, ability_scores=[10, 10, 15, 10, 15])]
4, [Developer(dev_id=31, dev_name=Anna, ability_scores=[10, 10, 10, 15, 20])]

3. [1425, 1225, 675, 475, 475]

1. group by ability, user id = 1403
1, [Developer(dev_id=13, dev_name=Baehyun, ability_scores=[10, 20, 20, 10, 10])]
3, [Developer(dev_id=14, dev_name=Jeong, ability_scores=[15, 10, 15, 20, 15]), Developer(dev_id=112, dev_name=Hillton, ability_scores=[10, 10, 15, 20, 10])]
0, [Developer(dev_id=72, dev_name=Hyein, ability_scores=[20, 20, 10, 20, 15]), Developer(dev_id=82, dev_name=Elia, ability_scores=[15, 10, 15, 15, 15]), Developer(dev_id=89, dev_name=Gazua, ability_scores=[20, 10, 10, 15, 15]), Developer(dev_id=100, dev_name=Sparta, ability_scores=[15, 15, 15, 10, 10]), Developer(dev_id=113, dev_name=King, ability_scores=[20, 20, 20, 15, 20]), Developer(dev_id=120, dev_name=Jack, ability_scores=[20, 20, 20, 20, 15])]
2, [Developer(dev_id=73, dev_name=Chanho, ability_scores=[15, 15, 20, 20, 15])]
4, [Developer(dev_id=83, dev_name=Frank, ability_scores=[10, 15, 15, 15, 20]), Developer(dev_id=124, dev_name=Miranda, ability_scores=[15, 15, 15, 10, 20])]

2. group by ability, user id = 1403
1, [Developer(dev_id=13, dev_name=Baehyun, ability_scores=[10, 20, 20, 10, 10])]
3, [Developer(dev_id=14, dev_name=Jeong, ability_scores=[15, 10, 15, 20, 15]), Developer(dev_id=112, dev_name=Hillton, ability_scores=[10, 10, 15, 20, 10])]
0, [Developer(dev_id=72, dev_name=Hyein, ability_scores=[20, 20, 10, 20, 15]), Developer(dev_id=89, dev_name=Gazua, ability_scores=[20, 10, 10, 15, 15]), Developer(dev_id=113, dev_name=King, ability_scores=[20, 20, 20, 15, 20]), Developer(dev_id=120, dev_name=Jack, ability_scores=[20, 20, 20, 20, 15]), Developer(dev_id=82, dev_name=Elia, ability_scores=[15, 10, 15, 15, 15]), Developer(dev_id=100, dev_name=Sparta, ability_scores=[15, 15, 15, 10, 10])]
2, [Developer(dev_id=73, dev_name=Chanho, ability_scores=[15, 15, 20, 20, 15])]
4, [Developer(dev_id=83, dev_name=Frank, ability_scores=[10, 15, 15, 15, 20]), Developer(dev_id=124, dev_name=Miranda, ability_scores=[15, 15, 15, 10, 20])]

3. [1600, 475, 475, 850, 850]

4. [1425, 1225, 675, 475, 475]
5. [1600, 475, 475, 850, 850]
6. [2030625, 1500625, 455625, 225625, 225625]
7. [2560000, 225625, 225625, 722500, 722500]
8. [0.4423417290673928, 0.8692976104272266, 0.6688073394495413, 0.23796967699406724, 0.23796967699406724]
9. [0.5576582709326072, 0.13070238957277336, 0.3311926605504587, 0.7620303230059328, 0.7620303230059328]
10. 1.5119069324077126
11. 1.599134866543122

final win probability : 0.4859809125411259
```
