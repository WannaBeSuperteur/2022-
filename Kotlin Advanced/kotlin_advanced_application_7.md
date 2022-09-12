## 요구 사항 분석
워너비슈퍼추어소프트에서는 왓O, 넷O릭스와 같은 콘텐츠 플랫폼 서비스 앱인 "워너비콘"을 작년에 출시하여 현재 안드로이드 버전의 구글 플레이 누적 다운로드 수가 20만 건을 기록 중이다. 누적 다운로드 수의 증가 모멘텀을 더욱 키우고자, 워너비콘 기획팀은 인공지능을 이용하여 사용자에게 적절한 콘텐츠를 추천하는 기능을 기획하였다.

해당 기능을 제공하기 위해, 서버에서 각 콘텐츠를 ```6```차원의 벡터(즉, 숫자 값 ```6```개가 있는 배열, 예를 들어 ```[0.35, -0.17, 1.06, 0.42, 0.07, -0.24]```) 형태로 적절히 나타낸 것(이하 ```콘텐츠 벡터```)들을 클라이언트에 제공하면, 클라이언트에서는 다음과 같이 처리한다.
* 현재 사용자가 재미있게 보았다고 표시한 ```N```개의 콘텐츠의 콘텐츠 벡터들을 이들 각 콘텐츠의 가중치와 함께 서버로부터 받아온다.
* 이들 콘텐츠 벡터의 각 차원(```6```개의 각각의 숫자)의 값의 가중 평균을 이 가중치를 이용하여 구하여 콘텐츠 벡터와 동일한 형태로 나타낸다. 이것을 ```평균 콘텐츠 벡터```라고 한다.
* 평균 콘텐츠 벡터와 앱에서 서비스되는 모든 콘텐츠(상술한 ```N```개의 콘텐츠 제외)의 콘텐츠 벡터를 비교하여, 이들 모든 콘텐츠 중 평균 콘텐츠 벡터와의 거리 값이 가장 작은 ```10```개의 콘텐츠 정보를 거리 값이 가까운 순서대로 출력한다.
  * 이때 ```거리 값```은 두 콘텐츠 벡터의 대응되는 원소의 차이의 제곱의 합으로 정의한다.
  * 단, 사용자가 재미있다고 표시한 콘텐츠의 개수 ```N```의 값이 ```5``` 미만인 경우에는 거리 값이 가장 작은 ```5```개의 콘텐츠 정보를 거리 값이 가까운 순서대로 출력한다.

클라이언트에서 위와 같이 처리하도록 아래 데이터 클래스를 이용하여 프로그래밍하시오. (단, 앱에서 서비스되는 콘텐츠는 사용자가 재미있다고 표시한 ```N```개의 콘텐츠 외에 최소 10개가 있다.)

### 데이터 클래스
```kotlin
// 앱 사용자
data class User (
    val user_id: Long, // 사용자 ID
    val name: String, // 사용자 이름
    val fun_contents: List<FunContent> // 사용자가 재미있다고 표시한 콘텐츠(총 N개)
)

// 유저가 재미있다고 표시한 콘텐츠
data class FunContent (
    val weight: Double, // 가중치
    val content: Content // 해당 콘텐츠에 대한 정보
)

// 전체 콘텐츠 정보
data class AllContentsInfo (
    val vector_max: Double, // 벡터의 각 원소 값의 이론상 최댓값
    val vector_min: Double, // 벡터의 각 원소 값의 이론상 최댓값
    val all_contents: List<Content> // 전체 콘텐츠 정보
)

// 각 콘텐츠에 대한 정보
data class Content (
    val content_id: Int, // 콘텐츠 ID
    val name: String, // 콘텐츠 이름
    val content_vector: List<Double>, // 콘텐츠 벡터 (6차원 배열)
    val user_fun: Boolean // 유저가 재미있다고 표시한 콘텐츠(총 N개)이면 true, 아니면 false
)
```

## 예제 코드
```kotlin
import kotlin.math.*

// 앱 사용자
data class User (
    val user_id: Long, // 사용자 ID
    val name: String, // 사용자 이름
    val fun_contents: List<FunContent> // 사용자가 재미있다고 표시한 콘텐츠(총 N개)
)

// 유저가 재미있다고 표시한 콘텐츠
data class FunContent (
    val weight: Double, // 가중치
    val content: Content // 해당 콘텐츠에 대한 정보
)

// 전체 콘텐츠 정보
data class AllContentsInfo (
    val vector_max: Double, // 벡터의 각 원소 값의 이론상 최댓값
    val vector_min: Double, // 벡터의 각 원소 값의 이론상 최솟값
    val all_contents: List<Content> // 전체 콘텐츠 정보
)

// 각 콘텐츠에 대한 정보
data class Content (
    val content_id: Int, // 콘텐츠 ID
    val name: String, // 콘텐츠 이름
    val content_vector: List<Double>, // 콘텐츠 벡터 (6차원 배열)
    val user_fun: Boolean // 유저가 재미있다고 표시한 콘텐츠(총 N개)이면 true, 아니면 false
)

// 거리 값이 가장 작은 10개(5개)의 콘텐츠 출력
class ContentDist (
    val content_id: Int, // 콘텐츠 ID
    val name: String, // 콘텐츠 이름
    val content_vector: List<Double>, // 콘텐츠 벡터 (6차원 배열)
    val distance: Double // 거리 값
)

fun printMinDistContents(user: User, allContentsInfo: AllContentsInfo) {
    
    // 평균 콘텐츠 벡터 계산
    val sumWeight = user.fun_contents.sumOf { it.weight }
    println("1. ${sumWeight}")
    
    // 배열의 각 원소에 대한 계산 결과들을 합쳐서 하나의 배열로 만들어야 하므로 flatMap() 함수를 이용
    val avgContentVector = (0..5).flatMap { index ->
        listOf(user.fun_contents.sumOf { it ->
            it.content.content_vector[index] * it.weight
        } / sumWeight)
    }
    println("2. ${avgContentVector}")
    
    // 각 콘텐츠에 대해 거리 값 계산
    val contentDist = mutableListOf<ContentDist>()
    
    allContentsInfo.all_contents.forEach { item ->
        if (!item.user_fun) {
            val dist = (0..5).sumOf { it ->
                (item.content_vector[it] - avgContentVector[it]).pow(2)
            }
            contentDist.add(ContentDist(item.content_id, item.name, item.content_vector, dist))
        }
    }
    println("\n3. distance of each content :")
    contentDist.forEach {
        println("id=${it.content_id}, name=${it.name}, cv=${it.content_vector}, dist=${it.distance}")
    }
    
    // 거리 값 순서대로 정렬
    contentDist.sortBy { it.distance }
    println("\n4. distance of each content :")
    contentDist.forEach {
        println("id=${it.content_id}, name=${it.name}, cv=${it.content_vector}, dist=${it.distance}")
    }
    
    // 거리 값이 가장 가까운 5개 또는 10개의 콘텐츠 출력
    val nContentsToPrint = if (user.fun_contents.size >= 5) 10 else 5
    println("\n5. ${nContentsToPrint}\n")
    
    // 최종 출력
    contentDist.take(nContentsToPrint).forEachIndexed { index, item ->
        println("${index + 1}위, 이름: ${item.name}, 벡터: ${item.content_vector}, 거리: ${item.distance}")
    }
}

fun main() {
    
    // 가상의 유저 정보
    val testUser = User (
        123728, "hong",
        listOf<FunContent> (
            FunContent(3.25, Content(13, "코틀린 게임", listOf(1.43, 1.72, -0.8, -0.25, 0.07, 0.26), true)),
            FunContent(2.75, Content(17, "지금 우리 개발팀은", listOf(1.32, 0.79, 0.27, -1.25, -1.05, 0.35), true)),
            FunContent(2.12, Content(19, "버그지옥", listOf(0.19, 0.23, -0.17, -0.06, 0.1, 0.02), true)),
            FunContent(1.37, Content(20, "모범개발자", listOf(0.79, 0.22, -0.15, -0.39, -0.17, -0.22), true))
        )
    )
    
    // 가상의 전체 콘텐츠 정보
    val testAllContentsInfo = AllContentsInfo (
        2.04, -2.04,
        listOf<Content> (
            Content(13, "코틀린 게임", listOf(1.43, 1.72, -0.8, -0.25, 0.07, 0.26), true),
            Content(17, "지금 우리 개발팀은", listOf(1.32, 0.79, 0.27, -1.25, -1.05, 0.35), true),
            Content(19, "버그지옥", listOf(0.19, 0.23, -0.17, -0.06, 0.1, 0.02), true),
            Content(20, "모범개발자", listOf(0.79, 0.22, -0.15, -0.39, -0.17, -0.22), true),
            Content(25, "이상한 개발자 스미스", listOf(0.14, 0.1, 0.15, 0.3, 0.0, -0.72), false),
            Content(26, "협업의 집: 공동개발구역", listOf(1.01, 0.14, 0.79, -0.21, -0.45, -0.16), false),
            Content(30, "코틀린 게임 2", listOf(1.5, 1.33, -0.25, -0.17, -0.04, 0.36), false),
            Content(32, "버그지옥 2", listOf(0.37, 0.91, -1.3, 0.0, 0.08, 0.44), false),
            Content(34, "지금 우리 개발팀은 2", listOf(1.52, 0.53, 0.2, -1.4, -1.75, 1.69), false),
            Content(38, "이상한 기획자 기획기", listOf(1.23, 0.75, -0.11, -0.18, -1.2, -0.75), false),
            Content(40, "개발도시 2", listOf(-0.71, -0.22, 0.35, -0.45, 1.2, 1.02), false),
            Content(45, "핵", listOf(1.72, 1.11, -0.91, -0.71, 1.22, 0.3), false),
            Content(49, "개발자들", listOf(1.5, 1.2, -1.37, 0.85, 1.0, -0.45), false),
            Content(51, "자바 게임", listOf(-0.12, -0.21, 0.05, 0.01, -1.97, 1.88), false),
            Content(53, "닥터 버그픽스: 대혼란의 버그월드", listOf(-1.3, 0.7, 0.81, -0.64, 0.2, 0.25), false)
        )
    )
    
    // 거리 값이 가장 작은 10개(5개)의 콘텐츠 출력
    printMinDistContents(testUser, testAllContentsInfo)
}
```

## 예제 출력
```kotlin
1. 9.490000000000002
2. [1.0287249736564803, 0.9011064278187563, -0.25536354056901994, -0.5175447839831401, -0.2824973656480505, 0.16317175974710216]

3. distance of each content :
id=25, name=이상한 개발자 스미스, cv=[0.14, 0.1, 0.15, 0.3, 0.0, -0.72], dist=3.124099780146812
id=26, name=협업의 집: 공동개발구역, cv=[1.01, 0.14, 0.79, -0.21, -0.45, -0.16], dist=1.8994994640245786
id=30, name=코틀린 게임 2, cv=[1.5, 1.33, -0.25, -0.17, -0.04, 0.36], dist=0.6244123196620928
id=32, name=버그지옥 2, cv=[0.37, 0.91, -1.3, 0.0, 0.08, 0.44], dist=2.001153837048815
id=34, name=지금 우리 개발팀은 2, cv=[1.52, 0.53, 0.2, -1.4, -1.75, 1.69], dist=5.8499227516958126
id=38, name=이상한 기획자 기획기, cv=[1.23, 0.75, -0.11, -0.18, -1.2, -0.75], dist=1.8741055757211016
id=40, name=개발도시 2, cv=[-0.71, -0.22, 0.35, -0.45, 1.2, 1.02], dist=7.583024543055135
id=45, name=핵, cv=[1.72, 1.11, -0.91, -0.71, 1.22, 0.3], dist=3.2633058918433355
id=49, name=개발자들, cv=[1.5, 1.2, -1.37, 0.85, 1.0, -0.45], dist=5.4448097906842206
id=51, name=자바 게임, cv=[-0.12, -0.21, 0.05, 0.01, -1.97, 1.88], dist=8.720841297533536
id=53, name=닥터 버그픽스: 대혼란의 버그월드, cv=[-1.3, 0.7, 0.81, -0.64, 0.2, 0.25], dist=6.8537414029076125

4. distance of each content :
id=30, name=코틀린 게임 2, cv=[1.5, 1.33, -0.25, -0.17, -0.04, 0.36], dist=0.6244123196620928
id=38, name=이상한 기획자 기획기, cv=[1.23, 0.75, -0.11, -0.18, -1.2, -0.75], dist=1.8741055757211016
id=26, name=협업의 집: 공동개발구역, cv=[1.01, 0.14, 0.79, -0.21, -0.45, -0.16], dist=1.8994994640245786
id=32, name=버그지옥 2, cv=[0.37, 0.91, -1.3, 0.0, 0.08, 0.44], dist=2.001153837048815
id=25, name=이상한 개발자 스미스, cv=[0.14, 0.1, 0.15, 0.3, 0.0, -0.72], dist=3.124099780146812
id=45, name=핵, cv=[1.72, 1.11, -0.91, -0.71, 1.22, 0.3], dist=3.2633058918433355
id=49, name=개발자들, cv=[1.5, 1.2, -1.37, 0.85, 1.0, -0.45], dist=5.4448097906842206
id=34, name=지금 우리 개발팀은 2, cv=[1.52, 0.53, 0.2, -1.4, -1.75, 1.69], dist=5.8499227516958126
id=53, name=닥터 버그픽스: 대혼란의 버그월드, cv=[-1.3, 0.7, 0.81, -0.64, 0.2, 0.25], dist=6.8537414029076125
id=40, name=개발도시 2, cv=[-0.71, -0.22, 0.35, -0.45, 1.2, 1.02], dist=7.583024543055135
id=51, name=자바 게임, cv=[-0.12, -0.21, 0.05, 0.01, -1.97, 1.88], dist=8.720841297533536

5. 5

1위, 이름: 코틀린 게임 2, 벡터: [1.5, 1.33, -0.25, -0.17, -0.04, 0.36], 거리: 0.6244123196620928
2위, 이름: 이상한 기획자 기획기, 벡터: [1.23, 0.75, -0.11, -0.18, -1.2, -0.75], 거리: 1.8741055757211016
3위, 이름: 협업의 집: 공동개발구역, 벡터: [1.01, 0.14, 0.79, -0.21, -0.45, -0.16], 거리: 1.8994994640245786
4위, 이름: 버그지옥 2, 벡터: [0.37, 0.91, -1.3, 0.0, 0.08, 0.44], 거리: 2.001153837048815
5위, 이름: 이상한 개발자 스미스, 벡터: [0.14, 0.1, 0.15, 0.3, 0.0, -0.72], 거리: 3.124099780146812
```
