## 요구사항 분석
문자열의 집합이 있을 때, 한 문자열이 다른 문자열을 포함하는 경우를 모두 나타내시오. 단, 모든 문자열은 알파벳 소문자만으로 구성된다.

예를 들어 문자열 집합에 ```programming, program, pro, ing```이 있을 때는 다음과 같이 출력한다.
```kotlin
programming -> program, pro, ing
program -> pro
pro -> -
ing -> -
```

### 데이터 클래스
데이터 클래스 없이, 아래 형식과 같은 문자열 집합이 주어진다.
```kotlin
List<String>
```

## 코드 및 실행 결과
### 코드
```kotlin
// 문자열의 포함을 모두 나타내기
fun printAllContainings(strings: List<String>) {
    val containing = mutableMapOf<String, List<String>>()
    
    strings.forEach { s0 ->
        val contained = strings.filter { s1 ->
            s0 != s1 && s0.contains(s1)
        }
        
        // contained에 항목이 없으면 "-"만 있는 리스트를 value로
        if (contained.size == 0) {
            containing.put(s0, listOf("-"))
        } else {
            containing.put(s0, contained)
        }
    }
    
    containing.forEach {
        println("${it.key} -> ${it.value.joinToString(", ")}")
    }
}

fun main() {
    
    // 임시 문자열 목록
    val tempStrings = listOf<String> (
        "developers", "developer", "develop", "dev", "devops",
        "programming", "programmer", "program", "pro", "gram",
        "prodev", "computer", "computer", "com",
        "devmaster", "master", "prodeveloper", "professional"
    )
    
    // 문자열의 포함을 모두 출력
    printAllContainings(tempStrings)
}
```

### 실행 결과
```kotlin
developers -> developer, develop, dev
developer -> develop, dev
develop -> dev
dev -> -
devops -> dev
programming -> program, pro, gram
programmer -> program, pro, gram
program -> pro, gram
pro -> -
gram -> -
prodev -> dev, pro
computer -> com
com -> -
devmaster -> dev, master
master -> -
prodeveloper -> developer, develop, dev, pro, prodev
professional -> pro
```
