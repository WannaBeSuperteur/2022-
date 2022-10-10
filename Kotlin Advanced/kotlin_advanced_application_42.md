## 요구사항 분석
4개의 집합 ```A```, ```B```, ```C```, ```D``` 에 대해, ```A```와 ```B```의 교집합을 ```A ^ B```, 합집합을 ``` A U B```와 같은 식으로 나타낸다.

이들 4개의 집합에 대해, 각 집합에 속하는지의 여부에 따라, ```집합식```이 나타내는 집합에 속하는지를 출력하시오.

단, 집합식에서는 교집합을 합집합보다 먼저 계산하며, 괄호나 여집합, 차집합 등의 개념은 사용하지 않는다.

예를 들어 집합식이
```kotlin
A ^ B U C ^ D
```
일 때, 각 집합에 속하는지의 여부에 따라 이 집합식이 나타내는 집합에 속하는지의 여부를 나타내면 다음과 같다.

```kotlin
 A  B  C  D -> true
 A  B  C ~D -> true
 A  B ~C  D -> true
 A  B ~C ~D -> true
 A ~B  C  D -> true
 A ~B  C ~D -> false
 A ~B ~C  D -> false
 A ~B ~C ~D -> false
~A  B  C  D -> true
~A  B  C ~D -> false
~A  B ~C  D -> false
~A  B ~C ~D -> false
~A ~B  C  D -> true
~A ~B  C ~D -> false
~A ~B ~C  D -> false
~A ~B ~C ~D -> false
```

### 데이터 클래스
데이터 클래스 없이 ```집합식```이 문자열로 주어진다.
