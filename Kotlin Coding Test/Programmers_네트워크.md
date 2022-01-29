## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/43162
 * Programmers - 코딩테스트 고득점 Kit - 깊이/너비 우선 탐색(DFS/BFS)

## 문제 풀이
```kotlin
class Solution {
    fun solution(n: Int, computers: Array<IntArray>): Int {
        
        // 인접 리스트 만들기
        val adjacentList = mutableListOf<MutableList<Int>>()
        
        for (i in 0..n-1) {
            val adjacent = mutableListOf<Int>()
            for (j in 0..computers[i].count()-1) {
                if (computers[i][j] == 1) {
                    adjacent.add(j)
                }
            }
            adjacentList.add(adjacent)
        }
        
        // 방문 여부
        val visited = mutableListOf<Boolean>()
        for (i in 0..n-1) visited.add(false)

        // DFS를 통해 네트워크 개수 구하기
        var answer = 0
        
        // 탐색 중인 노드를 0부터 n-1까지 검사
        for (startAt in 0..n-1) {
            
            // 이미 방문한 경우
            if (visited[startAt]) continue
            visited[startAt] = true
            
            val stack = mutableListOf<Int>()
            var current = startAt
            
            while (true) {

                // 주변 노드 탐색
                for (nei in adjacentList[current]) {
                    if (visited[nei] == false) {
                        stack.add(nei)
                    }
                }

                // stack이 비어 있으면 종료
                if (stack.isEmpty()) break

                // stack의 top에 있는 노드를 pop
                val top = stack[stack.count()-1]
                stack.remove(top)

                // current, visited 갱신
                current = top
                visited[current] = true
            }
            answer++
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.18ms, 58.5MB)
테스트 2 〉	통과 (0.11ms, 56.8MB)
테스트 3 〉	통과 (0.22ms, 58.1MB)
테스트 4 〉	통과 (0.31ms, 56.6MB)
테스트 5 〉	통과 (0.12ms, 57.3MB)
테스트 6 〉	통과 (1.20ms, 56.9MB)
테스트 7 〉	통과 (0.16ms, 56.2MB)
테스트 8 〉	통과 (0.41ms, 57.2MB)
테스트 9 〉	통과 (0.41ms, 56.4MB)
테스트 10 〉	통과 (0.37ms, 56.9MB)
테스트 11 〉	통과 (1.83ms, 60.3MB)
테스트 12 〉	통과 (1.17ms, 58.7MB)
테스트 13 〉	통과 (0.59ms, 57.1MB)
```
