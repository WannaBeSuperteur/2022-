## Filter (Collection)
### Filter (Collection) - Filter
```kotlin
list.filter {
    condition
}
```
위 함수는 리스트 또는 콜렉션 ```list```에서 조건 ```condition```을 만족시키는 항목들을 반환한다.

### Filter (Collection) - FilterNot
```kotlin
list.filterNot {
    condition
}
```
위 함수는 리스트 또는 콜렉션 ```list```에서 조건 ```condition```을 **만족시키지 않는** 항목들을 반환한다.

### Filter (Collection) - FilterIndexed
```kotlin
list.filterIndexed { index, value ->
    conditionOfIndexAndS
}
```
위 함수는 리스트 또는 콜렉션 ```list```에서 인덱스를 이용하여 원하는 항목을 반환한다. 즉, 인덱스 ```index```와 값 ```value```가 조건 ```conditionOfIndexAndS```을 만족시키는 항목들을 반환한다.

### Filter (Collection) - FilterNotNull
```kotlin
list.filterNotNull()
```
위 함수는 리스트 또는 콜렉션 ```list```에서 ```null``` 값들을 모두 제거하고 남은 항목들을 반환한다.

```kotlin
list.filterNotNull().filter {
    condition
}
```
위와 같이 활용할 수 있다.

### Filter (Collection) - FilterIsInstance
```kotlin
list.filterIsInstance<DataType>()
```
위 함수는 리스트 또는 콜렉션 ```list```에서 인스턴스의 자료형이 ```DataType```인 항목들을 반환한다.

```kotlin
list.filterIsInstance<DataType>().filter {
    condition
}
```
위와 같이 활용할 수 있다.

### Filter (Collection) - partition
```kotlin
val (m, r) = list.partition {
    condition
}
```
위 함수는 리스트 또는 콜렉션 ```list```에서 조건 ```condition```을 만족시키는, 즉 matching되는 원소들을 ```m```에, 그렇지 않은 나머지 원소들을 ```r```에 저장한다.

## Example
### Example 1
```kotlin
fun main() {
    val list1 = listOf("one", "two", "three", "four", "five", "six", "seven", "eleven")
    val list2 = listOf(1, "one", 2, "two", 3, "three", null, 4, "four", 5, "five", null, 6, "six")
    
    // o로 시작
    val filterTest = list1.filter { it.startsWith("o") }
    println("1. filter test = " + filterTest)
    
    // 길이가 3인 것 제외
    val filterNotTest = list1.filterNot { it.length == 3 }
    println("2. filterNot test = " + filterNotTest)
    
    // 인덱스가 5보다 작고 길이도 5보다 작음
    val filterIndexedTest = list1.filterIndexed { index, value ->
     	index < 5 && value.length < 5   
    }
    println("3. filterIndexed test = " + filterIndexedTest)
    
    // null이 아닌 것
    val filterNotNullTest = list2.filterNotNull()
    println("4. filterNotNull test = " + filterNotNullTest)
    
    // null이 아닌 것 중에서 String
    val filterNotNullIsInstanceStringTest = list2.filterNotNull().filterIsInstance<String>()
    println("5. filterNotNull and IsInstance String test = " + filterNotNullIsInstanceStringTest)
    
    // null이 아닌 것 중에서 Int
    val filterNotNullIsInstanceIntTest = list2.filterNotNull().filterIsInstance<Int>()
    println("6. filterNotNull and IsInstance Int test = " + filterNotNullIsInstanceIntTest)
    
    // null이 아닌 것 중에서 String, 그 중에서 길이가 4 이상인 것
    val filterNotNullStringLongTest = list2.filterNotNull().filterIsInstance<String>().filter {
        it.length >= 4
    }
    println("7. filterNotNull and IsInstance String (length >= 4) test = " + filterNotNullStringLongTest)
    
    // 길이가 짝수인 것과 그렇지 않은 것으로 파티셔닝
    val (partitionTest_m, partitionTest_r) = list1.partition { it.length % 2 == 0 }
    println("8. partitionTest_m = " + partitionTest_m + ", partitionTest_r = " + partitionTest_r)
}
```

## Output
### Output 1
```kotlin
1. filter test = [one]
2. filterNot test = [three, four, five, seven, eleven]
3. filterIndexed test = [one, two, four, five]
4. filterNotNull test = [1, one, 2, two, 3, three, 4, four, 5, five, 6, six]
5. filterNotNull and IsInstance String test = [one, two, three, four, five, six]
6. filterNotNull and IsInstance Int test = [1, 2, 3, 4, 5, 6]
7. filterNotNull and IsInstance String (length >= 4) test = [three, four, five]
8. partitionTest_m = [four, five, eleven], partitionTest_r = [one, two, three, six, seven]
```
