## 요구사항 분석
다음과 같은 형태의 문자열에서 구분 기호는 1차는 ``` || ```, 2차는 ``` + ```, 3차는 ``` ```, 4차는 ```,```이다. 예를 들어 다음과 같은 경우,
```kotlin
abc,de,f g,h + ij,k l m + n || op,q r + st + uvw,x || y + z
```
이것을 풀어서 나타내면 다음과 같다.
```kotlin
big 1
    medium 1
        small 1
            abc
            de
            f
        small 2
            g
            h
    medium 2
        small 1
            ij
            k
        small 2
            l
        small 3
            m
    medium 3
        small 1
            n
big 2
    medium 1
        small 1
            op
            q
        small 2
            r
    medium 2
        small 1
            st
    medium 3
        small 1
            uvw
            x
big 3
    medium 1
        small 1
            y
    medium 2
        small 1
            z
```
이와 같이, 4차까지의 구분 기호가 이번에는 차례로 ```.|.```, ```.|.|.```, ```.||.```, ```.|.||.```일 때, 문자열을 풀어서 나타내는 함수를 작성하시오. (단, 각 항목은 모두 알파벳 소문자로만 구성되어 있다.)

### 데이터 클래스
데이터 클래스 없이 문자열만 하나 주어진다.

## 코드 및 실행 결과
### 코드
```kotlin
// 4개의 구분 기호를 기준으로 한 문자열을 풀어서 보여주기
fun displayString(str: String) {
    
    // 구분 기호를 "긴 것부터 차례대로" 다른 것으로 치환하는 아이디어를 이용
    // 1차: *, 2차: +, 3차: 공백, 4차: ,
    val newStr = str.replace(".|.||.", ",").replace(".|.|.", "+").replace(".||.", " ").replace(".|.", "*")
    
    // 치환된 구분 기호를 이용하여 구분
    newStr.split("*").forEachIndexed { idxB, big ->
        println("big ${idxB + 1}")
        big.split("+").forEachIndexed { idxM, medium ->
            println("    medium ${idxM + 1}")
            medium.split(" ").forEachIndexed { idxS, small ->
                println("        small ${idxS + 1}")
                small.split(",").forEach {
                    println("            ${it}")
                }
            }
        }
    }
}

fun main() {
    displayString("abc.|.||.de.|.||.f.||.g.|.||.h.|.|.ij.|.||.k.||.l.||.m.|.|.n.|.op.|.||.q.||.r.|.|.st.|.|.uvw.|.||.x.|.y.|.|.z")
}
```

### 실행 결과
```kotlin
big 1
    medium 1
        small 1
            abc
            de
            f
        small 2
            g
            h
    medium 2
        small 1
            ij
            k
        small 2
            l
        small 3
            m
    medium 3
        small 1
            n
big 2
    medium 1
        small 1
            op
            q
        small 2
            r
    medium 2
        small 1
            st
    medium 3
        small 1
            uvw
            x
big 3
    medium 1
        small 1
            y
    medium 2
        small 1
            z
```
