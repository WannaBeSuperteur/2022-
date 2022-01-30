## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/64061
 * Programmers - 2019 카카오 개발자 겨울 인턴십

## 문제 풀이
```kotlin
class Solution {
    
    // 해당 column(n)에서 top인 인형의 번호 가져오기 (없으면 0)
    fun getTopOfColumn(board: Array<IntArray>, n: Int): Int {
        val size = board.count()
        
        for (i in 0..size-1) {
            val info = board[i][n-1]
            if (info > 0) {
                board[i][n-1] = 0
                return info
            }
        }
        return 0
    }
    
    fun solution(board: Array<IntArray>, moves: IntArray): Int {
        var stack = mutableListOf<Int>()
        var answer = 0
        
        for (move in moves) {
            val top = getTopOfColumn(board, move)
            if (top > 0) stack.add(top)
            
            val sc = stack.count()
            if (sc >= 2) {
                if(stack[sc-2] == stack[sc-1]) {
                    stack.removeAt(sc-1)
                    stack.removeAt(sc-2)
                    answer += 2
                }
            }
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.14ms, 56.3MB)
테스트 2 〉	통과 (0.07ms, 57.3MB)
테스트 3 〉	통과 (0.07ms, 56.4MB)
테스트 4 〉	통과 (0.51ms, 56.5MB)
테스트 5 〉	통과 (0.08ms, 56.7MB)
테스트 6 〉	통과 (0.07ms, 57.6MB)
테스트 7 〉	통과 (0.12ms, 56.7MB)
테스트 8 〉	통과 (0.31ms, 56.3MB)
테스트 9 〉	통과 (0.27ms, 57.3MB)
테스트 10 〉	통과 (0.27ms, 57.2MB)
테스트 11 〉	통과 (0.43ms, 56.7MB)
```
