## 요구사항 분석
문자열 ```string```이 표현 형식 ```expression```과 일치하는지 검사하는 함수 ```isMatched(string, expression)```을 작성하시오.

```expression```은 다음을 concatenate하여 연결한 형태이다.
* ```a``` : 문자 a 1개를 의미한다.
* ```a+``` : 문자 a 1개 이상을 의미한다.
* ```a*``` : 문자 a 0개 이상을 의미한다.

예를 들어 ```aaabbb```는 표현 형식 ```a*bb+```과 일치하므로, ```isMatched("aaabbb", "a*bb+")```의 값은 true이다.

단, ```expression```에는 ```*``` 기호가 최대 6개 들어갈 수 있다.

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// 표현 형식을 tokenize
fun tokenizeExpression(expression: String): List<String> {
    val result = mutableListOf<String>()
    var idx = 0
    
    while (idx < expression.length) {
        if (idx == expression.length - 1) {
            result.add(expression.substring(idx))
            return result
        } else {
            if (expression[idx + 1] in listOf('+', '*')) {
                result.add(expression.substring(idx, idx + 2))
                idx += 2
            } else {
                result.add(expression.substring(idx, idx + 1))
                idx++
            }
        }
    }
    
    return result
}

// tokenize된 표현 형식 중 * 기호를 포함하는 것은 제외/(a+로 교체) 하는 모든 경우의 수 구하기
fun findCaseForAtLeastZero(tokenizedExpression: List<String>): List<List<String>> {
    
    // * 기호를 포함하는 표현 형식의 인덱스
    val idxOfAtLeastZero = mutableListOf<Int>()
    tokenizedExpression.forEachIndexed { idx, it ->
        if (tokenizedExpression[idx].contains("*")) {
            idxOfAtLeastZero.add(idx)
        }
    }
    println("2. indices of * : ${idxOfAtLeastZero}")
    
    // 제외, (a+로 교체) 하는 모든 경우를 반환
    val n = idxOfAtLeastZero.size
    val cases = (2.0).pow(n.toDouble()).toInt()
    
    return (0 until cases).map { c ->
        
        // 제외, (a+로 교체) 할 인덱스를 지정
        val indicesToReplace = mutableListOf<Int>()
        
        (0 until n).forEach { i ->
            val powOf2 = (2.0).pow(i.toDouble()).toInt()
            
            if ((c / powOf2) % 2 == 0) {
                indicesToReplace.add(idxOfAtLeastZero[i])
            }
        }
        
        // 제외,교체할 인덱스를 처리하고, tokenized expression을 추출
        val result = mutableListOf<String>()
        tokenizedExpression.forEachIndexed { idx, it ->
            if (!idxOfAtLeastZero.contains(idx)) {
                result.add(it)
            } else if (indicesToReplace.contains(idx)) {
                result.add(it.replace("*", "+"))
            }
        }
        val sortedResult = sortTokenizedExpressions(result)
        
        println("3. case ${c + 1} : ${result} -> ${sortedResult}")
        sortedResult
    }
}

// tokenize된 표현 형식이 a+, a 순서이면 a, a+ 순서가 되도록 정렬
fun sortTokenizedExpressions(expressions: List<String>): List<String> {
    val newExpressions = mutableListOf<String>()
    
    var currentAlphabet = ' '
    var currentCount = 0
    var currentPlusCount = 0
    
    expressions.forEachIndexed { idx, it ->
        currentAlphabet = expressions[idx][0]
        if (expressions[idx].contains("+")) {
            currentPlusCount++
        } else {
            currentCount++
        }
        
        // 알파벳이 바뀌는 인덱스 or 마지막
        if (idx == expressions.size - 1 || currentAlphabet != expressions[idx + 1][0]) {
            
            // 카운트한 대로 정렬되도록 newExpressions에 표현 형식 추가
            repeat (currentCount) {
                newExpressions.add(currentAlphabet.toString())
            }
            repeat (currentPlusCount) {
                newExpressions.add(currentAlphabet.toString() + "+")
            }
            
            // 초기화
            currentPlusCount = 0
            currentCount = 0
        }
    }
    
    return newExpressions
}

// 문자열이 표현 형식과 일치하는지 검사
fun isMatched(string: String, expression: String): Boolean {
    
    // 먼저, 표현 형식을 tokenize
    val tokenizedExpression = tokenizeExpression(expression)
    println("1. tokenized expression = ${tokenizedExpression}")
    
    // * 기호를 포함하는 표현 형식 (a*) 의 모든 제외/(a+로 교체)
    val incOrExcAtLeastZero = findCaseForAtLeastZero(tokenizedExpression)
    print("\n")
    
    // 각각의 경우에 대해 하나라도 일치하면 true
    incOrExcAtLeastZero.forEachIndexed { idx, exps ->
        var str = string
        println("4. exps = ${exps}, string = ${str}")
        
        var isMatching = true
        
        // tokenize된 각 표현 형식에 대해 해당하는 string을 제거하는 식으로 함
        exps.forEach { exp ->
            
            // 표현 형식과 일치 불가능 확인 시, continue 처리
            if (!isMatching) {
                return@forEach
            }
            
            val alpha = exp[0]
            
            // 남아 있는 string이 비어 있는 경우의 예외처리
            if (str.isEmpty()) {
                isMatching = false
            }
            
            // 일반적인 경우
            else if (str[0] == alpha) {
                if (exp.contains("+")) {
                    str = str.dropWhile { it == alpha }
                } else {
                    str = str.drop(1)
                }
            } else {
                isMatching = false
            }
            println("5. exp = ${exp}, string left = ${str}")
        }
        print("\n")
        
        // 남아 있는 string이 비어 있지 않으면 해당 케이스는 실패임
        if (isMatching && str.isEmpty()) {
            return true
        }
    }
    return false
}

fun main() {
    
    // 임시 문자열 목록
    val tempStrings = listOf<String> (
        "", "a", "aa", "aaa", "b", "bb", "bbbb",
        "ba", "abb", "abab", "aaabbb"
    )
    
    // 임시 표현 형식 목록
    val tempExpressions = listOf<String> (
        "a", "a+", "a*", "b", "b+", "b*",
        "aa*", "aa+", "a*a", "a+a", "bb*", "bb+", "b*b", "b+b",
        "a*b*", "b*a*", "a+b+", "b+a+", "a*bb+", "a+bb*",
        "a*b*a*", "b*a*b*", "a+b*a*b+"
    )
    
    // 검사
    tempStrings.forEach { s ->
        tempExpressions.forEach { e ->
            println("string = ${s}, expression = ${e} -> ${isMatched(s, e)}\n")
        }
    }
}
```

### 실행 결과
```kotlin
1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = 
5. exp = a, string left = 

string = , expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = 
5. exp = a+, string left = 

string = , expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = 
5. exp = a+, string left = 

4. exps = [], string = 

string = , expression = a* -> true

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = 
5. exp = b, string left = 

string = , expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = 
5. exp = b+, string left = 

string = , expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = 
5. exp = b+, string left = 

4. exps = [], string = 

string = , expression = b* -> true

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = 
5. exp = a, string left = 

4. exps = [a], string = 
5. exp = a, string left = 

string = , expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = 
5. exp = a, string left = 

string = , expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = 
5. exp = a, string left = 

4. exps = [a], string = 
5. exp = a, string left = 

string = , expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = 
5. exp = a, string left = 

string = , expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = 
5. exp = b, string left = 

4. exps = [b], string = 
5. exp = b, string left = 

string = , expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = 
5. exp = b, string left = 

string = , expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = 
5. exp = b, string left = 

4. exps = [b], string = 
5. exp = b, string left = 

string = , expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = 
5. exp = b, string left = 

string = , expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = 
5. exp = a+, string left = 

4. exps = [b+], string = 
5. exp = b+, string left = 

4. exps = [a+], string = 
5. exp = a+, string left = 

4. exps = [], string = 

string = , expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = 
5. exp = b+, string left = 

4. exps = [a+], string = 
5. exp = a+, string left = 

4. exps = [b+], string = 
5. exp = b+, string left = 

4. exps = [], string = 

string = , expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = 
5. exp = a+, string left = 

string = , expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = 
5. exp = b+, string left = 

string = , expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = 
5. exp = a+, string left = 

4. exps = [b, b+], string = 
5. exp = b, string left = 

string = , expression = a*bb+ -> false

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = 
5. exp = a+, string left = 

4. exps = [a+, b], string = 
5. exp = a+, string left = 

string = , expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = 
5. exp = a+, string left = 

4. exps = [b+, a+], string = 
5. exp = b+, string left = 

4. exps = [a+, a+], string = 
5. exp = a+, string left = 

4. exps = [a+], string = 
5. exp = a+, string left = 

4. exps = [a+, b+], string = 
5. exp = a+, string left = 

4. exps = [b+], string = 
5. exp = b+, string left = 

4. exps = [a+], string = 
5. exp = a+, string left = 

4. exps = [], string = 

string = , expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = 
5. exp = b+, string left = 

4. exps = [a+, b+], string = 
5. exp = a+, string left = 

4. exps = [b+, b+], string = 
5. exp = b+, string left = 

4. exps = [b+], string = 
5. exp = b+, string left = 

4. exps = [b+, a+], string = 
5. exp = b+, string left = 

4. exps = [a+], string = 
5. exp = a+, string left = 

4. exps = [b+], string = 
5. exp = b+, string left = 

4. exps = [], string = 

string = , expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = 
5. exp = a+, string left = 

4. exps = [a+, a+, b+], string = 
5. exp = a+, string left = 

4. exps = [a+, b+, b+], string = 
5. exp = a+, string left = 

4. exps = [a+, b+], string = 
5. exp = a+, string left = 

string = , expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = a
5. exp = a, string left = 

string = a, expression = a -> true

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = a
5. exp = a+, string left = 

string = a, expression = a+ -> true

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = a
5. exp = a+, string left = 

string = a, expression = a* -> true

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = a
5. exp = b, string left = a

string = a, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = a
5. exp = b+, string left = a

string = a, expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = a
5. exp = b+, string left = a

4. exps = [], string = a

string = a, expression = b* -> false

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = a
5. exp = a, string left = 
5. exp = a+, string left = 

4. exps = [a], string = a
5. exp = a, string left = 

string = a, expression = aa* -> true

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = a
5. exp = a, string left = 
5. exp = a+, string left = 

string = a, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = a
5. exp = a, string left = 
5. exp = a+, string left = 

4. exps = [a], string = a
5. exp = a, string left = 

string = a, expression = a*a -> true

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = a
5. exp = a, string left = 
5. exp = a+, string left = 

string = a, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = a
5. exp = b, string left = a

4. exps = [b], string = a
5. exp = b, string left = a

string = a, expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = a
5. exp = b, string left = a

string = a, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = a
5. exp = b, string left = a

4. exps = [b], string = a
5. exp = b, string left = a

string = a, expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = a
5. exp = b, string left = a

string = a, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = a
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+], string = a
5. exp = b+, string left = a

4. exps = [a+], string = a
5. exp = a+, string left = 

string = a, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = a
5. exp = b+, string left = a

4. exps = [a+], string = a
5. exp = a+, string left = 

string = a, expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = a
5. exp = a+, string left = 
5. exp = b+, string left = 

string = a, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = a
5. exp = b+, string left = a

string = a, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = a
5. exp = a+, string left = 
5. exp = b, string left = 

4. exps = [b, b+], string = a
5. exp = b, string left = a

string = a, expression = a*bb+ -> false

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = a
5. exp = a+, string left = 
5. exp = b, string left = 

4. exps = [a+, b], string = a
5. exp = a+, string left = 
5. exp = b, string left = 

string = a, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = a
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+, a+], string = a
5. exp = b+, string left = a

4. exps = [a+, a+], string = a
5. exp = a+, string left = 
5. exp = a+, string left = 

4. exps = [a+], string = a
5. exp = a+, string left = 

string = a, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = a
5. exp = b+, string left = a

4. exps = [a+, b+], string = a
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+, b+], string = a
5. exp = b+, string left = a

4. exps = [b+], string = a
5. exp = b+, string left = a

4. exps = [b+, a+], string = a
5. exp = b+, string left = a

4. exps = [a+], string = a
5. exp = a+, string left = 

string = a, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = a
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [a+, a+, b+], string = a
5. exp = a+, string left = 
5. exp = a+, string left = 

4. exps = [a+, b+, b+], string = a
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [a+, b+], string = a
5. exp = a+, string left = 
5. exp = b+, string left = 

string = a, expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = aa
5. exp = a, string left = a

string = aa, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = aa
5. exp = a+, string left = 

string = aa, expression = a+ -> true

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = aa
5. exp = a+, string left = 

string = aa, expression = a* -> true

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = aa
5. exp = b, string left = aa

string = aa, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = aa
5. exp = b+, string left = aa

string = aa, expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = aa
5. exp = b+, string left = aa

4. exps = [], string = aa

string = aa, expression = b* -> false

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = aa
5. exp = a, string left = a
5. exp = a+, string left = 

string = aa, expression = aa* -> true

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = aa
5. exp = a, string left = a
5. exp = a+, string left = 

string = aa, expression = aa+ -> true

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = aa
5. exp = a, string left = a
5. exp = a+, string left = 

string = aa, expression = a*a -> true

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = aa
5. exp = a, string left = a
5. exp = a+, string left = 

string = aa, expression = a+a -> true

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = aa
5. exp = b, string left = aa

4. exps = [b], string = aa
5. exp = b, string left = aa

string = aa, expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = aa
5. exp = b, string left = aa

string = aa, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = aa
5. exp = b, string left = aa

4. exps = [b], string = aa
5. exp = b, string left = aa

string = aa, expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = aa
5. exp = b, string left = aa

string = aa, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = aa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+], string = aa
5. exp = b+, string left = aa

4. exps = [a+], string = aa
5. exp = a+, string left = 

string = aa, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = aa
5. exp = b+, string left = aa

4. exps = [a+], string = aa
5. exp = a+, string left = 

string = aa, expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = aa
5. exp = a+, string left = 
5. exp = b+, string left = 

string = aa, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = aa
5. exp = b+, string left = aa

string = aa, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = aa
5. exp = a+, string left = 
5. exp = b, string left = 

4. exps = [b, b+], string = aa
5. exp = b, string left = aa

string = aa, expression = a*bb+ -> false

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = aa
5. exp = a+, string left = 
5. exp = b, string left = 

4. exps = [a+, b], string = aa
5. exp = a+, string left = 
5. exp = b, string left = 

string = aa, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = aa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+, a+], string = aa
5. exp = b+, string left = aa

4. exps = [a+, a+], string = aa
5. exp = a+, string left = 
5. exp = a+, string left = 

4. exps = [a+], string = aa
5. exp = a+, string left = 

string = aa, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = aa
5. exp = b+, string left = aa

4. exps = [a+, b+], string = aa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+, b+], string = aa
5. exp = b+, string left = aa

4. exps = [b+], string = aa
5. exp = b+, string left = aa

4. exps = [b+, a+], string = aa
5. exp = b+, string left = aa

4. exps = [a+], string = aa
5. exp = a+, string left = 

string = aa, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = aa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [a+, a+, b+], string = aa
5. exp = a+, string left = 
5. exp = a+, string left = 

4. exps = [a+, b+, b+], string = aa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [a+, b+], string = aa
5. exp = a+, string left = 
5. exp = b+, string left = 

string = aa, expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = aaa
5. exp = a, string left = aa

string = aaa, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = aaa
5. exp = a+, string left = 

string = aaa, expression = a+ -> true

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = aaa
5. exp = a+, string left = 

string = aaa, expression = a* -> true

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = aaa
5. exp = b, string left = aaa

string = aaa, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = aaa
5. exp = b+, string left = aaa

string = aaa, expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = aaa
5. exp = b+, string left = aaa

4. exps = [], string = aaa

string = aaa, expression = b* -> false

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = aaa
5. exp = a, string left = aa
5. exp = a+, string left = 

string = aaa, expression = aa* -> true

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = aaa
5. exp = a, string left = aa
5. exp = a+, string left = 

string = aaa, expression = aa+ -> true

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = aaa
5. exp = a, string left = aa
5. exp = a+, string left = 

string = aaa, expression = a*a -> true

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = aaa
5. exp = a, string left = aa
5. exp = a+, string left = 

string = aaa, expression = a+a -> true

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = aaa
5. exp = b, string left = aaa

4. exps = [b], string = aaa
5. exp = b, string left = aaa

string = aaa, expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = aaa
5. exp = b, string left = aaa

string = aaa, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = aaa
5. exp = b, string left = aaa

4. exps = [b], string = aaa
5. exp = b, string left = aaa

string = aaa, expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = aaa
5. exp = b, string left = aaa

string = aaa, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = aaa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+], string = aaa
5. exp = b+, string left = aaa

4. exps = [a+], string = aaa
5. exp = a+, string left = 

string = aaa, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = aaa
5. exp = b+, string left = aaa

4. exps = [a+], string = aaa
5. exp = a+, string left = 

string = aaa, expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = aaa
5. exp = a+, string left = 
5. exp = b+, string left = 

string = aaa, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = aaa
5. exp = b+, string left = aaa

string = aaa, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = aaa
5. exp = a+, string left = 
5. exp = b, string left = 

4. exps = [b, b+], string = aaa
5. exp = b, string left = aaa

string = aaa, expression = a*bb+ -> false

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = aaa
5. exp = a+, string left = 
5. exp = b, string left = 

4. exps = [a+, b], string = aaa
5. exp = a+, string left = 
5. exp = b, string left = 

string = aaa, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = aaa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+, a+], string = aaa
5. exp = b+, string left = aaa

4. exps = [a+, a+], string = aaa
5. exp = a+, string left = 
5. exp = a+, string left = 

4. exps = [a+], string = aaa
5. exp = a+, string left = 

string = aaa, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = aaa
5. exp = b+, string left = aaa

4. exps = [a+, b+], string = aaa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [b+, b+], string = aaa
5. exp = b+, string left = aaa

4. exps = [b+], string = aaa
5. exp = b+, string left = aaa

4. exps = [b+, a+], string = aaa
5. exp = b+, string left = aaa

4. exps = [a+], string = aaa
5. exp = a+, string left = 

string = aaa, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = aaa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [a+, a+, b+], string = aaa
5. exp = a+, string left = 
5. exp = a+, string left = 

4. exps = [a+, b+, b+], string = aaa
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [a+, b+], string = aaa
5. exp = a+, string left = 
5. exp = b+, string left = 

string = aaa, expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = b
5. exp = a, string left = b

string = b, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = b
5. exp = a+, string left = b

string = b, expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = b
5. exp = a+, string left = b

4. exps = [], string = b

string = b, expression = a* -> false

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = b
5. exp = b, string left = 

string = b, expression = b -> true

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = b
5. exp = b+, string left = 

string = b, expression = b+ -> true

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = b
5. exp = b+, string left = 

string = b, expression = b* -> true

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = b
5. exp = a, string left = b

4. exps = [a], string = b
5. exp = a, string left = b

string = b, expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = b
5. exp = a, string left = b

string = b, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = b
5. exp = a, string left = b

4. exps = [a], string = b
5. exp = a, string left = b

string = b, expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = b
5. exp = a, string left = b

string = b, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = b
5. exp = b, string left = 
5. exp = b+, string left = 

4. exps = [b], string = b
5. exp = b, string left = 

string = b, expression = bb* -> true

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = b
5. exp = b, string left = 
5. exp = b+, string left = 

string = b, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = b
5. exp = b, string left = 
5. exp = b+, string left = 

4. exps = [b], string = b
5. exp = b, string left = 

string = b, expression = b*b -> true

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = b
5. exp = b, string left = 
5. exp = b+, string left = 

string = b, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = b
5. exp = a+, string left = b

4. exps = [b+], string = b
5. exp = b+, string left = 

string = b, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = b
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+], string = b
5. exp = a+, string left = b

4. exps = [b+], string = b
5. exp = b+, string left = 

string = b, expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = b
5. exp = a+, string left = b

string = b, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = b
5. exp = b+, string left = 
5. exp = a+, string left = 

string = b, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = b
5. exp = a+, string left = b

4. exps = [b, b+], string = b
5. exp = b, string left = 
5. exp = b+, string left = 

string = b, expression = a*bb+ -> false

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = b
5. exp = a+, string left = b

4. exps = [a+, b], string = b
5. exp = a+, string left = b

string = b, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = b
5. exp = a+, string left = b

4. exps = [b+, a+], string = b
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, a+], string = b
5. exp = a+, string left = b

4. exps = [a+], string = b
5. exp = a+, string left = b

4. exps = [a+, b+], string = b
5. exp = a+, string left = b

4. exps = [b+], string = b
5. exp = b+, string left = 

string = b, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = b
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, b+], string = b
5. exp = a+, string left = b

4. exps = [b+, b+], string = b
5. exp = b+, string left = 
5. exp = b+, string left = 

4. exps = [b+], string = b
5. exp = b+, string left = 

string = b, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = b
5. exp = a+, string left = b

4. exps = [a+, a+, b+], string = b
5. exp = a+, string left = b

4. exps = [a+, b+, b+], string = b
5. exp = a+, string left = b

4. exps = [a+, b+], string = b
5. exp = a+, string left = b

string = b, expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = bb
5. exp = a, string left = bb

string = bb, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = bb
5. exp = a+, string left = bb

string = bb, expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = bb
5. exp = a+, string left = bb

4. exps = [], string = bb

string = bb, expression = a* -> false

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = bb
5. exp = b, string left = b

string = bb, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = bb
5. exp = b+, string left = 

string = bb, expression = b+ -> true

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = bb
5. exp = b+, string left = 

string = bb, expression = b* -> true

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = bb
5. exp = a, string left = bb

4. exps = [a], string = bb
5. exp = a, string left = bb

string = bb, expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = bb
5. exp = a, string left = bb

string = bb, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = bb
5. exp = a, string left = bb

4. exps = [a], string = bb
5. exp = a, string left = bb

string = bb, expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = bb
5. exp = a, string left = bb

string = bb, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = bb
5. exp = b, string left = b
5. exp = b+, string left = 

string = bb, expression = bb* -> true

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = bb
5. exp = b, string left = b
5. exp = b+, string left = 

string = bb, expression = bb+ -> true

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = bb
5. exp = b, string left = b
5. exp = b+, string left = 

string = bb, expression = b*b -> true

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = bb
5. exp = b, string left = b
5. exp = b+, string left = 

string = bb, expression = b+b -> true

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = bb
5. exp = a+, string left = bb

4. exps = [b+], string = bb
5. exp = b+, string left = 

string = bb, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = bb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+], string = bb
5. exp = a+, string left = bb

4. exps = [b+], string = bb
5. exp = b+, string left = 

string = bb, expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = bb
5. exp = a+, string left = bb

string = bb, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = bb
5. exp = b+, string left = 
5. exp = a+, string left = 

string = bb, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = bb
5. exp = a+, string left = bb

4. exps = [b, b+], string = bb
5. exp = b, string left = b
5. exp = b+, string left = 

string = bb, expression = a*bb+ -> true

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = bb
5. exp = a+, string left = bb

4. exps = [a+, b], string = bb
5. exp = a+, string left = bb

string = bb, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = bb
5. exp = a+, string left = bb

4. exps = [b+, a+], string = bb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, a+], string = bb
5. exp = a+, string left = bb

4. exps = [a+], string = bb
5. exp = a+, string left = bb

4. exps = [a+, b+], string = bb
5. exp = a+, string left = bb

4. exps = [b+], string = bb
5. exp = b+, string left = 

string = bb, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = bb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, b+], string = bb
5. exp = a+, string left = bb

4. exps = [b+, b+], string = bb
5. exp = b+, string left = 
5. exp = b+, string left = 

4. exps = [b+], string = bb
5. exp = b+, string left = 

string = bb, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = bb
5. exp = a+, string left = bb

4. exps = [a+, a+, b+], string = bb
5. exp = a+, string left = bb

4. exps = [a+, b+, b+], string = bb
5. exp = a+, string left = bb

4. exps = [a+, b+], string = bb
5. exp = a+, string left = bb

string = bb, expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = bbbb
5. exp = a, string left = bbbb

string = bbbb, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = bbbb
5. exp = a+, string left = bbbb

string = bbbb, expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [], string = bbbb

string = bbbb, expression = a* -> false

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = bbbb
5. exp = b, string left = bbb

string = bbbb, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = bbbb
5. exp = b+, string left = 

string = bbbb, expression = b+ -> true

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = bbbb
5. exp = b+, string left = 

string = bbbb, expression = b* -> true

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = bbbb
5. exp = a, string left = bbbb

4. exps = [a], string = bbbb
5. exp = a, string left = bbbb

string = bbbb, expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = bbbb
5. exp = a, string left = bbbb

string = bbbb, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = bbbb
5. exp = a, string left = bbbb

4. exps = [a], string = bbbb
5. exp = a, string left = bbbb

string = bbbb, expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = bbbb
5. exp = a, string left = bbbb

string = bbbb, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = bbbb
5. exp = b, string left = bbb
5. exp = b+, string left = 

string = bbbb, expression = bb* -> true

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = bbbb
5. exp = b, string left = bbb
5. exp = b+, string left = 

string = bbbb, expression = bb+ -> true

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = bbbb
5. exp = b, string left = bbb
5. exp = b+, string left = 

string = bbbb, expression = b*b -> true

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = bbbb
5. exp = b, string left = bbb
5. exp = b+, string left = 

string = bbbb, expression = b+b -> true

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [b+], string = bbbb
5. exp = b+, string left = 

string = bbbb, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = bbbb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [b+], string = bbbb
5. exp = b+, string left = 

string = bbbb, expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = bbbb
5. exp = a+, string left = bbbb

string = bbbb, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = bbbb
5. exp = b+, string left = 
5. exp = a+, string left = 

string = bbbb, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [b, b+], string = bbbb
5. exp = b, string left = bbb
5. exp = b+, string left = 

string = bbbb, expression = a*bb+ -> true

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [a+, b], string = bbbb
5. exp = a+, string left = bbbb

string = bbbb, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [b+, a+], string = bbbb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, a+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [a+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [a+, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [b+], string = bbbb
5. exp = b+, string left = 

string = bbbb, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = bbbb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [b+, b+], string = bbbb
5. exp = b+, string left = 
5. exp = b+, string left = 

4. exps = [b+], string = bbbb
5. exp = b+, string left = 

string = bbbb, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [a+, a+, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [a+, b+, b+], string = bbbb
5. exp = a+, string left = bbbb

4. exps = [a+, b+], string = bbbb
5. exp = a+, string left = bbbb

string = bbbb, expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = ba
5. exp = a, string left = ba

string = ba, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = ba
5. exp = a+, string left = ba

string = ba, expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = ba
5. exp = a+, string left = ba

4. exps = [], string = ba

string = ba, expression = a* -> false

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = ba
5. exp = b, string left = a

string = ba, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = ba
5. exp = b+, string left = a

string = ba, expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = ba
5. exp = b+, string left = a

4. exps = [], string = ba

string = ba, expression = b* -> false

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = ba
5. exp = a, string left = ba

4. exps = [a], string = ba
5. exp = a, string left = ba

string = ba, expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = ba
5. exp = a, string left = ba

string = ba, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = ba
5. exp = a, string left = ba

4. exps = [a], string = ba
5. exp = a, string left = ba

string = ba, expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = ba
5. exp = a, string left = ba

string = ba, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = ba
5. exp = b, string left = a
5. exp = b+, string left = a

4. exps = [b], string = ba
5. exp = b, string left = a

string = ba, expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = ba
5. exp = b, string left = a
5. exp = b+, string left = a

string = ba, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = ba
5. exp = b, string left = a
5. exp = b+, string left = a

4. exps = [b], string = ba
5. exp = b, string left = a

string = ba, expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = ba
5. exp = b, string left = a
5. exp = b+, string left = a

string = ba, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = ba
5. exp = a+, string left = ba

4. exps = [b+], string = ba
5. exp = b+, string left = a

4. exps = [a+], string = ba
5. exp = a+, string left = ba

4. exps = [], string = ba

string = ba, expression = a*b* -> false

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = ba
5. exp = b+, string left = a
5. exp = a+, string left = 

string = ba, expression = b*a* -> true

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = ba
5. exp = a+, string left = ba

string = ba, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = ba
5. exp = b+, string left = a
5. exp = a+, string left = 

string = ba, expression = b+a+ -> true

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = ba
5. exp = a+, string left = ba

4. exps = [b, b+], string = ba
5. exp = b, string left = a
5. exp = b+, string left = a

string = ba, expression = a*bb+ -> false

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = ba
5. exp = a+, string left = ba

4. exps = [a+, b], string = ba
5. exp = a+, string left = ba

string = ba, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = ba
5. exp = a+, string left = ba

4. exps = [b+, a+], string = ba
5. exp = b+, string left = a
5. exp = a+, string left = 

string = ba, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = ba
5. exp = b+, string left = a
5. exp = a+, string left = 
5. exp = b+, string left = 

4. exps = [a+, b+], string = ba
5. exp = a+, string left = ba

4. exps = [b+, b+], string = ba
5. exp = b+, string left = a
5. exp = b+, string left = a

4. exps = [b+], string = ba
5. exp = b+, string left = a

4. exps = [b+, a+], string = ba
5. exp = b+, string left = a
5. exp = a+, string left = 

string = ba, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = ba
5. exp = a+, string left = ba

4. exps = [a+, a+, b+], string = ba
5. exp = a+, string left = ba

4. exps = [a+, b+, b+], string = ba
5. exp = a+, string left = ba

4. exps = [a+, b+], string = ba
5. exp = a+, string left = ba

string = ba, expression = a+b*a*b+ -> false

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = abb
5. exp = a, string left = bb

string = abb, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = abb
5. exp = a+, string left = bb

string = abb, expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = abb
5. exp = a+, string left = bb

4. exps = [], string = abb

string = abb, expression = a* -> false

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = abb
5. exp = b, string left = abb

string = abb, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = abb
5. exp = b+, string left = abb

string = abb, expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = abb
5. exp = b+, string left = abb

4. exps = [], string = abb

string = abb, expression = b* -> false

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = abb
5. exp = a, string left = bb
5. exp = a+, string left = bb

4. exps = [a], string = abb
5. exp = a, string left = bb

string = abb, expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = abb
5. exp = a, string left = bb
5. exp = a+, string left = bb

string = abb, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = abb
5. exp = a, string left = bb
5. exp = a+, string left = bb

4. exps = [a], string = abb
5. exp = a, string left = bb

string = abb, expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = abb
5. exp = a, string left = bb
5. exp = a+, string left = bb

string = abb, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = abb
5. exp = b, string left = abb

4. exps = [b], string = abb
5. exp = b, string left = abb

string = abb, expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = abb
5. exp = b, string left = abb

string = abb, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = abb
5. exp = b, string left = abb

4. exps = [b], string = abb
5. exp = b, string left = abb

string = abb, expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = abb
5. exp = b, string left = abb

string = abb, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 

string = abb, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = abb
5. exp = b+, string left = abb

4. exps = [a+], string = abb
5. exp = a+, string left = bb

4. exps = [b+], string = abb
5. exp = b+, string left = abb

4. exps = [], string = abb

string = abb, expression = b*a* -> false

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 

string = abb, expression = a+b+ -> true

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = abb
5. exp = b+, string left = abb

string = abb, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = abb
5. exp = a+, string left = bb
5. exp = b, string left = b
5. exp = b+, string left = 

string = abb, expression = a*bb+ -> true

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = abb
5. exp = a+, string left = bb
5. exp = b, string left = b
5. exp = b+, string left = 

string = abb, expression = a+bb* -> true

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [b+, a+], string = abb
5. exp = b+, string left = abb

4. exps = [a+, a+], string = abb
5. exp = a+, string left = bb
5. exp = a+, string left = bb

4. exps = [a+], string = abb
5. exp = a+, string left = bb

4. exps = [a+, b+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 

string = abb, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = abb
5. exp = b+, string left = abb

4. exps = [a+, b+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 

string = abb, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, a+, b+], string = abb
5. exp = a+, string left = bb
5. exp = a+, string left = bb

4. exps = [a+, b+, b+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 
5. exp = b+, string left = 

4. exps = [a+, b+], string = abb
5. exp = a+, string left = bb
5. exp = b+, string left = 

string = abb, expression = a+b*a*b+ -> true

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = abab
5. exp = a, string left = bab

string = abab, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = abab
5. exp = a+, string left = bab

string = abab, expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = abab
5. exp = a+, string left = bab

4. exps = [], string = abab

string = abab, expression = a* -> false

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = abab
5. exp = b, string left = abab

string = abab, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = abab
5. exp = b+, string left = abab

string = abab, expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = abab
5. exp = b+, string left = abab

4. exps = [], string = abab

string = abab, expression = b* -> false

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = abab
5. exp = a, string left = bab
5. exp = a+, string left = bab

4. exps = [a], string = abab
5. exp = a, string left = bab

string = abab, expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = abab
5. exp = a, string left = bab
5. exp = a+, string left = bab

string = abab, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = abab
5. exp = a, string left = bab
5. exp = a+, string left = bab

4. exps = [a], string = abab
5. exp = a, string left = bab

string = abab, expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = abab
5. exp = a, string left = bab
5. exp = a+, string left = bab

string = abab, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = abab
5. exp = b, string left = abab

4. exps = [b], string = abab
5. exp = b, string left = abab

string = abab, expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = abab
5. exp = b, string left = abab

string = abab, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = abab
5. exp = b, string left = abab

4. exps = [b], string = abab
5. exp = b, string left = abab

string = abab, expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = abab
5. exp = b, string left = abab

string = abab, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = abab
5. exp = a+, string left = bab
5. exp = b+, string left = ab

4. exps = [b+], string = abab
5. exp = b+, string left = abab

4. exps = [a+], string = abab
5. exp = a+, string left = bab

4. exps = [], string = abab

string = abab, expression = a*b* -> false

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = abab
5. exp = b+, string left = abab

4. exps = [a+], string = abab
5. exp = a+, string left = bab

4. exps = [b+], string = abab
5. exp = b+, string left = abab

4. exps = [], string = abab

string = abab, expression = b*a* -> false

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = abab
5. exp = a+, string left = bab
5. exp = b+, string left = ab

string = abab, expression = a+b+ -> false

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = abab
5. exp = b+, string left = abab

string = abab, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = abab
5. exp = a+, string left = bab
5. exp = b, string left = ab
5. exp = b+, string left = ab

4. exps = [b, b+], string = abab
5. exp = b, string left = abab

string = abab, expression = a*bb+ -> false

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = abab
5. exp = a+, string left = bab
5. exp = b, string left = ab
5. exp = b+, string left = ab

4. exps = [a+, b], string = abab
5. exp = a+, string left = bab
5. exp = b, string left = ab

string = abab, expression = a+bb* -> false

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = abab
5. exp = a+, string left = bab
5. exp = b+, string left = ab
5. exp = a+, string left = b

4. exps = [b+, a+], string = abab
5. exp = b+, string left = abab

4. exps = [a+, a+], string = abab
5. exp = a+, string left = bab
5. exp = a+, string left = bab

4. exps = [a+], string = abab
5. exp = a+, string left = bab

4. exps = [a+, b+], string = abab
5. exp = a+, string left = bab
5. exp = b+, string left = ab

4. exps = [b+], string = abab
5. exp = b+, string left = abab

4. exps = [a+], string = abab
5. exp = a+, string left = bab

4. exps = [], string = abab

string = abab, expression = a*b*a* -> false

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = abab
5. exp = b+, string left = abab

4. exps = [a+, b+], string = abab
5. exp = a+, string left = bab
5. exp = b+, string left = ab

4. exps = [b+, b+], string = abab
5. exp = b+, string left = abab

4. exps = [b+], string = abab
5. exp = b+, string left = abab

4. exps = [b+, a+], string = abab
5. exp = b+, string left = abab

4. exps = [a+], string = abab
5. exp = a+, string left = bab

4. exps = [b+], string = abab
5. exp = b+, string left = abab

4. exps = [], string = abab

string = abab, expression = b*a*b* -> false

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = abab
5. exp = a+, string left = bab
5. exp = b+, string left = ab
5. exp = a+, string left = b
5. exp = b+, string left = 

string = abab, expression = a+b*a*b+ -> true

1. tokenized expression = [a]
2. indices of * : []
3. case 1 : [a] -> [a]

4. exps = [a], string = aaabbb
5. exp = a, string left = aabbb

string = aaabbb, expression = a -> false

1. tokenized expression = [a+]
2. indices of * : []
3. case 1 : [a+] -> [a+]

4. exps = [a+], string = aaabbb
5. exp = a+, string left = bbb

string = aaabbb, expression = a+ -> false

1. tokenized expression = [a*]
2. indices of * : [0]
3. case 1 : [a+] -> [a+]
3. case 2 : [] -> []

4. exps = [a+], string = aaabbb
5. exp = a+, string left = bbb

4. exps = [], string = aaabbb

string = aaabbb, expression = a* -> false

1. tokenized expression = [b]
2. indices of * : []
3. case 1 : [b] -> [b]

4. exps = [b], string = aaabbb
5. exp = b, string left = aaabbb

string = aaabbb, expression = b -> false

1. tokenized expression = [b+]
2. indices of * : []
3. case 1 : [b+] -> [b+]

4. exps = [b+], string = aaabbb
5. exp = b+, string left = aaabbb

string = aaabbb, expression = b+ -> false

1. tokenized expression = [b*]
2. indices of * : [0]
3. case 1 : [b+] -> [b+]
3. case 2 : [] -> []

4. exps = [b+], string = aaabbb
5. exp = b+, string left = aaabbb

4. exps = [], string = aaabbb

string = aaabbb, expression = b* -> false

1. tokenized expression = [a, a*]
2. indices of * : [1]
3. case 1 : [a, a+] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = aaabbb
5. exp = a, string left = aabbb
5. exp = a+, string left = bbb

4. exps = [a], string = aaabbb
5. exp = a, string left = aabbb

string = aaabbb, expression = aa* -> false

1. tokenized expression = [a, a+]
2. indices of * : []
3. case 1 : [a, a+] -> [a, a+]

4. exps = [a, a+], string = aaabbb
5. exp = a, string left = aabbb
5. exp = a+, string left = bbb

string = aaabbb, expression = aa+ -> false

1. tokenized expression = [a*, a]
2. indices of * : [0]
3. case 1 : [a+, a] -> [a, a+]
3. case 2 : [a] -> [a]

4. exps = [a, a+], string = aaabbb
5. exp = a, string left = aabbb
5. exp = a+, string left = bbb

4. exps = [a], string = aaabbb
5. exp = a, string left = aabbb

string = aaabbb, expression = a*a -> false

1. tokenized expression = [a+, a]
2. indices of * : []
3. case 1 : [a+, a] -> [a, a+]

4. exps = [a, a+], string = aaabbb
5. exp = a, string left = aabbb
5. exp = a+, string left = bbb

string = aaabbb, expression = a+a -> false

1. tokenized expression = [b, b*]
2. indices of * : [1]
3. case 1 : [b, b+] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = aaabbb
5. exp = b, string left = aaabbb

4. exps = [b], string = aaabbb
5. exp = b, string left = aaabbb

string = aaabbb, expression = bb* -> false

1. tokenized expression = [b, b+]
2. indices of * : []
3. case 1 : [b, b+] -> [b, b+]

4. exps = [b, b+], string = aaabbb
5. exp = b, string left = aaabbb

string = aaabbb, expression = bb+ -> false

1. tokenized expression = [b*, b]
2. indices of * : [0]
3. case 1 : [b+, b] -> [b, b+]
3. case 2 : [b] -> [b]

4. exps = [b, b+], string = aaabbb
5. exp = b, string left = aaabbb

4. exps = [b], string = aaabbb
5. exp = b, string left = aaabbb

string = aaabbb, expression = b*b -> false

1. tokenized expression = [b+, b]
2. indices of * : []
3. case 1 : [b+, b] -> [b, b+]

4. exps = [b, b+], string = aaabbb
5. exp = b, string left = aaabbb

string = aaabbb, expression = b+b -> false

1. tokenized expression = [a*, b*]
2. indices of * : [0, 1]
3. case 1 : [a+, b+] -> [a+, b+]
3. case 2 : [b+] -> [b+]
3. case 3 : [a+] -> [a+]
3. case 4 : [] -> []

4. exps = [a+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 

string = aaabbb, expression = a*b* -> true

1. tokenized expression = [b*, a*]
2. indices of * : [0, 1]
3. case 1 : [b+, a+] -> [b+, a+]
3. case 2 : [a+] -> [a+]
3. case 3 : [b+] -> [b+]
3. case 4 : [] -> []

4. exps = [b+, a+], string = aaabbb
5. exp = b+, string left = aaabbb

4. exps = [a+], string = aaabbb
5. exp = a+, string left = bbb

4. exps = [b+], string = aaabbb
5. exp = b+, string left = aaabbb

4. exps = [], string = aaabbb

string = aaabbb, expression = b*a* -> false

1. tokenized expression = [a+, b+]
2. indices of * : []
3. case 1 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 

string = aaabbb, expression = a+b+ -> true

1. tokenized expression = [b+, a+]
2. indices of * : []
3. case 1 : [b+, a+] -> [b+, a+]

4. exps = [b+, a+], string = aaabbb
5. exp = b+, string left = aaabbb

string = aaabbb, expression = b+a+ -> false

1. tokenized expression = [a*, b, b+]
2. indices of * : [0]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [b, b+] -> [b, b+]

4. exps = [a+, b, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b, string left = bb
5. exp = b+, string left = 

string = aaabbb, expression = a*bb+ -> true

1. tokenized expression = [a+, b, b*]
2. indices of * : [2]
3. case 1 : [a+, b, b+] -> [a+, b, b+]
3. case 2 : [a+, b] -> [a+, b]

4. exps = [a+, b, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b, string left = bb
5. exp = b+, string left = 

string = aaabbb, expression = a+bb* -> true

1. tokenized expression = [a*, b*, a*]
2. indices of * : [0, 1, 2]
3. case 1 : [a+, b+, a+] -> [a+, b+, a+]
3. case 2 : [b+, a+] -> [b+, a+]
3. case 3 : [a+, a+] -> [a+, a+]
3. case 4 : [a+] -> [a+]
3. case 5 : [a+, b+] -> [a+, b+]
3. case 6 : [b+] -> [b+]
3. case 7 : [a+] -> [a+]
3. case 8 : [] -> []

4. exps = [a+, b+, a+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [b+, a+], string = aaabbb
5. exp = b+, string left = aaabbb

4. exps = [a+, a+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = a+, string left = bbb

4. exps = [a+], string = aaabbb
5. exp = a+, string left = bbb

4. exps = [a+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 

string = aaabbb, expression = a*b*a* -> true

1. tokenized expression = [b*, a*, b*]
2. indices of * : [0, 1, 2]
3. case 1 : [b+, a+, b+] -> [b+, a+, b+]
3. case 2 : [a+, b+] -> [a+, b+]
3. case 3 : [b+, b+] -> [b+, b+]
3. case 4 : [b+] -> [b+]
3. case 5 : [b+, a+] -> [b+, a+]
3. case 6 : [a+] -> [a+]
3. case 7 : [b+] -> [b+]
3. case 8 : [] -> []

4. exps = [b+, a+, b+], string = aaabbb
5. exp = b+, string left = aaabbb

4. exps = [a+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 

string = aaabbb, expression = b*a*b* -> true

1. tokenized expression = [a+, b*, a*, b+]
2. indices of * : [1, 2]
3. case 1 : [a+, b+, a+, b+] -> [a+, b+, a+, b+]
3. case 2 : [a+, a+, b+] -> [a+, a+, b+]
3. case 3 : [a+, b+, b+] -> [a+, b+, b+]
3. case 4 : [a+, b+] -> [a+, b+]

4. exps = [a+, b+, a+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 
5. exp = a+, string left = 

4. exps = [a+, a+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = a+, string left = bbb

4. exps = [a+, b+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 
5. exp = b+, string left = 

4. exps = [a+, b+], string = aaabbb
5. exp = a+, string left = bbb
5. exp = b+, string left = 

string = aaabbb, expression = a+b*a*b+ -> true
```
