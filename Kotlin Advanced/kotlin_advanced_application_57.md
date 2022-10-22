## 요구사항 분석
문자열 ```string```에서 ```조건```을 만족시키는 문자의 개수를 구하시오.

```조건```은 다음과 같다.
* ```0-9```, ```a-z```, ```A-Z```: 각각 숫자, 알파벳 소문자, 알파벳 대문자를 나타낸다. 예를 들어 ```2-5```이면 2부터 5까지의 숫자, ```b-d```이면 b부터 d까지의 알파벳 소문자를 의미한다.
* ```0```, ```a```, ```A```: 각각 숫자 0, 알파벳 소문자 a, 알파벳 대문자 A를 나타낸다.

위의 조건들을 concatenate로 연결하면 이들 조건 중 하나라도 만족시키는 것을 최종 조건으로 한다.
* 예를 들어 ```02-9a-egh```는 숫자 0, 2, 3, ..., 9, 알파벳 a, b, ..., e, g, h 중 하나인 것을 의미한다.

### 데이터 클래스
데이터 클래스는 주어지지 않는다.

## 코드 및 실행 결과
### 코드
```kotlin
// 조건을 만족시키는 문자의 개수 반환
fun findMatchedCharCount(string: String, condition: String): Int {
    
    // 조건이 1글자이면 해당 글자의 개수를 반환
    if (condition.length == 1) {
        return string.count { it == condition[0] }
    }
    
    // 조건을 그 조건에 맞는 문자들의 concatenation으로 변환
    val conditionChars = condition.foldIndexed ( "" ) { idx, acc, e ->
        
        // 첫 글자와 마지막 글자인 경우에 대한 처리
        if (idx == 0) {
            if (condition[1] != '-') {
                acc + e.toString()
            } else {
                acc
            }
        } else if (idx == condition.length - 1) {
            if (condition[idx - 1] != '-') {
                acc + e.toString()
            } else {
                acc
            }
        }
        
        // '-'가 중간 글자인 경우에 대한 처리
        else if (e == '-') {
            val start = condition[idx - 1]
            var end = condition[idx + 1]
            acc + (start..end).joinToString("")
        }
        
        // 중간 글자가 '-'가 아닌 경우
        else {
            
            // '-'로 표현된 범위와 관련이 없으면 추가
            if (condition[idx - 1] != '-' && condition[idx + 1] != '-') {
                acc + e.toString()
            }
            
            // 나머지 경우 (중간이면서 '-'의 양 옆 글자)
            else {
                acc
            }
        }
    }
    
    println("1. condition chars = ${conditionChars}")
    
    // 최종 결과
    return conditionChars.sumOf { cc ->
        string.count { it == cc }
    }
}

fun main() {
    
    // 임시 string 목록
    val tempStrings = listOf<String> (
        "0123456789abcdefghijABCDEFGHIJ",
        "20221022",
        "2022-10-22 22:08:00",
        "wannabesuperteur",
        "WannaBeSuperteur/2022-Kotlin",
        "kotlin_advanced_application_57.md",
        "0112222aaaaaaaa${"b".repeat(16)}${"A".repeat(32)}${"B".repeat(64)}",
        "abbccccdddddddd${"A".repeat(16)}${"B".repeat(32)}${"C".repeat(64)}${"D".repeat(64)}"
    )
    
    // 임시 조건 목록
    val tempConditions = listOf<String> (
        "0",
        "a",
        "0-9",
        "a-z",
        "A-Z",
        "0-9a-zA-Z",
        "05-9a-bn",
        "0-1aA",
        "02a-bC-D",
        "02acB-D",
        "012acdA-C",
        "02-356-8"
    )
    
    // 조건을 만족시키는 문자의 개수 구하기
    tempStrings.forEach { s ->
        tempConditions.forEach { c ->
            println("string = ${s}\ncondition = ${c} -> count = ${findMatchedCharCount(s, c)}\n")
        }
    }
}
```

### 실행 결과
```kotlin
string = 0123456789abcdefghijABCDEFGHIJ
condition = 0 -> count = 1

string = 0123456789abcdefghijABCDEFGHIJ
condition = a -> count = 1

1. condition chars = 0123456789
string = 0123456789abcdefghijABCDEFGHIJ
condition = 0-9 -> count = 10

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = 0123456789abcdefghijABCDEFGHIJ
condition = a-z -> count = 10

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 0123456789abcdefghijABCDEFGHIJ
condition = A-Z -> count = 10

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 0123456789abcdefghijABCDEFGHIJ
condition = 0-9a-zA-Z -> count = 30

1. condition chars = 056789abn
string = 0123456789abcdefghijABCDEFGHIJ
condition = 05-9a-bn -> count = 8

1. condition chars = 01aA
string = 0123456789abcdefghijABCDEFGHIJ
condition = 0-1aA -> count = 4

1. condition chars = 02abCD
string = 0123456789abcdefghijABCDEFGHIJ
condition = 02a-bC-D -> count = 6

1. condition chars = 02acBCD
string = 0123456789abcdefghijABCDEFGHIJ
condition = 02acB-D -> count = 7

1. condition chars = 012acdABC
string = 0123456789abcdefghijABCDEFGHIJ
condition = 012acdA-C -> count = 9

1. condition chars = 0235678
string = 0123456789abcdefghijABCDEFGHIJ
condition = 02-356-8 -> count = 7

string = 20221022
condition = 0 -> count = 2

string = 20221022
condition = a -> count = 0

1. condition chars = 0123456789
string = 20221022
condition = 0-9 -> count = 8

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = 20221022
condition = a-z -> count = 0

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 20221022
condition = A-Z -> count = 0

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 20221022
condition = 0-9a-zA-Z -> count = 8

1. condition chars = 056789abn
string = 20221022
condition = 05-9a-bn -> count = 2

1. condition chars = 01aA
string = 20221022
condition = 0-1aA -> count = 3

1. condition chars = 02abCD
string = 20221022
condition = 02a-bC-D -> count = 7

1. condition chars = 02acBCD
string = 20221022
condition = 02acB-D -> count = 7

1. condition chars = 012acdABC
string = 20221022
condition = 012acdA-C -> count = 8

1. condition chars = 0235678
string = 20221022
condition = 02-356-8 -> count = 7

string = 2022-10-22 22:08:00
condition = 0 -> count = 5

string = 2022-10-22 22:08:00
condition = a -> count = 0

1. condition chars = 0123456789
string = 2022-10-22 22:08:00
condition = 0-9 -> count = 14

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = 2022-10-22 22:08:00
condition = a-z -> count = 0

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 2022-10-22 22:08:00
condition = A-Z -> count = 0

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 2022-10-22 22:08:00
condition = 0-9a-zA-Z -> count = 14

1. condition chars = 056789abn
string = 2022-10-22 22:08:00
condition = 05-9a-bn -> count = 6

1. condition chars = 01aA
string = 2022-10-22 22:08:00
condition = 0-1aA -> count = 6

1. condition chars = 02abCD
string = 2022-10-22 22:08:00
condition = 02a-bC-D -> count = 12

1. condition chars = 02acBCD
string = 2022-10-22 22:08:00
condition = 02acB-D -> count = 12

1. condition chars = 012acdABC
string = 2022-10-22 22:08:00
condition = 012acdA-C -> count = 13

1. condition chars = 0235678
string = 2022-10-22 22:08:00
condition = 02-356-8 -> count = 13

string = wannabesuperteur
condition = 0 -> count = 0

string = wannabesuperteur
condition = a -> count = 2

1. condition chars = 0123456789
string = wannabesuperteur
condition = 0-9 -> count = 0

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = wannabesuperteur
condition = a-z -> count = 16

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = wannabesuperteur
condition = A-Z -> count = 0

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = wannabesuperteur
condition = 0-9a-zA-Z -> count = 16

1. condition chars = 056789abn
string = wannabesuperteur
condition = 05-9a-bn -> count = 5

1. condition chars = 01aA
string = wannabesuperteur
condition = 0-1aA -> count = 2

1. condition chars = 02abCD
string = wannabesuperteur
condition = 02a-bC-D -> count = 3

1. condition chars = 02acBCD
string = wannabesuperteur
condition = 02acB-D -> count = 2

1. condition chars = 012acdABC
string = wannabesuperteur
condition = 012acdA-C -> count = 2

1. condition chars = 0235678
string = wannabesuperteur
condition = 02-356-8 -> count = 0

string = WannaBeSuperteur/2022-Kotlin
condition = 0 -> count = 1

string = WannaBeSuperteur/2022-Kotlin
condition = a -> count = 2

1. condition chars = 0123456789
string = WannaBeSuperteur/2022-Kotlin
condition = 0-9 -> count = 4

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = WannaBeSuperteur/2022-Kotlin
condition = a-z -> count = 18

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = WannaBeSuperteur/2022-Kotlin
condition = A-Z -> count = 4

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = WannaBeSuperteur/2022-Kotlin
condition = 0-9a-zA-Z -> count = 26

1. condition chars = 056789abn
string = WannaBeSuperteur/2022-Kotlin
condition = 05-9a-bn -> count = 6

1. condition chars = 01aA
string = WannaBeSuperteur/2022-Kotlin
condition = 0-1aA -> count = 3

1. condition chars = 02abCD
string = WannaBeSuperteur/2022-Kotlin
condition = 02a-bC-D -> count = 6

1. condition chars = 02acBCD
string = WannaBeSuperteur/2022-Kotlin
condition = 02acB-D -> count = 7

1. condition chars = 012acdABC
string = WannaBeSuperteur/2022-Kotlin
condition = 012acdA-C -> count = 7

1. condition chars = 0235678
string = WannaBeSuperteur/2022-Kotlin
condition = 02-356-8 -> count = 4

string = kotlin_advanced_application_57.md
condition = 0 -> count = 0

string = kotlin_advanced_application_57.md
condition = a -> count = 4

1. condition chars = 0123456789
string = kotlin_advanced_application_57.md
condition = 0-9 -> count = 2

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = kotlin_advanced_application_57.md
condition = a-z -> count = 27

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = kotlin_advanced_application_57.md
condition = A-Z -> count = 0

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = kotlin_advanced_application_57.md
condition = 0-9a-zA-Z -> count = 29

1. condition chars = 056789abn
string = kotlin_advanced_application_57.md
condition = 05-9a-bn -> count = 9

1. condition chars = 01aA
string = kotlin_advanced_application_57.md
condition = 0-1aA -> count = 4

1. condition chars = 02abCD
string = kotlin_advanced_application_57.md
condition = 02a-bC-D -> count = 4

1. condition chars = 02acBCD
string = kotlin_advanced_application_57.md
condition = 02acB-D -> count = 6

1. condition chars = 012acdABC
string = kotlin_advanced_application_57.md
condition = 012acdA-C -> count = 9

1. condition chars = 0235678
string = kotlin_advanced_application_57.md
condition = 02-356-8 -> count = 2

string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 0 -> count = 1

string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = a -> count = 8

1. condition chars = 0123456789
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 0-9 -> count = 7

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = a-z -> count = 24

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = A-Z -> count = 96

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 0-9a-zA-Z -> count = 127

1. condition chars = 056789abn
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 05-9a-bn -> count = 25

1. condition chars = 01aA
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 0-1aA -> count = 43

1. condition chars = 02abCD
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 02a-bC-D -> count = 29

1. condition chars = 02acBCD
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 02acB-D -> count = 77

1. condition chars = 012acdABC
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 012acdA-C -> count = 111

1. condition chars = 0235678
string = 0112222aaaaaaaabbbbbbbbbbbbbbbbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
condition = 02-356-8 -> count = 5

string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 0 -> count = 0

string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = a -> count = 1

1. condition chars = 0123456789
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 0-9 -> count = 0

1. condition chars = abcdefghijklmnopqrstuvwxyz
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = a-z -> count = 15

1. condition chars = ABCDEFGHIJKLMNOPQRSTUVWXYZ
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = A-Z -> count = 176

1. condition chars = 0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 0-9a-zA-Z -> count = 191

1. condition chars = 056789abn
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 05-9a-bn -> count = 3

1. condition chars = 01aA
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 0-1aA -> count = 17

1. condition chars = 02abCD
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 02a-bC-D -> count = 131

1. condition chars = 02acBCD
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 02acB-D -> count = 165

1. condition chars = 012acdABC
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 012acdA-C -> count = 125

1. condition chars = 0235678
string = abbccccddddddddAAAAAAAAAAAAAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDD
condition = 02-356-8 -> count = 0
```
