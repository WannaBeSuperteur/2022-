## 요구사항 분석
워너비슈퍼추어대학교에서는 학생의 성적 관리 시스템을 개편하여, 서버에서 반환하는 정보를 통해 한번에 특정 학기의 어떤 학생의 평점 평균을 계산할 수 있도록 하였다. 그 정보는 다음과 같다.
* 학생의 각 과목별 각각의 평가 요소들의 ```성적```, ```만점``` 및 ```가중치```
  * 과목별 총점은 이 정보를 이용하여 도출하며, ```성적 * 가중치```의 평가 요소별 합계를 ```만점 * 가중치```의 평가 요소별 합계로 나눈 비율이다.
* 과목별 성적 산출 방법의 상대평가, 절대평가 여부
  * 상대평가 시, 총점 상위 ```7%```까지 ```S```, 상위 ```20%```까지 ```A```, 상위 ```40%```까지 ```B```, 상위 ```60%```까지 ```C```, 상위 ```90%```까지 ```D```를 부여하고, ```하위 10%```에게는 ```F```를 부여한다.
    * 학생의 성적의 상위 비율 계산은 같은 과목을 수강한 학생들의 점수 목록에서 자신의 총점보다 점수가 높은 학생의 비율로 계산한다. 즉 자신의 총점이 가장 높으면 (동점자가 있는 경우를 포함하여) 상위 0%가 된다.
  * 절대평가 시, 총점 ```90%``` 이상은 ```S```, ```80%``` 이상 ```90%``` 미만은 ```A```, ```70%``` 이상 ```80%``` 미만은 ```B```, ```65%``` 이상 ```70%``` 미만은 ```C```, ```60%``` 이상 ```65%``` 미만은 ```D```, ```60%``` 미만은 ```F```를 부여한다.
* 등급별 평점은 ```S```, ```A```, ```B```, ```C```, ```D```, ```F```가 각각 ```4.5점```, ```3.75점```, ```3.0점```, ```2.5점```, ```2.0점```, ```0.0점```이다.

### 데이터 클래스
```kotlin
// API 호출 로그 목록
data class Student (
    val student_id: Long, // 학번
    val name: String, // 학생 이름
    val subject_list: List<SubjectInfo> // 수강 과목 리스트
)

// 학생의 수강 과목
data class SubjectInfo (
    val subject_id: Int, // 과목코드 (학수번호)
    val name: String, // 과목명
    val is_absolute: Boolean, // 절대평가 여부 (true: 절대평가, false: 상대평가)
    val credit: Int, // 학점
    val evaluation_criteria: List<EvaluationCriterion>, // 평가 요소 목록
    val scores: List<Double> // 같은 과목을 수강한 학생들의 점수 목록
)

// 평가 요소
data class EvaluationCriterion (
    val name: String, // 평가요소 이름
    val weight: Int, // 평가 요소 가중치
    val score: Int, // 평가 요소 성적
    val highest_score: Int // 평가 요소의 만점
)
```

## 예제 코드
```kotlin
// API 호출 로그 목록
data class Student (
    val student_id: Long, // 학번
    val name: String, // 학생 이름
    val subject_list: List<SubjectInfo> // 수강 과목 리스트
)

// 학생의 수강 과목
data class SubjectInfo (
    val subject_id: Int, // 과목코드 (학수번호)
    val name: String, // 과목명
    val is_absolute: Boolean, // 절대평가 여부 (true: 절대평가, false: 상대평가)
    val credit: Int, // 학점
    val evaluation_criteria: List<EvaluationCriterion>, // 평가 요소 목록
    val scores: List<Double> // 같은 과목을 수강한 학생들의 점수 목록
)

// 평가 요소
data class EvaluationCriterion (
    val name: String, // 평가요소 이름
    val weight: Int, // 평가 요소 가중치
    val score: Int, // 평가 요소 성적
    val highest_score: Int // 평가 요소의 만점
)

// 과목별 평점 계산
fun computeGradeOfSubject(subjectInfo: SubjectInfo): Double {
    
    // 본인 점수 계산
    val myScore = subjectInfo.evaluation_criteria.sumOf {
        it.weight * it.score
    }
    val totalScore = subjectInfo.evaluation_criteria.sumOf {
        it.weight * it.highest_score
    }
    val myScoreRatio = myScore / totalScore.toDouble()
    
    println("\n1. (${subjectInfo.subject_id}) ${myScoreRatio}")
    
    // 절대평가인 경우 : 점수 계산 및 점수에 따라 반환
    if (subjectInfo.is_absolute) {
        val finalScore = when (myScoreRatio) {
            in 0.9..1.0 -> 4.5
            in 0.8..0.9 -> 3.75
            in 0.7..0.8 -> 3.0
            in 0.65..0.7 -> 2.5
            in 0.6..0.65 -> 2.0
            else -> 0.0
        }
        
        println("2. (${subjectInfo.subject_id}) ${finalScore}")
        return finalScore
    }
    
    // 상대평가인 경우 : 순위 계산 및 순위에 따라 반환
    else {
        val rankRatio = subjectInfo.scores.filter {
            it > myScoreRatio
        }.size / subjectInfo.scores.size.toDouble()
        
        println("3. (${subjectInfo.subject_id}) ${rankRatio}")
        
        val finalScore = when (rankRatio) {
            in 0.0..0.07 -> 4.5
            in 0.07..0.2 -> 3.75
            in 0.2..0.4 -> 3.0
            in 0.4..0.6 -> 2.5
            in 0.6..0.9 -> 2.0
            else -> 0.0
        }
        
        println("4. (${subjectInfo.subject_id}) ${finalScore}")
        return finalScore
    }
}

// 평점 계산
fun computeGPA(student: Student): Double {
    val totalGrade = student.subject_list.sumOf {
        it.credit * computeGradeOfSubject(it)
    }
    val totalCredits = student.subject_list.sumOf { it.credit }.toDouble()
    
    println("\n5. ${totalGrade}")
    println("6. ${totalCredits}")
    
    return totalGrade / totalCredits
}

fun main() {
    
    // 임시 학생 정보
    val tempStudent = Student (
        2020012345,
        "hong",
        listOf<SubjectInfo> (
            SubjectInfo (
                10240, "코틀린 프로그래밍", false, 3,
                listOf<EvaluationCriterion> (
                    EvaluationCriterion("출석", 10, 100, 100),
                    EvaluationCriterion("과제 및 실습", 20, 90, 100),
                    EvaluationCriterion("중간고사", 35, 87, 100),
                    EvaluationCriterion("기말고사", 35, 79, 100)
                ),
                listOf (0.99, 0.75, 0.86, 0.9, 0.92, 0.95, 0.95, 0.77, 0.68, 0.62)
            ),
            SubjectInfo (
                10257, "실무 코딩 역량 강화", false, 3,
                listOf<EvaluationCriterion> (
                    EvaluationCriterion("출석", 10, 100, 100),
                    EvaluationCriterion("중간고사", 10, 75, 100),
                    EvaluationCriterion("기말고사", 20, 90, 100),
                    EvaluationCriterion("실무 프로젝트", 60, 92, 100)
                ),
                listOf (0.72, 0.75, 0.8, 0.83, 0.7, 0.92, 0.95, 0.96, 0.68, 0.72, 0.75, 0.88, 0.85, 0.81, 0.87)
            ),
            SubjectInfo (
                10261, "코딩 테스트 실전", true, 3,
                listOf<EvaluationCriterion> (
                    EvaluationCriterion("과제 및 실습", 20, 95, 100),
                    EvaluationCriterion("퀴즈: 1차 코딩 테스트 모의고사", 5, 80, 100),
                    EvaluationCriterion("1차 코딩 테스트", 20, 56, 100),
                    EvaluationCriterion("2차 코딩 테스트", 25, 70, 100),
                    EvaluationCriterion("파이널 코딩 테스트", 30, 89, 100)
                ),
                listOf (0.64, 0.72, 0.59, 0.96, 0.85)
            ),
            SubjectInfo (
                10262, "고급 알고리즘", false, 4,
                listOf<EvaluationCriterion> (
                    EvaluationCriterion("출석", 10, 95, 100),
                    EvaluationCriterion("과제", 30, 92, 100),
                    EvaluationCriterion("중간고사", 30, 79, 100),
                    EvaluationCriterion("기말고사", 30, 76, 100)
                ),
                listOf (0.28, 0.72, 0.56, 0.79, 0.91, 0.47, 0.49, 0.96, 0.95, 0.8, 0.73, 0.75)
            ),
            SubjectInfo (
                15210, "실무 협업 프로젝트", true, 2,
                listOf<EvaluationCriterion> (
                    EvaluationCriterion("출석", 30, 95, 100),
                    EvaluationCriterion("프로젝트", 70, 98, 100)
                ),
                listOf (0.93, 0.94, 0.97, 0.99, 0.97, 0.97, 0.96, 0.81, 0.85, 0.9, 0.88)
            ),
            SubjectInfo (
                20027, "인공지능 및 딥러닝 실전", false, 3,
                listOf<EvaluationCriterion> (
                    EvaluationCriterion("출석", 5, 100, 100),
                    EvaluationCriterion("중간고사", 25, 80, 100),
                    EvaluationCriterion("기말고사", 30, 92, 100),
                    EvaluationCriterion("종합 프로젝트", 40, 88, 100)
                ),
                listOf (0.71, 0.79, 0.66, 0.65, 0.8, 0.82, 0.81, 0.9, 0.75, 0.76, 0.57, 0.6, 0.92, 0.95, 0.88, 0.45, 0.72, 0.78, 0.52, 0.93)
            )
        )
    )
    
    // 평점 출력
    println("\nGPA of ${tempStudent.name} : ${computeGPA(tempStudent)}")
}
```

## 예제 출력
```kotlin
1. (10240) 0.861
3. (10240) 0.5
4. (10240) 2.5

1. (10257) 0.907
3. (10257) 0.2
4. (10257) 3.75

1. (10261) 0.784
2. (10261) 3.0

1. (10262) 0.836
3. (10262) 0.25
4. (10262) 3.0

1. (15210) 0.971
2. (15210) 4.5

1. (20027) 0.878
3. (20027) 0.25
4. (20027) 3.0

5. 57.75
6. 18.0

GPA of hong : 3.2083333333333335
```

## 참고 사항
```kotlin
// 상대평가인 경우 : 순위 계산 및 순위에 따라 반환
else {
    val rankRatio = subjectInfo.scores.filter {
        it > myScoreRatio
    }.size / subjectInfo.scores.size.toDouble()
```
을
```kotlin
// 상대평가인 경우 : 순위 계산 및 순위에 따라 반환
else {
    val rankRatio = subjectInfo.scores.count {
        it > myScoreRatio
    } / subjectInfo.scores.size.toDouble()
```
로 바꿔도 동일한 결과가 출력된다.
