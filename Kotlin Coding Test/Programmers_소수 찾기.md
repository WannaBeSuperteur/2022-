## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/42839
 * Programmers - 완전탐색

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    
    // 팩토리얼 계산
    /*
    fun factorial(n: Int) : Int {
        var result = 1
        for (i in 2..n) result *= i
        return result
    }
    */
    
    // 소수 여부 판정
    fun isPrime(n: Int) : Boolean {
        if (n <= 1) return false
        
        val sqrtN = sqrt(n.toDouble()).toInt()
        for (i in 2..sqrtN) {
            if (n % i == 0) return false
        }
        return true
    }
    
    // 순열 생성
    fun makePermutation(numbers: String) : MutableList<String> {
        val n = numbers.length
        
        // 최종 결과로 반환되는 순열
        val result = mutableListOf<String>()
        
        // 1개의 숫자만 있으면 그것을 반환
        if (n == 1) {
            result.add(numbers)
        }
        
        // 2개 이상의 숫자가 있으면
        for (i in 0..n-1) {
            val without = numbers.substring(i, i+1)
            val subResult = makePermutation(numbers.substring(0, i) + numbers.substring(i+1))
            for (num in subResult) {
                result.add(without + num)
                result.add(num)
            }
        }
        
        return result
    }
    
    fun solution(numbers: String): Int {
        
        // 1개의 숫자일 때
        if (numbers.length == 1) {
            when (isPrime(numbers.toInt())) {
                true -> return 1
                false -> return 0
            }
        }
        
        // 2개 이상의 숫자일 때
        val permutation = makePermutation(numbers)
        
        var perm_int = mutableListOf<Int>()
        for (value in permutation) {
            perm_int.add(value.toInt())
        }
        
        val perm_set = perm_int.toSet().toMutableList()
        
        var answer = 0
        for (value in perm_set) {
            if (isPrime(value)) answer++
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (7.40ms, 57.1MB)
테스트 2 〉	통과 (26.87ms, 76.3MB)
테스트 3 〉	통과 (6.98ms, 57.9MB)
테스트 4 〉	통과 (26.90ms, 72.1MB)
테스트 5 〉	통과 (142.93ms, 126MB)
테스트 6 〉	통과 (6.81ms, 58.9MB)
테스트 7 〉	통과 (7.24ms, 57.9MB)
테스트 8 〉	통과 (164.30ms, 124MB)
테스트 9 〉	통과 (7.16ms, 57.9MB)
테스트 10 〉	통과 (26.80ms, 74.9MB)
테스트 11 〉	통과 (10.76ms, 58.1MB)
테스트 12 〉	통과 (10.04ms, 58.5MB)
```
