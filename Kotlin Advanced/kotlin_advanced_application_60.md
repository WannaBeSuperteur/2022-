## 요구사항 분석
문자열 ```string```이 문자열의 형식 ```expression```과 일치하는지 조건 ```condition```의 집합을 이용하여 검사하려고 한다.

예를 들어 ```string```이 ```aaa|bb ccc*```이고 ```expression```이 ```A|B C*```이며, ```condition```의 집합이 다음과 같을 때,
```kotlin
A -> a,aa,aaa
B -> bb,bab,baab
C -> ccc,dddd
```
```A|B C*```의 ```A```를 ```aaa```, ```B```를 ```bb```, ```C```를 ```ccc```로 대응시키면 ```string```과 일치하므로 반환값은 ```true```이다.

단, 각각의 ```condition```의 ```->```의 왼쪽은 항상 알파벳 대문자 1개이며, 그 오른쪽은 항상 알파벳 소문자와 ```,```만으로 구성되고, ```expression```에서 각 알파벳 대문자는 최소 1칸의 간격을 두고 있으며, ```expression```에는 알파벳 소문자가 존재하지 않는다.

### 함수
```isMatched(string, expression, condition)```

## 코드 및 실행 결과
### 코드
```kotlin
// 가정: expression의 변환 부분을 제외한 모든 부분은 알파벳 대문자가 아님

// 문자열이 expression의 형식과 매칭되는지 검사
fun isMatched(string: String, expression: String, condition: List<String>): Boolean {
    var modifiedString = expression
    println("TRY : string=${string}, exp=${expression}")
    println("1. expression (= initial modified string) = [${expression}]")
    
    // 조건을 표현식에서의 등장 순서대로 정렬 (bab aaa, B A -> true인데 false로 표시되는 버그)
    val sortedCondition = condition.sortedBy { c ->
        val firstIdx = expression.indexOfFirst {
            it == c.substringBefore(" -> ").get(0)!!
        }
        if (firstIdx < 0) { expression.length + 1 } else { firstIdx }
    }
    println("2. sorted conditions = ${sortedCondition.map { it.substringBefore(" -> ") }}")

    sortedCondition.forEachIndexed { idx, it ->
        val left = it.substringBefore(" -> ")
        val right = it.substringAfter(" -> ").split(",")
        
        var isConditionApplicable = false
        
        // 해당하는 기호가 modifiedString에 없으면 건너뛰기
        if (!modifiedString.contains(left)) {
            println("3. [${modifiedString}] does not contain ${left}")
            return@forEachIndexed // continue
        }
        
        // 각 조건을 이용하여 변환하기
        right.forEach { r ->
            
            // 조건 적용 가능 이미 확인되었으면 건너뛰기
            if (!isConditionApplicable) {
                val condAppliedString = modifiedString.replace(left, r)
                
                // 마지막 조건인 경우 일치해야 조건 적용 가능
                if (idx == condition.size - 1 || condAppliedString.none { it.isUpperCase() } ) {
                    if (condAppliedString == string) {
                        isConditionApplicable = true
                        modifiedString = condAppliedString
                    }
                }
                
                // 마지막 조건이 아닌 경우
                else {
                    val stringBeforeUpper = string.takeWhile {
                        !it.isUpperCase()
                    }
                    val newStringBeforeUpper = condAppliedString.takeWhile {
                        !it.isUpperCase()
                    }
                    val commonLength = Math.min(stringBeforeUpper.length, newStringBeforeUpper.length)
            
                    // 공통부분이 같으면 조건 적용 가능
                    if (stringBeforeUpper.take(commonLength) == newStringBeforeUpper.take(commonLength)) {
                        isConditionApplicable = true
                        modifiedString = condAppliedString
                    }
                }
            }
        }
        
        val modifiedStatus = if (isConditionApplicable) {
            "modified"
        } else {
            "preserved"
        }
        println("4. condition = ${it}, ${modifiedStatus} string = [${modifiedString}]")
        
        // 조건 적용 불가능한 경우
        if (!isConditionApplicable) {
            return false
        }
    }
    
    // 마지막 조건인 경우 일치해야 적용 가능하므로, 이 부분은 항상 true임
    println("5. final modified string = [${modifiedString}], goal = [${string}]")
    return modifiedString == string
}

fun main() {
    
    // 임시 문자열 목록
    val tempStrings = listOf<String> (
        "a", "ab", "aabb", "a a", "a b", "a c", "aa bb",
        "aa bab", "bab aaa", "aa cc", "aa dddd",
        "a aa", "a aaa", "bb aa", "bb aaa",
        "a b|c", "aa b|cc", "aaa bab|ccc", "ab c|d",
        "aaa|bb ccc*", "aa|bab cc*", "aa|baab dddd*"
    )
    
    // 임시 표현식 목록
    val tempExpressions = listOf<String> (
        "A", "B", "A B", "A C", "A D", "B A", "D A", "A B|C", "A|B C*"
    )
    
    // 임시 조건
    val tempConditions = listOf<String> (
        "A -> a,aa,aaa",
        "B -> bb,bab,baab",
        "C -> ccc,dddd",
        "D -> a,b,bb,cc"
    )
    
    // 일치 여부 검사
    tempStrings.forEach { s ->
        tempExpressions.forEach { e ->
            val result = isMatched(s, e, tempConditions)
            println("RESULT : ${s}, ${e} -> matched = ${result}\n")
        }
    }
}
```

### 실행 결과
```kotlin
TRY : string=a, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a]
3. [a] does not contain B
3. [a] does not contain C
3. [a] does not contain D
5. final modified string = [a], goal = [a]
RESULT : a, A -> matched = true

TRY : string=a, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : a, B -> matched = false

TRY : string=a, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B]
4. condition = B -> bb,bab,baab, preserved string = [a B]
RESULT : a, A B -> matched = false

TRY : string=a, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [a C]
4. condition = C -> ccc,dddd, preserved string = [a C]
RESULT : a, A C -> matched = false

TRY : string=a, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [a D]
4. condition = D -> a,b,bb,cc, preserved string = [a D]
RESULT : a, A D -> matched = false

TRY : string=a, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : a, B A -> matched = false

TRY : string=a, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [a A]
4. condition = A -> a,aa,aaa, preserved string = [a A]
RESULT : a, D A -> matched = false

TRY : string=a, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B|C]
4. condition = B -> bb,bab,baab, modified string = [a bb|C]
4. condition = C -> ccc,dddd, preserved string = [a bb|C]
RESULT : a, A B|C -> matched = false

TRY : string=a, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a|B C*]
4. condition = B -> bb,bab,baab, modified string = [a|bb C*]
4. condition = C -> ccc,dddd, preserved string = [a|bb C*]
RESULT : a, A|B C* -> matched = false

TRY : string=ab, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : ab, A -> matched = false

TRY : string=ab, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : ab, B -> matched = false

TRY : string=ab, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : ab, A B -> matched = false

TRY : string=ab, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : ab, A C -> matched = false

TRY : string=ab, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : ab, A D -> matched = false

TRY : string=ab, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : ab, B A -> matched = false

TRY : string=ab, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : ab, D A -> matched = false

TRY : string=ab, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : ab, A B|C -> matched = false

TRY : string=ab, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : ab, A|B C* -> matched = false

TRY : string=aabb, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aabb, A -> matched = false

TRY : string=aabb, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aabb, B -> matched = false

TRY : string=aabb, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : aabb, A B -> matched = false

TRY : string=aabb, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : aabb, A C -> matched = false

TRY : string=aabb, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : aabb, A D -> matched = false

TRY : string=aabb, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aabb, B A -> matched = false

TRY : string=aabb, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aabb, D A -> matched = false

TRY : string=aabb, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : aabb, A B|C -> matched = false

TRY : string=aabb, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : aabb, A|B C* -> matched = false

TRY : string=a a, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : a a, A -> matched = false

TRY : string=a a, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : a a, B -> matched = false

TRY : string=a a, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B]
4. condition = B -> bb,bab,baab, preserved string = [a B]
RESULT : a a, A B -> matched = false

TRY : string=a a, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [a C]
4. condition = C -> ccc,dddd, preserved string = [a C]
RESULT : a a, A C -> matched = false

TRY : string=a a, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [a D]
4. condition = D -> a,b,bb,cc, modified string = [a a]
3. [a a] does not contain B
3. [a a] does not contain C
5. final modified string = [a a], goal = [a a]
RESULT : a a, A D -> matched = true

TRY : string=a a, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : a a, B A -> matched = false

TRY : string=a a, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [a A]
4. condition = A -> a,aa,aaa, modified string = [a a]
3. [a a] does not contain B
3. [a a] does not contain C
5. final modified string = [a a], goal = [a a]
RESULT : a a, D A -> matched = true

TRY : string=a a, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B|C]
4. condition = B -> bb,bab,baab, preserved string = [a B|C]
RESULT : a a, A B|C -> matched = false

TRY : string=a a, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : a a, A|B C* -> matched = false

TRY : string=a b, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : a b, A -> matched = false

TRY : string=a b, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : a b, B -> matched = false

TRY : string=a b, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B]
4. condition = B -> bb,bab,baab, preserved string = [a B]
RESULT : a b, A B -> matched = false

TRY : string=a b, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [a C]
4. condition = C -> ccc,dddd, preserved string = [a C]
RESULT : a b, A C -> matched = false

TRY : string=a b, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [a D]
4. condition = D -> a,b,bb,cc, modified string = [a b]
3. [a b] does not contain B
3. [a b] does not contain C
5. final modified string = [a b], goal = [a b]
RESULT : a b, A D -> matched = true

TRY : string=a b, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : a b, B A -> matched = false

TRY : string=a b, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [a A]
4. condition = A -> a,aa,aaa, preserved string = [a A]
RESULT : a b, D A -> matched = false

TRY : string=a b, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B|C]
4. condition = B -> bb,bab,baab, modified string = [a bb|C]
4. condition = C -> ccc,dddd, preserved string = [a bb|C]
RESULT : a b, A B|C -> matched = false

TRY : string=a b, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : a b, A|B C* -> matched = false

TRY : string=a c, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : a c, A -> matched = false

TRY : string=a c, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : a c, B -> matched = false

TRY : string=a c, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B]
4. condition = B -> bb,bab,baab, preserved string = [a B]
RESULT : a c, A B -> matched = false

TRY : string=a c, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [a C]
4. condition = C -> ccc,dddd, preserved string = [a C]
RESULT : a c, A C -> matched = false

TRY : string=a c, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [a D]
4. condition = D -> a,b,bb,cc, preserved string = [a D]
RESULT : a c, A D -> matched = false

TRY : string=a c, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : a c, B A -> matched = false

TRY : string=a c, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [a A]
4. condition = A -> a,aa,aaa, preserved string = [a A]
RESULT : a c, D A -> matched = false

TRY : string=a c, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B|C]
4. condition = B -> bb,bab,baab, preserved string = [a B|C]
RESULT : a c, A B|C -> matched = false

TRY : string=a c, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : a c, A|B C* -> matched = false

TRY : string=aa bb, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aa bb, A -> matched = false

TRY : string=aa bb, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aa bb, B -> matched = false

TRY : string=aa bb, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B]
4. condition = B -> bb,bab,baab, modified string = [aa bb]
3. [aa bb] does not contain C
3. [aa bb] does not contain D
5. final modified string = [aa bb], goal = [aa bb]
RESULT : aa bb, A B -> matched = true

TRY : string=aa bb, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [aa C]
4. condition = C -> ccc,dddd, preserved string = [aa C]
RESULT : aa bb, A C -> matched = false

TRY : string=aa bb, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [aa D]
4. condition = D -> a,b,bb,cc, modified string = [aa bb]
3. [aa bb] does not contain B
3. [aa bb] does not contain C
5. final modified string = [aa bb], goal = [aa bb]
RESULT : aa bb, A D -> matched = true

TRY : string=aa bb, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aa bb, B A -> matched = false

TRY : string=aa bb, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aa bb, D A -> matched = false

TRY : string=aa bb, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B|C]
4. condition = B -> bb,bab,baab, modified string = [aa bb|C]
4. condition = C -> ccc,dddd, preserved string = [aa bb|C]
RESULT : aa bb, A B|C -> matched = false

TRY : string=aa bb, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : aa bb, A|B C* -> matched = false

TRY : string=aa bab, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aa bab, A -> matched = false

TRY : string=aa bab, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aa bab, B -> matched = false

TRY : string=aa bab, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B]
4. condition = B -> bb,bab,baab, modified string = [aa bab]
3. [aa bab] does not contain C
3. [aa bab] does not contain D
5. final modified string = [aa bab], goal = [aa bab]
RESULT : aa bab, A B -> matched = true

TRY : string=aa bab, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [aa C]
4. condition = C -> ccc,dddd, preserved string = [aa C]
RESULT : aa bab, A C -> matched = false

TRY : string=aa bab, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [aa D]
4. condition = D -> a,b,bb,cc, preserved string = [aa D]
RESULT : aa bab, A D -> matched = false

TRY : string=aa bab, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aa bab, B A -> matched = false

TRY : string=aa bab, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aa bab, D A -> matched = false

TRY : string=aa bab, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B|C]
4. condition = B -> bb,bab,baab, modified string = [aa bab|C]
4. condition = C -> ccc,dddd, preserved string = [aa bab|C]
RESULT : aa bab, A B|C -> matched = false

TRY : string=aa bab, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : aa bab, A|B C* -> matched = false

TRY : string=bab aaa, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : bab aaa, A -> matched = false

TRY : string=bab aaa, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : bab aaa, B -> matched = false

TRY : string=bab aaa, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : bab aaa, A B -> matched = false

TRY : string=bab aaa, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : bab aaa, A C -> matched = false

TRY : string=bab aaa, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : bab aaa, A D -> matched = false

TRY : string=bab aaa, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, modified string = [bab A]
4. condition = A -> a,aa,aaa, modified string = [bab aaa]
3. [bab aaa] does not contain C
3. [bab aaa] does not contain D
5. final modified string = [bab aaa], goal = [bab aaa]
RESULT : bab aaa, B A -> matched = true

TRY : string=bab aaa, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : bab aaa, D A -> matched = false

TRY : string=bab aaa, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : bab aaa, A B|C -> matched = false

TRY : string=bab aaa, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : bab aaa, A|B C* -> matched = false

TRY : string=aa cc, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aa cc, A -> matched = false

TRY : string=aa cc, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aa cc, B -> matched = false

TRY : string=aa cc, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B]
4. condition = B -> bb,bab,baab, preserved string = [aa B]
RESULT : aa cc, A B -> matched = false

TRY : string=aa cc, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [aa C]
4. condition = C -> ccc,dddd, preserved string = [aa C]
RESULT : aa cc, A C -> matched = false

TRY : string=aa cc, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [aa D]
4. condition = D -> a,b,bb,cc, modified string = [aa cc]
3. [aa cc] does not contain B
3. [aa cc] does not contain C
5. final modified string = [aa cc], goal = [aa cc]
RESULT : aa cc, A D -> matched = true

TRY : string=aa cc, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aa cc, B A -> matched = false

TRY : string=aa cc, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aa cc, D A -> matched = false

TRY : string=aa cc, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B|C]
4. condition = B -> bb,bab,baab, preserved string = [aa B|C]
RESULT : aa cc, A B|C -> matched = false

TRY : string=aa cc, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : aa cc, A|B C* -> matched = false

TRY : string=aa dddd, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aa dddd, A -> matched = false

TRY : string=aa dddd, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aa dddd, B -> matched = false

TRY : string=aa dddd, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B]
4. condition = B -> bb,bab,baab, preserved string = [aa B]
RESULT : aa dddd, A B -> matched = false

TRY : string=aa dddd, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [aa C]
4. condition = C -> ccc,dddd, modified string = [aa dddd]
3. [aa dddd] does not contain B
3. [aa dddd] does not contain D
5. final modified string = [aa dddd], goal = [aa dddd]
RESULT : aa dddd, A C -> matched = true

TRY : string=aa dddd, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [aa D]
4. condition = D -> a,b,bb,cc, preserved string = [aa D]
RESULT : aa dddd, A D -> matched = false

TRY : string=aa dddd, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aa dddd, B A -> matched = false

TRY : string=aa dddd, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aa dddd, D A -> matched = false

TRY : string=aa dddd, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B|C]
4. condition = B -> bb,bab,baab, preserved string = [aa B|C]
RESULT : aa dddd, A B|C -> matched = false

TRY : string=aa dddd, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : aa dddd, A|B C* -> matched = false

TRY : string=a aa, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : a aa, A -> matched = false

TRY : string=a aa, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : a aa, B -> matched = false

TRY : string=a aa, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B]
4. condition = B -> bb,bab,baab, preserved string = [a B]
RESULT : a aa, A B -> matched = false

TRY : string=a aa, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [a C]
4. condition = C -> ccc,dddd, preserved string = [a C]
RESULT : a aa, A C -> matched = false

TRY : string=a aa, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [a D]
4. condition = D -> a,b,bb,cc, preserved string = [a D]
RESULT : a aa, A D -> matched = false

TRY : string=a aa, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : a aa, B A -> matched = false

TRY : string=a aa, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [a A]
4. condition = A -> a,aa,aaa, modified string = [a aa]
3. [a aa] does not contain B
3. [a aa] does not contain C
5. final modified string = [a aa], goal = [a aa]
RESULT : a aa, D A -> matched = true

TRY : string=a aa, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B|C]
4. condition = B -> bb,bab,baab, preserved string = [a B|C]
RESULT : a aa, A B|C -> matched = false

TRY : string=a aa, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : a aa, A|B C* -> matched = false

TRY : string=a aaa, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : a aaa, A -> matched = false

TRY : string=a aaa, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : a aaa, B -> matched = false

TRY : string=a aaa, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B]
4. condition = B -> bb,bab,baab, preserved string = [a B]
RESULT : a aaa, A B -> matched = false

TRY : string=a aaa, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [a C]
4. condition = C -> ccc,dddd, preserved string = [a C]
RESULT : a aaa, A C -> matched = false

TRY : string=a aaa, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [a D]
4. condition = D -> a,b,bb,cc, preserved string = [a D]
RESULT : a aaa, A D -> matched = false

TRY : string=a aaa, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : a aaa, B A -> matched = false

TRY : string=a aaa, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [a A]
4. condition = A -> a,aa,aaa, modified string = [a aaa]
3. [a aaa] does not contain B
3. [a aaa] does not contain C
5. final modified string = [a aaa], goal = [a aaa]
RESULT : a aaa, D A -> matched = true

TRY : string=a aaa, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B|C]
4. condition = B -> bb,bab,baab, preserved string = [a B|C]
RESULT : a aaa, A B|C -> matched = false

TRY : string=a aaa, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : a aaa, A|B C* -> matched = false

TRY : string=bb aa, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : bb aa, A -> matched = false

TRY : string=bb aa, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : bb aa, B -> matched = false

TRY : string=bb aa, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : bb aa, A B -> matched = false

TRY : string=bb aa, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : bb aa, A C -> matched = false

TRY : string=bb aa, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : bb aa, A D -> matched = false

TRY : string=bb aa, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, modified string = [bb A]
4. condition = A -> a,aa,aaa, modified string = [bb aa]
3. [bb aa] does not contain C
3. [bb aa] does not contain D
5. final modified string = [bb aa], goal = [bb aa]
RESULT : bb aa, B A -> matched = true

TRY : string=bb aa, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [bb A]
4. condition = A -> a,aa,aaa, modified string = [bb aa]
3. [bb aa] does not contain B
3. [bb aa] does not contain C
5. final modified string = [bb aa], goal = [bb aa]
RESULT : bb aa, D A -> matched = true

TRY : string=bb aa, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : bb aa, A B|C -> matched = false

TRY : string=bb aa, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : bb aa, A|B C* -> matched = false

TRY : string=bb aaa, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : bb aaa, A -> matched = false

TRY : string=bb aaa, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : bb aaa, B -> matched = false

TRY : string=bb aaa, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : bb aaa, A B -> matched = false

TRY : string=bb aaa, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : bb aaa, A C -> matched = false

TRY : string=bb aaa, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : bb aaa, A D -> matched = false

TRY : string=bb aaa, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, modified string = [bb A]
4. condition = A -> a,aa,aaa, modified string = [bb aaa]
3. [bb aaa] does not contain C
3. [bb aaa] does not contain D
5. final modified string = [bb aaa], goal = [bb aaa]
RESULT : bb aaa, B A -> matched = true

TRY : string=bb aaa, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [bb A]
4. condition = A -> a,aa,aaa, modified string = [bb aaa]
3. [bb aaa] does not contain B
3. [bb aaa] does not contain C
5. final modified string = [bb aaa], goal = [bb aaa]
RESULT : bb aaa, D A -> matched = true

TRY : string=bb aaa, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : bb aaa, A B|C -> matched = false

TRY : string=bb aaa, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : bb aaa, A|B C* -> matched = false

TRY : string=a b|c, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : a b|c, A -> matched = false

TRY : string=a b|c, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : a b|c, B -> matched = false

TRY : string=a b|c, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B]
4. condition = B -> bb,bab,baab, preserved string = [a B]
RESULT : a b|c, A B -> matched = false

TRY : string=a b|c, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [a C]
4. condition = C -> ccc,dddd, preserved string = [a C]
RESULT : a b|c, A C -> matched = false

TRY : string=a b|c, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [a D]
4. condition = D -> a,b,bb,cc, preserved string = [a D]
RESULT : a b|c, A D -> matched = false

TRY : string=a b|c, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : a b|c, B A -> matched = false

TRY : string=a b|c, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, modified string = [a A]
4. condition = A -> a,aa,aaa, preserved string = [a A]
RESULT : a b|c, D A -> matched = false

TRY : string=a b|c, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [a B|C]
4. condition = B -> bb,bab,baab, preserved string = [a B|C]
RESULT : a b|c, A B|C -> matched = false

TRY : string=a b|c, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : a b|c, A|B C* -> matched = false

TRY : string=aa b|cc, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aa b|cc, A -> matched = false

TRY : string=aa b|cc, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aa b|cc, B -> matched = false

TRY : string=aa b|cc, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B]
4. condition = B -> bb,bab,baab, preserved string = [aa B]
RESULT : aa b|cc, A B -> matched = false

TRY : string=aa b|cc, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [aa C]
4. condition = C -> ccc,dddd, preserved string = [aa C]
RESULT : aa b|cc, A C -> matched = false

TRY : string=aa b|cc, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [aa D]
4. condition = D -> a,b,bb,cc, preserved string = [aa D]
RESULT : aa b|cc, A D -> matched = false

TRY : string=aa b|cc, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aa b|cc, B A -> matched = false

TRY : string=aa b|cc, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aa b|cc, D A -> matched = false

TRY : string=aa b|cc, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa B|C]
4. condition = B -> bb,bab,baab, preserved string = [aa B|C]
RESULT : aa b|cc, A B|C -> matched = false

TRY : string=aa b|cc, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : aa b|cc, A|B C* -> matched = false

TRY : string=aaa bab|ccc, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aaa bab|ccc, A -> matched = false

TRY : string=aaa bab|ccc, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aaa bab|ccc, B -> matched = false

TRY : string=aaa bab|ccc, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aaa B]
4. condition = B -> bb,bab,baab, preserved string = [aaa B]
RESULT : aaa bab|ccc, A B -> matched = false

TRY : string=aaa bab|ccc, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, modified string = [aaa C]
4. condition = C -> ccc,dddd, preserved string = [aaa C]
RESULT : aaa bab|ccc, A C -> matched = false

TRY : string=aaa bab|ccc, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, modified string = [aaa D]
4. condition = D -> a,b,bb,cc, preserved string = [aaa D]
RESULT : aaa bab|ccc, A D -> matched = false

TRY : string=aaa bab|ccc, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aaa bab|ccc, B A -> matched = false

TRY : string=aaa bab|ccc, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aaa bab|ccc, D A -> matched = false

TRY : string=aaa bab|ccc, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aaa B|C]
4. condition = B -> bb,bab,baab, modified string = [aaa bab|C]
4. condition = C -> ccc,dddd, modified string = [aaa bab|ccc]
3. [aaa bab|ccc] does not contain D
5. final modified string = [aaa bab|ccc], goal = [aaa bab|ccc]
RESULT : aaa bab|ccc, A B|C -> matched = true

TRY : string=aaa bab|ccc, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : aaa bab|ccc, A|B C* -> matched = false

TRY : string=ab c|d, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : ab c|d, A -> matched = false

TRY : string=ab c|d, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : ab c|d, B -> matched = false

TRY : string=ab c|d, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : ab c|d, A B -> matched = false

TRY : string=ab c|d, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : ab c|d, A C -> matched = false

TRY : string=ab c|d, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : ab c|d, A D -> matched = false

TRY : string=ab c|d, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : ab c|d, B A -> matched = false

TRY : string=ab c|d, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : ab c|d, D A -> matched = false

TRY : string=ab c|d, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : ab c|d, A B|C -> matched = false

TRY : string=ab c|d, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A|B C*]
RESULT : ab c|d, A|B C* -> matched = false

TRY : string=aaa|bb ccc*, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aaa|bb ccc*, A -> matched = false

TRY : string=aaa|bb ccc*, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aaa|bb ccc*, B -> matched = false

TRY : string=aaa|bb ccc*, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : aaa|bb ccc*, A B -> matched = false

TRY : string=aaa|bb ccc*, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : aaa|bb ccc*, A C -> matched = false

TRY : string=aaa|bb ccc*, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : aaa|bb ccc*, A D -> matched = false

TRY : string=aaa|bb ccc*, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aaa|bb ccc*, B A -> matched = false

TRY : string=aaa|bb ccc*, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aaa|bb ccc*, D A -> matched = false

TRY : string=aaa|bb ccc*, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : aaa|bb ccc*, A B|C -> matched = false

TRY : string=aaa|bb ccc*, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aaa|B C*]
4. condition = B -> bb,bab,baab, modified string = [aaa|bb C*]
4. condition = C -> ccc,dddd, modified string = [aaa|bb ccc*]
3. [aaa|bb ccc*] does not contain D
5. final modified string = [aaa|bb ccc*], goal = [aaa|bb ccc*]
RESULT : aaa|bb ccc*, A|B C* -> matched = true

TRY : string=aa|bab cc*, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aa|bab cc*, A -> matched = false

TRY : string=aa|bab cc*, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aa|bab cc*, B -> matched = false

TRY : string=aa|bab cc*, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : aa|bab cc*, A B -> matched = false

TRY : string=aa|bab cc*, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : aa|bab cc*, A C -> matched = false

TRY : string=aa|bab cc*, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : aa|bab cc*, A D -> matched = false

TRY : string=aa|bab cc*, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aa|bab cc*, B A -> matched = false

TRY : string=aa|bab cc*, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aa|bab cc*, D A -> matched = false

TRY : string=aa|bab cc*, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : aa|bab cc*, A B|C -> matched = false

TRY : string=aa|bab cc*, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa|B C*]
4. condition = B -> bb,bab,baab, modified string = [aa|bab C*]
4. condition = C -> ccc,dddd, preserved string = [aa|bab C*]
RESULT : aa|bab cc*, A|B C* -> matched = false

TRY : string=aa|baab dddd*, exp=A
1. expression (= initial modified string) = [A]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A]
RESULT : aa|baab dddd*, A -> matched = false

TRY : string=aa|baab dddd*, exp=B
1. expression (= initial modified string) = [B]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B]
RESULT : aa|baab dddd*, B -> matched = false

TRY : string=aa|baab dddd*, exp=A B
1. expression (= initial modified string) = [A B]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B]
RESULT : aa|baab dddd*, A B -> matched = false

TRY : string=aa|baab dddd*, exp=A C
1. expression (= initial modified string) = [A C]
2. sorted conditions = [A, C, B, D]
4. condition = A -> a,aa,aaa, preserved string = [A C]
RESULT : aa|baab dddd*, A C -> matched = false

TRY : string=aa|baab dddd*, exp=A D
1. expression (= initial modified string) = [A D]
2. sorted conditions = [A, D, B, C]
4. condition = A -> a,aa,aaa, preserved string = [A D]
RESULT : aa|baab dddd*, A D -> matched = false

TRY : string=aa|baab dddd*, exp=B A
1. expression (= initial modified string) = [B A]
2. sorted conditions = [B, A, C, D]
4. condition = B -> bb,bab,baab, preserved string = [B A]
RESULT : aa|baab dddd*, B A -> matched = false

TRY : string=aa|baab dddd*, exp=D A
1. expression (= initial modified string) = [D A]
2. sorted conditions = [D, A, B, C]
4. condition = D -> a,b,bb,cc, preserved string = [D A]
RESULT : aa|baab dddd*, D A -> matched = false

TRY : string=aa|baab dddd*, exp=A B|C
1. expression (= initial modified string) = [A B|C]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, preserved string = [A B|C]
RESULT : aa|baab dddd*, A B|C -> matched = false

TRY : string=aa|baab dddd*, exp=A|B C*
1. expression (= initial modified string) = [A|B C*]
2. sorted conditions = [A, B, C, D]
4. condition = A -> a,aa,aaa, modified string = [aa|B C*]
4. condition = B -> bb,bab,baab, modified string = [aa|baab C*]
4. condition = C -> ccc,dddd, modified string = [aa|baab dddd*]
3. [aa|baab dddd*] does not contain D
5. final modified string = [aa|baab dddd*], goal = [aa|baab dddd*]
RESULT : aa|baab dddd*, A|B C* -> matched = true
```
