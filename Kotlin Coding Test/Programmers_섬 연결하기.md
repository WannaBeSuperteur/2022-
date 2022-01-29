## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/42861
 * Programmers - 코딩테스트 고득점 Kit - 탐욕법(Greedy)

## 문제 풀이
```kotlin
class Solution {
    
    // 섬 번호(A, B) 및 섬 간의 cost
    data class costData (var A: Int, var B: Int, var cost: Int)
    
    // costs 배열을 costData의 배열로 변환
    fun costsToCostData(costs: Array<IntArray>): MutableList<costData> {
        
        val nCosts = costs.count()
        val newCosts = mutableListOf<costData>()
        
        for (i in 0..nCosts-1) {
            val newCost = costData(costs[i][0], costs[i][1], costs[i][2])
            newCosts.add(newCost)
        }
        
        return newCosts
    }
    
    // Kruskal 알고리즘의 기초 문제
    fun solution(n: Int, costs: Array<IntArray>): Int {
        
        val nCosts = costs.count()
        
        // costs 배열을 costData 클래스 형태로 변환
        val newCosts = costsToCostData(costs)
        
        // 가장 작은 cost 순으로 찾기 위해 costs 배열을 cost 기준으로 정렬하기
        newCosts.sortBy{it.cost}
        
        // 섬의 set 초기화하기
        var sets = mutableListOf<Int>()
        for (i in 0..n-1) sets.add(i)
        
        // 가장 작은 cost부터 순서대로 Kruskal 알고리즘 적용
        var answer = 0
        var modifications = 0
        
        for (i in 0..nCosts-1) {
            val setA = sets[newCosts[i].A]
            val setB = sets[newCosts[i].B]
            
            // 두 섬이 속한 set이 서로 다르면 같도록 정보를 변경
            if (setA != setB) {
                answer += newCosts[i].cost
                for(j in 0..n-1) {
                    if (sets[j] == setB) sets[j] = setA
                }
                modifications++
            }
            
            // 정보 변경 횟수가 n-1 이상이면 모든 섬이 한 set에 있으므로 종료
            if (modifications >= n-1) break
        }
        
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (5.90ms, 57.7MB)
테스트 2 〉	통과 (6.46ms, 58MB)
테스트 3 〉	통과 (6.02ms, 57.4MB)
테스트 4 〉	통과 (6.17ms, 56.9MB)
테스트 5 〉	통과 (6.43ms, 57.5MB)
테스트 6 〉	통과 (6.33ms, 57.2MB)
테스트 7 〉	통과 (7.22ms, 57.2MB)
테스트 8 〉	통과 (8.10ms, 57.2MB)
```
