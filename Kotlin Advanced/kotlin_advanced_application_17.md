## 요구사항 분석
워너비슈퍼추어소프트에서는 고객 감사 이벤트를 진행하기로 하였다. 고객 감사 이벤트에는 수많은 고객들이 참여했는데, 다음과 같은 우선 순위 기준으로 이벤트 당첨자 ```N```명을 선발하려고 한다.
* 설치한 앱의 수가 가장 많은 사용자
* 설치한 앱의 수가 같은 사용자의 경우, 설치한 앱의 다운로드 수의 합이 가장 많은 사용자
* 설치한 앱과 그 다운로드 수의 합이 같은 사용자의 경우, 행운 점수(0점~100점)가 가장 높은 사용자
  * 행운 점수 계산 함수는 **행운 점수 계산 함수**의 내용과 같다.
* 행운 점수마저 같은 사용자의 경우, 모두 선발

다음 데이터 클래스 형식으로 정보가 제공될 때, 이벤트 당첨 고객을 선발하시오.

### 데이터 클래스
```kotlin
// 사용자 정보
data class User (
    val user_id: Int, // 사용자 ID
    val user_name: String, // 사용자 이름
    val installed_app_ids: List<Int> // 설치한 앱의 ID 목록
)

// 앱 정보
data class App (
    val app_id: Int, // 앱 ID
    val app_name: String, // 앱 이름
    val download_count: Int, // 다운로드 수
    val rating: Double // 스토어 평점
)
```

### 행운 점수 계산 함수
```kotlin
// 행운 점수 계산 함수
fun getLuckyScore(id: Int, name: String): Int {
    return name.fold(id * 1234 - 56789 / (id % 123 + 321)) { acc, e ->
        3 * (acc + 5 * e.code)
    } % 101
}
```

## 코드 및 결과
### 코드
```kotlin
// 사용자 정보
data class User (
    val user_id: Int, // 사용자 ID
    val user_name: String, // 사용자 이름
    val installed_app_ids: List<Int> // 설치한 앱의 ID 목록
)

// 앱 정보
data class App (
    val app_id: Int, // 앱 ID
    val app_name: String, // 앱 이름
    val download_count: Int, // 다운로드 수
    val rating: Double // 스토어 평점
)

// 행운 점수 계산 함수
fun getLuckyScore(id: Int, name: String): Int {
    return name.fold(id * 1234 - 56789 / (id % 123 + 321)) { acc, e ->
        3 * (acc + 5 * e.code)
    } % 101
}

// 당첨자 선발 및 표시 (n은 당첨자 인원수)
fun findLuckyGuys(users: List<User>, apps: List<App>, n: Int) {
    
    // 설치한 앱의 수
    val getInstalledAppCount: ((User) -> Int) = {
        it.installed_app_ids.size
    }
    
    // 설치한 앱의 다운로드 수의 합
    val getAppTotalDownloadCount: ((User) -> Int) = {
        it.installed_app_ids.sumOf { appId ->
            apps.find { app ->
                app.app_id == appId
            }!!.download_count
        }
    }
    
    // sortedWith를 이용하여 한번에 처리
    val sortedUsers = users.sortedWith(
        compareBy(
            { -getInstalledAppCount(it) },
            { -getAppTotalDownloadCount(it) },
            { -getLuckyScore(it.user_id, it.user_name) }
        )
    )

    if (n == 2) {
        sortedUsers.forEach {
            println("1. ${it}")
            println("2. 앱설치=${getInstalledAppCount(it)}, 다운로드합=${getAppTotalDownloadCount(it)}")
            println("3. 행운점수=${getLuckyScore(it.user_id, it.user_name)}\n")
        }
    }
    
    // 당첨 커트라인
    var installAppCountCutline = 0
    var totalDownloadCutline = 0
    var luckyScoreCutline = 0
    with (sortedUsers[n-1]) {
        installAppCountCutline = getInstalledAppCount(this)
        totalDownloadCutline = getAppTotalDownloadCount(this)
        luckyScoreCutline = getLuckyScore(this.user_id, this.user_name)
        println("4. event cutlines: ${installAppCountCutline}, ${totalDownloadCutline}, ${luckyScoreCutline}\n")
    }
    
    // 유저 선발
    println("당첨자 명단 :")
    sortedUsers.forEachIndexed { idx, user ->
        if (idx < n) { // 최초 N명
            println("당첨자 ID: ${user.user_id}, 이름: ${user.user_name}")
        } else if ( // 동점자
            getInstalledAppCount(user) == installAppCountCutline &&
            getAppTotalDownloadCount(user) == totalDownloadCutline &&
            getLuckyScore(user.user_id, user.user_name) == luckyScoreCutline
        ) {
            println("당첨자 ID: ${user.user_id}, 이름: ${user.user_name}")
        }
    }
}

fun main() {
    
    // 사용자 목록
    val users = listOf<User> (
        User(1000, "hong", listOf(1, 2, 3, 4)),
        User(1001, "min", listOf(0, 1, 3)),
        User(1002, "jee", listOf(0, 1, 2)),
        User(1003, "jinny", listOf(1, 2)),
        User(1004, "hyuk", listOf(1, 2, 3)),
        User(1005, "yoon", listOf(0, 1, 4)),
        User(1006, "hyun", listOf(1, 2, 4)),
        User(1007, "ho", listOf(0, 1, 2, 4)),
        User(1008, "suk", listOf(0, 1, 2, 3)),
        User(1009, "myeong", listOf(1, 3)),
        User(1010, "tae", listOf(0, 1, 4)),
        User(1011, "jong", listOf(0, 1, 2, 4)),
    )
    
    // 앱 목록
    val apps = listOf<App> (
        App(0, "WannaBeHabit", 510000, 4.75),
        App(1, "WannaBePuzzle", 720000, 4.5),
        App(2, "WannaBeMelody", 450000, 4.25),
        App(3, "ASAP King", 160000, 4.66),
        App(4, "Memo of WannaBe", 100000, 4.56)
    )
    
    // 당첨자 선발 및 표시
    (2..5).forEach {
        println("\n **** ${it}명 선발 ****")
        findLuckyGuys(users, apps, it)
    }
}
```

### 실행 결과
```kotlin

 **** 2명 선발 ****
1. User(user_id=1008, user_name=suk, installed_app_ids=[0, 1, 2, 3])
2. 앱설치=4, 다운로드합=1840000
3. 행운점수=13

1. User(user_id=1011, user_name=jong, installed_app_ids=[0, 1, 2, 4])
2. 앱설치=4, 다운로드합=1780000
3. 행운점수=65

1. User(user_id=1007, user_name=ho, installed_app_ids=[0, 1, 2, 4])
2. 앱설치=4, 다운로드합=1780000
3. 행운점수=24

1. User(user_id=1000, user_name=hong, installed_app_ids=[1, 2, 3, 4])
2. 앱설치=4, 다운로드합=1430000
3. 행운점수=54

1. User(user_id=1002, user_name=jee, installed_app_ids=[0, 1, 2])
2. 앱설치=3, 다운로드합=1680000
3. 행운점수=100

1. User(user_id=1001, user_name=min, installed_app_ids=[0, 1, 3])
2. 앱설치=3, 다운로드합=1390000
3. 행운점수=98

1. User(user_id=1004, user_name=hyuk, installed_app_ids=[1, 2, 3])
2. 앱설치=3, 다운로드합=1330000
3. 행운점수=80

1. User(user_id=1010, user_name=tae, installed_app_ids=[0, 1, 4])
2. 앱설치=3, 다운로드합=1330000
3. 행운점수=70

1. User(user_id=1005, user_name=yoon, installed_app_ids=[0, 1, 4])
2. 앱설치=3, 다운로드합=1330000
3. 행운점수=1

1. User(user_id=1006, user_name=hyun, installed_app_ids=[1, 2, 4])
2. 앱설치=3, 다운로드합=1270000
3. 행운점수=33

1. User(user_id=1003, user_name=jinny, installed_app_ids=[1, 2])
2. 앱설치=2, 다운로드합=1170000
3. 행운점수=90

1. User(user_id=1009, user_name=myeong, installed_app_ids=[1, 3])
2. 앱설치=2, 다운로드합=880000
3. 행운점수=47

4. event cutlines: 4, 1780000, 65

당첨자 명단 :
당첨자 ID: 1008, 이름: suk
당첨자 ID: 1011, 이름: jong

 **** 3명 선발 ****
4. event cutlines: 4, 1780000, 24

당첨자 명단 :
당첨자 ID: 1008, 이름: suk
당첨자 ID: 1011, 이름: jong
당첨자 ID: 1007, 이름: ho

 **** 4명 선발 ****
4. event cutlines: 4, 1430000, 54

당첨자 명단 :
당첨자 ID: 1008, 이름: suk
당첨자 ID: 1011, 이름: jong
당첨자 ID: 1007, 이름: ho
당첨자 ID: 1000, 이름: hong

 **** 5명 선발 ****
4. event cutlines: 3, 1680000, 100

당첨자 명단 :
당첨자 ID: 1008, 이름: suk
당첨자 ID: 1011, 이름: jong
당첨자 ID: 1007, 이름: ho
당첨자 ID: 1000, 이름: hong
당첨자 ID: 1002, 이름: jee
```
