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
