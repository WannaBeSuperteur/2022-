# listOf
## 설명
 * 다음과 같이 ```listOf```를 이용하여 list를 정의할 수 있음.
   * ```val VAL_NAME = listOf(VALUE0, VALUE1, ...)```
   * 이 list는 ```VALUE0```, ```VALUE1```과 같은 값을 포함.
   * 이 방법으로 정의된 list에는 새로운 항목을 추가할 수 없음. (즉, 해당 list를 수정할 수 없음)
     * ```val NEW_VAL_NAME = VAL_NAME - VALUE_X```를 통해, ```listOf```로 정의된 ```VAL_NAME``` 에서 ```VALUE_X```라는 값만 제외한 새로운 list를 생성할 수 있음.
     * ```val NEW_VAL_NAME = VAL_NAME + VALUE_X```를 통해, ```listOf```로 정의된 ```VAL_NAME``` 에서 ```VALUE_X```라는 값을 추가한 새로운 list를 생성할 수 있음.
 * ```listOf``` 대신 다음과 같이 ```mutableListOf```를 이용하면 <b>값을 추가할 수 있는</b> list를 정의할 수 있음.
   * ```val VAL_NAME = mutableListOf<DATA_TYPE>(VALUE0, VALUE1, ...)```
   * 새로운 값 ```VALUE_X```를 추가하려면 ```VAL_NAME.add(VALUE_X)```를 이용.
 * 리스트의 값 출력
```kotlin
for (i in 0..LIST_NAME.size-1) {
  println(LIST_NAME.get(i))
}
```
또는
```kotlin
for (i in LIST_NAME) {
  println(i)
}
```
 * ```filterNotNull```
   * list에서 ```null```인 항목을 제외하고 출력할 때 사용
   * ```VAL_NAME.filterNotNull()```은 ```VAL_NAME``` 리스트에서 ```null```인 값을 제외한 것을 의미함. 

## 예제 코드
```kotlin
fun main() {
    
    // listOf를 이용하여 list를 정의
    val languages = listOf("Kotlin", "Java", "Swift", "Python2", "Python3", "C", "C++")
    
    // Error: Unresolved reference: add
   	// languages.add("Go")
    // languages.add("Objective-C")
    
    // listOf로 정의된 list의 각 항목 출력
    println(" ==== listOf programming languages (1) ====")
    
    for (i in 0..languages.size-1) {
        println(languages.get(i)) // get(i) : i번째 항목을 반환
    }
    
    println("\n ==== listOf programming languages (2) ====")
    
    for (i in languages) {
        println(i)
    }
    
    // mutableListOf를 이용하여 list를 정의
    val languagesMutable = mutableListOf<String>("Kotlin", "Java", "Swift", "Python2", "Python3", "C", "C++")
    
    // 새로운 항목 추가 가능
    languagesMutable.add("Go")
    languagesMutable.add("Objective-C")
    
    // mutableListOf로 정의된 list의 각 항목 출력
    println("\n ==== mutableListOf programming languages ====")
    
    for (i in 0..languagesMutable.size-1) {
        println(languagesMutable.get(i)) // get(i) : i번째 항목을 반환
    }
    
    // filterNotNull 테스트
    val languagesFilterNotNull = listOf("Kotlin", "Java", "Swift", null, "Python2", "Python3", null, "C", "C++")

	// filterNotNull을 이용하여 null 값을 제외하고 출력
	println("\n ==== (filterNotNull) listOf programming languages ====")
	
	for (i in languagesFilterNotNull.filterNotNull()) {
        println(i)
    }

    // listOf로 정의된 list의 항목 추가 및 삭제
    val languages_0 = languages - "Python2" // Python2 제외
    val languages_1 = languages_0 - "Python3" // Python2, Python3 제외
    val languages_2 = languages + "Ruby" // Ruby 추가
    
    println("\n ==== (add, remove) listOf programming languages ====")
    println("remove Python2 :\n" + languages_0.toString())
    println("remove Python2 and Python3 :\n" + languages_1.toString())
    println("add Ruby :\n" + languages_2.toString())
}
```

## 실행 결과
```
 ==== listOf programming languages (1) ====
Kotlin
Java
Swift
Python2
Python3
C
C++

 ==== listOf programming languages (2) ====
Kotlin
Java
Swift
Python2
Python3
C
C++

 ==== mutableListOf programming languages ====
Kotlin
Java
Swift
Python2
Python3
C
C++
Go
Objective-C

 ==== (filterNotNull) listOf programming languages ====
Kotlin
Java
Swift
Python2
Python3
C
C++

 ==== (add, remove) listOf programming languages ====
remove Python2 :
[Kotlin, Java, Swift, Python3, C, C++]
remove Python2 and Python3 :
[Kotlin, Java, Swift, C, C++]
add Ruby :
[Kotlin, Java, Swift, Python2, Python3, C, C++, Ruby]
```
