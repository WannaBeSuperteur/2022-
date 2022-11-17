괄호 안은 예제 번호

## 문자열
* 첫 ```A```부터 그 다음 ```B``` 이전까지의 문자열 구하기 **(26)**
  * ```string.substringAfter(A).substringBefore(B)```
* 여러 개의 서로 다른 문자열을 모두 치환 **(27, 32)**
  * ```listOf("str1", "str2", "str3").fold(string) { acc, e -> acc.replace(e, afterReplacement) }``` : 기본
  * ```listOf("str1", "str2", "str3").fold(string) { acc, e -> acc.replace(e.toString(), afterReplacement) }``` : ```e```가 String 자료형으로 인식되지 않는 경우
* 구분자를 이용하여 연결하기 **(36)**
  * ```list.joinToString(separator="")``` : 공백 없이 연결 (```KotlinJavaPython```)
  * ```list.joinToString(separator=" ")``` : 공백을 두고 연결 (```Kotlin Java Python```)

## 자료형 변환
* 배열의 모든 원소의 자료형 바꾸기 **(27)**
  * ```list.map { it.toDataType() }``` : 수정 불가 배열
  * ```list.map { it.toDataType() }.toMutableList()``` : 수정 가능 배열

## Regex (정규 표현식)
* 숫자 제거 **(27)**
  * ```Regex("[0-9]").replace(string, "")``` : 숫자 제거
  * ```Regex("[0-9 ]").replace(string, "")``` : 숫자 및 공백 제거
  * ```Regex("[^0-9]").replace(string, "")``` : 숫자를 제외하고 제거 (숫자만 남김)

## 집합, ```map``` 및 ```groupBy```
### 집합 및 ```map```
* 특정 속성의 집합 **(21)**
  * ```instance.map { it.property }.toSet()```
* map의 원소를 정렬하기 **(34)**
  * ```mapping.toList().sortedBy { ... }``` : 오름차순 정렬
  * ```mapping.toList().sortedByDescending { ... }``` : 내림차순 정렬

### ```groupBy``` 관련
* 각 그룹별 통계 값 구하기 **(22, 37)**
  * ```list.groupBy { it.??? }.map { computeValue(it.key, it.value) }```
  * ```list.groupBy { it.??? }.map { it.value.size }``` : 각 값의 빈도수
  * ```list.groupBy { it.??? }.map { listOf(it.key, it.value.size) }``` : 각 값과 해당 값의 빈도수
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
* 배열의 A번째부터 (B-1)번째까지의 항목 구하기 **(25, 30)**
  * ```list.take(B).takeLast(B-A)``` 또는 ```list.subList(A, B)```
* 배열의 argmax (가장 큰 값의 index) 구하기 **(38)**
  * ```val argmax = (0 until N).fold ( 0 ) { acc, e -> if (list[e] > list[acc]) { e } else { acc } }``` : 가장 큰 값의 index
  * ```val argmin = (0 until N).fold ( 0 ) { acc, e -> if (list[e] < list[acc]) { e } else { acc } }``` : 가장 작은 값의 index
* 배열의 값들의 곱 구하기 **(39)**
  * ```val product = list.fold (1.0) { acc, e -> acc * e }```

## 수학
* 반올림 **(34)**
  * ```import kotlin.math.*; round(value * 10.0) / 10.0``` : 소수점 아래 1번째 자리까지 반올림
  * ```import kotlin.math.*; round(value * 1000.0) / 1000.0``` : 소수점 아래 3번째 자리까지 반올림
