## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/67257
 * Programmers - 2020 카카오 인턴십

## 문제 풀이
```kotlin
import kotlin.math.*

class Solution {
    
    fun compute(values:MutableList<Long>, operators:MutableList<String>, rank:String): Long {
        
        // 각 수식 기호별로 왼쪽부터 차례대로 찾아서 수식 계산
        for (r in rank) {
            val rr = r.toString()
            
            var ind: Int = 0
            for (i in 0..operators.size-1) {
                
                // 수식 기호가 일치하는 경우 계산
                if (rr.equals(operators.get(ind))) {
                    val num0 = values.get(ind)
                    val num1 = values.get(ind+1)
                    
                    when (rr) {
                        "+" -> values[ind] = num0 + num1
                        "-" -> values[ind] = num0 - num1
                        "*" -> values[ind] = num0 * num1
                    }
                    values.removeAt(ind+1)
                    operators.removeAt(ind)
                    
                } else {
                    ind++
                }
            }
        }
        
        // 최종 계산 결과 반환
        return values.get(0)
    }
    
    fun solution(expression: String): Long {
        
        // 숫자 값 추출
        val values_str = expression.split("+", "-", "*")
        val values = mutableListOf<Long>()
        
        // 텍스트를 숫자로 변환
        for (i in 0..values_str.size-1) {
            values.add(values_str.get(i).toLong())
        }
        
        // 수식 기호 추출
        val operators = mutableListOf<String>()
        
        for (e in expression) {
            val E = e.toString()
            if (E.equals("+") || E.equals("-") || E.equals("*")) {
                operators.add(E)
            }
        }
        
        // 각 우선순위에 대해 계산
        var answer : Long = 0
        for (case in listOf("+-*", "+*-", "-+*", "-*+", "*+-", "*-+")) {
            
            // values와 operators를 복사
            val values_copy = values.toMutableList()
            val ops_copy = operators.toMutableList()
            
            // 수식 계산
            val result = Math.abs(compute(values_copy, ops_copy, case))
            
            // 수식 계산 결과의 절댓값의 최댓값 갱신
            if (result > answer) {
                answer = result
            }
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (26.72ms, 61.6MB)
테스트 2 〉	통과 (27.56ms, 61.8MB)
테스트 3 〉	통과 (27.88ms, 60.9MB)
테스트 4 〉	통과 (25.65ms, 61.5MB)
테스트 5 〉	통과 (38.04ms, 61.8MB)
테스트 6 〉	통과 (37.53ms, 60.9MB)
테스트 7 〉	통과 (43.97ms, 62MB)
테스트 8 〉	통과 (28.77ms, 61.8MB)
테스트 9 〉	통과 (29.57ms, 61.8MB)
테스트 10 〉	통과 (30.80ms, 62MB)
테스트 11 〉	통과 (27.86ms, 61.8MB)
테스트 12 〉	통과 (31.62ms, 61.8MB)
테스트 13 〉	통과 (29.46ms, 61.2MB)
테스트 14 〉	통과 (36.75ms, 61.9MB)
테스트 15 〉	통과 (25.57ms, 62MB)
테스트 16 〉	통과 (27.83ms, 61.4MB)
테스트 17 〉	통과 (28.14ms, 61.7MB)
테스트 18 〉	통과 (25.38ms, 61.5MB)
테스트 19 〉	통과 (26.41ms, 61.9MB)
테스트 20 〉	통과 (27.20ms, 62.2MB)
테스트 21 〉	통과 (25.77ms, 61.5MB)
테스트 22 〉	통과 (25.55ms, 61.4MB)
테스트 23 〉	통과 (37.91ms, 61.9MB)
테스트 24 〉	통과 (29.43ms, 61.7MB)
테스트 25 〉	통과 (29.79ms, 61.5MB)
테스트 26 〉	통과 (37.35ms, 61.6MB)
테스트 27 〉	통과 (32.08ms, 61.7MB)
테스트 28 〉	통과 (33.42ms, 61.7MB)
테스트 29 〉	통과 (28.26ms, 61.8MB)
테스트 30 〉	통과 (25.35ms, 62.2MB)
```
