## 요구사항 분석
SQL-LIKE 문은 SQL과 유사한 형태의 데이터베이스 언어이다.

여러 개의 테이블들이 주어져 있을 때, 다음의 SQL-LIKE 문을 수행하고 그 결과를 출력하는 코드를 작성하시오.

실행 가능한 SQL-LIKE 문의 예시는 다음과 같다. (단, 괄호는 사용할 수 없으며, ```AND```를 ```OR```보다 먼저 처리한다. 고정된 값을 참조할 때는 양쪽에 작은따옴표를 붙인다.)

* ```SELECT COL1,COL2,COL3 FROM TABLE1 WHERE COL2=VAL1 AND COL3=VAL2```
  * ```TABLE1```에서 ```COL2``` 열의 값이 ```VAL1```이고 ```COL3``` 열의 값이 ```VAL2```인 모든 행에 대해, 그 행의 ```COL1```, ```COL2```, ```COL3``` 열의 값을 출력한다.
* ```SELECT * FROM TABLE1 WHERE COL1 = VAL3 OR COL2 = VAL4```
  * ```TABLE1```에서 ```COL1``` 열의 값이 ```VAL3```이거나 ```COL2``` 열의 값이 ```VAL4```인 모든 행에 대해, 그 행의 모든 열의 값을 출력한다.
* ```SELECT COL4,COL5 FROM TABLE1```
  * ```TABLE1```에서 모든 행에 대해, 그 행의 ```COL4```, ```COL5``` 열의 값을 출력한다.
* ```SELECT * FROM TABLE1 WHERE COL1 = VAL5 OR COL2 = VAL6 AND COL3 = VAL7```
  * ```TABLE1```에서 ```COL1``` 열의 값이 ```VAL5```이거나, ```COL2``` 열의 값이 ```VAL6```이면서 ```COL3``` 열의 값이 ```VAL7```인 모든 행에 대해, 그 행의 모든 열의 값을 출력한다.
* ```SELECT * FROM TABLE1 WHERE COL1 = 'abc' ORDER BY COL2 ASC COL3 DESC```
  * ```TABLE1```에서 ```COL1``` 열의 값이 ```abc```인 모든 행에 대해, 그 행의 모든 열의 값을 출력한다. 단, ```COL2```의 오름차순, ```COL3```의 내림차순으로 정렬 우선 순위를 정한다.
* ```SELECT TABLE1.COL1,TABLE1.COL3,TABLE2.COL4 FROM TABLE1,TABLE2 WHERE TABLE1.COL1 = TABLE2.COL2 AND TABLE1.COL3 = '200'```
  * ```TABLE1```과 ```TABLE2```의 카티션 곱에 대해, ```TABLE1```의 열 ```COL1```의 값이 ```TABLE2```의 열 ```COL2``` 값과 같고, ```TABLE1```의 열 ```COL3```의 값이 ```200```인 모든 행에 대해, ```TABLE1```의 열 ```COL1```, ```COL3```의 값과 ```TABLE2```의 열 ```COL4```의 값을 출력한다.

### 데이터 클래스
데이터 클래스는 다음과 같다.

```kotlin
// 데이터베이스
data class DataBase (
    val tables: List<Table>, // 데이터베이스 테이블들 
    val queries: List<String> // SQL-LIKE 쿼리들
)

// 데이터베이스의 테이블
data class Table (
    val name: String, // 테이블 이름
    val column_name: List<String>, // 열 이름
    val rows: List<Row> // 테이블을 이루는 행
)

// 테이블을 이루는 각 행
data class Row (
    val row: List<String> // 각 행 (같은 테이블의 column_name과 원소 개수 일치)
)
```
