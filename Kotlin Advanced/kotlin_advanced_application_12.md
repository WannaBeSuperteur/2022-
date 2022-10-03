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

## 코드 및 출력
### 코드
```kotlin
import kotlin.math.*

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

// 최대, 최소, 평균값 계산 및 표시
fun printStats(modelGroups: Map<String, List<Model>>,
               displayMapFunc: ((String) -> String)?,
               questionNo: Int) {
    
    // 모델 그룹 출력
    modelGroups.forEach {
        println("${questionNo}. ${it}")
    }
    print("\n")
    
    // 각 그룹별 성능의 최대, 최소, 평균값 출력
    println("${questionNo}. answer:")
    modelGroups.forEach {
        val displayKey = if (displayMapFunc != null) {
            displayMapFunc(it.key)
        } else {
            it.key
        }
        
        val maxValue = it.value.maxOf { model ->
            model.accuracy
        }
        val minValue = it.value.minOf { model ->
            model.accuracy
        }
        val avgValue = it.value.sumOf { model ->
            model.accuracy
        } / it.value.size
        
        println("${displayKey} : 최대 ${maxValue}, 최소 ${minValue}, 평균 ${avgValue} (개수: ${it.value.size})")
    }
    print("\n")
}

// 각 모델의 입력 데이터의 전체 속성의 개수의 범위별 성능 지표의 최대, 최소, 평균값
fun getAccuracyStatByAttributeCount(models: List<Model>) {
    val modelGroups = models.groupBy {
        it.input_data.attributes.size.toString() // key는 string으로 통일
    }
    
    // 최대, 최소, 평균값 계산 및 표시
    printStats(modelGroups, null, 1)
}

// 각 모델의 입력 데이터의 숫자 값에 해당하는 속성의 개수에 따른 성능 지표의 최대, 최소, 평균값
fun getAccuracyStatByNumericAttributeCount(models: List<Model>) {
    val modelGroups = models.groupBy {
        it.input_data.attributes.filter {
            it.type == "n"
        }.size.toString() // key는 string으로 통일
    }
    
    // 최대, 최소, 평균값 계산 및 표시
    printStats(modelGroups, null, 2)
}

// 각 모델의 입력 데이터의 숫자 값에 해당하는 속성의 처리 방법 중 적용된 속성의 개수가 가장 많은 처리 방법이 무엇인지에 따른 성능 지표의 최대, 최소, 평균값
fun getAccuracyStatByMostPopularHandleMethod(models: List<Model>) {
    
    // 숫자 값에 해당하는 속성이 없는 모델 제외
    val modelsWithNumericFeatures = models.filter {
        it.input_data.attributes.filter {
            it.type == "n"
        }.size > 0
    }
    modelsWithNumericFeatures.forEach {
        println("3-1. ${it}")
    }
    print("\n")
    
    val modelGroups = modelsWithNumericFeatures.groupBy {
        
        // nzo, nn, o의 각 처리 방법별로 attribute들을 구분
        val numericAttributes = it.input_data.attributes.filter {
            it.type == "n"
        }
        val numericHandleMethods = numericAttributes.groupBy {
            it.handle_method
        }
        println("3-2. ${numericHandleMethods}")
        
        // nzo, nn, o의 각 처리 방법 중 사용한 속성이 가장 많은 처리 방법의 사용한 속성 수
        // 각 처리 방법에 해당하는 속성이 존재하지 않는 경우를 처리하기 위해 getOrElse()를 사용
        val numericHandleMethodsMax = listOf("nzo", "nn", "o").maxOf {
            numericHandleMethods.getOrElse(it){ listOf() }.size
        }
        println("3-3. ${numericHandleMethodsMax}")
        
        // 가장 많은 attribute에서 사용한 처리 방법
        // {key=value} 형식의 원소가 1개인 dict에서 key를 뽑아내기 위해 toList()[0].first를 사용
        val mostUsedHandleMethod = numericHandleMethods.filter {
            it.value.size == numericHandleMethodsMax
        }.toList()[0].first
        println("3-4. ${mostUsedHandleMethod}\n")
        
        mostUsedHandleMethod
    }
    
    // 최대, 최소, 평균값 계산 및 표시
    printStats(modelGroups, null, 3)
}

// 각 모델의 입력 데이터의 문자열에 해당하는 속성의 개수에 따른 성능 지표의 최대, 최소, 평균값
fun getAccuracyStatByStringAttributeCount(models: List<Model>) {
    val modelGroups = models.groupBy {
        it.input_data.attributes.filter {
            it.type == "s"
        }.size.toString() // key는 string으로 통일
    }
    
    // 최대, 최소, 평균값 계산 및 표시
    printStats(modelGroups, null, 4)
}

// 각 모델의 하이퍼 파라미터 3종류 (epoch 개수, learning rate, dropout) 각각의 값이 평균 이상인지, 그 미만인지에 따른 성능 지표의 최대, 최소, 평균값
fun getAccuracyStatByHyperParamValues(models: List<Model>) {
    val n = models.size
    val avgEpochs = models.sumOf {
        it.hyper_parameters.epochs
    } / n.toDouble()
    val avgLearningRate = models.sumOf {
        it.hyper_parameters.learning_rate
    } / n
    val avgDropout = models.sumOf {
        it.hyper_parameters.dropout
    } / n
    println("5-1. epochs=${avgEpochs}, lr=${avgLearningRate}, dropout=${avgDropout}\n")
    
    // 각 케이스에 따른 구분
    val modelGroups = mutableMapOf<String, MutableList<Model>>()
    
    models.forEach {
        val aboveAvgEpochs = if (it.hyper_parameters.epochs >= avgEpochs) { "1" } else { "0" }
        val aboveAvgLR = if (it.hyper_parameters.learning_rate >= avgLearningRate) { "1" } else { "0" }
        val aboveAvgDropout = if (it.hyper_parameters.dropout >= avgDropout) { "1" } else { "0" }
        val keyString = "${aboveAvgEpochs}${aboveAvgLR}${aboveAvgDropout}"
        
        println("5-2. ${keyString}")
        
        // 해당 key(000 ~ 111) 포함 시, 해당 리스트에 추가
        if (modelGroups.containsKey(keyString)) {
            modelGroups.get(keyString)!!.add(it)
        }
        
        // 그렇지 않으면, 해당 리스트를 생성
        else {
            modelGroups.put(keyString, mutableListOf(it))
        }
    }
    print("\n")
    
    // 최대, 최소, 평균값 계산 및 표시 함수
    val displayMapFunc : ((String) -> String) = {
        var result = if (it.get(0) == '1') { "epoch 평균이상 " } else { "epoch 평균미만 "}
        result += if (it.get(1) == '1') { "lr 평균이상 " } else { "lr 평균미만 " }
        result += if (it.get(2) == '1') { "dropout 평균이상" } else { "dropout 평균미만" }
        result
    }

    printStats(modelGroups, displayMapFunc, 5)
}

fun main() {
    val models = listOf<Model> (
        Model(
            "ai_00", 0, InputData(listOf<Attribute>(
                Attribute("s", "t")
            )), HyperParams(10, 0.01, 0.25), 0.789
        ),
        Model(
            "ai_01", 1, InputData(listOf<Attribute>(
                Attribute("s", "t")
            )), HyperParams(10, 0.005, 0.25), 0.813
        ),
        Model(
            "ai_02", 2, InputData(listOf<Attribute>(
                Attribute("s", "t")
            )), HyperParams(10, 0.01, 0.35), 0.795
        ),
        Model(
            "advanced_00", 5, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("n", "nzo")
            )), HyperParams(8, 0.015, 0.25), 0.84
        ),
        Model(
            "advanced_01", 6, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("n", "nzo"), Attribute("n", "nn")
            )), HyperParams(8, 0.015, 0.25), 0.852
        ),
        Model(
            "advanced_02", 13, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("n", "nzo"), Attribute("n", "o")
            )), HyperParams(12, 0.005, 0.3), 0.839
        ),
        Model(
            "advanced_03", 14, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("n", "nzo"), Attribute("n", "nn"), Attribute("n", "nn")
            )), HyperParams(12, 0.005, 0.3), 0.866
        ),
        Model(
            "advanced_04", 16, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("n", "nn"), Attribute("n", "nn")
            )), HyperParams(16, 0.0025, 0.325), 0.896
        ),
        Model(
            "advanced_05", 18, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("n", "o"), Attribute("n", "nn"), Attribute("n", "nn")
            )), HyperParams(16, 0.0025, 0.325), 0.918
        ),
        Model(
            "advanced_tf_00", 20, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("tf", "zo"), Attribute("n", "nzo"), Attribute("n", "o")
            )), HyperParams(12, 0.005, 0.3), 0.848
        ),
        Model(
            "advanced_tf_01", 21, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("tf", "zo"), Attribute("n", "nzo"), Attribute("n", "nn"), Attribute("n", "nn")
            )), HyperParams(12, 0.005, 0.3), 0.872
        ),
        Model(
            "advanced_tf_02", 22, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("tf", "zo"), Attribute("n", "nn"), Attribute("n", "nn")
            )), HyperParams(16, 0.0025, 0.325), 0.951
        ),
        Model(
            "advanced_tf_03", 23, InputData(listOf<Attribute>(
                Attribute("s", "t"), Attribute("tf", "zo"), Attribute("n", "o"), Attribute("n", "nn"), Attribute("n", "nn")
            )), HyperParams(16, 0.0025, 0.325), 0.96
        )
    )
    
    // 각 모델의 입력 데이터의 전체 속성의 개수의 범위별 성능 지표의 최대, 최소, 평균값
    getAccuracyStatByAttributeCount(models)
    
    // 각 모델의 입력 데이터의 숫자 값에 해당하는 속성의 개수에 따른 성능 지표의 최대, 최소, 평균값
    getAccuracyStatByNumericAttributeCount(models)

    // 각 모델의 입력 데이터의 숫자 값에 해당하는 속성의 처리 방법 중 적용된 속성의 개수가 가장 많은 처리 방법이 무엇인지에 따른 성능 지표의 최대, 최소, 평균값
    getAccuracyStatByMostPopularHandleMethod(models)

    // 각 모델의 입력 데이터의 문자열에 해당하는 속성의 개수에 따른 성능 지표의 최대, 최소, 평균값
    getAccuracyStatByStringAttributeCount(models)

    // 각 모델의 하이퍼 파라미터 3종류 (epoch 개수, learning rate, dropout) 각각의 값이 평균 이상인지, 그 미만인지에 따른 성능 지표의 최대, 최소, 평균값
    getAccuracyStatByHyperParamValues(models)
}
```

### 출력
```kotlin
1. 1=[Model(name=ai_00, ID=0, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.25), accuracy=0.789), Model(name=ai_01, ID=1, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.005, dropout=0.25), accuracy=0.813), Model(name=ai_02, ID=2, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.35), accuracy=0.795)]
1. 2=[Model(name=advanced_00, ID=5, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.84)]
1. 3=[Model(name=advanced_01, ID=6, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.852), Model(name=advanced_02, ID=13, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.839), Model(name=advanced_04, ID=16, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.896)]
1. 4=[Model(name=advanced_03, ID=14, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.866), Model(name=advanced_05, ID=18, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.918), Model(name=advanced_tf_00, ID=20, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.848), Model(name=advanced_tf_02, ID=22, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.951)]
1. 5=[Model(name=advanced_tf_01, ID=21, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.872), Model(name=advanced_tf_03, ID=23, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.96)]

1. answer:
1 : 최대 0.813, 최소 0.789, 평균 0.7989999999999999 (개수: 3)
2 : 최대 0.84, 최소 0.84, 평균 0.84 (개수: 1)
3 : 최대 0.896, 최소 0.839, 평균 0.8623333333333333 (개수: 3)
4 : 최대 0.951, 최소 0.848, 평균 0.89575 (개수: 4)
5 : 최대 0.96, 최소 0.872, 평균 0.9159999999999999 (개수: 2)

2. 0=[Model(name=ai_00, ID=0, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.25), accuracy=0.789), Model(name=ai_01, ID=1, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.005, dropout=0.25), accuracy=0.813), Model(name=ai_02, ID=2, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.35), accuracy=0.795)]
2. 1=[Model(name=advanced_00, ID=5, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.84)]
2. 2=[Model(name=advanced_01, ID=6, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.852), Model(name=advanced_02, ID=13, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.839), Model(name=advanced_04, ID=16, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.896), Model(name=advanced_tf_00, ID=20, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.848), Model(name=advanced_tf_02, ID=22, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.951)]
2. 3=[Model(name=advanced_03, ID=14, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.866), Model(name=advanced_05, ID=18, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.918), Model(name=advanced_tf_01, ID=21, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.872), Model(name=advanced_tf_03, ID=23, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.96)]

2. answer:
0 : 최대 0.813, 최소 0.789, 평균 0.7989999999999999 (개수: 3)
1 : 최대 0.84, 최소 0.84, 평균 0.84 (개수: 1)
2 : 최대 0.951, 최소 0.839, 평균 0.8771999999999999 (개수: 5)
3 : 최대 0.96, 최소 0.866, 평균 0.904 (개수: 4)

3-1. Model(name=advanced_00, ID=5, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.84)
3-1. Model(name=advanced_01, ID=6, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.852)
3-1. Model(name=advanced_02, ID=13, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.839)
3-1. Model(name=advanced_03, ID=14, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.866)
3-1. Model(name=advanced_04, ID=16, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.896)
3-1. Model(name=advanced_05, ID=18, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.918)
3-1. Model(name=advanced_tf_00, ID=20, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.848)
3-1. Model(name=advanced_tf_01, ID=21, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.872)
3-1. Model(name=advanced_tf_02, ID=22, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.951)
3-1. Model(name=advanced_tf_03, ID=23, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.96)

3-2. {nzo=[Attribute(type=n, handle_method=nzo)]}
3-3. 1
3-4. nzo

3-2. {nzo=[Attribute(type=n, handle_method=nzo)], nn=[Attribute(type=n, handle_method=nn)]}
3-3. 1
3-4. nzo

3-2. {nzo=[Attribute(type=n, handle_method=nzo)], o=[Attribute(type=n, handle_method=o)]}
3-3. 1
3-4. nzo

3-2. {nzo=[Attribute(type=n, handle_method=nzo)], nn=[Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]}
3-3. 2
3-4. nn

3-2. {nn=[Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]}
3-3. 2
3-4. nn

3-2. {o=[Attribute(type=n, handle_method=o)], nn=[Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]}
3-3. 2
3-4. nn

3-2. {nzo=[Attribute(type=n, handle_method=nzo)], o=[Attribute(type=n, handle_method=o)]}
3-3. 1
3-4. nzo

3-2. {nzo=[Attribute(type=n, handle_method=nzo)], nn=[Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]}
3-3. 2
3-4. nn

3-2. {nn=[Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]}
3-3. 2
3-4. nn

3-2. {o=[Attribute(type=n, handle_method=o)], nn=[Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]}
3-3. 2
3-4. nn

3. nzo=[Model(name=advanced_00, ID=5, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.84), Model(name=advanced_01, ID=6, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.852), Model(name=advanced_02, ID=13, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.839), Model(name=advanced_tf_00, ID=20, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.848)]
3. nn=[Model(name=advanced_03, ID=14, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.866), Model(name=advanced_04, ID=16, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.896), Model(name=advanced_05, ID=18, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.918), Model(name=advanced_tf_01, ID=21, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.872), Model(name=advanced_tf_02, ID=22, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.951), Model(name=advanced_tf_03, ID=23, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.96)]

3. answer:
nzo : 최대 0.852, 최소 0.839, 평균 0.8447499999999999 (개수: 4)
nn : 최대 0.96, 최소 0.866, 평균 0.9105 (개수: 6)

4. 1=[Model(name=ai_00, ID=0, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.25), accuracy=0.789), Model(name=ai_01, ID=1, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.005, dropout=0.25), accuracy=0.813), Model(name=ai_02, ID=2, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.35), accuracy=0.795), Model(name=advanced_00, ID=5, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.84), Model(name=advanced_01, ID=6, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.852), Model(name=advanced_02, ID=13, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.839), Model(name=advanced_03, ID=14, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.866), Model(name=advanced_04, ID=16, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.896), Model(name=advanced_05, ID=18, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.918), Model(name=advanced_tf_00, ID=20, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.848), Model(name=advanced_tf_01, ID=21, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.872), Model(name=advanced_tf_02, ID=22, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.951), Model(name=advanced_tf_03, ID=23, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.96)]

4. answer:
1 : 최대 0.96, 최소 0.789, 평균 0.8645384615384616 (개수: 13)

5-1. epochs=12.153846153846153, lr=0.00653846153846154, dropout=0.2961538461538462

5-2. 010
5-2. 000
5-2. 011
5-2. 010
5-2. 010
5-2. 001
5-2. 001
5-2. 101
5-2. 101
5-2. 001
5-2. 001
5-2. 101
5-2. 101

5. 010=[Model(name=ai_00, ID=0, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.25), accuracy=0.789), Model(name=advanced_00, ID=5, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.84), Model(name=advanced_01, ID=6, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=8, learning_rate=0.015, dropout=0.25), accuracy=0.852)]
5. 000=[Model(name=ai_01, ID=1, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.005, dropout=0.25), accuracy=0.813)]
5. 011=[Model(name=ai_02, ID=2, input_data=InputData(attributes=[Attribute(type=s, handle_method=t)]), hyper_parameters=HyperParams(epochs=10, learning_rate=0.01, dropout=0.35), accuracy=0.795)]
5. 001=[Model(name=advanced_02, ID=13, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.839), Model(name=advanced_03, ID=14, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.866), Model(name=advanced_tf_00, ID=20, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=o)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.848), Model(name=advanced_tf_01, ID=21, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nzo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=12, learning_rate=0.005, dropout=0.3), accuracy=0.872)]
5. 101=[Model(name=advanced_04, ID=16, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.896), Model(name=advanced_05, ID=18, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.918), Model(name=advanced_tf_02, ID=22, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.951), Model(name=advanced_tf_03, ID=23, input_data=InputData(attributes=[Attribute(type=s, handle_method=t), Attribute(type=tf, handle_method=zo), Attribute(type=n, handle_method=o), Attribute(type=n, handle_method=nn), Attribute(type=n, handle_method=nn)]), hyper_parameters=HyperParams(epochs=16, learning_rate=0.0025, dropout=0.325), accuracy=0.96)]

5. answer:
epoch 평균미만 lr 평균이상 dropout 평균미만 : 최대 0.852, 최소 0.789, 평균 0.827 (개수: 3)
epoch 평균미만 lr 평균미만 dropout 평균미만 : 최대 0.813, 최소 0.813, 평균 0.813 (개수: 1)
epoch 평균미만 lr 평균이상 dropout 평균이상 : 최대 0.795, 최소 0.795, 평균 0.795 (개수: 1)
epoch 평균미만 lr 평균미만 dropout 평균이상 : 최대 0.872, 최소 0.839, 평균 0.85625 (개수: 4)
epoch 평균이상 lr 평균미만 dropout 평균이상 : 최대 0.96, 최소 0.896, 평균 0.93125 (개수: 4)
```
