## 요구사항 분석
다음 **데이터 클래스**의 형식을 따르는 각 항목들을 다음 우선순위에 따라 정렬하여 출력하시오.
* ```arg1```의 길이를 5로 나눈 나머지가 큰 순으로 정렬
* ```arg1```을 ```compareTo``` 함수로 비교했을 때 큰 순으로 정렬
* ```(arg3 - 1.25)```의 제곱이 작은 순으로 정렬
* ```arg2```의 값이 홀수인 것 -> ```arg2```의 값이 짝수인 것 순으로
* ```arg2```의 값이 큰 순으로 정렬
* ```arg4```의 원소의 개수가 큰 순으로 정렬
* ```arg4```의 원소의 길이의 합이 큰 순으로 정렬
* ```arg4```의 인덱스 0의 원소를 숫자로 나눈 각 부분(예: 12ab3c45 -> 12, 3, 45)의 개수가 많은 순으로 정렬
* ```arg4```의 인덱스 1의 원소 (없을 시 인덱스 0의 원소) 를 숫자로 나눈 각 부분의 합이 큰 순으로 정렬
* ```arg4```의 인덱스 2의 원소 (없을 시 인덱스가 가장 큰 원소) 를 숫자로 나눈 각 부분의 곱이 큰 순으로 정렬

### 데이터 클래스
```kotlin
// 데이터 클래스
data class TestDataClass (
    val arg1: String,
    val arg2: Int,
    val arg3: Double,
    val arg4: List<String>
)
```

## 코드 및 실행 결과
### 코드
```kotlin
import java.util.Random

// 데이터 클래스
data class TestDataClass (
    val arg1: String,
    val arg2: Int,
    val arg3: Double,
    val arg4: List<String>
)

// 원소를 숫자로 나눈 각 부분 구하기
fun findItemNumList(item: String): List<Int> {
    val re = Regex("[^0-9 ]+")
    val itemNum = re.replace(item, " ")
    val itemNumList = itemNum.trim().split(" ")

    // 숫자가 없는 경우 예외 처리
    if (itemNumList[0].isEmpty()) {
        return listOf()
    }
    
    return itemNumList.map {
        it.toInt()
    }
}

// 데이터 클래스 정렬하여 표시
fun sortAndThenDisplay(testDataClasses: List<TestDataClass>) {
    
    // 목록 정렬
    val sortedTestDataClasses = testDataClasses.sortedWith (
        compareBy (
            
            // arg1의 길이를 5로 나눈 나머지가 큰 순으로 정렬
            { -it.arg1.length % 5 },
            
            // arg1을 compareTo 함수로 비교했을 때 큰 순으로 정렬
            // 참고: https://stackoverflow.com/questions/47303355/comparator-as-lambda
            { it.arg1 },
            
            // arg2, arg3의 값에 따라 정렬
            { (it.arg3 - 1.25) * (it.arg3 - 1.25) },
            { -(it.arg2 % 2) },
            { -it.arg2 },
            
            // arg4의 원소의 개수가 큰 순으로 정렬
            { -(it.arg4.size) },
            
            // arg4의 원소의 길이의 합이 큰 순으로 정렬
            { 
                -(it.arg4.sumOf {
                    it.length
                })
            },
            
            // arg4의 인덱스 0의 원소를 숫자로 나눈 각 부분의 개수가 많은 순으로 정렬
            {
                val firstItemNumList = findItemNumList(it.arg4[0])
                -firstItemNumList.size
            },
            
            // arg4의 인덱스 1의 원소 (없을 시 인덱스 0의 원소) 를 숫자로 나눈 각 부분의 합이 큰 순으로 정렬
            {
                val secondItemNumList = if (it.arg4.size >= 2) {
                    findItemNumList(it.arg4[1])
                } else {
                    findItemNumList(it.arg4[0])
                }
                
                -secondItemNumList.sum()
            },
            
            // arg4의 인덱스 2의 원소 (없을 시 인덱스가 가장 큰 원소) 를 숫자로 나눈 각 부분의 곱이 큰 순으로 정렬
            {
                val thirdItemNumList = if (it.arg4.size >= 3) {
                    findItemNumList(it.arg4[2])
                } else {
                    findItemNumList(it.arg4.last())
                }
                
                val productOfNumList = thirdItemNumList.fold (1) { acc, e ->
                    acc * e
                }
                println("${it.arg4}, ${thirdItemNumList}, ${productOfNumList}")
                
                -productOfNumList
            }
        )
    )
    print("\n")
    
    // 정렬된 목록을 출력
    sortedTestDataClasses.forEach {
        println("args : ${it.arg1}, ${it.arg2}, ${it.arg3}, ${it.arg4}")
    }
}

fun main() {
    
    // 임시 데이터 클래스 (랜덤으로 항목 1000개 생성)
    val testDataClasses = mutableListOf<TestDataClass>()
    
    (0..499).forEach {
        val num1 = Random().nextInt(7) + 2
        val arg1 = (0 until num1).fold ("") { acc, e ->
            acc + ('a'.code + Random().nextInt(2)).toChar().toString()
        }
        
        val arg2 = Random().nextInt(5) + 5
        val arg3 = (Random().nextInt(4) + 4) / 4.0
        
        val num2 = Random().nextInt(5) + 1
        val arg4 = (0 until num2).map {
            val num3 = Random().nextInt(7) + 2
            val str = (0 until num3).fold ("") { acc, e ->
                val x = Random().nextInt(8)
                if (x >= 4) {
                    acc + ('a'.code + x - 4).toChar().toString()
                } else {
                    acc + (x + 1).toString()
                }
            }
            str
        }
        
        testDataClasses.add(TestDataClass(arg1, arg2, arg3, arg4))
        testDataClasses.add(TestDataClass("aba", 8, 2.0, arg4))
    }
    
    // 데이터 클래스 정렬하여 표시하기
    sortAndThenDisplay(testDataClasses)
}
```

### 실행 결과
```kotlin
[ba], [], 1
[ac], [], 1
[1c], [1], 1
[d1], [1], 1
[c4, 2cb, 42a3243, 32dba4b4, 3c1], [42, 3243], 136206
[4411, 1a1a, c21ad, cc4, 1dc4bbd], [21], 21
[c2d], [2], 2
[2cc], [2], 2
[22aaa2], [22, 2], 44
[22cd2a], [22, 2], 44
[3a2, 3bd], [3], 3
[b1b2, 3d], [3], 3
[bacd4c, dc2d], [2], 2
[a1c, d2cbcdd], [2], 2
[2244, 13c1abd, ba], [], 1
[cc4a, 14, ccb4ccc], [4], 4
[4bb], [4], 4
[4cc], [4], 4
[1a], [1], 1
[d1], [1], 1
[1a], [1], 1
[1c], [1], 1
[bd], [], 1
[ac], [], 1
[bd], [], 1
[ba], [], 1
[cd1ada3, c2d], [2], 2
[a1ddcb1b, b2], [2], 2
[bd], [], 1
[ac], [], 1
[d1], [1], 1
[1a], [1], 1
[da22b], [22], 22
[22dba], [22], 22
[cb2], [2], 2
[dd2], [2], 2
[3a], [3], 3
[d3], [3], 3
[3cc3], [3, 3], 9
[a3c3], [3, 3], 9
[acabada, 14], [14], 14
[bbd, b1c13b], [1, 13], 13
[3a], [3], 3
[a3], [3], 3
[d3], [3], 3
[a3], [3], 3
[d3d], [3], 3
[da3], [3], 3
[21ca, ad2c], [2], 2
[a14, b2dcb], [2], 2
[bd], [], 1
[bd], [], 1
[ac], [], 1
[bd], [], 1
[d1], [1], 1
[1a], [1], 1
[1a], [1], 1
[1a], [1], 1
[3a], [3], 3
[3d], [3], 3
[d3], [3], 3
[3d], [3], 3
[a3], [3], 3
[3d], [3], 3
[41], [41], 41
[41], [41], 41
[cb2], [2], 2
[c2d], [2], 2
[dd2], [2], 2
[c2d], [2], 2
[ac4], [4], 4
[4bb], [4], 4
[acaa], [], 1
[daaa], [], 1
[3ac2], [3, 2], 6
[4d1d], [4, 1], 4
[adbaa], [], 1
[bccbb], [], 1
[c1aa1b], [1, 1], 1
[ca1c1a], [1, 1], 1
[34bdbba], [34], 34
[d34abcd], [34], 34
[32cdd14], [32, 14], 448
[42ccb4d], [42, 4], 168
[33cbb2b3], [33, 2, 3], 198
[c33cd4b1], [33, 4, 1], 132
[dd4d, da1bbc, da3], [3], 3
[1a, ba1b, bbda2d3], [2, 3], 6
[4a22, 4bd, cc41aa24], [41, 24], 984
[1c431dba, dd4, 3d12], [3, 12], 36
[ca42, d4, 444, 1a1c1cac], [444], 444
[43c, 4ab, bb3d, 1acc43b], [3], 3
[ac3a2, 1a, bd3, 34bc2a, d244], [3], 3
[2ac4a, 1bc, ab3b3c1, dc, bbc], [3, 3, 1], 9

args : aaaa, 7, 1.25, [3a132, 12cc1ab1, 1c3b3dba, 3d21, bbd12ad]
args : aaaa, 9, 1.0, [d31, 3dbaa2, a34aab2d]
args : aaaa, 7, 1.0, [2ad4b1b, 21c1b2a, ca2]
args : aaaa, 5, 1.0, [24b3b, 2ccb141]
args : aaaa, 5, 1.75, [43c, 4ab, bb3d, 1acc43b]
args : aaaa, 6, 1.75, [bd]
args : aaab, 8, 1.25, [c1dd2, 2a, 32da, 11bca221]
args : aaab, 9, 1.5, [1411, 1314, 3432a11d, ab2c, a1]
args : aaab, 9, 1.0, [ad, 13344a1, 23d, dba4, 3b4caa]
args : aaab, 9, 1.0, [c2a4d324, b4b1]
args : aaab, 5, 1.0, [bca4ad44, d432, cbbaddbb, bd33b4c]
args : aaab, 5, 1.0, [22cdc43, b1c22c, ad1112, 34]
args : aaab, 8, 1.75, [b33ddc1, 42c, 1a41, 32, da2a3]
args : aaba, 7, 1.0, [4a3b2b24, 13a1c, 1dba]
args : aaba, 6, 1.75, [d1c1b4, 1423ac3b, bb]
args : aabb, 9, 1.0, [3d]
args : aabb, 5, 1.75, [dc3b, 43, d4b112a]
args : abaa, 6, 1.25, [cbda44]
args : abaa, 5, 1.0, [ac4]
args : abaa, 8, 1.0, [daa4, 4caa221d, a1443dd1, a2]
args : abaa, 8, 1.5, [3adc]
args : abaa, 6, 1.5, [ba34d2, b1b, 3cca, 33]
args : abaa, 6, 1.5, [324d4ad, 424b24]
args : abab, 9, 1.25, [d3, 4ccdbaa, a22, bdba]
args : abab, 8, 1.25, [dbbacb, 2b22cb1, 3ac, 1ab2ccdc, da]
args : abab, 8, 1.25, [a14142c]
args : abab, 9, 1.5, [21a1, 14, 23da1]
args : abab, 7, 1.0, [b33b4]
args : abab, 7, 1.5, [4a1]
args : abba, 9, 1.0, [aa, 4ba2b]
args : abba, 7, 1.5, [acabada, 14]
args : abba, 5, 1.5, [c144244, 3c1ad1c, 4dacb4]
args : abba, 8, 1.0, [ca1414d, cac, b3dca]
args : abba, 6, 1.0, [dd124b14, 2da, 22b143]
args : abba, 8, 1.75, [ba]
args : abbb, 9, 1.5, [3d3, b33]
args : abbb, 8, 1.0, [3d1c4]
args : abbb, 6, 1.5, [2bc3c, 1a, 133144d2, 224a]
args : baaa, 9, 1.25, [14, a2114]
args : baaa, 6, 1.25, [3ca3, 43d, ccca]
args : baaa, 6, 1.25, [dc42cd3c]
args : baaa, 5, 1.0, [aaa2a, 1aa34, b3cdbc, b1, 124]
args : baaa, 5, 1.5, [c33cd4b1]
args : baaa, 9, 1.75, [d1]
args : baaa, 7, 1.75, [a241a2b]
args : baab, 7, 1.25, [32caac3, c1c, 33ac, 3db2da41]
args : baab, 7, 1.25, [22b4243, 1d22ddb]
args : baab, 5, 1.25, [42ccb4d]
args : baab, 8, 1.25, [2ab2d]
args : baab, 5, 1.0, [3a1ab, ddd2, cbc4, 4c3c1]
args : baba, 7, 1.25, [abc3adbd, 1bcc3, cd1a3ad, bccdbc]
args : baba, 5, 1.25, [ad44b33, 4c1c]
args : baba, 5, 1.0, [a41]
args : baba, 7, 1.75, [d3c, ca44a, d2ab]
args : babb, 5, 1.25, [23d3, b14cd, 1da221, d2, 4c22]
args : babb, 8, 1.0, [43abb1b, ad2abb, ccaa, b22a4a1d, abc44a]
args : babb, 9, 1.75, [a2422, 2c42d4b]
args : babb, 5, 1.75, [ac]
args : bbab, 5, 1.0, [33, 1d3, c24d2a]
args : bbab, 8, 1.0, [2ac4a, 1bc, ab3b3c1, dc, bbc]
args : bbba, 7, 1.0, [3b3ad, dc1d4c2, 2b2bcc, b41cc1, 41b4323a]
args : bbba, 6, 1.5, [bab32, 2dadd, 3c11c23b]
args : bbba, 9, 1.75, [3ab341a1, c224b2a]
args : bbba, 6, 1.75, [21abd2, dcd12, 11cbacb, 11bbba2]
args : bbbb, 9, 1.5, [dc4, 4bb, 2d43, d1]
args : bbbb, 5, 1.0, [dd4d, da1bbc, da3]
args : bbbb, 8, 1.5, [3ca1b, cc2, 43a3bcaa]
args : bbbb, 6, 1.5, [23a, c1dc4ba3, ac4cd414, c3112, 4aad2da]
args : bbbb, 7, 1.75, [44312444, c3441d, c3, c4d3, 4a24b14]
args : aaa, 9, 1.25, [d4aa3aa, 2cd23bc3, 33, ad23322]
args : aaa, 8, 1.25, [d23]
args : aaa, 9, 1.0, [d4c32334, a21b312, 21bb, d4144c, ab4c4]
args : aaa, 9, 1.0, [2411d, aabbbd, 112223b, b21]
args : aaa, 9, 1.0, [bc34d, dda21dd, b3db2a, ba]
args : aaa, 9, 1.5, [43b, aa31bd]
args : aaa, 9, 1.0, [33cbb2b3]
args : aaa, 7, 1.5, [3addbd, 4a3db, abcb2, 12ddb4c3]
args : aaa, 5, 1.0, [33c4, a24b3, cc3c43]
args : aaa, 6, 1.0, [3cc3, 13d, cc4b1, cdd424, 3d432baa]
args : aaa, 9, 1.75, [1d4, ccbadc1, ddc3d4cd, 3a]
args : aaa, 7, 1.75, [141, aa13]
args : aaaaaaba, 7, 1.0, [4ca, 4db3b, d232b3]
args : aaaaaaba, 8, 1.75, [cddbb4, ad1b113d, 41, 3b1ba]
args : aaaaaabb, 6, 1.5, [adbb11, daa334, 13a2c]
args : aaaaabab, 8, 1.5, [cb, c32ca32b, 2ac2]
args : aaaabaab, 8, 1.75, [c4d, 14a2add3, b21c4, b23d4, ab]
args : aaabaaaa, 9, 1.5, [a3, cc3c41b1, bcd32133]
args : aaabaaab, 5, 1.5, [a313ba, dc2, 1c21, 1dd3d]
args : aaababaa, 8, 1.75, [434ab]
args : aaababab, 9, 1.75, [a13a33]
args : aab, 5, 1.25, [c3bdddd4]
args : aab, 9, 1.0, [c2ba33, c41c]
args : aab, 7, 1.5, [ac3a2, 1a, bd3, 34bc2a, d244]
args : aab, 6, 1.0, [14d, 1313bb]
args : aab, 9, 1.75, [1da2cc2, aa4cbda, db, c2311, 4d3b]
args : aab, 7, 1.75, [1cc4c2a, 21b4a, cb3dbabd, d1abd3d]
args : aab, 6, 1.75, [4d3dd12, c3cd, b311ddb, d13b242, aa4c]
args : aabaaaab, 9, 1.5, [32a4d31a, 42dad, b411, dbc234c, 34d34114]
args : aabaabbb, 5, 1.25, [12434c34, 4dcd342]
args : aabbaaab, 9, 1.75, [dd2b11c, 33b, b3d13b4, a33aa, ba]
args : aabbaaba, 6, 1.25, [bca1dcc]
args : aabbaabb, 6, 1.5, [ca42, d4, 444, 1a1c1cac]
args : aabbabaa, 5, 1.25, [2a32cc3]
args : aabbabaa, 8, 1.75, [add2d3, 4cda12d, a313223, cabc, d4]
args : aabbabab, 5, 1.0, [3b, a32c4]
args : aba, 7, 1.25, [da344, 342, 43b32, d13d]
args : aba, 7, 1.25, [da1]
args : aba, 8, 1.25, [4d3d, 4111dd1d, b43ba2b]
args : aba, 6, 1.25, [b4, 12, 4a3, 323d1423, 2a344dc3]
args : aba, 9, 1.5, [b1a2cd1, 21b1, ccda4]
args : aba, 9, 1.5, [adb, cba]
args : aba, 7, 1.5, [cb1, 33132bc, 2b2, dc]
args : aba, 5, 1.5, [43b31]
args : aba, 8, 1.0, [a2, 4433c123, b43cc2d]
args : aba, 9, 1.75, [d4, 13, cc, bd2b2ab]
args : aba, 9, 1.75, [baab13c, 4ad1, bc2d4]
args : aba, 8, 2.0, [a1a243cc, 4241b3, d2b, 1d12cd14, 2bd44bdd]
args : aba, 8, 2.0, [ac1cb4, 2b3d, a1b21a32, 2431334, b4a1132a]
args : aba, 8, 2.0, [2aaa1b1c, bbb1124, b4d3ca, 13a1, 4d24acb]
args : aba, 8, 2.0, [32a4d31a, 42dad, b411, dbc234c, 34d34114]
args : aba, 8, 2.0, [3a132, 12cc1ab1, 1c3b3dba, 3d21, bbd12ad]
args : aba, 8, 2.0, [3b3ad, dc1d4c2, 2b2bcc, b41cc1, 41b4323a]
args : aba, 8, 2.0, [a33c2aa2, 42111244, 1d1c1a3, d4d, 3dc1c]
args : aba, 8, 2.0, [a4d24d4c, b3b24, a1d4, ab24bcd, cbcaddb]
args : aba, 8, 2.0, [4d121b14, a1c2c2, 41b2bcb2, cd4, 41d422]
args : aba, 8, 2.0, [43b4bc1, 1c2bbc, 1bb2ddbc, 44cc1b23, dc]
args : aba, 8, 2.0, [33bc4c, 42321dc, aacb13b2, a4db2cb4, 1a]
args : aba, 8, 2.0, [c23bd414, 133ad44, bcbaa3a, bb4432, 1b2]
args : aba, 8, 2.0, [dbd4b3, 4badbab, 333222c2, ab313a33, aa]
args : aba, 8, 2.0, [43abb1b, ad2abb, ccaa, b22a4a1d, abc44a]
args : aba, 8, 2.0, [23a, c1dc4ba3, ac4cd414, c3112, 4aad2da]
args : aba, 8, 2.0, [d4c32334, a21b312, 21bb, d4144c, ab4c4]
args : aba, 8, 2.0, [cadaddc3, d313, d32d24c2, 4c132411, 2a]
args : aba, 8, 2.0, [c343c, cb3d32ac, 34113, 2dbcaa, c1aacc]
args : aba, 8, 2.0, [4d3dd12, c3cd, b311ddb, d13b242, aa4c]
args : aba, 8, 2.0, [b34b21, 12d41c32, 42b, 2cddb34a, c3ac]
args : aba, 8, 2.0, [cb2cc4c, acd, 2a42ad, d21a1b1, 2ccbab]
args : aba, 8, 2.0, [31acba1, b4d21bd1, 3a, 1ac424, c313c]
args : aba, 8, 2.0, [abbc1a4, a4a2, b41, ccbd332d, c21d44]
args : aba, 8, 2.0, [a2daa4bd, dcc, aaa2, 122bdb34, 14c44]
args : aba, 8, 2.0, [c1dc, 44a, 1a343dad, 3a2c4d3d, d2b14]
args : aba, 8, 2.0, [22ba1b4, b31cb4, 4bbab, cd2c2, acb4]
args : aba, 8, 2.0, [c3aaa4, 13332, 3c2, 43cc2bb, 231bd2]
args : aba, 8, 2.0, [d13cbc2, 41a121b, 2a2b43, c1, c1343]
args : aba, 8, 2.0, [2dc2, d2a133, 41, 34cd2aa, 44c43141]
args : aba, 8, 2.0, [3ada4, cbc, 3bc21, adac4bb, a431ac2]
args : aba, 8, 2.0, [44312444, c3441d, c3, c4d3, 4a24b14]
args : aba, 8, 2.0, [a4, d24cb, ab11313b, ca122c, dda2da]
args : aba, 8, 2.0, [ab2ac, c22, d24d42, 43addb24, a4dd3]
args : aba, 8, 2.0, [4a, ab1d, c421ca3d, 1db1ca, 4241a4c]
args : aba, 8, 2.0, [add2d3, 4cda12d, a313223, cabc, d4]
args : aba, 8, 2.0, [3cc3, 13d, cc4b1, cdd424, 3d432baa]
args : aba, 8, 2.0, [2dadc2d, ba4d1c, 4b, 4d33ab1b, cd4]
args : aba, 8, 2.0, [2a, 1accbc1, 1ddcb3d2, 12, 4143dcd]
args : aba, 8, 2.0, [dbbacb, 2b22cb1, 3ac, 1ab2ccdc, da]
args : aba, 8, 2.0, [1da2cc2, aa4cbda, db, c2311, 4d3b]
args : aba, 8, 2.0, [1b31dada, 431d, b1a, 441d, 32d1a4]
args : aba, 8, 2.0, [1ad14aa, b1, dd1c33, 1ba, 3bcdd1c]
args : aba, 8, 2.0, [113, 33, 3c1dd3a, 21abbba2, 32143]
args : aba, 8, 2.0, [4a, c11, ada43311, 1dadd42c, 4a3d]
args : aba, 8, 2.0, [c4, 3dcd4c2, 41d4, dba33, cbab3b3]
args : aba, 8, 2.0, [b4d, 3baaab4a, 13db32b, a3, ca2ab]
args : aba, 8, 2.0, [aacbd, a21b1, 4a, 12d1bd41, 4a2b2]
args : aba, 8, 2.0, [dd2b11c, 33b, b3d13b4, a33aa, ba]
args : aba, 8, 2.0, [34c33, cac2, b2c2b4c, 1d, 3d44db]
args : aba, 8, 2.0, [d4, 324141b, 23, 1dcdd, cdcda1d4]
args : aba, 8, 2.0, [d31ddb, 31114, bda, 4c113b, 24cd]
args : aba, 8, 2.0, [abc1, d3411a1, 3a1ad, b44d2, 2aa]
args : aba, 8, 2.0, [cc222, b4, 2a3114d3, ad, dcd3a4c]
args : aba, 8, 2.0, [b1b44b34, a2, 2db3b2, 3c, ba11a]
args : aba, 8, 2.0, [14d4b, bc4, 33, a2bb41dd, a224c]
args : aba, 8, 2.0, [a42ad, 442, 2aad1da, c3cc, 24bc]
args : aba, 8, 2.0, [c4d, 14a2add3, b21c4, b23d4, ab]
args : aba, 8, 2.0, [2334, 2cc12, 421c, 3c, d24da2ca]
args : aba, 8, 2.0, [b4, 12, 4a3, 323d1423, 2a344dc3]
args : aba, 8, 2.0, [a231, 1b1d2, 1a3a, d3d2, 1141d3]
args : aba, 8, 2.0, [c4, 2cb, 42a3243, 32dba4b4, 3c1]
args : aba, 8, 2.0, [4411, 1a1a, c21ad, cc4, 1dc4bbd]
args : aba, 8, 2.0, [dc3c, bbba, aad41d, d433b4, 4d1]
args : aba, 8, 2.0, [213b1a, 1dd, a22b4b, 1dba, 34b]
args : aba, 8, 2.0, [1411, 1314, 3432a11d, ab2c, a1]
args : aba, 8, 2.0, [23, ad4c41, a2d3, b2, 41424414]
args : aba, 8, 2.0, [1c, b1a31a, 232d, ddad2c, ccd4]
args : aba, 8, 2.0, [344, bb4c4c4, 2a4413c, dc3, 2c]
args : aba, 8, 2.0, [ac13, bd3, dc3bb2b, 3aa2c, ac2]
args : aba, 8, 2.0, [ad, 13344a1, 23d, dba4, 3b4caa]
args : aba, 8, 2.0, [ca, 11a4221d, 4d31ca, d2b1, 42]
args : aba, 8, 2.0, [b33ddc1, 42c, 1a41, 32, da2a3]
args : aba, 8, 2.0, [23d3, b14cd, 1da221, d2, 4c22]
args : aba, 8, 2.0, [3cacbd3, a4, 32, baa, b1d4d41]
args : aba, 8, 2.0, [1bb21cbc, cbc3, ab, c43a, ac3]
args : aba, 8, 2.0, [4ad, cd12c34, 4c3a, 314, 412a]
args : aba, 8, 2.0, [aaa2a, 1aa34, b3cdbc, b1, 124]
args : aba, 8, 2.0, [a3, b3, dca, 1b324221, 2b2ab4]
args : aba, 8, 2.0, [dd4, a1, 3134, b4b33a, b22ca1]
args : aba, 8, 2.0, [a232a1, b423, c4, 1c, dacac2]
args : aba, 8, 2.0, [2bc1cc, d34, aa1, 3a2bcd, 4d]
args : aba, 8, 2.0, [1a4, ba21c3, b141d, 3da, 2aa]
args : aba, 8, 2.0, [2ac4a, 1bc, ab3b3c1, dc, bbc]
args : aba, 8, 2.0, [ac3a2, 1a, bd3, 34bc2a, d244]
args : aba, 8, 2.0, [c3d32, dbd, cb33acd4, 3d, ca]
args : aba, 8, 2.0, [cada1b, d23, 4a, a3cc4d3, d2]
args : aba, 8, 2.0, [b12a, 4c, 44, 41d1d2b4, dbb1]
args : aba, 8, 2.0, [b4d, 4a2, 232c2, cc, cad3d1]
args : aba, 8, 2.0, [ca, 222, a11d, 12adadc, 3bb]
args : aba, 8, 2.0, [42, c3aba, da, 134, 3dd3a3]
args : aba, 8, 2.0, [4c, a2ab, d2, 443db, cb32c]
args : aba, 8, 2.0, [a413b1, bd244, dd, dc, 2b]
args : aba, 8, 2.0, [a3b2, b1, ca, 3c412, b22]
args : aba, 8, 2.0, [3c, b2cb1d, ad, bc, ac2]
args : aba, 8, 2.0, [1ba, aac, 2b, bdcc, a3b]
args : aba, 8, 2.0, [baa233a4, ab1143b, 4441a231, d131b1]
args : aba, 8, 2.0, [2bb142ba, c42b23, aa13ac, dac32a4c]
args : aba, 8, 2.0, [42a1b, 2c2d32c3, 2caa4abc, 431a3cd]
args : aba, 8, 2.0, [1cc4c2a, 21b4a, cb3dbabd, d1abd3d]
args : aba, 8, 2.0, [bca4ad44, d432, cbbaddbb, bd33b4c]
args : aba, 8, 2.0, [db2a324b, 1d1b, 1a122dd1, 31b3a4d]
args : aba, 8, 2.0, [ad2bdbba, 3dd433a, 341cac4, b2cbc]
args : aba, 8, 2.0, [cc3322c, a1d3ca11, 1bca1, d1bd4dd]
args : aba, 8, 2.0, [33bd2dcc, 4d2ada43, aa3bbca, 2c1]
args : aba, 8, 2.0, [233cb133, 34a, 41c143c1, 1ab1a43]
args : aba, 8, 2.0, [d42, b24c1b21, a1c33dc1, 3cb34a2]
args : aba, 8, 2.0, [abc3adbd, 1bcc3, cd1a3ad, bccdbc]
args : aba, 8, 2.0, [1b2cc, b112c3, 1aa214, bdbb12d4]
args : aba, 8, 2.0, [23a42ac, 31d31d44, 4c4ccb, a13b]
args : aba, 8, 2.0, [21abd2, dcd12, 11cbacb, 11bbba2]
args : aba, 8, 2.0, [b1b2, 2cb3a, 4ccb4b33, 3ccb3ab2]
args : aba, 8, 2.0, [db, 3bd4abc4, bdcbc2bd, b4cb4a4]
args : aba, 8, 2.0, [4dca141, 23d23, 4c3aaca, 21c23]
args : aba, 8, 2.0, [d4aa3aa, 2cd23bc3, 33, ad23322]
args : aba, 8, 2.0, [b4dca4d, 2c, ba4b3cc, bc142bad]
args : aba, 8, 2.0, [3addbd, 4a3db, abcb2, 12ddb4c3]
args : aba, 8, 2.0, [2b13c2db, c324d21, b3, 11c1a4]
args : aba, 8, 2.0, [44c1b44, 4b221, 13d, 4a2b2d41]
args : aba, 8, 2.0, [2d4b2, 1b3a4, cdcc3a, ab1dcca]
args : aba, 8, 2.0, [44cb334, 3daa31a, 4ac1, b2ac3]
args : aba, 8, 2.0, [4143bca2, bbcd3, ac131c, 312b]
args : aba, 8, 2.0, [dd1ac, 231bd4d, 4423bd2, cb24]
args : aba, 8, 2.0, [414bd, abcaa42c, 43baa3, 2a33]
args : aba, 8, 2.0, [3c, d4db4ac4, dca33, ccb4a1b3]
args : aba, 8, 2.0, [a1c1cad2, 4213b34, 1d31a, bd]
args : aba, 8, 2.0, [12423c14, 2d34adbb, cd14, 24]
args : aba, 8, 2.0, [bb3d2443, 3c23b3b, ccd, bb4b]
args : aba, 8, 2.0, [b2c1, cd3c1, babda24, 3a434d]
args : aba, 8, 2.0, [32caac3, c1c, 33ac, 3db2da41]
args : aba, 8, 2.0, [daa4, 4caa221d, a1443dd1, a2]
args : aba, 8, 2.0, [bc112422, 22, 4d2344, 3d4cab]
args : aba, 8, 2.0, [c3d, 11ca2, 312a1413, b2cdda]
args : aba, 8, 2.0, [db3b2d3, 42aa, db, a4ca41d3]
args : aba, 8, 2.0, [233dbb2, 1313, 4d43, adc242]
args : aba, 8, 2.0, [33aba23, 311dd, 4c122, 2b1d]
args : aba, 8, 2.0, [22cdc43, b1c22c, ad1112, 34]
args : aba, 8, 2.0, [d2ad121, 4abcc, 414b13, 131]
args : aba, 8, 2.0, [b4adb31, cd2bba1c, 2d3d, d1]
args : aba, 8, 2.0, [b13a34, db, cb4cc, 1cdabc4a]
args : aba, 8, 2.0, [bc3a, 21312, 2d33, 1423cdda]
args : aba, 8, 2.0, [cddbb4, ad1b113d, 41, 3b1ba]
args : aba, 8, 2.0, [cd21da, b2c3a34, bddc, d1c3]
args : aba, 8, 2.0, [2411d, aabbbd, 112223b, b21]
args : aba, 8, 2.0, [c2c432c2, a1432bb, d2d, b1]
args : aba, 8, 2.0, [141c24ba, b412, 4b21d3, 1b]
args : aba, 8, 2.0, [1d4, ccbadc1, ddc3d4cd, 3a]
args : aba, 8, 2.0, [1ba, 2444c, 41c22da, 42daa]
args : aba, 8, 2.0, [212, 141, 1ca2b4a, 4bb44dc]
args : aba, 8, 2.0, [bc34d, dda21dd, b3db2a, ba]
args : aba, 8, 2.0, [c1, bdd, 3d4d4d2, 4cb1da12]
args : aba, 8, 2.0, [3b11d, bac122dc, 14, b114]
args : aba, 8, 2.0, [224a233, 2d2b, ab, 21331d]
args : aba, 8, 2.0, [c1dd2, 2a, 32da, 11bca221]
args : aba, 8, 2.0, [2bc3c, 1a, 133144d2, 224a]
args : aba, 8, 2.0, [41c, c44c2c, 31baad4, a3a]
args : aba, 8, 2.0, [da23, 43, 3cddc, 41db111c]
args : aba, 8, 2.0, [abc43bb, 23, 44ad3, d1bcd]
args : aba, 8, 2.0, [cd2d, bdac2d, b34, a1c42b]
args : aba, 8, 2.0, [bc12, 1b, dbb34, 1c1dd4aa]
args : aba, 8, 2.0, [cd4ad3d, 24b1c, 41, 3caa]
args : aba, 8, 2.0, [3a1ab, ddd2, cbc4, 4c3c1]
args : aba, 8, 2.0, [b4c, a4334a1c, aca, a34d]
args : aba, 8, 2.0, [b222333, 3a124, 311, abd]
args : aba, 8, 2.0, [44, 33da11, 4424, 31dd41]
args : aba, 8, 2.0, [2bbd, 2acac3, 23, 34d23d]
args : aba, 8, 2.0, [a313ba, dc2, 1c21, 1dd3d]
args : aba, 8, 2.0, [3ad44, 21bb, 12ac, 2ac2]
args : aba, 8, 2.0, [d3b, 4313c43, 2333d, 4b]
args : aba, 8, 2.0, [da344, 342, 43b32, d13d]
args : aba, 8, 2.0, [ca42, d4, 444, 1a1c1cac]
args : aba, 8, 2.0, [43c, 4ab, bb3d, 1acc43b]
args : aba, 8, 2.0, [bb, a4c, 243b22, 2bdacc]
args : aba, 8, 2.0, [242cbcb1, 22, bbd, bd1]
args : aba, 8, 2.0, [3b231b, dc3, 2ab24, 1b]
args : aba, 8, 2.0, [c3c, db3a42, cb, 24131]
args : aba, 8, 2.0, [d3, 4ccdbaa, a22, bdba]
args : aba, 8, 2.0, [43bca, 3ad, a1bc4, 4d2]
args : aba, 8, 2.0, [cad1, 2d, 31c2134, 2b2]
args : aba, 8, 2.0, [dbda, b2dc1ba, d3, 1ab]
args : aba, 8, 2.0, [21d11a, 412, 33, c3a3]
args : aba, 8, 2.0, [ba34d2, b1b, 3cca, 33]
args : aba, 8, 2.0, [cb1, 33132bc, 2b2, dc]
args : aba, 8, 2.0, [2b, 44d1, ab14c, 2dad]
args : aba, 8, 2.0, [2a, 21d, 3422d, ab41c]
args : aba, 8, 2.0, [b2, d4a, ab, a21acacb]
args : aba, 8, 2.0, [24bc, dc, 4dda1, ba2a]
args : aba, 8, 2.0, [2214d2b, 4d2, cb, 3b]
args : aba, 8, 2.0, [143bb1, a2cc, 21, c2]
args : aba, 8, 2.0, [12, da4, c124, 24b4d]
args : aba, 8, 2.0, [d4, 13, cc, bd2b2ab]
args : aba, 8, 2.0, [c2dbc, a4a3, b1, d2]
args : aba, 8, 2.0, [4dacdba, 1c, 21, 4d]
args : aba, 8, 2.0, [ad, 44cd, 1c, da344]
args : aba, 8, 2.0, [dc4, 4bb, 2d43, d1]
args : aba, 8, 2.0, [ba, ca2, 2d, 423dc]
args : aba, 8, 2.0, [121da3cb, 34331, 33c4a1ad]
args : aba, 8, 2.0, [c1cbdb33, cddca3, 134ab33]
args : aba, 8, 2.0, [d321d, cc32d344, 2dab1313]
args : aba, 8, 2.0, [a2442, 1ab2212, dbcc233b]
args : aba, 8, 2.0, [c144244, 3c1ad1c, 4dacb4]
args : aba, 8, 2.0, [4c2b4cca, 42b, 2d331cab]
args : aba, 8, 2.0, [4d3d, 4111dd1d, b43ba2b]
args : aba, 8, 2.0, [aacc322, c1d1d1, 14a3bd]
args : aba, 8, 2.0, [caaa, cab32d41, 3a4bcd1]
args : aba, 8, 2.0, [13b3aa44, 113d31d, 414]
args : aba, 8, 2.0, [a3c2, 3cc42bd, d41adda]
args : aba, 8, 2.0, [321ba422, 1ba, ca13d22]
args : aba, 8, 2.0, [ccdcb4, 141cd, d2d3aca]
args : aba, 8, 2.0, [a3, cc3c41b1, bcd32133]
args : aba, 8, 2.0, [bab32, 2dadd, 3c11c23b]
args : aba, 8, 2.0, [4a3b2b24, 13a1c, 1dba]
args : aba, 8, 2.0, [2ad4b1b, 21c1b2a, ca2]
args : aba, 8, 2.0, [3b1add2, 3d1, 41bb141]
args : aba, 8, 2.0, [a4c4a13a, aad3db, 3c4]
args : aba, 8, 2.0, [a3b441a, b23413c, cdd]
args : aba, 8, 2.0, [a3b4323b, c2222b, 3c3]
args : aba, 8, 2.0, [da4bb4d, 211b1c, 41cb]
args : aba, 8, 2.0, [1224a1, 23a42a43, cb2]
args : aba, 8, 2.0, [1b3ca, 22dc1db, 4d1b1]
args : aba, 8, 2.0, [1b3, dad4bb, 41c2bddb]
args : aba, 8, 2.0, [1ba34b, 3ac, 433b1a41]
args : aba, 8, 2.0, [dd124b14, 2da, 22b143]
args : aba, 8, 2.0, [a2, 4433c123, b43cc2d]
args : aba, 8, 2.0, [adbb11, daa334, 13a2c]
args : aba, 8, 2.0, [d2d, aa322cd, c42b22d]
args : aba, 8, 2.0, [213, 2a241cbb, 1d2ada]
args : aba, 8, 2.0, [cb2ab, 4d43d, 2dd214d]
args : aba, 8, 2.0, [143, 4a4d3ca2, 31aada]
args : aba, 8, 2.0, [d31, 3dbaa2, a34aab2d]
args : aba, 8, 2.0, [1211311a, db, cca324d]
args : aba, 8, 2.0, [d1c1b4, 1423ac3b, bb]
args : aba, 8, 2.0, [4a1bb2, abb414, dac1]
args : aba, 8, 2.0, [b1a2cd1, 21b1, ccda4]
args : aba, 8, 2.0, [12b31, b31c142c, ccc]
args : aba, 8, 2.0, [c3ccd2, 33c14, a3cd4]
args : aba, 8, 2.0, [2d2, a1bab42, a3a21c]
args : aba, 8, 2.0, [3dd21, ab4cbb3, c2c4]
args : aba, 8, 2.0, [3ca1b, cc2, 43a3bcaa]
args : aba, 8, 2.0, [d214bd, 21143, dadd3]
args : aba, 8, 2.0, [dac2dc, 133a3da, a34]
args : aba, 8, 2.0, [1d, 1ddc42bb, 1c2a13]
args : aba, 8, 2.0, [db3cdbd, 42, 12d4ba2]
args : aba, 8, 2.0, [baab13c, 4ad1, bc2d4]
args : aba, 8, 2.0, [12d, cd1b3, 3d231ab4]
args : aba, 8, 2.0, [cdca4bba, 2a1, ab3ca]
args : aba, 8, 2.0, [adca4b, 2d, 31d24cbd]
args : aba, 8, 2.0, [da2a4, 24ca4433, 4c]
args : aba, 8, 2.0, [4c32, 31acb, 114b4c]
args : aba, 8, 2.0, [33c4, a24b3, cc3c43]
args : aba, 8, 2.0, [32ac2b, 1d3dd2, 31b]
args : aba, 8, 2.0, [4a22, 4bd, cc41aa24]
args : aba, 8, 2.0, [1c431dba, dd4, 3d12]
args : aba, 8, 2.0, [c42341dd, 234b, aab]
args : aba, 8, 2.0, [11ad, 134d4a, cac42]
args : aba, 8, 2.0, [ac34244d, 14, d21a2]
args : aba, 8, 2.0, [312ca, b4a3c, a2444]
args : aba, 8, 2.0, [4bbdbd, b1acb1, da3]
args : aba, 8, 2.0, [ca1414d, cac, b3dca]
args : aba, 8, 2.0, [b1b33c, 21, 4adb4d]
args : aba, 8, 2.0, [4cd42c, 3b4, 1a2da]
args : aba, 8, 2.0, [1acbdd, 4414c, c2b]
args : aba, 8, 2.0, [b2bb, 42, bcd4c3b1]
args : aba, 8, 2.0, [4c, 21b13, d4d4bb4]
args : aba, 8, 2.0, [4ca, 4db3b, d232b3]
args : aba, 8, 2.0, [c1bab, 4baa, 144b2]
args : aba, 8, 2.0, [db3, d3ab, da3bc4a]
args : aba, 8, 2.0, [1acd, bdbb1b, 11bc]
args : aba, 8, 2.0, [cb, 21333, dad113b]
args : aba, 8, 2.0, [cb, c32ca32b, 2ac2]
args : aba, 8, 2.0, [2a3a3, 2b2c, 432a]
args : aba, 8, 2.0, [2cc2a1d, 1a, 14c2]
args : aba, 8, 2.0, [4d3, d344c3db, dc]
args : aba, 8, 2.0, [21adc2, 44, 1c3d4]
args : aba, 8, 2.0, [dc3b, 43, d4b112a]
args : aba, 8, 2.0, [b2d, 12a3, 141bdb]
args : aba, 8, 2.0, [cc4a, 14, ccb4ccc]
args : aba, 8, 2.0, [2244, 13c1abd, ba]
args : aba, 8, 2.0, [bda1aa, bb3a, b4d]
args : aba, 8, 2.0, [1a, ba1b, bbda2d3]
args : aba, 8, 2.0, [dd4d, da1bbc, da3]
args : aba, 8, 2.0, [1cc, bbccac, a312]
args : aba, 8, 2.0, [dcd, 2d441, 1d22b]
args : aba, 8, 2.0, [cb, d41c33, cbc14]
args : aba, 8, 2.0, [db, d41a, b4d3d12]
args : aba, 8, 2.0, [cad, 2a34b, d33dc]
args : aba, 8, 2.0, [4d41, bb, 2ddb41]
args : aba, 8, 2.0, [24, 4d141, aaddc]
args : aba, 8, 2.0, [b4244, 111, 2422]
args : aba, 8, 2.0, [d3c, ca44a, d2ab]
args : aba, 8, 2.0, [b4b, 14d, 12b3d4]
args : aba, 8, 2.0, [21, b11abadd, b1]
args : aba, 8, 2.0, [22, 1da3c3ad, ac]
args : aba, 8, 2.0, [31, 2d3, cbabdd2]
args : aba, 8, 2.0, [ba211, bd1b, cab]
args : aba, 8, 2.0, [b31, ca, 4ac4d3d]
args : aba, 8, 2.0, [bc, daca23da, a1]
args : aba, 8, 2.0, [bcbc, ba, d3cdc1]
args : aba, 8, 2.0, [3ca3, 43d, ccca]
args : aba, 8, 2.0, [21a1, 14, 23da1]
args : aba, 8, 2.0, [b4b441, 2c4, 4c]
args : aba, 8, 2.0, [b11, 4341c2, 4b]
args : aba, 8, 2.0, [431c, 31d, cb4c]
args : aba, 8, 2.0, [33, 1d3, c24d2a]
args : aba, 8, 2.0, [a2, c33, bd]
args : aba, 8, 2.0, [a4, 1a, a4]
args : aba, 8, 2.0, [3ab341a1, c224b2a]
args : aba, 8, 2.0, [4da143b1, 4bbadbd]
args : aba, 8, 2.0, [12434c34, 4dcd342]
args : aba, 8, 2.0, [4bc43cc, 4acd33cb]
args : aba, 8, 2.0, [d14a1c4c, 2ddba4]
args : aba, 8, 2.0, [3caaa13, 33d31cb]
args : aba, 8, 2.0, [22b4243, 1d22ddb]
args : aba, 8, 2.0, [23d2332a, 4a11ba]
args : aba, 8, 2.0, [ab2dbcc, cc3131c]
args : aba, 8, 2.0, [3cdcbb, d2ba1b11]
args : aba, 8, 2.0, [b321234b, aa2ba2]
args : aba, 8, 2.0, [212bdb3, db2422]
args : aba, 8, 2.0, [33434b1d, b2133]
args : aba, 8, 2.0, [324d4ad, 424b24]
args : aba, 8, 2.0, [4dbc1, 22a31ddc]
args : aba, 8, 2.0, [2322a3c, ba1db1]
args : aba, 8, 2.0, [43bba, a11d1bdb]
args : aba, 8, 2.0, [dd34c, 4cb2aaa1]
args : aba, 8, 2.0, [c2a4d324, b4b1]
args : aba, 8, 2.0, [3c31cad3, 1abc]
args : aba, 8, 2.0, [24b3b, 2ccb141]
args : aba, 8, 2.0, [1b113d, 2b34d2]
args : aba, 8, 2.0, [a3cb1, b4dc1d3]
args : aba, 8, 2.0, [2b3a, 3d1bd1ba]
args : aba, 8, 2.0, [43a4344d, dac2]
args : aba, 8, 2.0, [1214b, a4a1a44]
args : aba, 8, 2.0, [a2422, 2c42d4b]
args : aba, 8, 2.0, [b33cb, c2a21d4]
args : aba, 8, 2.0, [222411dd, c2d1]
args : aba, 8, 2.0, [32a1d141, a2d]
args : aba, 8, 2.0, [31d2ad, d42cc]
args : aba, 8, 2.0, [ad44b33, 4c1c]
args : aba, 8, 2.0, [d41ac22, 4dda]
args : aba, 8, 2.0, [d4ac, cb3a221]
args : aba, 8, 2.0, [c2b, 1b4b1a31]
args : aba, 8, 2.0, [11cab, 4ddd32]
args : aba, 8, 2.0, [a3d, a3a3dda4]
args : aba, 8, 2.0, [13bbb, b1a2ba]
args : aba, 8, 2.0, [c2ba33, c41c]
args : aba, 8, 2.0, [a1ddcb1b, b2]
args : aba, 8, 2.0, [cd1ada3, c2d]
args : aba, 8, 2.0, [b2cd, c23d4a]
args : aba, 8, 2.0, [db342, a1a22]
args : aba, 8, 2.0, [3bda, ba4cc1]
args : aba, 8, 2.0, [a1c, d2cbcdd]
args : aba, 8, 2.0, [bacd4c, dc2d]
args : aba, 8, 2.0, [a3b2, 44b22]
args : aba, 8, 2.0, [b141a13, 34]
args : aba, 8, 2.0, [2b3a, 2a1db]
args : aba, 8, 2.0, [14d, 1313bb]
args : aba, 8, 2.0, [d2424b, 111]
args : aba, 8, 2.0, [1a, 2b3ad32]
args : aba, 8, 2.0, [43b, aa31bd]
args : aba, 8, 2.0, [122a, 12cad]
args : aba, 8, 2.0, [1332, db3b4]
args : aba, 8, 2.0, [abc1abd, 2a]
args : aba, 8, 2.0, [1331dca, cc]
args : aba, 8, 2.0, [acabada, 14]
args : aba, 8, 2.0, [bbd, b1c13b]
args : aba, 8, 2.0, [c1dd1, 112]
args : aba, 8, 2.0, [32d34a, b4]
args : aba, 8, 2.0, [21, 4c1ab2]
args : aba, 8, 2.0, [b32cb, ad3]
args : aba, 8, 2.0, [a14, b2dcb]
args : aba, 8, 2.0, [21ca, ad2c]
args : aba, 8, 2.0, [dbc, 13122]
args : aba, 8, 2.0, [a4, d3212]
args : aba, 8, 2.0, [14, a2114]
args : aba, 8, 2.0, [1ad, c211]
args : aba, 8, 2.0, [3b, a32c4]
args : aba, 8, 2.0, [141, aa13]
args : aba, 8, 2.0, [13, cbd3d]
args : aba, 8, 2.0, [aa, 4ba2b]
args : aba, 8, 2.0, [3d3, b33]
args : aba, 8, 2.0, [d1d3, c4]
args : aba, 8, 2.0, [b1b2, 3d]
args : aba, 8, 2.0, [3a2, 3bd]
args : aba, 8, 2.0, [132, 23a]
args : aba, 8, 2.0, [334, aa3]
args : aba, 8, 2.0, [ad3, dcc]
args : aba, 8, 2.0, [cc, 3b41]
args : aba, 8, 2.0, [adb, cba]
args : aba, 8, 2.0, [4bc, ac]
args : aba, 8, 2.0, [c3, 14]
args : aba, 8, 2.0, [3a, 2a]
args : aba, 8, 2.0, [dd, 4c]
args : aba, 8, 2.0, [db, 3d]
args : aba, 8, 2.0, [33cbb2b3]
args : aba, 8, 2.0, [c33cd4b1]
args : aba, 8, 2.0, [a2baa2d2]
args : aba, 8, 2.0, [2d322ddc]
args : aba, 8, 2.0, [dc42cd3c]
args : aba, 8, 2.0, [a1ad32cd]
args : aba, 8, 2.0, [1a12daca]
args : aba, 8, 2.0, [c3bdddd4]
args : aba, 8, 2.0, [b3acccb2]
args : aba, 8, 2.0, [4cb4c44]
args : aba, 8, 2.0, [2a32cc3]
args : aba, 8, 2.0, [11d1d4a]
args : aba, 8, 2.0, [2c32311]
args : aba, 8, 2.0, [2424c1b]
args : aba, 8, 2.0, [a23c242]
args : aba, 8, 2.0, [a241a2b]
args : aba, 8, 2.0, [211cac1]
args : aba, 8, 2.0, [43a44cd]
args : aba, 8, 2.0, [32cdd14]
args : aba, 8, 2.0, [42ccb4d]
args : aba, 8, 2.0, [a42c2dc]
args : aba, 8, 2.0, [dd31a3b]
args : aba, 8, 2.0, [c2dd12b]
args : aba, 8, 2.0, [4bbbcd4]
args : aba, 8, 2.0, [cb1cdd4]
args : aba, 8, 2.0, [d1a1dcd]
args : aba, 8, 2.0, [2342223]
args : aba, 8, 2.0, [d34411b]
args : aba, 8, 2.0, [a14142c]
args : aba, 8, 2.0, [bdb4412]
args : aba, 8, 2.0, [d34abcd]
args : aba, 8, 2.0, [34bdbba]
args : aba, 8, 2.0, [bca1dcc]
args : aba, 8, 2.0, [a4c2d1]
args : aba, 8, 2.0, [1c4132]
args : aba, 8, 2.0, [a13a33]
args : aba, 8, 2.0, [22cd2a]
args : aba, 8, 2.0, [22aaa2]
args : aba, 8, 2.0, [ca1c1a]
args : aba, 8, 2.0, [c1aa1b]
args : aba, 8, 2.0, [cbda44]
args : aba, 8, 2.0, [cc22dd]
args : aba, 8, 2.0, [3bbcaa]
args : aba, 8, 2.0, [b2dbcb]
args : aba, 8, 2.0, [bdadbd]
args : aba, 8, 2.0, [3d1c4]
args : aba, 8, 2.0, [211c4]
args : aba, 8, 2.0, [43b31]
args : aba, 8, 2.0, [b33b4]
args : aba, 8, 2.0, [db4c3]
args : aba, 8, 2.0, [2ab2d]
args : aba, 8, 2.0, [434ab]
args : aba, 8, 2.0, [324bd]
args : aba, 8, 2.0, [ccd34]
args : aba, 8, 2.0, [22dba]
args : aba, 8, 2.0, [da22b]
args : aba, 8, 2.0, [cccc4]
args : aba, 8, 2.0, [d1ddd]
args : aba, 8, 2.0, [bccbb]
args : aba, 8, 2.0, [adbaa]
args : aba, 8, 2.0, [13c4]
args : aba, 8, 2.0, [a3c3]
args : aba, 8, 2.0, [3cc3]
args : aba, 8, 2.0, [3ac2]
args : aba, 8, 2.0, [4d1d]
args : aba, 8, 2.0, [1ac3]
args : aba, 8, 2.0, [1224]
args : aba, 8, 2.0, [43ab]
args : aba, 8, 2.0, [c33b]
args : aba, 8, 2.0, [ad24]
args : aba, 8, 2.0, [bb4d]
args : aba, 8, 2.0, [3adc]
args : aba, 8, 2.0, [1dba]
args : aba, 8, 2.0, [daaa]
args : aba, 8, 2.0, [acaa]
args : aba, 8, 2.0, [4a1]
args : aba, 8, 2.0, [2c1]
args : aba, 8, 2.0, [444]
args : aba, 8, 2.0, [222]
args : aba, 8, 2.0, [a41]
args : aba, 8, 2.0, [d23]
args : aba, 8, 2.0, [4cc]
args : aba, 8, 2.0, [4bb]
args : aba, 8, 2.0, [ac4]
args : aba, 8, 2.0, [da3]
args : aba, 8, 2.0, [d3d]
args : aba, 8, 2.0, [2cc]
args : aba, 8, 2.0, [c2d]
args : aba, 8, 2.0, [dd2]
args : aba, 8, 2.0, [cb2]
args : aba, 8, 2.0, [da1]
args : aba, 8, 2.0, [41]
args : aba, 8, 2.0, [41]
args : aba, 8, 2.0, [3d]
args : aba, 8, 2.0, [a3]
args : aba, 8, 2.0, [d3]
args : aba, 8, 2.0, [3a]
args : aba, 8, 2.0, [d2]
args : aba, 8, 2.0, [d1]
args : aba, 8, 2.0, [1c]
args : aba, 8, 2.0, [1a]
args : aba, 8, 2.0, [1a]
args : aba, 8, 2.0, [d1]
args : aba, 8, 2.0, [ac]
args : aba, 8, 2.0, [ba]
args : aba, 8, 2.0, [bd]
args : aba, 8, 2.0, [ac]
args : aba, 8, 2.0, [bd]
args : abaaaaaa, 5, 1.5, [2a, 1accbc1, 1ddcb3d2, 12, 4143dcd]
args : abaaaabb, 9, 1.75, [b2cd, c23d4a]
args : abaabaab, 9, 1.0, [db3cdbd, 42, 12d4ba2]
args : abaababa, 5, 1.0, [4c, 21b13, d4d4bb4]
args : ababaaba, 9, 1.75, [21, 4c1ab2]
args : abababba, 6, 1.0, [1a, 2b3ad32]
args : ababbaab, 6, 1.0, [1d, 1ddc42bb, 1c2a13]
args : ababbaba, 7, 1.5, [a3d, a3a3dda4]
args : ababbabb, 9, 1.0, [22aaa2]
args : ababbabb, 7, 1.5, [b2dbcb]
args : ababbbab, 8, 1.0, [321ba422, 1ba, ca13d22]
args : ababbbab, 8, 1.5, [bc, daca23da, a1]
args : abb, 7, 1.25, [334, aa3]
args : abb, 5, 1.0, [cb, d41c33, cbc14]
args : abb, 8, 1.0, [c3c, db3a42, cb, 24131]
args : abb, 6, 1.5, [cc3322c, a1d3ca11, 1bca1, d1bd4dd]
args : abb, 7, 1.75, [b13a34, db, cb4cc, 1cdabc4a]
args : abbababb, 8, 1.0, [2d2, a1bab42, a3a21c]
args : abbabbba, 8, 1.25, [ac34244d, 14, d21a2]
args : abbabbba, 5, 1.75, [2c1]
args : abbbabaa, 9, 1.25, [32cdd14]
args : baa, 5, 1.25, [da2a4, 24ca4433, 4c]
args : baa, 8, 1.25, [a33c2aa2, 42111244, 1d1c1a3, d4d, 3dc1c]
args : baa, 8, 1.25, [aacbd, a21b1, 4a, 12d1bd41, 4a2b2]
args : baa, 7, 1.0, [dbd4b3, 4badbab, 333222c2, ab313a33, aa]
args : baa, 6, 1.5, [34bdbba]
args : baa, 9, 1.75, [1acbdd, 4414c, c2b]
args : baa, 5, 1.75, [abc1abd, 2a]
args : baa, 5, 1.75, [da22b]
args : baaaaaab, 7, 1.0, [2244, 13c1abd, ba]
args : baaaabba, 5, 1.25, [a1ddcb1b, b2]
args : baaababb, 8, 1.0, [db2a324b, 1d1b, 1a122dd1, 31b3a4d]
args : baababba, 9, 1.0, [adca4b, 2d, 31d24cbd]
args : baabbaaa, 7, 1.0, [242cbcb1, 22, bbd, bd1]
args : baabbaab, 7, 1.5, [22dba]
args : baabbabb, 7, 1.0, [1b113d, 2b34d2]
args : baabbbab, 6, 1.75, [1cc, bbccac, a312]
args : baabbbbb, 8, 1.5, [a3c3]
args : bab, 7, 1.25, [b4d, 3baaab4a, 13db32b, a3, ca2ab]
args : bab, 5, 1.25, [3cdcbb, d2ba1b11]
args : bab, 6, 1.25, [c4, 2cb, 42a3243, 32dba4b4, 3c1]
args : bab, 8, 1.0, [db342, a1a22]
args : bab, 9, 1.75, [22ba1b4, b31cb4, 4bbab, cd2c2, acb4]
args : bab, 9, 1.75, [cb2ab, 4d43d, 2dd214d]
args : bab, 5, 1.75, [b2, d4a, ab, a21acacb]
args : babaaaab, 7, 1.0, [42, c3aba, da, 134, 3dd3a3]
args : babaaaba, 7, 1.0, [d2]
args : babaabba, 9, 1.5, [31d2ad, d42cc]
args : babbaaab, 9, 1.5, [1c431dba, dd4, 3d12]
args : babbabba, 8, 1.25, [b2bb, 42, bcd4c3b1]
args : babbbbaa, 6, 1.25, [3ac2]
args : babbbbba, 9, 1.25, [d31ddb, 31114, bda, 4c113b, 24cd]
args : bba, 7, 1.25, [431c, 31d, cb4c]
args : bba, 5, 1.25, [13c4]
args : bba, 8, 1.25, [21, b11abadd, b1]
args : bba, 9, 1.5, [23a42ac, 31d31d44, 4c4ccb, a13b]
args : bba, 9, 1.5, [db, d41a, b4d3d12]
args : bba, 5, 1.5, [a3c2, 3cc42bd, d41adda]
args : bba, 6, 1.5, [ccdcb4, 141cd, d2d3aca]
args : bba, 6, 1.0, [d14a1c4c, 2ddba4]
args : bba, 6, 1.5, [2cc]
args : bba, 7, 1.75, [bd]
args : bbaaaaba, 7, 1.5, [db3, d3ab, da3bc4a]
args : bbaaabaa, 5, 1.0, [da4bb4d, 211b1c, 41cb]
args : bbaaabaa, 6, 1.75, [2b, 44d1, ab14c, 2dad]
args : bbaaabba, 6, 1.75, [b32cb, ad3]
args : bbaaabbb, 6, 1.25, [233cb133, 34a, 41c143c1, 1ab1a43]
args : bbaaabbb, 5, 1.5, [bb3d2443, 3c23b3b, ccd, bb4b]
args : bbaabaab, 5, 1.25, [bc112422, 22, 4d2344, 3d4cab]
args : bbaababb, 6, 1.25, [4411, 1a1a, c21ad, cc4, 1dc4bbd]
args : bbabaabb, 6, 1.5, [a232a1, b423, c4, 1c, dacac2]
args : bbababaa, 5, 1.75, [43bca, 3ad, a1bc4, 4d2]
args : bbabbaab, 7, 1.75, [c2d]
args : bbabbbaa, 7, 1.5, [cb2]
args : bbb, 9, 1.25, [c23bd414, 133ad44, bcbaa3a, bb4432, 1b2]
args : bbb, 9, 1.25, [ab2ac, c22, d24d42, 43addb24, a4dd3]
args : bbb, 7, 1.5, [c4, 3dcd4c2, 41d4, dba33, cbab3b3]
args : bbb, 7, 1.0, [ba, ca2, 2d, 423dc]
args : bbb, 7, 1.0, [41]
args : bbb, 5, 1.5, [1b3ca, 22dc1db, 4d1b1]
args : bbb, 8, 1.0, [3c, d4db4ac4, dca33, ccb4a1b3]
args : bbb, 8, 1.5, [4da143b1, 4bbadbd]
args : bbb, 7, 1.75, [3b231b, dc3, 2ab24, 1b]
args : bbbaaaba, 8, 1.75, [212bdb3, db2422]
args : bbbaaabb, 8, 1.25, [acaa]
args : bbbaabab, 5, 1.5, [a413b1, bd244, dd, dc, 2b]
args : bbbaabab, 5, 1.5, [cd1ada3, c2d]
args : bbbabaab, 7, 1.25, [2b3a, 2a1db]
args : bbbabbab, 5, 1.0, [ca, 11a4221d, 4d31ca, d2b1, 42]
args : bbbabbba, 6, 1.75, [b2d, 12a3, 141bdb]
args : bbbabbbb, 6, 1.0, [211c4]
args : bbbbaaaa, 5, 1.5, [1ad14aa, b1, dd1c33, 1ba, 3bcdd1c]
args : bbbbabaa, 6, 1.75, [a4, 1a, a4]
args : bbbbabab, 5, 1.25, [bc12, 1b, dbb34, 1c1dd4aa]
args : bbbbbaaa, 8, 1.25, [db3b2d3, 42aa, db, a4ca41d3]
args : bbbbbaba, 8, 1.25, [dcd, 2d441, 1d22b]
args : bbbbbbaa, 7, 1.25, [1c, b1a31a, 232d, ddad2c, ccd4]
args : aa, 9, 1.25, [3a]
args : aa, 7, 1.25, [3b11d, bac122dc, 14, b114]
args : aa, 8, 1.25, [cc222, b4, 2a3114d3, ad, dcd3a4c]
args : aa, 6, 1.25, [cb, 21333, dad113b]
args : aa, 9, 1.0, [c2c432c2, a1432bb, d2d, b1]
args : aa, 9, 1.5, [2d322ddc]
args : aa, 7, 1.5, [43b4bc1, 1c2bbc, 1bb2ddbc, 44cc1b23, dc]
args : aa, 7, 1.0, [2214d2b, 4d2, cb, 3b]
args : aa, 5, 1.5, [33bd2dcc, 4d2ada43, aa3bbca, 2c1]
args : aa, 5, 1.0, [4143bca2, bbcd3, ac131c, 312b]
args : aa, 5, 1.0, [c1, bdd, 3d4d4d2, 4cb1da12]
args : aa, 5, 1.5, [b321234b, aa2ba2]
args : aa, 8, 1.5, [d2ad121, 4abcc, 414b13, 131]
args : aa, 8, 1.0, [143, 4a4d3ca2, 31aada]
args : aa, 6, 1.5, [4dbc1, 22a31ddc]
args : aa, 5, 1.75, [3ada4, cbc, 3bc21, adac4bb, a431ac2]
args : aa, 8, 1.75, [1a]
args : aa, 6, 1.75, [ac1cb4, 2b3d, a1b21a32, 2431334, b4a1132a]
args : aaaaaab, 9, 1.5, [2a, 21d, 3422d, ab41c]
args : aaaaabb, 9, 1.5, [c343c, cb3d32ac, 34113, 2dbcaa, c1aacc]
args : aaaaabb, 6, 1.0, [23, ad4c41, a2d3, b2, 41424414]
args : aaaabaa, 8, 1.0, [d4ac, cb3a221]
args : aaaabab, 7, 1.5, [41c, c44c2c, 31baad4, a3a]
args : aaaabbb, 5, 1.25, [1a4, ba21c3, b141d, 3da, 2aa]
args : aaabaaa, 6, 1.25, [43bba, a11d1bdb]
args : aaababb, 7, 1.5, [d3d]
args : aaabbab, 9, 1.75, [43a44cd]
args : aabaaaa, 5, 1.25, [2bc1cc, d34, aa1, 3a2bcd, 4d]
args : aabaaba, 8, 1.75, [a3b4323b, c2222b, 3c3]
args : aababaa, 9, 1.75, [a4c2d1]
args : aababaa, 8, 1.75, [a1ad32cd]
args : aababba, 7, 1.0, [22cd2a]
args : aabbaaa, 5, 1.5, [a4, d3212]
args : aabbaab, 6, 1.5, [ad, 44cd, 1c, da344]
args : aabbaab, 6, 1.5, [cc, 3b41]
args : aabbabb, 9, 1.25, [ca1c1a]
args : aabbabb, 8, 1.25, [a3b441a, b23413c, cdd]
args : aabbbab, 5, 1.0, [d3]
args : aabbbba, 6, 1.5, [d13cbc2, 41a121b, 2a2b43, c1, c1343]
args : aabbbba, 6, 1.75, [3c, b2cb1d, ad, bc, ac2]
args : aabbbbb, 5, 1.5, [a231, 1b1d2, 1a3a, d3d2, 1141d3]
args : ab, 9, 1.25, [d214bd, 21143, dadd3]
args : ab, 7, 1.25, [3ad44, 21bb, 12ac, 2ac2]
args : ab, 7, 1.25, [a4c4a13a, aad3db, 3c4]
args : ab, 8, 1.25, [3b1add2, 3d1, 41bb141]
args : ab, 9, 1.5, [cadaddc3, d313, d32d24c2, 4c132411, 2a]
args : ab, 9, 1.5, [cd2d, bdac2d, b34, a1c42b]
args : ab, 9, 1.5, [13bbb, b1a2ba]
args : ab, 9, 1.0, [daaa]
args : ab, 7, 1.0, [db, 3bd4abc4, bdcbc2bd, b4cb4a4]
args : ab, 7, 1.5, [12d, cd1b3, 3d231ab4]
args : ab, 7, 1.0, [cccc4]
args : ab, 5, 1.0, [c3d, 11ca2, 312a1413, b2cdda]
args : ab, 8, 1.5, [11ad, 134d4a, cac42]
args : ab, 8, 1.5, [c2dd12b]
args : ab, 6, 1.5, [4ad, cd12c34, 4c3a, 314, 412a]
args : ab, 9, 1.75, [121da3cb, 34331, 33c4a1ad]
args : ab, 9, 1.75, [4cb4c44]
args : ab, 7, 1.75, [2a3a3, 2b2c, 432a]
args : ab, 8, 1.75, [113, 33, 3c1dd3a, 21abbba2, 32143]
args : ab, 8, 1.75, [dd31a3b]
args : ab, 6, 1.75, [a4, d24cb, ab11313b, ca122c, dda2da]
args : abaaaba, 7, 1.25, [44c1b44, 4b221, 13d, 4a2b2d41]
args : abaabab, 8, 1.25, [3a2, 3bd]
args : abaabab, 9, 1.5, [b2c1, cd3c1, babda24, 3a434d]
args : abaabba, 5, 1.0, [1214b, a4a1a44]
args : abaabbb, 5, 1.0, [c1aa1b]
args : abaabbb, 9, 1.75, [224a233, 2d2b, ab, 21331d]
args : ababaaa, 8, 1.25, [212, 141, 1ca2b4a, 4bb44dc]
args : ababaaa, 6, 1.25, [1211311a, db, cca324d]
args : ababaaa, 6, 1.25, [dd34c, 4cb2aaa1]
args : ababaaa, 7, 1.5, [4d1d]
args : ababaab, 8, 1.0, [213, 2a241cbb, 1d2ada]
args : abababb, 6, 1.5, [4d3, d344c3db, dc]
args : abbaabb, 5, 1.25, [cc4a, 14, ccb4ccc]
args : abbabab, 6, 1.25, [a1a243cc, 4241b3, d2b, 1d12cd14, 2bd44bdd]
args : abbbaaa, 9, 1.5, [da23, 43, 3cddc, 41db111c]
args : abbbaab, 8, 1.0, [4cc]
args : abbbabb, 9, 1.75, [da3]
args : abbbbab, 9, 1.75, [2d4b2, 1b3a4, cdcc3a, ab1dcca]
args : abbbbbb, 9, 1.5, [cdca4bba, 2a1, ab3ca]
args : ba, 9, 1.25, [122a, 12cad]
args : ba, 5, 1.25, [24, 4d141, aaddc]
args : ba, 5, 1.25, [21ca, ad2c]
args : ba, 8, 1.25, [cd21da, b2c3a34, bddc, d1c3]
args : ba, 9, 1.0, [b4b, 14d, 12b3d4]
args : ba, 9, 1.5, [d1ddd]
args : ba, 7, 1.0, [1ba, 2444c, 41c22da, 42daa]
args : ba, 7, 1.0, [a1c, d2cbcdd]
args : ba, 5, 1.0, [32a1d141, a2d]
args : ba, 5, 1.5, [db4c3]
args : ba, 8, 1.0, [1331dca, cc]
args : ba, 8, 1.5, [1ad, c211]
args : ba, 8, 1.5, [1c4132]
args : ba, 6, 1.5, [43a4344d, dac2]
args : ba, 8, 1.75, [b1b44b34, a2, 2db3b2, 3c, ba11a]
args : ba, 8, 1.75, [caaa, cab32d41, 3a4bcd1]
args : ba, 6, 1.75, [2424c1b]
args : baaaaaa, 9, 1.75, [aacc322, c1d1d1, 14a3bd]
args : baaaaba, 5, 1.0, [ac13, bd3, dc3bb2b, 3aa2c, ac2]
args : baaabaa, 9, 1.5, [4bb]
args : baabaaa, 5, 1.25, [4c, a2ab, d2, 443db, cb32c]
args : baababb, 6, 1.5, [d41ac22, 4dda]
args : bababbb, 7, 1.25, [b34b21, 12d41c32, 42b, 2cddb34a, c3ac]
args : babbaab, 8, 1.75, [abc43bb, 23, 44ad3, d1bcd]
args : babbaba, 7, 1.25, [c3d32, dbd, cb33acd4, 3d, ca]
args : babbbab, 7, 1.5, [c3, 14]
args : babbbbb, 8, 1.0, [211cac1]
args : bb, 7, 1.25, [312ca, b4a3c, a2444]
args : bb, 5, 1.25, [dbda, b2dc1ba, d3, 1ab]
args : bb, 5, 1.25, [cb1cdd4]
args : bb, 9, 1.0, [14d4b, bc4, 33, a2bb41dd, a224c]
args : bb, 9, 1.5, [bccbb]
args : bb, 7, 1.0, [b12a, 4c, 44, 41d1d2b4, dbb1]
args : bb, 7, 1.5, [13, cbd3d]
args : bb, 7, 1.0, [3bbcaa]
args : bb, 5, 1.0, [12, da4, c124, 24b4d]
args : bb, 8, 1.5, [a42ad, 442, 2aad1da, c3cc, 24bc]
args : bb, 8, 1.5, [2bb142ba, c42b23, aa13ac, dac32a4c]
args : bb, 8, 1.0, [b4dca4d, 2c, ba4b3cc, bc142bad]
args : bb, 8, 1.5, [4bc43cc, 4acd33cb]
args : bb, 8, 1.0, [1ac3]
args : bb, 8, 1.0, [444]
args : bb, 6, 1.0, [42a1b, 2c2d32c3, 2caa4abc, 431a3cd]
args : bb, 7, 1.75, [31, 2d3, cbabdd2]
args : bb, 8, 1.75, [cad, 2a34b, d33dc]
args : bb, 8, 1.75, [c2b, 1b4b1a31]
args : bbaaaaa, 9, 1.0, [b4244, 111, 2422]
args : bbaaabb, 7, 1.5, [4dca141, 23d23, 4c3aaca, 21c23]
args : bbaabbb, 5, 1.75, [33bc4c, 42321dc, aacb13b2, a4db2cb4, 1a]
args : bbabaaa, 9, 1.75, [d34abcd]
args : bbabaab, 8, 1.5, [a1c1cad2, 4213b34, 1d31a, bd]
args : bbabaab, 6, 1.5, [2342223]
args : bbababa, 7, 1.75, [222411dd, c2d1]
args : bbabbab, 5, 1.25, [a42c2dc]
args : bbabbbb, 7, 1.25, [32ac2b, 1d3dd2, 31b]
args : bbabbbb, 7, 1.75, [a2442, 1ab2212, dbcc233b]
args : bbbaaab, 6, 1.5, [22, 1da3c3ad, ac]
args : bbbaaba, 6, 1.5, [1a12daca]
args : bbbaabb, 6, 1.75, [b4adb31, cd2bba1c, 2d3d, d1]
args : bbbabab, 7, 1.75, [dd, 4c]
args : bbbbaaa, 7, 1.75, [b1b2, 2cb3a, 4ccb4b33, 3ccb3ab2]
args : bbbbabb, 9, 1.0, [3cacbd3, a4, 32, baa, b1d4d41]
args : bbbbabb, 7, 1.5, [324bd]
args : aaabaa, 9, 1.5, [b4c, a4334a1c, aca, a34d]
args : aaabaa, 8, 1.5, [bdb4412]
args : aaabab, 7, 1.0, [ab2dbcc, cc3131c]
args : aaabba, 9, 1.5, [a3b2, 44b22]
args : aaabbb, 5, 1.5, [4d41, bb, 2ddb41]
args : aabaaa, 7, 1.25, [c3ccd2, 33c14, a3cd4]
args : aabaaa, 5, 1.25, [414bd, abcaa42c, 43baa3, 2a33]
args : aabaaa, 5, 1.5, [44, 33da11, 4424, 31dd41]
args : aabaab, 9, 1.25, [4cd42c, 3b4, 1a2da]
args : aabaab, 5, 1.25, [b1b33c, 21, 4adb4d]
args : aabaab, 9, 1.5, [c2dbc, a4a3, b1, d2]
args : aababa, 7, 1.25, [d42, b24c1b21, a1c33dc1, 3cb34a2]
args : aababa, 9, 1.0, [43ab]
args : aababb, 6, 1.25, [1a]
args : aabbaa, 8, 1.5, [3bda, ba4cc1]
args : aabbab, 7, 1.25, [4c32, 31acb, 114b4c]
args : abaaba, 5, 1.25, [d3b, 4313c43, 2333d, 4b]
args : abaaba, 8, 1.25, [d1a1dcd]
args : abaaba, 5, 1.0, [bbd, b1c13b]
args : abaaba, 8, 1.0, [13b3aa44, 113d31d, 414]
args : abaaba, 6, 1.5, [b4b441, 2c4, 4c]
args : abaabb, 7, 1.25, [1ba34b, 3ac, 433b1a41]
args : abaabb, 6, 1.5, [11d1d4a]
args : ababab, 9, 1.5, [1224a1, 23a42a43, cb2]
args : ababbb, 8, 1.5, [dd4, a1, 3134, b4b33a, b22ca1]
args : abbaaa, 7, 1.25, [d1]
args : abbaab, 5, 1.5, [a2baa2d2]
args : abbaab, 6, 1.0, [dbc, 13122]
args : abbabb, 9, 1.0, [c1dc, 44a, 1a343dad, 3a2c4d3d, d2b14]
args : abbabb, 5, 1.5, [cad1, 2d, 31c2134, 2b2]
args : abbbbb, 6, 1.0, [cb2cc4c, acd, 2a42ad, d21a1b1, 2ccbab]
args : baaaaa, 8, 1.25, [32d34a, b4]
args : baaaaa, 6, 1.75, [b3acccb2]
args : baaaab, 7, 1.0, [2aaa1b1c, bbb1124, b4d3ca, 13a1, 4d24acb]
args : baaaba, 6, 1.0, [bdadbd]
args : baaabb, 5, 1.75, [4a, ab1d, c421ca3d, 1db1ca, 4241a4c]
args : baabbb, 8, 1.25, [1ba, aac, 2b, bdcc, a3b]
args : babaaa, 9, 1.5, [b222333, 3a124, 311, abd]
args : bababa, 8, 1.0, [222]
args : bababb, 8, 1.5, [4a1bb2, abb414, dac1]
args : bababb, 6, 1.5, [24bc, dc, 4dda1, ba2a]
args : bababb, 5, 1.75, [33aba23, 311dd, 4c122, 2b1d]
args : babbab, 8, 1.25, [d2424b, 111]
args : babbab, 8, 1.25, [bb4d]
args : babbab, 9, 1.5, [4dacdba, 1c, 21, 4d]
args : bbaaaa, 5, 1.0, [dd2]
args : bbaaba, 7, 1.0, [143bb1, a2cc, 21, c2]
args : bbaaba, 7, 1.75, [344, bb4c4c4, 2a4413c, dc3, 2c]
args : bbaaba, 5, 1.75, [11cab, 4ddd32]
args : bbabaa, 9, 1.25, [bcbc, ba, d3cdc1]
args : bbabab, 8, 1.0, [44cb334, 3daa31a, 4ac1, b2ac3]
args : bbabba, 9, 1.75, [ad24]
args : bbabba, 8, 1.75, [34c33, cac2, b2c2b4c, 1d, 3d44db]
args : bbabbb, 7, 1.25, [cd4ad3d, 24b1c, 41, 3caa]
args : bbabbb, 5, 1.25, [132, 23a]
args : bbabbb, 6, 1.0, [41]
args : bbabbb, 5, 1.75, [c33b]
args : bbbaab, 8, 1.0, [ad3, dcc]
args : bbbabb, 5, 1.25, [3c31cad3, 1abc]
args : bbbabb, 8, 1.25, [bacd4c, dc2d]
args : bbbabb, 7, 1.5, [2b13c2db, c324d21, b3, 11c1a4]
args : bbbabb, 5, 1.0, [baa233a4, ab1143b, 4441a231, d131b1]
args : bbbbaa, 5, 1.0, [cada1b, d23, 4a, a3cc4d3, d2]
args : bbbbab, 8, 1.5, [abbc1a4, a4a2, b41, ccbd332d, c21d44]
args : bbbbab, 7, 1.75, [a3b2, b1, ca, 3c412, b22]
args : bbbbba, 5, 1.25, [2cc2a1d, 1a, 14c2]
args : bbbbbb, 5, 1.25, [ac]
args : bbbbbb, 5, 1.75, [c1cbdb33, cddca3, 134ab33]
args : aaaaa, 9, 1.0, [12b31, b31c142c, ccc]
args : aaaaa, 9, 1.75, [a3cb1, b4dc1d3]
args : aaabb, 9, 1.5, [1224]
args : aaabb, 7, 1.75, [2bbd, 2acac3, 23, 34d23d]
args : aabaa, 5, 1.25, [1c]
args : aabaa, 8, 1.5, [4a22, 4bd, cc41aa24]
args : aabab, 6, 1.75, [141c24ba, b412, 4b21d3, 1b]
args : aabba, 7, 1.0, [dd1ac, 231bd4d, 4423bd2, cb24]
args : aabba, 5, 1.5, [bb, a4c, 243b22, 2bdacc]
args : aabba, 8, 1.5, [1a, ba1b, bbda2d3]
args : aabbb, 8, 1.25, [2c32311]
args : aabbb, 9, 1.0, [1b3, dad4bb, 41c2bddb]
args : aabbb, 6, 1.0, [d2d, aa322cd, c42b22d]
args : aabbb, 8, 1.75, [b33cb, c2a21d4]
args : abaaa, 9, 1.0, [b31, ca, 4ac4d3d]
args : abaaa, 8, 1.0, [cc22dd]
args : abaab, 9, 1.25, [2334, 2cc12, 421c, 3c, d24da2ca]
args : abaab, 8, 1.25, [3a, 2a]
args : abaab, 6, 1.25, [213b1a, 1dd, a22b4b, 1dba, 34b]
args : abaab, 5, 1.0, [2322a3c, ba1db1]
args : abaab, 6, 1.5, [12423c14, 2d34adbb, cd14, 24]
args : abaab, 6, 1.75, [1332, db3b4]
args : ababa, 6, 1.0, [2dadc2d, ba4d1c, 4b, 4d33ab1b, cd4]
args : ababa, 6, 1.5, [33434b1d, b2133]
args : ababb, 7, 1.0, [4bc, ac]
args : ababb, 5, 1.0, [b1b2, 3d]
args : abbaa, 9, 1.75, [a2, c33, bd]
args : abbab, 5, 1.25, [4d121b14, a1c2c2, 41b2bcb2, cd4, 41d422]
args : abbab, 7, 1.0, [1b2cc, b112c3, 1aa214, bdbb12d4]
args : abbab, 7, 1.5, [dac2dc, 133a3da, a34]
args : abbab, 5, 1.5, [4c2b4cca, 42b, 2d331cab]
args : abbab, 8, 1.5, [a4d24d4c, b3b24, a1d4, ab24bcd, cbcaddb]
args : abbab, 6, 1.5, [3caaa13, 33d31cb]
args : abbba, 9, 1.25, [b141a13, 34]
args : abbba, 6, 1.25, [d1d3, c4]
args : abbba, 9, 1.0, [bda1aa, bb3a, b4d]
args : abbba, 7, 1.5, [21d11a, 412, 33, c3a3]
args : abbbb, 9, 1.25, [c1bab, 4baa, 144b2]
args : abbbb, 8, 1.25, [d34411b]
args : abbbb, 6, 1.5, [bc3a, 21312, 2d33, 1423cdda]
args : baaaa, 5, 1.5, [b4d, 4a2, 232c2, cc, cad3d1]
args : baaaa, 5, 1.5, [21adc2, 44, 1c3d4]
args : baaab, 7, 1.25, [233dbb2, 1313, 4d43, adc242]
args : baaab, 8, 1.75, [1dba]
args : baaab, 6, 1.75, [a2daa4bd, dcc, aaa2, 122bdb34, 14c44]
args : baaba, 6, 1.25, [ad2bdbba, 3dd433a, 341cac4, b2cbc]
args : baaba, 7, 1.5, [1b31dada, 431d, b1a, 441d, 32d1a4]
args : baabb, 8, 1.25, [d321d, cc32d344, 2dab1313]
args : baabb, 8, 1.0, [b11, 4341c2, 4b]
args : baabb, 5, 1.75, [a14, b2dcb]
args : babaa, 6, 1.25, [a3, b3, dca, 1b324221, 2b2ab4]
args : babbb, 8, 1.25, [a23c242]
args : babbb, 7, 1.5, [c42341dd, 234b, aab]
args : bbaaa, 9, 1.5, [31acba1, b4d21bd1, 3a, 1ac424, c313c]
args : bbaaa, 9, 1.5, [1bb21cbc, cbc3, ab, c43a, ac3]
args : bbaaa, 8, 1.5, [4bbbcd4]
args : bbaab, 6, 1.25, [2dc2, d2a133, 41, 34cd2aa, 44c43141]
args : bbaba, 9, 1.25, [d4, 324141b, 23, 1dcdd, cdcda1d4]
args : bbaba, 5, 1.25, [3dd21, ab4cbb3, c2c4]
args : bbaba, 7, 1.5, [ba211, bd1b, cab]
args : bbaba, 9, 1.75, [adbaa]
args : bbabb, 7, 1.5, [abc1, d3411a1, 3a1ad, b44d2, 2aa]
args : bbabb, 7, 1.0, [db, 3d]
args : bbbaa, 5, 1.5, [c1dd1, 112]
args : bbbaa, 5, 1.0, [ccd34]
args : bbbab, 9, 1.0, [a3]
args : bbbab, 5, 1.0, [1acd, bdbb1b, 11bc]
args : bbbba, 6, 1.25, [4bbdbd, b1acb1, da3]
args : bbbba, 5, 1.75, [ca, 222, a11d, 12adadc, 3bb]
args : bbbbb, 8, 1.5, [c3aaa4, 13332, 3c2, 43cc2bb, 231bd2]
args : bbbbb, 8, 1.0, [dc3c, bbba, aad41d, d433b4, 4d1]
args : bbbbb, 5, 1.75, [23d2332a, 4a11ba]
args : bbbbb, 5, 1.75, [2b3a, 3d1bd1ba]
args : bbbbb, 8, 1.75, [3cc3]
args : bbbbb, 6, 1.75, [4a, c11, ada43311, 1dadd42c, 4a3d]
```
