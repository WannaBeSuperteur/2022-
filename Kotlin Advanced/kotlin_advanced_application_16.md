## 요구사항 분석
워너비슈퍼추어소프트에서는 이번에 페이스북, 인스타그램과 같은 형태의 SNS 서비스인 ```워너비스타```를 개발하려고 한다. ```워너비스타```의 시스템은 다음과 같다.
* 일반적인 SNS처럼, 각 사용자는 다른 사용자들을 팔로우할 수 있다.
* 각 사용자의 ```좋아요 점수```는 다음과 같이 계산된다.
  * 자신이 가장 최근에 작성한 ```3```개의 게시물을 각각 ```P1```, ```P2```, ```P3```이라고 한다.
  * 상술한 최대 ```3```개의 게시물의 좋아요 수의 평균을 ```A```, 최댓값을 ```M```이라고 한다.
    * 자신이 누적 작성한 게시물이 ```3```개 미만인 ```N```개인 경우, 3개를 채우기 위한 나머지 ```(3-N)```개의 (가상의) 게시물의 좋아요 수가 모두 0개인 것처럼 계산한다.
      * 예를 들어, 게시물이 총 2개이고 이들의 좋아요 수가 각각 20, 15일 때, ```A = (20 + 15 + 0) / 3```, ```M = max(20, 15, 0)```으로 계산한다.
  * ```좋아요 점수```는 최종적으로 ```100 * log2(A + M + 1) / 12```를 가장 가까운 정수로 내림한 값으로 계산한다. (최대 100점)
* 각 사용자의 ```인싸력```은 다음과 같이 계산된다.
  * 해당 사용자의 좋아요 점수를 ```L```이라고 한다.
  * 해당 사용자의 팔로워 중 좋아요 점수가 가장 큰 ```3```명의 좋아요 점수를 각각 ```L1```, ```L2```, ```L3```이라고 한다.
  * ```인싸력```은 최종적으로 ```(L + L1 + L2 + L3 + 3 * min(L1, L2, L3)) / 7```를 가장 가까운 정수로 내림한 값으로 계산한다.
    * 자신의 팔로워가 ```3```명 미만인 ```F```명인 경우, 3명을 채우기 위한 나머지 ```(3-F)```명의 (가상의) 팔로워들의 좋아요 점수가 모두 0점인 것처럼 계산한다.
      * 예를 들어, 팔로워가 총 2명이고 이들의 좋아요 점수가 각각 80, 70일 때, ```L1 = 80, L2 = 70, L3 = 0```으로 계산할 수 있다.

다음 데이터 클래스와 같은 사용자 정보가 주어질 때, 이를 기반으로 각 사용자의 ```좋아요 점수``` 및 ```인싸력```을 계산하시오.

### 데이터 클래스
```kotlin
// 사용자 정보
data class User (
    val user_id: Int, // 사용자 ID
    val user_name: String, // 사용자 이름
    val recent_posts: List<Post>, // 최근 작성 게시물 (최대 10개)
    val followings: List<Int> // 팔로워하는 사용자 ID 목록
)

// 게시물 정보
data class Post (
    val post_id: Int, // 게시물 ID
    val post_title: String, // 게시물 제목
    val likes: Int // 좋아요 수
)
```

### 코드 및 결과
## 코드
```kotlin
import kotlin.math.*

// 사용자 정보
data class User (
    val user_id: Int, // 사용자 ID
    val user_name: String, // 사용자 이름
    val recent_posts: List<Post>, // 최근 작성 게시물 (최대 10개)
    val followings: List<Int> // 팔로워하는 사용자 ID 목록
)

// 게시물 정보
// post_id의 값이 클수록 나중에 작성된 게시물임
data class Post (
    val post_id: Int, // 게시물 ID
    val post_title: String, // 게시물 제목
    val likes: Int // 좋아요 수
)

// 각 유저의 좋아요 점수 계산
fun computeLikesScore(users: List<User>): Map<Int, Int> {
    val result = mutableMapOf<Int, Int>()
    
    for (user in users) {
        val recentPosts = user.recent_posts.sortedByDescending { it.post_id }.take(3)
        
        // (0..2).flatMap { ... } 일 경우 ... 부분을 listOf(...)로 바꿔야 함
        // (0..2).map { ... } 일 경우 ... 부분을 그대로 유지
        val likes = (0..2).map {
            recentPosts.getOrElse(it) { Post(-1, "", 0) }.likes
        }
        println("2. user_id=${user.user_id}, likes=${likes}")
        
        val A = likes.sum() / 3.0
        val M = likes.max().toDouble()
        val likesScore = 100.0 * log(A + M + 1.0, 2.0) / 12.0
        val likesScoreInt = min(likesScore.toInt(), 100)
        println("3. A=${A}, M=${M}, likesScore=${likesScore}, likesScoreInt=${likesScoreInt}\n")
        
        result.put(user.user_id, likesScoreInt)
    }
    
    println("4. 좋아요 점수: ${result}\n")
    return result
}

// 각 유저의 팔로워 목록 추출
fun getFollowersInfo(users: List<User>): Map<Int, List<Int>> {
    val result = mutableMapOf<Int, List<Int>>()
    
    // 각 유저의 ID를 먼저 추가
    users.forEach {
        result.put(it.user_id, mutableListOf<Int>())
    }
    
    for (user in users) {
        user.followings.forEach {
            (result.get(it)!! as MutableList).add(user.user_id)
        }
    }
    
    return result
}

// 좋아요 점수와 팔로워 데이터에 기반, 각 유저의 인싸력 계산
fun computeInsiderPower(
    likesScore: Map<Int, Int>,
    followers: Map<Int, List<Int>>
): Map<Int, Int> {
    val result = mutableMapOf<Int, Int>()
    val userIds = likesScore.keys
    
    for (userId in userIds) {
        val L = likesScore.get(userId)!!
        val fols = followers.get(userId)!!
        
        // (0 until fols.size).flatMap { ... } 일 경우 ... 부분을 listOf(...)로 바꿔야 함
        // (0 until fols.size).map { ... } 일 경우 ... 부분을 그대로 유지
        val Ln = (0 until fols.size).map {
            likesScore.get(fols[it])!!
        }.sortedDescending()
        println("5. userId=${userId}, Ln=${Ln}")
        
        val L123 = (0..2).map {
            Ln.getOrElse(it) { 0 }
        }
        println("6. L123=${L123}")
        
        val insiderPower = (L + L123.sum() + 3 * L123.min()) / 7.0
        val insiderPowerInt = insiderPower.toInt()
        println("7. L=${L}, L123=${L123}, sum=${L123.sum()}, insiderPower=${insiderPower}, insiderPowerInt=${insiderPowerInt}\n")
    
        result.put(userId, insiderPowerInt)
    }
    
    return result
}

fun main() {
    
    // 사용자 목록
    val users = listOf<User>(
        User(
            200, "hong", listOf<Post>(
                Post(13700, "오랜만에 부산 여행!", 30),
                Post(13704, "혁수야 사랑해!♥", 26),
                Post(13702, "코로나 종식을 위하여~~", 14),
                Post(13701, "굳이 제목이 필요할까?", 129)
            ), listOf(201, 203, 204)
        ),
        User(
            201, "hyuk", listOf<Post>(
                Post(13918, "홍민이와의 데이트😍", 40),
                Post(13912, "생일🎁🎂 축하합니다~", 57)
            ), listOf(200, 207)
        ),
        User(
            202, "min", listOf<Post>(
                Post(11447, "테스트", 36),
                Post(11452, "워너비스타 오픈!🎉", 64)
            ), listOf(203, 206)
        ),
        User(
            203, "jinny", listOf<Post>(
                Post(928, "워너비스타 공식 도움말이에요~", 196),
                Post(927, "ㅇㅇ", 16),
                Post(921, "테스트", 3),
                Post(919, "아싸의 삶", 29),
                Post(926, "?", 7)
            ), listOf(200, 206, 207)
        ),
        User(
            204, "O.A.S.I.S", listOf<Post>(
                Post(10200, "오아시스💧 워너비스타입니다!", 1360),
                Post(10202, "오아시스 서비스 안내", 979),
                Post(10204, "오아시스 앱 다운로드 방법", 864)
            ), listOf(205, 206)
        ),
        User(
            205, "newbie", listOf<Post>(), listOf(206, 207)
        ),
        User(
            206, "wow_lab", listOf<Post>(
                Post(7235, "와우랩 오픈 안내", 2495),
                Post(7242, "와우랩 서비스 이용 방법", 1237),
                Post(7240, "와우랩 오픈 기념 이벤트🎈🎇🧨✨🎊", 2001),
                Post(7259, "와우랩 이벤트 당첨자 발표🎁", 1962),
                Post(7260, "와우랩 2차 이벤트 공지", 1280)
            ), listOf(202, 207)
        ),
        User(
            207, "trend_master", listOf<Post>(
                Post(1391, "요즘 트렌드는? ✨워너비스타✨", 2102),
                Post(1395, "2022 트렌드 테스트📝 풀이", 2980),
                Post(1398, "트렌드마스터 소개!!", 1601)
            ), listOf(200, 206)
        ),
    )
    
    // ID -> 이름 매칭
    val userIdToName = mutableMapOf<Int, String>()
    for (user in users) {
        userIdToName.put(user.user_id, user.user_name)
    }
    println("1. ID to name: ${userIdToName}\n")
    
    // 각 사용자의 좋아요 점수 계산
    val likesScore = computeLikesScore(users)
    likesScore.forEach {
        println("user id ${it.key} name ${userIdToName.get(it.key)!!} -> 좋아요 점수: ${it.value}")
    }
    print("\n")
    
    // 각 유저의 팔로워 목록 추출
    val followers = getFollowersInfo(users)
    followers.forEach {
        println("user id ${it.key} name ${userIdToName.get(it.key)!!} -> 팔로워 ID: ${it.value}")
    }
    print("\n")
    
    // 각 사용자의 인싸력 계산
    val insiderPower = computeInsiderPower(likesScore, followers)
    insiderPower.forEach {
        println("user id ${it.key} name ${userIdToName.get(it.key)!!} -> 인싸력: ${it.value}")
    }
}
```

## 결과
```kotlin
1. ID to name: {200=hong, 201=hyuk, 202=min, 203=jinny, 204=O.A.S.I.S, 205=newbie, 206=wow_lab, 207=trend_master}

2. user_id=200, likes=[26, 14, 129]
3. A=56.333333333333336, M=129.0, likesScore=62.84784976768361, likesScoreInt=62

2. user_id=201, likes=[40, 57, 0]
3. A=32.333333333333336, M=57.0, likesScore=54.143221171939295, likesScoreInt=54

2. user_id=202, likes=[64, 36, 0]
3. A=33.333333333333336, M=64.0, likesScore=55.163405362733734, likesScoreInt=55

2. user_id=203, likes=[196, 16, 7]
3. A=73.0, M=196.0, likesScore=67.30679664209026, likesScoreInt=67

2. user_id=204, likes=[864, 979, 1360]
3. A=1067.6666666666667, M=1360.0, likesScore=93.71623982518021, likesScoreInt=93

2. user_id=205, likes=[0, 0, 0]
3. A=0.0, M=0.0, likesScore=0.0, likesScoreInt=0

2. user_id=206, likes=[1280, 1962, 1237]
3. A=1493.0, M=1962.0, likesScore=97.95739585136225, likesScoreInt=97

2. user_id=207, likes=[1601, 2980, 2102]
3. A=2227.6666666666665, M=2980.0, likesScore=102.88915331775935, likesScoreInt=100

4. 좋아요 점수: {200=62, 201=54, 202=55, 203=67, 204=93, 205=0, 206=97, 207=100}

user id 200 name hong -> 좋아요 점수: 62
user id 201 name hyuk -> 좋아요 점수: 54
user id 202 name min -> 좋아요 점수: 55
user id 203 name jinny -> 좋아요 점수: 67
user id 204 name O.A.S.I.S -> 좋아요 점수: 93
user id 205 name newbie -> 좋아요 점수: 0
user id 206 name wow_lab -> 좋아요 점수: 97
user id 207 name trend_master -> 좋아요 점수: 100

user id 200 name hong -> 팔로워 ID: [201, 203, 207]
user id 201 name hyuk -> 팔로워 ID: [200]
user id 202 name min -> 팔로워 ID: [206]
user id 203 name jinny -> 팔로워 ID: [200, 202]
user id 204 name O.A.S.I.S -> 팔로워 ID: [200]
user id 205 name newbie -> 팔로워 ID: [204]
user id 206 name wow_lab -> 팔로워 ID: [202, 203, 204, 205, 207]
user id 207 name trend_master -> 팔로워 ID: [201, 203, 205, 206]

5. userId=200, Ln=[100, 67, 54]
6. L123=[100, 67, 54]
7. L=62, L123=[100, 67, 54], sum=221, insiderPower=63.57142857142857, insiderPowerInt=63

5. userId=201, Ln=[62]
6. L123=[62, 0, 0]
7. L=54, L123=[62, 0, 0], sum=62, insiderPower=16.571428571428573, insiderPowerInt=16

5. userId=202, Ln=[97]
6. L123=[97, 0, 0]
7. L=55, L123=[97, 0, 0], sum=97, insiderPower=21.714285714285715, insiderPowerInt=21

5. userId=203, Ln=[62, 55]
6. L123=[62, 55, 0]
7. L=67, L123=[62, 55, 0], sum=117, insiderPower=26.285714285714285, insiderPowerInt=26

5. userId=204, Ln=[62]
6. L123=[62, 0, 0]
7. L=93, L123=[62, 0, 0], sum=62, insiderPower=22.142857142857142, insiderPowerInt=22

5. userId=205, Ln=[93]
6. L123=[93, 0, 0]
7. L=0, L123=[93, 0, 0], sum=93, insiderPower=13.285714285714286, insiderPowerInt=13

5. userId=206, Ln=[100, 93, 67, 55, 0]
6. L123=[100, 93, 67]
7. L=97, L123=[100, 93, 67], sum=260, insiderPower=79.71428571428571, insiderPowerInt=79

5. userId=207, Ln=[97, 67, 54, 0]
6. L123=[97, 67, 54]
7. L=100, L123=[97, 67, 54], sum=218, insiderPower=68.57142857142857, insiderPowerInt=68

user id 200 name hong -> 인싸력: 63
user id 201 name hyuk -> 인싸력: 16
user id 202 name min -> 인싸력: 21
user id 203 name jinny -> 인싸력: 26
user id 204 name O.A.S.I.S -> 인싸력: 22
user id 205 name newbie -> 인싸력: 13
user id 206 name wow_lab -> 인싸력: 79
user id 207 name trend_master -> 인싸력: 68
```
