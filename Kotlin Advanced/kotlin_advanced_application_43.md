## 요구사항 분석
오토마타 이론에서는 생성규칙들을 이용하여 문자열을 생성하는 것에 대해 다룬다. 생성규칙은 다음의 형태를 갖는다.
```kotlin
S -> aBa
A -> aa
B -> bA, Aa
```
여기서 ```->```의 오른쪽에 있는 대문자는 이후에 특정한 형태로 변형될 수 있는 것을 나타내고, ```->```의 왼쪽에 있는 대문자는 그것을 변형하는 규칙을 의미한다. 또한 ```->```의 오른쪽에 있는 소문자는 확정되어 앞으로 변하지 않는 문자를 나타낸다.

예를 들어 위 생성규칙을 이용하여 다음과 같이 문자열을 만들 수 있다.
```kotlin
S -> aBa -> abAa -> abaaa
S -> aBa -> aAaa -> aaaaa
```

생성규칙의 집합과 ```생성할 문자열```이 주어졌을 때, 해당 생성규칙들을 이용하여 ```생성할 문자열```을 생성하는 방법 중 아무 것이나 한 가지를 나타내시오. (단, ```생성할 문자열```은 반드시 생성 가능하며, 처음에는 ```S```에서 시작한다.)

또한, 메모리 오류 방지를 위해 생성규칙의 최대 사용 횟수는 5회로 제한된다.

### 데이터 클래스
```kotlin
// 문자열 생성 문제 상황
data class GenerationProblem (
    val rules: List<Rule>, // 생성규칙의 집합
    val str_to_generate: String // 생성할 문자열
)

// 생성규칙
data class Rule (
    val rule: String // 생성규칙을 S -> aBa 형태로 나타낸 것
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 문자열 생성 문제 상황
data class GenerationProblem (
    val rules: List<Rule>, // 생성규칙의 집합
    val str_to_generate: String // 생성할 문자열
)

// 생성규칙
data class Rule (
    val rule: String // 생성규칙을 S -> aBa 형태로 나타낸 것
)

// 문자열 생성 함수 (재귀적으로 찾기)
fun findSolution(
    currentStr: String,
    goal: String,
    ruleMap: Map<String, List<String>>,
    currentSolution: List<String>,
    finalSolution: MutableList<String>,
    count: Int
) {
    println("현재: ${currentStr}, 탐색: ${currentSolution}")
    
    // 최종 정답을 찾음
    if (finalSolution.isNotEmpty()) {
        return
    }
    
    // 메모리 오류 방지
    if (count >= 5) {
        return
    }
    
    currentStr.forEachIndexed { idx, it ->
        if (it.isUpperCase()) { // 생성규칙은 대문자에 대해서만 적용
            val matchedTransformations = ruleMap.get(it.toString())!!
            matchedTransformations.forEach { t ->
                val newStr = currentStr.substring(0, idx) + t + currentStr.substring(idx + 1)
                val newSolution = currentSolution + listOf(newStr)
                
                // 새로운 string이 목표와 일치하면 종료
                if (newStr == goal) {
                    finalSolution.clear()
                    newSolution.forEach {
                        finalSolution.add(it)
                    }
                }
                
                // 그렇지 않으면 계속 탐색
                else {
                    findSolution(newStr, goal, ruleMap, newSolution, finalSolution, count + 1)
                }
            }
        }
    }
}

// 문자열 생성 방법 표시
fun printSolution(problem: GenerationProblem) {

    // "A -> aA, Aa, b"를 Map("A" to ["aA", "Aa", "b"])와 같은 식으로 변환
    val ruleMap = mutableMapOf<String, List<String>>()
    
    val rules = problem.rules.map {
        val from = it.rule.substringBefore(" -> ")
        val to = it.rule.substringAfter(" -> ").split(", ")
        ruleMap.put(from, to)
    }
    println("생성규칙 목록 : ${ruleMap}\n")
    
    // 해결 방법 찾기
    val goal = problem.str_to_generate
    val finalSolution = mutableListOf<String>()
    findSolution("S", goal, ruleMap, listOf<String>("S"), finalSolution, 0)
    println("\n최종 정답 : ${finalSolution.joinToString(" -> ")}\n")
}

fun main() {
    
    // 문자열 생성 문제 상황의 집합
    val tempGenerationProblems = listOf<GenerationProblem> (
        GenerationProblem(listOf(Rule("S -> aBa"), Rule("A -> aa"), Rule("B -> bA, Aa")), "abaaa"),
        GenerationProblem(listOf(Rule("S -> aBa"), Rule("A -> aa"), Rule("B -> bA, Aa")), "aaaaa"),
        GenerationProblem(listOf(Rule("S -> AA"), Rule("A -> aA, Aa, b")), "abab"),
        GenerationProblem(listOf(Rule("S -> Aaa"), Rule("A -> aaA, b")), "aaaaaabaa"),
        GenerationProblem(listOf(Rule("S -> AB"), Rule("A -> aAa, a"), Rule("B -> bBb, b")), "aaabbb"),
        GenerationProblem(listOf(Rule("S -> aAa, bBb"), Rule("A -> aAa, b"), Rule("B -> bBb, A, a")), "baabaab"),
        GenerationProblem(listOf(Rule("S -> aS, Sb, A, B"), Rule("A -> aA, a"), Rule("B -> bB, b")), "abbb"),
        GenerationProblem(listOf(Rule("S -> aSa, bSb, A, B"), Rule("A -> aAa, a"), Rule("B -> bBb, b")), "bbaaabb")
    )
    
    tempGenerationProblems.forEach {
        printSolution(it)
    }
}
```

### 실행 결과
```kotlin
생성규칙 목록 : {S=[aBa], A=[aa], B=[bA, Aa]}

현재: S, 탐색: [S]
현재: aBa, 탐색: [S, aBa]
현재: abAa, 탐색: [S, aBa, abAa]
현재: aAaa, 탐색: [S, aBa, aAaa]

최종 정답 : S -> aBa -> abAa -> abaaa

생성규칙 목록 : {S=[aBa], A=[aa], B=[bA, Aa]}

현재: S, 탐색: [S]
현재: aBa, 탐색: [S, aBa]
현재: abAa, 탐색: [S, aBa, abAa]
현재: abaaa, 탐색: [S, aBa, abAa, abaaa]
현재: aAaa, 탐색: [S, aBa, aAaa]

최종 정답 : S -> aBa -> aAaa -> aaaaa

생성규칙 목록 : {S=[AA], A=[aA, Aa, b]}

현재: S, 탐색: [S]
현재: AA, 탐색: [S, AA]
현재: aAA, 탐색: [S, AA, aAA]
현재: aaAA, 탐색: [S, AA, aAA, aaAA]
현재: aaaAA, 탐색: [S, AA, aAA, aaAA, aaaAA]
현재: aaaaAA, 탐색: [S, AA, aAA, aaAA, aaaAA, aaaaAA]
현재: aaaAaA, 탐색: [S, AA, aAA, aaAA, aaaAA, aaaAaA]
현재: aaabA, 탐색: [S, AA, aAA, aaAA, aaaAA, aaabA]
현재: aaaAaA, 탐색: [S, AA, aAA, aaAA, aaaAA, aaaAaA]
현재: aaaAAa, 탐색: [S, AA, aAA, aaAA, aaaAA, aaaAAa]
현재: aaaAb, 탐색: [S, AA, aAA, aaAA, aaaAA, aaaAb]
현재: aaAaA, 탐색: [S, AA, aAA, aaAA, aaAaA]
현재: aaaAaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aaaAaA]
현재: aaAaaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAaaA]
현재: aabaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aabaA]
현재: aaAaaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAaaA]
현재: aaAaAa, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAaAa]
현재: aaAab, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAab]
현재: aabA, 탐색: [S, AA, aAA, aaAA, aabA]
현재: aabaA, 탐색: [S, AA, aAA, aaAA, aabA, aabaA]
현재: aabAa, 탐색: [S, AA, aAA, aaAA, aabA, aabAa]
현재: aabb, 탐색: [S, AA, aAA, aaAA, aabA, aabb]
현재: aaAaA, 탐색: [S, AA, aAA, aaAA, aaAaA]
현재: aaaAaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aaaAaA]
현재: aaAaaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAaaA]
현재: aabaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aabaA]
현재: aaAaaA, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAaaA]
현재: aaAaAa, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAaAa]
현재: aaAab, 탐색: [S, AA, aAA, aaAA, aaAaA, aaAab]
현재: aaAAa, 탐색: [S, AA, aAA, aaAA, aaAAa]
현재: aaaAAa, 탐색: [S, AA, aAA, aaAA, aaAAa, aaaAAa]
현재: aaAaAa, 탐색: [S, AA, aAA, aaAA, aaAAa, aaAaAa]
현재: aabAa, 탐색: [S, AA, aAA, aaAA, aaAAa, aabAa]
현재: aaAaAa, 탐색: [S, AA, aAA, aaAA, aaAAa, aaAaAa]
현재: aaAAaa, 탐색: [S, AA, aAA, aaAA, aaAAa, aaAAaa]
현재: aaAba, 탐색: [S, AA, aAA, aaAA, aaAAa, aaAba]
현재: aaAb, 탐색: [S, AA, aAA, aaAA, aaAb]
현재: aaaAb, 탐색: [S, AA, aAA, aaAA, aaAb, aaaAb]
현재: aaAab, 탐색: [S, AA, aAA, aaAA, aaAb, aaAab]
현재: aabb, 탐색: [S, AA, aAA, aaAA, aaAb, aabb]
현재: aAaA, 탐색: [S, AA, aAA, aAaA]
현재: aaAaA, 탐색: [S, AA, aAA, aAaA, aaAaA]
현재: aaaAaA, 탐색: [S, AA, aAA, aAaA, aaAaA, aaaAaA]
현재: aaAaaA, 탐색: [S, AA, aAA, aAaA, aaAaA, aaAaaA]
현재: aabaA, 탐색: [S, AA, aAA, aAaA, aaAaA, aabaA]
현재: aaAaaA, 탐색: [S, AA, aAA, aAaA, aaAaA, aaAaaA]
현재: aaAaAa, 탐색: [S, AA, aAA, aAaA, aaAaA, aaAaAa]
현재: aaAab, 탐색: [S, AA, aAA, aAaA, aaAaA, aaAab]
현재: aAaaA, 탐색: [S, AA, aAA, aAaA, aAaaA]
현재: aaAaaA, 탐색: [S, AA, aAA, aAaA, aAaaA, aaAaaA]
현재: aAaaaA, 탐색: [S, AA, aAA, aAaA, aAaaA, aAaaaA]
현재: abaaA, 탐색: [S, AA, aAA, aAaA, aAaaA, abaaA]
현재: aAaaaA, 탐색: [S, AA, aAA, aAaA, aAaaA, aAaaaA]
현재: aAaaAa, 탐색: [S, AA, aAA, aAaA, aAaaA, aAaaAa]
현재: aAaab, 탐색: [S, AA, aAA, aAaA, aAaaA, aAaab]
현재: abaA, 탐색: [S, AA, aAA, aAaA, abaA]
현재: abaaA, 탐색: [S, AA, aAA, aAaA, abaA, abaaA]
현재: abaAa, 탐색: [S, AA, aAA, aAaA, abaA, abaAa]
현재: aAaaA, 탐색: [S, AA, aAA, aAaA, aAaaA]
현재: aAaAa, 탐색: [S, AA, aAA, aAaA, aAaAa]
현재: aAab, 탐색: [S, AA, aAA, aAaA, aAab]
현재: abA, 탐색: [S, AA, aAA, abA]
현재: aAaA, 탐색: [S, AA, aAA, aAaA]
현재: aAAa, 탐색: [S, AA, aAA, aAAa]
현재: aAb, 탐색: [S, AA, aAA, aAb]
현재: AaA, 탐색: [S, AA, AaA]
현재: bA, 탐색: [S, AA, bA]
현재: AaA, 탐색: [S, AA, AaA]
현재: AAa, 탐색: [S, AA, AAa]
현재: Ab, 탐색: [S, AA, Ab]

최종 정답 : S -> AA -> aAA -> aAaA -> abaA -> abab

생성규칙 목록 : {S=[Aaa], A=[aaA, b]}

현재: S, 탐색: [S]
현재: Aaa, 탐색: [S, Aaa]
현재: aaAaa, 탐색: [S, Aaa, aaAaa]
현재: aaaaAaa, 탐색: [S, Aaa, aaAaa, aaaaAaa]
현재: aaaaaaAaa, 탐색: [S, Aaa, aaAaa, aaaaAaa, aaaaaaAaa]
현재: aaaaaaaaAaa, 탐색: [S, Aaa, aaAaa, aaaaAaa, aaaaaaAaa, aaaaaaaaAaa]
현재: aaaabaa, 탐색: [S, Aaa, aaAaa, aaaaAaa, aaaabaa]
현재: aabaa, 탐색: [S, Aaa, aaAaa, aabaa]
현재: baa, 탐색: [S, Aaa, baa]

최종 정답 : S -> Aaa -> aaAaa -> aaaaAaa -> aaaaaaAaa -> aaaaaabaa

생성규칙 목록 : {S=[AB], A=[aAa, a], B=[bBb, b]}

현재: S, 탐색: [S]
현재: AB, 탐색: [S, AB]
현재: aAaB, 탐색: [S, AB, aAaB]
현재: aaAaaB, 탐색: [S, AB, aAaB, aaAaaB]
현재: aaaAaaaB, 탐색: [S, AB, aAaB, aaAaaB, aaaAaaaB]
현재: aaaaAaaaaB, 탐색: [S, AB, aAaB, aaAaaB, aaaAaaaB, aaaaAaaaaB]
현재: aaaaaaaB, 탐색: [S, AB, aAaB, aaAaaB, aaaAaaaB, aaaaaaaB]
현재: aaaAaaabBb, 탐색: [S, AB, aAaB, aaAaaB, aaaAaaaB, aaaAaaabBb]
현재: aaaAaaab, 탐색: [S, AB, aAaB, aaAaaB, aaaAaaaB, aaaAaaab]
현재: aaaaaB, 탐색: [S, AB, aAaB, aaAaaB, aaaaaB]
현재: aaaaabBb, 탐색: [S, AB, aAaB, aaAaaB, aaaaaB, aaaaabBb]
현재: aaaaab, 탐색: [S, AB, aAaB, aaAaaB, aaaaaB, aaaaab]
현재: aaAaabBb, 탐색: [S, AB, aAaB, aaAaaB, aaAaabBb]
현재: aaaAaaabBb, 탐색: [S, AB, aAaB, aaAaaB, aaAaabBb, aaaAaaabBb]
현재: aaaaabBb, 탐색: [S, AB, aAaB, aaAaaB, aaAaabBb, aaaaabBb]
현재: aaAaabbBbb, 탐색: [S, AB, aAaB, aaAaaB, aaAaabBb, aaAaabbBbb]
현재: aaAaabbb, 탐색: [S, AB, aAaB, aaAaaB, aaAaabBb, aaAaabbb]
현재: aaAaab, 탐색: [S, AB, aAaB, aaAaaB, aaAaab]
현재: aaaAaaab, 탐색: [S, AB, aAaB, aaAaaB, aaAaab, aaaAaaab]
현재: aaaaab, 탐색: [S, AB, aAaB, aaAaaB, aaAaab, aaaaab]
현재: aaaB, 탐색: [S, AB, aAaB, aaaB]
현재: aaabBb, 탐색: [S, AB, aAaB, aaaB, aaabBb]
현재: aaabbBbb, 탐색: [S, AB, aAaB, aaaB, aaabBb, aaabbBbb]
현재: aaab, 탐색: [S, AB, aAaB, aaaB, aaab]
현재: aAabBb, 탐색: [S, AB, aAaB, aAabBb]
현재: aAab, 탐색: [S, AB, aAaB, aAab]
현재: aB, 탐색: [S, AB, aB]
현재: AbBb, 탐색: [S, AB, AbBb]
현재: Ab, 탐색: [S, AB, Ab]

최종 정답 : S -> AB -> aAaB -> aaaB -> aaabBb -> aaabbb

생성규칙 목록 : {S=[aAa, bBb], A=[aAa, b], B=[bBb, A, a]}

현재: S, 탐색: [S]
현재: aAa, 탐색: [S, aAa]
현재: aaAaa, 탐색: [S, aAa, aaAaa]
현재: aaaAaaa, 탐색: [S, aAa, aaAaa, aaaAaaa]
현재: aaaaAaaaa, 탐색: [S, aAa, aaAaa, aaaAaaa, aaaaAaaaa]
현재: aaaaaAaaaaa, 탐색: [S, aAa, aaAaa, aaaAaaa, aaaaAaaaa, aaaaaAaaaaa]
현재: aaaabaaaa, 탐색: [S, aAa, aaAaa, aaaAaaa, aaaaAaaaa, aaaabaaaa]
현재: aaabaaa, 탐색: [S, aAa, aaAaa, aaaAaaa, aaabaaa]
현재: aabaa, 탐색: [S, aAa, aaAaa, aabaa]
현재: aba, 탐색: [S, aAa, aba]
현재: bBb, 탐색: [S, bBb]
현재: bbBbb, 탐색: [S, bBb, bbBbb]
현재: bbbBbbb, 탐색: [S, bBb, bbBbb, bbbBbbb]
현재: bbbbBbbbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbbBbbbb]
현재: bbbbbBbbbbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbbBbbbb, bbbbbBbbbbb]
현재: bbbbAbbbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbbBbbbb, bbbbAbbbb]
현재: bbbbabbbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbbBbbbb, bbbbabbbb]
현재: bbbAbbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbAbbb]
현재: bbbaAabbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbAbbb, bbbaAabbb]
현재: bbbbbbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbAbbb, bbbbbbb]
현재: bbbabbb, 탐색: [S, bBb, bbBbb, bbbBbbb, bbbabbb]
현재: bbAbb, 탐색: [S, bBb, bbBbb, bbAbb]
현재: bbaAabb, 탐색: [S, bBb, bbBbb, bbAbb, bbaAabb]
현재: bbaaAaabb, 탐색: [S, bBb, bbBbb, bbAbb, bbaAabb, bbaaAaabb]
현재: bbababb, 탐색: [S, bBb, bbBbb, bbAbb, bbaAabb, bbababb]
현재: bbbbb, 탐색: [S, bBb, bbBbb, bbAbb, bbbbb]
현재: bbabb, 탐색: [S, bBb, bbBbb, bbabb]
현재: bAb, 탐색: [S, bBb, bAb]
현재: baAab, 탐색: [S, bBb, bAb, baAab]
현재: baaAaab, 탐색: [S, bBb, bAb, baAab, baaAaab]
현재: baaaAaaab, 탐색: [S, bBb, bAb, baAab, baaAaab, baaaAaaab]
현재: babab, 탐색: [S, bBb, bAb, baAab, babab]
현재: bbb, 탐색: [S, bBb, bAb, bbb]
현재: bab, 탐색: [S, bBb, bab]

최종 정답 : S -> bBb -> bAb -> baAab -> baaAaab -> baabaab

생성규칙 목록 : {S=[aS, Sb, A, B], A=[aA, a], B=[bB, b]}

현재: S, 탐색: [S]
현재: aS, 탐색: [S, aS]
현재: aaS, 탐색: [S, aS, aaS]
현재: aaaS, 탐색: [S, aS, aaS, aaaS]
현재: aaaaS, 탐색: [S, aS, aaS, aaaS, aaaaS]
현재: aaaaaS, 탐색: [S, aS, aaS, aaaS, aaaaS, aaaaaS]
현재: aaaaSb, 탐색: [S, aS, aaS, aaaS, aaaaS, aaaaSb]
현재: aaaaA, 탐색: [S, aS, aaS, aaaS, aaaaS, aaaaA]
현재: aaaaB, 탐색: [S, aS, aaS, aaaS, aaaaS, aaaaB]
현재: aaaSb, 탐색: [S, aS, aaS, aaaS, aaaSb]
현재: aaaaSb, 탐색: [S, aS, aaS, aaaS, aaaSb, aaaaSb]
현재: aaaSbb, 탐색: [S, aS, aaS, aaaS, aaaSb, aaaSbb]
현재: aaaAb, 탐색: [S, aS, aaS, aaaS, aaaSb, aaaAb]
현재: aaaBb, 탐색: [S, aS, aaS, aaaS, aaaSb, aaaBb]
현재: aaaA, 탐색: [S, aS, aaS, aaaS, aaaA]
현재: aaaaA, 탐색: [S, aS, aaS, aaaS, aaaA, aaaaA]
현재: aaaa, 탐색: [S, aS, aaS, aaaS, aaaA, aaaa]
현재: aaaB, 탐색: [S, aS, aaS, aaaS, aaaB]
현재: aaabB, 탐색: [S, aS, aaS, aaaS, aaaB, aaabB]
현재: aaab, 탐색: [S, aS, aaS, aaaS, aaaB, aaab]
현재: aaSb, 탐색: [S, aS, aaS, aaSb]
현재: aaaSb, 탐색: [S, aS, aaS, aaSb, aaaSb]
현재: aaaaSb, 탐색: [S, aS, aaS, aaSb, aaaSb, aaaaSb]
현재: aaaSbb, 탐색: [S, aS, aaS, aaSb, aaaSb, aaaSbb]
현재: aaaAb, 탐색: [S, aS, aaS, aaSb, aaaSb, aaaAb]
현재: aaaBb, 탐색: [S, aS, aaS, aaSb, aaaSb, aaaBb]
현재: aaSbb, 탐색: [S, aS, aaS, aaSb, aaSbb]
현재: aaaSbb, 탐색: [S, aS, aaS, aaSb, aaSbb, aaaSbb]
현재: aaSbbb, 탐색: [S, aS, aaS, aaSb, aaSbb, aaSbbb]
현재: aaAbb, 탐색: [S, aS, aaS, aaSb, aaSbb, aaAbb]
현재: aaBbb, 탐색: [S, aS, aaS, aaSb, aaSbb, aaBbb]
현재: aaAb, 탐색: [S, aS, aaS, aaSb, aaAb]
현재: aaaAb, 탐색: [S, aS, aaS, aaSb, aaAb, aaaAb]
현재: aaab, 탐색: [S, aS, aaS, aaSb, aaAb, aaab]
현재: aaBb, 탐색: [S, aS, aaS, aaSb, aaBb]
현재: aabBb, 탐색: [S, aS, aaS, aaSb, aaBb, aabBb]
현재: aabb, 탐색: [S, aS, aaS, aaSb, aaBb, aabb]
현재: aaA, 탐색: [S, aS, aaS, aaA]
현재: aaaA, 탐색: [S, aS, aaS, aaA, aaaA]
현재: aaaaA, 탐색: [S, aS, aaS, aaA, aaaA, aaaaA]
현재: aaaa, 탐색: [S, aS, aaS, aaA, aaaA, aaaa]
현재: aaa, 탐색: [S, aS, aaS, aaA, aaa]
현재: aaB, 탐색: [S, aS, aaS, aaB]
현재: aabB, 탐색: [S, aS, aaS, aaB, aabB]
현재: aabbB, 탐색: [S, aS, aaS, aaB, aabB, aabbB]
현재: aabb, 탐색: [S, aS, aaS, aaB, aabB, aabb]
현재: aab, 탐색: [S, aS, aaS, aaB, aab]
현재: aSb, 탐색: [S, aS, aSb]
현재: aaSb, 탐색: [S, aS, aSb, aaSb]
현재: aaaSb, 탐색: [S, aS, aSb, aaSb, aaaSb]
현재: aaaaSb, 탐색: [S, aS, aSb, aaSb, aaaSb, aaaaSb]
현재: aaaSbb, 탐색: [S, aS, aSb, aaSb, aaaSb, aaaSbb]
현재: aaaAb, 탐색: [S, aS, aSb, aaSb, aaaSb, aaaAb]
현재: aaaBb, 탐색: [S, aS, aSb, aaSb, aaaSb, aaaBb]
현재: aaSbb, 탐색: [S, aS, aSb, aaSb, aaSbb]
현재: aaaSbb, 탐색: [S, aS, aSb, aaSb, aaSbb, aaaSbb]
현재: aaSbbb, 탐색: [S, aS, aSb, aaSb, aaSbb, aaSbbb]
현재: aaAbb, 탐색: [S, aS, aSb, aaSb, aaSbb, aaAbb]
현재: aaBbb, 탐색: [S, aS, aSb, aaSb, aaSbb, aaBbb]
현재: aaAb, 탐색: [S, aS, aSb, aaSb, aaAb]
현재: aaaAb, 탐색: [S, aS, aSb, aaSb, aaAb, aaaAb]
현재: aaab, 탐색: [S, aS, aSb, aaSb, aaAb, aaab]
현재: aaBb, 탐색: [S, aS, aSb, aaSb, aaBb]
현재: aabBb, 탐색: [S, aS, aSb, aaSb, aaBb, aabBb]
현재: aabb, 탐색: [S, aS, aSb, aaSb, aaBb, aabb]
현재: aSbb, 탐색: [S, aS, aSb, aSbb]
현재: aaSbb, 탐색: [S, aS, aSb, aSbb, aaSbb]
현재: aaaSbb, 탐색: [S, aS, aSb, aSbb, aaSbb, aaaSbb]
현재: aaSbbb, 탐색: [S, aS, aSb, aSbb, aaSbb, aaSbbb]
현재: aaAbb, 탐색: [S, aS, aSb, aSbb, aaSbb, aaAbb]
현재: aaBbb, 탐색: [S, aS, aSb, aSbb, aaSbb, aaBbb]
현재: aSbbb, 탐색: [S, aS, aSb, aSbb, aSbbb]
현재: aaSbbb, 탐색: [S, aS, aSb, aSbb, aSbbb, aaSbbb]
현재: aSbbbb, 탐색: [S, aS, aSb, aSbb, aSbbb, aSbbbb]
현재: aAbbb, 탐색: [S, aS, aSb, aSbb, aSbbb, aAbbb]
현재: aBbbb, 탐색: [S, aS, aSb, aSbb, aSbbb, aBbbb]
현재: aAbb, 탐색: [S, aS, aSb, aSbb, aAbb]
현재: aaAbb, 탐색: [S, aS, aSb, aSbb, aAbb, aaAbb]
현재: aabb, 탐색: [S, aS, aSb, aSbb, aAbb, aabb]
현재: aBbb, 탐색: [S, aS, aSb, aSbb, aBbb]
현재: abBbb, 탐색: [S, aS, aSb, aSbb, aBbb, abBbb]
현재: aAb, 탐색: [S, aS, aSb, aAb]
현재: aBb, 탐색: [S, aS, aSb, aBb]
현재: aA, 탐색: [S, aS, aA]
현재: aB, 탐색: [S, aS, aB]
현재: Sb, 탐색: [S, Sb]
현재: A, 탐색: [S, A]
현재: B, 탐색: [S, B]

최종 정답 : S -> aS -> aSb -> aSbb -> aBbb -> abbb

생성규칙 목록 : {S=[aSa, bSb, A, B], A=[aAa, a], B=[bBb, b]}

현재: S, 탐색: [S]
현재: aSa, 탐색: [S, aSa]
현재: aaSaa, 탐색: [S, aSa, aaSaa]
현재: aaaSaaa, 탐색: [S, aSa, aaSaa, aaaSaaa]
현재: aaaaSaaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaaSaaaa]
현재: aaaaaSaaaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaaSaaaa, aaaaaSaaaaa]
현재: aaaabSbaaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaaSaaaa, aaaabSbaaaa]
현재: aaaaAaaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaaSaaaa, aaaaAaaaa]
현재: aaaaBaaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaaSaaaa, aaaaBaaaa]
현재: aaabSbaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaabSbaaa]
현재: aaabaSabaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaabSbaaa, aaabaSabaaa]
현재: aaabbSbbaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaabSbaaa, aaabbSbbaaa]
현재: aaabAbaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaabSbaaa, aaabAbaaa]
현재: aaabBbaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaabSbaaa, aaabBbaaa]
현재: aaaAaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaAaaa]
현재: aaaaAaaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaAaaa, aaaaAaaaa]
현재: aaaaaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaAaaa, aaaaaaa]
현재: aaaBaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaBaaa]
현재: aaabBbaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaBaaa, aaabBbaaa]
현재: aaabaaa, 탐색: [S, aSa, aaSaa, aaaSaaa, aaaBaaa, aaabaaa]
현재: aabSbaa, 탐색: [S, aSa, aaSaa, aabSbaa]
현재: aabaSabaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabaSabaa]
현재: aabaaSaabaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabaSabaa, aabaaSaabaa]
현재: aababSbabaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabaSabaa, aababSbabaa]
현재: aabaAabaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabaSabaa, aabaAabaa]
현재: aabaBabaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabaSabaa, aabaBabaa]
현재: aabbSbbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabbSbbaa]
현재: aabbaSabbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabbSbbaa, aabbaSabbaa]
현재: aabbbSbbbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabbSbbaa, aabbbSbbbaa]
현재: aabbAbbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabbSbbaa, aabbAbbaa]
현재: aabbBbbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabbSbbaa, aabbBbbaa]
현재: aabAbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabAbaa]
현재: aabaAabaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabAbaa, aabaAabaa]
현재: aababaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabAbaa, aababaa]
현재: aabBbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabBbaa]
현재: aabbBbbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabBbaa, aabbBbbaa]
현재: aabbbaa, 탐색: [S, aSa, aaSaa, aabSbaa, aabBbaa, aabbbaa]
현재: aaAaa, 탐색: [S, aSa, aaSaa, aaAaa]
현재: aaaAaaa, 탐색: [S, aSa, aaSaa, aaAaa, aaaAaaa]
현재: aaaaAaaaa, 탐색: [S, aSa, aaSaa, aaAaa, aaaAaaa, aaaaAaaaa]
현재: aaaaaaa, 탐색: [S, aSa, aaSaa, aaAaa, aaaAaaa, aaaaaaa]
현재: aaaaa, 탐색: [S, aSa, aaSaa, aaAaa, aaaaa]
현재: aaBaa, 탐색: [S, aSa, aaSaa, aaBaa]
현재: aabBbaa, 탐색: [S, aSa, aaSaa, aaBaa, aabBbaa]
현재: aabbBbbaa, 탐색: [S, aSa, aaSaa, aaBaa, aabBbaa, aabbBbbaa]
현재: aabbbaa, 탐색: [S, aSa, aaSaa, aaBaa, aabBbaa, aabbbaa]
현재: aabaa, 탐색: [S, aSa, aaSaa, aaBaa, aabaa]
현재: abSba, 탐색: [S, aSa, abSba]
현재: abaSaba, 탐색: [S, aSa, abSba, abaSaba]
현재: abaaSaaba, 탐색: [S, aSa, abSba, abaSaba, abaaSaaba]
현재: abaaaSaaaba, 탐색: [S, aSa, abSba, abaSaba, abaaSaaba, abaaaSaaaba]
현재: abaabSbaaba, 탐색: [S, aSa, abSba, abaSaba, abaaSaaba, abaabSbaaba]
현재: abaaAaaba, 탐색: [S, aSa, abSba, abaSaba, abaaSaaba, abaaAaaba]
현재: abaaBaaba, 탐색: [S, aSa, abSba, abaSaba, abaaSaaba, abaaBaaba]
현재: ababSbaba, 탐색: [S, aSa, abSba, abaSaba, ababSbaba]
현재: ababaSababa, 탐색: [S, aSa, abSba, abaSaba, ababSbaba, ababaSababa]
현재: ababbSbbaba, 탐색: [S, aSa, abSba, abaSaba, ababSbaba, ababbSbbaba]
현재: ababAbaba, 탐색: [S, aSa, abSba, abaSaba, ababSbaba, ababAbaba]
현재: ababBbaba, 탐색: [S, aSa, abSba, abaSaba, ababSbaba, ababBbaba]
현재: abaAaba, 탐색: [S, aSa, abSba, abaSaba, abaAaba]
현재: abaaAaaba, 탐색: [S, aSa, abSba, abaSaba, abaAaba, abaaAaaba]
현재: abaaaba, 탐색: [S, aSa, abSba, abaSaba, abaAaba, abaaaba]
현재: abaBaba, 탐색: [S, aSa, abSba, abaSaba, abaBaba]
현재: ababBbaba, 탐색: [S, aSa, abSba, abaSaba, abaBaba, ababBbaba]
현재: abababa, 탐색: [S, aSa, abSba, abaSaba, abaBaba, abababa]
현재: abbSbba, 탐색: [S, aSa, abSba, abbSbba]
현재: abbaSabba, 탐색: [S, aSa, abSba, abbSbba, abbaSabba]
현재: abbaaSaabba, 탐색: [S, aSa, abSba, abbSbba, abbaSabba, abbaaSaabba]
현재: abbabSbabba, 탐색: [S, aSa, abSba, abbSbba, abbaSabba, abbabSbabba]
현재: abbaAabba, 탐색: [S, aSa, abSba, abbSbba, abbaSabba, abbaAabba]
현재: abbaBabba, 탐색: [S, aSa, abSba, abbSbba, abbaSabba, abbaBabba]
현재: abbbSbbba, 탐색: [S, aSa, abSba, abbSbba, abbbSbbba]
현재: abbbaSabbba, 탐색: [S, aSa, abSba, abbSbba, abbbSbbba, abbbaSabbba]
현재: abbbbSbbbba, 탐색: [S, aSa, abSba, abbSbba, abbbSbbba, abbbbSbbbba]
현재: abbbAbbba, 탐색: [S, aSa, abSba, abbSbba, abbbSbbba, abbbAbbba]
현재: abbbBbbba, 탐색: [S, aSa, abSba, abbSbba, abbbSbbba, abbbBbbba]
현재: abbAbba, 탐색: [S, aSa, abSba, abbSbba, abbAbba]
현재: abbaAabba, 탐색: [S, aSa, abSba, abbSbba, abbAbba, abbaAabba]
현재: abbabba, 탐색: [S, aSa, abSba, abbSbba, abbAbba, abbabba]
현재: abbBbba, 탐색: [S, aSa, abSba, abbSbba, abbBbba]
현재: abbbBbbba, 탐색: [S, aSa, abSba, abbSbba, abbBbba, abbbBbbba]
현재: abbbbba, 탐색: [S, aSa, abSba, abbSbba, abbBbba, abbbbba]
현재: abAba, 탐색: [S, aSa, abSba, abAba]
현재: abaAaba, 탐색: [S, aSa, abSba, abAba, abaAaba]
현재: abaaAaaba, 탐색: [S, aSa, abSba, abAba, abaAaba, abaaAaaba]
현재: abaaaba, 탐색: [S, aSa, abSba, abAba, abaAaba, abaaaba]
현재: ababa, 탐색: [S, aSa, abSba, abAba, ababa]
현재: abBba, 탐색: [S, aSa, abSba, abBba]
현재: abbBbba, 탐색: [S, aSa, abSba, abBba, abbBbba]
현재: abbbBbbba, 탐색: [S, aSa, abSba, abBba, abbBbba, abbbBbbba]
현재: abbbbba, 탐색: [S, aSa, abSba, abBba, abbBbba, abbbbba]
현재: abbba, 탐색: [S, aSa, abSba, abBba, abbba]
현재: aAa, 탐색: [S, aSa, aAa]
현재: aaAaa, 탐색: [S, aSa, aAa, aaAaa]
현재: aaaAaaa, 탐색: [S, aSa, aAa, aaAaa, aaaAaaa]
현재: aaaaAaaaa, 탐색: [S, aSa, aAa, aaAaa, aaaAaaa, aaaaAaaaa]
현재: aaaaaaa, 탐색: [S, aSa, aAa, aaAaa, aaaAaaa, aaaaaaa]
현재: aaaaa, 탐색: [S, aSa, aAa, aaAaa, aaaaa]
현재: aaa, 탐색: [S, aSa, aAa, aaa]
현재: aBa, 탐색: [S, aSa, aBa]
현재: abBba, 탐색: [S, aSa, aBa, abBba]
현재: abbBbba, 탐색: [S, aSa, aBa, abBba, abbBbba]
현재: abbbBbbba, 탐색: [S, aSa, aBa, abBba, abbBbba, abbbBbbba]
현재: abbbbba, 탐색: [S, aSa, aBa, abBba, abbBbba, abbbbba]
현재: abbba, 탐색: [S, aSa, aBa, abBba, abbba]
현재: aba, 탐색: [S, aSa, aBa, aba]
현재: bSb, 탐색: [S, bSb]
현재: baSab, 탐색: [S, bSb, baSab]
현재: baaSaab, 탐색: [S, bSb, baSab, baaSaab]
현재: baaaSaaab, 탐색: [S, bSb, baSab, baaSaab, baaaSaaab]
현재: baaaaSaaaab, 탐색: [S, bSb, baSab, baaSaab, baaaSaaab, baaaaSaaaab]
현재: baaabSbaaab, 탐색: [S, bSb, baSab, baaSaab, baaaSaaab, baaabSbaaab]
현재: baaaAaaab, 탐색: [S, bSb, baSab, baaSaab, baaaSaaab, baaaAaaab]
현재: baaaBaaab, 탐색: [S, bSb, baSab, baaSaab, baaaSaaab, baaaBaaab]
현재: baabSbaab, 탐색: [S, bSb, baSab, baaSaab, baabSbaab]
현재: baabaSabaab, 탐색: [S, bSb, baSab, baaSaab, baabSbaab, baabaSabaab]
현재: baabbSbbaab, 탐색: [S, bSb, baSab, baaSaab, baabSbaab, baabbSbbaab]
현재: baabAbaab, 탐색: [S, bSb, baSab, baaSaab, baabSbaab, baabAbaab]
현재: baabBbaab, 탐색: [S, bSb, baSab, baaSaab, baabSbaab, baabBbaab]
현재: baaAaab, 탐색: [S, bSb, baSab, baaSaab, baaAaab]
현재: baaaAaaab, 탐색: [S, bSb, baSab, baaSaab, baaAaab, baaaAaaab]
현재: baaaaab, 탐색: [S, bSb, baSab, baaSaab, baaAaab, baaaaab]
현재: baaBaab, 탐색: [S, bSb, baSab, baaSaab, baaBaab]
현재: baabBbaab, 탐색: [S, bSb, baSab, baaSaab, baaBaab, baabBbaab]
현재: baabaab, 탐색: [S, bSb, baSab, baaSaab, baaBaab, baabaab]
현재: babSbab, 탐색: [S, bSb, baSab, babSbab]
현재: babaSabab, 탐색: [S, bSb, baSab, babSbab, babaSabab]
현재: babaaSaabab, 탐색: [S, bSb, baSab, babSbab, babaSabab, babaaSaabab]
현재: bababSbabab, 탐색: [S, bSb, baSab, babSbab, babaSabab, bababSbabab]
현재: babaAabab, 탐색: [S, bSb, baSab, babSbab, babaSabab, babaAabab]
현재: babaBabab, 탐색: [S, bSb, baSab, babSbab, babaSabab, babaBabab]
현재: babbSbbab, 탐색: [S, bSb, baSab, babSbab, babbSbbab]
현재: babbaSabbab, 탐색: [S, bSb, baSab, babSbab, babbSbbab, babbaSabbab]
현재: babbbSbbbab, 탐색: [S, bSb, baSab, babSbab, babbSbbab, babbbSbbbab]
현재: babbAbbab, 탐색: [S, bSb, baSab, babSbab, babbSbbab, babbAbbab]
현재: babbBbbab, 탐색: [S, bSb, baSab, babSbab, babbSbbab, babbBbbab]
현재: babAbab, 탐색: [S, bSb, baSab, babSbab, babAbab]
현재: babaAabab, 탐색: [S, bSb, baSab, babSbab, babAbab, babaAabab]
현재: bababab, 탐색: [S, bSb, baSab, babSbab, babAbab, bababab]
현재: babBbab, 탐색: [S, bSb, baSab, babSbab, babBbab]
현재: babbBbbab, 탐색: [S, bSb, baSab, babSbab, babBbab, babbBbbab]
현재: babbbab, 탐색: [S, bSb, baSab, babSbab, babBbab, babbbab]
현재: baAab, 탐색: [S, bSb, baSab, baAab]
현재: baaAaab, 탐색: [S, bSb, baSab, baAab, baaAaab]
현재: baaaAaaab, 탐색: [S, bSb, baSab, baAab, baaAaab, baaaAaaab]
현재: baaaaab, 탐색: [S, bSb, baSab, baAab, baaAaab, baaaaab]
현재: baaab, 탐색: [S, bSb, baSab, baAab, baaab]
현재: baBab, 탐색: [S, bSb, baSab, baBab]
현재: babBbab, 탐색: [S, bSb, baSab, baBab, babBbab]
현재: babbBbbab, 탐색: [S, bSb, baSab, baBab, babBbab, babbBbbab]
현재: babbbab, 탐색: [S, bSb, baSab, baBab, babBbab, babbbab]
현재: babab, 탐색: [S, bSb, baSab, baBab, babab]
현재: bbSbb, 탐색: [S, bSb, bbSbb]
현재: bbaSabb, 탐색: [S, bSb, bbSbb, bbaSabb]
현재: bbaaSaabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaaSaabb]
현재: bbaaaSaaabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaaSaabb, bbaaaSaaabb]
현재: bbaabSbaabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaaSaabb, bbaabSbaabb]
현재: bbaaAaabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaaSaabb, bbaaAaabb]
현재: bbaaBaabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaaSaabb, bbaaBaabb]
현재: bbabSbabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbabSbabb]
현재: bbabaSababb, 탐색: [S, bSb, bbSbb, bbaSabb, bbabSbabb, bbabaSababb]
현재: bbabbSbbabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbabSbabb, bbabbSbbabb]
현재: bbabAbabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbabSbabb, bbabAbabb]
현재: bbabBbabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbabSbabb, bbabBbabb]
현재: bbaAabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaAabb]
현재: bbaaAaabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaAabb, bbaaAaabb]
현재: bbaBabb, 탐색: [S, bSb, bbSbb, bbaSabb, bbaBabb]
현재: bbbSbbb, 탐색: [S, bSb, bbSbb, bbbSbbb]
현재: bbAbb, 탐색: [S, bSb, bbSbb, bbAbb]
현재: bbBbb, 탐색: [S, bSb, bbSbb, bbBbb]
현재: bAb, 탐색: [S, bSb, bAb]
현재: bBb, 탐색: [S, bSb, bBb]
현재: A, 탐색: [S, A]
현재: B, 탐색: [S, B]

최종 정답 : S -> bSb -> bbSbb -> bbaSabb -> bbaAabb -> bbaaabb
```
