# apply 함수
## 설명
 * Kotlin에서 제공하는 ```apply``` 함수는 다음과 같이 <b>클래스의 인스턴스의 속성 값을 할당하여 인스턴스를 초기화</b>할 때 사용됨.
 * ```apply``` 함수는 ```instanceName```이 가리키는 인스턴스를 반환함.
 * 아래 2가지 코드는 서로 같은 의미임.
 ```kotlin
 val instanceName = className()
 instanceName.property0 = value0
 instanceName.property1 = value1
 ...
 ```
 ```kotlin
 val instanceName = className().apply {
     property0 = value0
     property1 = value1
     ...
 }
 ```

## 예제 코드
```kotlin
// 클래스 Game
class Game {
    var name:String? = null
    var developer:String? = null
    var age:Int? = null
    var users:Int? = null
    var rating:Double? = null
    
    // 정보 출력 함수
    fun printInfo() {
        println("< " + this.name + " >\ndeveloper: " + this.developer +
                "\nage: " + this.age.toString() +
                "+, number of daily users: " + this.users.toString() +
                ", rating: " + this.rating.toString() + " / 5.0 stars\n")
    }
    
    // 게임의 가치를 나타내는 값을 반환하는 함수
    // '!!' 표시는 해당 값이 null이 아님을 보장함을 나타냄
    fun getValueOfGame() : Int {
        when (this.rating!!) {
            in 4.5..5.0 -> return (2.50 * this.users!!).toInt()
            in 4.0..4.5 -> return (1.60 * this.users!!).toInt()
            in 3.5..4.0 -> return (1.00 * this.users!!).toInt()
            in 3.0..3.5 -> return (0.65 * this.users!!).toInt()
            in 2.5..3.0 -> return (0.40 * this.users!!).toInt()
            in 2.0..2.5 -> return (0.25 * this.users!!).toInt()
            else        -> return (0.15 * this.users!!).toInt()
        }
    }
}

fun main() {
    
    // 기본 형태
    val starOfLegends_0 = Game()
    starOfLegends_0.name = "Star of Legends"
    starOfLegends_0.developer = "Star of Legends Software"
    starOfLegends_0.age = 12
    starOfLegends_0.users = 37500
    starOfLegends_0.rating = 4.5
    starOfLegends_0.printInfo()
    
    // apply 함수를 사용하여 속성을 할당 (instance 초기화) 한 형태
    // apply 함수는 해당 instance를 반환
    val starOfLegends_1 = Game().apply {
    	name = "Star of Legends"
        developer = "Star of Legends Software."
    	age = 12
    	users = 37500
    	rating = 4.5
    }
    starOfLegends_1.printInfo()
}
```

## 실행 결과
```
< Star of Legends >
developer: Star of Legends Software
age: 12+, number of daily users: 37500, rating: 4.5 / 5.0 stars

< Star of Legends >
developer: Star of Legends Software.
age: 12+, number of daily users: 37500, rating: 4.5 / 5.0 stars
```
