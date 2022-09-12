## 요구 사항 분석
워너비슈퍼추어소프트에서 개발한 쇼핑 앱 "워너비쇼핑"에서는 2022년 추석을 맞이하여 고객들에게 등급에 따라 ```x```만원 상당의 사은품을 증정하려고 한다. 이때, 다음과 같은 제약 조건을 적용한다.
* 고객의 등급은 ```S```, ```A```, ```B```, ```C```로 나뉜다.
* 고객의 지난달 쇼핑 금액은 ```2022년 8월 1일```부터 ```2022년 8월 31일```까지의 총 쇼핑 금액의 합으로, 단위는 만원이다.
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
    val shopping_list: List<ShoppingItem>, // 쇼핑 리스트
    val messages: List<Message>, // 고객이 받은 메시지
    val posts: List<Posting>, // 고객이 남긴 글
    val dont_want: Boolean // 고객이 사은품 수령을 원하지 않는다고 명시하면 true, 그렇지 않으면 false
)

// 고객의 쇼핑 목록의 각 항목
data class ShoppingItem (
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
    val board_name: String, // 게시판 이름 ("review": 상품 후기, "free": 자유게시판, "exchange": 거래게시판)
    val time: String // 게시물 등록 일시 (예를 들어 2022년 8월 31일 오후 4시 56분 7초이면 "20220831165607")
)
```

## 예제 코드
```kotlin
import kotlin.math.*

// 고객 (사용자)
data class Customer (
    val ID: Int, // 고객 ID
    val name: String, // 고객 이름
    val level: String, // 고객 등급 (S, A, B, C)
    val shopping_list: List<ShoppingItem>, // 쇼핑 리스트
    val messages: List<Message>, // 고객이 받은 메시지
    val posts: List<Posting>, // 고객이 남긴 글
    val dont_want: Boolean // 고객이 사은품 수령을 원하지 않는다고 명시하면 true, 그렇지 않으면 false
)

// 고객의 쇼핑 목록의 각 항목
data class ShoppingItem (
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
    val board_name: String, // 게시판 이름 ("review": 상품 후기, "free": 자유게시판, "exchange": 거래게시판)
    val time: String // 게시물 등록 일시 (예를 들어 2022년 8월 31일 오후 4시 56분 7초이면 "20220831165607")
)

// 보너스 (L의 값에 따라 처리할 부분) 계산
fun computeBonus(level: String, loyalty: Double) : Double {
    when (level) {
        "S" -> {
            when (loyalty) {
                in 1.5..999999.9 -> return 0.05 * loyalty
                in 1.0..1.5 -> return 0.03 * loyalty
                else -> return 0.03
            }
        }
        "A" -> {
            when (loyalty) {
                in 1.25..999999.9 -> return 0.01 * loyalty
                in 0.5..1.25 -> return 0.0075 * loyalty
                else -> return 0.003
            }
        }
        "B" -> {
            when (loyalty) {
                in 1.5..999999.9 -> return 0.005 * loyalty
                else -> return 0.002
            }
        }
    }
    return 0.0
}

// x의 값을 계산하는 함수
fun computeValueOfX(level: String, cost: Int, loyalty: Double): Double {
    val priceBase = mapOf("S" to 20.0, "A" to 10.0, "B" to 5.0, "C" to 1.0)
    val bonusBase = mapOf("S" to 0.06, "A" to 0.05, "B" to 0.04, "C" to 0.03)
    
    val x = (priceBase.get(level) as Double) + (cost / 10000.0).toDouble() * ((bonusBase.get(level) as Double) + computeBonus(level, loyalty))
    return x.toDouble()
}

// 사은품 가격 계산 함수
fun getPrice(customer: Customer, customerList: List<Customer>): Int {
    
    // 고객이 사은품 수령을 원하지 않는 경우
    if (customer.dont_want) {
        return 0
    }
    
    // shopping item이 지난달인지 검사
    val isLastMonth : (ShoppingItem) -> Boolean = { it ->
        it.time.compareTo("20220801000000") >= 0 && it.time.compareTo("20220831235959") <= 0
    }
    
    // shopping list에 대해 가격의 합을 계산하는 함수
    val sumPrice : (List<ShoppingItem>) -> Int = { it ->
        it.fold(0) { acc, e ->
            acc + e.price
        }
    }
    
    // 평균
    val avg : (List<Int>) -> Double = { it ->
        if (it.size > 0) {
            it.sum().toDouble() / it.size.toDouble()
        } else {
            0.0
        }
    }
    
    // 고객의 등급에 속한 고객 전체
    val customersOfSameLevel = customerList.filter { it ->
        it.level == customer.level
    }
    println("1. " + customersOfSameLevel.toString())
    
    // 고객의 등급에 속한 고객 전체의 지난달 쇼핑 목록
    val lastMonthCostOfSameLevel = customersOfSameLevel.map { it ->
        it.shopping_list.filter { item -> isLastMonth(item) }
    }
    println("2. " + lastMonthCostOfSameLevel.toString())
    
    // 고객의 등급에 속한 고객 전체의 지난달 쇼핑 금액
    // property를 참조하기 위하여 fold 함수 사용
    val lastMonthPriceOfSameLevel = lastMonthCostOfSameLevel.map { it -> sumPrice(it) }
    println("3. " + lastMonthPriceOfSameLevel.toString())
    
    // 해당 고객의 지난달 쇼핑 가격의 합 계산
    val lastMonthPriceOfCustomer = customer.shopping_list.filter {
        it -> isLastMonth(it)
    }
    println("4. " + lastMonthPriceOfCustomer.toString())
    
    val sumLastMonthPrice = sumPrice(lastMonthPriceOfCustomer)
    println("5. " + sumLastMonthPrice.toString())
    
    // 평균 대비 충성도 (L) 계산
    val loyalty = sumLastMonthPrice.toDouble() / avg(lastMonthPriceOfSameLevel)
    println("6. " + loyalty.toString())
    
    // x의 값을 계산
    var x = computeValueOfX(customer.level, sumLastMonthPrice, loyalty)
    println("7. " + x.toString())
    
    // 상품 후기 게시판에 2022.08.31 ~ 09.08에 글 5개 이상 작성 시 x1.2
    val reviewPosts = customer.posts.filter { it ->
        it.time.compareTo("20220831000000") >= 0 && it.time.compareTo("20220908235959") <= 0 && it.board_name == "review" 
    }
    println("8. " + reviewPosts.size)
    
    if (reviewPosts.size >= 5) {
        x = x * 1.2
    }
    println("9. " + x)
    
    // 2022.05.31 ~ 09.08에 비매너 경고 3회 이상 시 x0.7
    // 2022.05.31 ~ 09.08에 비매너 경고 6회 이상 시 0
    val warnings = customer.messages.filter { it ->
        it.time.compareTo("20220531000000") >= 0 && it.time.compareTo("20220908235959") <= 0 && (it.msg_type == 3 || it.msg_type == 4)
    }
    println("10. " + warnings)
    
    if (warnings.size >= 6) {
        x = 0.0
    } else if (warnings.size >= 3) {
        x = x * 0.7
    }
    println("11. " + x.toString() + "\n")
    
    // 반올림 처리
    x = (x * 100.0).roundToInt().toDouble() * 100.0
    return x.toInt()
}

fun main() {
    
    // 임시 쇼핑 리스트 데이터
    val exampleShoppingList1 = listOf<ShoppingItem>(
        ShoppingItem(product_ID = 19035, price = 200000, time = "20220719135000"),
        ShoppingItem(product_ID = 19036, price = 210000, time = "20220801100659"),
        ShoppingItem(product_ID = 18112, price = 150000, time = "20220806113752"),
        ShoppingItem(product_ID = 17024, price = 240000, time = "20220807121508"),
        ShoppingItem(product_ID = 19075, price = 80000, time = "20220908003720"),
        ShoppingItem(product_ID = 18129, price = 160000, time = "20220908182204")
    )
    
    val exampleShoppingList2 = listOf<ShoppingItem>(
        ShoppingItem(product_ID = 19035, price = 400000, time = "20220719135000"),
        ShoppingItem(product_ID = 19125, price = 200000, time = "20220907115000")
    )
    
    val exampleShoppingList3 = listOf<ShoppingItem>(
        ShoppingItem(product_ID = 19036, price = 300000, time = "20220720135000"),
        ShoppingItem(product_ID = 19126, price = 200000, time = "20220801001357")
    )
    
    val exampleShoppingList4 = listOf<ShoppingItem>(
        ShoppingItem(product_ID = 19037, price = 200000, time = "20220721135000"),
        ShoppingItem(product_ID = 19130, price = 100000, time = "20220803001357")
    )
    
    val exampleShoppingList5 = listOf<ShoppingItem>(
        ShoppingItem(product_ID = 19038, price = 400000, time = "20220819135000"),
        ShoppingItem(product_ID = 17502, price = 420000, time = "20220901144030"),
        ShoppingItem(product_ID = 17513, price = 310000, time = "20220902144032"),
        ShoppingItem(product_ID = 17514, price = 270000, time = "20220903144034")
    )
    
    // 임시 메시지 데이터
    val exampleMessages = listOf<Message>(
        Message(msg_ID = 1370017, msg_type = 0, title = "앱 사용법 안내", content = "앱 사용법 안내입니다.", time = "20220716093100"),
        Message(msg_ID = 1370025, msg_type = 1, title = "앱 가입 이벤트 당첨", content = "앱 가입 이벤트 당첨을 축하드립니다.", time = "20220720100004"),
        Message(msg_ID = 1370094, msg_type = 2, title = "추천 5회 달성", content = "작성하신 게시물 '여기 깨끗한 유저 천국이네요'가 추천 5회 받았습니다.", time = "20220803220721"),
        Message(msg_ID = 1370106, msg_type = 2, title = "추천 5회 달성", content = "작성하신 게시물 '저도 확진됐네요. 모두 조심하세요'가 추천 5회 받았습니다.", time = "20220805143010"),
        Message(msg_ID = 1370107, msg_type = 3, title = "거래 비매너 경고", content = "거래 비매너 경고 누적 1회입니다. (사유: 방문 거래 약속 미이행)", time = "20220808163000"),
        Message(msg_ID = 1370109, msg_type = 0, title = "게시물 삭제 안내", content = "작성하신 게시물 '아니 코로나 때문에 거래 약속 못 지킨 걸로 경고네요?'가 운영원칙 위반으로 삭제되었습니다.", time = "20220808171145"),
        Message(msg_ID = 1370110, msg_type = 4, title = "게시판 비매너 경고", content = "게시판 비매너 경고 누적 1회입니다. (사유: 유저 간 분쟁 유발)", time = "20220808171205"),
        Message(msg_ID = 1370114, msg_type = 3, title = "거래 비매너 경고", content = "거래 비매너 경고 누적 2회입니다. (사유: 거래 상대방과의 분쟁 유발)", time = "20220808180417"),
        Message(msg_ID = 1370115, msg_type = 0, title = "앱 사용 정지 안내", content = "단시간 내 다수 경고로 인해 고객님의 앱 사용이 2022-09-07 오후 6:04:27 까지 정지됩니다", time = "20220808180427"),
        Message(msg_ID = 1370185, msg_type = 3, title = "추석 이벤트 안내", content = "2022년 한가위 이벤트 안내입니다.", time = "20220908134700"),
    )
    
    // 임시 게시물 데이터
    val examplePosts = listOf<Posting>(
        Posting(post_ID = 703918, board_name = "exchange", time = "20220802114736"),
        Posting(post_ID = 704001, board_name = "review", time = "20220803160511"),
        Posting(post_ID = 704007, board_name = "free", time = "20220803173920"),
        Posting(post_ID = 704924, board_name = "free", time = "20220805102840"),
        Posting(post_ID = 705650, board_name = "free", time = "20220808082239"),
        Posting(post_ID = 715186, board_name = "free", time = "20220907181128"),
        Posting(post_ID = 715188, board_name = "review", time = "20220907221000"),
        Posting(post_ID = 715205, board_name = "review", time = "20220907230519"),
        Posting(post_ID = 715210, board_name = "review", time = "20220908000147"),
        Posting(post_ID = 715400, board_name = "free", time = "20220909102359")
    )
    
    // 임시 고객 데이터
    val exampleCustomer1 = Customer(100, "hong", "S", exampleShoppingList1, exampleMessages, examplePosts, false)
    val exampleCustomer2 = Customer(101, "yoon", "S", exampleShoppingList2, listOf<Message>(), listOf<Posting>(), false)
    val exampleCustomer3 = Customer(102, "muk", "A", exampleShoppingList3, listOf<Message>(), listOf<Posting>(), false)
    val exampleCustomer4 = Customer(103, "so", "A", exampleShoppingList4, listOf<Message>(), listOf<Posting>(), false)
    val exampleCustomer5 = Customer(104, "kwan", "S", exampleShoppingList5, listOf<Message>(), listOf<Posting>(), false)
	
    val customerList = listOf<Customer> (
    	exampleCustomer1, exampleCustomer2, exampleCustomer3, exampleCustomer4, exampleCustomer5
    )
    
    // 사은품 가격 계산
    val prices = customerList.map { it ->
        getPrice(it, customerList)
    }
    
    // 사은품 가격 출력
    println("\nID      won")
    for (i in 0 until customerList.size) {
        println(customerList[i].ID.toString() + "     " + prices[i].toString())
    }
}
```

## 예제 출력
```
1. [Customer(ID=100, name=hong, level=S, shopping_list=[ShoppingItem(product_ID=19035, price=200000, time=20220719135000), ShoppingItem(product_ID=19036, price=210000, time=20220801100659), ShoppingItem(product_ID=18112, price=150000, time=20220806113752), ShoppingItem(product_ID=17024, price=240000, time=20220807121508), ShoppingItem(product_ID=19075, price=80000, time=20220908003720), ShoppingItem(product_ID=18129, price=160000, time=20220908182204)], messages=[Message(msg_ID=1370017, msg_type=0, title=앱 사용법 안내, content=앱 사용법 안내입니다., time=20220716093100), Message(msg_ID=1370025, msg_type=1, title=앱 가입 이벤트 당첨, content=앱 가입 이벤트 당첨을 축하드립니다., time=20220720100004), Message(msg_ID=1370094, msg_type=2, title=추천 5회 달성, content=작성하신 게시물 '여기 깨끗한 유저 천국이네요'가 추천 5회 받았습니다., time=20220803220721), Message(msg_ID=1370106, msg_type=2, title=추천 5회 달성, content=작성하신 게시물 '저도 확진됐네요. 모두 조심하세요'가 추천 5회 받았습니다., time=20220805143010), Message(msg_ID=1370107, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 1회입니다. (사유: 방문 거래 약속 미이행), time=20220808163000), Message(msg_ID=1370109, msg_type=0, title=게시물 삭제 안내, content=작성하신 게시물 '아니 코로나 때문에 거래 약속 못 지킨 걸로 경고네요?'가 운영원칙 위반으로 삭제되었습니다., time=20220808171145), Message(msg_ID=1370110, msg_type=4, title=게시판 비매너 경고, content=게시판 비매너 경고 누적 1회입니다. (사유: 유저 간 분쟁 유발), time=20220808171205), Message(msg_ID=1370114, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 2회입니다. (사유: 거래 상대방과의 분쟁 유발), time=20220808180417), Message(msg_ID=1370115, msg_type=0, title=앱 사용 정지 안내, content=단시간 내 다수 경고로 인해 고객님의 앱 사용이 2022-09-07 오후 6:04:27 까지 정지됩니다, time=20220808180427), Message(msg_ID=1370185, msg_type=3, title=추석 이벤트 안내, content=2022년 한가위 이벤트 안내입니다., time=20220908134700)], posts=[Posting(post_ID=703918, board_name=exchange, time=20220802114736), Posting(post_ID=704001, board_name=review, time=20220803160511), Posting(post_ID=704007, board_name=free, time=20220803173920), Posting(post_ID=704924, board_name=free, time=20220805102840), Posting(post_ID=705650, board_name=free, time=20220808082239), Posting(post_ID=715186, board_name=free, time=20220907181128), Posting(post_ID=715188, board_name=review, time=20220907221000), Posting(post_ID=715205, board_name=review, time=20220907230519), Posting(post_ID=715210, board_name=review, time=20220908000147), Posting(post_ID=715400, board_name=free, time=20220909102359)], dont_want=false), Customer(ID=101, name=yoon, level=S, shopping_list=[ShoppingItem(product_ID=19035, price=400000, time=20220719135000), ShoppingItem(product_ID=19125, price=200000, time=20220907115000)], messages=[], posts=[], dont_want=false), Customer(ID=104, name=kwan, level=S, shopping_list=[ShoppingItem(product_ID=19038, price=400000, time=20220819135000), ShoppingItem(product_ID=17502, price=420000, time=20220901144030), ShoppingItem(product_ID=17513, price=310000, time=20220902144032), ShoppingItem(product_ID=17514, price=270000, time=20220903144034)], messages=[], posts=[], dont_want=false)]
2. [[ShoppingItem(product_ID=19036, price=210000, time=20220801100659), ShoppingItem(product_ID=18112, price=150000, time=20220806113752), ShoppingItem(product_ID=17024, price=240000, time=20220807121508)], [], [ShoppingItem(product_ID=19038, price=400000, time=20220819135000)]]
3. [600000, 0, 400000]
4. [ShoppingItem(product_ID=19036, price=210000, time=20220801100659), ShoppingItem(product_ID=18112, price=150000, time=20220806113752), ShoppingItem(product_ID=17024, price=240000, time=20220807121508)]
5. 600000
6. 1.8
7. 29.0
8. 3
9. 29.0
10. [Message(msg_ID=1370107, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 1회입니다. (사유: 방문 거래 약속 미이행), time=20220808163000), Message(msg_ID=1370110, msg_type=4, title=게시판 비매너 경고, content=게시판 비매너 경고 누적 1회입니다. (사유: 유저 간 분쟁 유발), time=20220808171205), Message(msg_ID=1370114, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 2회입니다. (사유: 거래 상대방과의 분쟁 유발), time=20220808180417), Message(msg_ID=1370185, msg_type=3, title=추석 이벤트 안내, content=2022년 한가위 이벤트 안내입니다., time=20220908134700)]
11. 20.299999999999997

1. [Customer(ID=100, name=hong, level=S, shopping_list=[ShoppingItem(product_ID=19035, price=200000, time=20220719135000), ShoppingItem(product_ID=19036, price=210000, time=20220801100659), ShoppingItem(product_ID=18112, price=150000, time=20220806113752), ShoppingItem(product_ID=17024, price=240000, time=20220807121508), ShoppingItem(product_ID=19075, price=80000, time=20220908003720), ShoppingItem(product_ID=18129, price=160000, time=20220908182204)], messages=[Message(msg_ID=1370017, msg_type=0, title=앱 사용법 안내, content=앱 사용법 안내입니다., time=20220716093100), Message(msg_ID=1370025, msg_type=1, title=앱 가입 이벤트 당첨, content=앱 가입 이벤트 당첨을 축하드립니다., time=20220720100004), Message(msg_ID=1370094, msg_type=2, title=추천 5회 달성, content=작성하신 게시물 '여기 깨끗한 유저 천국이네요'가 추천 5회 받았습니다., time=20220803220721), Message(msg_ID=1370106, msg_type=2, title=추천 5회 달성, content=작성하신 게시물 '저도 확진됐네요. 모두 조심하세요'가 추천 5회 받았습니다., time=20220805143010), Message(msg_ID=1370107, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 1회입니다. (사유: 방문 거래 약속 미이행), time=20220808163000), Message(msg_ID=1370109, msg_type=0, title=게시물 삭제 안내, content=작성하신 게시물 '아니 코로나 때문에 거래 약속 못 지킨 걸로 경고네요?'가 운영원칙 위반으로 삭제되었습니다., time=20220808171145), Message(msg_ID=1370110, msg_type=4, title=게시판 비매너 경고, content=게시판 비매너 경고 누적 1회입니다. (사유: 유저 간 분쟁 유발), time=20220808171205), Message(msg_ID=1370114, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 2회입니다. (사유: 거래 상대방과의 분쟁 유발), time=20220808180417), Message(msg_ID=1370115, msg_type=0, title=앱 사용 정지 안내, content=단시간 내 다수 경고로 인해 고객님의 앱 사용이 2022-09-07 오후 6:04:27 까지 정지됩니다, time=20220808180427), Message(msg_ID=1370185, msg_type=3, title=추석 이벤트 안내, content=2022년 한가위 이벤트 안내입니다., time=20220908134700)], posts=[Posting(post_ID=703918, board_name=exchange, time=20220802114736), Posting(post_ID=704001, board_name=review, time=20220803160511), Posting(post_ID=704007, board_name=free, time=20220803173920), Posting(post_ID=704924, board_name=free, time=20220805102840), Posting(post_ID=705650, board_name=free, time=20220808082239), Posting(post_ID=715186, board_name=free, time=20220907181128), Posting(post_ID=715188, board_name=review, time=20220907221000), Posting(post_ID=715205, board_name=review, time=20220907230519), Posting(post_ID=715210, board_name=review, time=20220908000147), Posting(post_ID=715400, board_name=free, time=20220909102359)], dont_want=false), Customer(ID=101, name=yoon, level=S, shopping_list=[ShoppingItem(product_ID=19035, price=400000, time=20220719135000), ShoppingItem(product_ID=19125, price=200000, time=20220907115000)], messages=[], posts=[], dont_want=false), Customer(ID=104, name=kwan, level=S, shopping_list=[ShoppingItem(product_ID=19038, price=400000, time=20220819135000), ShoppingItem(product_ID=17502, price=420000, time=20220901144030), ShoppingItem(product_ID=17513, price=310000, time=20220902144032), ShoppingItem(product_ID=17514, price=270000, time=20220903144034)], messages=[], posts=[], dont_want=false)]
2. [[ShoppingItem(product_ID=19036, price=210000, time=20220801100659), ShoppingItem(product_ID=18112, price=150000, time=20220806113752), ShoppingItem(product_ID=17024, price=240000, time=20220807121508)], [], [ShoppingItem(product_ID=19038, price=400000, time=20220819135000)]]
3. [600000, 0, 400000]
4. []
5. 0
6. 0.0
7. 20.0
8. 0
9. 20.0
10. []
11. 20.0

1. [Customer(ID=102, name=muk, level=A, shopping_list=[ShoppingItem(product_ID=19036, price=300000, time=20220720135000), ShoppingItem(product_ID=19126, price=200000, time=20220801001357)], messages=[], posts=[], dont_want=false), Customer(ID=103, name=so, level=A, shopping_list=[ShoppingItem(product_ID=19037, price=200000, time=20220721135000), ShoppingItem(product_ID=19130, price=100000, time=20220803001357)], messages=[], posts=[], dont_want=false)]
2. [[ShoppingItem(product_ID=19126, price=200000, time=20220801001357)], [ShoppingItem(product_ID=19130, price=100000, time=20220803001357)]]
3. [200000, 100000]
4. [ShoppingItem(product_ID=19126, price=200000, time=20220801001357)]
5. 200000
6. 1.3333333333333333
7. 11.266666666666667
8. 0
9. 11.266666666666667
10. []
11. 11.266666666666667

1. [Customer(ID=102, name=muk, level=A, shopping_list=[ShoppingItem(product_ID=19036, price=300000, time=20220720135000), ShoppingItem(product_ID=19126, price=200000, time=20220801001357)], messages=[], posts=[], dont_want=false), Customer(ID=103, name=so, level=A, shopping_list=[ShoppingItem(product_ID=19037, price=200000, time=20220721135000), ShoppingItem(product_ID=19130, price=100000, time=20220803001357)], messages=[], posts=[], dont_want=false)]
2. [[ShoppingItem(product_ID=19126, price=200000, time=20220801001357)], [ShoppingItem(product_ID=19130, price=100000, time=20220803001357)]]
3. [200000, 100000]
4. [ShoppingItem(product_ID=19130, price=100000, time=20220803001357)]
5. 100000
6. 0.6666666666666666
7. 10.55
8. 0
9. 10.55
10. []
11. 10.55

1. [Customer(ID=100, name=hong, level=S, shopping_list=[ShoppingItem(product_ID=19035, price=200000, time=20220719135000), ShoppingItem(product_ID=19036, price=210000, time=20220801100659), ShoppingItem(product_ID=18112, price=150000, time=20220806113752), ShoppingItem(product_ID=17024, price=240000, time=20220807121508), ShoppingItem(product_ID=19075, price=80000, time=20220908003720), ShoppingItem(product_ID=18129, price=160000, time=20220908182204)], messages=[Message(msg_ID=1370017, msg_type=0, title=앱 사용법 안내, content=앱 사용법 안내입니다., time=20220716093100), Message(msg_ID=1370025, msg_type=1, title=앱 가입 이벤트 당첨, content=앱 가입 이벤트 당첨을 축하드립니다., time=20220720100004), Message(msg_ID=1370094, msg_type=2, title=추천 5회 달성, content=작성하신 게시물 '여기 깨끗한 유저 천국이네요'가 추천 5회 받았습니다., time=20220803220721), Message(msg_ID=1370106, msg_type=2, title=추천 5회 달성, content=작성하신 게시물 '저도 확진됐네요. 모두 조심하세요'가 추천 5회 받았습니다., time=20220805143010), Message(msg_ID=1370107, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 1회입니다. (사유: 방문 거래 약속 미이행), time=20220808163000), Message(msg_ID=1370109, msg_type=0, title=게시물 삭제 안내, content=작성하신 게시물 '아니 코로나 때문에 거래 약속 못 지킨 걸로 경고네요?'가 운영원칙 위반으로 삭제되었습니다., time=20220808171145), Message(msg_ID=1370110, msg_type=4, title=게시판 비매너 경고, content=게시판 비매너 경고 누적 1회입니다. (사유: 유저 간 분쟁 유발), time=20220808171205), Message(msg_ID=1370114, msg_type=3, title=거래 비매너 경고, content=거래 비매너 경고 누적 2회입니다. (사유: 거래 상대방과의 분쟁 유발), time=20220808180417), Message(msg_ID=1370115, msg_type=0, title=앱 사용 정지 안내, content=단시간 내 다수 경고로 인해 고객님의 앱 사용이 2022-09-07 오후 6:04:27 까지 정지됩니다, time=20220808180427), Message(msg_ID=1370185, msg_type=3, title=추석 이벤트 안내, content=2022년 한가위 이벤트 안내입니다., time=20220908134700)], posts=[Posting(post_ID=703918, board_name=exchange, time=20220802114736), Posting(post_ID=704001, board_name=review, time=20220803160511), Posting(post_ID=704007, board_name=free, time=20220803173920), Posting(post_ID=704924, board_name=free, time=20220805102840), Posting(post_ID=705650, board_name=free, time=20220808082239), Posting(post_ID=715186, board_name=free, time=20220907181128), Posting(post_ID=715188, board_name=review, time=20220907221000), Posting(post_ID=715205, board_name=review, time=20220907230519), Posting(post_ID=715210, board_name=review, time=20220908000147), Posting(post_ID=715400, board_name=free, time=20220909102359)], dont_want=false), Customer(ID=101, name=yoon, level=S, shopping_list=[ShoppingItem(product_ID=19035, price=400000, time=20220719135000), ShoppingItem(product_ID=19125, price=200000, time=20220907115000)], messages=[], posts=[], dont_want=false), Customer(ID=104, name=kwan, level=S, shopping_list=[ShoppingItem(product_ID=19038, price=400000, time=20220819135000), ShoppingItem(product_ID=17502, price=420000, time=20220901144030), ShoppingItem(product_ID=17513, price=310000, time=20220902144032), ShoppingItem(product_ID=17514, price=270000, time=20220903144034)], messages=[], posts=[], dont_want=false)]
2. [[ShoppingItem(product_ID=19036, price=210000, time=20220801100659), ShoppingItem(product_ID=18112, price=150000, time=20220806113752), ShoppingItem(product_ID=17024, price=240000, time=20220807121508)], [], [ShoppingItem(product_ID=19038, price=400000, time=20220819135000)]]
3. [600000, 0, 400000]
4. [ShoppingItem(product_ID=19038, price=400000, time=20220819135000)]
5. 400000
6. 1.2000000000000002
7. 23.84
8. 0
9. 23.84
10. []
11. 23.84


ID      won
100     203000
101     200000
102     112700
103     105500
104     238400
```

## 참고 (sumOf 함수 이용)
```kotlin
// shopping list에 대해 가격의 합을 계산하는 함수
val sumPrice : (List<ShoppingItem>) -> Int = { it ->
    it.fold(0) { acc, e ->
        acc + e.price
    }
}
```
대신
```kotlin
// shopping list에 대해 가격의 합을 계산하는 함수
val sumPrice : (List<ShoppingItem>) -> Int = { it ->
    it.sumOf { it.price }
}
```
을 이용해도 동일한 결과를 얻을 수 있다.
