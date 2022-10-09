## 요구사항 분석
워너비슈퍼추어소프트에서는 자체 코딩 테스트 플랫폼을 만들어서 2022년 10월 수시 채용을 위한 코딩 테스트를 실시하였다. 코딩 테스트의 채점 기준은 다음과 같다.
* 만점의 ```60%``` 이상을 득점한 지원자들을 합격시킨다. ```60%``` 미만 득점자는 탈락이다.
* 각 문제별로 배점이 서로 다르다.
* 각 문제별로 여러 개의 테스트 케이스가 있으며, 해당 문제의 득점 비율을 해당 문제의 배점으로 변환한 점수를 해당 문제에서 획득한 점수로 한다.
  * 득점 비율이란 ```정답 테스트 케이스```의 득점의 합산을 ```모든 테스트 케이스```의 득점의 합산으로 나눈 값이다.
  * 각 테스트 케이스의 배점은 서로 다르다.
* 테스트 케이스의 정답과 지원자의 알고리즘이 출력한 답을 비교하여, 다음을 모두 만족시키는 경우 ```정답 테스트 케이스```로 채점한다.
  * 정답과 지원자의 답에서 ```채점 제외 문자 목록```에 속한 문자들을 각각 제거한 결과가 동일하다.
  * 알고리즘 수행 시간이 ```제한시간```을 초과하지 않는다.
  * 알고리즘 수행에 사용된 메모리 양이 ```제한 메모리```를 초과하지 않는다.

특정 지원자의 코딩 테스트 채점 기록이 주어졌을 때, 해당 지원자의 합격 여부를 표시하시오.

### 데이터 클래스
```kotlin
// 지원자 정보
data class Candidate (
    val id: Int, // 지원자 ID
    val name: String, // 지원자 이름
    val questions: List<Question> // 코딩 테스트 문제 목록
)

// 코딩 테스트 문제
data class Question (
    val id: Int, // 문제 ID
    val name: String, // 문제 이름
    val time_limit: Int, // 제한시간 (ms)
    val memory_limit: Int, // 제한 메모리 (MB)
    val score: Int, // 배점
    val except_char: List<Char>, // 채점 제외 문자 목록
    val test_cases: List<TestCase> // 테스트 케이스 목록
)

// 테스트 케이스
data class TestCase (
    val score: Int, // 테스트 케이스의 배점
    val correct_answer: String, // 정답
    var candidate_answer: String, // 지원자의 알고리즘이 출력한 답
    val time: Int, // 알고리즘 수행 시간 (ms)
    val memory: Int // 알고리즘 수행 시 사용된 메모리 (MB)
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 지원자 정보
data class Candidate (
    val id: Int, // 지원자 ID
    val name: String, // 지원자 이름
    val questions: List<Question> // 코딩 테스트 문제 목록
)

// 코딩 테스트 문제
data class Question (
    val id: Int, // 문제 ID
    val name: String, // 문제 이름
    val time_limit: Int, // 제한시간 (ms)
    val memory_limit: Int, // 제한 메모리 (MB)
    val score: Int, // 배점
    val except_char: List<Char>, // 채점 제외 문자 목록
    val test_cases: List<TestCase> // 테스트 케이스 목록
)

// 테스트 케이스
data class TestCase (
    val score: Int, // 테스트 케이스의 배점
    val correct_answer: String, // 정답
    var candidate_answer: String, // 지원자의 알고리즘이 출력한 답
    val time: Int, // 알고리즘 수행 시간 (ms)
    val memory: Int // 알고리즘 수행 시 사용된 메모리 (MB)
)

// 각 문제별 획득 점수 계산
fun computeScore(question: Question): Int {
    
    // 모든 테스트 케이스의 배점의 합산
    val totalTestCaseScore = question.test_cases.sumOf { it.score }
    
    // 정답인 테스트 케이스만 필터링
    val correctTestCases = question.test_cases.filter {
        val isTimeSuccess = it.time <= question.time_limit
        val isMemorySuccess = it.memory <= question.memory_limit
        
        val reducedCandidateAnswer = question.except_char.fold(
            it.candidate_answer
        ) { acc, e ->
            acc.replace(e.toString(), "")
        }
        val reducedCorrectAnswer = question.except_char.fold(
            it.correct_answer
        ) { acc, e ->
            acc.replace(e.toString(), "")
        }
        val isAnswerCorrect = reducedCandidateAnswer == reducedCorrectAnswer
        
        val finalResult = isTimeSuccess && isMemorySuccess && isAnswerCorrect
        val mark = if (finalResult) { "P" } else { "F" }
        
        println(
            "[${mark}] 점수: ${it.score}, 시간: ${isTimeSuccess}, 메모리: ${isMemorySuccess} " +
            "답: ${reducedCandidateAnswer}, ${reducedCorrectAnswer} (${isAnswerCorrect})"
        )
        finalResult
    }
    
    // 정답인 테스트 케이스의 배점의 합산
    val correctTestCaseScore = correctTestCases.sumOf { it.score }
    
    // 점수 변환
    val finalScore = question.score * correctTestCaseScore / totalTestCaseScore
    
    println("\n3. 모든 케이스 점수: ${totalTestCaseScore}")
    println("4. 정답 케이스 점수: ${correctTestCaseScore}")
    println("5. 문제 배점: ${question.score}, 최종 점수: ${finalScore}\n")
    
    return finalScore
}

// 채점 및 합격 여부 판정
fun analysisTestAnswers(candidate: Candidate): Boolean {
    val scoreSum = candidate.questions.sumOf { it.score }
    val acquired = candidate.questions.sumOf { computeScore(it) }
    
    println("1. 총점: ${scoreSum}")
    println("2. 획득: ${acquired}")
    
    return acquired >= 0.6 * scoreSum
}

fun main() {
    
    // 임시 지원자 정보
    val testCandidate = Candidate(
        1000, "hong",
        listOf<Question> (
            Question(
                20, "징검다리", 300, 128, 1000, listOf(' '),
                listOf<TestCase> (
                    TestCase(3, "1234567", "1234567", 124, 36),
                    TestCase(3, "1357", "13 57", 96, 33),
                    TestCase(4, "145689", "1 4 5 6 8 9", 136, 45),
                    TestCase(5, "123456", "1234456", 168, 45),
                    TestCase(6, "12468", "12468", 256, 60),
                    TestCase(6, "124567", "124 56   7", 244, 62),
                    TestCase(6, "123456789101112", "1 2 3 4 5 6 7 8 9 10 11 12", 244, 78),
                    TestCase(5, "13579", "1 3 5. 7 9", 140, 34),
                    TestCase(4, "14681016", "1 4 6 8 10 16", 302, 64),
                    TestCase(5, "15101520", "1 5 10 15 20", 264, 132),
                    TestCase(5, "14691217", "14691217", 310, 85),
                    TestCase(7, "345", "34 5", 284, 100),
                    TestCase(7, "1359", "1 3 59", 277, 76),
                    TestCase(7, "123456711", "1 2 3 4 5 6 7", 324, 136)
                )
            ),
            Question(
                21, "시그널", 500, 256, 1500, listOf(' ', '.', '*'),
                listOf<TestCase> (
                    TestCase(2, "1", "1", 144, 96),
                    TestCase(3, "2", "2", 152, 100),
                    TestCase(3, "7", "7", 168, 104),
                    TestCase(4, "2", "2*", 160, 96),
                    TestCase(2, "1", "1 ", 164, 96),
                    TestCase(3, "4", " .4", 224, 108),
                    TestCase(5, "5", ".*5", 200, 112),
                    TestCase(2, "3", "3", 276, 108),
                    TestCase(6, "4", "4.", 264, 112),
                    TestCase(8, "5", "5.*", 280, 160),
                    TestCase(7, "6", "6", 340, 260),
                    TestCase(7, "0", " 0.", 404, 212),
                    TestCase(9, "8", "   8.*", 520, 244),
                    TestCase(6, "3", "3", 172, 120),
                    TestCase(8, "9", "9", 204, 120),
                    TestCase(8, "3", "4", 284, 124),
                    TestCase(8, "1", "2  1", 388, 136),
                )
            ),
            Question(
                22, "정확성의 최종 보스", 4000, 1024, 2000, listOf('+', '-'),
                listOf<TestCase> (
                    TestCase(5, "A", "A+", 720, 152),
                    TestCase(5, "C", "C", 724, 153),
                    TestCase(7, "F", "F", 896, 157),
                    TestCase(9, "A", "A+", 1008, 160),
                    TestCase(11, "A", "B-", 1208, 158),
                    TestCase(13, "S", "S-", 1216, 166),
                    TestCase(13, "B", "B+", 1344, 170),
                    TestCase(14, "S", "S", 1604, 172),
                    TestCase(15, "C", "B", 1884, 178)
                )
            ),
            Question(
                23, "효율성의 최종 보스", 100, 64, 2000, listOf(),
                listOf<TestCase> (
                    TestCase(3, "107", "107", 60, 36),
                    TestCase(3, "24", "24", 64, 36),
                    TestCase(5, "68", "68 ", 72, 37),
                    TestCase(7, "29", "29", 72, 38),
                    TestCase(8, "124", "124", 100, 38),
                    TestCase(8, "136", "117", 92, 36),
                    TestCase(12, "88", "88", 136, 41),
                    TestCase(15, "52", "52", 148, 42),
                    TestCase(15, "207", "207", 96, 64)
                )
            )
        )
    )
    
    // 합격 여부
    println("합격 여부: ${analysisTestAnswers(testCandidate)}")
}
```

### 실행 결과
```kotlin
[P] 점수: 3, 시간: true, 메모리: true 답: 1234567, 1234567 (true)
[P] 점수: 3, 시간: true, 메모리: true 답: 1357, 1357 (true)
[P] 점수: 4, 시간: true, 메모리: true 답: 145689, 145689 (true)
[F] 점수: 5, 시간: true, 메모리: true 답: 1234456, 123456 (false)
[P] 점수: 6, 시간: true, 메모리: true 답: 12468, 12468 (true)
[P] 점수: 6, 시간: true, 메모리: true 답: 124567, 124567 (true)
[P] 점수: 6, 시간: true, 메모리: true 답: 123456789101112, 123456789101112 (true)
[F] 점수: 5, 시간: true, 메모리: true 답: 135.79, 13579 (false)
[F] 점수: 4, 시간: false, 메모리: true 답: 14681016, 14681016 (true)
[F] 점수: 5, 시간: true, 메모리: false 답: 15101520, 15101520 (true)
[F] 점수: 5, 시간: false, 메모리: true 답: 14691217, 14691217 (true)
[P] 점수: 7, 시간: true, 메모리: true 답: 345, 345 (true)
[P] 점수: 7, 시간: true, 메모리: true 답: 1359, 1359 (true)
[F] 점수: 7, 시간: false, 메모리: false 답: 1234567, 123456711 (false)

3. 모든 케이스 점수: 73
4. 정답 케이스 점수: 42
5. 문제 배점: 1000, 최종 점수: 575

[P] 점수: 2, 시간: true, 메모리: true 답: 1, 1 (true)
[P] 점수: 3, 시간: true, 메모리: true 답: 2, 2 (true)
[P] 점수: 3, 시간: true, 메모리: true 답: 7, 7 (true)
[P] 점수: 4, 시간: true, 메모리: true 답: 2, 2 (true)
[P] 점수: 2, 시간: true, 메모리: true 답: 1, 1 (true)
[P] 점수: 3, 시간: true, 메모리: true 답: 4, 4 (true)
[P] 점수: 5, 시간: true, 메모리: true 답: 5, 5 (true)
[P] 점수: 2, 시간: true, 메모리: true 답: 3, 3 (true)
[P] 점수: 6, 시간: true, 메모리: true 답: 4, 4 (true)
[P] 점수: 8, 시간: true, 메모리: true 답: 5, 5 (true)
[F] 점수: 7, 시간: true, 메모리: false 답: 6, 6 (true)
[P] 점수: 7, 시간: true, 메모리: true 답: 0, 0 (true)
[F] 점수: 9, 시간: false, 메모리: true 답: 8, 8 (true)
[P] 점수: 6, 시간: true, 메모리: true 답: 3, 3 (true)
[P] 점수: 8, 시간: true, 메모리: true 답: 9, 9 (true)
[F] 점수: 8, 시간: true, 메모리: true 답: 4, 3 (false)
[F] 점수: 8, 시간: true, 메모리: true 답: 21, 1 (false)

3. 모든 케이스 점수: 91
4. 정답 케이스 점수: 59
5. 문제 배점: 1500, 최종 점수: 972

[P] 점수: 5, 시간: true, 메모리: true 답: A, A (true)
[P] 점수: 5, 시간: true, 메모리: true 답: C, C (true)
[P] 점수: 7, 시간: true, 메모리: true 답: F, F (true)
[P] 점수: 9, 시간: true, 메모리: true 답: A, A (true)
[F] 점수: 11, 시간: true, 메모리: true 답: B, A (false)
[P] 점수: 13, 시간: true, 메모리: true 답: S, S (true)
[P] 점수: 13, 시간: true, 메모리: true 답: B, B (true)
[P] 점수: 14, 시간: true, 메모리: true 답: S, S (true)
[F] 점수: 15, 시간: true, 메모리: true 답: B, C (false)

3. 모든 케이스 점수: 92
4. 정답 케이스 점수: 66
5. 문제 배점: 2000, 최종 점수: 1434

[P] 점수: 3, 시간: true, 메모리: true 답: 107, 107 (true)
[P] 점수: 3, 시간: true, 메모리: true 답: 24, 24 (true)
[F] 점수: 5, 시간: true, 메모리: true 답: 68 , 68 (false)
[P] 점수: 7, 시간: true, 메모리: true 답: 29, 29 (true)
[P] 점수: 8, 시간: true, 메모리: true 답: 124, 124 (true)
[F] 점수: 8, 시간: true, 메모리: true 답: 117, 136 (false)
[F] 점수: 12, 시간: false, 메모리: true 답: 88, 88 (true)
[F] 점수: 15, 시간: false, 메모리: true 답: 52, 52 (true)
[P] 점수: 15, 시간: true, 메모리: true 답: 207, 207 (true)

3. 모든 케이스 점수: 76
4. 정답 케이스 점수: 36
5. 문제 배점: 2000, 최종 점수: 947

1. 총점: 6500
2. 획득: 3928
합격 여부: true
```
