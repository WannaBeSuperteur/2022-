## 요구사항 분석
워너비슈퍼추어대학교의 김 교수는 시험의 답안을 채점할 때 특정 키워드의 포함 및 제외 여부를 이용하여 점수를 매긴다.

각 문제는 ```정답 조건```, ```학생 답안```의 2가지 세부 항목으로 구분되며, 이 중 정답 조건은 다음의 형식을 따른다.
```kotlin
A 포함 SA점 B 포함 SB점 C 제외 SC점
```
정답 조건이 위와 같을 시, ```학생 답안```이 키워드 ```A```를 포함하면 ```SA```점을 추가하고, ```B```를 포함하면 ```SB```점을 추가한다. 또한 ```C```를 포함하면 기존 점수에서 ```SC```점을 차감한다. (단, 0점 이하가 되는 경우는 0점으로 한다.)

예를 들어, ```학생 답안```이 ```A```, ```B```, ```C```를 모두 포함할 때의 점수는 ```SA + SB - SC```점이다.

아래의 **데이터 클래스**와 같은 형식으로 주어진 데이터에서, 학생의 각 문항별 점수 및 시험의 최종 점수를 구하시오.

### 데이터 클래스
```kotlin
// 시험 정보
data class Exam (
    val subject_name: String, // 과목명
    val subject_code: Int, // 과목 코드
    val date_time: String, // 시험 일시
    val questions: List<Question> // 시험 문제 목록
)

// 시험 문항별 정답 조건 및 학생 답안
data class Question (
    val conditions: String, // 정답 조건
    val answer: String // 학생 답안
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 시험 정보
data class Exam (
    val subject_name: String, // 과목명
    val subject_code: Int, // 과목 코드
    val date_time: String, // 시험 일시
    val questions: List<Question> // 시험 문제 목록
)

// 시험 문항별 정답 조건 및 학생 답안
data class Question (
    val conditions: String, // 정답 조건
    val answer: String // 학생 답안
)

// 점수 계산
fun computeScore(exam: Exam): Int {
    return exam.questions.sumOf {
        
        // 조건 파싱
        val conditionMap = mutableMapOf<String, Int>()
        val conditionSplit = it.conditions.split(" ")
        
        conditionSplit.forEachIndexed { idx, s ->
            if (s == "포함") {
                conditionMap.put(
                    conditionSplit[idx-1], conditionSplit[idx+1].substringBefore("점").toInt()
                )
            } else if (s == "제외") {
                conditionMap.put(
                    conditionSplit[idx-1], -conditionSplit[idx+1].substringBefore("점").toInt()
                )
            }
        }
        
        println("1. ${conditionSplit}")
        println("2. ${it.answer}")
        
        // 학생 답안 채점 (map이므로 list로 바꾼 후 first, second를 이용하여 sumOf)
        var score = conditionMap.toList().sumOf { c ->
            if (it.answer.contains(c.first)) {
                c.second
            } else {
                0
            }
        }
        score = Math.max(score, 0)
        
        // 해당 문제의 만점 출력
        val totalScore = conditionMap.toList().sumOf { c ->
            Math.max(c.second, 0)
        }
        
        println("3. ${score} / ${totalScore}\n")
        score
    }
}

fun main() {
    
    // 임시 시험 정보
    val testExam = Exam (
        "알고리즘",
        12345,
        "2022-10-18 17:00",
        listOf<Question> (
            Question("문제 포함 4점 해결 포함 5점 명령 포함 3점 절차 포함 5점 집합 포함 3점", "알고리즘은 문제 해결을 위한 명령의 절차의 집합이다."),
            Question("스택 포함 5점 큐 포함 5점 트리 제외 4점 그래프 제외 3점", "스택 큐 트리 그래프"),
            Question("트리 포함 5점 그래프 포함 5점 스택 제외 4점 큐 제외 4점", "트리와 그래프"),
            Question("분할정복법 포함 10점 그리디 제외 7점 메모이제이션 제외 7점", "분할정복법, 그리디, 메모이제이션"),
            Question("BFS 포함 10점 DFS 포함 10점 다익스트라 제외 10점", "BFS, 브루트 포스, DFS"),
            Question("재귀 포함 10점 스택 포함 5점 큐 포함 5점", "재귀와 스택을 이용하고 또 스택으로 해결한다."),
            Question("인공지능 포함 5점 머신러닝 포함 5점", "인공지능은 위대하다")
        )
    )
    
    // 점수 계산
    println("최종 점수: ${computeScore(testExam)}")
}
```

### 실행 결과
```kotlin
1. [문제, 포함, 4점, 해결, 포함, 5점, 명령, 포함, 3점, 절차, 포함, 5점, 집합, 포함, 3점]
2. 알고리즘은 문제 해결을 위한 명령의 절차의 집합이다.
3. 20 / 20

1. [스택, 포함, 5점, 큐, 포함, 5점, 트리, 제외, 4점, 그래프, 제외, 3점]
2. 스택 큐 트리 그래프
3. 3 / 10

1. [트리, 포함, 5점, 그래프, 포함, 5점, 스택, 제외, 4점, 큐, 제외, 4점]
2. 트리와 그래프
3. 10 / 10

1. [분할정복법, 포함, 10점, 그리디, 제외, 7점, 메모이제이션, 제외, 7점]
2. 분할정복법, 그리디, 메모이제이션
3. 0 / 10

1. [BFS, 포함, 10점, DFS, 포함, 10점, 다익스트라, 제외, 10점]
2. BFS, 브루트 포스, DFS
3. 20 / 20

1. [재귀, 포함, 10점, 스택, 포함, 5점, 큐, 포함, 5점]
2. 재귀와 스택을 이용하고 또 스택으로 해결한다.
3. 15 / 20

1. [인공지능, 포함, 5점, 머신러닝, 포함, 5점]
2. 인공지능은 위대하다
3. 5 / 10

최종 점수: 73
```
