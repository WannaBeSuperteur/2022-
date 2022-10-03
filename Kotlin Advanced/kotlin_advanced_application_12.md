## 요구사항 분석
워너비슈퍼추어소프트에서는 인공지능을 통한 감정 분석 소프트웨어를 개발하기 위하여, 각 인공지능 모델의 성능을 측정하여 분석하려고 한다. 이를 위해 다음과 같은 데이터를 필요로 한다.
* 각 모델의 입력층(input layer)을 구성하는 입력 데이터의 구성
  * 입력 데이터는 여러 개의 속성(attribute)으로 구성된다.
    * 각 속성별로 다음과 같은 정보가 있다.
      * 해당 속성의 type (숫자 값, 문자열, 참/거짓, 분류)
      * 해당 속성의 처리 방법 (0~1 표준화, 정규분포 표준화, 원래 값 그대로, one-hot, 0 또는 1로 변환, 토큰화)
* 출력 데이터는 각 감정을 텍스트로 나타낸 값으로 고정되므로 분석에 불필요하다.
* 하이퍼 파라미터 (epoch 개수, learning rate, dropout)
* 성능 지표 (예측 정확도)

분석하여 표시해야 하는 것 다음과 같다.
* 각 모델의 입력 데이터의 전체 속성의 개수의 범위별 성능 지표의 최대, 최소, 평균값
* 각 모델의 입력 데이터의 숫자 값에 해당하는 속성의 개수에 따른 성능 지표의 최대, 최소, 평균값
* 각 모델의 입력 데이터의 숫자 값에 해당하는 속성의 처리 방법 중 적용된 속성의 개수가 가장 많은 처리 방법이 무엇인지에 따른 성능 지표의 최대, 최소, 평균값
  * 입력 데이터에 숫자 값에 해당하는 속성이 있는 모델만을 대상으로 한다.
  * 적용된 속성의 개수가 가장 많은 처리 방법이 여러 개인 경우, ```0~1 표준화```, ```정규분포 표준화```, ```원래 값 그대로``` 순으로 우선순위를 둔다. (이외의 처리 방법은 숫자 값에 해당하는 속성에서 사용하지 않음)
* 각 모델의 입력 데이터의 문자열에 해당하는 속성의 개수에 따른 성능 지표의 최대, 최소, 평균값
* 각 모델의 하이퍼 파라미터 3종류 (epoch 개수, learning rate, dropout) 각각의 값이 평균 이상인지, 그 미만인지에 따른 성능 지표의 최대, 최소, 평균값
  * 예를 들어, ```epoch 개수```가 평균 이상, ```learning rate```가 평균 미만, ```dropout```이 평균 이상인 모델들의 성능 지표의 최대, 최소, 평균값을 계산하며, 이런 식으로 각각이 평균 이상인지의 여부에 따라 계산한다.

### 데이터 클래스
```kotlin
// 모델
data class Model (
    val name: String, // 모델 이름
    val ID: Int, // 모델 ID
    val input_data: InputData, // 입력 데이터 구성
    val hyper_parameters: HyperParams, // 하이퍼파라미터
    val accuracy: Double // 성능 지표 (정확도)
)

// 입력 데이터
data class InputData (
    val attributes: List<Attribute> // 입력 데이터의 속성들
)

// 입력 데이터를 구성하는 각 속성
data class Attribute (
    val type: String, // 속성 종류 (숫자 값: n, 문자열: s, 참/거짓: tf, 분류: c)
    val handle_method: String // 처리 방법 (0~1 표준화: nzo, 정규분포 표준화: nn, 원래 값 그대로: o, one-hot: oh, 0 또는 1로 변환: zo, 토큰화: t)
)

// 하이퍼파라미터 정보
data class HyperParams (
    val epochs: Int, // epoch 개수
    val learning_rate: Double, // learning rate
    val dropout: Double // dropout
)
```
