## 문제 정보
 * https://www.acmicpc.net/problem/2975
 * Baekjoon - 수학, 구현, 사칙연산

## 문제 풀이
```kotlin
fun main() {
    while (true) {
        val transaction = readLine().toString()
        if (transaction.equals("0 W 0")) return
        
        val transactionInfo = transaction.split(" ")
        val value0 = transactionInfo[0].toInt()
        val WD = transactionInfo[1]
        val value1 = transactionInfo[2].toInt()
        
        if (value0 - value1 < -200 && WD.equals("W")) {
            println("Not allowed")
        } else {
            when(WD) {
                "W" -> println(value0 - value1)
                "D" -> println(value0 + value1)
            }
        }
    }
}
```

## 채점 결과
```
맞았습니다!!
메모리 : 14260 KB
시간 : 112 ms
```
