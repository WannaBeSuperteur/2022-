## 요구사항 분석
워너비슈퍼추어소프트에서는 올해 하반기에 총 5명의 개발자를 채용하기로 결정하였다. 전형 순서는 ```코딩 테스트 (총 3회) + 서류 결격사유 확인 -> 면접 (총 2회)```이다. 이를 위해서 총 15명의 지원자를 면접 대상자를 선발하려고 하는데, 그 기준은 다음과 같다.
* 서류 전형에서 결격 사유가 있는 인원은 면접 대상자 선발을 위한 지원자 목록에서 먼저 제외한다. 이때 다음 중 1가지 이상을 만족시키면 서류 전형에서 결격 사유가 있는 것이다.
  * ```경력```의 일수가 ```180일``` 또는 ```1097일``` 이상
  * 현재 전형과 지원 기록상의 각 전형의 최종 결과 발표일 기준으로 최근 ```2년``` 이내에 워너비슈퍼추어소프트의 채용 전형에서 부정행위를 한 사람
* 제외된 인원을 뺀 나머지 각 지원자들을 ```1차 코딩 테스트 점수```의 값에 따라서 다음과 같이 그룹화하고, 각 그룹에서 일정 인원을 면접 대상자로 선발한다.
  * ```전설 그룹``` : ```1차 코딩 테스트 점수```가 전체에서 상위 ```10%``` 이내에 드는 인원들의 모임으로, 여기서 ```나머지 총점```을 기준으로 상위 ```5명```을 선발한다.
  * ```신화 그룹``` : ```1차 코딩 테스트 점수```가 전체에서 상위 ```10% ~ 30%```에 드는 인원들의 모임으로, 여기서 ```나머지 총점```을 기준으로 상위 ```5명```을 선발한다.
  * ```에픽 그룹``` : ```1차 코딩 테스트 점수```가 전체에서 상위 ```30% ~ 50%```에 드는 인원들의 모임으로, 여기서 ```나머지 총점```을 기준으로 상위 ```3명```을 선발한다.
  * ```레어 그룹``` : ```1차 코딩 테스트 점수```가 전체에서 상위 ```50% ~ 75%```에 드는 인원들의 모임으로, 여기서 ```나머지 총점```을 기준으로 상위 ```2명```을 선발한다.
  * ```일반 그룹``` : ```1차 코딩 테스트 점수```가 전체에서 하위 ```25%```에 드는 인원들의 모임으로, 여기서는 모든 인원을 탈락시킨다.
  * 상위 비율은 자신을 제외한 나머지 인원 중 ```1차 코딩 테스트 점수```가 자신보다 높은 사람들의 비율로 한다.
  * 각 그룹별로 해당하는 인원 수가 면접 대상자 선발 인원 수 미만일 시에는 해당 그룹 전원을 면접 대상자로 선발한다. 이에 해당하는 그룹이 있는 경우, 면접 대상자가 총 15명 미만이 될 수 있다.
* ```나머지 총점```은 다음과 같이 계산한다.
  * ```(2차 코딩 테스트 점수) * 0.45 + (3차 코딩 테스트 점수) * 0.55```와 ```(2차 코딩 테스트 점수) * 0.3 + (3차 코딩 테스트 점수) * 0.7```중 큰 값 + ```가산점```
  * ```가산점```은 다음과 같이 구한다.
    * 1. 2차 코딩 테스트 및 3차 코딩 테스트 모두 만점을 받은 문제가 각각 ```3문항``` 이상씩 있는 경우 ```50점```
    * 2. a에 해당하지 않지만, 2차 코딩 테스트 및 3차 코딩 테스트 모두 만점을 받은 문제가 각각 ```2문항``` 이상씩 있는 경우 ```25점```
    * 3. a, b에 둘 다 해당하지 않지만 2차 코딩 테스트 및 3차 코딩 테스트에서 만점을 받은 문제 수의 합이 ```3문항``` 이상인 경우 ```10점```
    * 4. a, b, c에 셋 다 해당하지 않는 경우 가산점은 ```0점```이다.
* 선발 대상자 중 ```나머지 총점```의 동점자가 있는 경우, ```지원자 ID```의 값이 작은 지원자를 우선 선발한다. (```지원자 ID```는 지원 순서대로 0, 1, 2, ... 형태로 부여된다.)

아래 데이터 클래스가 주어졌을 때, 이 데이터 클래스를 이용하여 면접 대상자의 ```지원자 ID```와 ```지원자 이름```을 ```지원자 ID```의 오름차순으로 목록으로 반환하는 코드를 작성하시오.

## 데이터 클래스
```kotlin
// 채용 공고
data class Recruitment (
    val recruitment_name: String, // 채용 전형 이름
    val start_date: String, // 서류 지원 시작일 ("yyyy-mm-dd")
    val end_date: String, // 서류 지원 종료일 ("yyyy-mm-dd")
    val finish_date: String, // 최종 결과 발표일 ("yyyy-mm-dd")
    val candidate_list: List<Candidate> // 지원자 목록
)

// 지원자
data class Candidate (
    val id: Int, // 지원자 ID
    val name: String, // 지원자 이름
    val experience: Int, // 경력 (일수)
    val apply_history: List<ApplyHistory>, // 지원 이력
    val first_test_result: CodingTestResult, // 1차 코딩 테스트 결과
    val second_test_result: CodingTestResult, // 2차 코딩 테스트 결과
    val third_test_result: CodingTestResult // 3차 코딩 테스트 결과
)

// 지원 이력
data class ApplyHistory (
    val apply_date: String, // 지원일 ("yyyy-mm-dd")
    val finish_date: String, // 최종 결과 발표일 ("yyyy-mm-dd")
    val result: Int // 최종 결과 (0: 면접 대상 미선발, 1: 면접 불합격, 2: 최종합격, -1: 부정행위)
)

// 코딩 테스트 결과
data class CodingTestResult (
    val time: Int, // 코딩 테스트 응시 소요 시간
    val scores: List<Int> // 각 문제별 점수 (100점 만점)
)
```

## 예제 코드
```kotlin
// 채용 공고
data class Recruitment (
    val recruitment_name: String, // 채용 전형 이름
    val start_date: String, // 서류 지원 시작일 ("yyyy-mm-dd")
    val end_date: String, // 서류 지원 종료일 ("yyyy-mm-dd")
    val finish_date: String, // 최종 결과 발표일 ("yyyy-mm-dd")
    val candidate_list: List<Candidate> // 지원자 목록
)

// 지원자
data class Candidate (
    val id: Int, // 지원자 ID
    val name: String, // 지원자 이름
    val experience: Int, // 경력 (일수)
    val apply_history: List<ApplyHistory>, // 지원 이력
    val first_test_result: CodingTestResult, // 1차 코딩 테스트 결과
    val second_test_result: CodingTestResult, // 2차 코딩 테스트 결과
    val third_test_result: CodingTestResult // 3차 코딩 테스트 결과
)

// 지원 이력
data class ApplyHistory (
    val apply_date: String, // 지원일 ("yyyy-mm-dd")
    val finish_date: String, // 최종 결과 발표일 ("yyyy-mm-dd")
    val result: Int // 최종 결과 (0: 면접 대상 미선발, 1: 면접 불합격, 2: 최종합격, -1: 부정행위)
)

// 코딩 테스트 결과
data class CodingTestResult (
    val time: Int, // 코딩 테스트 응시 소요 시간
    val scores: List<Int> // 각 문제별 점수 (100점 만점)
)

// 1차 코딩 테스트 점수에 의한 등급 계산
fun getClassOfCandidate(candidate: Candidate, candidateList: List<Candidate>): String {
    val firstCodingScore = candidate.first_test_result.scores.sum()
    
    // 본인을 제외하므로 ID가 불일치해야 함
    val otherCandidates = candidateList.filter {
        it.id != candidate.id
    }
    
    val percentFromTop = otherCandidates.filter {
        it.first_test_result.scores.sum() > firstCodingScore
    }.size / otherCandidates.size.toDouble()
    
    val candidateClass = when (percentFromTop) {
        in 0.0..0.1 -> "Legend"
        in 0.1..0.3 -> "Myth"
        in 0.3..0.5 -> "Epic"
        in 0.5..0.75 -> "Rare"
        else -> "Common"
    }
    println("2. (${candidate.id}) ${candidate.name}, ${firstCodingScore}, ${percentFromTop}, ${candidateClass}")

	return candidateClass
}

// 면접 대상자 목록 출력
class Interviewee (
    val id: Int, // 지원자 ID
    val name: String, // 지원자 이름
    val score: Double // 나머지 총점
)

fun getIntervieweeList(recruitment: Recruitment) {
    
    val compareDate: (String, String) -> Int = { s0, s1 ->
        val past = s0.replace("-", "").toInt()
        val current = s1.replace("-", "").toInt()
        current - past
    }
    
    // 경력, 부정행위 이력에 의한 결격 사유자 제외
    val filteredCandidateList = recruitment.candidate_list.filter {
        it.experience >= 180 && it.experience < 1097
    }.filter { it ->
        it.apply_history.filter { ah ->
            compareDate(ah.finish_date, recruitment.finish_date) <= 20000 && ah.result == -1
        }.size == 0
    }
    println("1. ${filteredCandidateList.map { it.name }}")
    
    // 1차 코딩 테스트 결과에 따라 그룹화
    println("")
    val groupByFirstTest = with (filteredCandidateList) {
        this.groupBy {
            getClassOfCandidate(it, this)
        }
    }
    println("")
    groupByFirstTest.forEach {
        println("3. ${it.key}, ${it.value.map { c -> "${c.name}_${c.first_test_result.scores.sum()}" } }")
    }
    
    // 전체 면접 대상자 목록
    val allInterviewees = mutableListOf<Interviewee>()
    
    // 각 그룹별로 나머지 총점 계산 및 면접 대상자 목록 구하기
    println("")
    groupByFirstTest.forEach {
        val interviewees = mutableListOf<Interviewee>()
        
        it.value.forEach { c -> // 각 지원자의 나머지 총점 계산
            val test2Score = c.second_test_result.scores.sum()
            val test3Score = c.third_test_result.scores.sum()
            val test2Perfect = c.second_test_result.scores.filter { it == 100 }.size
            val test3Perfect = c.third_test_result.scores.filter { it == 100 }.size
            
            // 기본 점수 계산
            val base = Math.max(test2Score * 0.45 + test3Score * 0.55,
                                 test2Score * 0.3 + test3Score * 0.7)
            
            // 가산점 계산
            val additional = if (test2Perfect >= 3 && test3Perfect >= 3) {
                50.0
            } else if (test2Perfect >= 2 && test3Perfect >= 2) {
                25.0
            } else if (test2Perfect + test3Perfect >= 3) {
                10.0
            } else {
                0.0
            }
            
            // 최종 점수 계산
            val score = base + additional
            
            println("4. ${c.id}, ${c.name}, ${base}, ${additional}, ${score}")
            
            // 점수 목록에 추가
            interviewees.add(Interviewee(c.id, c.name, score))
        }
        
        println("5. ${it.key}, ${interviewees.map { "${it.id}_${it.name}_${it.score}"}}")
                
        // 지원자를 (나머지 총점 내림차순 -> ID 오름차순) 으로 정렬한 후 지정된 수의 인원만 선발
        // 참고: https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/compare-by.html
        val sortedInterviewees = interviewees
        .sortedWith (
            compareBy (
                { it.score * (-1) }, // 나머지 총점 내림차순
                { it.id } // ID 오름차순
            )
        ).take ( when (it.key) {
            "Legend" -> 5
            "Myth" -> 5
            "Epic" -> 3
            "Rare" -> 2
            else -> 0 // 일반 등급도 0명 선발
        })
        
        println("6. ${it.key}, ${sortedInterviewees.map { "${it.id}_${it.name}_${it.score}"}}\n")
                
        // 선발된 지원자들을 전체 면접 대상자 목록에 포함
        allInterviewees.addAll(sortedInterviewees)
    }
    
    // 전체 면접 대상자를 지원자 ID 오름차순으로 정렬
    val allIntervieweesSorted = allInterviewees.sortedBy { it.id }
    
    // 전체 면접 대상자 목록 출력
    println("\n전체 면접 대상자 목록 :")
    allIntervieweesSorted.forEach {
        println("지원자 ID: ${it.id}, 이름: ${it.name}")
    }
}

fun main() {
    
    // 임시 지원자 목록
    val testCandidateList = listOf<Candidate> (
        Candidate(13, "hong", 615,
                  listOf<ApplyHistory>(
                      ApplyHistory("2020-09-15", "2020-12-01", 0),
                      ApplyHistory("2021-03-16", "2021-06-03", 1),
                      ApplyHistory("2021-09-17", "2021-12-02", 1),
                      ApplyHistory("2022-03-14", "2022-05-31", 0)
                  ),
                  CodingTestResult(7194, listOf(80, 100, 100, 100)),
                  CodingTestResult(7188, listOf(100, 100, 100, 50)),
                  CodingTestResult(7196, listOf(90, 90, 100, 100))
        ),
        Candidate(14, "yoon", 71,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7200, listOf(70, 70, 90, 100)),
                  CodingTestResult(7195, listOf(100, 50, 100, 30)),
                  CodingTestResult(7197, listOf(90, 80, 80, 50))
        ),
        Candidate(15, "min", 1400,
                  listOf<ApplyHistory>(),
                  CodingTestResult(5904, listOf(30, 100, 60, 90)),
                  CodingTestResult(7190, listOf(100, 90, 90, 70)),
                  CodingTestResult(6340, listOf(90, 70, 90, 60))
        ),
        Candidate(16, "jin", 362,
                  listOf<ApplyHistory>(
                      ApplyHistory("2019-09-15", "2019-12-06", -1),
                      ApplyHistory("2021-03-19", "2021-06-03", 1),
                  ),
                  CodingTestResult(7199, listOf(70, 100, 80, 100)),
                  CodingTestResult(7020, listOf(80, 100, 60, 60)),
                  CodingTestResult(7112, listOf(60, 50, 50, 100))
        ),
        Candidate(17, "jeong", 313,
                  listOf<ApplyHistory>(
                      ApplyHistory("2020-09-13", "2020-12-01", 0),
                      ApplyHistory("2022-03-10", "2022-05-31", -1),
                  ),
                  CodingTestResult(7194, listOf(100, 100, 60, 60)),
                  CodingTestResult(7186, listOf(100, 50, 80, 60)),
                  CodingTestResult(7190, listOf(90, 70, 20, 100))
        ),
        Candidate(18, "hee", 710,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7200, listOf(90, 90, 100, 100)),
                  CodingTestResult(7199, listOf(100, 60, 100, 100)),
                  CodingTestResult(7199, listOf(100, 100, 80, 90))
        ),
        Candidate(20, "hwan", 722,
                  listOf<ApplyHistory>(
                      ApplyHistory("2019-09-18", "2019-12-06", 1),
                      ApplyHistory("2021-03-13", "2021-06-03", -1),
                  ),
                  CodingTestResult(7120, listOf(60, 80, 80, 60)),
                  CodingTestResult(3997, listOf(70, 40, 100, 50)),
                  CodingTestResult(5240, listOf(100, 50, 60, 50))
        ),
        Candidate(21, "won", 495,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7128, listOf(70, 80, 80, 100)),
                  CodingTestResult(7140, listOf(100, 40, 50, 100)),
                  CodingTestResult(6512, listOf(100, 100, 70, 20))
        ),
        Candidate(22, "mun", 970,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7200, listOf(90, 70, 80, 100)),
                  CodingTestResult(7190, listOf(100, 90, 90, 0)),
                  CodingTestResult(7125, listOf(100, 90, 100, 80))
        ),
        Candidate(23, "suk", 1095,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7003, listOf(70, 70, 50, 70)),
                  CodingTestResult(7127, listOf(100, 100, 100, 100)),
                  CodingTestResult(7198, listOf(100, 30, 30, 20))
        ),
        Candidate(24, "sung", 182,
                  listOf<ApplyHistory>(),
                  CodingTestResult(6740, listOf(70, 80, 90, 90)),
                  CodingTestResult(7020, listOf(100, 100, 100, 100)),
                  CodingTestResult(7187, listOf(80, 0, 40, 20))
        ),
        Candidate(25, "yoon", 192,
                  listOf<ApplyHistory>(),
                  CodingTestResult(1096, listOf(70, 80, 70, 70)),
                  CodingTestResult(1232, listOf(100, 20, 10, 90)),
                  CodingTestResult(1604, listOf(100, 80, 80, 50))
        ),
        Candidate(26, "sul", 699,
                  listOf<ApplyHistory>(),
                  CodingTestResult(3508, listOf(100, 80, 80, 90)),
                  CodingTestResult(4720, listOf(100, 100, 90, 80)),
                  CodingTestResult(5801, listOf(100, 100, 0, 0))
        ),
        Candidate(27, "do", 900,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7200, listOf(100, 100, 100, 100)),
                  CodingTestResult(7200, listOf(100, 70, 20, 90)),
                  CodingTestResult(7129, listOf(100, 100, 100, 40))
        ),
        Candidate(28, "ji", 191,
                  listOf<ApplyHistory>(),
                  CodingTestResult(5401, listOf(100, 80, 60, 50)),
                  CodingTestResult(6307, listOf(50, 50, 70, 90)),
                  CodingTestResult(7150, listOf(100, 0, 40, 10))
        ),
        Candidate(29, "dong", 1002,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7000, listOf(80, 20, 80, 30)),
                  CodingTestResult(7100, listOf(100, 100, 90, 80)),
                  CodingTestResult(7192, listOf(100, 70, 20, 50))
        ),
        Candidate(30, "hyuk", 1090,
                  listOf<ApplyHistory>(),
                  CodingTestResult(2248, listOf(90, 90, 90, 100)),
                  CodingTestResult(3072, listOf(100, 80, 90, 100)),
                  CodingTestResult(7121, listOf(100, 100, 90, 90))
        ),
        Candidate(31, "mee", 830,
                  listOf<ApplyHistory>(),
                  CodingTestResult(1378, listOf(80, 100, 80, 70)),
                  CodingTestResult(1619, listOf(100, 90, 50, 50)),
                  CodingTestResult(1832, listOf(80, 70, 100, 100))
        ),
        Candidate(32, "myeong", 222,
                  listOf<ApplyHistory>(),
                  CodingTestResult(3064, listOf(60, 90, 90, 90)),
                  CodingTestResult(7029, listOf(100, 100, 100, 100)),
                  CodingTestResult(7121, listOf(100, 100, 50, 20))
        ),
        Candidate(33, "sook", 750,
                  listOf<ApplyHistory>(),
                  CodingTestResult(6454, listOf(100, 90, 70, 60)),
                  CodingTestResult(6700, listOf(100, 60, 60, 60)),
                  CodingTestResult(6886, listOf(100, 100, 100, 100))
        ),
        Candidate(34, "sin", 450,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7190, listOf(80, 80, 80, 80)),
                  CodingTestResult(7195, listOf(90, 90, 90, 90)),
                  CodingTestResult(7197, listOf(100, 100, 90, 90))
        ),
        Candidate(35, "cos", 365,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7160, listOf(70, 80, 90, 100)),
                  CodingTestResult(7199, listOf(100, 100, 100, 10)),
                  CodingTestResult(7199, listOf(100, 20, 100, 100))
        ),
        Candidate(36, "tan", 1096,
                  listOf<ApplyHistory>(),
                  CodingTestResult(7117, listOf(80, 70, 100, 90)),
                  CodingTestResult(7145, listOf(100, 20, 20, 100)),
                  CodingTestResult(7127, listOf(100, 100, 30, 30))
        ),
        Candidate(37, "sec", 367,
                  listOf<ApplyHistory>(),
                  CodingTestResult(4022, listOf(100, 20, 90, 100)),
                  CodingTestResult(4567, listOf(100, 100, 100, 50)),
                  CodingTestResult(5694, listOf(90, 80, 60, 70))
        ),
        Candidate(40, "csc", 270,
                  listOf<ApplyHistory>(),
                  CodingTestResult(875, listOf(90, 80, 60, 80)),
                  CodingTestResult(938, listOf(70, 90, 70, 100)),
                  CodingTestResult(2789, listOf(80, 100, 60, 80))
        ),
    )
    
    // 임시 채용 공고
    val tempRecruitment = Recruitment (
        "2022년 하반기 신입 개발자 채용",
        "2022-09-01", "2022-09-21", "2022-11-24", testCandidateList
    )
    
    // 임시 채용 공고에 대한 면접 대상자 목록 출력
    getIntervieweeList(tempRecruitment)
}
```

## 예제 출력
```kotlin
1. [hong, jin, hee, won, mun, suk, sung, yoon, sul, do, ji, dong, hyuk, mee, myeong, sook, sin, cos, tan, sec, csc]

2. (13) hong, 380, 0.05, Legend
2. (16) jin, 350, 0.2, Myth
2. (18) hee, 380, 0.05, Legend
2. (21) won, 330, 0.45, Epic
2. (22) mun, 340, 0.3, Myth
2. (23) suk, 260, 0.95, Common
2. (24) sung, 330, 0.45, Epic
2. (25) yoon, 290, 0.85, Common
2. (26) sul, 350, 0.2, Myth
2. (27) do, 400, 0.0, Legend
2. (28) ji, 290, 0.85, Common
2. (29) dong, 210, 1.0, Common
2. (30) hyuk, 370, 0.15, Myth
2. (31) mee, 330, 0.45, Epic
2. (32) myeong, 330, 0.45, Epic
2. (33) sook, 320, 0.65, Rare
2. (34) sin, 320, 0.65, Rare
2. (35) cos, 340, 0.3, Myth
2. (36) tan, 340, 0.3, Myth
2. (37) sec, 310, 0.75, Rare
2. (40) csc, 310, 0.75, Rare

3. Legend, [hong_380, hee_380, do_400]
3. Myth, [jin_350, mun_340, sul_350, hyuk_370, cos_340, tan_340]
3. Epic, [won_330, sung_330, mee_330, myeong_330]
3. Common, [suk_260, yoon_290, ji_290, dong_210]
3. Rare, [sook_320, sin_320, sec_310, csc_310]

4. 13, hong, 371.0, 25.0, 396.0
4. 18, hee, 367.0, 25.0, 392.0
4. 27, do, 322.0, 10.0, 332.0
5. Legend, [13_hong_396.0, 18_hee_392.0, 27_do_332.0]
6. Legend, [13_hong_396.0, 18_hee_392.0, 27_do_332.0]

4. 16, jin, 278.0, 0.0, 278.0
4. 22, mun, 343.0, 10.0, 353.0
4. 26, sul, 276.5, 25.0, 301.5
4. 30, hyuk, 377.0, 25.0, 402.0
4. 35, cos, 317.0, 50.0, 367.0
4. 36, tan, 254.0, 25.0, 279.0
5. Myth, [16_jin_278.0, 22_mun_353.0, 26_sul_301.5, 30_hyuk_402.0, 35_cos_367.0, 36_tan_279.0]
6. Myth, [30_hyuk_402.0, 35_cos_367.0, 22_mun_353.0, 26_sul_301.5, 36_tan_279.0]

4. 21, won, 290.0, 25.0, 315.0
4. 24, sung, 257.0, 10.0, 267.0
4. 31, mee, 332.0, 10.0, 342.0
4. 32, myeong, 328.5, 25.0, 353.5
5. Epic, [21_won_315.0, 24_sung_267.0, 31_mee_342.0, 32_myeong_353.5]
6. Epic, [32_myeong_353.5, 31_mee_342.0, 21_won_315.0]

4. 23, suk, 279.0, 10.0, 289.0
4. 25, yoon, 283.0, 0.0, 283.0
4. 28, ji, 199.5, 0.0, 199.5
4. 29, dong, 298.5, 10.0, 308.5
5. Common, [23_suk_289.0, 25_yoon_283.0, 28_ji_199.5, 29_dong_308.5]
6. Common, []

4. 33, sook, 364.0, 10.0, 374.0
4. 34, sin, 374.0, 0.0, 374.0
4. 37, sec, 322.5, 10.0, 332.5
4. 40, csc, 324.5, 0.0, 324.5
5. Rare, [33_sook_374.0, 34_sin_374.0, 37_sec_332.5, 40_csc_324.5]
6. Rare, [33_sook_374.0, 34_sin_374.0]


전체 면접 대상자 목록 :
지원자 ID: 13, 이름: hong
지원자 ID: 18, 이름: hee
지원자 ID: 21, 이름: won
지원자 ID: 22, 이름: mun
지원자 ID: 26, 이름: sul
지원자 ID: 27, 이름: do
지원자 ID: 30, 이름: hyuk
지원자 ID: 31, 이름: mee
지원자 ID: 32, 이름: myeong
지원자 ID: 33, 이름: sook
지원자 ID: 34, 이름: sin
지원자 ID: 35, 이름: cos
지원자 ID: 36, 이름: tan
```
