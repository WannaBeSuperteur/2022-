괄호 안은 예제 번호

## 문자열
* 첫 ```A```부터 그 다음 ```B``` 이전까지의 문자열 구하기 **(26)**
  * ```string.substringAfter(A).substringBefore(B)```
* 여러 개의 서로 다른 문자열을 모두 치환 **(27)**
  * ```listOf("str1", "str2", "str3").fold(string) { acc, e -> acc.replace(e, afterReplacement) }```

## 자료형 변환
* 배열의 모든 원소의 자료형 바꾸기 **(27)**
  * ```list.map { it.toDataType() }``` : 수정 불가 배열
  * ```list.map { it.toDataType() }.toMutableList()``` : 수정 가능 배열

## Regex (정규 표현식)
* 숫자 제거 **(27)**
  * ```Regex("[0-9]").replace(string, "")``` : 숫자 제거
  * ```Regex("[0-9 ]").replace(string, "")``` : 숫자 및 공백 제거
  * ```Regex("[^0-9]").replace(string, "")``` : 숫자를 제외하고 제거 (숫자만 남김)

## 집합 및 ```groupBy```
### 집합
* 특정 속성의 집합 **(21)**
  * ```instance.map { it.property }.toSet()```

### ```groupBy``` 관련
* 각 그룹별 통계 값 구하기 **(22)**
  * ```list.groupBy { it.??? }.map { computeValue(it.key, it.value) }```
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
* 크기가 N인 편집 가능한 배열 만들기 **(25)**
  * ```(0..N-1).map { defaultValue }.toMutableList()``` 또는 ```(0 until N).map { defaultValue }.toMutableList()```
* 배열의 A번째부터 (B-1)번째까지의 항목 구하기 **(25, 30)**
  * ```list.take(B).takeLast(B-A)``` 또는 ```list.subList(A, B)```
