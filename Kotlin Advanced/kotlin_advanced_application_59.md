## 요구사항 분석
https://github.com/WannaBeSuperteur/2022-Kotlin/blob/main/Kotlin%20Advanced/kotlin_advanced_regex.md 의 ```string.matches(Regex(regex))``` 함수를 ```isMatched(string, regex)``` 형식으로 작성하시오.

단, 고려해야 하는 정규 표현식의 형식은 다음과 같으며, 문자열에는 알파벳 소문자, 숫자, 공백만 들어간다.
* 문자의 개수
  * ```a``` : 1개의 a
  * ```a{n}``` : n개의 a
  * ```a{a,b}``` : a개 이상 b개 이하의 a
  * ```a{a,}``` : a개 이상의 a
  * ```a+``` : 1개 이상의 a
  * ```a*``` : 0개 이상의 a
* 범위 내의 문자의 개수 (단, 범위는 ```a-b``` 형식으로만 주어진다.)
  * ```[0-9]``` : 1개의 숫자
  * ```[0-9]{n}``` : n개의 숫자
  * ```[0-9]{a,b}``` : a개 이상 b개 이하의 숫자
  * ```[0-9]{a,}``` : a개 이상의 숫자
  * ```[0-9]+``` : 1개 이상의 숫자
  * ```[0-9]*``` : 0개 이상의 숫자

### 데이터 클래스
데이터 클래스는 주어지지 않는다.

## 코드 및 실행 결과
### 코드
```kotlin
// 문자열이 정규 표현식과 매칭되는지 검사
fun isMatched(string: String, regex: String): Boolean {
    
    // 'a' 또는 '[0-9]'에 해당하는 부분 추출
    val prefix = regex.substringBefore("{").substringBefore("+").substringBefore("*")
    val matchedChars = if (prefix.length == 1) {
        prefix
    } else {
        val start = prefix[1]
        val end = prefix[3]
        (start..end).joinToString("")
    }
    println("1. matched chars = ${matchedChars}")
    
    // '{'가 포함된 경우
    if (regex.contains("{")) {
        val info = regex.substringAfter("{").substringBefore("}")

        // a{n} 또는 [0-9]{n}의 꼴
        if (!info.contains(",")) {
            val count = info.toInt()
        
            val isLengthMatched = string.length == count
            val isFormatMatched = string.all {
                it in matchedChars
            }
            return isLengthMatched && isFormatMatched
        }
        
        // a{n,} 또는 [0-9]{n,}의 꼴
        else if (info.substringAfter(",").isEmpty()) {
            val count = info.substringBefore(",").toInt()
            
            val isLengthMatched = string.length >= count
            val isFormatMatched = string.all {
                it in matchedChars
            }
            return isLengthMatched && isFormatMatched
        }
        
        // a{n0,n1} 또는 [0-9]{n0,n1}의 꼴
        else {
            val countMin = info.substringBefore(",").toInt()
            val countMax = info.substringAfter(",").toInt()
            
            val isLengthMatched = string.length >= countMin && string.length <= countMax
            val isFormatMatched = string.all {
                it in matchedChars
            }
            return isLengthMatched && isFormatMatched
        }
    }
    
    // '+'가 포함된 경우
    else if (regex.contains("+")) {
        return string.isNotEmpty() && string.all {
            it in matchedChars
        }
    }
    
    // '*'가 포함된 경우
    else if (regex.contains("*")) {
        return string.all {
            it in matchedChars
        }
    }
    
    // 그렇지 않고 단일 문자인 경우
    else {
        matchedChars.forEach {
            if (string == it.toString()) {
                return true
            }
        }
        return false
    }
}

fun main() {
    
    // 임시 문자열 목록
    val tempStrings = listOf<String> (
        "", "0", "00", "022", "0200", "00110",
        "041", "0123",
        "a", "aa", "aaa", "aaaa", "aaaaa",
        "abb", "abcd",
        "1a", "b2"
    )
    
    // 임시 정규 표현식 목록
    val tempRegexes = listOf<String> (
        "0", "a",
        "a{2}", "a{2,}", "a{1,3}", "a{0}", "a{0,}", "a{0,2}", "a+", "a*",
        "[0-9]", "[0-9]{2}", "[0-2]{2,}", "[0-2]{1,3}", "[0-2]+", "[0-2]*"
    )
    
    // 일치 여부 검사
    tempStrings.forEach { s ->
        tempRegexes.forEach { r ->
            println("${s}, ${r} -> matched = ${isMatched(s, r)}\n")
        }
    }
}
```

### 실행 결과
```kotlin
1. matched chars = 0
, 0 -> matched = false

1. matched chars = a
, a -> matched = false

1. matched chars = a
, a{2} -> matched = false

1. matched chars = a
, a{2,} -> matched = false

1. matched chars = a
, a{1,3} -> matched = false

1. matched chars = a
, a{0} -> matched = true

1. matched chars = a
, a{0,} -> matched = true

1. matched chars = a
, a{0,2} -> matched = true

1. matched chars = a
, a+ -> matched = false

1. matched chars = a
, a* -> matched = true

1. matched chars = 0123456789
, [0-9] -> matched = false

1. matched chars = 0123456789
, [0-9]{2} -> matched = false

1. matched chars = 012
, [0-2]{2,} -> matched = false

1. matched chars = 012
, [0-2]{1,3} -> matched = false

1. matched chars = 012
, [0-2]+ -> matched = false

1. matched chars = 012
, [0-2]* -> matched = true

1. matched chars = 0
0, 0 -> matched = true

1. matched chars = a
0, a -> matched = false

1. matched chars = a
0, a{2} -> matched = false

1. matched chars = a
0, a{2,} -> matched = false

1. matched chars = a
0, a{1,3} -> matched = false

1. matched chars = a
0, a{0} -> matched = false

1. matched chars = a
0, a{0,} -> matched = false

1. matched chars = a
0, a{0,2} -> matched = false

1. matched chars = a
0, a+ -> matched = false

1. matched chars = a
0, a* -> matched = false

1. matched chars = 0123456789
0, [0-9] -> matched = true

1. matched chars = 0123456789
0, [0-9]{2} -> matched = false

1. matched chars = 012
0, [0-2]{2,} -> matched = false

1. matched chars = 012
0, [0-2]{1,3} -> matched = true

1. matched chars = 012
0, [0-2]+ -> matched = true

1. matched chars = 012
0, [0-2]* -> matched = true

1. matched chars = 0
00, 0 -> matched = false

1. matched chars = a
00, a -> matched = false

1. matched chars = a
00, a{2} -> matched = false

1. matched chars = a
00, a{2,} -> matched = false

1. matched chars = a
00, a{1,3} -> matched = false

1. matched chars = a
00, a{0} -> matched = false

1. matched chars = a
00, a{0,} -> matched = false

1. matched chars = a
00, a{0,2} -> matched = false

1. matched chars = a
00, a+ -> matched = false

1. matched chars = a
00, a* -> matched = false

1. matched chars = 0123456789
00, [0-9] -> matched = false

1. matched chars = 0123456789
00, [0-9]{2} -> matched = true

1. matched chars = 012
00, [0-2]{2,} -> matched = true

1. matched chars = 012
00, [0-2]{1,3} -> matched = true

1. matched chars = 012
00, [0-2]+ -> matched = true

1. matched chars = 012
00, [0-2]* -> matched = true

1. matched chars = 0
022, 0 -> matched = false

1. matched chars = a
022, a -> matched = false

1. matched chars = a
022, a{2} -> matched = false

1. matched chars = a
022, a{2,} -> matched = false

1. matched chars = a
022, a{1,3} -> matched = false

1. matched chars = a
022, a{0} -> matched = false

1. matched chars = a
022, a{0,} -> matched = false

1. matched chars = a
022, a{0,2} -> matched = false

1. matched chars = a
022, a+ -> matched = false

1. matched chars = a
022, a* -> matched = false

1. matched chars = 0123456789
022, [0-9] -> matched = false

1. matched chars = 0123456789
022, [0-9]{2} -> matched = false

1. matched chars = 012
022, [0-2]{2,} -> matched = true

1. matched chars = 012
022, [0-2]{1,3} -> matched = true

1. matched chars = 012
022, [0-2]+ -> matched = true

1. matched chars = 012
022, [0-2]* -> matched = true

1. matched chars = 0
0200, 0 -> matched = false

1. matched chars = a
0200, a -> matched = false

1. matched chars = a
0200, a{2} -> matched = false

1. matched chars = a
0200, a{2,} -> matched = false

1. matched chars = a
0200, a{1,3} -> matched = false

1. matched chars = a
0200, a{0} -> matched = false

1. matched chars = a
0200, a{0,} -> matched = false

1. matched chars = a
0200, a{0,2} -> matched = false

1. matched chars = a
0200, a+ -> matched = false

1. matched chars = a
0200, a* -> matched = false

1. matched chars = 0123456789
0200, [0-9] -> matched = false

1. matched chars = 0123456789
0200, [0-9]{2} -> matched = false

1. matched chars = 012
0200, [0-2]{2,} -> matched = true

1. matched chars = 012
0200, [0-2]{1,3} -> matched = false

1. matched chars = 012
0200, [0-2]+ -> matched = true

1. matched chars = 012
0200, [0-2]* -> matched = true

1. matched chars = 0
00110, 0 -> matched = false

1. matched chars = a
00110, a -> matched = false

1. matched chars = a
00110, a{2} -> matched = false

1. matched chars = a
00110, a{2,} -> matched = false

1. matched chars = a
00110, a{1,3} -> matched = false

1. matched chars = a
00110, a{0} -> matched = false

1. matched chars = a
00110, a{0,} -> matched = false

1. matched chars = a
00110, a{0,2} -> matched = false

1. matched chars = a
00110, a+ -> matched = false

1. matched chars = a
00110, a* -> matched = false

1. matched chars = 0123456789
00110, [0-9] -> matched = false

1. matched chars = 0123456789
00110, [0-9]{2} -> matched = false

1. matched chars = 012
00110, [0-2]{2,} -> matched = true

1. matched chars = 012
00110, [0-2]{1,3} -> matched = false

1. matched chars = 012
00110, [0-2]+ -> matched = true

1. matched chars = 012
00110, [0-2]* -> matched = true

1. matched chars = 0
041, 0 -> matched = false

1. matched chars = a
041, a -> matched = false

1. matched chars = a
041, a{2} -> matched = false

1. matched chars = a
041, a{2,} -> matched = false

1. matched chars = a
041, a{1,3} -> matched = false

1. matched chars = a
041, a{0} -> matched = false

1. matched chars = a
041, a{0,} -> matched = false

1. matched chars = a
041, a{0,2} -> matched = false

1. matched chars = a
041, a+ -> matched = false

1. matched chars = a
041, a* -> matched = false

1. matched chars = 0123456789
041, [0-9] -> matched = false

1. matched chars = 0123456789
041, [0-9]{2} -> matched = false

1. matched chars = 012
041, [0-2]{2,} -> matched = false

1. matched chars = 012
041, [0-2]{1,3} -> matched = false

1. matched chars = 012
041, [0-2]+ -> matched = false

1. matched chars = 012
041, [0-2]* -> matched = false

1. matched chars = 0
0123, 0 -> matched = false

1. matched chars = a
0123, a -> matched = false

1. matched chars = a
0123, a{2} -> matched = false

1. matched chars = a
0123, a{2,} -> matched = false

1. matched chars = a
0123, a{1,3} -> matched = false

1. matched chars = a
0123, a{0} -> matched = false

1. matched chars = a
0123, a{0,} -> matched = false

1. matched chars = a
0123, a{0,2} -> matched = false

1. matched chars = a
0123, a+ -> matched = false

1. matched chars = a
0123, a* -> matched = false

1. matched chars = 0123456789
0123, [0-9] -> matched = false

1. matched chars = 0123456789
0123, [0-9]{2} -> matched = false

1. matched chars = 012
0123, [0-2]{2,} -> matched = false

1. matched chars = 012
0123, [0-2]{1,3} -> matched = false

1. matched chars = 012
0123, [0-2]+ -> matched = false

1. matched chars = 012
0123, [0-2]* -> matched = false

1. matched chars = 0
a, 0 -> matched = false

1. matched chars = a
a, a -> matched = true

1. matched chars = a
a, a{2} -> matched = false

1. matched chars = a
a, a{2,} -> matched = false

1. matched chars = a
a, a{1,3} -> matched = true

1. matched chars = a
a, a{0} -> matched = false

1. matched chars = a
a, a{0,} -> matched = true

1. matched chars = a
a, a{0,2} -> matched = true

1. matched chars = a
a, a+ -> matched = true

1. matched chars = a
a, a* -> matched = true

1. matched chars = 0123456789
a, [0-9] -> matched = false

1. matched chars = 0123456789
a, [0-9]{2} -> matched = false

1. matched chars = 012
a, [0-2]{2,} -> matched = false

1. matched chars = 012
a, [0-2]{1,3} -> matched = false

1. matched chars = 012
a, [0-2]+ -> matched = false

1. matched chars = 012
a, [0-2]* -> matched = false

1. matched chars = 0
aa, 0 -> matched = false

1. matched chars = a
aa, a -> matched = false

1. matched chars = a
aa, a{2} -> matched = true

1. matched chars = a
aa, a{2,} -> matched = true

1. matched chars = a
aa, a{1,3} -> matched = true

1. matched chars = a
aa, a{0} -> matched = false

1. matched chars = a
aa, a{0,} -> matched = true

1. matched chars = a
aa, a{0,2} -> matched = true

1. matched chars = a
aa, a+ -> matched = true

1. matched chars = a
aa, a* -> matched = true

1. matched chars = 0123456789
aa, [0-9] -> matched = false

1. matched chars = 0123456789
aa, [0-9]{2} -> matched = false

1. matched chars = 012
aa, [0-2]{2,} -> matched = false

1. matched chars = 012
aa, [0-2]{1,3} -> matched = false

1. matched chars = 012
aa, [0-2]+ -> matched = false

1. matched chars = 012
aa, [0-2]* -> matched = false

1. matched chars = 0
aaa, 0 -> matched = false

1. matched chars = a
aaa, a -> matched = false

1. matched chars = a
aaa, a{2} -> matched = false

1. matched chars = a
aaa, a{2,} -> matched = true

1. matched chars = a
aaa, a{1,3} -> matched = true

1. matched chars = a
aaa, a{0} -> matched = false

1. matched chars = a
aaa, a{0,} -> matched = true

1. matched chars = a
aaa, a{0,2} -> matched = false

1. matched chars = a
aaa, a+ -> matched = true

1. matched chars = a
aaa, a* -> matched = true

1. matched chars = 0123456789
aaa, [0-9] -> matched = false

1. matched chars = 0123456789
aaa, [0-9]{2} -> matched = false

1. matched chars = 012
aaa, [0-2]{2,} -> matched = false

1. matched chars = 012
aaa, [0-2]{1,3} -> matched = false

1. matched chars = 012
aaa, [0-2]+ -> matched = false

1. matched chars = 012
aaa, [0-2]* -> matched = false

1. matched chars = 0
aaaa, 0 -> matched = false

1. matched chars = a
aaaa, a -> matched = false

1. matched chars = a
aaaa, a{2} -> matched = false

1. matched chars = a
aaaa, a{2,} -> matched = true

1. matched chars = a
aaaa, a{1,3} -> matched = false

1. matched chars = a
aaaa, a{0} -> matched = false

1. matched chars = a
aaaa, a{0,} -> matched = true

1. matched chars = a
aaaa, a{0,2} -> matched = false

1. matched chars = a
aaaa, a+ -> matched = true

1. matched chars = a
aaaa, a* -> matched = true

1. matched chars = 0123456789
aaaa, [0-9] -> matched = false

1. matched chars = 0123456789
aaaa, [0-9]{2} -> matched = false

1. matched chars = 012
aaaa, [0-2]{2,} -> matched = false

1. matched chars = 012
aaaa, [0-2]{1,3} -> matched = false

1. matched chars = 012
aaaa, [0-2]+ -> matched = false

1. matched chars = 012
aaaa, [0-2]* -> matched = false

1. matched chars = 0
aaaaa, 0 -> matched = false

1. matched chars = a
aaaaa, a -> matched = false

1. matched chars = a
aaaaa, a{2} -> matched = false

1. matched chars = a
aaaaa, a{2,} -> matched = true

1. matched chars = a
aaaaa, a{1,3} -> matched = false

1. matched chars = a
aaaaa, a{0} -> matched = false

1. matched chars = a
aaaaa, a{0,} -> matched = true

1. matched chars = a
aaaaa, a{0,2} -> matched = false

1. matched chars = a
aaaaa, a+ -> matched = true

1. matched chars = a
aaaaa, a* -> matched = true

1. matched chars = 0123456789
aaaaa, [0-9] -> matched = false

1. matched chars = 0123456789
aaaaa, [0-9]{2} -> matched = false

1. matched chars = 012
aaaaa, [0-2]{2,} -> matched = false

1. matched chars = 012
aaaaa, [0-2]{1,3} -> matched = false

1. matched chars = 012
aaaaa, [0-2]+ -> matched = false

1. matched chars = 012
aaaaa, [0-2]* -> matched = false

1. matched chars = 0
abb, 0 -> matched = false

1. matched chars = a
abb, a -> matched = false

1. matched chars = a
abb, a{2} -> matched = false

1. matched chars = a
abb, a{2,} -> matched = false

1. matched chars = a
abb, a{1,3} -> matched = false

1. matched chars = a
abb, a{0} -> matched = false

1. matched chars = a
abb, a{0,} -> matched = false

1. matched chars = a
abb, a{0,2} -> matched = false

1. matched chars = a
abb, a+ -> matched = false

1. matched chars = a
abb, a* -> matched = false

1. matched chars = 0123456789
abb, [0-9] -> matched = false

1. matched chars = 0123456789
abb, [0-9]{2} -> matched = false

1. matched chars = 012
abb, [0-2]{2,} -> matched = false

1. matched chars = 012
abb, [0-2]{1,3} -> matched = false

1. matched chars = 012
abb, [0-2]+ -> matched = false

1. matched chars = 012
abb, [0-2]* -> matched = false

1. matched chars = 0
abcd, 0 -> matched = false

1. matched chars = a
abcd, a -> matched = false

1. matched chars = a
abcd, a{2} -> matched = false

1. matched chars = a
abcd, a{2,} -> matched = false

1. matched chars = a
abcd, a{1,3} -> matched = false

1. matched chars = a
abcd, a{0} -> matched = false

1. matched chars = a
abcd, a{0,} -> matched = false

1. matched chars = a
abcd, a{0,2} -> matched = false

1. matched chars = a
abcd, a+ -> matched = false

1. matched chars = a
abcd, a* -> matched = false

1. matched chars = 0123456789
abcd, [0-9] -> matched = false

1. matched chars = 0123456789
abcd, [0-9]{2} -> matched = false

1. matched chars = 012
abcd, [0-2]{2,} -> matched = false

1. matched chars = 012
abcd, [0-2]{1,3} -> matched = false

1. matched chars = 012
abcd, [0-2]+ -> matched = false

1. matched chars = 012
abcd, [0-2]* -> matched = false

1. matched chars = 0
1a, 0 -> matched = false

1. matched chars = a
1a, a -> matched = false

1. matched chars = a
1a, a{2} -> matched = false

1. matched chars = a
1a, a{2,} -> matched = false

1. matched chars = a
1a, a{1,3} -> matched = false

1. matched chars = a
1a, a{0} -> matched = false

1. matched chars = a
1a, a{0,} -> matched = false

1. matched chars = a
1a, a{0,2} -> matched = false

1. matched chars = a
1a, a+ -> matched = false

1. matched chars = a
1a, a* -> matched = false

1. matched chars = 0123456789
1a, [0-9] -> matched = false

1. matched chars = 0123456789
1a, [0-9]{2} -> matched = false

1. matched chars = 012
1a, [0-2]{2,} -> matched = false

1. matched chars = 012
1a, [0-2]{1,3} -> matched = false

1. matched chars = 012
1a, [0-2]+ -> matched = false

1. matched chars = 012
1a, [0-2]* -> matched = false

1. matched chars = 0
b2, 0 -> matched = false

1. matched chars = a
b2, a -> matched = false

1. matched chars = a
b2, a{2} -> matched = false

1. matched chars = a
b2, a{2,} -> matched = false

1. matched chars = a
b2, a{1,3} -> matched = false

1. matched chars = a
b2, a{0} -> matched = false

1. matched chars = a
b2, a{0,} -> matched = false

1. matched chars = a
b2, a{0,2} -> matched = false

1. matched chars = a
b2, a+ -> matched = false

1. matched chars = a
b2, a* -> matched = false

1. matched chars = 0123456789
b2, [0-9] -> matched = false

1. matched chars = 0123456789
b2, [0-9]{2} -> matched = false

1. matched chars = 012
b2, [0-2]{2,} -> matched = false

1. matched chars = 012
b2, [0-2]{1,3} -> matched = false

1. matched chars = 012
b2, [0-2]+ -> matched = false

1. matched chars = 012
b2, [0-2]* -> matched = false
```
