괄호 안은 예제 번호

## 문자열
* 첫 ```A```부터 그 다음 ```B``` 이전까지의 문자열 구하기 **(26)**
  * ```string.substringAfter(A).substringBefore(B)```
* 여러 개의 서로 다른 문자열을 모두 치환 **(27, 32, 42)**
  * ```listOf("str1", "str2", "str3").fold(string) { acc, e -> acc.replace(e, afterReplacement) }``` : 기본
  * ```listOf("str1", "str2", "str3").fold(string) { acc, e -> acc.replace(e.toString(), afterReplacement) }``` : ```e```가 String 자료형으로 인식되지 않는 경우
  * ```(0 until N).fold(string) { acc, e -> acc.replace(arr[e], afterReplacement }``` : ```arr```의 원소 ```N```개를 서로 다른 문자열들로 하여 치환하는 경우 
* 구분자를 이용하여 연결하기 **(36)**
  * ```list.joinToString(separator="")``` : 공백 없이 연결 (```KotlinJavaPython```)
  * ```list.joinToString(separator=" ")``` : 공백을 두고 연결 (```Kotlin Java Python```)
* 배열에 있는 문자열을 연결하기 **(42)**
  * ```(0 until N).fold("") { acc, e -> "${acc}${func(arr[e])}" }``` : 배열 ```arr```에 있는 각 문자열에 대해 함수를 적용하여 연결하는 경우
* 특정 문자열 직전의 숫자 값 구하기 **(44)**
  * ```string.substringBefore("specific_string").takeLastWhile { it.isDigit() }```
* 첫 번째 숫자 값의 인덱스 구하기 **(44)**
  * ```string.indexOfFirst { it in "0123456789" }``` 또는 ```string.indexOfFirst { it.isDigit() }```
* 연속된 숫자로 이루어진 각 부분 구하기 **(47)**
```kotlin
val re = Regex("[^0-9]+")
val nums = re.replace(string, " ")
val numList = nums.trim().split(" ")
```
* 문자열 뒤집기 **(50)**
  * ```string.reversed()```
* 문자열에서 첫 대문자 직전까지의 소문자들로 된 부문자열 구하기 **(60)**
  * ```string.takeWhile { !it.isUpperCase() }```

### 숫자, 문자의 연속되는 부분 찾기
* 숫자가 연속되는 부분이 1곳인지 구하기 **(44)**
```kotlin
val numStartIdx = (0 until string.length - 1).filter {
    !string[it].isDigit() && string[it + 1].isDigit()
}
val numEndIdx = (0 until string.length - 1).filter {
    string[it].isDigit() && !string[it + 1].isDigit()
}
numStartIdx.size > 0 && numEndIdx.size > 0 && numStartIdx.max() > numEndIdx.min()
```
* 특정 문자가 나타나기 시작하는 지점과 끝나는 지점 구하기 **(51)**
```kotlin
val charStartIdx = (0 until string.length).filter {
    val stringWithSpace = " ${string}"
    stringWithSpace[it] != char && stringWithSpace[it + 1] == char
}
        
val charEndIdx = (0 until string.length).filter {
    val stringWithSpace = "${string} "
    stringWithSpace[it] == char && stringWithSpace[it + 1] != char
}
```

## 자료형 변환
* 배열의 모든 원소의 자료형 바꾸기 **(27)**
  * ```list.map { it.toDataType() }``` : 수정 불가 배열
  * ```list.map { it.toDataType() }.toMutableList()``` : 수정 가능 배열
* 특정 자료형으로 변환할 수 없으면 null을 반환 **(56)**
  * ```value.toIntOrNull()``` : Int 자료형으로 바꿀 수 없으면 null을 반환
  * ```value.toDoubleOrNull()``` : Double 자료형으로 바꿀 수 없으면 null을 반환

## Regex (정규 표현식)
* 숫자 제거 **(27)**
  * ```Regex("[0-9]").replace(string, "")``` : 숫자 제거
  * ```Regex("[0-9 ]").replace(string, "")``` : 숫자 및 공백 제거
  * ```Regex("[^0-9]").replace(string, "")``` : 숫자를 제외하고 제거 (숫자만 남김)
* 문자들의 연속된 부분을 치환 **(47)**
  * ```Regex("[^0-9 ]+").replace(string, "")``` : 숫자 및 공백을 제외한 문자들의 연속된 부분을 공백 1칸으로 치환
* 시간 표시 나타내기 **(49)**
  * ```Regex("[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}")``` : ```yyyy-mm-dd hh:mm:ss```
  * ```Regex("[0-9]{4}/[0-9]{2}/[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}")``` : ```yyyy/mm/dd hh:mm:ss```
  * ```Regex("[0-9]{4}년 [0-9]{1,2}월 [0-9]{1,2}일 오[전후] [0-9]{1,2}시 [0-9]{1,2}분 [0-9]{1,2}초")``` : ```yyyy년 m월 d일 오전/오후 h시 m분 s초```

## 집합, ```map``` 및 ```groupBy```
### 집합 및 ```map```
* 특정 속성의 집합 **(21)**
  * ```instance.map { it.property }.toSet()```
* map의 원소를 정렬하기 **(34)**
  * ```mapping.toList().sortedBy { ... }``` : 오름차순 정렬
  * ```mapping.toList().sortedByDescending { ... }``` : 내림차순 정렬
* map의 원소들의 합계 구하기 **(45)**
  * ```mapping.toList().sumOf { it.first }``` : key의 합계
  * ```mapping.toList().sumOf { it.second }``` : value의 합계
  * ```mapping.toList().sumOf { func(it.first, it.second) }``` : 함수를 이용한 합계

### ```groupBy``` 관련
* 각 그룹별 통계 값 구하기 **(22, 37, 67)**
  * ```list.groupBy { it.??? }.map { computeValue(it.key, it.value) }```
  * ```list.groupBy { it.??? }.map { it.value.size }``` : 각 값의 빈도수
  * ```list.groupBy { it.??? }.map { listOf(it.key, it.value.size) }``` : 각 값과 해당 값의 빈도수
  * ```list.groupBy { it.??? }.filter { ... }.map { listOf(it.key, it.value.size) }``` : 각 값과 해당 값의 빈도수 (필터 적용)
* 각 그룹별 정보를 배열에 저장하기 **(23)**
  * ```list.groupBy { it.??? }.toList()``` : ```[[it.???, (list에서 해당하는 원소들의 배열)], ...]``` 형태의 값
    * ```it.???```는 ```.first```로, ```list에서 해당하는 원소들의 배열```은 ```.second```로 참조 가능

## 배열
* 첫 번째 및 마지막 값 **(20)**
  * ```list.first()``` : 배열의 첫 번째 값
  * ```list.last()``` : 배열의 마지막 값
* 배열의 일부분의 합 **(20, 30)**
  * ```list.take(N).sum()``` : 처음 ```N```개의 값들의 합
  * ```list.takeLast(N).sum()``` : 마지막 ```N```개의 값들의 합
  * ```list.subList(A, B).sum()``` : 배열의 A번째부터 (B-1)번째까지의 값들의 합
* 배열에서 TOP N 구하기 **(22)**
  * ```list.sorted().take(N)``` 또는 ```list.sortedDescending().take(N)```
* 크기가 N인 편집 가능한 배열 만들기 **(25, 33)**
  * ```(0..N-1).map { defaultValue }.toMutableList()``` 또는 ```(0 until N).map { defaultValue }.toMutableList()```
  * ```(0..N-1).map { it }.toMutableList()``` : 편집 가능한 ```[0, 1, ..., N-1]```의 배열
* 배열의 A번째부터 (B-1)번째까지의 항목 구하기 **(25, 30, 65)**
  * ```list.take(B).takeLast(B-A)```, ```list.subList(A, B)``` 또는 ```list.slice(A .. B-1)```
    * ```list.slice(A .. B-1)``` 방식의 경우, ```A .. B-1``` 대신 리스트를 넣어서 ```list```에서 해당 리스트의 각 값에 해당하는 인덱스가 가리키는 값들로 이루어진 새로운 배열을 만들 수 있다.
* 배열의 argmax (가장 큰 값의 index) 구하기 **(38)**
  * ```val argmax = (0 until N).fold ( 0 ) { acc, e -> if (list[e] > list[acc]) { e } else { acc } }``` : 가장 큰 값의 index
  * ```val argmin = (0 until N).fold ( 0 ) { acc, e -> if (list[e] < list[acc]) { e } else { acc } }``` : 가장 작은 값의 index
* 배열의 값들의 곱 구하기 **(39)**
  * ```val product = list.fold (1.0) { acc, e -> acc * e }```
* 2차원 배열 만들기 **(41)**
  * ```(0 until N).map { a -> (0 until M).map { b -> ... } }```
* 합계를 저장한 배열 만들기 **(41)**
  * ```(0 until N).map { a -> (0 until M).sumOf { b -> ... } }```
* 배열의 모든 항목 삭제 **(43)**
  * ```list.clear()```

## 여러 기준에 의한 정렬하기
* 여러 기준에 의한 정렬하기 **(47)**
```kotlin
val sortResult = list.sortedWith (
    compareBy (
        { it.arg1 }, // arg1 오름차순 정렬
        { -it.arg2 }, // arg2 내림차순 정렬
        { func(it.arg3, it.arg4) }, // arg3, arg4에 함수를 적용한 특정 기준에 의해 정렬
        ...
    )
)
```

## 수학
* 반올림 **(34)**
  * ```import kotlin.math.*; round(value * 10.0) / 10.0``` : 소수점 아래 1번째 자리까지 반올림
  * ```import kotlin.math.*; round(value * 1000.0) / 1000.0``` : 소수점 아래 3번째 자리까지 반올림
* 퍼센트 반올림 **(61)**
  * ```val percent = Math.round(value * 1000.0) / 10.0``` : 소수점 이하 1번째 자리까지 반올림 (예: ```12.7%```)

## 기타
* 문자의 아스키 코드 구하기 **(47)**
  * ```'a'.code```
* ```fold()```를 사용할 때 인덱스도 같이 사용하기 **(56)**
  * ```list.foldIndexed ( ... ) { idx, acc, e -> ... }```
* ```forEach```, ```forEachIndexed``` 문에서 다른 언어의 ```continue``` 적용하기 **(62, 65)**
  * ```return@forEach``` 또는 ```return@forEachIndexed```
