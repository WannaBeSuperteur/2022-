# mapOf
## 설명
### mapOf 기본
 * 다음과 같이 어떤 한 요소와 다른 요소를 대응시키는 ```map```을 정의할 수 있음.
   * ```val MAP_NAME = mapOf(VALUE_A_0 to VALUE_B_0, VALUE_A_1 to VALUE_B_1, ...)```
   * 이것은 값 ```VALUE_A_0```을 값 ```VALUE_B_0```에 대응시키고, ```VALUE_A_1```을 ```VALUE_B_1```에 대응시키고, ..., 이런 식으로 값들을 대응시키는 것을 의미함.
 * ```keys```와 ```values```
   * ```MAP_NAME.keys```: map ```MAP_NAME```에서 ```VALUE_A_0```, ```VALUE_A_1```, ...에 해당하는 것들, 즉 ```key```들을 저장한 배열
   * ```MAP_NAME.values```: map ```MAP_NAME```에서 ```VALUE_B_0```, ```VALUE_B_1```, ...에 해당하는 것들, 즉 ```value```들을 저장한 배열
 * key에 대한 value 반환하기
   * ```MAP_NAME.get(key)```: map ```MAP_NAME```에서 key (```VALUE_A_X```에 해당) ```key```에 대한 value (```VALUE_B_X```에 해당) 값을 반환
 * 각 key에 대한 value를 차례대로 출력하기
```kotlin
for (i in MAP_NAME.keys) {
   println("key " + i + " value " + MAP_NAME.get(i))
}
```

### mutableMapOf
 * ```mapOf``` 대신 ```mutableMapOf```를 사용하면, 값을 추가할 수 있는 map으로 정의됨.
   * ```val MAP_NAME = mutableMapOf(VALUE_A_0 to VALUE_B_0, VALUE_A_1 to VALUE_B_1, ...)```
 * ```mutableMapOf```로 정의된 map에 항목 추가하기 (key: ```KEY```, value: ```VALUE```):
   * ```MAP_NAME.put(KEY, VALUE)```

### 항목이 추가/제거된 map 생성
 * 원래 map이 ```MAP_NAME```이고, 여기에서 key가 ```KEY```인 항목을 제거한 새로운 map ```NEW_MAP_NAME```을 반환하는 방법은 다음과 같음.
   * ```val NEW_MAP_NAME = MAP_NAME - KEY```

## 예제 코드
```kotlin
fun main() {
  
  // listOf로 생성된 list와 마찬가지로 map 역시 기본적으로 값 추가 불가능
  val GPA = mapOf(4 to "A", 3 to "B", 2 to "C", 1 to "D", 0 to "F")
  println("GPA.keys   = " + GPA.keys.toString())
  println("GPA.values = " + GPA.values.toString())
  
  println("\n ==== GPA (mapOf) ==== ")
  
  for (i in GPA.keys) {
      println("grade " + GPA.get(i) + " -> numeric = " + i)
  }
  
  // 값을 추가할 수 있는 map은 mutableMapOf를 이용
  val mutableGPA = mutableMapOf(4 to "A", 3 to "B", 2 to "C", 1 to "D", 0 to "F")
  
  // mutableGPA에 key=5, value="S"인 새로운 항목 추가  
  mutableGPA.put(5, "S")
  
  println("\n ==== GPA (mutableMapOf) ==== ")
  
  for (i in mutableGPA.keys) {
      println("grade " + mutableGPA.get(i) + " -> numeric = " + i)
  }
  
  // map에 +, -를 사용하여 항목이 추가/제거된 map 생성
  // GPA_remove_F는 GPA에서 key가 0인 항목 (value: "F") 을 제거한 후의 map
  val GPA_remove_F = GPA - 0
  
  println("\n ==== GPA_remove_F (mapOf) ==== ")
  
  for (i in GPA_remove_F.keys) {
      println("grade " + GPA_remove_F.get(i) + " -> numeric = " + i)
  }
}
```

## 실행 결과
```
GPA.keys   = [4, 3, 2, 1, 0]
GPA.values = [A, B, C, D, F]

 ==== GPA (mapOf) ==== 
grade A -> numeric = 4
grade B -> numeric = 3
grade C -> numeric = 2
grade D -> numeric = 1
grade F -> numeric = 0

 ==== GPA (mutableMapOf) ==== 
grade A -> numeric = 4
grade B -> numeric = 3
grade C -> numeric = 2
grade D -> numeric = 1
grade F -> numeric = 0
grade S -> numeric = 5

 ==== GPA_remove_F (mapOf) ==== 
grade A -> numeric = 4
grade B -> numeric = 3
grade C -> numeric = 2
grade D -> numeric = 1
```
