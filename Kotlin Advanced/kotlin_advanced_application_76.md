## 요구사항 분석
다음과 같은 형식의 문장들의 내용대로 실행하면서 그래프를 그려 나갈 때, 다음을 각각 구하는 함수 ```printGraphArea(sentences)```를 작성하시오.
* 그래프의 y 값이 양의 값일 때의 그래프와 x축 사이의 전체 넓이
* 그래프의 y 값이 음의 값일 때의 그래프와 x축 사이의 전체 넓이
* 그래프가 x축과 만나는 전체 횟수 (원점 포함)

문장은 다음과 같은 형식이며, 위쪽부터 순서대로 실행해 나간다. 단, ```x축의 양의 방향으로 1 이동할 때```는 고정이다.
```kotlin
x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 n 이동하는 것을 m회 반복한다.
```

## 코드 및 실행 결과
### 코드
```kotlin
// 그래프의 넓이 및 기타 정보 출력
fun printGraphArea(sentences: List<String>) {
    var currentY = 0
    var currentPositiveArea = 0.0
    var currentNegativeArea = 0.0
    var totalCross = 1
    
    sentences.forEach {
        val movement = it.substringAfter("y축의 양의 방향으로 ").substringBefore(" 이동하는").toInt()
        val times = it.substringAfter("것을 ").substringBefore("회 반복").toInt()
        println("1. movement = ${movement}, times = ${times}")
        
        // 그래프와 x축 사이의 넓이 계산 (4가지 경우로 나누기)
        val newY = currentY + movement * times
        
        // 원점을 지나지 않는 경우
        if (currentY >= 0 && newY >= 0) {
            currentPositiveArea += times * (newY + currentY) / 2.0 
        }
        else if (currentY <= 0 && newY <= 0) {
            currentNegativeArea -= times * (newY + currentY) / 2.0 
        }
        
        // 원점을 지나는 경우
        else {
            val crossAt = -currentY / movement.toDouble()
            
            if (currentY >= 0 && newY <= 0) {
                currentPositiveArea += crossAt * currentY / 2.0
                currentNegativeArea -= (times - crossAt) * newY / 2.0
            } else {
                currentPositiveArea += (times - crossAt) * newY / 2.0
                currentNegativeArea -= crossAt * currentY / 2.0
            }
        }
        
        // 현재 y 값 갱신 및 직전 y 값과 비교하여 x축과 만나는 횟수 증가
        val oldY = currentY
        currentY += movement * times
        
        if (oldY < 0 && currentY >= 0 || oldY > 0 && currentY <= 0) {
            totalCross++
        }
        println("2. positive area = ${currentPositiveArea}, negative area = ${currentNegativeArea}")
        println("3. current Y = ${currentY}, total cross = ${totalCross}\n")
    }
    
    // 최종 값 출력하기
    println("답: 1. ${currentPositiveArea}, 2. ${currentNegativeArea}, 3. ${totalCross}")
}

fun main() {
    
    // 문장 목록
    val sentences = listOf(
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 2 이동하는 것을 3회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -3 이동하는 것을 2회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 4 이동하는 것을 4회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -3 이동하는 것을 5회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 2 이동하는 것을 4회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -5 이동하는 것을 2회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 1 이동하는 것을 1회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -2 이동하는 것을 2회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 1 이동하는 것을 5회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -3 이동하는 것을 1회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 2 이동하는 것을 1회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 3 이동하는 것을 4회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -2 이동하는 것을 6회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 1 이동하는 것을 2회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 2 이동하는 것을 3회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -12 이동하는 것을 1회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 2 이동하는 것을 13회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -8 이동하는 것을 3회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 3 이동하는 것을 2회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -1 이동하는 것을 2회 반복한다.",
        "x축의 양의 방향으로 1 이동할 때, y축의 양의 방향으로 -2 이동하는 것을 1회 반복한다."
    )
    
    // 문장대로 실행하면서 그래프 그리기
    printGraphArea(sentences)
}
```

### 실행 결과
```kotlin
1. movement = 2, times = 3
2. positive area = 9.0, negative area = 0.0
3. current Y = 6, total cross = 1

1. movement = -3, times = 2
2. positive area = 15.0, negative area = 0.0
3. current Y = 0, total cross = 2

1. movement = 4, times = 4
2. positive area = 47.0, negative area = 0.0
3. current Y = 16, total cross = 2

1. movement = -3, times = 5
2. positive area = 89.5, negative area = 0.0
3. current Y = 1, total cross = 2

1. movement = 2, times = 4
2. positive area = 109.5, negative area = 0.0
3. current Y = 9, total cross = 2

1. movement = -5, times = 2
2. positive area = 117.6, negative area = 0.09999999999999998
3. current Y = -1, total cross = 3

1. movement = 1, times = 1
2. positive area = 117.6, negative area = 0.6
3. current Y = 0, total cross = 4

1. movement = -2, times = 2
2. positive area = 117.6, negative area = 4.6
3. current Y = -4, total cross = 4

1. movement = 1, times = 5
2. positive area = 118.1, negative area = 12.6
3. current Y = 1, total cross = 5

1. movement = -3, times = 1
2. positive area = 118.26666666666667, negative area = 13.266666666666666
3. current Y = -2, total cross = 6

1. movement = 2, times = 1
2. positive area = 118.26666666666667, negative area = 14.266666666666666
3. current Y = 0, total cross = 7

1. movement = 3, times = 4
2. positive area = 142.26666666666665, negative area = 14.266666666666666
3. current Y = 12, total cross = 7

1. movement = -2, times = 6
2. positive area = 178.26666666666665, negative area = 14.266666666666666
3. current Y = 0, total cross = 8

1. movement = 1, times = 2
2. positive area = 180.26666666666665, negative area = 14.266666666666666
3. current Y = 2, total cross = 8

1. movement = 2, times = 3
2. positive area = 195.26666666666665, negative area = 14.266666666666666
3. current Y = 8, total cross = 8

1. movement = -12, times = 1
2. positive area = 197.9333333333333, negative area = 14.933333333333332
3. current Y = -4, total cross = 9

1. movement = 2, times = 13
2. positive area = 318.9333333333333, negative area = 18.93333333333333
3. current Y = 22, total cross = 10

1. movement = -8, times = 3
2. positive area = 349.1833333333333, negative area = 19.18333333333333
3. current Y = -2, total cross = 11

1. movement = 3, times = 2
2. positive area = 351.84999999999997, negative area = 19.849999999999998
3. current Y = 4, total cross = 12

1. movement = -1, times = 2
2. positive area = 357.84999999999997, negative area = 19.849999999999998
3. current Y = 2, total cross = 12

1. movement = -2, times = 1
2. positive area = 358.84999999999997, negative area = 19.849999999999998
3. current Y = 0, total cross = 13

답: 1. 358.84999999999997, 2. 19.849999999999998, 3. 13
```
