## 요구사항 분석
문자열의 집합이 있을 때, 한 문자열이 다른 문자열을 포함하는 경우를 모두 나타내시오. 단, 모든 문자열은 알파벳 소문자만으로 구성된다.

예를 들어 문자열 집합에 ```programming, program, pro, ing```이 있을 때는 다음과 같이 출력한다.
```kotlin
programming -> program, pro, ing
program -> pro
pro -> -
ing -> -
```

### 데이터 클래스
데이터 클래스 없이, 아래 형식과 같은 문자열 집합이 주어진다.
```kotlin
List<String>
```
