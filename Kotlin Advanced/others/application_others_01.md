## 요구사항 분석
```(A)(B)(C)``` 형식의 텍스트를 ```(D)(E)(F)``` 형식으로 변환하시오.
* ```(A)```는 맨 앞의 숫자 부분으로, 소수이면 바로 다음의 합성수로 바꾸고, 0, 1 또는 합성수이면 그대로 둔다.
* ```(B)```는 중간의 알파벳으로, 그 부분의 각 n번째 글자를 알파벳 순서상으로 n만큼 뒤에 있는 글자로 바꾼다.
* ```(C)```는 뒤쪽의 숫자 부분으로, 그 부분의 첫 번째 숫자의 값만큼 앞에 '/'를 붙이고, 그 숫자 자체에는 2를 곱한다.

위와 같은 방법으로 ```(A)```, ```(B)```, ```(C)```를 각각 ```(D)```, ```(E)```, ```(F)```로 변환한다.

## 코드
```kotlin
import kotlin.math.*
import java.util.Random

fun isPrime(n: Int): Boolean {
    if (n < 1) {
        return false
    }
    
    val sqroot = sqrt(n.toDouble()).toInt()
    for (i in 2..sqroot) {
        if (n % i == 0) {
            return false
        }
    }
    return true
}

fun convertString(original: String): String {
    
    // 알파벳 부분을 공백으로 교체한 후 split을 이용하여 각 부분의 길이 구하기
    val replaceTest = original.replace(Regex("[a-z]+"), " ")

    val lengthA = replaceTest.indexOf(" ")
    val lengthC = replaceTest.length - (lengthA + 1)
    val lengthB = original.length - (replaceTest.length - 1)
    
    val A = original.substring(0, lengthA)
    val B = original.substring(lengthA, lengthA + lengthB)
    val C = original.substring(lengthA + lengthB)
    
    println("\n1. lengthA = $lengthA, lengthB = $lengthB, lengthC = $lengthC")
    println("2. A = $A, B = $B, C = $C")
    
    // A -> D
    val D = when (A) {
        "0" -> 0
        "1" -> 1
        "2" -> 4
        else -> if (isPrime(A.toInt())) { A.toInt() + 1 } else { A.toInt() }
    }
    
    // B -> E
    val charToAnotherChar: ((Char, Int) -> Char) = { c, idx ->
        val nextMod = ((c.code - 'a'.code) + idx + 1) % 26
        'a' + nextMod
    }
    val E = B.mapIndexed { idx, it ->
        charToAnotherChar(it, idx)
    }.joinToString("")
    
    // C -> F
    val F = "${"/".repeat((C[0] - 48).toInt())}${C.toInt() * 2}"
    
    println("3. D = $D, E = $E, F = $F")
    
    return "${D}${E}${F}"
}

fun main() {
    val random = Random()
    
    for (i in 0..99) {
        val A = random.nextInt(200)
        val B = listOf("abcd", "eee", "aaa", "cba", "dd")[random.nextInt(5)]
        val C = random.nextInt(70)
        
        val originalString = "${A}${B}${C}"
        val convertedString = convertString(originalString)
        
        println("$originalString -> $convertedString")
    }
}
```

## 실행 결과
```kotlin

1. lengthA = 1, lengthB = 2, lengthC = 1
2. A = 2, B = dd, C = 5
3. D = 4, E = ef, F = /////10
2dd5 -> 4ef/////10

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 172, B = aaa, C = 2
3. D = 172, E = bcd, F = //4
172aaa2 -> 172bcd//4

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 57, B = aaa, C = 56
3. D = 57, E = bcd, F = /////112
57aaa56 -> 57bcd/////112

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 93, B = dd, C = 57
3. D = 93, E = ef, F = /////114
93dd57 -> 93ef/////114

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 96, B = dd, C = 41
3. D = 96, E = ef, F = ////82
96dd41 -> 96ef////82

1. lengthA = 1, lengthB = 3, lengthC = 2
2. A = 5, B = eee, C = 30
3. D = 6, E = fgh, F = ///60
5eee30 -> 6fgh///60

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 18, B = aaa, C = 17
3. D = 18, E = bcd, F = /34
18aaa17 -> 18bcd/34

1. lengthA = 2, lengthB = 4, lengthC = 2
2. A = 60, B = abcd, C = 62
3. D = 60, E = bdfh, F = //////124
60abcd62 -> 60bdfh//////124

1. lengthA = 3, lengthB = 4, lengthC = 1
2. A = 192, B = abcd, C = 0
3. D = 192, E = bdfh, F = 0
192abcd0 -> 192bdfh0

1. lengthA = 1, lengthB = 2, lengthC = 2
2. A = 5, B = dd, C = 27
3. D = 6, E = ef, F = //54
5dd27 -> 6ef//54

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 172, B = dd, C = 57
3. D = 172, E = ef, F = /////114
172dd57 -> 172ef/////114

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 131, B = aaa, C = 0
3. D = 132, E = bcd, F = 0
131aaa0 -> 132bcd0

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 113, B = cba, C = 5
3. D = 114, E = ddd, F = /////10
113cba5 -> 114ddd/////10

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 96, B = aaa, C = 50
3. D = 96, E = bcd, F = /////100
96aaa50 -> 96bcd/////100

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 199, B = aaa, C = 54
3. D = 200, E = bcd, F = /////108
199aaa54 -> 200bcd/////108

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 100, B = eee, C = 8
3. D = 100, E = fgh, F = ////////16
100eee8 -> 100fgh////////16

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 126, B = aaa, C = 61
3. D = 126, E = bcd, F = //////122
126aaa61 -> 126bcd//////122

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 71, B = cba, C = 52
3. D = 72, E = ddd, F = /////104
71cba52 -> 72ddd/////104

1. lengthA = 2, lengthB = 4, lengthC = 2
2. A = 88, B = abcd, C = 21
3. D = 88, E = bdfh, F = //42
88abcd21 -> 88bdfh//42

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 83, B = dd, C = 17
3. D = 84, E = ef, F = /34
83dd17 -> 84ef/34

1. lengthA = 2, lengthB = 4, lengthC = 2
2. A = 22, B = abcd, C = 28
3. D = 22, E = bdfh, F = //56
22abcd28 -> 22bdfh//56

1. lengthA = 1, lengthB = 3, lengthC = 2
2. A = 1, B = eee, C = 23
3. D = 1, E = fgh, F = //46
1eee23 -> 1fgh//46

1. lengthA = 3, lengthB = 4, lengthC = 2
2. A = 158, B = abcd, C = 14
3. D = 158, E = bdfh, F = /28
158abcd14 -> 158bdfh/28

1. lengthA = 2, lengthB = 4, lengthC = 2
2. A = 86, B = abcd, C = 46
3. D = 86, E = bdfh, F = ////92
86abcd46 -> 86bdfh////92

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 34, B = cba, C = 62
3. D = 34, E = ddd, F = //////124
34cba62 -> 34ddd//////124

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 168, B = eee, C = 42
3. D = 168, E = fgh, F = ////84
168eee42 -> 168fgh////84

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 24, B = aaa, C = 27
3. D = 24, E = bcd, F = //54
24aaa27 -> 24bcd//54

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 105, B = dd, C = 15
3. D = 105, E = ef, F = /30
105dd15 -> 105ef/30

1. lengthA = 1, lengthB = 3, lengthC = 2
2. A = 1, B = cba, C = 65
3. D = 1, E = ddd, F = //////130
1cba65 -> 1ddd//////130

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 114, B = dd, C = 43
3. D = 114, E = ef, F = ////86
114dd43 -> 114ef////86

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 58, B = eee, C = 62
3. D = 58, E = fgh, F = //////124
58eee62 -> 58fgh//////124

1. lengthA = 2, lengthB = 4, lengthC = 2
2. A = 36, B = abcd, C = 42
3. D = 36, E = bdfh, F = ////84
36abcd42 -> 36bdfh////84

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 39, B = aaa, C = 62
3. D = 39, E = bcd, F = //////124
39aaa62 -> 39bcd//////124

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 27, B = eee, C = 34
3. D = 27, E = fgh, F = ///68
27eee34 -> 27fgh///68

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 30, B = eee, C = 57
3. D = 30, E = fgh, F = /////114
30eee57 -> 30fgh/////114

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 147, B = dd, C = 10
3. D = 147, E = ef, F = /20
147dd10 -> 147ef/20

1. lengthA = 3, lengthB = 4, lengthC = 2
2. A = 108, B = abcd, C = 39
3. D = 108, E = bdfh, F = ///78
108abcd39 -> 108bdfh///78

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 116, B = eee, C = 67
3. D = 116, E = fgh, F = //////134
116eee67 -> 116fgh//////134

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 88, B = cba, C = 55
3. D = 88, E = ddd, F = /////110
88cba55 -> 88ddd/////110

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 65, B = aaa, C = 52
3. D = 65, E = bcd, F = /////104
65aaa52 -> 65bcd/////104

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 56, B = eee, C = 47
3. D = 56, E = fgh, F = ////94
56eee47 -> 56fgh////94

1. lengthA = 2, lengthB = 3, lengthC = 1
2. A = 34, B = eee, C = 6
3. D = 34, E = fgh, F = //////12
34eee6 -> 34fgh//////12

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 35, B = eee, C = 61
3. D = 35, E = fgh, F = //////122
35eee61 -> 35fgh//////122

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 137, B = aaa, C = 0
3. D = 138, E = bcd, F = 0
137aaa0 -> 138bcd0

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 92, B = aaa, C = 14
3. D = 92, E = bcd, F = /28
92aaa14 -> 92bcd/28

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 189, B = dd, C = 24
3. D = 189, E = ef, F = //48
189dd24 -> 189ef//48

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 144, B = eee, C = 24
3. D = 144, E = fgh, F = //48
144eee24 -> 144fgh//48

1. lengthA = 3, lengthB = 4, lengthC = 2
2. A = 186, B = abcd, C = 45
3. D = 186, E = bdfh, F = ////90
186abcd45 -> 186bdfh////90

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 76, B = aaa, C = 23
3. D = 76, E = bcd, F = //46
76aaa23 -> 76bcd//46

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 43, B = cba, C = 18
3. D = 44, E = ddd, F = /36
43cba18 -> 44ddd/36

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 173, B = dd, C = 34
3. D = 174, E = ef, F = ///68
173dd34 -> 174ef///68

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 118, B = dd, C = 42
3. D = 118, E = ef, F = ////84
118dd42 -> 118ef////84

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 80, B = eee, C = 26
3. D = 80, E = fgh, F = //52
80eee26 -> 80fgh//52

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 192, B = aaa, C = 6
3. D = 192, E = bcd, F = //////12
192aaa6 -> 192bcd//////12

1. lengthA = 3, lengthB = 2, lengthC = 1
2. A = 138, B = dd, C = 7
3. D = 138, E = ef, F = ///////14
138dd7 -> 138ef///////14

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 22, B = eee, C = 48
3. D = 22, E = fgh, F = ////96
22eee48 -> 22fgh////96

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 33, B = aaa, C = 42
3. D = 33, E = bcd, F = ////84
33aaa42 -> 33bcd////84

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 21, B = eee, C = 46
3. D = 21, E = fgh, F = ////92
21eee46 -> 21fgh////92

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 127, B = eee, C = 2
3. D = 128, E = fgh, F = //4
127eee2 -> 128fgh//4

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 131, B = cba, C = 19
3. D = 132, E = ddd, F = /38
131cba19 -> 132ddd/38

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 144, B = dd, C = 19
3. D = 144, E = ef, F = /38
144dd19 -> 144ef/38

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 99, B = eee, C = 20
3. D = 99, E = fgh, F = //40
99eee20 -> 99fgh//40

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 34, B = aaa, C = 56
3. D = 34, E = bcd, F = /////112
34aaa56 -> 34bcd/////112

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 13, B = dd, C = 60
3. D = 14, E = ef, F = //////120
13dd60 -> 14ef//////120

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 73, B = dd, C = 50
3. D = 74, E = ef, F = /////100
73dd50 -> 74ef/////100

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 43, B = eee, C = 14
3. D = 44, E = fgh, F = /28
43eee14 -> 44fgh/28

1. lengthA = 1, lengthB = 3, lengthC = 2
2. A = 5, B = aaa, C = 21
3. D = 6, E = bcd, F = //42
5aaa21 -> 6bcd//42

1. lengthA = 3, lengthB = 3, lengthC = 1
2. A = 169, B = aaa, C = 8
3. D = 169, E = bcd, F = ////////16
169aaa8 -> 169bcd////////16

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 71, B = dd, C = 14
3. D = 72, E = ef, F = /28
71dd14 -> 72ef/28

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 67, B = dd, C = 20
3. D = 68, E = ef, F = //40
67dd20 -> 68ef//40

1. lengthA = 3, lengthB = 2, lengthC = 1
2. A = 141, B = dd, C = 6
3. D = 141, E = ef, F = //////12
141dd6 -> 141ef//////12

1. lengthA = 2, lengthB = 4, lengthC = 2
2. A = 26, B = abcd, C = 68
3. D = 26, E = bdfh, F = //////136
26abcd68 -> 26bdfh//////136

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 185, B = eee, C = 68
3. D = 185, E = fgh, F = //////136
185eee68 -> 185fgh//////136

1. lengthA = 1, lengthB = 3, lengthC = 2
2. A = 6, B = aaa, C = 16
3. D = 6, E = bcd, F = /32
6aaa16 -> 6bcd/32

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 198, B = aaa, C = 55
3. D = 198, E = bcd, F = /////110
198aaa55 -> 198bcd/////110

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 175, B = dd, C = 12
3. D = 175, E = ef, F = /24
175dd12 -> 175ef/24

1. lengthA = 3, lengthB = 4, lengthC = 2
2. A = 121, B = abcd, C = 58
3. D = 121, E = bdfh, F = /////116
121abcd58 -> 121bdfh/////116

1. lengthA = 1, lengthB = 2, lengthC = 1
2. A = 1, B = dd, C = 0
3. D = 1, E = ef, F = 0
1dd0 -> 1ef0

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 66, B = aaa, C = 42
3. D = 66, E = bcd, F = ////84
66aaa42 -> 66bcd////84

1. lengthA = 2, lengthB = 4, lengthC = 2
2. A = 19, B = abcd, C = 25
3. D = 20, E = bdfh, F = //50
19abcd25 -> 20bdfh//50

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 197, B = dd, C = 25
3. D = 198, E = ef, F = //50
197dd25 -> 198ef//50

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 22, B = dd, C = 39
3. D = 22, E = ef, F = ///78
22dd39 -> 22ef///78

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 105, B = cba, C = 63
3. D = 105, E = ddd, F = //////126
105cba63 -> 105ddd//////126

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 190, B = dd, C = 65
3. D = 190, E = ef, F = //////130
190dd65 -> 190ef//////130

1. lengthA = 3, lengthB = 4, lengthC = 2
2. A = 134, B = abcd, C = 68
3. D = 134, E = bdfh, F = //////136
134abcd68 -> 134bdfh//////136

1. lengthA = 1, lengthB = 4, lengthC = 2
2. A = 7, B = abcd, C = 41
3. D = 8, E = bdfh, F = ////82
7abcd41 -> 8bdfh////82

1. lengthA = 3, lengthB = 2, lengthC = 2
2. A = 139, B = dd, C = 37
3. D = 140, E = ef, F = ///74
139dd37 -> 140ef///74

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 15, B = eee, C = 21
3. D = 15, E = fgh, F = //42
15eee21 -> 15fgh//42

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 78, B = cba, C = 39
3. D = 78, E = ddd, F = ///78
78cba39 -> 78ddd///78

1. lengthA = 3, lengthB = 4, lengthC = 2
2. A = 177, B = abcd, C = 30
3. D = 177, E = bdfh, F = ///60
177abcd30 -> 177bdfh///60

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 177, B = eee, C = 50
3. D = 177, E = fgh, F = /////100
177eee50 -> 177fgh/////100

1. lengthA = 3, lengthB = 4, lengthC = 2
2. A = 187, B = abcd, C = 33
3. D = 187, E = bdfh, F = ///66
187abcd33 -> 187bdfh///66

1. lengthA = 1, lengthB = 3, lengthC = 2
2. A = 7, B = eee, C = 19
3. D = 8, E = fgh, F = /38
7eee19 -> 8fgh/38

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 129, B = eee, C = 16
3. D = 129, E = fgh, F = /32
129eee16 -> 129fgh/32

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 154, B = aaa, C = 51
3. D = 154, E = bcd, F = /////102
154aaa51 -> 154bcd/////102

1. lengthA = 3, lengthB = 3, lengthC = 2
2. A = 125, B = aaa, C = 18
3. D = 125, E = bcd, F = /36
125aaa18 -> 125bcd/36

1. lengthA = 2, lengthB = 3, lengthC = 2
2. A = 61, B = aaa, C = 16
3. D = 62, E = bcd, F = /32
61aaa16 -> 62bcd/32

1. lengthA = 2, lengthB = 3, lengthC = 1
2. A = 64, B = eee, C = 2
3. D = 64, E = fgh, F = //4
64eee2 -> 64fgh//4

1. lengthA = 2, lengthB = 3, lengthC = 1
2. A = 58, B = cba, C = 7
3. D = 58, E = ddd, F = ///////14
58cba7 -> 58ddd///////14

1. lengthA = 2, lengthB = 2, lengthC = 2
2. A = 51, B = dd, C = 52
3. D = 51, E = ef, F = /////104
51dd52 -> 51ef/////104
```
