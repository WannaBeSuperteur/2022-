## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/43163
 * Programmers - 연습문제 - 깊이/너비 우선 탐색 (DFS/BFS)

## 문제 풀이
```kotlin
class Solution {
    
    // 두 word 사이의 다른 글자 개수
    fun distance(word0: String, word1: String) : Int {
        var result = 0
        for (i in 0..word0.length-1) {
            if (word0[i] != word1[i]) result++
        }
        return result
    }
    
    fun solution(begin: String, target: String, words: Array<String>): Int {
        
        val visited = mutableListOf<Boolean>()
        for (i in 0..words.count()-1) visited.add(false)
        
        // 너비 우선 탐색 (BFS)
        val queue = mutableListOf<String>()
        var current = begin
        var depth = 0
        
        while (true) {
            
            // 이웃한 노드 찾기
            for (i in 0..words.count()-1) {
                val word = words[i]
                if (distance(current, word) == 1 && visited[i] == false) {
                    queue.add(word + "," + (depth + 1).toString())
                    visited[i] = true
                }
            }
            
            // queue가 비어 있으면 종료
            if (queue.isEmpty()) break
            
            // queue에서 가장 처음 항목을 꺼냄
            val dequeued = queue[0]
            queue.remove(dequeued)
            current = dequeued.split(",")[0]
            depth = dequeued.split(",")[1].toInt()
            
            // 목표 단어에 도달한 경우 종료
            if (current.equals(target)) return depth
        }
        
        // 목표 단어에 도달하지 못한 경우
        return 0
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (6.29ms, 57.4MB)
테스트 2 〉	통과 (6.27ms, 57.8MB)
테스트 3 〉	통과 (7.62ms, 57.6MB)
테스트 4 〉	통과 (6.12ms, 58.3MB)
테스트 5 〉	통과 (0.04ms, 56.6MB)
```
