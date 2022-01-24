# Class의 기본
## 설명
 * Kotlin에서 Class는 다음과 같이 정의 가능
```kotlin
class CLASS_NAME {
  constructor (VALUE0: DATA_TYPE0, VALUE1: DATA_TYPE1, ...) {
    this.VALUE0 = VALUE0
    this.VALUE1 = VALUE1
    ...
  }
  
  var VALUE0: DATA_TYPE0 = INIT_VALUE0
  var VALUE1: DATA_TYPE1 = INIT_VALUE1
  ...
}
```

```kotlin
class CLASS_NAME constructor (VALUE0: DATA_TYPE0, VALUE1: DATA_TYPE1, ...) {
  var VALUE0: DATA_TYPE0 = INIT_VALUE0
  var VALUE1: DATA_TYPE1 = INIT_VALUE1
  ...
}
```

```kotlin
class CLASS_NAME (VALUE0: DATA_TYPE0, VALUE1: DATA_TYPE1, ...) {
  var VALUE0: DATA_TYPE0 = INIT_VALUE0
  var VALUE1: DATA_TYPE1 = INIT_VALUE1
  ...
}
```
 * 위 3가지 정의는 방법만 다를 뿐, 의미는 모두 서로 같음.
 * Class 안에는 함수가 들어갈 수 있음.
 
 * Class에 대한 객체 (instance) 는 다음과 같이 생성할 수 있음.
```kotlin
val INSTANCE = CLASS_NAME (VALUE_OF_VALUE0, VALUE_OF_VALUE1, ...)
```

 * Class의 instance에 대해, Class 내부에 정의된 함수는 다음과 같이 실행.
```kotlin
CLASS_NAME.FUNC_NAME (ARG0, ARG1, ...)
```

## 예제 코드
```kotlin
// Case 1, 2 and 3 are the same
// Case 1
class Developer_0 {
    constructor (name: String, age: Int, favoriteLanguage: String, MBTI:String) {
        this.name = name
        this.age = age
        this.favoriteLanguage = favoriteLanguage
        this.MBTI = MBTI
    }
    
    var name: String = "default name"
    var age: Int = 20
    var favoriteLanguage: String = "Kotlin"
    var MBTI: String = "ISTJ"
    
    // function: print info (only in class Developer_0)
    fun printInfo() {
        println("name = " + this.name + ", age = " + this.age.toString() +
                ", favorite language = " + this.favoriteLanguage + ", MBTI = " + this.MBTI)
    }
}

// Case 2 (with 'constructor')
class Developer_1 constructor(name: String, age: Int, favoriteLanguage: String, MBTI:String) {
    var name: String = name
    var age: Int = age
    var favoriteLanguage: String = favoriteLanguage
    var MBTI: String = MBTI
}

// Case 3 (without 'constructor')
class Developer_2 (name: String, age: Int, favoriteLanguage: String, MBTI: String) {
    var name: String = name
    var age: Int = age
    var favoriteLanguage: String = favoriteLanguage
    var MBTI: String = MBTI
}

fun main() {
    
    // Developer_0 객체 (instance) 'dev0' 생성
    val dev0 = Developer_0("김철수", 30, "Python3", "ESTP")
    
    // Developer_0 객체 (instance) 'dev1' 생성
    val dev1 = Developer_0("이영희", 26, "Java", "ENFJ")
    
    // Developer_0 객체 (instance) 'dev2' 생성
    val dev2 = Developer_0("박민수", 28, "C++", "ISTP")
    
    // Developer_0 객체들에 대해 printInfo 함수 실행
    dev0.printInfo()
    dev1.printInfo()
    dev2.printInfo()
}
```

## 실행 결과
```
name = 김철수, age = 30, favorite language = Python3, MBTI = ESTP
name = 이영희, age = 26, favorite language = Java, MBTI = ENFJ
name = 박민수, age = 28, favorite language = C++, MBTI = ISTP
```
