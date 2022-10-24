## 요구사항 분석
다음과 같은 형태의 문장들을 이용하여 각 글자가 나타내는 좌표를 모두 출력하시오. (단, 좌표 값은 모두 정수이다.)
```kotlin
B는 원점에 있다.
A는 B로부터 위쪽 3칸 지점에 있다.
C는 D로부터 아래쪽 3칸 지점에 있다.
D는 E로부터 위쪽, 오른쪽 각각 3칸 지점에 있다.
G는 B로부터 왼쪽 2칸, 아래쪽 4칸 지점에 있다.
G는 C로부터 벡터 <1, 2> 방향으로 6.708만큼 떨어져 있다. // = G는 C로부터 위쪽 6칸, 오른쪽 3칸 지점에 있다.
H는 B와 G 사이의 정중앙에 있다. 
```

이때 각 점의 좌표는 다음과 같이 출력한다.
```kotlin
A(0, 3) B(0, 0), C(-5, -10), D(-5, -7), E(-8, -10), G(-2, -4), H(-1, -2)
```

단, 위쪽부터 읽어 나갈 때 특정 문장에서는 그 아래쪽에 있는 문장을 이용해야 할 수 있다.

## 코드 및 실행 결과
### 코드 1
```kotlin
import kotlin.math.*

// 좌표 출력하기
fun printPoints(sentences: List<String>) {
    val pointMap = mutableMapOf<Char, List<Int>?>()
    
    // 모든 점을 null로 초기화 (valueCount, nullCount 용도)
    sentences.forEach { s ->
        s.forEach {
            if (it >= 'A' && it <= 'Z') {
                pointMap.put(it, null)
            }
        }
    }
    
    // 원점에 있는 점을 모두 찾아서 (0, 0)으로 초기화
    sentences.forEach { s ->
        if (s.matches(Regex("[A-Z]는 원점에 있다."))) {
            pointMap.put(s[0], listOf(0, 0))
        }
    }
    
    // 문장을 모두 찾아서 정보 출력
    var count = 0
    
    while (true) {
        println(" **** ROUND ${count} ****")
        println("1. current point map = ${pointMap}\n")
        
        sentences.forEach { s ->
            
            // 각 문장의 알파벳 대문자가 (값이 있는 것 1개 이상) + (null인 것 1개)인 경우 값 채워 나가기
            val alpha = s.filter { it >= 'A' && it <= 'Z' }.toSet()
            var valueCount = 0
            var nullCount = 0
            
            alpha.forEach {
                if (pointMap.get(it) == null) { // ~.get(it)!! 은 null이면 중지하므로 주의 !!
                    nullCount++
                } else {
                    valueCount++
                }
            }
            
            // pointMap에 null이 없으면 출력하고 종료 (정렬을 위해 toList()를 사용)
            val pointMapNullCount = pointMap.count { it.value == null }
            
            if (pointMapNullCount == 0) {
                println("**** FINAL ****")
                
                val result = pointMap.toList().sortedBy { it.first }.map {
                    "${it.first}(${it.second!![0]}, ${it.second!![1]})"
                }.joinToString(" ")
                println(result)
                return
            }
            
            // 그렇지 않으면 정보 채우기
            if (valueCount >= 1 && nullCount == 1) {
                println("2. 문장 = ${s}, 알파벳 = ${alpha}")
                
                // '정중앙에 있다'가 아닌 경우
                if (s.matches(Regex("[A-Z]는 [A-Z]로부터 .*"))) {
                    
                    // toPoint, fromPoint는 1글자의 string이므로, char 자료형으로 바꾸기 위하여 [0]을 추가
                    val toPoint = s.substringBefore("는")[0]
                    val fromPoint = s.substringBefore("로부터").substringAfter("는 ")[0]
                    
                    val sr = s.substring(8)
                    var x = 0
                    var y = 0
                    
                    // 여러 정규 표현식과 동시 일치 방지
                    val isType1 = sr.matches(Regex(".*쪽, .*쪽 각각 .*칸 지점에 있다."))
                    val isType2 = sr.matches(Regex(".*쪽 .*칸, .*쪽 .*칸 지점에 있다."))
                    
                    // A는 B로부터 위쪽 3칸 지점에 있다.
                    if (sr.matches(Regex(".*쪽 .*칸 지점에 있다.")) && !isType1 && !isType2) {
                        val direction = sr.substringBefore("쪽")
                        val value = sr.substringBefore("칸").substringAfter("쪽 ").toInt()
                        
                        when (direction) {
                            "위" -> y = value
                            "아래" -> y = -value
                            "왼" -> x = -value
                            "오른" -> x = value
                        }
                    }
                    
                    // D는 E로부터 위쪽, 오른쪽 각각 3칸 지점에 있다.
                    else if (isType1) {
                        val direction0 = sr.substringBefore("쪽, ")
                        val direction1 = sr.substringBefore("쪽 ").substringAfter("쪽, ")
                        val value = sr.substringBefore("칸").substringAfter("각각 ").toInt()
                        
                        listOf(direction0, direction1).forEach {
                            when (it) {
                                "위" -> y += value
                                "아래" -> y -= value
                                "왼" -> x -= value
                                "오른" -> x += value
                            }
                        }
                    }
                    
                    // G는 B로부터 왼쪽 2칸, 아래쪽 4칸 지점에 있다.
                    else if (isType2) {
                        val direction0 = sr.substringBefore("쪽 ")
                        val direction1 = sr.substringAfter("칸, ").substringBefore("쪽 ")
                        
                        val value0 = sr.substringBefore("칸, ").substringAfter("쪽 ").toInt()
                        val value1 = sr.substringBefore("칸 ").substringAfterLast("쪽 ").toInt()
                        
                        val directions = listOf(direction0, direction1)
                        val values = listOf(value0, value1)
                        
                        (0..1).forEach {
                            when (directions[it]) {
                                "위" -> y += values[it]
                                "아래" -> y -= values[it]
                                "왼" -> x -= values[it]
                                "오른" -> x += values[it]
                            }
                        }
                    }
                    
                    // G는 C로부터 벡터 <1, 2> 방향으로 6.708만큼 떨어져 있다.
                    else if (sr.matches(Regex("벡터 .* 방향으로 .*만큼 떨어져 있다."))) {
                        val xy = sr.substringAfter("<").substringBefore(">")
                        val xDist = xy.split(", ")[0].toInt()
                        val yDist = xy.split(", ")[1].toInt()
                        
                        val dist = sr.substringBefore("만큼").substringAfter("으로 ").toDouble()
                        val multiple = dist / sqrt((xDist * xDist + yDist * yDist).toDouble())
                        
                        println("3. x dist = ${xDist}, y dist = ${yDist}, multiple = ${multiple}")
                        
                        x = (xDist * multiple).roundToInt()
                        y = (yDist * multiple).roundToInt()
                    }
                    
                    println("4. to = ${toPoint}, from = ${fromPoint}, x = ${x}, y = ${y}")
                    
                    // 어떤 점의 정보가 null인지 파악하여 적절히 처리
                    if (pointMap.get(fromPoint) == null) {
                        val toPointInfo = pointMap.get(toPoint)!!
                        val fromPointX = toPointInfo[0] - x
                        val fromPointY = toPointInfo[1] - y
                        
                        pointMap.put(fromPoint, listOf(fromPointX, fromPointY))
                    }
                    
                    // toPoint가 null
                    else {
                        val fromPointInfo = pointMap.get(fromPoint)!!
                        val toPointX = fromPointInfo[0] + x
                        val toPointY = fromPointInfo[1] + y
                        
                        pointMap.put(toPoint, listOf(toPointX, toPointY))
                    }
                }
                
                // '정중앙에 있다'인 경우
                else {
                    val middlePoint = s.substringBefore("는 ")[0]
                    val firstPoint = s.substringBefore("와 ").substringAfter("는 ")[0]
                    val lastPoint = s.substringBefore(" 사이의").substringAfter("와 ")[0]
                    println("5. middle = ${middlePoint}, first = ${firstPoint}, last = ${lastPoint}")
                    
                    val middlePointInfo = pointMap.get(middlePoint)
                    val firstPointInfo = pointMap.get(firstPoint)
                    val lastPointInfo = pointMap.get(lastPoint)
                    println("6. middle = ${middlePointInfo}, first = ${firstPointInfo}, last = ${lastPointInfo}")
                    
                    // 어떤 점의 정보가 null인지에 따라 다르게 처리
                    if (firstPointInfo == null) {
                        pointMap.put(firstPoint, (0..1).map {
                            2 * middlePointInfo!![it] - lastPointInfo!![it]
                        })
                    } else if (middlePointInfo == null) {
                        pointMap.put(middlePoint, (0..1).map {
                            (firstPointInfo!![it] + lastPointInfo!![it]) / 2
                        })
                    } else if (lastPointInfo == null) {
                        pointMap.put(lastPoint, (0..1).map {
                            2 * middlePointInfo!![it] - firstPointInfo!![it]
                        })
                    }
                }
                
                println("7. updated point map = ${pointMap}\n")
            }
        }
        count++
    }
}

fun main() {
    val sentences = listOf(
        "B는 원점에 있다.",
        "A는 B로부터 위쪽 3칸 지점에 있다.",
        "C는 D로부터 아래쪽 3칸 지점에 있다.",
        "D는 E로부터 위쪽, 오른쪽 각각 3칸 지점에 있다.",
        "G는 B로부터 왼쪽 2칸, 아래쪽 4칸 지점에 있다.",
        "O는 B로부터 벡터 <4, -1> 방향으로 8.246만큼 떨어져 있다.", // = O는 B로부터 아래쪽 2칸, 오른쪽 8칸 지점에 있다.
        "G는 C로부터 벡터 <1, 2> 방향으로 6.708만큼 떨어져 있다.", // = G는 C로부터 위쪽 6칸, 오른쪽 3칸 지점에 있다.
        "H는 B와 G 사이의 정중앙에 있다.",
        "I는 J와 E 사이의 정중앙에 있다.",
        "K는 G와 L 사이의 정중앙에 있다.",
        "B는 I로부터 왼쪽 4칸, 아래쪽 6칸 지점에 있다.",
        "K는 B로부터 오른쪽 8칸, 위쪽 4칸 지점에 있다.",
    )
    
    // 점들의 좌표 출력하기
    printPoints(sentences)
}
```

### 실행 결과 1
```kotlin
 **** ROUND 0 ****
1. current point map = {B=[0, 0], A=null, C=null, D=null, E=null, G=null, O=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = A는 B로부터 위쪽 3칸 지점에 있다., 알파벳 = [A, B]
4. to = A, from = B, x = 0, y = 3
7. updated point map = {B=[0, 0], A=[0, 3], C=null, D=null, E=null, G=null, O=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = G는 B로부터 왼쪽 2칸, 아래쪽 4칸 지점에 있다., 알파벳 = [G, B]
4. to = G, from = B, x = -2, y = -4
7. updated point map = {B=[0, 0], A=[0, 3], C=null, D=null, E=null, G=[-2, -4], O=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = O는 B로부터 벡터 <4, -1> 방향으로 8.246만큼 떨어져 있다., 알파벳 = [O, B]
3. x dist = 4, y dist = -1, multiple = 1.9999487640496019
4. to = O, from = B, x = 8, y = -2
7. updated point map = {B=[0, 0], A=[0, 3], C=null, D=null, E=null, G=[-2, -4], O=[8, -2], H=null, I=null, J=null, K=null, L=null}

2. 문장 = G는 C로부터 벡터 <1, 2> 방향으로 6.708만큼 떨어져 있다., 알파벳 = [G, C]
3. x dist = 1, y dist = 2, multiple = 2.999908798613718
4. to = G, from = C, x = 3, y = 6
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=null, E=null, G=[-2, -4], O=[8, -2], H=null, I=null, J=null, K=null, L=null}

2. 문장 = H는 B와 G 사이의 정중앙에 있다., 알파벳 = [H, B, G]
5. middle = H, first = B, last = G
6. middle = null, first = [0, 0], last = [-2, -4]
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=null, E=null, G=[-2, -4], O=[8, -2], H=[-1, -2], I=null, J=null, K=null, L=null}

2. 문장 = B는 I로부터 왼쪽 4칸, 아래쪽 6칸 지점에 있다., 알파벳 = [B, I]
4. to = B, from = I, x = -4, y = -6
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=null, E=null, G=[-2, -4], O=[8, -2], H=[-1, -2], I=[4, 6], J=null, K=null, L=null}

2. 문장 = K는 B로부터 오른쪽 8칸, 위쪽 4칸 지점에 있다., 알파벳 = [K, B]
4. to = K, from = B, x = 8, y = 4
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=null, E=null, G=[-2, -4], O=[8, -2], H=[-1, -2], I=[4, 6], J=null, K=[8, 4], L=null}

 **** ROUND 1 ****
1. current point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=null, E=null, G=[-2, -4], O=[8, -2], H=[-1, -2], I=[4, 6], J=null, K=[8, 4], L=null}

2. 문장 = C는 D로부터 아래쪽 3칸 지점에 있다., 알파벳 = [C, D]
4. to = C, from = D, x = 0, y = -3
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=[-5, -7], E=null, G=[-2, -4], O=[8, -2], H=[-1, -2], I=[4, 6], J=null, K=[8, 4], L=null}

2. 문장 = D는 E로부터 위쪽, 오른쪽 각각 3칸 지점에 있다., 알파벳 = [D, E]
4. to = D, from = E, x = 3, y = 3
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=[-5, -7], E=[-8, -10], G=[-2, -4], O=[8, -2], H=[-1, -2], I=[4, 6], J=null, K=[8, 4], L=null}

2. 문장 = I는 J와 E 사이의 정중앙에 있다., 알파벳 = [I, J, E]
5. middle = I, first = J, last = E
6. middle = [4, 6], first = null, last = [-8, -10]
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=[-5, -7], E=[-8, -10], G=[-2, -4], O=[8, -2], H=[-1, -2], I=[4, 6], J=[16, 22], K=[8, 4], L=null}

2. 문장 = K는 G와 L 사이의 정중앙에 있다., 알파벳 = [K, G, L]
5. middle = K, first = G, last = L
6. middle = [8, 4], first = [-2, -4], last = null
7. updated point map = {B=[0, 0], A=[0, 3], C=[-5, -10], D=[-5, -7], E=[-8, -10], G=[-2, -4], O=[8, -2], H=[-1, -2], I=[4, 6], J=[16, 22], K=[8, 4], L=[18, 12]}

**** FINAL ****
A(0, 3) B(0, 0) C(-5, -10) D(-5, -7) E(-8, -10) G(-2, -4) H(-1, -2) I(4, 6) J(16, 22) K(8, 4) L(18, 12) O(8, -2)
```

### 코드 2
```kotlin
fun main() {
    val sentences = listOf(
        "A는 원점에 있다.",
        "B는 A로부터 오른쪽 16칸 지점에 있다.",
        "C는 B로부터 위쪽 16칸 지점에 있다.",
        "D는 C로부터 왼쪽 16칸 지점에 있다.",
        "E는 A와 B 사이의 정중앙에 있다.",
        "F는 B와 C 사이의 정중앙에 있다.",
        "G는 C와 D 사이의 정중앙에 있다.",
        "H는 D와 A 사이의 정중앙에 있다.",
        "I는 E와 F 사이의 정중앙에 있다.",
        "J는 F와 G 사이의 정중앙에 있다.",
        "K는 G와 H 사이의 정중앙에 있다.",
        "L는 H와 E 사이의 정중앙에 있다."
    )
    
    // 점들의 좌표 출력하기
    printPoints(sentences)
}
```

### 실행 결과 2
```kotlin
 **** ROUND 0 ****
1. current point map = {A=[0, 0], B=null, C=null, D=null, E=null, F=null, G=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = B는 A로부터 오른쪽 16칸 지점에 있다., 알파벳 = [B, A]
4. to = B, from = A, x = 16, y = 0
7. updated point map = {A=[0, 0], B=[16, 0], C=null, D=null, E=null, F=null, G=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = C는 B로부터 위쪽 16칸 지점에 있다., 알파벳 = [C, B]
4. to = C, from = B, x = 0, y = 16
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=null, E=null, F=null, G=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = D는 C로부터 왼쪽 16칸 지점에 있다., 알파벳 = [D, C]
4. to = D, from = C, x = -16, y = 0
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=null, F=null, G=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = E는 A와 B 사이의 정중앙에 있다., 알파벳 = [E, A, B]
5. middle = E, first = A, last = B
6. middle = null, first = [0, 0], last = [16, 0]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=null, G=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = F는 B와 C 사이의 정중앙에 있다., 알파벳 = [F, B, C]
5. middle = F, first = B, last = C
6. middle = null, first = [16, 0], last = [16, 16]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=null, H=null, I=null, J=null, K=null, L=null}

2. 문장 = G는 C와 D 사이의 정중앙에 있다., 알파벳 = [G, C, D]
5. middle = G, first = C, last = D
6. middle = null, first = [16, 16], last = [0, 16]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=[8, 16], H=null, I=null, J=null, K=null, L=null}

2. 문장 = H는 D와 A 사이의 정중앙에 있다., 알파벳 = [H, D, A]
5. middle = H, first = D, last = A
6. middle = null, first = [0, 16], last = [0, 0]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=[8, 16], H=[0, 8], I=null, J=null, K=null, L=null}

2. 문장 = I는 E와 F 사이의 정중앙에 있다., 알파벳 = [I, E, F]
5. middle = I, first = E, last = F
6. middle = null, first = [8, 0], last = [16, 8]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=[8, 16], H=[0, 8], I=[12, 4], J=null, K=null, L=null}

2. 문장 = J는 F와 G 사이의 정중앙에 있다., 알파벳 = [J, F, G]
5. middle = J, first = F, last = G
6. middle = null, first = [16, 8], last = [8, 16]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=[8, 16], H=[0, 8], I=[12, 4], J=[12, 12], K=null, L=null}

2. 문장 = K는 G와 H 사이의 정중앙에 있다., 알파벳 = [K, G, H]
5. middle = K, first = G, last = H
6. middle = null, first = [8, 16], last = [0, 8]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=[8, 16], H=[0, 8], I=[12, 4], J=[12, 12], K=[4, 12], L=null}

2. 문장 = L는 H와 E 사이의 정중앙에 있다., 알파벳 = [L, H, E]
5. middle = L, first = H, last = E
6. middle = null, first = [0, 8], last = [8, 0]
7. updated point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=[8, 16], H=[0, 8], I=[12, 4], J=[12, 12], K=[4, 12], L=[4, 4]}

 **** ROUND 1 ****
1. current point map = {A=[0, 0], B=[16, 0], C=[16, 16], D=[0, 16], E=[8, 0], F=[16, 8], G=[8, 16], H=[0, 8], I=[12, 4], J=[12, 12], K=[4, 12], L=[4, 4]}

**** FINAL ****
A(0, 0) B(16, 0) C(16, 16) D(0, 16) E(8, 0) F(16, 8) G(8, 16) H(0, 8) I(12, 4) J(12, 12) K(4, 12) L(4, 4)
```
