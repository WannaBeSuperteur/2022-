## 문제 정보
 * https://programmers.co.kr/learn/courses/30/lessons/81302
 * Programmers - 2021 카카오 채용연계형 인턴십

## 문제 풀이
```kotlin
class Solution {
    fun isSocialDistancing(place: Array<String>): Int {
        for (i in 0..3) {
            for (j in 0..4) {
                if (place[i][j] == 'P' && place[i+1][j] == 'P') return 0
                if (place[j][i] == 'P' && place[j][i+1] == 'P') return 0
            }
        }
        for (i in 0..2) {
            for (j in 0..4) {
                if (place[i][j] == 'P' && place[i+1][j] == 'O' && place[i+2][j] == 'P') return 0
                if (place[j][i] == 'P' && place[j][i+1] == 'O' && place[j][i+2] == 'P') return 0
            }
        }
        for (i in 0..3) {
            for (j in 0..3) {
                if (place[i][j] == 'P' && place[i+1][j+1] == 'P' && (place[i][j+1] == 'O' || place[i+1][j] == 'O')) return 0
                if (place[i+1][j] == 'P' && place[i][j+1] == 'P' && (place[i][j] == 'O' || place[i+1][j+1] == 'O')) return 0
            }
        }
        return 1
    }
    
    fun solution(places: Array<Array<String>>): IntArray {
        val result = IntArray(5)
        for (i in 0..4) {
            result[i] = isSocialDistancing(places[i])
        }
        return result
    }
}
```

## 채점 결과
```
테스트 1 〉	통과 (0.05ms, 57.2MB)
테스트 2 〉	통과 (0.03ms, 56.8MB)
테스트 3 〉	통과 (0.03ms, 55.7MB)
테스트 4 〉	통과 (0.05ms, 57MB)
테스트 5 〉	통과 (0.02ms, 56.9MB)
테스트 6 〉	통과 (0.03ms, 56.9MB)
테스트 7 〉	통과 (0.03ms, 56.6MB)
테스트 8 〉	통과 (0.04ms, 59.1MB)
테스트 9 〉	통과 (0.03ms, 57.9MB)
테스트 10 〉	통과 (0.03ms, 58.6MB)
테스트 11 〉	통과 (0.05ms, 56.8MB)
테스트 12 〉	통과 (0.02ms, 56.4MB)
테스트 13 〉	통과 (0.03ms, 57MB)
테스트 14 〉	통과 (0.03ms, 56.5MB)
테스트 15 〉	통과 (0.03ms, 56.8MB)
테스트 16 〉	통과 (0.04ms, 56.5MB)
테스트 17 〉	통과 (0.04ms, 57.4MB)
테스트 18 〉	통과 (0.02ms, 57.5MB)
테스트 19 〉	통과 (0.02ms, 57.8MB)
테스트 20 〉	통과 (0.03ms, 56.7MB)
테스트 21 〉	통과 (0.02ms, 57.2MB)
테스트 22 〉	통과 (0.02ms, 57.7MB)
테스트 23 〉	통과 (0.03ms, 57.1MB)
테스트 24 〉	통과 (0.03ms, 57.7MB)
테스트 25 〉	통과 (0.03ms, 56.5MB)
테스트 26 〉	통과 (0.03ms, 57MB)
테스트 27 〉	통과 (0.03ms, 57.3MB)
테스트 28 〉	통과 (0.04ms, 57.2MB)
테스트 29 〉	통과 (0.03ms, 56.7MB)
테스트 30 〉	통과 (0.04ms, 56.6MB)
```
