## 요구사항 분석
워너비슈퍼추어소프트에서 개발한 게임인 <워너비몬스터>에서는 보스 몬스터를 잡을 때 다음과 같은 형식의 메시지를 출력한다. 단, 유저와 보스 몬스터가 번갈아 가면서 상대를 공격하며, 처음에는 유저가 보스 몬스터를 공격하는 것으로 시작한다. 유저 이름과 몬스터 이름은 모두 알파벳 소문자 또는 ```_```만으로 구성된다.
* 유저 및 몬스터 정보 출력
  * ```[사용자 ID]의 캐릭터 이름 [캐릭터명]의 HP는 [HP], 공격력은 [공격력], 방어력은 [방어력], 공격 면역은 [공격 면역 비율]입니다.```
  * ```[몬스터 이름]의 HP는 [몬스터HP], 공격력은 [몬스터 공격력], 방어력은 [몬스터 방어력], 공격 면역은 [몬스터 공격 면역 비율]입니다.```
* 유저의 몬스터 공격
  * 일반 공격 
    * ```[사용자 ID]가 몬스터에게 [행운 점수]의 행운 점수로 일반 공격을 가했습니다!```
  * 강력한 공격
    * ```[사용자 ID]가 몬스터에게 [행운 점수]의 행운 점수로 강력하게 공격했습니다!```
  * 면역 회피 공격
    * ```[사용자 ID]가 몬스터에게 면역 회피 공격! (행운 점수: [행운 점수])```
  * 마법 공격
    * ```[사용자 ID]가 몬스터에게 마법 공격! (행운 점수: [행운 점수])```
* 몬스터의 유저 공격
  * 일반 공격 
    * ```몬스터가 [사용자 ID]에게 [행운 점수]의 행운 점수로 일반 공격을 가했습니다!```
  * 강력한 공격
    * ```몬스터가 [사용자 ID]에게 [행운 점수]의 행운 점수로 강력하게 공격했습니다!```
  * 면역 회피 공격
    * ```몬스터가 [사용자 ID]에게 면역 회피 공격! (행운 점수: [행운 점수])```
  * 마법 공격
    * ```몬스터가 [사용자 ID]에게 마법 공격! (행운 점수: [행운 점수])```

각 공격의 유형별로 상대방이 입는 데미지는 다음과 같다. 데미지를 입는다는 것은 ```HP```가 데미지만큼 감소하는 것을 의미한다. 단, 행운 점수의 범위는 ```0``` 이상 ```15``` 이하이다.
* 일반 공격
  * (```자신의 공격력``` - ```상대방의 방어력```) * (1.0 - ```상대방의 공격 면역 비율```) * (0.7 + 0.04 * ```행운 점수```)
* 강력한 공격
  * (```자신의 공격력``` - ```상대방의 방어력```) * (1.0 - 0.75 * ```상대방의 공격 면역 비율```) * (0.55 + 0.15 * ```행운 점수```)
* 면역 회피 공격
  * (```자신의 공격력``` - ```상대방의 방어력```) * (0.7 + 0.04 * ```행운 점수```)
* 마법 공격
  * ```3 + (행운 점수) / 2```를 가장 가까운 자연수로 내림한 값에 해당하는 turn 수(=```전체 지속 turn 수```)만큼의 turn에 걸쳐서 반복적으로 데미지를 입는다. (다른 공격과 중복하여 데미지를 입음)
  * 데미지는 (```자신의 공격력``` - ```상대방의 방어력```) * (1.0 - ```상대방의 공격 면역 비율```) * (1.0 - (```현재 누적 turn 수``` - 1) / ```전체 지속 turn 수```) 
데미지는 소수점 아래를 버리고 가장 가까운 자연수로 내림하여 계산한다.

이때, 각 turn별로 공격자, 데미지, 사용자와 몬스터 각각의 남은 HP를 출력하시오.

### 데이터 클래스
데이터 클래스는 없이 그냥 string의 목록이다.

## 코드 및  결과
### 코드
```kotlin
// 로그 파싱 및 정보 출력
data class GameChar (
    var hp: Int,
    var att: Int,
    var def: Int,
    var attack_immune: Double
)

// 유저 또는 몬스터의 정보 설정
fun initCharacter(gameChar: GameChar, infoStr: String) {
    gameChar.hp = infoStr.substringAfter("HP는 ").substringBefore(", ").toInt()
    gameChar.att = infoStr.substringAfter("공격력은 ").substringBefore(", ").toInt()
    gameChar.def = infoStr.substringAfter("방어력은 ").substringBefore(", ").toInt()
    gameChar.attack_immune = infoStr.substringAfter("면역은 ").substringBefore("%").toDouble() / 100.0
}

// 마법 공격
data class MagicAttack (
    val turn: Int,
    val total_turns: Int
)

// 이전의 마법 공격 데미지 계산
fun computePreviousMagicDamage(
    attacker: GameChar,
    attacked: GameChar,
    magicAttackTurns: List<MagicAttack>,
    currentTurn: Int
): Int {
    val baseDamage = attacker.att - attacked.def
    
    return magicAttackTurns.filter {
        it.total_turns >= currentTurn - it.turn + 1
    }.sumOf {
        val turnWeight = (1.0 - (currentTurn - it.turn) / it.total_turns.toDouble())
        val damage = baseDamage * (1.0 - attacked.attack_immune) * turnWeight
        println("지속성 마법 공격 ${it} : turnWeight=${turnWeight}, damage=${damage}")
        damage
    }.toInt()
}

// 공격 데미지 판정
fun computeDamage(
    isUserAttack: Boolean,
    attacker: GameChar,
    attacked: GameChar,
    infoStr: String,
    magicAttackTurns: MutableList<MagicAttack>,
    currentTurn: Int
): Int {

    // 이전의 마법 공격 데미지 계산
    // infoStr이 비어 있으면 마법 공격 데미지만 계산하고 종료 (한쪽만 +이므로 덧셈으로 처리 가능)
    val magicDamage = computePreviousMagicDamage(attacker, attacked, magicAttackTurns, currentTurn)
    if (magicDamage > 0) {
        println("마법 데미지 (사용자 -> 몬스터 : ${isUserAttack}): ${magicDamage}")
    }
    
    if (infoStr.isEmpty()) {
        return magicDamage
    }
    
    // 공격 데미지 계산
    val baseDamage = attacker.att - attacked.def
    var intDamage = 0
    var luckScore = 0
    
    if (infoStr.contains("일반 공격")) { // 일반
        luckScore = infoStr.substringAfter("에게 ").substringBefore("의").toInt()
        val damage = magicDamage + baseDamage * (1.0 - attacked.attack_immune) * (0.7 + 0.04 * luckScore.toDouble())
        intDamage = damage.toInt()
    }
    else if (infoStr.contains("강력하게 공격")) { // 강력
        luckScore = infoStr.substringAfter("에게 ").substringBefore("의").toInt()
        val damage = magicDamage + baseDamage * (1.0 - 0.75 * attacked.attack_immune) * (0.55 + 0.15 * luckScore.toDouble())
        intDamage = damage.toInt()
    }
    else {
        luckScore = infoStr.substringAfterLast(": ").substringBefore(")").toInt()
            
        if (infoStr.contains("면역 회피")) { // 면역 회피
            val damage = magicDamage + baseDamage * (0.7 + 0.04 * luckScore.toDouble())
            intDamage = damage.toInt()
        }
        else { // 마법
            magicAttackTurns.add(MagicAttack(currentTurn, 3 + luckScore / 2))
 
            val damage = magicDamage + baseDamage * (1.0 - attacked.attack_immune)
            intDamage = damage.toInt()
        }
    }
    
    println("데미지 계산 (${intDamage}) : magic=${magicDamage}, base=${baseDamage}, immune=${attacked.attack_immune}, luck=${luckScore}")
    return intDamage
}

fun parseLog(log: List<String>) {
    val user = GameChar(0, 0, 0, 0.0)
    val monster = GameChar(0, 0, 0, 0.0)
    var userName = ""
    
    val userMagicAttackTurns = mutableListOf<MagicAttack>()
    val monsterMagicAttackTurns = mutableListOf<MagicAttack>()
    
    log.forEachIndexed { idx, s ->
        var attacker = ""
        var damage = 0
        
        // 유저 또는 몬스터 정보
        if (s.contains("HP는")) {
            if (s.contains("캐릭터 이름")) { // 유저 정보
                userName = s.substringBefore("의 ")
                initCharacter(user, s)
                println("1. 유저=${user}, userName=${userName}")
            } else { // 몬스터 정보
                initCharacter(monster, s)
                println("2. 몬스터=${monster}\n")
            }
        }
        
        // 유저의 공격
        else if (s.contains("${userName}가 몬스터에게")) {
            attacker = "user   "
            damage = computeDamage(true, user, monster, s, userMagicAttackTurns, idx)
            monster.hp -= damage
            
            // 지속성 마법 공격
            user.hp -= computeDamage(false, monster, user, "", monsterMagicAttackTurns, idx)
        }
        
        // 몬스터의 공격
        else {
            attacker = "monster"
            damage = computeDamage(false, monster, user, s, monsterMagicAttackTurns, idx)
            user.hp -= damage
            
            // 지속성 마법 공격
            monster.hp -= computeDamage(true, user, monster, "", userMagicAttackTurns, idx)
        }
        
        // 정보 출력
        if (attacker.isNotEmpty()) {
            println("공격자: ${attacker} , 데미지: ${damage}, 유저 HP: ${user.hp}, 몬스터 HP: ${monster.hp}\n")
        }
    }
}

fun main() {
    val log = listOf<String> (
        "hong의 캐릭터 이름 Kotlin의 HP는 30000, 공격력은 220, 방어력은 10, 공격 면역은 40%입니다.",
        "monster_reducefold의 HP는 15000, 공격력은 310, 방어력은 20, 공격 면역은 20%입니다.",
        "hong가 몬스터에게 13의 행운 점수로 일반 공격을 가했습니다!",
        "몬스터가 hong에게 6의 행운 점수로 강력하게 공격했습니다!",
        "hong가 몬스터에게 8의 행운 점수로 강력하게 공격했습니다!",
        "몬스터가 hong에게 마법 공격! (행운 점수: 2)",
        "hong가 몬스터에게 면역 회피 공격! (행운 점수: 12)",
        "몬스터가 hong에게 면역 회피 공격! (행운 점수: 15)",
        "hong가 몬스터에게 마법 공격! (행운 점수: 4)",
        "몬스터가 hong에게 4의 행운 점수로 일반 공격을 가했습니다!",
        "hong가 몬스터에게 12의 행운 점수로 일반 공격을 가했습니다!",
        "몬스터가 hong에게 면역 회피 공격! (행운 점수: 6)",
        "hong가 몬스터에게 10의 행운 점수로 일반 공격을 가했습니다!",
        "몬스터가 hong에게 7의 행운 점수로 일반 공격을 가했습니다!",
        "hong가 몬스터에게 마법 공격! (행운 점수: 7)",
        "몬스터가 hong에게 0의 행운 점수로 강력하게 공격했습니다!",
        "hong가 몬스터에게 12의 행운 점수로 일반 공격을 가했습니다!",
        "몬스터가 hong에게 마법 공격! (행운 점수: 15)",
        "hong가 몬스터에게 7의 행운 점수로 강력하게 공격했습니다!",
        "몬스터가 hong에게 10의 행운 점수로 강력하게 공격했습니다!",
        "hong가 몬스터에게 면역 회피 공격! (행운 점수: 1)",
        "몬스터가 hong에게 마법 공격! (행운 점수: 14)",
        "hong가 몬스터에게 14의 행운 점수로 강력하게 공격했습니다!",
        "몬스터가 hong에게 3의 행운 점수로 강력하게 공격했습니다!"
    )
    
    parseLog(log)
}
```

### 실행 결과
```kotlin
1. 유저=GameChar(hp=30000, att=220, def=10, attack_immune=0.4), userName=hong
2. 몬스터=GameChar(hp=15000, att=310, def=20, attack_immune=0.2)

데미지 계산 (195) : magic=0, base=200, immune=0.2, luck=13
공격자: user    , 데미지: 195, 유저 HP: 30000, 몬스터 HP: 14805

데미지 계산 (304) : magic=0, base=300, immune=0.4, luck=6
공격자: monster , 데미지: 304, 유저 HP: 29696, 몬스터 HP: 14805

데미지 계산 (297) : magic=0, base=200, immune=0.2, luck=8
공격자: user    , 데미지: 297, 유저 HP: 29696, 몬스터 HP: 14508

데미지 계산 (180) : magic=0, base=300, immune=0.4, luck=2
공격자: monster , 데미지: 180, 유저 HP: 29516, 몬스터 HP: 14508

데미지 계산 (236) : magic=0, base=200, immune=0.2, luck=12
지속성 마법 공격 MagicAttack(turn=5, total_turns=4) : turnWeight=0.75, damage=135.0
마법 데미지 (사용자 -> 몬스터 : false): 135
공격자: user    , 데미지: 236, 유저 HP: 29381, 몬스터 HP: 14272

지속성 마법 공격 MagicAttack(turn=5, total_turns=4) : turnWeight=0.5, damage=90.0
마법 데미지 (사용자 -> 몬스터 : false): 90
데미지 계산 (479) : magic=90, base=300, immune=0.4, luck=15
공격자: monster , 데미지: 479, 유저 HP: 28902, 몬스터 HP: 14272

데미지 계산 (160) : magic=0, base=200, immune=0.2, luck=4
지속성 마법 공격 MagicAttack(turn=5, total_turns=4) : turnWeight=0.25, damage=45.0
마법 데미지 (사용자 -> 몬스터 : false): 45
공격자: user    , 데미지: 160, 유저 HP: 28857, 몬스터 HP: 14112

데미지 계산 (154) : magic=0, base=300, immune=0.4, luck=4
지속성 마법 공격 MagicAttack(turn=8, total_turns=5) : turnWeight=0.8, damage=128.0
마법 데미지 (사용자 -> 몬스터 : true): 128
공격자: monster , 데미지: 154, 유저 HP: 28703, 몬스터 HP: 13984

지속성 마법 공격 MagicAttack(turn=8, total_turns=5) : turnWeight=0.6, damage=96.0
마법 데미지 (사용자 -> 몬스터 : true): 96
데미지 계산 (284) : magic=96, base=200, immune=0.2, luck=12
공격자: user    , 데미지: 284, 유저 HP: 28703, 몬스터 HP: 13700

데미지 계산 (282) : magic=0, base=300, immune=0.4, luck=6
지속성 마법 공격 MagicAttack(turn=8, total_turns=5) : turnWeight=0.4, damage=64.0
마법 데미지 (사용자 -> 몬스터 : true): 64
공격자: monster , 데미지: 282, 유저 HP: 28421, 몬스터 HP: 13636

지속성 마법 공격 MagicAttack(turn=8, total_turns=5) : turnWeight=0.19999999999999996, damage=31.999999999999993
마법 데미지 (사용자 -> 몬스터 : true): 31
데미지 계산 (207) : magic=31, base=200, immune=0.2, luck=10
공격자: user    , 데미지: 207, 유저 HP: 28421, 몬스터 HP: 13429

데미지 계산 (176) : magic=0, base=300, immune=0.4, luck=7
공격자: monster , 데미지: 176, 유저 HP: 28245, 몬스터 HP: 13429

데미지 계산 (160) : magic=0, base=200, immune=0.2, luck=7
공격자: user    , 데미지: 160, 유저 HP: 28245, 몬스터 HP: 13269

데미지 계산 (115) : magic=0, base=300, immune=0.4, luck=0
지속성 마법 공격 MagicAttack(turn=14, total_turns=6) : turnWeight=0.8333333333333334, damage=133.33333333333334
마법 데미지 (사용자 -> 몬스터 : true): 133
공격자: monster , 데미지: 115, 유저 HP: 28130, 몬스터 HP: 13136

지속성 마법 공격 MagicAttack(turn=14, total_turns=6) : turnWeight=0.6666666666666667, damage=106.66666666666669
마법 데미지 (사용자 -> 몬스터 : true): 106
데미지 계산 (294) : magic=106, base=200, immune=0.2, luck=12
공격자: user    , 데미지: 294, 유저 HP: 28130, 몬스터 HP: 12842

데미지 계산 (180) : magic=0, base=300, immune=0.4, luck=15
지속성 마법 공격 MagicAttack(turn=14, total_turns=6) : turnWeight=0.5, damage=80.0
마법 데미지 (사용자 -> 몬스터 : true): 80
공격자: monster , 데미지: 180, 유저 HP: 27950, 몬스터 HP: 12762

지속성 마법 공격 MagicAttack(turn=14, total_turns=6) : turnWeight=0.33333333333333337, damage=53.33333333333334
마법 데미지 (사용자 -> 몬스터 : true): 53
데미지 계산 (325) : magic=53, base=200, immune=0.2, luck=7
지속성 마법 공격 MagicAttack(turn=17, total_turns=10) : turnWeight=0.9, damage=162.0
마법 데미지 (사용자 -> 몬스터 : false): 162
공격자: user    , 데미지: 325, 유저 HP: 27788, 몬스터 HP: 12437

지속성 마법 공격 MagicAttack(turn=17, total_turns=10) : turnWeight=0.8, damage=144.0
마법 데미지 (사용자 -> 몬스터 : false): 144
데미지 계산 (574) : magic=144, base=300, immune=0.4, luck=10
지속성 마법 공격 MagicAttack(turn=14, total_turns=6) : turnWeight=0.16666666666666663, damage=26.66666666666666
마법 데미지 (사용자 -> 몬스터 : true): 26
공격자: monster , 데미지: 574, 유저 HP: 27214, 몬스터 HP: 12411

데미지 계산 (148) : magic=0, base=200, immune=0.2, luck=1
지속성 마법 공격 MagicAttack(turn=17, total_turns=10) : turnWeight=0.7, damage=125.99999999999999
마법 데미지 (사용자 -> 몬스터 : false): 125
공격자: user    , 데미지: 148, 유저 HP: 27089, 몬스터 HP: 12263

지속성 마법 공격 MagicAttack(turn=17, total_turns=10) : turnWeight=0.6, damage=108.0
마법 데미지 (사용자 -> 몬스터 : false): 108
데미지 계산 (288) : magic=108, base=300, immune=0.4, luck=14
공격자: monster , 데미지: 288, 유저 HP: 26801, 몬스터 HP: 12263

데미지 계산 (450) : magic=0, base=200, immune=0.2, luck=14
지속성 마법 공격 MagicAttack(turn=17, total_turns=10) : turnWeight=0.5, damage=90.0
지속성 마법 공격 MagicAttack(turn=21, total_turns=10) : turnWeight=0.9, damage=162.0
마법 데미지 (사용자 -> 몬스터 : false): 252
공격자: user    , 데미지: 450, 유저 HP: 26549, 몬스터 HP: 11813

지속성 마법 공격 MagicAttack(turn=17, total_turns=10) : turnWeight=0.4, damage=72.0
지속성 마법 공격 MagicAttack(turn=21, total_turns=10) : turnWeight=0.8, damage=144.0
마법 데미지 (사용자 -> 몬스터 : false): 216
데미지 계산 (426) : magic=216, base=300, immune=0.4, luck=3
공격자: monster , 데미지: 426, 유저 HP: 26123, 몬스터 HP: 11813
```
