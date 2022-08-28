## Companion Object
### Companion Object Basic
```kotlin
class ClassName private constuctor (cArg1: cArgType1, cArg2: cArgType2, ...) {
  companion object ObjectName : ClassInterface {
    const val constant1: dataType1 = value1
    const val constant2: dataType2 = value2
    ...
    const val constantN: dataTypeN = valueN
    
    // 일반 함수
    fun funcName(arg1: argType1, arg2: argType2, ...) : returnDataType {
      ...
    }
    
    // 오버라이딩된 함수
    override fun overridedFuncName(oArg1: oArgType1, oArg2: oArgType2, ...) : oReturnDataType {
      ...
    }
  }
}

interface ClassInterface {
  fun overridedFuncName(oArg1: oArgType1, oArg2: oArgType2, ...) : oReturnDataType
}
```

여기서 각각은 다음과 같다.
* ```ClassName```: 클래스의 이름
* ```cArg1, cArg2, ...```: 클래스의 인수
  * ```cArgType1, cArgType2, ...```: 클래스의 인수의 data type
* ```ClassInterface```: 클래스의 인터페이스 이름
* ```constant1, constant2, ..., constantN```: 클래스에서 정의되는 상수
  * ```dataType1, dataType2, ..., dataTypeN```: 클래스에서 정의되는 상수의 data type
  * ```value1, value2, ..., valueN```: 이들 상수의 값
* ```funcName```: 함수 이름
  * ```arg1, arg2, ...```: 함수의 인수
  * ```argType1, argType2, ...```: 함수의 인수의 data type
  * ```returnDataType```: 함수의 반환값의 인수
* ```overridedFuncName```: 오버라이딩된 함수 이름
  * ```oArg1, oArg2, ...```: 오버라이딩된 함수의 인수
  * ```oArgType1, oArgType2, ...```: 오버라이딩된 함수의 인수의 data type
  * ```oReturnDataType```: 오버라이딩된 함수의 반환값의 인수

## Example
### Example 1
```kotlin
class Developer private constructor (name: String, language: String, months: Int, hoursPerWeek: Int) {
    companion object developerObject : ClassInterface {
        const val companyName: String = "ExampleSoft"
    	const val commitsPerHour: Double = 0.75
        
        var name: String = ""
        var language: String = ""
        var months: Int = 0
        var hoursPerWeek: Int = 0
        
        // 초기화 및 생성 함수
        fun init(n: String, l: String, m: Int, hpw: Int) {
            this.name = n
            this.language = l
            this.months = m
            this.hoursPerWeek = hpw
        }
        
        fun create(n: String, l: String, m: Int, hpw: Int) : Developer {
            return Developer(n, l, m, hpw)
        }
    
    	// 오버라이딩된 함수
    	override fun getInfo() {
    	    print("developer name = ${this.name}\n")
            print("company   name = ${companyName}\n")
            print("language       = ${this.language}\n")
            print("working months = ${this.months}\n")
            print("hrs per week   = ${this.hoursPerWeek}\n\n")
    	}
    
    	override fun getCommitCount(option: String) : Int {
            when (option) {
                "daily" -> return (hoursPerWeek * commitsPerHour / 7.0).toInt()
                "weekly" -> return (hoursPerWeek * commitsPerHour).toInt()
                "monthly" -> return (4.35 * hoursPerWeek * commitsPerHour).toInt()
                "yearly" -> return (12.0 * 4.35 * hoursPerWeek * commitsPerHour).toInt()
                else -> return (12.0 * 4.35 * hoursPerWeek * commitsPerHour).toInt()
            }
	}
    }
}

interface ClassInterface {
    fun getInfo()
    fun getCommitCount(option: String) : Int
}

fun main() {
    Developer.developerObject.init("hong", "Kotlin", 8, 45)
    Developer.developerObject.getInfo()
    
    print("my daily   commit count : " + Developer.developerObject.getCommitCount("daily").toString() + "\n")
    print("my weekly  commit count : " + Developer.developerObject.getCommitCount("weekly").toString() + "\n")
    print("my monthly commit count : " + Developer.developerObject.getCommitCount("monthly").toString() + "\n")
    print("my yearly  commit count : " + Developer.developerObject.getCommitCount("yearly").toString() + "\n")
}
```

## Output
### Output 1
```kotlin
developer name = hong
company   name = ExampleSoft
language       = Kotlin
working months = 8
hrs per week   = 45

my daily   commit count : 4
my weekly  commit count : 33
my monthly commit count : 146
my yearly  commit count : 1761
```
