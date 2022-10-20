## 요구사항 분석
주어진 문자열 ```string```이 주어진 형식의 목록 ```formats```에 있는 형식 중 몇 번째에 있는 형식과 처음으로 일치하는지 출력하시오.

```formats```에 들어가는 형식은 예를 들어 다음과 같다.
```kotlin
0000-00-00 -> (숫자 4개)-(숫자 2개)-(숫자 2개), 예를 들어 1234-56-78
aaa000 -> (알파벳 소문자 3개)(숫자 3개), 예를 들어 abc123
AAAaaa000 -> (알파벳 대문자 3개)(알파벳 소문자 3개)(숫자 3개), 예를 들어 ABCabc123
aa 00-00 -> (알파벳 소문자 2개) (숫자 2개)-(숫자 2개), 예를 들어 ab 01-23
```
단, ```formats```의 각 형식은 ```0```, ```a```, ```A```, ``` ```, ```-```로만 구성되어 있다.

### 데이터 클래스
데이터 클래스는 다음과 같다.
```kotlin
// 데이터 클래스
data class Info (
    val string: String,
    val formats: List<String>
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 데이터 클래스
data class Info (
    val string: String,
    val formats: List<String>
)

// format을 Regex 형식으로 변경
fun formatToRegex(format: String, char: Char, regex: String): String {
    
    // char (0, a 또는 A) 가 나타나기 시작하는 지점과 끝나는 지점 구하기
    val charStartIdx = (0 until format.length).filter {
        val formatWithSpace = " ${format}"
        formatWithSpace[it] != char && formatWithSpace[it + 1] == char
    }
        
    val charEndIdx = (0 until format.length).filter {
        val formatWithSpace = "${format} "
        formatWithSpace[it] == char && formatWithSpace[it + 1] != char
    }
        
    // 해당 char에 해당하는 부분의 개수
    val charsCnt = charStartIdx.size
        
    val formatReplaced = (charsCnt - 1 downTo 0).fold (format) { acc, e ->
        val start = charStartIdx[e]
        val end = charEndIdx[e]
            
        acc.substring(0, start) + "${regex}{${end - start + 1}}" + acc.substring(end + 1)
    }
    
    return formatReplaced
}

// 처음으로 일치하는 형식의 인덱스 찾기 (없으면 null)
fun findFirstMatched(info: Info): Int? {
    var count = 0
    
    // 원소 자체를 구할 때는 find, 인덱스를 구할 때는 indexOfFirst
    val result = info.formats.indexOfFirst { f ->
        
        // string을 Regex 형식으로 변경
        // 1. 연속된 0을 변경
        val numReplaced = formatToRegex(f, '0', "[0-9]")
        
        // 2. 연속된 소문자를 변경
        val lowerReplaced = formatToRegex(numReplaced, 'a', "[a-z]")
        
        // 3. 연속된 대문자를 변경
        val UpperReplaced = formatToRegex(lowerReplaced, 'A', "[A-Z]")
        
        println("try : ${count + 1}")
        println("1. original : ${f}")
        println("2. numReplaced : ${numReplaced}")
        println("3. lowerReplaced : ${lowerReplaced}")
        println("4. UpperReplaced : ${UpperReplaced}\n")
        count++
        
        info.string.matches(Regex(UpperReplaced))
    }
    
    // 최종 결과가 음이 아닌 정수이면 그 정수, 아니면 null을 최종 반환
    if (result >= 0) {
        return result
    } else {
        return null
    }
}

fun main() {
    
    // 임시 데이터 클래스
    val tempInfo = listOf<Info> (
        Info(
            "123-456",
            listOf("000 000", "000-000", "aaa-000", "aaa-aaa")
        ),
        Info(
            "12-34 56-78",
            listOf("aaAA00", "aa-AA 00-00", "aa-00 AA-00", "00-00 00-00", "00 00 00 00", "00-00-00-00")
        ),
        Info(
            "abcd",
            listOf("aaaa", "AAaa", "AAAA", "0000")
        ),
        Info(
            "AB123",
            listOf("aa000", "AA000", "aa-000", "aa 000", "AA 000")
        ),
        Info(
            "ab CD-196",
            listOf("aa AA 000", "aa aa-000", "AA AA-000", "aa AA-000")
        ),
        Info(
            "Cd 41 ----",
            listOf("Aa 00 ---", "aA 00 --", "Aa 00 ----", "Aa00 ----", "Aa 00--")
        ),
        Info(
            "is TJ-200",
            listOf("aa AA 000", "aa aa-000", "AA AA-000", "aaa AA-000")
        ),
        Info(
            "CS 99 ----",
            listOf("Aa 00 ---", "aA 00 --", "aA 00 ----", "Aa00 ----", "Aa 00--")
        )
    )
        
    tempInfo.forEach {
        println("info : ${it}, first matched : ${findFirstMatched(it)}\n")
    }
}
```

### 실행 결과
```kotlin
try : 1
1. original : 000 000
2. numReplaced : [0-9]{3} [0-9]{3}
3. lowerReplaced : [0-9]{3} [0-9]{3}
4. UpperReplaced : [0-9]{3} [0-9]{3}

try : 2
1. original : 000-000
2. numReplaced : [0-9]{3}-[0-9]{3}
3. lowerReplaced : [0-9]{3}-[0-9]{3}
4. UpperReplaced : [0-9]{3}-[0-9]{3}

info : Info(string=123-456, formats=[000 000, 000-000, aaa-000, aaa-aaa]), first matched : 1

try : 1
1. original : aaAA00
2. numReplaced : aaAA[0-9]{2}
3. lowerReplaced : [a-z]{2}AA[0-9]{2}
4. UpperReplaced : [a-z]{2}[A-Z]{2}[0-9]{2}

try : 2
1. original : aa-AA 00-00
2. numReplaced : aa-AA [0-9]{2}-[0-9]{2}
3. lowerReplaced : [a-z]{2}-AA [0-9]{2}-[0-9]{2}
4. UpperReplaced : [a-z]{2}-[A-Z]{2} [0-9]{2}-[0-9]{2}

try : 3
1. original : aa-00 AA-00
2. numReplaced : aa-[0-9]{2} AA-[0-9]{2}
3. lowerReplaced : [a-z]{2}-[0-9]{2} AA-[0-9]{2}
4. UpperReplaced : [a-z]{2}-[0-9]{2} [A-Z]{2}-[0-9]{2}

try : 4
1. original : 00-00 00-00
2. numReplaced : [0-9]{2}-[0-9]{2} [0-9]{2}-[0-9]{2}
3. lowerReplaced : [0-9]{2}-[0-9]{2} [0-9]{2}-[0-9]{2}
4. UpperReplaced : [0-9]{2}-[0-9]{2} [0-9]{2}-[0-9]{2}

info : Info(string=12-34 56-78, formats=[aaAA00, aa-AA 00-00, aa-00 AA-00, 00-00 00-00, 00 00 00 00, 00-00-00-00]), first matched : 3

try : 1
1. original : aaaa
2. numReplaced : aaaa
3. lowerReplaced : [a-z]{4}
4. UpperReplaced : [a-z]{4}

info : Info(string=abcd, formats=[aaaa, AAaa, AAAA, 0000]), first matched : 0

try : 1
1. original : aa000
2. numReplaced : aa[0-9]{3}
3. lowerReplaced : [a-z]{2}[0-9]{3}
4. UpperReplaced : [a-z]{2}[0-9]{3}

try : 2
1. original : AA000
2. numReplaced : AA[0-9]{3}
3. lowerReplaced : AA[0-9]{3}
4. UpperReplaced : [A-Z]{2}[0-9]{3}

info : Info(string=AB123, formats=[aa000, AA000, aa-000, aa 000, AA 000]), first matched : 1

try : 1
1. original : aa AA 000
2. numReplaced : aa AA [0-9]{3}
3. lowerReplaced : [a-z]{2} AA [0-9]{3}
4. UpperReplaced : [a-z]{2} [A-Z]{2} [0-9]{3}

try : 2
1. original : aa aa-000
2. numReplaced : aa aa-[0-9]{3}
3. lowerReplaced : [a-z]{2} [a-z]{2}-[0-9]{3}
4. UpperReplaced : [a-z]{2} [a-z]{2}-[0-9]{3}

try : 3
1. original : AA AA-000
2. numReplaced : AA AA-[0-9]{3}
3. lowerReplaced : AA AA-[0-9]{3}
4. UpperReplaced : [A-Z]{2} [A-Z]{2}-[0-9]{3}

try : 4
1. original : aa AA-000
2. numReplaced : aa AA-[0-9]{3}
3. lowerReplaced : [a-z]{2} AA-[0-9]{3}
4. UpperReplaced : [a-z]{2} [A-Z]{2}-[0-9]{3}

info : Info(string=ab CD-196, formats=[aa AA 000, aa aa-000, AA AA-000, aa AA-000]), first matched : 3

try : 1
1. original : Aa 00 ---
2. numReplaced : Aa [0-9]{2} ---
3. lowerReplaced : A[a-z]{1} [0-9]{2} ---
4. UpperReplaced : [A-Z]{1}[a-z]{1} [0-9]{2} ---

try : 2
1. original : aA 00 --
2. numReplaced : aA [0-9]{2} --
3. lowerReplaced : [a-z]{1}A [0-9]{2} --
4. UpperReplaced : [a-z]{1}[A-Z]{1} [0-9]{2} --

try : 3
1. original : Aa 00 ----
2. numReplaced : Aa [0-9]{2} ----
3. lowerReplaced : A[a-z]{1} [0-9]{2} ----
4. UpperReplaced : [A-Z]{1}[a-z]{1} [0-9]{2} ----

info : Info(string=Cd 41 ----, formats=[Aa 00 ---, aA 00 --, Aa 00 ----, Aa00 ----, Aa 00--]), first matched : 2

try : 1
1. original : aa AA 000
2. numReplaced : aa AA [0-9]{3}
3. lowerReplaced : [a-z]{2} AA [0-9]{3}
4. UpperReplaced : [a-z]{2} [A-Z]{2} [0-9]{3}

try : 2
1. original : aa aa-000
2. numReplaced : aa aa-[0-9]{3}
3. lowerReplaced : [a-z]{2} [a-z]{2}-[0-9]{3}
4. UpperReplaced : [a-z]{2} [a-z]{2}-[0-9]{3}

try : 3
1. original : AA AA-000
2. numReplaced : AA AA-[0-9]{3}
3. lowerReplaced : AA AA-[0-9]{3}
4. UpperReplaced : [A-Z]{2} [A-Z]{2}-[0-9]{3}

try : 4
1. original : aaa AA-000
2. numReplaced : aaa AA-[0-9]{3}
3. lowerReplaced : [a-z]{3} AA-[0-9]{3}
4. UpperReplaced : [a-z]{3} [A-Z]{2}-[0-9]{3}

info : Info(string=is TJ-200, formats=[aa AA 000, aa aa-000, AA AA-000, aaa AA-000]), first matched : null

try : 1
1. original : Aa 00 ---
2. numReplaced : Aa [0-9]{2} ---
3. lowerReplaced : A[a-z]{1} [0-9]{2} ---
4. UpperReplaced : [A-Z]{1}[a-z]{1} [0-9]{2} ---

try : 2
1. original : aA 00 --
2. numReplaced : aA [0-9]{2} --
3. lowerReplaced : [a-z]{1}A [0-9]{2} --
4. UpperReplaced : [a-z]{1}[A-Z]{1} [0-9]{2} --

try : 3
1. original : aA 00 ----
2. numReplaced : aA [0-9]{2} ----
3. lowerReplaced : [a-z]{1}A [0-9]{2} ----
4. UpperReplaced : [a-z]{1}[A-Z]{1} [0-9]{2} ----

try : 4
1. original : Aa00 ----
2. numReplaced : Aa[0-9]{2} ----
3. lowerReplaced : A[a-z]{1}[0-9]{2} ----
4. UpperReplaced : [A-Z]{1}[a-z]{1}[0-9]{2} ----

try : 5
1. original : Aa 00--
2. numReplaced : Aa [0-9]{2}--
3. lowerReplaced : A[a-z]{1} [0-9]{2}--
4. UpperReplaced : [A-Z]{1}[a-z]{1} [0-9]{2}--

info : Info(string=CS 99 ----, formats=[Aa 00 ---, aA 00 --, aA 00 ----, Aa00 ----, Aa 00--]), first matched : null
```
