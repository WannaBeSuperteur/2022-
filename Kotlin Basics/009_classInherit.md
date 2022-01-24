# Class의 상속
## 설명
### Class의 상속 (inheritance)
 * Class의 상속이란, 어떤 Class에 있는 함수 등을 다른 새로운 Class로 물려받아서, 그 새로운 Class에서 이들 함수를 사용할 수 있게 하는 것임.
 * Class의 상속을 위해서는, 상속 대상 (새로운 Class로 물려받을 함수가 있는 원래 Class)이 ```open class``` 등의 형태여야 한다.
 * ```open class CLASS_NAME(VALUE0:DATA_TYPE0, VALUE1:DATA_TYPE1, ...) { ... }``` 형태의 Class는 다음과 같이 상속한다.
   * ```class NEW_CLASS_NAME(VALUE0:DATA_TYPE0, VALUE1:DATA_TYPE1, ...) : CLASS_NAME(VALUE0, VALUE1) { ... }```

### 함수의 Overriding
 * 함수의 overriding이란, 어떤 Class를 상속하는 새로운 Class에 대해서, 원래 Class에 존재하는 함수를 <b>재정의</b>하여 사용하는 것이다.
 * 함수의 overriding을 위해서는, override하려는 함수가 ```open fun``` 등의 형태여야 한다.
 * ```open fun FUNCTION_NAME(ARG0:DATA_TYPE0, ARG1:DATA_TYPE1, ...): RETURN_TYPE { }``` 형태의 함수는 다음과 같이 override한다.
   * ```override fun FUNCTION_NAME(ARG0:DATA_TYPE0, ARG1:DATA_TYPE1, ...): RETURN_TYPE { }```
   * 이때, 이 함수를 원래 함수와 동일하게 정의하고 싶으면 다음과 같이 한다.
     * ```super.FUNCTION_NAME(ARG0, ARG1, ...)``` (return 값이 없을 때)
     * ```return super.FUNCTION_NAME(ARG0, ARG1, ...)``` (return 값이 있을 때)

## 예제 코드
```kotlin
// pow 함수를 사용하기 위해 import
import kotlin.math.*

// open class (using open annotation)
open class twoNumbers(a:Int, b:Int) {
	var a: Int = a
	var b: Int = b

    // open function만 override 가능
    // TIP: 함수 밖에서는 a, b 함수 안에서는 this.a, this.b 등을 사용
	open fun operation(opType: String): Int {
        when (opType) {
            "+" -> return this.a + this.b // opType가 + 이면 덧셈
            "-" -> return this.a - this.b // opType가 - 이면 뺄셈
            "*" -> return this.a * this.b // opType가 * 이면 곱셈
            "/" -> return this.a / this.b // opType가 ^ 이면 나눗셈
            else -> return -1
        }
    }
}

// open class인 twoNumbers를 상속한 class
class derivedClass(a:Int, b:Int) : twoNumbers(a, b) {
    
    // 상속 대상 class인 twoNumber에서 함수 operation을 가져와서 "재정의(re-define)"
    // 이것을 override라고 함
    override fun operation(opType: String): Int {
        return super.operation(opType) // 원래 함수와 동일하게 정의
    }
    
    // 새로 정의한 함수
    fun advancedOperation(opType: String, reverse: Boolean): Int {
        when (opType) {
            "^" -> // opType 가 ^ 이면 제곱 계산
                if (reverse) return (b.toDouble().pow(a)).toInt()
                else return (a.toDouble().pow(b)).toInt()
            "--" -> // opType 가 -- 이면 뺄셈 (반대로도 가능)
            	if (reverse) return b - a
            	else return a - b
            "//" -> // opType 가 // 이면 나눗셈 (반대로도 가능)
            	if (reverse) return b / a
            	else return a / b
            else -> return -1
        }
    }
}

fun main() {
    
    // twoNumbers 객체 (instance) 'nums0' 생성
    val nums0 = twoNumbers(20, 4)
    
    // 'nums0' 객체에 대해 정의된 함수 실행
    println(" << nums0 ( funcs in twoNumbers ) >>")
    println("addition    : " + nums0.operation("+"))
    println("subtraction : " + nums0.operation("-"))
    println("multiply    : " + nums0.operation("*"))
    println("divide      : " + nums0.operation("/"))
    
    // derivedClass (twoNumbers를 상속) 객체 'derived0' 생성
    val derived0 = derivedClass(8, 2)
    
    // 상속 대상인 twoNumbers 클래스에 정의된 함수 실행
    println("\n << derived0 ( funcs in twoNumbers ) >>")
    println("addition    : " + derived0.operation("+"))
    println("subtraction : " + derived0.operation("-"))
    println("multiply    : " + derived0.operation("*"))
    println("divide      : " + derived0.operation("/"))
    
    // derivedClass 클래스에 정의된 함수 실행
    println("\n << derived0 ( funcs in derivedClass ) >>")
    println("power              : " + derived0.advancedOperation("^", false))
    println("power    (reverse) : " + derived0.advancedOperation("^", true))
    println("subtract           : " + derived0.advancedOperation("--", false))
    println("subtract (reverse) : " + derived0.advancedOperation("--", true))
    println("divide             : " + derived0.advancedOperation("//", false))
    println("divide   (reverse) : " + derived0.advancedOperation("//", true))
}
```

## 실행 결과
```
 << nums0 ( funcs in twoNumbers ) >>
addition    : 24
subtraction : 16
multiply    : 80
divide      : 5

 << derived0 ( funcs in twoNumbers ) >>
addition    : 10
subtraction : 6
multiply    : 16
divide      : 4

 << derived0 ( funcs in derivedClass ) >>
power              : 64
power    (reverse) : 256
subtract           : 6
subtract (reverse) : -6
divide             : 4
divide   (reverse) : 0
```
