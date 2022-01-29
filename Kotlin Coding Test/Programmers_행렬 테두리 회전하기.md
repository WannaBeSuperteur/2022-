## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/77485
 * Programmers - 2021 Dev-Matching: 웹 백엔드 개발자 (상반기)

## 문제 풀이
```kotlin
class Solution {
    
    fun swap(arr: Array<IntArray>, x0: Int, y0: Int, x1: Int, y1: Int) : Int {
        val temp = arr[x0][y0]
        arr[x0][y0] = arr[x1][y1]
        arr[x1][y1] = temp

        return Math.min(arr[x0][y0], arr[x1][y1])
    }
    
    fun solution(rows: Int, columns: Int, queries: Array<IntArray>): IntArray {
        
        // 배열 초기화
        val arr = Array(rows, {IntArray(columns)})
        for (i in 0..rows-1) {
            for (j in 0..columns-1) {
                arr[i][j] = i * columns + j + 1
            }
        }
        
        // 쿼리 처리하기
        var answer = mutableListOf<Int>()
        
        for (i in 0..queries.count()-1) {
            val x1 = queries[i][0]-1 // 0부터 시작하는 index를 이용
            val y1 = queries[i][1]-1
            val x2 = queries[i][2]-1
            val y2 = queries[i][3]-1
            
            var minValue = 10000
            
            for (j in y2-1 downTo y1) minValue = Math.min(minValue, swap(arr, x1, j, x1, j+1))
            for (j in x1..x2-1) minValue = Math.min(minValue, swap(arr, j, y1, j+1, y1))
            for (j in y1..y2-1) minValue = Math.min(minValue, swap(arr, x2, j, x2, j+1))
            for (j in x2-1 downTo x1+1) minValue = Math.min(minValue, swap(arr, j, y2, j+1, y2))
            
            answer.add(minValue)
        }
        return answer.toIntArray()
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (5.07ms, 57MB)
테스트 2 〉	통과 (5.02ms, 57.1MB)
테스트 3 〉	통과 (29.48ms, 68.8MB)
테스트 4 〉	통과 (16.55ms, 67.9MB)
테스트 5 〉	통과 (20.94ms, 67.8MB)
테스트 6 〉	통과 (22.18ms, 74.9MB)
테스트 7 〉	통과 (23.87ms, 68.2MB)
테스트 8 〉	통과 (18.29ms, 67.6MB)
테스트 9 〉	통과 (21.16ms, 69.9MB)
테스트 10 〉	통과 (22.94ms, 68.5MB)
테스트 11 〉	통과 (27.26ms, 67.5MB)
```
