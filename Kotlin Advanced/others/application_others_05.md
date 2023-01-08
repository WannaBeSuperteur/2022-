## 요구사항 분석
다음과 같은 연산을 반복한 결과를 2 ~ 1000의 자연수에 대해 나타내시오.
* 짝수이면 2로 나눈다.
* 홀수이면 그 수에 3을 곱한 후 1을 더한다.
* 값이 1에 도달할 때까지 이것을 반복한다. (최대 30회. chain을 합쳤을 때 총 개수가 30개 이하이면 합치고, 그렇지 않으면 합쳐지는 지점의 값까지만 나타낸다.)

단, 같은 chain에 속하는 값들은 이어서 표시한다. 예를 들어 2 ~ 10의 자연수에 대해 나타내면 다음과 같다.
```kotlin
6 -> 3 -> 10 -> 5 -> 16 -> 8 -> 4 -> 2 -> 1
9 -> 28 -> 14 -> 7 -> 22 -> 11 -> 34 -> 17 -> 52 -> 26 -> 13 -> 40 -> 20 -> 10 -> 5 -> 16 -> 8 -> 4 -> 2 -> 1
```

## 코드

## 실행 결과