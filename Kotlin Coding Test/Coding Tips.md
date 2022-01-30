## 최종 업데이트
 * 2022.01.30

## data type 변환
 * <b>문자열의 각 문자를 정수로 변환하기</b>
   * ```s[i].toString().toInt()```
   * 예제: Programmers - <b>3진법 뒤집기</b> solution code
     ```kotlin 
     for (i in 0..s.length-1) {
          if (i == 0) result = s[i].toString().toInt()
          else result = result * 3 + s[i].toString().toInt()
     }
     ```

 * <b>주어진 값으로 Array 만들기</b>
   * ```값1```, ```값2```, ... 를 이용하여 ```IntArray``` 자료형의 배열 만들기: ```intArrayOf(값1, 값2, ...)```
   * 예제: Programmers - <b>이진 변환 반복하기</b> solution code
     ```kotlin 
     var answer: IntArray = intArrayOf(count, removedZeros)
     return answer
     ```

## 수학
 * <b>최댓값, 최솟값 구하기</b>
   * ```a```, ```b``` 중 최댓값: ```Math.max(a, b)```
   * ```a```, ```b``` 중 최솟값: ```Math.min(a, b)```
   * 예제: Baekjoon - <b>LAGNO</b> solution code
     ```kotlin
     result = Math.max(result, simulate(board, i, j))
     ```
   * 예제: Programmers - <b>최소직사각형</b> solution code
     ```kotlin 
     for (size in sizes) {
         max_1st = Math.max(max_1st, Math.max(size[0], size[1]))
         max_2nd = Math.max(max_2nd, Math.min(size[0], size[1]))
     }
     ```
     
 * <b>절댓값 구하기</b>
   * ```Math.abs(x)```
   * 예제: Programmers - <b>N-queen</b> solution code
     ```kotlin
     if (Math.abs(i - j) == Math.abs(queens[i] - queens[j])) return false
     ```

 * <b>상용로그(log10) 구하기</b>
   * ```Math.log10(n.toDouble()).toInt()```
   * 예제: Programmers - <b>문자열 압축</b> solution code
     ```kotlin
     import kotlin.math.*
     fun numbers(num: Int): Int {
         return log10(num.toDouble()).toInt() + 1
     }
     ```

 * <b>제곱 (power) 구하기</b>
   * ```x```의 ```y```제곱 구하기: ```(x).pow(y).toInt()```
   * 예제: Programmers - <b>예상 대진표</b> solution code
     ```kotlin
     import kotlin.math.*
     fun divideByPowOf2(a: Int, power: Int) = a / (2.0).pow(power).toInt()
     ```
   * 예제: Programmers - <b>타겟 넘버</b> solution code
     ```kotlin
     // gray code의 각 case를 검토 (완전탐색)
     val power = (2.0).pow(n).toInt()
     ```
     
 * <b>제곱근 (sqrt) 구하기</b>
   * ```x```의 제곱근 구하기: ```sqrt(x.toDouble()).toInt()```
   * 예제: Programmers - <b>카펫</b> solution code
     ```kotlin
     import kotlin.math.*
     val blocks = brown + yellow
     val maxHeight = sqrt(blocks.toDouble()).toInt()
     ```

## 문자열
 * <b>문자열이 empty인지 확인</b>
   * ```null``` 또는 empty인지 확인: ```s.isNullOrEmpty()``` 
   * empty인지 확인: ```s.isEmpty()```
   * 예제: Programmers - <b>JadenCase 문자열 만들기</b> solution code
     ```kotlin
     if (s.isNullOrEmpty()) return ""
     ```
   * 예제: Programmers - <b>괄호 회전하기</b> solution code
     ```kotlin
     return stack.isEmpty()
     ```
   * 예제: Programmers - <b>신규 아이디 추천</b> solution code
     ```kotlin
     if (answer.isEmpty()) answer = "a"
     ```
     
 * <b>문자열을 대문자 또는 소문자로 바꾸기</b>
   * 대문자로 변환: ```s.toUpperCase()```
   * 소문자로 변환: ```s.toLowerCase()```
   * 예제: Programmers - <b>JadenCase 문자열 만들기</b> solution code
     ```kotlin
     return s.substring(0, 1).toUpperCase() + s.substring(1).toLowerCase()
     ```
     
 * <b>문자열의 부문자열 (substring) 반환</b>
   * index ```i```부터 ```j-1```까지의 부문자열 반환: ```s.substring(i, j)```
   * index ```i```부터 끝까지의 부문자열 반환: ```s.substring(i)```
   * 예제: Programmers - <b>JadenCase 문자열 만들기</b> solution code
     ```kotlin
     return s.substring(0, 1).toUpperCase() + s.substring(1).toLowerCase()
     ```
   * 예제: Programmers - <b>가장 큰 수</b> solution code
     ```kotlin
     fun repeat4(num: Int): String = (num.toString().repeat(4)).substring(0, 4)
     ```
   * 예제: Programmers - <b>괄호 회전하기</b> solution code
     ```kotlin
     for (n in 0..s.length-1) {
         if (check(s.substring(n) + s.substring(0, n))) result++
     }
     ```
     
 * <b>문자열을 구분 기호를 기준으로 나누기</b>
   * ```s.split(기호1, 기호2, ...)``` (여러 개의 구분 기호 사용 가능)
   * 예제: Programmers - <b>JadenCase 문자열 만들기</b> solution code
     ```kotlin
     val s_split = s.split(" ")
     ```
   * 예제: Programmers - <b>단어 변환</b> solution code
     ```kotlin
     current = dequeued.split(",")[0]
     depth = dequeued.split(",")[1].toInt()
     ```

 * <b>문자열의 마지막 n글자 제거하기</b>
   * ```s.dropLast(n)```
   * 예제: Programmers - <b>JadenCase 문자열 만들기</b> solution code
     ```kotlin
     return answer.dropLast(1)
     ```
   * 예제: Programmers - <b>신규 아이디 추천</b> solution code
     ```kotlin
     // 4
     if (answer[0] == '.') answer = answer.substring(1)
     if (answer.length > 0 && answer[answer.length - 1] == '.') answer = answer.dropLast(1)
     ```
     
 * <b>문자열을 n번 반복하기</b>
   * ```s.repeat(n)```
   * 예제: Programmers - <b>가장 큰 수</b> solution code
     ```kotlin
     // 4번 반복한 것의 첫 4개의 숫자를 반환
     fun repeat4(num: Int): String = (num.toString().repeat(4)).substring(0, 4)
     ```
     
 * <b>문자열이 특정 글자/문자열로 시작/끝나는지 확인하기</b>
   * 특정 글자/문자열(```substr```)로 시작: ```s.startsWith(substr)```
   * 특정 글자/문자열로 끝: ```s.endsWith(substr)```
   * 예제: Programmers - <b>괄호 회전하기</b> solution code
     ```kotlin
     ')' -> {
         if(!stack.endsWith("(")) return false
         stack = stack.dropLast(1)
     }
     ```

 * <b>문자열 간 동등 비교</b>
   * ```str0.equals(str1)```
   * 예제: Programmers - <b>단어 변환</b> solution code
     ```kotlin
     // 목표 단어에 도달한 경우 종료
     if (current.equals(target)) return depth
     ```
   * 예제: Programmers - <b>문자열 압축</b> solution code
     ```kotlin
     if(s.substring(j, j+i).equals(s.substring(j-i, j))) {
         currentCount++
     } else {
     ```

 * <b>문자열에서 ```A```를 ```B```로 바꾸기</b>
   * ```str.replace(A, B)```
   * 예제: Programmers - <b>신규 아이디 추천</b> solution code
     ```kotlin
     while (true) {
         answer = answer.replace("..", ".")
         if (answer.length == old_len) break
         old_len = answer.length
     }
     ```
   * 예제: Programmers - <b>이진 변환 반복하기</b> solution code
     ```kotlin
     while(var_s.length > 1) {
         val zeros = var_s.length - var_s.replace("0", "").length
         var_s = binary(var_s.length - zeros)
     ```
     
### 정규 표현식
 * <b>정규 표현식을 이용하여 특정 문자만 남기기</b>
   * ```str.replace(REGEX.toRegex(), "")```, ```REGEX```는 ```"[^(특정문자)]"``` 
   * 예제: Programmers - <b>신규 아이디 추천</b> solution code
     ```kotlin
     answer = answer.replace("[^0-9a-z_.-]".toRegex(), "")
     ```
     
## 배열
 * <b>배열 정렬하기</b>
   * ```IntArray```의 오름차순 정렬: ```arr.sorted()```
   * ```IntArray```의 내림차순 정렬: ```arr.sortedDescending()```
   * 예제: Programmers - <b>H-index</b> solution code
     ```kotlin
     val sortedCitations = citations.sortedDescending().toMutableList()
     ```
   * 예제: Programmers - <b>가장 큰 수</b> solution code
     ```kotlin
     sortedNumbers = sortedNumbers.sortedDescending().toMutableList()
     ```
     
 * <b>배열의 원소의 개수</b>
   * ```arr.size``` 또는 ```arr.count()```
   * 예제: Programmers - <b>H-index</b> solution code
     ```kotlin
     val n = sortedCitations.size
     ```
   * 예제: Programmers - <b>네트워크</b> solution code
     ```kotlin
     for (j in 0..computers[i].count()-1) {
         if (computers[i][j] == 1) {
             adjacent.add(j)
         }
     }
     ``` 
     ``` kotlin
     // stack의 top에 있는 노드를 pop
     val top = stack[stack.count()-1]
     stack.remove(top)
     ```
     
 * <b>배열이 empty인지 확인</b>
   * ```arr.isEmpty()```
   * 예제: Programmers - <b>네트워크</b> solution code
     ```kotlin
     // stack이 비어 있으면 종료
     if (stack.isEmpty()) break
     ```
     
 * <b>배열에서 특정 항목 제거</b>
   * ```arr.remove(item)```
   * 예제: Programmers - <b>단어 변환</b> solution code
     ```kotlin
     // queue에서 가장 처음 항목을 꺼냄
     val dequeued = queue[0]
     queue.remove(dequeued)
     ```

 * <b>배열에서 특정 위치의 항목 제거</b>
   * ```arr.removeAt(i)```
   * 예제: Programmers - <b>수식 최대화</b> solution code
     ```kotlin
     values.removeAt(ind+1)
     operators.removeAt(ind)
     ```
   * 예제: Programmers - <b>크레인 인형뽑기 게임</b> solution code
     ```kotlin
     if(stack[sc-2] == stack[sc-1]) {
         stack.removeAt(sc-1)
         stack.removeAt(sc-2)
         answer += 2
     }
     ```
     
 * <b>리스트<클래스> 형식의 리스트를 특정 기준으로 정렬</b>
   * ```list.sortBy{it.valueName}```
   * 예제: Programmers - <b>섬 연결하기</b> solution code
     ```kotlin
     data class costData (var A: Int, var B: Int, var cost: Int)
     val newCosts = mutableListOf<costData>()
  
     // 가장 작은 cost 순으로 찾기 위해 costs 배열을 cost 기준으로 정렬하기
     newCosts.sortBy{it.cost}
     ```
  
 * <b>2차원 배열 생성하기</b>
   * 행이 ```rows```개, 열이 ```columns```개인 2차원 배열 생성: ```Array(rows, {IntArray(columns)})```
   * 예제: Programmers - <b>행렬 테두리 회전하기</b> solution code
     ```kotlin
     // 배열 초기화
     val arr = Array(rows, {IntArray(columns)})
     for (i in 0..rows-1) {
         for (j in 0..columns-1) {
             arr[i][j] = i * columns + j + 1
         }
     }
     ```
   * 예제: Programmers - <b>행렬의 곱셈</b> solution code
     ```kotlin
     val I = arr1.size
     val J = arr2.size
     val K = arr2[0].size
        
     var answer = Array(I, {IntArray(K)})
     ```
