## 요구사항 분석
워너비슈퍼추어소프트에서는 멜X, 벅X와 같은 음악 플랫폼 어플리케이션을 새로 개발하여 2023년에 출시하려고 한다. 이 앱의 데이터 구조는 다음과 같이 데이터베이스(DB)와 유사한 형태의 구조로 되어 있다.

여기서 key는 해당 property를 통해 아이템을 고유하게 식별할 수 있다는 것을 의미한다.

* 유저(사용자) 아이템
  * **고유 ID (key)**
  * 이름
  * 성별
  * 생년월일
* 음악
  * **음악 ID (key)**
  * 노래 제목
  * 가수의 고유 ID
  * 발매일
  * 재생 시간
* 유저-음악
  * **유저 ID (key)**
  * **음악 ID (key)**
  * 재생 횟수
  * 평점
  * (유저 ID, 고유 ID)를 묶어서 key로 사용한다. 
* 가수
  * **가수 ID (key)**
  * 가수 이름
  * 가수 데뷔일
  * 가수 분류 (솔로, 보이그룹, 걸그룹)
  * 멤버 목록

다음을 계산하는 코드를 작성하시오.
* 1. 유저 ID가 ```2```인 유저가 들은 모든 음악에 대해, 해당 유저가 그 음악들에 매긴 평점의 평균
* 2. 유저 ID가 ```3```인 유저가 들은 음악 중 발매일이 가장 이른 음악과 가장 늦은 음악의 제목
* 3. 가수 이름이 ```WEVE```인 가수는 오직 하나뿐이다. 해당 가수가 발매한 모든 음악의 제목을 출력하시오.
* 4. 가수 이름이 ```WEVE```인 가수에 소속된 멤버들이 소속된 모든 '가수'의 이름을 출력하시오.
* 5. 어떤 음악에 대해, 그 음악을 들은 모든 유저가 매긴 평점 평균을 해당 음악의 ```총 평점```으로 한다. 가수 ID가 ```0```인 가수가 발매한 모든 음악에 대해, 해당 음악의 ```총 평점```의 평균을 출력하시오.

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
// 유저 (사용자)
data class User (
    val id: Int, // 고유 ID (key)
    val name: String, // 이름
    val gender: Int, // 남자:0, 여자:1
    val birth_date: String // "yyyy-mm-dd"
)

// 음악
data class Music (
    val id: Int, // 고유 ID (key)
    val title: String, // 노래 제목
    val singer_id: Int, // 가수의 고유 ID
    val release_date: String, // "yyyy-mm-dd"
    val time: Int // 재생 시간 (초)
)

// 유저-음악
data class UserMusic (
    val user_id: Int, // 유저 ID (key)
    val music_id: Int, // 음악 ID (key)
    val play_times: Int, // 재생 횟수
    val rating: Int, // 평점
)

// 가수
data class Singer (
    val id: Int, // 가수 ID    
    val name: String, // 가수 이름
    val debut_date: String, // 데뷔일
    val type: Int, // 가수 분류 (솔로:0, 보이그룹:1, 걸그룹:2)
    val members: List<Member>
)

// 가수 멤버
data class Member (
    val id: Int, // 앱 전체를 기준으로 한 멤버 ID
    val name: String, // 멤버 이름
    val birth_date: String, // 멤버 생일, "yyyy-mm-dd"
)
```

## 예제 코드
```kotlin
// 유저 (사용자)
data class User (
    val id: Int, // 고유 ID (key)
    val name: String, // 이름
    val gender: Int, // 남자:0, 여자:1
    val birth_date: String // "yyyy-mm-dd"
)

// 음악
data class Music (
    val id: Int, // 고유 ID (key)
    val title: String, // 노래 제목
    val singer_id: Int, // 가수의 고유 ID
    val release_date: String, // "yyyy-mm-dd"
    val time: Int // 재생 시간 (초)
)

// 유저-음악
data class UserMusic (
    val user_id: Int, // 유저 ID (key)
    val music_id: Int, // 음악 ID (key)
    val play_times: Int, // 재생 횟수
    val rating: Int, // 평점
)

// 가수
data class Singer (
    val id: Int, // 가수 ID
    val name: String, // 가수 이름
    val debut_date: String, // 데뷔일
    val type: Int, // 가수 분류 (솔로:0, 보이그룹:1, 걸그룹:2)
    val members: List<Member>
)

// 가수 멤버
data class Member (
    val id: Int, // 앱 전체를 기준으로 한 멤버 ID
    val name: String, // 멤버 이름
    val birth_date: String, // 멤버 생일, "yyyy-mm-dd"
)

// 1. 유저 ID가 2인 유저가 들은 모든 음악에 대해, 해당 유저가 그 음악들에 매긴 평점의 평균
fun printRatingsOfMusicListenedByUser(userId: Int, userMusics: List<UserMusic>) {
    val filteredUserMusics = userMusics.filter {
        it.user_id == userId
    }
    println("1. ${filteredUserMusics}")
    val avgRatings = filteredUserMusics.sumOf { it.rating } / filteredUserMusics.size.toDouble()
    
    println("1 답: ${avgRatings}")
}

// 2. 유저 ID가 3인 유저가 들은 음악 중 발매일이 가장 이른 음악과 가장 늦은 음악의 제목
fun printTitlesOfEarliestAndLatestMusicListenedByUser (
    userId: Int,
    userMusics: List<UserMusic>,
	musics: List<Music>
) {
    // 음악의 발매일
    val musicReleaseDates = mutableMapOf<Int, String>()
    musics.forEach {
        musicReleaseDates.put(it.id, it.release_date)
    }
    println("\n2. ${musicReleaseDates}")
    
    // 발매일이 가장 이른 음악과 가장 늦은 음악 찾기
    val filteredUserMusics = userMusics.filter {
        it.user_id == userId
    }
    println("3. ${filteredUserMusics}")
    val filteredMusicIds = filteredUserMusics.map {
        it.music_id
    }
    println("4. ${filteredMusicIds}")
    
    var maxReleaseDate = musicReleaseDates[filteredUserMusics[0].music_id]
    var minReleaseDate = musicReleaseDates[filteredUserMusics[0].music_id]
    
    filteredUserMusics.forEach {
        if (musicReleaseDates[it.music_id]!!.compareTo(minReleaseDate!!) < 0) {
            minReleaseDate = musicReleaseDates[it.music_id]
        }
        if (musicReleaseDates[it.music_id]!!.compareTo(maxReleaseDate!!) > 0) {
            maxReleaseDate = musicReleaseDates[it.music_id]
        }
    }
    println("2 답: ${minReleaseDate}, ${maxReleaseDate}")
}

// 3. 가수 이름이 WEVE인 가수는 오직 하나뿐이다. 해당 가수가 발매한 모든 음악의 제목을 출력하시오.
fun printAllMusicsOfSinger(singerName: String, musics: List<Music>, singers: List<Singer>) {
    val singerId = singers.find {
        it.name == singerName
    }!!.id
    println("\n6. ${singerId}")
    
    val musicTitles = musics.filter {
        it.singer_id == singerId
    }.map {
        it.title
    }
    println("3 답: ${musicTitles}")
}

// 4. 가수 이름이 WEVE인 가수에 소속된 멤버들이 소속된 모든 '가수'의 이름을 출력하시오.
fun printAllSingersOfMembers(singerName: String, singers: List<Singer>) {
    val singerMemberIDs = singers.find {
        it.name == singerName
    }!!.members.map {
        it.id
    }
    println("\n7. ${singerMemberIDs}")
    
    val setOfSingerMemberIDs = singerMemberIDs.toSet()
    println("8. set: ${setOfSingerMemberIDs}")
    
    val allNames = singers.foldIndexed (mutableListOf<String>()) { idx, acc, e ->
        val memberIDs = singers[idx].members.map { it.id }
        val setOfMemberIDs = memberIDs.toSet()
        println("9. (${singers[idx].name}) set: ${setOfMemberIDs.intersect(singerMemberIDs)}")
        
        // 멤버 ID 간 교집합을 검사
        if (setOfMemberIDs.intersect(singerMemberIDs).size > 0) {
            acc.add(e.name)
        }
        acc
    }
    
    println("4 답: ${allNames}")
}

// 5. 어떤 음악에 대해, 그 음악을 들은 모든 유저가 매긴 평점 평균을 해당 음악의 총 평점으로 한다. 가수 ID가 0인 가수가 발매한 모든 음악에 대해, 해당 음악의 총 평점의 평균을 출력하시오.
fun printAvgScoreOfSinger(singerId: Int, musics: List<Music>, userMusics: List<UserMusic>) {
    val listOfSinger = musics.filter {
        it.singer_id == singerId
    }
    println("\n10. ${listOfSinger}")
    
    val ratings = listOfSinger.map {
        val musicId = it.id
        val musicTitle = it.title
        
        val userMusicInfo = userMusics.filter { it.music_id == musicId }
        val musicScore = userMusicInfo.sumOf { it.rating } / userMusicInfo.size.toDouble()
        println("11. (${musicTitle}), ${musicScore}")
        
        musicScore // map에서 list에 대한 최종 계산 결과값을 이용 가능
    }
    println("12. ${ratings}")
    
    val avgRatings = ratings.sum() / ratings.size
    println("5 답: ${avgRatings}")
}

fun main() {
    
    // 임시 유저 목록
    val users = listOf<User> (
        User(0, "hong", 0, "1999-09-17"),
        User(1, "min", 1, "1999-06-03"),
        User(2, "jeong", 1, "2000-11-29"),
        User(3, "hyuk", 0, "1997-08-30")
    )
    
    // 임시 음악 목록
    val musics = listOf<Music> (
        Music(0, "After LOVE", 0, "2022-09-22", 180),
        Music(1, "Born Black", 1, "2022-08-16", 208),
        Music(2, "Your Feeling", 2, "2022-05-21", 197),
        Music(3, "Good Bad", 3, "2027-09-01", 206),
        Music(4, "Forever Trendy", 1, "2020-06-15", 204),
        Music(5, "FEEL DIVE", 0, "2022-05-05", 212),
        Music(6, "Happy Memory", 4, "2030-07-12", 236),
        Music(7, "Better Man", 5, "2022-01-03", 228),
        Music(8, "Queen of Coding", 3, "2027-12-24", 210),
        Music(9, "My Days", 4, "2030-01-07", 199),
        Music(10, "Q.E.D.", 3, "2022-10-05", 196),
        Music(11, "ELEVEN", 0, "2022-01-01", 186)
    )
    
    // 임시 유저-음악 목록
    val userMusics = listOf<UserMusic> (
        UserMusic(0, 0, 200, 5),
        UserMusic(0, 2, 3, 4),
        UserMusic(0, 5, 150, 5),
        UserMusic(0, 10, 4, 4),
        UserMusic(0, 11, 75, 5),
        UserMusic(1, 1, 19, 5),
        UserMusic(1, 5, 24, 5),
        UserMusic(2, 0, 7, 3),
        UserMusic(2, 3, 9, 4),
        UserMusic(2, 4, 12, 5),
        UserMusic(2, 9, 13, 3),
        UserMusic(3, 5, 96, 4),
        UserMusic(3, 6, 4, 4),
        UserMusic(3, 10, 12, 5),
        UserMusic(3, 11, 100, 5),
    )
    
    // 임시 가수 목록
    val singers = listOf<Singer> (
        Singer(0, "WEVE", "2022-01-01", 2, listOf<Member> (
            Member(0, "Yoo-j", "2002-09-01"),
            Member(1, "Autumn", "2002-10-24"),
            Member(2, "Laser", "2003-02-03"),
            Member(3, "Youngwon", "2003-08-31"),
            Member(4, "LZ", "2002-11-21"),
            Member(5, "Hyun", "2004-02-21")
        )),
        
        Singer(1, "BLACK MAGENTA", "2018-08-08", 2, listOf<Member> (
            Member(6, "Sooji", "1996-01-01"),
            Member(7, "Kennie", "1996-02-16"),
            Member(8, "Magenta Rose", "1997-01-11"),
            Member(9, "L", "1997-02-27")
        )),
        
        Singer(2, "ENFP Girls", "2014-01-08", 2, listOf<Member> (
            Member(10, "Rin", "1992-02-29"),
            Member(11, "MinJeong", "1993-01-01"),
            Member(12, "SunA", "1994-01-07"),
            Member(13, "May", "1995-05-01")
        )),
        
        Singer(3, "AUTUMN", "2027-09-01", 0, listOf<Member> (
            Member(1, "Autumn", "2002-10-24")
        )),
        
        Singer(4, "H-Girls", "2030-01-07", 2, listOf<Member> (
            Member(5, "Hyun", "2004-02-21"),
            Member(11, "MinJeong", "1993-01-01"),
            Member(13, "May", "1995-05-01")
        )),
        
        Singer(5, "M.A.N.K.I.N.D.", "2021-01-18", 1, listOf<Member> (
            Member(14, "MinWoo", "2000-01-23"),
            Member(15, "HyunJin", "2000-03-28"),
            Member(16, "Loo", "2001-05-07"),
            Member(17, "K.O.", "2002-06-13")
        ))
    )
    
    // 1. 유저 ID가 2인 유저가 들은 모든 음악에 대해, 해당 유저가 그 음악들에 매긴 평점의 평균
    printRatingsOfMusicListenedByUser(2, userMusics)
    
    // 2. 유저 ID가 3인 유저가 들은 음악 중 발매일이 가장 이른 음악과 가장 늦은 음악의 제목
    printTitlesOfEarliestAndLatestMusicListenedByUser(3, userMusics, musics)
    
    // 3. 가수 이름이 WEVE인 가수는 오직 하나뿐이다. 해당 가수가 발매한 모든 음악의 제목을 출력하시오.
    printAllMusicsOfSinger("WEVE", musics, singers)
    
    // 4. 가수 이름이 WEVE인 가수에 소속된 멤버들이 소속된 모든 '가수'의 이름을 출력하시오.
    printAllSingersOfMembers("WEVE", singers)
    
    // 5. 어떤 음악에 대해, 그 음악을 들은 모든 유저가 매긴 평점 평균을 해당 음악의 총 평점으로 한다. 가수 ID가 0인 가수가 발매한 모든 음악에 대해, 해당 음악의 총 평점의 평균을 출력하시오.
    printAvgScoreOfSinger(0, musics, userMusics)
}
```

## 예제 출력
```kotlin
1. [UserMusic(user_id=2, music_id=0, play_times=7, rating=3), UserMusic(user_id=2, music_id=3, play_times=9, rating=4), UserMusic(user_id=2, music_id=4, play_times=12, rating=5), UserMusic(user_id=2, music_id=9, play_times=13, rating=3)]
1 답: 3.75

2. {0=2022-09-22, 1=2022-08-16, 2=2022-05-21, 3=2027-09-01, 4=2020-06-15, 5=2022-05-05, 6=2030-07-12, 7=2022-01-03, 8=2027-12-24, 9=2030-01-07, 10=2022-10-05, 11=2022-01-01}
3. [UserMusic(user_id=3, music_id=5, play_times=96, rating=4), UserMusic(user_id=3, music_id=6, play_times=4, rating=4), UserMusic(user_id=3, music_id=10, play_times=12, rating=5), UserMusic(user_id=3, music_id=11, play_times=100, rating=5)]
4. [5, 6, 10, 11]
2 답: 2022-01-01, 2030-07-12

6. 0
3 답: [After LOVE, FEEL DIVE, ELEVEN]

7. [0, 1, 2, 3, 4, 5]
8. set: [0, 1, 2, 3, 4, 5]
9. (WEVE) set: [0, 1, 2, 3, 4, 5]
9. (BLACK MAGENTA) set: []
9. (ENFP Girls) set: []
9. (AUTUMN) set: [1]
9. (H-Girls) set: [5]
9. (M.A.N.K.I.N.D.) set: []
4 답: [WEVE, AUTUMN, H-Girls]

10. [Music(id=0, title=After LOVE, singer_id=0, release_date=2022-09-22, time=180), Music(id=5, title=FEEL DIVE, singer_id=0, release_date=2022-05-05, time=212), Music(id=11, title=ELEVEN, singer_id=0, release_date=2022-01-01, time=186)]
11. (After LOVE), 4.0
11. (FEEL DIVE), 4.666666666666667
11. (ELEVEN), 5.0
12. [4.0, 4.666666666666667, 5.0]
5 답: 4.555555555555556
```
