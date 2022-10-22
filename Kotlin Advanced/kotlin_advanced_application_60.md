## 요구사항 분석
문자열 ```string```이 문자열의 형식 ```expression```과 일치하는지 조건 ```condition```의 집합을 이용하여 검사하려고 한다.

예를 들어 ```string```이 ```aaa|bb cccdef```이고 ```expression```이 ```A|B Cdef```이며, ```condition```의 집합이 다음과 같을 때,
```kotlin
A -> a,aa,aaa
B -> bb,bab,baab
C -> ccc,dddd
```
```A|B Cdef```의 ```A```를 ```aaa```, ```B```를 ```bb```, ```C```를 ```ccc```로 대응시키면 ```string```과 일치하므로 반환값은 ```true```이다.

단, 각각의 ```condition```의 ```->```의 왼쪽은 항상 알파벳 대문자 1개이며, 그 오른쪽은 항상 알파벳 소문자와 ```,```만으로 구성되고, ```expression```에서 각 알파벳 대문자는 최소 1칸의 간격을 두고 있다.

### 함수
```isMatched(string, expression, condition)```
