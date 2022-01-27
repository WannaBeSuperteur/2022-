## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/68936
 * Programmers - 월간 코드 챌린지 시즌 1

## 문제 풀이
```kotlin
class Solution {
    
    fun allTheSame(arr: Array<IntArray>, startY: Int, startX: Int, endY: Int, endX: Int) : Int {
        
        // 1개의 칸만 있으면 그 값을 반환
        if (endY - startY == 1 && endX - startX == 1) return arr[startY][startX]
        
        for(i in startY..endY-1) {
            for(j in startX..endX-1) {
                if(arr[i][j] != arr[startY][startX]) return -1
            }
        }
        return arr[startY][startX]
    }
    
    // 재귀를 이용하여 해결
    fun quad(arr: Array<IntArray>, startY: Int, startX: Int, endY: Int, endX: Int, answer: IntArray) {
        
        // 해당 범위 내의 모든 값이 같으면 answer의 해당 값의 개수를 1만큼 증가
        val allTheSameResult = allTheSame(arr, startY, startX, endY, endX)

        when(allTheSameResult) {
            
            // 모든 값이 같지는 않으면 재귀를 이용하여 하위 배열 탐색
            -1 -> {
                quad(arr, startY, startX, (startY + endY) / 2, (startX + endX) / 2, answer)
                quad(arr, startY, (startX + endX) / 2, (startY + endY) / 2, endX, answer)
                quad(arr, (startY + endY) / 2, startX, endY, (startX + endX) / 2, answer)
                quad(arr, (startY + endY) / 2, (startX + endX) / 2, endY, endX, answer)       
            }
            
            // 모든 값이 같으면 해당 지점에서 중단
            else -> {
                answer[allTheSameResult] += 1
            }
        }   
    }
    
    fun solution(arr: Array<IntArray>): IntArray {
        var answer: IntArray = intArrayOf(0, 0)
        quad(arr, 0, 0, arr.size, arr.size, answer)
        return answer
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.41ms, 56.8MB)
테스트 2 〉	통과 (0.32ms, 56.9MB)
테스트 3 〉	통과 (0.10ms, 56.1MB)
테스트 4 〉	통과 (0.04ms, 56.4MB)
테스트 5 〉	통과 (6.93ms, 69.1MB)
테스트 6 〉	통과 (3.58ms, 69.3MB)
테스트 7 〉	통과 (3.08ms, 69.2MB)
테스트 8 〉	통과 (2.71ms, 66.1MB)
테스트 9 〉	통과 (6.13ms, 65.8MB)
테스트 10 〉	통과 (6.58ms, 98MB)
테스트 11 〉	통과 (0.05ms, 56.4MB)
테스트 12 〉	통과 (0.04ms, 57.2MB)
테스트 13 〉	통과 (4.66ms, 66.3MB)
테스트 14 〉	통과 (9.62ms, 98.5MB)
테스트 15 〉	통과 (12.23ms, 101MB)
테스트 16 〉	통과 (3.62ms, 69.4MB)
```
