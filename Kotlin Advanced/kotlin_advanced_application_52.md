## 요구사항 분석
총 6개의 칸 A, B, C, D, E, F를 8가지 색 a, b, c, d, e, f, g, h 중 하나로 각각 칠하는 경우의 수는 ```8^6 = 262,144```가지이다.

이들 각 경우에 대해, 다음과 같은 4가지 형식의 제약 조건들이 있다.
* ```A == B``` : 두 칸 A, B는 서로 같은 색으로 칠해야 한다.
* ```A != B``` : 두 칸 A, B는 서로 다른 색으로 칠해야 한다.
* ```A == a,b,c``` : 칸 A는 a, b, c 중 한 가지 색으로 칠해야 한다.
* ```A != a,b,c``` : 칸 A는 a, b, c가 아닌 다른 한 가지 색으로 칠해야 한다.

다음과 같이 **데이터 클래스**가 주어질 때, (a, a, a, a, a, a)로 칠하는 경우부터 시작하여 최초 100가지 가능한 경우를 구하고, 500, 1000, 2000, 3000번째 경우를 구하고, 전체 경우의 수를 구하시오.

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
// 데이터 클래스
data class Info (
    val constraints: List<String> // 제약 조건 목록
)
```

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// 데이터 클래스
data class Info (
    val constraints: List<String> // 제약 조건 목록
)

// 가능한 경우 및 전체 경우의 수 출력
fun printCases(info: Info) {
    var possibleCount = 0
    val cases = 8.0.pow(6.0).toInt()
    
    for (i in 0 until cases) {
        
        // 인덱스 i를 각 칸에 칠해진 색 정보로 변환
        val colors = listOf("a", "b", "c", "d", "e", "f", "g", "h")
        val colorInfo = (5 downTo 0).map {
            val mod = (i / colors.size.toDouble().pow(it.toDouble())) % colors.size
            colors[mod.toInt()]
        }
        val cellMap = mapOf("A" to 0, "B" to 1, "C" to 2, "D" to 3, "E" to 4, "F" to 5)
        var isMatched = true
        
        // 각 조건을 검토, info.constraints.forEach { 도 가능
        val constCount = info.constraints.size
        
        for (i in 0 until constCount) {
            val cond = info.constraints[i]
            
            // A == B : 두 칸 A, B는 서로 같은 색으로 칠해야 한다.
            // A != B : 두 칸 A, B는 서로 다른 색으로 칠해야 한다.
            if (cond.matches(Regex("[A-Z] .* [A-Z]"))) {
                val color0 = colorInfo[cellMap[cond.substringBefore(" ")]!!]
                val color1 = colorInfo[cellMap[cond.substringAfterLast(" ")]!!]
                    
                if ((cond.contains("==") && color0 != color1) || (cond.contains("!=") && color0 == color1)) {
                    isMatched = false
                    break
                }
            }
                
            // A == a,b,c : 칸 A는 a, b, c 중 한 가지 색으로 칠해야 한다.
            // A != a,b,c : 칸 A는 a, b, c가 아닌 다른 한 가지 색으로 칠해야 한다.
            else if (cond.matches(Regex("[A-Z] .* ([a-z],)*[a-z]"))) {
                val color = colorInfo[cellMap[cond.substringBefore(" ")]!!]
                val colors = cond.substringAfterLast(" ").split(",")
                    
                if ((cond.contains("==") && !(color in colors)) || (cond.contains("!=") && color in colors)) {
                    isMatched = false
                    break
                }
            }
        }
        
        if (isMatched) {
            possibleCount++
            
            val markList = listOf(500, 1000, 2000, 3000)
            if (possibleCount <= 100 || possibleCount in markList) {
                println("case No. ${possibleCount} : ${colorInfo}")
            }
        }
    }
    println("전체 경우의 수 : ${possibleCount}\n")
}

fun main() {
    
    // 임시 데이터 클래스
    val tempInfo = listOf<Info> (
        Info(listOf("A == B", "A == a,b,c")),
        Info(listOf("A != B", "C != D", "E == F")),
        Info(listOf("A == B", "B == C", "C == d,e,g", "D != E", "F != e,f,g,h")),
        Info(listOf("A != B", "B != C", "A != C", "A == a,b,c", "B == a,b,c", "C == a,b,c", "D == d", "E == e")),
    )
        
    tempInfo.forEach {
        println("info : ${it}")
        printCases(it)
    }
}
```

### 실행 결과
```kotlin
info : Info(constraints=[A == B, A == a,b,c])
case No. 1 : [a, a, a, a, a, a]
case No. 2 : [a, a, a, a, a, b]
case No. 3 : [a, a, a, a, a, c]
case No. 4 : [a, a, a, a, a, d]
case No. 5 : [a, a, a, a, a, e]
case No. 6 : [a, a, a, a, a, f]
case No. 7 : [a, a, a, a, a, g]
case No. 8 : [a, a, a, a, a, h]
case No. 9 : [a, a, a, a, b, a]
case No. 10 : [a, a, a, a, b, b]
case No. 11 : [a, a, a, a, b, c]
case No. 12 : [a, a, a, a, b, d]
case No. 13 : [a, a, a, a, b, e]
case No. 14 : [a, a, a, a, b, f]
case No. 15 : [a, a, a, a, b, g]
case No. 16 : [a, a, a, a, b, h]
case No. 17 : [a, a, a, a, c, a]
case No. 18 : [a, a, a, a, c, b]
case No. 19 : [a, a, a, a, c, c]
case No. 20 : [a, a, a, a, c, d]
case No. 21 : [a, a, a, a, c, e]
case No. 22 : [a, a, a, a, c, f]
case No. 23 : [a, a, a, a, c, g]
case No. 24 : [a, a, a, a, c, h]
case No. 25 : [a, a, a, a, d, a]
case No. 26 : [a, a, a, a, d, b]
case No. 27 : [a, a, a, a, d, c]
case No. 28 : [a, a, a, a, d, d]
case No. 29 : [a, a, a, a, d, e]
case No. 30 : [a, a, a, a, d, f]
case No. 31 : [a, a, a, a, d, g]
case No. 32 : [a, a, a, a, d, h]
case No. 33 : [a, a, a, a, e, a]
case No. 34 : [a, a, a, a, e, b]
case No. 35 : [a, a, a, a, e, c]
case No. 36 : [a, a, a, a, e, d]
case No. 37 : [a, a, a, a, e, e]
case No. 38 : [a, a, a, a, e, f]
case No. 39 : [a, a, a, a, e, g]
case No. 40 : [a, a, a, a, e, h]
case No. 41 : [a, a, a, a, f, a]
case No. 42 : [a, a, a, a, f, b]
case No. 43 : [a, a, a, a, f, c]
case No. 44 : [a, a, a, a, f, d]
case No. 45 : [a, a, a, a, f, e]
case No. 46 : [a, a, a, a, f, f]
case No. 47 : [a, a, a, a, f, g]
case No. 48 : [a, a, a, a, f, h]
case No. 49 : [a, a, a, a, g, a]
case No. 50 : [a, a, a, a, g, b]
case No. 51 : [a, a, a, a, g, c]
case No. 52 : [a, a, a, a, g, d]
case No. 53 : [a, a, a, a, g, e]
case No. 54 : [a, a, a, a, g, f]
case No. 55 : [a, a, a, a, g, g]
case No. 56 : [a, a, a, a, g, h]
case No. 57 : [a, a, a, a, h, a]
case No. 58 : [a, a, a, a, h, b]
case No. 59 : [a, a, a, a, h, c]
case No. 60 : [a, a, a, a, h, d]
case No. 61 : [a, a, a, a, h, e]
case No. 62 : [a, a, a, a, h, f]
case No. 63 : [a, a, a, a, h, g]
case No. 64 : [a, a, a, a, h, h]
case No. 65 : [a, a, a, b, a, a]
case No. 66 : [a, a, a, b, a, b]
case No. 67 : [a, a, a, b, a, c]
case No. 68 : [a, a, a, b, a, d]
case No. 69 : [a, a, a, b, a, e]
case No. 70 : [a, a, a, b, a, f]
case No. 71 : [a, a, a, b, a, g]
case No. 72 : [a, a, a, b, a, h]
case No. 73 : [a, a, a, b, b, a]
case No. 74 : [a, a, a, b, b, b]
case No. 75 : [a, a, a, b, b, c]
case No. 76 : [a, a, a, b, b, d]
case No. 77 : [a, a, a, b, b, e]
case No. 78 : [a, a, a, b, b, f]
case No. 79 : [a, a, a, b, b, g]
case No. 80 : [a, a, a, b, b, h]
case No. 81 : [a, a, a, b, c, a]
case No. 82 : [a, a, a, b, c, b]
case No. 83 : [a, a, a, b, c, c]
case No. 84 : [a, a, a, b, c, d]
case No. 85 : [a, a, a, b, c, e]
case No. 86 : [a, a, a, b, c, f]
case No. 87 : [a, a, a, b, c, g]
case No. 88 : [a, a, a, b, c, h]
case No. 89 : [a, a, a, b, d, a]
case No. 90 : [a, a, a, b, d, b]
case No. 91 : [a, a, a, b, d, c]
case No. 92 : [a, a, a, b, d, d]
case No. 93 : [a, a, a, b, d, e]
case No. 94 : [a, a, a, b, d, f]
case No. 95 : [a, a, a, b, d, g]
case No. 96 : [a, a, a, b, d, h]
case No. 97 : [a, a, a, b, e, a]
case No. 98 : [a, a, a, b, e, b]
case No. 99 : [a, a, a, b, e, c]
case No. 100 : [a, a, a, b, e, d]
case No. 500 : [a, a, a, h, g, d]
case No. 1000 : [a, a, b, h, e, h]
case No. 2000 : [a, a, d, h, b, h]
case No. 3000 : [a, a, f, g, g, h]
전체 경우의 수 : 12288

info : Info(constraints=[A != B, C != D, E == F])
case No. 1 : [a, b, a, b, a, a]
case No. 2 : [a, b, a, b, b, b]
case No. 3 : [a, b, a, b, c, c]
case No. 4 : [a, b, a, b, d, d]
case No. 5 : [a, b, a, b, e, e]
case No. 6 : [a, b, a, b, f, f]
case No. 7 : [a, b, a, b, g, g]
case No. 8 : [a, b, a, b, h, h]
case No. 9 : [a, b, a, c, a, a]
case No. 10 : [a, b, a, c, b, b]
case No. 11 : [a, b, a, c, c, c]
case No. 12 : [a, b, a, c, d, d]
case No. 13 : [a, b, a, c, e, e]
case No. 14 : [a, b, a, c, f, f]
case No. 15 : [a, b, a, c, g, g]
case No. 16 : [a, b, a, c, h, h]
case No. 17 : [a, b, a, d, a, a]
case No. 18 : [a, b, a, d, b, b]
case No. 19 : [a, b, a, d, c, c]
case No. 20 : [a, b, a, d, d, d]
case No. 21 : [a, b, a, d, e, e]
case No. 22 : [a, b, a, d, f, f]
case No. 23 : [a, b, a, d, g, g]
case No. 24 : [a, b, a, d, h, h]
case No. 25 : [a, b, a, e, a, a]
case No. 26 : [a, b, a, e, b, b]
case No. 27 : [a, b, a, e, c, c]
case No. 28 : [a, b, a, e, d, d]
case No. 29 : [a, b, a, e, e, e]
case No. 30 : [a, b, a, e, f, f]
case No. 31 : [a, b, a, e, g, g]
case No. 32 : [a, b, a, e, h, h]
case No. 33 : [a, b, a, f, a, a]
case No. 34 : [a, b, a, f, b, b]
case No. 35 : [a, b, a, f, c, c]
case No. 36 : [a, b, a, f, d, d]
case No. 37 : [a, b, a, f, e, e]
case No. 38 : [a, b, a, f, f, f]
case No. 39 : [a, b, a, f, g, g]
case No. 40 : [a, b, a, f, h, h]
case No. 41 : [a, b, a, g, a, a]
case No. 42 : [a, b, a, g, b, b]
case No. 43 : [a, b, a, g, c, c]
case No. 44 : [a, b, a, g, d, d]
case No. 45 : [a, b, a, g, e, e]
case No. 46 : [a, b, a, g, f, f]
case No. 47 : [a, b, a, g, g, g]
case No. 48 : [a, b, a, g, h, h]
case No. 49 : [a, b, a, h, a, a]
case No. 50 : [a, b, a, h, b, b]
case No. 51 : [a, b, a, h, c, c]
case No. 52 : [a, b, a, h, d, d]
case No. 53 : [a, b, a, h, e, e]
case No. 54 : [a, b, a, h, f, f]
case No. 55 : [a, b, a, h, g, g]
case No. 56 : [a, b, a, h, h, h]
case No. 57 : [a, b, b, a, a, a]
case No. 58 : [a, b, b, a, b, b]
case No. 59 : [a, b, b, a, c, c]
case No. 60 : [a, b, b, a, d, d]
case No. 61 : [a, b, b, a, e, e]
case No. 62 : [a, b, b, a, f, f]
case No. 63 : [a, b, b, a, g, g]
case No. 64 : [a, b, b, a, h, h]
case No. 65 : [a, b, b, c, a, a]
case No. 66 : [a, b, b, c, b, b]
case No. 67 : [a, b, b, c, c, c]
case No. 68 : [a, b, b, c, d, d]
case No. 69 : [a, b, b, c, e, e]
case No. 70 : [a, b, b, c, f, f]
case No. 71 : [a, b, b, c, g, g]
case No. 72 : [a, b, b, c, h, h]
case No. 73 : [a, b, b, d, a, a]
case No. 74 : [a, b, b, d, b, b]
case No. 75 : [a, b, b, d, c, c]
case No. 76 : [a, b, b, d, d, d]
case No. 77 : [a, b, b, d, e, e]
case No. 78 : [a, b, b, d, f, f]
case No. 79 : [a, b, b, d, g, g]
case No. 80 : [a, b, b, d, h, h]
case No. 81 : [a, b, b, e, a, a]
case No. 82 : [a, b, b, e, b, b]
case No. 83 : [a, b, b, e, c, c]
case No. 84 : [a, b, b, e, d, d]
case No. 85 : [a, b, b, e, e, e]
case No. 86 : [a, b, b, e, f, f]
case No. 87 : [a, b, b, e, g, g]
case No. 88 : [a, b, b, e, h, h]
case No. 89 : [a, b, b, f, a, a]
case No. 90 : [a, b, b, f, b, b]
case No. 91 : [a, b, b, f, c, c]
case No. 92 : [a, b, b, f, d, d]
case No. 93 : [a, b, b, f, e, e]
case No. 94 : [a, b, b, f, f, f]
case No. 95 : [a, b, b, f, g, g]
case No. 96 : [a, b, b, f, h, h]
case No. 97 : [a, b, b, g, a, a]
case No. 98 : [a, b, b, g, b, b]
case No. 99 : [a, b, b, g, c, c]
case No. 100 : [a, b, b, g, d, d]
case No. 500 : [a, c, a, h, d, d]
case No. 1000 : [a, d, b, g, h, h]
case No. 2000 : [a, f, d, f, h, h]
case No. 3000 : [a, h, f, d, h, h]
전체 경우의 수 : 25088

info : Info(constraints=[A == B, B == C, C == d,e,g, D != E, F != e,f,g,h])
case No. 1 : [d, d, d, a, b, a]
case No. 2 : [d, d, d, a, b, b]
case No. 3 : [d, d, d, a, b, c]
case No. 4 : [d, d, d, a, b, d]
case No. 5 : [d, d, d, a, c, a]
case No. 6 : [d, d, d, a, c, b]
case No. 7 : [d, d, d, a, c, c]
case No. 8 : [d, d, d, a, c, d]
case No. 9 : [d, d, d, a, d, a]
case No. 10 : [d, d, d, a, d, b]
case No. 11 : [d, d, d, a, d, c]
case No. 12 : [d, d, d, a, d, d]
case No. 13 : [d, d, d, a, e, a]
case No. 14 : [d, d, d, a, e, b]
case No. 15 : [d, d, d, a, e, c]
case No. 16 : [d, d, d, a, e, d]
case No. 17 : [d, d, d, a, f, a]
case No. 18 : [d, d, d, a, f, b]
case No. 19 : [d, d, d, a, f, c]
case No. 20 : [d, d, d, a, f, d]
case No. 21 : [d, d, d, a, g, a]
case No. 22 : [d, d, d, a, g, b]
case No. 23 : [d, d, d, a, g, c]
case No. 24 : [d, d, d, a, g, d]
case No. 25 : [d, d, d, a, h, a]
case No. 26 : [d, d, d, a, h, b]
case No. 27 : [d, d, d, a, h, c]
case No. 28 : [d, d, d, a, h, d]
case No. 29 : [d, d, d, b, a, a]
case No. 30 : [d, d, d, b, a, b]
case No. 31 : [d, d, d, b, a, c]
case No. 32 : [d, d, d, b, a, d]
case No. 33 : [d, d, d, b, c, a]
case No. 34 : [d, d, d, b, c, b]
case No. 35 : [d, d, d, b, c, c]
case No. 36 : [d, d, d, b, c, d]
case No. 37 : [d, d, d, b, d, a]
case No. 38 : [d, d, d, b, d, b]
case No. 39 : [d, d, d, b, d, c]
case No. 40 : [d, d, d, b, d, d]
case No. 41 : [d, d, d, b, e, a]
case No. 42 : [d, d, d, b, e, b]
case No. 43 : [d, d, d, b, e, c]
case No. 44 : [d, d, d, b, e, d]
case No. 45 : [d, d, d, b, f, a]
case No. 46 : [d, d, d, b, f, b]
case No. 47 : [d, d, d, b, f, c]
case No. 48 : [d, d, d, b, f, d]
case No. 49 : [d, d, d, b, g, a]
case No. 50 : [d, d, d, b, g, b]
case No. 51 : [d, d, d, b, g, c]
case No. 52 : [d, d, d, b, g, d]
case No. 53 : [d, d, d, b, h, a]
case No. 54 : [d, d, d, b, h, b]
case No. 55 : [d, d, d, b, h, c]
case No. 56 : [d, d, d, b, h, d]
case No. 57 : [d, d, d, c, a, a]
case No. 58 : [d, d, d, c, a, b]
case No. 59 : [d, d, d, c, a, c]
case No. 60 : [d, d, d, c, a, d]
case No. 61 : [d, d, d, c, b, a]
case No. 62 : [d, d, d, c, b, b]
case No. 63 : [d, d, d, c, b, c]
case No. 64 : [d, d, d, c, b, d]
case No. 65 : [d, d, d, c, d, a]
case No. 66 : [d, d, d, c, d, b]
case No. 67 : [d, d, d, c, d, c]
case No. 68 : [d, d, d, c, d, d]
case No. 69 : [d, d, d, c, e, a]
case No. 70 : [d, d, d, c, e, b]
case No. 71 : [d, d, d, c, e, c]
case No. 72 : [d, d, d, c, e, d]
case No. 73 : [d, d, d, c, f, a]
case No. 74 : [d, d, d, c, f, b]
case No. 75 : [d, d, d, c, f, c]
case No. 76 : [d, d, d, c, f, d]
case No. 77 : [d, d, d, c, g, a]
case No. 78 : [d, d, d, c, g, b]
case No. 79 : [d, d, d, c, g, c]
case No. 80 : [d, d, d, c, g, d]
case No. 81 : [d, d, d, c, h, a]
case No. 82 : [d, d, d, c, h, b]
case No. 83 : [d, d, d, c, h, c]
case No. 84 : [d, d, d, c, h, d]
case No. 85 : [d, d, d, d, a, a]
case No. 86 : [d, d, d, d, a, b]
case No. 87 : [d, d, d, d, a, c]
case No. 88 : [d, d, d, d, a, d]
case No. 89 : [d, d, d, d, b, a]
case No. 90 : [d, d, d, d, b, b]
case No. 91 : [d, d, d, d, b, c]
case No. 92 : [d, d, d, d, b, d]
case No. 93 : [d, d, d, d, c, a]
case No. 94 : [d, d, d, d, c, b]
case No. 95 : [d, d, d, d, c, c]
case No. 96 : [d, d, d, d, c, d]
case No. 97 : [d, d, d, d, e, a]
case No. 98 : [d, d, d, d, e, b]
case No. 99 : [d, d, d, d, e, c]
case No. 100 : [d, d, d, d, e, d]
case No. 500 : [g, g, g, b, g, d]
전체 경우의 수 : 672

info : Info(constraints=[A != B, B != C, A != C, A == a,b,c, B == a,b,c, C == a,b,c, D == d, E == e])
case No. 1 : [a, b, c, d, e, a]
case No. 2 : [a, b, c, d, e, b]
case No. 3 : [a, b, c, d, e, c]
case No. 4 : [a, b, c, d, e, d]
case No. 5 : [a, b, c, d, e, e]
case No. 6 : [a, b, c, d, e, f]
case No. 7 : [a, b, c, d, e, g]
case No. 8 : [a, b, c, d, e, h]
case No. 9 : [a, c, b, d, e, a]
case No. 10 : [a, c, b, d, e, b]
case No. 11 : [a, c, b, d, e, c]
case No. 12 : [a, c, b, d, e, d]
case No. 13 : [a, c, b, d, e, e]
case No. 14 : [a, c, b, d, e, f]
case No. 15 : [a, c, b, d, e, g]
case No. 16 : [a, c, b, d, e, h]
case No. 17 : [b, a, c, d, e, a]
case No. 18 : [b, a, c, d, e, b]
case No. 19 : [b, a, c, d, e, c]
case No. 20 : [b, a, c, d, e, d]
case No. 21 : [b, a, c, d, e, e]
case No. 22 : [b, a, c, d, e, f]
case No. 23 : [b, a, c, d, e, g]
case No. 24 : [b, a, c, d, e, h]
case No. 25 : [b, c, a, d, e, a]
case No. 26 : [b, c, a, d, e, b]
case No. 27 : [b, c, a, d, e, c]
case No. 28 : [b, c, a, d, e, d]
case No. 29 : [b, c, a, d, e, e]
case No. 30 : [b, c, a, d, e, f]
case No. 31 : [b, c, a, d, e, g]
case No. 32 : [b, c, a, d, e, h]
case No. 33 : [c, a, b, d, e, a]
case No. 34 : [c, a, b, d, e, b]
case No. 35 : [c, a, b, d, e, c]
case No. 36 : [c, a, b, d, e, d]
case No. 37 : [c, a, b, d, e, e]
case No. 38 : [c, a, b, d, e, f]
case No. 39 : [c, a, b, d, e, g]
case No. 40 : [c, a, b, d, e, h]
case No. 41 : [c, b, a, d, e, a]
case No. 42 : [c, b, a, d, e, b]
case No. 43 : [c, b, a, d, e, c]
case No. 44 : [c, b, a, d, e, d]
case No. 45 : [c, b, a, d, e, e]
case No. 46 : [c, b, a, d, e, f]
case No. 47 : [c, b, a, d, e, g]
case No. 48 : [c, b, a, d, e, h]
전체 경우의 수 : 48
```
