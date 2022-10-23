## 요구사항 분석
1차원 좌표가 ```-a``` ~ ```+a```인 선분에서, 원점 ```0```에서 ```r0, r1, ..., r(n-1)```번 (```walks = [r0, r1, ..., r(n-1)]```) random walk를 한 결과, 각 지점의 도달 횟수의 상대값을 다음과 같이 출력하는 함수를 작성하시오.

예를 들어 ```a```의 값이 ```2```, 상대값의 최댓값 ```relativeMax```가 ```9```이고 ```10, 20, 30```번 random walk를 한 결과 각 지점의 도달 횟수가
```kotlin
10회 -> -2부터 +2까지 각각 1, 2, 4, 2, 1
20회 -> -2부터 +2까지 각각 2, 4, 7, 5, 1
30회 -> -2부터 +2까지 각각 2, 5, 7, 10, 6
```
일 때, 다음과 같이 각 상대값을 가장 가까운 정수로 내림하여 출력한다. 맨 마지막에는 상댓값이 가장 큰 인덱스를 출력한다.

```kotlin
for each :
2 4 9 4 2 / 2
2 5 9 6 1 / 2
1 4 9 6 5 / 2

for entire :
0 1 3 1 0
1 3 6 4 0
1 4 6 9 5 / 3
(2, 3)
```

단, 초기 위치는 제외하고, 마지막 random walk 이후의 위치는 포함한다.

### 함수
```doRandomWalk(a, walks, relativeMax)```