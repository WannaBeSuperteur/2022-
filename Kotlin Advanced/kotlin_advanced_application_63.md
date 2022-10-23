## 개요
문장들의 집합 ```sentences```로부터 주어진 질문의 집합 ```questions```의 각 질문에 답하는 함수 ```provideAnswer(questions, sentences)```를 작성하시오.

단, ```sentences```의 각 문장은 다음과 같은 형식 중 하나이다.
* ```~은/는/이/가 ~이다/~이 아니다```
* ```~은/는 ~을/를 ~한다/~하지 않는다```
* ```~은/는 ~이/가 ~이다```
* ```~은/는 ~에 있다/있지 않다```
* ```~은/는 ~을/를 하고 있다```
* ```~은/는 ~에게 ~을/를 주었다```

또한, ```questions```의 각 질문은 다음과 같은 형식 중 하나이다.
* ```~은/는/이/가 ~입니까?```
* ```~은/는 ~을/를 ~합니까?```
* ```~은/는 ~이/가 ~입니까?```
* ```~은/는 어디에 있습니까?```
* ```~은/는 무엇을 하고 있습니까?```
* ```~은/는 ~을/를 누구에게 주었습니까?```
* ```~은/는 누구에게 ~을/를 주었습니까?```
* ```~은/는 ~에게 무엇을 주었습니까?```

각 질문에 대해, 그 답이 ```예```이면 ```true```, ```아니오```이면 ```false```, 그 외이면 해당 내용으로 답하시오.

## 코드 및 실행 결과
### 코드
```kotlin
// ~은/는/이/가/을/를 중 원하는 것들을 '|' 로 바꾸기
fun replaceSuffix(string: String, suffix: String): String {
    return (0 until suffix.length).fold (string) { acc, e ->
        acc.replace("${suffix[e]} ", "|")
    }
}

// map에 새로 put하거나 리스트에 항목 추가하기
fun putIntoMap(key: String, value: String, mapToPut: MutableMap<String, MutableList<String>>) {
    if (key in mapToPut) {
        mapToPut.get(key)!!.add(value)
    } else {
        mapToPut.put(key, mutableListOf(value))
    }
}

// 문장에서 정보 찾기
fun getInfoFromSentences(sentences: List<String>): List<Map<String, MutableList<String>>> {
    
    // 정보 : ~은/는/이/가 ~이다/~이 아니다, ..., ~은/는 ~에게 ~을/를 주었다
    val mapIs = mutableMapOf<String, MutableList<String>>()
    val mapIsNot = mutableMapOf<String, MutableList<String>>()
    val mapDo = mutableMapOf<String, MutableList<String>>()
    val mapDoNot = mutableMapOf<String, MutableList<String>>()
    val mapProperty = mutableMapOf<String, MutableList<String>>()
    val mapWhere = mutableMapOf<String, MutableList<String>>()
    val mapWhereNot = mutableMapOf<String, MutableList<String>>()
    val mapDoing = mutableMapOf<String, MutableList<String>>()
    val mapGive = mutableMapOf<String, MutableList<String>>()
    
    // 문장에서 정보를 찾아서 해당하는 map에 추가하기
    sentences.forEach { s ->
        
        // ~은 ~이다 / ~은 ~이 ~이다 공통 일치 방지
        val isProperty = s.matches(Regex(".*[은는] .*[이가] .*이다"))
        
        // ~은/는/이/가 ~이다, ~은/는/이/가 ~이 아니다
        if (
            (s.matches(Regex(".*[은는이가] .*이다")) || s.matches(Regex(".*[은는이가] .*[이가] 아니다"))) &&
            !isProperty
        ) {
            val isNot = s.takeLast(3) == "아니다"
            
            val newS = if (isNot) {
                replaceSuffix(s.dropLast(5), "은는이가")
            } else {
                replaceSuffix(s.dropLast(2), "은는이가")
            }
            
            val left = newS.substringBefore("|")
            val right = newS.substringAfter("|")
            
            if (isNot) {
                putIntoMap(left, right, mapIsNot)
            } else {
                putIntoMap(left, right, mapIs)
            }
        }
        
        // ~은/는 ~을/를 ~한다/~하지 않는다
        else if (s.matches(Regex(".*[은는] .*[을를] .*한다")) || s.matches(Regex(".*[은는] .*[을를] .*하지 않는다"))) {
            val isNot = s.takeLast(3) == "않는다"
            
            val newS = if (isNot) {
                replaceSuffix(s.dropLast(6), "은는을를")
            } else {
                replaceSuffix(s.dropLast(2), "은는을를")
            }
            
            val left = newS.split("|")[0]
            val middle = newS.split("|")[1]
            val right = newS.split("|")[2]
            
            if (isNot) {
                putIntoMap(left, "${middle}|${right}", mapDoNot)
            } else {
                putIntoMap(left, "${middle}|${right}", mapDo)
            }
        }
        
        // ~은/는 ~이/가 ~이다
        else if (isProperty) {
            val newS = replaceSuffix(s.dropLast(2), "은는이가")
            
            val left = newS.split("|")[0]
            val middle = newS.split("|")[1]
            val right = newS.split("|")[2]
            
            putIntoMap(left, "${middle}|${right}", mapProperty)
        }
        
        // ~은/는 ~에 있다/있지 않다
        else if (s.matches(Regex(".*[은는] .*에 있다")) || s.matches(Regex(".*[은는] .*에 있지 않다"))) {
            val isNot = s.takeLast(2) == "않다"
            
            val newS = if (isNot) {
                replaceSuffix(s.dropLast(7), "은는")
            } else {
                replaceSuffix(s.dropLast(4), "은는")
            }
            
            val left = newS.substringBefore("|")
            val right = newS.substringAfter("|")
            
            if (isNot) {
                putIntoMap(left, right, mapWhereNot)
            } else {
                putIntoMap(left, right, mapWhere)
            }
        }
        
        // ~은/는 ~을/를 하고 있다
        else if (s.matches(Regex(".*[은는] .*[을를] 하고 있다"))) {
            val newS = replaceSuffix(s.dropLast(7), "은는")
            
            val left = newS.substringBefore("|")
            val right = newS.substringAfter("|")
            
            putIntoMap(left, right, mapDoing)
        }
        
        // ~은/는 ~에게 ~을/를 주었다
        else if (s.matches(Regex(".*[은는] .*에게 .*[을를] 주었다"))) {
            val newS = replaceSuffix(s.dropLast(5), "은는").replace("에게 ", "|")
            
            val left = newS.split("|")[0]
            val middle = newS.split("|")[1]
            val right = newS.split("|")[2]
            
            putIntoMap(left, "${middle}|${right}", mapGive)
        }
    }
    
    return listOf(mapIs, mapIsNot, mapDo, mapDoNot, mapProperty, mapWhere, mapWhereNot, mapDoing, mapGive)
}

// 찾은 정보를 이용하여 질문에 답변하기
fun findAnswerUsingInfo(questions: List<String>, infoMap: List<Map<String, MutableList<String>>>): List<String> {
    return questions.map {
        
        // 마지막 물음표 제거
        val q = it.dropLast(1)
        var answer = ""
        
        // ~은 ~이다 / ~은 ~이 ~이다 공통 일치 방지 및 기타 중복된 값 할당 처리용
        val isProperty = q.matches(Regex(".*[은는] .*[이가] .*입니까"))
        val isLastCase = q.matches(Regex(".*[은는] .*에게 무엇을 주었습니까"))
        
        // ~은/는/이/가 ~입니까?
        if (q.matches(Regex(".*[은는이가] .*입니까")) && !isProperty) {
            val newQ = replaceSuffix(q.dropLast(3), "은는이가")
            answer = "cannot find info type 0"
            
            val left = newQ.substringBefore("|")
            val right = newQ.substringAfter("|")
            
            // 덮어쓰므로, not을 먼저 탐색해야 true가 false보다 우선함
            listOf(1, 0).forEach {
                if (infoMap[it].containsKey(left)) {
                    if (infoMap[it].get(left)!!.contains(right)) {
                        answer = if (it == 0) { "true" } else { "false" }
                    }
                }
            }
        }
        
        // ~은/는 ~을/를 ~합니까?
        // ~은/는 ~이/가 ~입니까? (둘 다 일치하는 것은 불가능)
        else if (q.matches(Regex(".*[은는] .*[을를] .*합니까")) || isProperty) {
            val newQ = replaceSuffix(q.dropLast(3), if (isProperty) { "은는이가" } else { "은는을를" })
            answer = "cannot find info type ${if (isProperty) { 2 } else { 1 }}"
            
            val left = newQ.split("|")[0]
            val middle = newQ.split("|")[1]
            val right = newQ.split("|")[2]
            
            val isIndex = if (isProperty) { 4 } else { 2 }
            val isNotIndex = if (isProperty) { null } else { 3 }
            
            listOf(isNotIndex, isIndex).forEach {
                if (it != null && infoMap[it].containsKey(left)) {
                    if (infoMap[it].get(left)!!.contains("${middle}|${right}")) {
                        answer = if (it == isIndex) { "true" } else {
                            if (isProperty) { answer } else { "false "}
                        }
                    }
                }
            }
        }
        
        // ~은/는 어디에 있습니까?
        else if (q.matches(Regex(".*[은는] 어디에 있습니까"))) {
            val item = q.dropLast(10)
            answer = "cannot find info type 3"
            
            listOf(6, 5).forEach {
                if (infoMap[it].containsKey(item)) {
                    val answerString = infoMap[it].get(item)!!.joinToString(" 또는 ")
                    answer = if (it == 5) {
                        answerString
                    } else {
                        "${answerString}에 있지 않음"
                    }
                }
            }
        }
        
        // ~은/는 무엇을 하고 있습니까?
        else if (q.matches(Regex(".*[은는] 무엇을 하고 있습니까"))) {
            val item = q.dropLast(13)
            
            answer = if (infoMap[7].containsKey(item)) {
                infoMap[7].get(item)!!.joinToString("와(과) ")
            } else {
                "cannot find info type 4"
            }
        }
        
        // ~은/는 ~을/를 누구에게 주었습니까?
        // ~은/는 누구에게 ~을/를 주었습니까?
        // ~은/는 ~에게 무엇을 주었습니까?
        else if (
            q.matches(Regex(".*[은는] .*[을를] 누구에게 주었습니까")) || q.matches(Regex(".*[은는] 누구에게 .*[을를] 주었습니까")) || isLastCase
        ) {
            val newQ = if (isLastCase) {
                replaceSuffix(q.dropLast(12), "은는")
            } else {
                // '누구에게'를 제거하여 '~은/는 ~을/를' 형태로 통합
                replaceSuffix(q.dropLast(5), "은는을를").replace("누구에게 ", "")
            }
            answer = "cannot find info type ${if (isLastCase) { 6 } else { 5 }}"
            
            val left = newQ.split("|")[0]
            val right = newQ.split("|")[1]
            
            // 일치하는 항목을 모두 찾아서 목록 형태로 답을 반환
            val answerList = mutableListOf<String>()
            if (infoMap[8].containsKey(left)) {
                val matched = infoMap[8].get(left)!!
                
                matched.forEach {
                    if (isLastCase) { // ~은/는 ~에게 무엇을 주었습니까?
                        if (it.substringBefore("|") == right) {
                            answerList.add(it.substringAfter("|"))
                        }
                    } else { // '~은/는 ~을/를 누구에게 주었습니까?' 또는 '~은/는 누구에게 ~을/를 주었습니까?'
                        if (it.substringAfter("|") == right) {
                            answerList.add(it.substringBefore("|"))
                        }
                    }
                }
            }
            
            if (answerList.isNotEmpty()) {
                answer = answerList.joinToString("와(과) ")
            }
        }
        
        // 인식할 수 없는 질문
        else {
            answer = "cannot parse question"
        }
        
        answer
    }
}

// 질문에 답변하기
fun provideAnswer(questions: List<String>, sentences: List<String>) {
    
    // 문장에서 정보 찾기
    val infoMap = getInfoFromSentences(sentences)
    val infoMapTitles = listOf(
        "~은 ~이다", "~은 ~이 아니다", "~은 ~을 한다", "~은 ~을 하지 않는다",
        "~은 ~이 ~이다", "~은 ~에 있다", "~은 ~에 있지 않다", "~은 ~을 하고 있다", "~은 ~에게 ~을 주었다"
    )
    
    (0..8).forEach {
        println("${it + 1}. ${infoMapTitles[it]}\n -> ${infoMap[it]}\n")
    }
    
    // 찾은 정보를 이용하여 답하기
    val answers = findAnswerUsingInfo(questions, infoMap)
    
    println("최종 답변 :")
    (0 until questions.size).forEach {
        println("${it + 1} : [Q] ${questions[it]} [A] ${answers[it]}")
    }
}

fun main() {
    
    // 임시 문장 목록
    val tempSentences = listOf(
        "코틀린은 멋진 언어이다",
        "파이썬이 나쁜 언어가 아니다",
        "파이썬이 나쁜 기술이 아니다",
        "자바는 최고의 언어이다",
        "C 언어가 비효율적인 언어가 아니다",
        "코틀린은 A 함수를 빠르게 처리한다",
        "자바는 B 함수를 잘 처리한다",
        "코틀린은 C 기능을 빠르게 처리하지 않는다",
        "자바는 실행 속도가 우주급이다",
        "코틀린은 품질이 최상급이다",
        "자바 센터는 지구 어딘가에 있다",
        "자바 센터는 개발자의 나라에 있다",
        "달은 지구 어딘가에 있지 않다",
        "코틀린소프트는 외계 행성 외의 곳에 있지 않다",
        "코틀린소프트는 대한민국에 있지 않다",
        "지구는 우주에 있다",
        "김자바는 프로그래밍을 하고 있다",
        "김코틀린은 드럼 연주를 하고 있다",
        "김코틀린은 볼링을 하고 있다",
        "김자바는 김코틀린에게 프로그래밍 책을 주었다",
        "김자바는 김코틀린에게 직장 동료들과의 협업 꿀팁 책을 주었다",
        "김자바는 박코틀린에게 프로그래밍 책을 주었다",
        "김코틀린은 김파이썬에게 아메리카노를 주었다",
        "이자바는 이파이썬에게 캔커피를 주었다",
        "이파이썬은 박자바에게 레터링 케이크 선물을 주었다",
        "박파이썬은 최코틀린에게 최고의 생일 선물을 주었다",
        "김코틀린은 정자바에게 의자를 주었다"
    )
    
    // 임시 질문 목록
    val tempQuestions = listOf(
        "코틀린은 멋진 언어입니까?",
        "자바는 멋진 언어입니까?",
        "코틀린이 멋진 언어입니까?",
        "자바가 멋진 언어입니까?",
        "코틀린은 좋은 언어입니까?",
        "자바는 좋은 언어입니까?",
        "코틀린이 좋은 언어입니까?",
        "자바가 좋은 언어입니까?",
        "자바는 최고의 언어입니까?",
        "파이썬은 나쁜 언어입니까?",
        "파이썬은 좋은 언어입니까?",
        "파이썬이 나쁜 기술입니까?",
        "C 언어가 비효율적인 언어입니까?",
        "C 언어가 효율적인 언어입니까?",
        "코틀린은 A 함수를 빠르게 처리합니까?",
        "코틀린은 A 함수를 잘 처리합니까?",
        "코틀린은 B 함수를 잘 처리합니까?",
        "코틀린은 C 기능을 빠르게 처리합니까?",
        "자바는 A 함수를 빠르게 처리합니까?",
        "자바는 A 함수를 잘 처리합니까?",
        "자바는 B 함수를 잘 처리합니까?",
        "자바는 C 기능을 빠르게 처리합니까?",
        "파이썬은 A 함수를 빠르게 처리합니까?",
        "자바는 실행 속도가 우주급입니까?",
        "자바는 실행 속도가 최상급입니까?",
        "자바는 품질이 우주급입니까?",
        "자바는 품질이 최상급입니까?",
        "코틀린은 실행 속도가 우주급입니까?",
        "코틀린은 실행 속도가 최상급입니까?",
        "코틀린은 품질이 우주급입니까?",
        "코틀린은 품질이 최상급입니까?",
        "자바스크립트는 코드 퀄리티가 정상급입니까?",
        "자바 센터는 어디에 있습니까?",
        "코틀린소프트는 어디에 있습니까?",
        "달은 어디에 있습니까?",
        "지구는 어디에 있습니까?",
        "우리 은하는 어디에 있습니까?",
        "우주는 어디에 있습니까?",
        "김자바는 무엇을 하고 있습니까?",
        "김코틀린은 무엇을 하고 있습니까?",
        "최코틀린은 무엇을 하고 있습니까?",
        "이자바는 무엇을 하고 있습니까?",
        "박코틀린은 무엇을 하고 있습니까?",
        "김자바는 프로그래밍 책을 누구에게 주었습니까?",
        "이자바는 캔커피를 누구에게 주었습니까?",
        "이자바는 프로그래밍 책을 누구에게 주었습니까?",
        "최코틀린은 의자를 누구에게 주었습니까?",
        "정코틀린은 최고의 생일 선물을 누구에게 주었습니까?",
        "김자바는 누구에게 프로그래밍 책을 주었습니까?",
        "이자바는 누구에게 캔커피를 주었습니까?",
        "이자바는 누구에게 프로그래밍 책을 주었습니까?",
        "최코틀린은 누구에게 의자를 주었습니까?",
        "정코틀린은 누구에게 최고의 생일 선물을 주었습니까?",
        "김자바는 김코틀린에게 무엇을 주었습니까?",
        "김코틀린은 김파이썬에게 무엇을 주었습니까?",
        "김코틀린은 박자바에게 무엇을 주었습니까?",
        "이파이썬은 박자바에게 무엇을 주었습니까?",
        "박파이썬은 최코틀린에게 무엇을 주었습니까?",
        "최코틀린은 박파이썬에게 무엇을 주었습니까?",
        "정자바는 최코틀린에게 무엇을 주었습니까?",
        "A 함수를 코틀린은 빠르게 처리합니까?",
        "어디에 지구는 있습니까?",
        "김코틀린은 지금 무엇을 하고 있습니까?",
        "이파이썬은 누구에게 캔커피를 받았습니까?",
        "김파이썬은 김코틀린에게 무엇을 받았습니까?"
    )
    
    // 질문에 답하기
    provideAnswer(tempQuestions, tempSentences)
}
```

### 실행 결과
```kotlin
1. ~은 ~이다
 -> {코틀린=[멋진 언어], 자바=[최고의 언어]}

2. ~은 ~이 아니다
 -> {파이썬=[나쁜 언어, 나쁜 기술], C 언어=[비효율적인 언어]}

3. ~은 ~을 한다
 -> {코틀린=[A 함수|빠르게 처리], 자바=[B 함수|잘 처리]}

4. ~은 ~을 하지 않는다
 -> {코틀린=[C 기능|빠르게 처리]}

5. ~은 ~이 ~이다
 -> {자바=[실행 속도|우주급], 코틀린=[품질|최상급]}

6. ~은 ~에 있다
 -> {자바 센터=[지구 어딘가, 개발자의 나라], 지구=[우주]}

7. ~은 ~에 있지 않다
 -> {달=[지구 어딘가], 코틀린소프트=[외계 행성 외의 곳, 대한민국]}

8. ~은 ~을 하고 있다
 -> {김자바=[프로그래밍], 김코틀린=[드럼 연주, 볼링]}

9. ~은 ~에게 ~을 주었다
 -> {김자바=[김코틀린|프로그래밍 책, 김코틀린|직장 동료들과의 협업 꿀팁 책, 박코틀린|프로그래밍 책], 김코틀린=[김파이썬|아메리카노, 정자바|의자], 이자바=[이파이썬|캔커피], 이파이썬=[박자바|레터링 케이크 선물], 박파이썬=[최코틀린|최고의 생일 선물]}

최종 답변 :
1 : [Q] 코틀린은 멋진 언어입니까? [A] true
2 : [Q] 자바는 멋진 언어입니까? [A] cannot find info type 0
3 : [Q] 코틀린이 멋진 언어입니까? [A] true
4 : [Q] 자바가 멋진 언어입니까? [A] cannot find info type 0
5 : [Q] 코틀린은 좋은 언어입니까? [A] cannot find info type 0
6 : [Q] 자바는 좋은 언어입니까? [A] cannot find info type 0
7 : [Q] 코틀린이 좋은 언어입니까? [A] cannot find info type 0
8 : [Q] 자바가 좋은 언어입니까? [A] cannot find info type 0
9 : [Q] 자바는 최고의 언어입니까? [A] true
10 : [Q] 파이썬은 나쁜 언어입니까? [A] false
11 : [Q] 파이썬은 좋은 언어입니까? [A] cannot find info type 0
12 : [Q] 파이썬이 나쁜 기술입니까? [A] false
13 : [Q] C 언어가 비효율적인 언어입니까? [A] false
14 : [Q] C 언어가 효율적인 언어입니까? [A] cannot find info type 0
15 : [Q] 코틀린은 A 함수를 빠르게 처리합니까? [A] true
16 : [Q] 코틀린은 A 함수를 잘 처리합니까? [A] cannot find info type 1
17 : [Q] 코틀린은 B 함수를 잘 처리합니까? [A] cannot find info type 1
18 : [Q] 코틀린은 C 기능을 빠르게 처리합니까? [A] false 
19 : [Q] 자바는 A 함수를 빠르게 처리합니까? [A] cannot find info type 1
20 : [Q] 자바는 A 함수를 잘 처리합니까? [A] cannot find info type 1
21 : [Q] 자바는 B 함수를 잘 처리합니까? [A] true
22 : [Q] 자바는 C 기능을 빠르게 처리합니까? [A] cannot find info type 1
23 : [Q] 파이썬은 A 함수를 빠르게 처리합니까? [A] cannot find info type 1
24 : [Q] 자바는 실행 속도가 우주급입니까? [A] true
25 : [Q] 자바는 실행 속도가 최상급입니까? [A] cannot find info type 2
26 : [Q] 자바는 품질이 우주급입니까? [A] cannot find info type 2
27 : [Q] 자바는 품질이 최상급입니까? [A] cannot find info type 2
28 : [Q] 코틀린은 실행 속도가 우주급입니까? [A] cannot find info type 2
29 : [Q] 코틀린은 실행 속도가 최상급입니까? [A] cannot find info type 2
30 : [Q] 코틀린은 품질이 우주급입니까? [A] cannot find info type 2
31 : [Q] 코틀린은 품질이 최상급입니까? [A] true
32 : [Q] 자바스크립트는 코드 퀄리티가 정상급입니까? [A] cannot find info type 2
33 : [Q] 자바 센터는 어디에 있습니까? [A] 지구 어딘가 또는 개발자의 나라
34 : [Q] 코틀린소프트는 어디에 있습니까? [A] 외계 행성 외의 곳 또는 대한민국에 있지 않음
35 : [Q] 달은 어디에 있습니까? [A] 지구 어딘가에 있지 않음
36 : [Q] 지구는 어디에 있습니까? [A] 우주
37 : [Q] 우리 은하는 어디에 있습니까? [A] cannot find info type 3
38 : [Q] 우주는 어디에 있습니까? [A] cannot find info type 3
39 : [Q] 김자바는 무엇을 하고 있습니까? [A] 프로그래밍
40 : [Q] 김코틀린은 무엇을 하고 있습니까? [A] 드럼 연주와(과) 볼링
41 : [Q] 최코틀린은 무엇을 하고 있습니까? [A] cannot find info type 4
42 : [Q] 이자바는 무엇을 하고 있습니까? [A] cannot find info type 4
43 : [Q] 박코틀린은 무엇을 하고 있습니까? [A] cannot find info type 4
44 : [Q] 김자바는 프로그래밍 책을 누구에게 주었습니까? [A] 김코틀린와(과) 박코틀린
45 : [Q] 이자바는 캔커피를 누구에게 주었습니까? [A] 이파이썬
46 : [Q] 이자바는 프로그래밍 책을 누구에게 주었습니까? [A] cannot find info type 5
47 : [Q] 최코틀린은 의자를 누구에게 주었습니까? [A] cannot find info type 5
48 : [Q] 정코틀린은 최고의 생일 선물을 누구에게 주었습니까? [A] cannot find info type 5
49 : [Q] 김자바는 누구에게 프로그래밍 책을 주었습니까? [A] 김코틀린와(과) 박코틀린
50 : [Q] 이자바는 누구에게 캔커피를 주었습니까? [A] 이파이썬
51 : [Q] 이자바는 누구에게 프로그래밍 책을 주었습니까? [A] cannot find info type 5
52 : [Q] 최코틀린은 누구에게 의자를 주었습니까? [A] cannot find info type 5
53 : [Q] 정코틀린은 누구에게 최고의 생일 선물을 주었습니까? [A] cannot find info type 5
54 : [Q] 김자바는 김코틀린에게 무엇을 주었습니까? [A] 프로그래밍 책와(과) 직장 동료들과의 협업 꿀팁 책
55 : [Q] 김코틀린은 김파이썬에게 무엇을 주었습니까? [A] 아메리카노
56 : [Q] 김코틀린은 박자바에게 무엇을 주었습니까? [A] cannot find info type 6
57 : [Q] 이파이썬은 박자바에게 무엇을 주었습니까? [A] 레터링 케이크 선물
58 : [Q] 박파이썬은 최코틀린에게 무엇을 주었습니까? [A] 최고의 생일 선물
59 : [Q] 최코틀린은 박파이썬에게 무엇을 주었습니까? [A] cannot find info type 6
60 : [Q] 정자바는 최코틀린에게 무엇을 주었습니까? [A] cannot find info type 6
61 : [Q] A 함수를 코틀린은 빠르게 처리합니까? [A] cannot parse question
62 : [Q] 어디에 지구는 있습니까? [A] cannot parse question
63 : [Q] 김코틀린은 지금 무엇을 하고 있습니까? [A] cannot parse question
64 : [Q] 이파이썬은 누구에게 캔커피를 받았습니까? [A] cannot parse question
65 : [Q] 김파이썬은 김코틀린에게 무엇을 받았습니까? [A] cannot parse question
```
