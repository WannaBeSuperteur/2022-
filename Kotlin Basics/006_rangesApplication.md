# ranges의 응용
## 설명
 * ```A```부터 시작하여 ```B```까지 ```interval```씩 증가시켜 나가면서 실행하기
```kotlin
for (i in A..B step interval) {
  // code to execute
}
```
 * ```B```부터 시작하여 ```A```까지 1씩 감소시켜 나가면서 실행하기
```kotlin
for (i in B downTo A) {
  // code to execute
}
```
 * ```B```부터 시작하여 ```A```까지 ```interval```씩 감소시켜 나가면서 실행하기
```kotlin
for (i in B downTo A step interval) {
  // code to execute
}
```

 * ```A```부터 시작하여 ```B-1```까지 증가시켜 나가면서 실행하기
   * java의 ```for(int i = A; i < B; i++)```과 같은 의미
```kotlin
for (i in A until B) {
  // code to execute
}
```

 * ranges를 이용한 ```progression``` 만들기
   * ```A```부터 시작하여 ```B```까지의 값을 저장한 ```progression``` ```[A, A+1, ..., B]``` : ```(A..B)```
   * ```A```부터 시작하여 ```B```까지의 값을 ```interval``` 간격으로 저장한 ```progression``` ```[A, A+interval, ..., B]``` : ```(A..B step interval)```
   * ```B```부터 시작하여 ```A```까지의 값을 ```interval```씩 감소시킨 값들을 저장한 ```progression``` ```[B, B-interval, ..., A]``` : ```(B downTo A step interval)```
   * ```for (i in X)``` 형태의 구문은 <b>```progression X```에 있는 값에 대해 코드를 차례대로 수행</b>하는 것임을 알 수 있다. 
   * 이때의 progression의 자료형은 ```IntProgression```

## 예제 코드
```kotlin
fun main() {
    
    // 1부터 10까지 2 간격으로 출력
    for(i in 1..10 step 2) {
        print(String.format("%d ", i))
    }
    println("")
    
    // 1부터 20까지 5 간격으로 출력
    for(i in 1..20 step 5) {
        print(String.format("%d ", i))
    }
    println("")
    
    // 10부터 시작하여 1까지 1씩 감소시켜 가면서 출력
    for(i in 10 downTo 1) {
        print(String.format("%d ", i))
    }
    println("")
    
    // 10부터 시작하여 1까지 3씩 감소시켜 가면서 출력
    for(i in 10 downTo 1 step 3) {
        print(String.format("%d ", i))
    }
    println("")
    
    // 1부터 시작하여 9(=10-1)까지 증가시켜 나가면서 실행하기
    for(i in 1 until 10) {
        print(String.format("%d ", i))
    }
    println("")
    
    // 5부터 시작하여 30까지의 값을 4 간격으로 저장한 progression
    var pg:IntProgression = (5..30 step 4)
    print(pg) // [5, 9, 13, ..., 29] 이므로 (5..29 step 4)가 됨
}
```

## 실행 결과
```
1 3 5 7 9 
1 6 11 16 
10 9 8 7 6 5 4 3 2 1 
10 7 4 1 
1 2 3 4 5 6 7 8 9 
5..29 step 4
```
