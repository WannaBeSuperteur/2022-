# lateinit과 lazy 키워드
## 설명
### lateinit 키워드: var의 늦은 초기화
 * ```lateinit``` 키워드는 ```var```로 선언한 변수를 그 이후에 초기화할 때 사용함.
 * 기본 사용법:
   * ```lateinit var variableName : dataType```
     * 여기서 ```dataType```이 ```Int```, ```Long```, ```Double```, ```Float```인 경우는 오류 발생
   * ```lateinit```으로 선언한 변수는 초기화 전에는 사용 불가능.
   * 초기화할 때 ```null```로 초기화할 수 없음.

### lazy 키워드: val의 늦은 초기화
 * ```lazy``` 키워드는 ```val```로 선언한 변수를 그 이후에 초기화할 때 사용함.
 * 다음 구문은 ```lazy``` 키워드를 이용하여 ```valName```을 ```value```로 초기화하는 코드
   * 여기서 ```valName```을 처음 참조하면 ```초기화 구문```이 실행되고, 그 다음부터는 그 값인 ```value```의 반환만 수행
   ```kotlin
   val valName : dataType by lazy {
       // 초기화 구문
       value
   }
   ```
    
## 예제 코드
```kotlin
fun lateinitTest() {
    println("\n==== lateinit test ====")
    
    // lateinit var myInteger : Int
    // lateinit var myLong : Long
    // lateinit var myDouble : Double
    // lateinit var myFloat : Float
    // (Error: 'lateinit' modifier is not allowed on
    //         local variables of primitive types)
    
    lateinit var myStr : String
    // println(myStr)
    // (Error: kotlin.UninitializedPropertyAccessException:
    //         lateinit property myStr has not been initialized)
    
    // myStr = null
    // (Error: Null can not be a value of a non-null type String)
    
    myStr = "my string test"
    println(myStr)
}

fun lazyTest() {
    println("\n==== lazy test ====")
    
    val myLazyStr : String by lazy {
        println("initializing my lazy string ...")
        "my lazy string test"
    }
    
    println(1)
    println(myLazyStr)
    println(2)
    println(myLazyStr)
}

fun main() {
    lateinitTest()
    lazyTest()
}
```

## 실행 결과
```
==== lateinit test ====
my string test

==== lazy test ====
1
initializing my lazy string ...
my lazy string test
2
my lazy string test
```
