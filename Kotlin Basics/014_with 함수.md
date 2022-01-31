# with 함수
## 설명
 * Kotlin에서 제공하는 ```with``` 함수는 다음과 같이 <b>null이 아닌 객체를 다룰</b> 때 사용됨.
 * ```with (instanceName) { ... }``` 구문을 통해 ```instanceName.``` 부분을 생략할 수 있음.
 * ```apply``` 함수와 달리 반환하는 값이 없으며, 인스턴스의 속성 대신 클래스에 정의된 함수 사용 가능.
 * 아래 2가지 코드는 서로 같은 의미임.
   ```kotlin
   val instanceName = className()
   instanceName.function0(arg0, arg1, ...)
   instanceName.function1(arg2, arg3, ...)
   ...
   ```
   ```kotlin
   val instanceName = className()
   with (instanceName) {
       function0(arg0, arg1, ...)
       function1(arg2, arg3, ...)
       ...
   }
   ```
 * 클래스에 정의된 함수뿐만 아니라 ```print```, ```println``` 등의 함수도 사용 가능
   * 그 함수의 인수가 해당 인스턴스와 관련있을 때 해당 인스턴스 이름(```instanceName```)을 생략 가능.
 * 아래 2가지 코드는 서로 같은 의미임.
   ```kotlin
   val instanceName = className()
   funcName0(instanceName.property0, instanceName.property1, ...)
   funcName1(instanceName.property2, instanceName.property3, ...) 
   ...
   ```
   ```kotlin
   val instanceName = className()
   with (instanceName) {
       funcName0(property0, property1, ...)
       funcName1(property2, property3, ...) 
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
    val gameInstance = Game().apply {
        name = "World Cup 2122"
        developer = "Wannabe Software"
        age = 8
        users = 30000
        rating = 4.2
    }
    
    // gameInstance 인스턴스의 정보 및 가치 출력
    gameInstance.printInfo()
    println(gameInstance.name) // 출력: World Cup 2122
    println(gameInstance.getValueOfGame()) // 출력: 48000
    
    print("\n\n\n")
    
    // with를 이용한 형태
    with (gameInstance) {
        printInfo()
        println(name) // 출력: World Cup 2122
        println(getValueOfGame()) // 출력: 48000
    }
}
```

## 실행 결과
```
< World Cup 2122 >
developer: Wannabe Software
age: 8+, number of daily users: 30000, rating: 4.2 / 5.0 stars

World Cup 2122
48000



< World Cup 2122 >
developer: Wannabe Software
age: 8+, number of daily users: 30000, rating: 4.2 / 5.0 stars

World Cup 2122
48000
```
