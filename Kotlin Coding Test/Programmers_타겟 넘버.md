## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/43165
 * Programmers - 연습문제 - 깊이/너비 우선 탐색 (DFS/BFS)

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    
    // 2로 나누기 가능한 횟수
    fun getCountOfDivideBy2(n: Int) : Int {
        var count = 0
        var N = n
        
        while (N % 2 == 0) {
            N /= 2
            count++
        }
        return count
    }
    
    // 두 배열의 곱의 합
    fun sumProduct(arr0: IntArray, arr1: IntArray) : Int {
        val n = arr0.size
        var result = 0
        for (i in 0..n-1) {
            result += arr0[i] * arr1[i]
        }
        return result
    }
    
    // gray code 생성
    fun getGrayCode(n: Int, positive: Int, negative: Int) : MutableList<IntArray> {
        
        // 최종 결과로 생성되는 grayCode 값의 배열
        val result = mutableListOf<IntArray>()
        
        // 1개 비트씩 바꿔 가면서 result에 값 추가
        val value = mutableListOf<Int>()
        for (i in 0..n-1) value.add(negative)
        
        // 2^n회 반복하면서 추가
        val power = (2.0).pow(n).toInt()
        
        for (i in 0..power-2) {
            result.add(value.toIntArray())
            
            val indexToToggle = n - 1 - getCountOfDivideBy2(i + 1)
            when (value[indexToToggle]) {
                positive -> value[indexToToggle] = negative
                negative -> value[indexToToggle] = positive
            }
        }
        result.add(value.toIntArray())

        return result
    }
    
    fun solution(numbers: IntArray, target: Int): Int {
        
        val n = numbers.size
        
        // gray code 생성
        val grayCode = getGrayCode(numbers.size, 1, -1)
        var answer = 0
        
        // gray code의 각 case를 검토 (완전탐색)
        val power = (2.0).pow(n).toInt()
    
        for(i in 0..power-1) {
            val computeResult = sumProduct(numbers, grayCode[i])
            if (computeResult == target) answer++
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (184.83ms, 195MB)
테스트 2 〉	통과 (182.82ms, 195MB)
테스트 3 〉	통과 (6.36ms, 57.4MB)
테스트 4 〉	통과 (11.69ms, 57.6MB)
테스트 5 〉	통과 (20.23ms, 61.2MB)
테스트 6 〉	통과 (7.28ms, 57.8MB)
테스트 7 〉	통과 (6.29ms, 57.2MB)
테스트 8 〉	통과 (13.69ms, 57.8MB)
```
