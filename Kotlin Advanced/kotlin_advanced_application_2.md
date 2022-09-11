## 요구 사항 분석
워너비슈퍼추어소프트에서 개발한 쇼핑 앱 "워너비쇼핑"에서는 2022년 추석을 맞이하여 고객들에게 등급에 따라 ```x```만원 상당의 사은품을 증정하려고 한다. 이때, 다음과 같은 제약 조건을 적용한다.
* 고객의 등급은 ```S```, ```A```, ```B```, ```C```로 나뉜다.
* 고객의 지난달 쇼핑 금액은 ```2022년 8월 1일```부터 ```2022년 8월 31일```까지의 총 쇼핑 금액의 합이다.
* 각 등급별로 ```x```의 값은 다음과 같이 계산하며, ```x```의 값은 소수점 아래 둘째 자리까지 반올림한다.
  * ```평균 대비 충성도 (L)``` : 해당 고객의 지난달 쇼핑 금액을 이 고객이 속한 등급 전체의 지난달 쇼핑 금액의 평균으로 나눈 값
  * ```S```등급 : ```20 + (지난달 쇼핑 금액) * ( 0.06 + (L이 1.5 이상이면 0.05 * L, L이 1 이상 1.5 미만이면 0.03 * L, L이 1 미만이면 0.03))```
  * ```A```등급 : ```10 + (지난달 쇼핑 금액) * ( 0.05 + (L이 1.25 이상이면 0.01 * L, L이 0.5 이상 1.25 미만이면 0.0075 * L, L이 0.5 미만이면 0.003))```
  * ```B```등급 : ```5 + (지난달 쇼핑 금액) * ( 0.04 + (L이 1.5 이상이면 0.005 * L, L이 1.5 미만이면 0.002))```
  * ```C```등급 : ```1 + (지난달 쇼핑 금액) * 0.03```
* 다음 조건은 개별 적용된다. 즉 다음 조건 둘 다 해당되는 경우, ```x```에 ```1.2```를 곱한 다음 ```0.7```을 곱해서 최종적으로 ```x```에 ```0.84```을 곱한다.
  * 상품 후기 게시판에 ```2022년 8월 31일```부터 동년 ```9월 8일```까지 글을 ```5```개 이상 남긴 사용자의 경우 ```x```의 값에 ```1.2```를 곱한다.
  * ```2022년 5월 31일```부터 동년 ```9월 8일```까지 비매너 경고 횟수가 ```3```회 이상인 사용자의 경우 ```x```의 값에 ```0.7```을 곱한다.
* 위 모든 조건에도 불구하고, 고객이 사은품 수령을 원하지 않는다고 명시했거나, ```2022년 5월 31일```부터 동년 ```9월 8일```까지 비매너 경고 횟수가 ```6```회 이상인 고객에게는 사은품을 증정하지 않는다.

고객 ID별로 얼마 상당의 사은품을 증정할지를 다음과 같은 형식으로 원 단위로 표시하시오.
```kotlin
ID      won
123     192800
147     200500
165     38000
181     45600
```
예를 들어 위와 같은 경우, 먼저 나열된 2명의 고객에 대해서는, 고객 ID가 ```123```인 고객에게는 ```192,800```원 상당의 사은품을 증정하고, ```147```번 고객에게는 ```200,500```원 상당의 사은품을 증정한다는 것을 의미한다.

### 고객 데이터 클래스
고객을 나타내는 데이터 클래스는 다음과 같다. 비매너 경고는 ```거래 비매너 경고``` 및 ```게시판 비매너 경고```로 분류되며, 메시지를 통해 확인할 수 있다.

```kotlin
// 고객 (사용자)
data class Customer (
    val ID: Int, // 고객 ID
    val name: String, // 고객 이름
    val level: String, // 고객 등급 (S, A, B, C)
    val shopping_list: List<ShoppingList>, // 쇼핑 리스트
    val messages: List<Message>, // 고객이 받은 메시지
    val posts: List<Posting>, // 고객이 남긴 글
    val dont_want: Boolean // 고객이 사은품 수령을 원하지 않는다고 명시하면 true, 그렇지 않으면 false
)

// 고객의 쇼핑 목록
data class ShoppingList (
    val product_ID: Int, // 제품 ID
    val price: Int, // 제품 가격 (원)
    val time: String // 제품 구매 일시 (예를 들어 2022년 8월 31일 오후 4시 56분 7초이면 "20220831165607")
)

// 고객이 받은 메시지
data class Message (
    val msg_ID: Int, // 메시지 ID
    val msg_type: Int, // 메시지 종류 (0: 일반 안내, 1: 이벤트 당첨, 2: 게시물이 추천을 일정 횟수 받음, 3: 거래 비매너 경고, 4: 게시판 비매너 경고)
    val title: String, // 메시지 제목
    val content: String, // 메시지 내용
    val time: String // 메시지 수신 일시 (예를 들어 2022년 8월 31일 오후 4시 56분 7초이면 "20220831165607")
)

// 고객이 게시판에 등록한 게시물
data class Posting (
    val post_ID: Int, // 게시물 ID
    val time: String // 게시물 등록 일시 (예를 들어 2022년 8월 31일 오후 4시 56분 7초이면 "20220831165607")
)
```