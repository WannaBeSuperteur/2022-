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
