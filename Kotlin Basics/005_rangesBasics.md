# ranges의 기초
## 설명
 * Kotlin의 ```ranges```는 Java의 ```for 문```과 같이 값의 범위를 지정
 * 다음의 2가지는 서로 같은 의미임.
   * (Java) ```for (int i = 0; i <= 10; i++)```
   * (Kotlin) ```for (i in 0..10)```
   * ```for (i in a..b)```에서 <b>양 끝 값인 ```a```와 ```b```를 포함</b>한다는 것에 유의.
 * 변수가 특정 범위 내에 있는지 확인하기
   * ```ARGNAME in A..B```는 ```ARGNAME``` 변수가 ```A 이상 B 이하```의 범위에 속해 있다는 것을 의미
   * ```ARGNAME !in A..B```는 ```ARGNAME``` 변수가 ```A 이상 B 이하```의 범위에 속해 있지 않다는 것을 의미

## 예제 코드
```kotlin
fun main() {
    
    // i=7,8,9,10, j=8,9를 곱한 값들을 출력
    for (i in 7..10) {
        for (j in 8..9) {
            // println(i + " * " + j + " = " + (i * j)) (ERROR)
            println(String.format("%d * %d = %d", i, j, i * j))
        }
    }
    println("")
    
    // 1부터 10까지에 대해, 3 이상 7 이하인지를 출력
    for (i in 1..10) {
        if (i in 3..7) println(String.format("%d is between 3 and 7", i))
        else println(String.format("%d is NOT between 3 and 7", i))
    }
    println("")
    
    // 11부터 20까지에 대해, 12 이상 15 이하의 범위에 없는지를 출력
    for (i in 11..20) {
        if (i !in 12..15) println(String.format("%d is NOT between 12 and 15", i))
        else println(String.format("%d is between 12 and 15", i))
    }
}
```

## 실행 결과
```
7 * 8 = 56
7 * 9 = 63
8 * 8 = 64
8 * 9 = 72
9 * 8 = 72
9 * 9 = 81
10 * 8 = 80
10 * 9 = 90

1 is NOT between 3 and 7
2 is NOT between 3 and 7
3 is between 3 and 7
4 is between 3 and 7
5 is between 3 and 7
6 is between 3 and 7
7 is between 3 and 7
8 is NOT between 3 and 7
9 is NOT between 3 and 7
10 is NOT between 3 and 7

11 is NOT between 12 and 15
12 is between 12 and 15
13 is between 12 and 15
14 is between 12 and 15
15 is between 12 and 15
16 is NOT between 12 and 15
17 is NOT between 12 and 15
18 is NOT between 12 and 15
19 is NOT between 12 and 15
20 is NOT between 12 and 15
```
