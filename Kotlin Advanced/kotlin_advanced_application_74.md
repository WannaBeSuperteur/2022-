## 요구사항 분석
정수의 목록을 크기 순으로 정렬했을 때, 연속된 두 수 간의 차 중 가장 큰 4개의 값을 기준으로 전체 값을 5개의 등급 ```A```, ```B```, ```C```, ```D```, ```F```로 나누는 함수 ```findClass(valueList)```를 작성하시오.

예를 들어, 정수의 목록이
```kotlin
[3, 4, 10, 11, 12, 19, 25, 26, 28, 41]
```
일 때, 각 값의 등급은 다음과 같다.
```kotlin
3, 4 -> F
10, 11, 12 -> D
19 -> C
25, 26, 28 -> B
41 -> A
```

## 코드 및 출력 결과
### 코드
```kotlin
// 각 수의 등급 구하기
fun findClass(valueList: List<Int>): Map<Int, Char> {
    
    // 정렬하고 차이 구하기
    val sortedValueList = valueList.sorted()
    val n = sortedValueList.size
    val diff = (0 until n - 1).map {
        it to sortedValueList[it + 1] - sortedValueList[it]
    }
    println("1. sorted value list = ${sortedValueList}")
    println("2. difference = ${diff}")
    
    // 차이가 가장 큰 4개를 이용하여 등급화
    val diffValuesMax4 = diff.toList().sortedByDescending { it.second }.take(4)
    val cuttingIndices = listOf(0) + diffValuesMax4.map { it.first + 1 }.sorted() + listOf(n)
    println("3. difference values max 4 = ${diffValuesMax4}")
    println("4. cutting index = ${cuttingIndices}\n")
    
    val classList = listOf('F', 'D', 'C', 'B', 'A')
    
    // 각 반복에서 해당하는 커트라인 인덱스 사이의 모든 값에 등급을 매김 (fold를 이용하여 map 만들기)
    val result = (0..4).fold ( mapOf<Int, Char>() ) { acc, e ->
        acc + sortedValueList.subList(cuttingIndices[e], cuttingIndices[e + 1]).map {
            it to classList[e]
        }
    }
    
    // 각 수를 등급화 (fold를 이용하여 map 만들기)
    val classValues = classList.fold ( mapOf<Char, MutableList<Int>>() ) { acc, e ->
        acc + mapOf(e to mutableListOf<Int>())
    }.toMutableMap()
    
    valueList.forEach { classValues[result[it]]!!.add(it) }
    println("5. class to numbers : ${classValues}")
    
    val valueClass = valueList.map { listOf(it, result[it]) }
    println("6. numbers to class : ${valueClass}")
    
    return result
}

fun main() {
    
    // 임시 값 목록
    val tempValueLists = listOf(
        listOf(10, 11, 19, 4, 12, 25, 3, 28, 41, 26),
        listOf(3, 3, 3, 7, 7, 7, 2, 2, 1, 1, 1, 5, 5, 5, 5, 5),
        listOf(31, 32, 33, 21, 22, 11, 12, 13, 14, 41, 42, 43, 50, 51),
        listOf(2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59),
        listOf(1, 1, 2, 2, 4, 5, 6, 4, 2, 1, 8, 9, 12, 8, 15, 16, 8, 8, 12, 12),
        listOf(73, 65, 69, 60, 79, 96, 91, 77, 48, 49, 96, 97, 91, 56, 50),
        listOf(70, 71, 71, 72, 73, 75, 76, 80, 83, 88, 95, 100),
        listOf(100, 100, 99, 99, 96, 95, 94, 92, 88, 86, 85, 81, 73, 64, 41, 22)
    )
    
    // 각 목록에 대해 각 수의 등급 출력하기
    tempValueLists.forEach {
        println("\n등급 : ${findClass(it)}\n")
    }
}
```

### 출력 결과
```kotlin
1. sorted value list = [3, 4, 10, 11, 12, 19, 25, 26, 28, 41]
2. difference = [(0, 1), (1, 6), (2, 1), (3, 1), (4, 7), (5, 6), (6, 1), (7, 2), (8, 13)]
3. difference values max 4 = [(8, 13), (4, 7), (1, 6), (5, 6)]
4. cutting index = [0, 2, 5, 6, 9, 10]

5. class to numbers : {F=[4, 3], D=[10, 11, 12], C=[19], B=[25, 28, 26], A=[41]}
6. numbers to class : [[10, D], [11, D], [19, C], [4, F], [12, D], [25, B], [3, F], [28, B], [41, A], [26, B]]

등급 : {3=F, 4=F, 10=D, 11=D, 12=D, 19=C, 25=B, 26=B, 28=B, 41=A}

1. sorted value list = [1, 1, 1, 2, 2, 3, 3, 3, 5, 5, 5, 5, 5, 7, 7, 7]
2. difference = [(0, 0), (1, 0), (2, 1), (3, 0), (4, 1), (5, 0), (6, 0), (7, 2), (8, 0), (9, 0), (10, 0), (11, 0), (12, 2), (13, 0), (14, 0)]
3. difference values max 4 = [(7, 2), (12, 2), (2, 1), (4, 1)]
4. cutting index = [0, 3, 5, 8, 13, 16]

5. class to numbers : {F=[1, 1, 1], D=[2, 2], C=[3, 3, 3], B=[5, 5, 5, 5, 5], A=[7, 7, 7]}
6. numbers to class : [[3, C], [3, C], [3, C], [7, A], [7, A], [7, A], [2, D], [2, D], [1, F], [1, F], [1, F], [5, B], [5, B], [5, B], [5, B], [5, B]]

등급 : {1=F, 2=D, 3=C, 5=B, 7=A}

1. sorted value list = [11, 12, 13, 14, 21, 22, 31, 32, 33, 41, 42, 43, 50, 51]
2. difference = [(0, 1), (1, 1), (2, 1), (3, 7), (4, 1), (5, 9), (6, 1), (7, 1), (8, 8), (9, 1), (10, 1), (11, 7), (12, 1)]
3. difference values max 4 = [(5, 9), (8, 8), (3, 7), (11, 7)]
4. cutting index = [0, 4, 6, 9, 12, 14]

5. class to numbers : {F=[11, 12, 13, 14], D=[21, 22], C=[31, 32, 33], B=[41, 42, 43], A=[50, 51]}
6. numbers to class : [[31, C], [32, C], [33, C], [21, D], [22, D], [11, F], [12, F], [13, F], [14, F], [41, B], [42, B], [43, B], [50, A], [51, A]]

등급 : {11=F, 12=F, 13=F, 14=F, 21=D, 22=D, 31=C, 32=C, 33=C, 41=B, 42=B, 43=B, 50=A, 51=A}

1. sorted value list = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59]
2. difference = [(0, 1), (1, 2), (2, 2), (3, 4), (4, 2), (5, 4), (6, 2), (7, 4), (8, 6), (9, 2), (10, 6), (11, 4), (12, 2), (13, 4), (14, 6), (15, 6)]
3. difference values max 4 = [(8, 6), (10, 6), (14, 6), (15, 6)]
4. cutting index = [0, 9, 11, 15, 16, 17]

5. class to numbers : {F=[2, 3, 5, 7, 11, 13, 17, 19, 23], D=[29, 31], C=[37, 41, 43, 47], B=[53], A=[59]}
6. numbers to class : [[2, F], [3, F], [5, F], [7, F], [11, F], [13, F], [17, F], [19, F], [23, F], [29, D], [31, D], [37, C], [41, C], [43, C], [47, C], [53, B], [59, A]]

등급 : {2=F, 3=F, 5=F, 7=F, 11=F, 13=F, 17=F, 19=F, 23=F, 29=D, 31=D, 37=C, 41=C, 43=C, 47=C, 53=B, 59=A}

1. sorted value list = [1, 1, 1, 2, 2, 2, 4, 4, 5, 6, 8, 8, 8, 8, 9, 12, 12, 12, 15, 16]
2. difference = [(0, 0), (1, 0), (2, 1), (3, 0), (4, 0), (5, 2), (6, 0), (7, 1), (8, 1), (9, 2), (10, 0), (11, 0), (12, 0), (13, 1), (14, 3), (15, 0), (16, 0), (17, 3), (18, 1)]
3. difference values max 4 = [(14, 3), (17, 3), (5, 2), (9, 2)]
4. cutting index = [0, 6, 10, 15, 18, 20]

5. class to numbers : {F=[1, 1, 2, 2, 2, 1], D=[4, 5, 6, 4], C=[8, 9, 8, 8, 8], B=[12, 12, 12], A=[15, 16]}
6. numbers to class : [[1, F], [1, F], [2, F], [2, F], [4, D], [5, D], [6, D], [4, D], [2, F], [1, F], [8, C], [9, C], [12, B], [8, C], [15, A], [16, A], [8, C], [8, C], [12, B], [12, B]]

등급 : {1=F, 2=F, 4=D, 5=D, 6=D, 8=C, 9=C, 12=B, 15=A, 16=A}

1. sorted value list = [48, 49, 50, 56, 60, 65, 69, 73, 77, 79, 91, 91, 96, 96, 97]
2. difference = [(0, 1), (1, 1), (2, 6), (3, 4), (4, 5), (5, 4), (6, 4), (7, 4), (8, 2), (9, 12), (10, 0), (11, 5), (12, 0), (13, 1)]
3. difference values max 4 = [(9, 12), (2, 6), (4, 5), (11, 5)]
4. cutting index = [0, 3, 5, 10, 12, 15]

5. class to numbers : {F=[48, 49, 50], D=[60, 56], C=[73, 65, 69, 79, 77], B=[91, 91], A=[96, 96, 97]}
6. numbers to class : [[73, C], [65, C], [69, C], [60, D], [79, C], [96, A], [91, B], [77, C], [48, F], [49, F], [96, A], [97, A], [91, B], [56, D], [50, F]]

등급 : {48=F, 49=F, 50=F, 56=D, 60=D, 65=C, 69=C, 73=C, 77=C, 79=C, 91=B, 96=A, 97=A}

1. sorted value list = [70, 71, 71, 72, 73, 75, 76, 80, 83, 88, 95, 100]
2. difference = [(0, 1), (1, 0), (2, 1), (3, 1), (4, 2), (5, 1), (6, 4), (7, 3), (8, 5), (9, 7), (10, 5)]
3. difference values max 4 = [(9, 7), (8, 5), (10, 5), (6, 4)]
4. cutting index = [0, 7, 9, 10, 11, 12]

5. class to numbers : {F=[70, 71, 71, 72, 73, 75, 76], D=[80, 83], C=[88], B=[95], A=[100]}
6. numbers to class : [[70, F], [71, F], [71, F], [72, F], [73, F], [75, F], [76, F], [80, D], [83, D], [88, C], [95, B], [100, A]]

등급 : {70=F, 71=F, 72=F, 73=F, 75=F, 76=F, 80=D, 83=D, 88=C, 95=B, 100=A}

1. sorted value list = [22, 41, 64, 73, 81, 85, 86, 88, 92, 94, 95, 96, 99, 99, 100, 100]
2. difference = [(0, 19), (1, 23), (2, 9), (3, 8), (4, 4), (5, 1), (6, 2), (7, 4), (8, 2), (9, 1), (10, 1), (11, 3), (12, 0), (13, 1), (14, 0)]
3. difference values max 4 = [(1, 23), (0, 19), (2, 9), (3, 8)]
4. cutting index = [0, 1, 2, 3, 4, 16]

5. class to numbers : {F=[22], D=[41], C=[64], B=[73], A=[100, 100, 99, 99, 96, 95, 94, 92, 88, 86, 85, 81]}
6. numbers to class : [[100, A], [100, A], [99, A], [99, A], [96, A], [95, A], [94, A], [92, A], [88, A], [86, A], [85, A], [81, A], [73, B], [64, C], [41, D], [22, F]]

등급 : {22=F, 41=D, 64=C, 73=B, 81=A, 85=A, 86=A, 88=A, 92=A, 94=A, 95=A, 96=A, 99=A, 100=A}
```
