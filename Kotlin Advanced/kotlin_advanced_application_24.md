## 요구사항 분석
주어진 데이터의 왜도(skew)와 첨도(kurtosis)를 계산하여 각각 출력하시오. 단, 정규분포의 왜도는 0, 첨도는 3이다.

### 데이터 클래스
// 자료
data class Data (
    val values: List<Int> // 값들의 목록
)
