## 문제 정보
 * https://www.acmicpc.net/problem/6502
 * Baekjoon - 수학, 구현, 기하학, 피타고라스 정리

## 문제 풀이
```kotlin
fun main() {
    var count = 0
    
    while (true) {
        count++
        
        val info = readLine().toString()
        if (info.equals("0")) return
        
        val infoSplit = info.split(" ")
        val r = infoSplit[0].toInt()
        val w = infoSplit[1].toDouble()
        val l = infoSplit[2].toDouble()
        
        if (Math.sqrt(Math.pow(w, 2.0) + Math.pow(l, 2.0)) <= (2 * r).toDouble()) {
            println("Pizza " + count.toString() + " fits on the table.")
        } else {
            println("Pizza " + count.toString() + " does not fit on the table.")
        }
    }
}
```

## 채점 결과
```
맞았습니다!!
메모리 : 15324 KB
시간 : 168 ms
```
