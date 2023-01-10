## 요구사항 분석
주어진 string을 서로 동일한 n부분으로 나누었을 때의 각 부분을 다음과 같이 출력하시오. (단, n은 2 이상의 자연수)

예를 들어,
```kotlin
kotlinkotlinkotlinkotlin
```
은 다음과 같이 출력한다.
```kotlin
2 : kotlinkotlin
4 : kotlin
```

## 코드
```kotlin
// ref: Extension Function (https://kotlinlang.org/docs/extensions.html)
fun List<String>.allTheSame(): Boolean {
    if (this.size <= 1) {
        return true
    } else {
        for (i in 1 until this.size) {
            if (this[i] != this[0]) {
                return false
            }
        }
    }
    return true
}

// 약수가 내림차순으로 정렬되므로, 반환 시 반대로 정렬 (asReversed())
fun findDivisors(n: Int): List<Int> {
    return (1..(n / 2)).filter { n % it == 0 }.map { n / it }.asReversed()
}

fun printSameParts(str: String) {
    println("\nstring: ${str}")
    val leng = str.length
    
    val divisors = findDivisors(leng)
    println("1. length = ${leng}, divisors (n) = ${divisors}")
    
    // 동일한 길이로 나누기 위해서 chunked()를 이용
    val results = divisors.map { n ->
        val eachLength = leng / n
        str.chunked(eachLength)
    }
    results.forEach {
        println("2. result: ${it}")
    }
    
    // 최종 결과 출력 (2가지 옵션을 대응되는 것끼지 적용하기 위해서 zip()을 이용)
    val title = listOf("EXACT", "NOT EXACT")
    val isExact = listOf(true, false)
    
    print("\n")
    title.zip(isExact) { t, e ->
        println("==== $t ====")
        
        // 각 결과에 대해 divisor(=n)와 result도 서로 대응하므로 zip()을 이용
        divisors.zip(results) { n, result ->
            if (result.allTheSame() == e) {
                println("$n -> ${result[0]}")
            }
        }
    }
}

fun main() {
    val strings = listOf(
        "kotlin",
        "kotlinjavakotlinjava",
        "kotlinkotlinkotlinkotlin",
        "ababaabb",
        "aaabbbccaaabbbccaaabbbcc",
        "abababcdcdcdabababcdcdcd",
        "abcdabcdabcdabcd",
        "abcdabcdabcdabcdabcdabcd"
    )
    
    strings.forEach {
        printSameParts(it)
    }
}
```

## 실행 결과
```kotlin

string: kotlin
1. length = 6, divisors (n) = [2, 3, 6]
2. result: [kot, lin]
2. result: [ko, tl, in]
2. result: [k, o, t, l, i, n]

==== EXACT ====
==== NOT EXACT ====
2 -> kot
3 -> ko
6 -> k

string: kotlinjavakotlinjava
1. length = 20, divisors (n) = [2, 4, 5, 10, 20]
2. result: [kotlinjava, kotlinjava]
2. result: [kotli, njava, kotli, njava]
2. result: [kotl, inja, vako, tlin, java]
2. result: [ko, tl, in, ja, va, ko, tl, in, ja, va]
2. result: [k, o, t, l, i, n, j, a, v, a, k, o, t, l, i, n, j, a, v, a]

==== EXACT ====
2 -> kotlinjava
==== NOT EXACT ====
4 -> kotli
5 -> kotl
10 -> ko
20 -> k

string: kotlinkotlinkotlinkotlin
1. length = 24, divisors (n) = [2, 3, 4, 6, 8, 12, 24]
2. result: [kotlinkotlin, kotlinkotlin]
2. result: [kotlinko, tlinkotl, inkotlin]
2. result: [kotlin, kotlin, kotlin, kotlin]
2. result: [kotl, inko, tlin, kotl, inko, tlin]
2. result: [kot, lin, kot, lin, kot, lin, kot, lin]
2. result: [ko, tl, in, ko, tl, in, ko, tl, in, ko, tl, in]
2. result: [k, o, t, l, i, n, k, o, t, l, i, n, k, o, t, l, i, n, k, o, t, l, i, n]

==== EXACT ====
2 -> kotlinkotlin
4 -> kotlin
==== NOT EXACT ====
3 -> kotlinko
6 -> kotl
8 -> kot
12 -> ko
24 -> k

string: ababaabb
1. length = 8, divisors (n) = [2, 4, 8]
2. result: [abab, aabb]
2. result: [ab, ab, aa, bb]
2. result: [a, b, a, b, a, a, b, b]

==== EXACT ====
==== NOT EXACT ====
2 -> abab
4 -> ab
8 -> a

string: aaabbbccaaabbbccaaabbbcc
1. length = 24, divisors (n) = [2, 3, 4, 6, 8, 12, 24]
2. result: [aaabbbccaaab, bbccaaabbbcc]
2. result: [aaabbbcc, aaabbbcc, aaabbbcc]
2. result: [aaabbb, ccaaab, bbccaa, abbbcc]
2. result: [aaab, bbcc, aaab, bbcc, aaab, bbcc]
2. result: [aaa, bbb, cca, aab, bbc, caa, abb, bcc]
2. result: [aa, ab, bb, cc, aa, ab, bb, cc, aa, ab, bb, cc]
2. result: [a, a, a, b, b, b, c, c, a, a, a, b, b, b, c, c, a, a, a, b, b, b, c, c]

==== EXACT ====
3 -> aaabbbcc
==== NOT EXACT ====
2 -> aaabbbccaaab
4 -> aaabbb
6 -> aaab
8 -> aaa
12 -> aa
24 -> a

string: abababcdcdcdabababcdcdcd
1. length = 24, divisors (n) = [2, 3, 4, 6, 8, 12, 24]
2. result: [abababcdcdcd, abababcdcdcd]
2. result: [abababcd, cdcdabab, abcdcdcd]
2. result: [ababab, cdcdcd, ababab, cdcdcd]
2. result: [abab, abcd, cdcd, abab, abcd, cdcd]
2. result: [aba, bab, cdc, dcd, aba, bab, cdc, dcd]
2. result: [ab, ab, ab, cd, cd, cd, ab, ab, ab, cd, cd, cd]
2. result: [a, b, a, b, a, b, c, d, c, d, c, d, a, b, a, b, a, b, c, d, c, d, c, d]

==== EXACT ====
2 -> abababcdcdcd
==== NOT EXACT ====
3 -> abababcd
4 -> ababab
6 -> abab
8 -> aba
12 -> ab
24 -> a

string: abcdabcdabcdabcd
1. length = 16, divisors (n) = [2, 4, 8, 16]
2. result: [abcdabcd, abcdabcd]
2. result: [abcd, abcd, abcd, abcd]
2. result: [ab, cd, ab, cd, ab, cd, ab, cd]
2. result: [a, b, c, d, a, b, c, d, a, b, c, d, a, b, c, d]

==== EXACT ====
2 -> abcdabcd
4 -> abcd
==== NOT EXACT ====
8 -> ab
16 -> a

string: abcdabcdabcdabcdabcdabcd
1. length = 24, divisors (n) = [2, 3, 4, 6, 8, 12, 24]
2. result: [abcdabcdabcd, abcdabcdabcd]
2. result: [abcdabcd, abcdabcd, abcdabcd]
2. result: [abcdab, cdabcd, abcdab, cdabcd]
2. result: [abcd, abcd, abcd, abcd, abcd, abcd]
2. result: [abc, dab, cda, bcd, abc, dab, cda, bcd]
2. result: [ab, cd, ab, cd, ab, cd, ab, cd, ab, cd, ab, cd]
2. result: [a, b, c, d, a, b, c, d, a, b, c, d, a, b, c, d, a, b, c, d, a, b, c, d]

==== EXACT ====
2 -> abcdabcdabcd
3 -> abcdabcd
6 -> abcd
==== NOT EXACT ====
4 -> abcdab
8 -> abc
12 -> ab
24 -> a
```
