## Object
### Object Basic
```kotlin
object ObjectNameFactory {
    var variable1: DataType1 = value1
    var variable2: DataType2 = value2
    ...
    var variableN: DataTypeN = valueN
    
    fun funcName(arg1: DataType1, arg2: DataType2, ...) : returnType {
        ...
    }
}

data class ObjectName(
    val constant1: cDataType1
    val constant2: cDataType2
    ...
    val constantN: cDataTypeN
)
```

여기서 각각은 다음과 같다.
* ```ObjectNameFactory```: ```ObjectName```의 data class에 해당하는 object를 생성하기 위한 Factory
* ```variable1, variable2, ..., variableN```: ```ObjectNameFactory```에 들어가는 변수들
  * ```DataType1, DataType2, ..., DataTypeN```: 이들 변수의 data type
  * ```value1, value2, ..., valueN```: 이들 변수의 값
* ```funcName (arg1, arg2) : returnType```: ```ObjectNameFactory```에 들어가는 함수
* ```ObjectName```: data class 형식으로, 실질적인 object의 이름
* ```constant1, constant2, ..., constantN```: ```ObjectName```의 데이터 클래스에 들어가는 상수들
  * ```cDataType1, cDataType2, ..., cDataTypeN```: 이들 상수의 data type 

## Example
### Example 1
```kotlin
object DeveloperFactory {
    val developers = mutableListOf<Developer>()
    
    // Developer 객체 생성
    fun createDeveloper(name: String, language: String, months: Int, hoursPerWeek: Int) : Developer {
        val dev = Developer(name, language, months, hoursPerWeek)
        developers.add(dev)
        return dev
    }
    
    // Developer 객체 찾기
    fun findDeveloper(name: String) : Developer? {
        developers.forEach {
            if (it.name == name) {
                return it
            }
        }
        return null
    }
}

// Developer 데이터 클래스
data class Developer(
    val name: String,
    val language: String,
    val months: Int,
    val hoursPerWeek: Int
)

fun main() {
    
    // developer 생성
    val dev0 : Developer = DeveloperFactory.createDeveloper("hong", "Kotlin", 8, 45)
    val dev1 : Developer = DeveloperFactory.createDeveloper("min", "Java", 15, 42)
    val dev2 : Developer = DeveloperFactory.createDeveloper("seok", "Python3", 30, 41)
    val dev3 : Developer = DeveloperFactory.createDeveloper("yong", "JavaScript", 72, 48)
    
    // developer 출력
    println("hong's name : " + dev0.name)
    println("min's language : " + dev1.language)
    println("seok's working months : " + dev2.months)
    println("yong's hours per week : " + dev3.hoursPerWeek + "\n")
    
    println("size = " + DeveloperFactory.developers.size.toString())
    DeveloperFactory.developers.forEach {
        println("name: " + it.name + ", language: " + it.language + ", working months: " + it.months + ", hours per week: " + it.hoursPerWeek)
    }
    println("")
    
    // developer 검색
    println("print seok : " + DeveloperFactory.findDeveloper("seok"))
    println("print min : " + DeveloperFactory.findDeveloper("min") + "\n")
}
```

## Output
### Output 1
```kotlin
hong's name : hong
min's language : Java
seok's working months : 30
yong's hours per week : 48

size = 4
name: hong, language: Kotlin, working months: 8, hours per week: 45
name: min, language: Java, working months: 15, hours per week: 42
name: seok, language: Python3, working months: 30, hours per week: 41
name: yong, language: JavaScript, working months: 72, hours per week: 48

print seok : Developer(name=seok, language=Python3, months=30, hoursPerWeek=41)
print min : Developer(name=min, language=Java, months=15, hoursPerWeek=42)
```
