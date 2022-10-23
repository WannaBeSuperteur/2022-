## 요구사항 분석
1차원 좌표가 ```-a``` ~ ```+a```인 선분에서, 원점 ```0```에서 ```r0, r1, ..., r(n-1)```번 (```walks = [r0, r1, ..., r(n-1)]```) random walk를 한 결과, 각 지점의 도달 횟수의 상대값을 다음과 같이 출력하는 함수를 작성하시오.

예를 들어 ```a```의 값이 ```2```, 상대값의 최댓값 ```relativeMax```가 ```9```이고 ```10, 20, 30```번 random walk를 한 결과 각 지점의 도달 횟수가
```kotlin
10회 -> -2부터 +2까지 각각 1, 2, 4, 2, 1
20회 -> -2부터 +2까지 각각 2, 4, 7, 5, 1
30회 -> -2부터 +2까지 각각 2, 5, 7, 10, 6
```
일 때, 다음과 같이 각 상대값을 가장 가까운 정수로 내림하여 출력한다. 맨 마지막에는 상댓값이 가장 큰 좌표를 출력한다.

```kotlin
for each :
2 4 9 4 2 / 0
2 5 9 6 1 / 0
1 4 9 6 5 / 0

for entire :
0 1 3 1 0
1 3 6 4 0
1 4 6 9 5 / 1
(2, 1)
```

단, 초기 위치는 제외하고, 마지막 random walk 이후의 위치는 포함한다.

### 함수
```doRandomWalk(a, walks, relativeMax)```

## 코드 및 실행 결과
### 코드
```kotlin
import java.util.Random

// 랜덤워크 수행
fun doRandomWalk(a: Int, walks: List<Int>, relativeMax: Int) {
    val random = Random()
    
    // 각 지점의 도달 횟수
    val count = walks.map {
        val times = (0 .. 2 * a).map { 0 }.toMutableList()
        var currentPosition = a
        
        for (i in 0 until it) {
            
            // 양 끝에 있으면 가능한 방향이 1개이므로 그쪽으로만 이동 가능
            if (currentPosition in listOf(0, 2 * a)) {
                if (currentPosition == 0) {
                    currentPosition++
                } else {
                    currentPosition--
                }
                times[currentPosition]++
                continue
            }
            
            val decision = random.nextInt(2)
            
            // 왼쪽으로 이동
            if (decision == 0) {
                currentPosition--
            }
            
            // 오른쪽으로 이동
            else {
                currentPosition++
            }
            times[currentPosition]++
        }
        times
    }
    
    println("1. count :")
    count.forEach {
        println(it)
    }
    
    // for entire를 처리하기 위한 전체 최댓값
    val globalMaxValue = count.fold (0) { acc, e ->
        Math.max(acc, e.max())
    }
    println("\n2. global max value = ${globalMaxValue}\n")
    
    // for each (각 랜덤워크 횟수에 대한 상대값)
    println("for each :")
    count.forEach { cnt ->
        val maxValue = cnt.max()
        val indexOfMax = cnt.indexOfFirst { it == maxValue }
        
        val newCount = cnt.map { c ->
            c * relativeMax / maxValue
        }
        println("${newCount.joinToString(" ")} / ${indexOfMax - a}")
    }
    
    // for entire (전체 랜덤워크 횟수에 대한 상댓값)
    var globalMaxWalkTimesIndex = -1
    var maxPointOfGlobalMaxWalkTimesIndex = -1
    
    println("\nfor entire :")
    count.forEachIndexed { idx, cnt ->
        val indexOfGlobalMax = cnt.indexOfFirst { it == globalMaxValue }
        
        val newCount = cnt.map { c ->
            c * relativeMax / globalMaxValue
        }
        println(newCount.joinToString(" ") + if (indexOfGlobalMax >= 0) {
            
            // 전역 최대 도달 횟수 인덱스 및 해당 좌표는 최초 인덱스에 대해서만 갱신
            if (globalMaxWalkTimesIndex == -1) {
                globalMaxWalkTimesIndex = idx
                maxPointOfGlobalMaxWalkTimesIndex = indexOfGlobalMax - a
            }
            " / ${indexOfGlobalMax - a}"
        } else {
            ""
        })
    }
    println("(${globalMaxWalkTimesIndex}, ${maxPointOfGlobalMaxWalkTimesIndex})")
}

fun main() {
    
    // 랜덤워크
    doRandomWalk(24, listOf(5, 10, 30, 50, 100, 1000, 1500, 2000, 2500), 9)
}
```

### 실행 결과
```kotlin
1. count :
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 3, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 2, 4, 3, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 2, 4, 6, 8, 7, 3, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 3, 5, 4, 3, 5, 7, 9, 7, 4, 2, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 4, 6, 10, 11, 8, 13, 16, 11, 6, 8, 6, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 3, 5, 9, 12, 13, 12, 16, 23, 28, 39, 54, 72, 86, 91, 88, 89, 84, 62, 52, 43, 29, 23, 23, 20, 10, 5, 3, 3, 2, 0]
[61, 106, 100, 123, 131, 131, 114, 88, 74, 73, 64, 40, 36, 40, 41, 37, 32, 24, 16, 13, 12, 13, 15, 19, 17, 16, 21, 19, 11, 6, 4, 2, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[2, 5, 13, 15, 25, 48, 58, 52, 42, 35, 28, 34, 49, 54, 42, 28, 27, 30, 25, 22, 18, 18, 30, 29, 34, 61, 74, 64, 70, 94, 97, 78, 75, 87, 95, 91, 69, 50, 49, 44, 29, 27, 31, 28, 16, 6, 2, 0, 0]
[20, 49, 51, 58, 65, 73, 95, 95, 85, 69, 63, 80, 87, 85, 70, 51, 58, 68, 69, 75, 85, 110, 118, 94, 81, 66, 55, 41, 29, 24, 16, 23, 32, 40, 38, 32, 32, 31, 25, 18, 18, 17, 14, 15, 14, 15, 20, 21, 10]

2. global max value = 131

for each :
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 3 9 3 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 / 1
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 4 9 6 2 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 / 2
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 2 4 6 9 7 3 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 / -1
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 3 5 4 3 5 7 9 7 4 2 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 / -2
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 2 3 5 6 4 7 9 6 3 4 3 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 / 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1 2 2 3 5 7 8 9 8 8 8 6 5 4 2 2 2 1 0 0 0 0 0 0 / 8
4 7 6 8 9 9 7 6 5 5 4 2 2 2 2 2 2 1 1 0 0 0 1 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 / -20
0 0 1 1 2 4 5 4 3 3 2 3 4 5 3 2 2 2 2 2 1 1 2 2 3 5 6 5 6 8 9 7 6 8 8 8 6 4 4 4 2 2 2 2 1 0 0 0 0 / 6
1 3 3 4 4 5 7 7 6 5 4 6 6 6 5 3 4 5 5 5 6 8 9 7 6 5 4 3 2 1 1 1 2 3 2 2 2 2 1 1 1 1 1 1 1 1 1 1 0 / -2

for entire :
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 2 3 4 5 6 6 6 5 4 3 2 1 1 1 1 0 0 0 0 0 0
4 7 6 8 9 9 7 6 5 5 4 2 2 2 2 2 2 1 1 0 0 0 1 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 / -20
0 0 0 1 1 3 3 3 2 2 1 2 3 3 2 1 1 2 1 1 1 1 2 1 2 4 5 4 4 6 6 5 5 5 6 6 4 3 3 3 1 1 2 1 1 0 0 0 0
1 3 3 3 4 5 6 6 5 4 4 5 5 5 4 3 3 4 4 5 5 7 8 6 5 4 3 2 1 1 1 1 2 2 2 2 2 2 1 1 1 1 0 1 0 1 1 1 0
(6, -20)
```
