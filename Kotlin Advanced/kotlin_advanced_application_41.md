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

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// 신경망 출력에 필요한 데이터
data class NeuralNetworkTest (
    val input_data: List<Double>, // 입력 데이터
    val filters_a: List<Filter>, // 필터 A1, A2
    val filters_b: List<Filter>, // 필터 B1, B2, B3, B4
    val filters_c: List<Filter>, // 필터 C1, C2, C3, C4
    val matrix_0: List<List<Double>>, // 16 x 4 행렬 (flat -> dense)
    val matrix_1: List<Double> // 4 x 1 행렬 (dense -> out)
)

// 필터
data class Filter (
    val filter_matrix: List<Double> // 필터의 행렬
)

// Conv 계산
fun computeConv(
    outputSize: Int, filterSize: Int, input: List<Double>, filterList: List<Filter>
): List<List<Double>> {
    val filters = filterList.size
    
    return (0 until filters).map { filterNo ->
        (0 until outputSize).map { i0 ->
            (0 until filterSize).sumOf { i1 ->
                input[i0 + i1] * filterList[filterNo].filter_matrix[i1]
            }
        }
    }
}

// Leaky ReLU 계산
fun computeLeakyReLU(arr: List<Double>, slope: Double): List<Double> {
    return arr.map {
        Math.max(it * slope, it)
    }
}

// sigmoid 계산
fun sigmoid(x: Double): Double {
    return 1.0 / (1.0 + exp(-x))
}

// 소수점 이하 4째 자리까지 반올림하여 출력
fun printRounded(arr: List<Double>) {
    println(arr.map { round(it * 10000.0) / 10000.0})
}

// 행렬의 곱셈 (arr0 : 1 * a, arr1 : a * b -> 결과: 1 * b)
fun computeMatMul(arr0: List<Double>, arr1: List<List<Double>>): List<Double> {
    val a = arr1.size
    val b = arr1[0].size
    
    return (0 until b).map { idx_b ->
        (0 until a).sumOf { idx_a ->
            arr0[idx_a] * arr1[idx_a][idx_b]
        }
    }
}

// 신경망의 출력값 계산
fun computeFinalOutput(nn: NeuralNetworkTest) {
    
    // Conv1 계산
    val conv1 = computeConv(28, 3, nn.input_data, nn.filters_a)
    println("1. Conv1 결과:")
    conv1.forEach {
        printRounded(it)
    }
    
    // Conv1의 leaky ReLU 계산
    val conv1L = conv1.map {
        computeLeakyReLU(it, 0.05)
    }
    println("\n2. Conv1 Leaky ReLU 결과:")
    conv1L.forEach {
        printRounded(it)
    }
    
    // Pool1 계산 (max pooling)
    val pool1 = (0..1).map { filterNo ->
        (0..13).map {
            Math.max(conv1L[filterNo][2 * it], conv1L[filterNo][2 * it + 1])
        }
    }
    println("\n3. Pool1 결과 :")
    pool1.forEach {
        printRounded(it)
    }
    
    // Pool1의 모든 feature map을 합산
    val pool1Sum = (0..13).map {
        pool1[0][it] + pool1[1][it]
    }
    println("\n4. Pool1 결과의 합산 :")
    printRounded(pool1Sum)
    
    // Conv2 계산
    val conv2 = computeConv(12, 3, pool1Sum, nn.filters_b)
    println("\n5. Conv2 결과:")
    conv2.forEach {
        printRounded(it)
    }
    
    // Conv2의 leaky ReLU 계산
    val conv2L = conv2.map {
        computeLeakyReLU(it, 0.1)
    }
    println("\n6. Conv2 Leaky ReLU 결과:")
    conv2L.forEach {
        printRounded(it)
    }
    
    // Pool2 계산 (average pooling)
    val pool2 = (0..3).map { filterNo ->
        (0..5).map {
            (conv2L[filterNo][2 * it] + conv2L[filterNo][2 * it + 1]) / 2.0
        }
    }
    println("\n7. pool2 결과 :")
    pool2.forEach {
        printRounded(it)
    }
    
    // Pool2의 모든 feature map을 합산
    val pool2Sum = (0..5).map { idx ->
        pool2.sumOf { it[idx] } // = pool2[0][idx] + ... + pool2[3][idx]
    }
    println("\n8. Pool2 결과의 합산 :")
    printRounded(pool2Sum)
    
    // Conv3 계산
    val conv3 = computeConv(4, 3, pool2Sum, nn.filters_c)
    println("\n9. Conv3 결과:")
    conv3.forEach {
        printRounded(it)
    }
    
    // flat 계산
    val flat = conv3.flatMap { it }
    println("\n10. flat 결과:")
    printRounded(flat)
    
    // flat의 sigmoid 계산
    val flatSigmoid = flat.map { sigmoid(it) }
    println("\n11. flat의 sigmoid 결과:")
    printRounded(flatSigmoid)
    
    // dense 계산
    val dense = computeMatMul(flatSigmoid, nn.matrix_0)
    println("\n12. dense 결과:")
    printRounded(dense)
    
    // dense의 tanh 계산
    val denseTanh = dense.map { tanh(it) }
    println("\n13. dense의 tanh 결과:")
    printRounded(denseTanh)
    
    // out 계산
    val output = computeMatMul(denseTanh, nn.matrix_1.map { listOf(it) })
    println("\n14. out 결과: ${output[0]}, 행렬_1: ${nn.matrix_1}")
    
    // out의 tanh 계산
    val outputTanh = tanh(output[0])
    println("\n15. out의 tanh 결과: ${outputTanh}")
}

fun main() {
    
    // 신경망 출력용 임시 데이터
    val tempNeuralNetworkTest = NeuralNetworkTest (
        listOf(
            0.92, 0.65, 0.47, 0.44, 0.35, 0.36, 0.18, 0.1, 0.05, 0.27,
            0.45, 0.46, 0.72, 0.73, 0.89, 0.97, 0.91, 0.66, 0.73, 0.7,
            0.71, 0.77, 0.7, 0.75, 0.73, 0.61, 0.5, 0.41, 0.4, 0.47
        ),
        listOf(
            Filter(listOf(0.37, -0.79, 0.18)),
            Filter(listOf(0.13, 0.25, -0.4))
        ),
        listOf(
            Filter(listOf(0.28, -0.79, 0.33)),
            Filter(listOf(0.21, -0.44, -0.9)),
            Filter(listOf(-0.6, 0.2, 1.05)),
            Filter(listOf(0.13, 0.56, 1.22))
        ),
        listOf(
            Filter(listOf(-1.01, 0.9, 0.24)),
            Filter(listOf(-1.0, -0.65, 0.92)),
            Filter(listOf(-0.3, 0.97, -0.76)),
            Filter(listOf(0.11, 1.25, 0.44))
        ),
        listOf(
            listOf(-0.37, 0.1, -0.79, 0.29),
            listOf(0.12, 0.13, 0.45, 0.13),
            listOf(0.05, 0.84, 0.4, 0.37),
            listOf(0.72, -0.8, 0.27, -0.85),
            listOf(-0.58, -0.5, -0.18, -0.77),
            listOf(-0.14, 0.03, 0.6, -0.12),
            listOf(-0.78, -0.02, 0.41, 0.18),
            listOf(-1.12, -0.19, 0.27, 0.23),
            listOf(0.6, 0.2, 0.21, 0.22),
            listOf(1.04, 0.13, -0.66, -0.2),
            listOf(0.01, 0.12, 0.45, 0.6),
            listOf(-0.02, 1.18, 0.74, -0.91),
            listOf(-0.17, -1.01, 0.11, 0.44),
            listOf(-0.12, -0.97, -0.28, 0.45),
            listOf(0.36, 0.3, 0.2, -0.11),
            listOf(0.49, 0.15, 0.13, -0.71),
        ),
        listOf(0.78, -0.39, 0.66, 0.12)
    )
    
    // 출력 계산
    computeFinalOutput(tempNeuralNetworkTest)
}
```

### 실행 결과
```kotlin
1. Conv1 결과:
[-0.0885, -0.0516, -0.1107, -0.0489, -0.1225, 0.009, -0.0034, 0.0461, -0.1138, -0.1728, -0.0673, -0.2672, -0.1501, -0.2584, -0.2732, -0.2412, -0.0533, -0.2065, -0.1551, -0.1633, -0.2196, -0.1331, -0.2021, -0.1894, -0.1218, -0.0955, -0.0669, -0.0797]
[0.0941, 0.026, 0.0311, 7.0E-4, 0.0635, 0.0518, 0.0284, -0.0825, -0.106, -0.0364, -0.1145, -0.0522, -0.0799, -0.0706, -0.0058, 0.0896, -0.0087, -0.0117, -0.0141, -0.0395, 0.0048, -0.0249, -0.0135, 0.036, 0.0474, 0.0403, 0.0075, -0.0347]

2. Conv1 Leaky ReLU 결과:
[-0.0044, -0.0026, -0.0055, -0.0024, -0.0061, 0.009, -2.0E-4, 0.0461, -0.0057, -0.0086, -0.0034, -0.0134, -0.0075, -0.0129, -0.0137, -0.0121, -0.0027, -0.0103, -0.0078, -0.0082, -0.011, -0.0067, -0.0101, -0.0095, -0.0061, -0.0048, -0.0033, -0.004]
[0.0941, 0.026, 0.0311, 7.0E-4, 0.0635, 0.0518, 0.0284, -0.0041, -0.0053, -0.0018, -0.0057, -0.0026, -0.004, -0.0035, -3.0E-4, 0.0896, -4.0E-4, -6.0E-4, -7.0E-4, -0.002, 0.0048, -0.0012, -7.0E-4, 0.036, 0.0474, 0.0403, 0.0075, -0.0017]

3. Pool1 결과 :
[-0.0026, -0.0024, 0.009, 0.0461, -0.0057, -0.0034, -0.0075, -0.0121, -0.0027, -0.0078, -0.0067, -0.0095, -0.0048, -0.0033]
[0.0941, 0.0311, 0.0635, 0.0284, -0.0018, -0.0026, -0.0035, 0.0896, -4.0E-4, -7.0E-4, 0.0048, 0.036, 0.0474, 0.0075]

4. Pool1 결과의 합산 :
[0.0915, 0.0287, 0.0725, 0.0745, -0.0075, -0.006, -0.011, 0.0775, -0.0031, -0.0085, -0.0019, 0.0265, 0.0426, 0.0042]

5. Conv2 결과:
[0.0269, -0.0247, -0.041, 0.0248, -0.001, 0.0326, -0.0654, 0.0214, 0.0052, 0.0079, -0.0074, -0.0249]
[-0.0586, -0.0929, -0.0108, 0.0243, 0.011, -0.0662, -0.0336, 0.0253, 0.0047, -0.0248, -0.0504, -0.0169]
[0.0269, 0.0755, -0.0365, -0.0525, -0.0083, 0.0828, 0.0189, -0.056, -0.0018, 0.0326, 0.0512, -0.003]
[0.1164, 0.1352, 0.042, -0.0018, -0.0178, 0.0876, 0.0382, -0.002, -0.0074, 0.0302, 0.0666, 0.0324]

6. Conv2 Leaky ReLU 결과:
[0.0269, -0.0025, -0.0041, 0.0248, -1.0E-4, 0.0326, -0.0065, 0.0214, 0.0052, 0.0079, -7.0E-4, -0.0025]
[-0.0059, -0.0093, -0.0011, 0.0243, 0.011, -0.0066, -0.0034, 0.0253, 0.0047, -0.0025, -0.005, -0.0017]
[0.0269, 0.0755, -0.0036, -0.0052, -8.0E-4, 0.0828, 0.0189, -0.0056, -2.0E-4, 0.0326, 0.0512, -3.0E-4]
[0.1164, 0.1352, 0.042, -2.0E-4, -0.0018, 0.0876, 0.0382, -2.0E-4, -7.0E-4, 0.0302, 0.0666, 0.0324]

7. pool2 결과 :
[0.0122, 0.0104, 0.0163, 0.0074, 0.0065, -0.0016]
[-0.0076, 0.0116, 0.0022, 0.0109, 0.0011, -0.0034]
[0.0512, -0.0044, 0.041, 0.0066, 0.0162, 0.0254]
[0.1258, 0.0209, 0.0429, 0.019, 0.0147, 0.0495]

8. Pool2 결과의 합산 :
[0.1817, 0.0384, 0.1024, 0.044, 0.0386, 0.07]

9. Conv3 결과:
[-0.1243, 0.0639, -0.0545, 0.0071]
[-0.1125, -0.0645, -0.0955, -0.0047]
[-0.095, 0.0543, -0.0174, -0.0289]
[0.1131, 0.1515, 0.0832, 0.0839]

10. flat 결과:
[-0.1243, 0.0639, -0.0545, 0.0071, -0.1125, -0.0645, -0.0955, -0.0047, -0.095, 0.0543, -0.0174, -0.0289, 0.1131, 0.1515, 0.0832, 0.0839]

11. flat의 sigmoid 결과:
[0.469, 0.516, 0.4864, 0.5018, 0.4719, 0.4839, 0.4762, 0.4988, 0.4763, 0.5136, 0.4957, 0.4928, 0.5282, 0.5378, 0.5208, 0.521]

12. dense 결과:
[0.1059, -0.2225, 1.1552, -0.3662]

13. dense의 tanh 결과:
[0.1055, -0.2189, 0.8195, -0.3506]

14. out 결과: 0.6664027064992601, 행렬_1: [0.78, -0.39, 0.66, 0.12]

15. out의 tanh 결과: 0.5826086087313175
```
