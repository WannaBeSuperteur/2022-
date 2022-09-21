## 요구 사항 분석
워너비슈퍼추어소프트에서는 재직 중인 개발자들의 MBTI에 따른 능력치를 통계 내려고 한다.

* MBTI별로 다음을 구하는 프로그램을 작성하시오.
  * MBTI가 ```E```와 ```I```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * MBTI가 ```N```과 ```S```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * MBTI가 ```T```와 ```F```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * MBTI가 ```P```와 ```J```인 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * 16가지 MBTI 유형 각각의 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * ```ST``` 유형과 ```TJ``` 유형 각각의 직원들의 ```전체 정보```의 각 항목의 최대, 최소, 평균, 중앙값
  * ```근태 및 성실성``` 점수와 ```주당 근로시간```의 값 각각이 가장 큰 MBTI 유형
  * ```근태 및 성실성``` 점수와 ```주당 근로시간``` 간, 전체 유형 및 ```ISTJ``` 유형 각각의 상관계수
  * ```총평 등급``` 점수와 ```주당 근로시간``` 간, 전체 유형 및 ```ISTJ``` 유형 각각의 상관계수
  * ```협업 및 커뮤니케이션 능력``` 점수와 ```주당 근로시간``` 간, 전체 유형 및 ```ISTJ``` 유형 각각의 상관계수
* ```전체 정보```는 ```영역별 점수```와 ```주당 근로시간```, ```총평 등급```으로 나누어진다.
  * ```영역별 점수```는 ```기본 업무 능력```, ```기술력```, ```근태 및 성실성```, ```협업 및 커뮤니케이션 능력```의 4가지로 나누어진다.
  * ```주당 근로시간```은 ```시간``` 단위로 나타낸다.
  * ```총평 등급```은 ```S```부터 ```F```까지이며, 높은 순서대로 ```S```, ```A```, ```B```, ```C```, ```F```이다.
    * 이때, ```S```는 10점, ```A```는 9점, ```B```는 8점, ```C```는 7점, ```F```는 0점으로 계산한다. 

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
data class Developer (
    val name: String, // 개발자 이름
    val ID: Int, // 개발자의 고유 ID
    val MBTI: String, // MBTI
    val total_info: TotalInfo // 전체 정보
)

// 전체 정보
data class TotalInfo (
    val scores: Scores, // 영역별 점수
    val working_hours: Int, // 주당 근로시간
    val rating: Char // 등급
)

// 영역별 점수
data class Scores (
    val basic: Int, // 기본 업무 능력
    val skill: Int, // 기술력
    val sincerity: Int, // 근태 및 성실성
    val co_working: Int // 협업 및 커뮤니케이션 능력
)
```

### 상관계수 계산 함수
상관계수를 계산하는 함수는 다음과 같다.
```kotlin
// 상관계수 계산하기 (단, first와 second의 항목 개수는 서로 같음)
fun getCorrCoef(first: List<Double>, second: List<Double>): Double {
    
    // 항목이 1개이면 계산 불가능
    val n = first.size
    if (n == 1) {
        return 0.0
    }

    val avgFirst = first.sum() / n
    val avgSecond = second.sum() / n
    
    val sumFirstSecond = (0..n-1).sumOf { it ->
        (first[it] - avgFirst) * (second[it] - avgSecond)
    }
    val sumFirstSqaure = (0..n-1).sumOf { it ->
        (first[it] - avgFirst) * (first[it] - avgFirst)
    }
    val sumSecondSqaure = (0..n-1).sumOf { it ->
        (second[it] - avgSecond) * (second[it] - avgSecond)
    }
    
    val cov = sumFirstSecond / (n - 1)
    val varFirst = sumFirstSqaure / (n - 1)
    val varSecond = sumSecondSqaure / (n - 1)
    
    return cov / Math.sqrt(varFirst * varSecond)
}
```
