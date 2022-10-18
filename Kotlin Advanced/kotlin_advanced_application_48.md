## 요구사항 분석
다음과 같이 연산을 정의하자.
```kotlin
C = A + B
A $ B = C * C
```
이때, 다음 등식이 성립한다.
```
12 $ 4 = 256
```

```연산 정의```와 ```수식```이 주어졌을 때, ```수식```을 계산하시오.
* ```연산 정의```는 여러 줄로 구성되어 있으며, 이것을 이용하여 ```수식```의 각 줄을 계산한다.
* ```연산 정의```의 각 줄의 연산자는 가운데의 ```=```을 기준으로 양쪽 각각 최대 1개이고, ```수식```의 각 줄의 연산자는 최대 1개이고 중간에 ```=```가 없다.
* ```연산 정의```의 각 줄의 ```=``` 오른쪽에 있는 연산자는 ```연산 정의```의 다른 줄에서 정의한 연산자가 아니다.

### 데이터 클래스
데이터 클래스는 다음과 같다.
```kotlin
// 연산 정의 및 수식 목록
data class DefinitionAndFormula (
    val definition: List<String>, // 연산 정의
    val formula_list: List<String> // 계산할 각각의 수식
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 연산 정의 및 수식 목록
data class DefinitionAndFormula (
    val definition: List<String>, // 연산 정의
    val formula_list: List<String> // 계산할 각각의 수식
)

// 연산의 정의에 따라 각 수식 계산하기
fun compute(defAndFormula: DefinitionAndFormula) {
    
    // 연산 정의에 따른 치환용 map
    val replaceMap = mutableMapOf<String, String>()
    
    defAndFormula.definition.forEach {
        val left = it.substringBefore(" = ").replace(" ", "")
        val right = it.substringAfter(" = ").replace(" ", "")
        replaceMap.put(left, right)
    }
    println("1. replaceMap = ${replaceMap}")
    
    // 각 수식에 대해,
    defAndFormula.formula_list.forEach { f ->
        
        // 해당하는 기호를 치환용 map에서 찾고, 그 수식의 알파벳에 대응시켜기
        val op = f.split(" ")[1]
        val alphabetMap = mutableMapOf<String, Int>()
        
        replaceMap.forEach { d ->
            if (d.key.contains(op)) {
                val alphabet_0 = d.key.substringBefore(op)
                val alphabet_1 = d.key.substringAfter(op)
                
                alphabetMap.put(alphabet_0, f.split(" ")[0].toInt())
                alphabetMap.put(alphabet_1, f.split(" ")[2].toInt())
            }
        }
        println("2. alphabetMap = ${alphabetMap}\n")
        
        // 치환용 map에서 대응된 알파벳 및 계산된 알파벳이 보이는 대로 치환하기
        var isExitWhile = false
        
        val replaceMapCopy = replaceMap.toMutableMap()
        val replaceCount = mutableMapOf<String, Int>()
            
        replaceMapCopy.forEach {
            replaceCount.put(it.key, 0)
        }
        
        while (true) {
            println("3. replaceMapCopy = ${replaceMapCopy}")
            println("4. alphabetMap = ${alphabetMap}")
            println("5. replaceCount = ${replaceCount}\n")
            
            replaceMapCopy.forEach { d ->
                
                // 알파벳을 숫자로 바꾸기 (카운트 2회를 해야 하므로 첫번째만 바꾸기)
                alphabetMap.forEach { a ->
                    if (d.value.contains(a.key)) {
                        replaceMapCopy.put(d.key, d.value.replaceFirst(a.key, a.value.toString()))
                        replaceCount.put(d.key, replaceCount.get(d.key)!! + 1)
                    }
                }
                
                // 등호의 오른쪽이 모두 숫자인 경우
                if (replaceCount.get(d.key)!! == 2) {
                    
                    // 알파벳 또는 해당 기호인 경우
                    if (d.key.length == 1 || d.key.contains(op)) {
                        
                        // 숫자를 제외하고 (=기호) 모두 공백으로 바꾸기
                        val re = Regex("[^0-9 ]")
                        val reNum = Regex("[0-9 ]")
                        val defRightNums = re.replace(d.value, " ")
                        
                        val defRight_0 = defRightNums.substringBefore(" ").toInt()
                        val defRight_1 = defRightNums.substringAfter(" ").toInt()
                        val defRight_op = reNum.replace(d.value, "")
                        
                        // 해당 알파벳의 값 추가
                        val result = when (defRight_op) {
                            "+" -> defRight_0 + defRight_1
                            "-" -> defRight_0 - defRight_1
                            "*" -> defRight_0 * defRight_1
                            "/" -> defRight_0 / defRight_1
                            else -> 0
                        }
                        if (d.key.length == 1) {
                            alphabetMap.put(d.key, result)
                        }
                        
                        // 해당 기호인 경우
                        if (d.key.contains(op)) {
                            println("6. replaceMapCopy = ${replaceMapCopy}")
                            println("7. alphabetMap = ${alphabetMap}")
                            println("8. replaceCount = ${replaceCount}\n")
            
                            println("${d.key} 의 계산 결과 = ${result}\n")
                            isExitWhile = true
                        }
                    }
                }
            }
            
            if (isExitWhile) {
                break
            }
        }
    }
}

fun main() {
    
    // 임시 연산 정의 및 수식 목록
    val testDefinitionAndFormula = DefinitionAndFormula(
        listOf(
            "C = A + B",
            "A $ B = C * C",
            "D = E - F",
            "E & F = D * D",
            "G = H + I",
            "J = H - I",
            "H | I = G * J",
            "L ^ M = O - M", // L + M * (L + M) - M
            "O = L + N",     // L + M * (L + M)
            "N = M * K",     // M * (L + M)
            "K = L + M"
        ),
        listOf(
            "12 $ 4", "12 & 4", "12 | 4", "12 ^ 4",
            "16 $ 6", "16 & 6", "16 | 6", "16 ^ 6"
        )
    )
    
    // 연산의 정의에 따라 각 수식 계산하기
    compute(testDefinitionAndFormula)
}
```

### 실행 결과
```kotlin
1. replaceMap = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
2. alphabetMap = {A=12, B=4}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {A=12, B=4}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

3. replaceMapCopy = {C=12+4, A$B=16*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {A=12, B=4, C=16}
5. replaceCount = {C=2, A$B=1, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

6. replaceMapCopy = {C=12+4, A$B=16*16, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
7. alphabetMap = {A=12, B=4, C=16}
8. replaceCount = {C=2, A$B=2, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

A$B 의 계산 결과 = 256

2. alphabetMap = {E=12, F=4}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {E=12, F=4}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=12-4, E&F=8*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {E=12, F=4, D=8}
5. replaceCount = {C=0, A$B=0, D=2, E&F=1, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

6. replaceMapCopy = {C=A+B, A$B=C*C, D=12-4, E&F=8*8, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
7. alphabetMap = {E=12, F=4, D=8}
8. replaceCount = {C=0, A$B=0, D=2, E&F=2, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

E&F 의 계산 결과 = 64

2. alphabetMap = {H=12, I=4}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {H=12, I=4}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

6. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=12+4, J=12-4, H|I=16*8, L^M=O-M, O=L+N, N=M*K, K=L+M}
7. alphabetMap = {H=12, I=4, G=16, J=8}
8. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=2, J=2, H|I=2, L^M=0, O=0, N=0, K=0}

H|I 의 계산 결과 = 128

2. alphabetMap = {L=12, M=4}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {L=12, M=4}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-4, O=12+N, N=4*K, K=12+4}
4. alphabetMap = {L=12, M=4, K=16}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=1, O=1, N=1, K=2}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-4, O=12+N, N=4*16, K=12+4}
4. alphabetMap = {L=12, M=4, K=16, N=64}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=1, O=1, N=2, K=2}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-4, O=12+64, N=4*16, K=12+4}
4. alphabetMap = {L=12, M=4, K=16, N=64, O=76}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=1, O=2, N=2, K=2}

6. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=76-4, O=12+64, N=4*16, K=12+4}
7. alphabetMap = {L=12, M=4, K=16, N=64, O=76}
8. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=2, O=2, N=2, K=2}

L^M 의 계산 결과 = 72

2. alphabetMap = {A=16, B=6}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {A=16, B=6}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

3. replaceMapCopy = {C=16+6, A$B=22*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {A=16, B=6, C=22}
5. replaceCount = {C=2, A$B=1, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

6. replaceMapCopy = {C=16+6, A$B=22*22, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
7. alphabetMap = {A=16, B=6, C=22}
8. replaceCount = {C=2, A$B=2, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

A$B 의 계산 결과 = 484

2. alphabetMap = {E=16, F=6}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {E=16, F=6}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=16-6, E&F=10*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {E=16, F=6, D=10}
5. replaceCount = {C=0, A$B=0, D=2, E&F=1, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

6. replaceMapCopy = {C=A+B, A$B=C*C, D=16-6, E&F=10*10, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
7. alphabetMap = {E=16, F=6, D=10}
8. replaceCount = {C=0, A$B=0, D=2, E&F=2, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

E&F 의 계산 결과 = 100

2. alphabetMap = {H=16, I=6}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {H=16, I=6}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

6. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=16+6, J=16-6, H|I=22*10, L^M=O-M, O=L+N, N=M*K, K=L+M}
7. alphabetMap = {H=16, I=6, G=22, J=10}
8. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=2, J=2, H|I=2, L^M=0, O=0, N=0, K=0}

H|I 의 계산 결과 = 220

2. alphabetMap = {L=16, M=6}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-M, O=L+N, N=M*K, K=L+M}
4. alphabetMap = {L=16, M=6}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=0, O=0, N=0, K=0}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-6, O=16+N, N=6*K, K=16+6}
4. alphabetMap = {L=16, M=6, K=22}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=1, O=1, N=1, K=2}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-6, O=16+N, N=6*22, K=16+6}
4. alphabetMap = {L=16, M=6, K=22, N=132}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=1, O=1, N=2, K=2}

3. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=O-6, O=16+132, N=6*22, K=16+6}
4. alphabetMap = {L=16, M=6, K=22, N=132, O=148}
5. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=1, O=2, N=2, K=2}

6. replaceMapCopy = {C=A+B, A$B=C*C, D=E-F, E&F=D*D, G=H+I, J=H-I, H|I=G*J, L^M=148-6, O=16+132, N=6*22, K=16+6}
7. alphabetMap = {L=16, M=6, K=22, N=132, O=148}
8. replaceCount = {C=0, A$B=0, D=0, E&F=0, G=0, J=0, H|I=0, L^M=2, O=2, N=2, K=2}

L^M 의 계산 결과 = 142
```
