## 요구사항 분석
다음과 같은 구조의 간단한 인공신경망에 값을 입력하여, 출력되는 값을 구하려고 한다.
```kotlin
[ input (30    ) ]
        V          <--- convolution with 2 filters A1, A2 (each 1 x 3 matrix)
[ Conv1 (28 x 2) ]
        |          <--- leakyReLU(0.05)
        V          <--- max pooling
[ Pool1 (14 x 2) ]
        V          <--- convolution with 4 filters B1, B2, B3, B4 (each 1 x 3 matrix)
[ Conv2 (12 x 4) ]
        |          <--- leakyReLU(0.1)
        V          <--- average pooling
[ Pool2 ( 6 x 4) ]
        V          <--- convolution with 4 filters C1, C2, C3, C4 (each 1 x 3 matrix)
[ Conv3 ( 4 x 4) ]
        V          <--- flatten
[ flat  (16    ) ] <--- row vector
        |          <--- sigmoid
        V          <--- with 16 x 4 matrix
[ dense ( 4    ) ] <--- row vector
        |          <--- tanh
        V          <--- with 4 x 1 matrix
[ out   ( 1    ) ]
```

데이터 클래스가 다음과 같을 때, 위 신경망의 출력값을 구하시오.

### 데이터 클래스
```kotlin
// 신경망 출력에 필요한 데이터
data class NeuralNetworkTest (
    val input_data: List<Double>, // 입력 데이터
    val filters_a: List<Filter>, // 필터 A1, A2
    val filters_b: List<Filter>, // 필터 B1, B2, B3, B4
    val filters_c: List<Filter>, // 필터 C1, C2, C3, C4
    val matrix_0: List<List<Double>>, // 16 x 4 행렬 (flat -> dense)
    val matrix_1: List<Double> // 4 x 1 행렬로 변환 가능한 리스트 (dense -> out)
)

// 필터
data class Filter (
    val filter_matrix: List<Double> // 필터의 행렬
)
```
