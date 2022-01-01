# Kotlin의 자료형
## 설명
 * Kotlin의 자료형은 다음과 같음.
   * 정수 자료형
     * ```Byte``` : 1바이트 (8비트), -2<sup>7</sup> ~ +2<sup>7</sup>-1
     * ```Short``` : 2바이트 (16비트), -2<sup>15</sup> ~ +2<sup>15</sup>-1
     * ```Int``` : 4바이트 (32비트), -2<sup>31</sup> ~ +2<sup>31</sup>-1
     * ```Long``` : 8바이트 (64비트), -2<sup>63</sup> ~ +2<sup>63</sup>-1
     * ```UByte``` : 1바이트 (8비트), 0 ~ +2<sup>8</sup>-1
     * ```UShort``` : 2바이트 (16비트), 0 ~ +2<sup>16</sup>-1
     * ```UInt``` : 4바이트 (32비트), 0 ~ +2<sup>32</sup>-1
     * ```ULong``` : 8바이트 (64비트), 0 ~ +2<sup>64</sup>-1
   * 실수 자료형
     * ```Float``` : 4바이트 (32비트)
     * ```Double``` : 8바이트 (64비트)
   * 논리 자료형
     * ```Boolean``` : ```true``` 또는 ```false```의 값을 가짐
   * 문자 및 문자열 자료형
     * ```Char``` : 1개의 문자
     * ```String``` : 여러 개의 문자 (문자열)
 * 자료형의 추론
   * 변수를 선언할 때 그 자료형을 추론하여 표시
   * ```var num0 = 1234``` : ```num0```은 ```Int``` 자료형으로 추론
   * ```var num1 = 1234L``` : ```num1```은 ```Long``` 자료형으로 추론 (접미사 ```L```에 근거)
   * ```var num2 = 0xDEADBEEF``` : ```num2```는 16진수 표기법을 적용한 ```Long``` 자료형으로 추론
   * ```var theseFeelingsThatIfeel = true``` : ```theseFeelingsThatIfeel```은 ```Boolean``` 자료형으로 추론
 * 형 변환
   * 자료형이 ```DATATYPE0```이고 값이 ```VALUE0```인 변수 ```VARIABLE0```을 선언한 후, ```DATATYPE1``` 자료형의 변수 ```VARIABLE1```에 그 자료형을 바꾸어 ```VARIABLE0```의 값을 저장하는 방법은 다음과 같음.
```kotlin
var VARIABLE0 : DATATYPE0 = VALUE0 // 변수 VARIABLE0 선언
var VARIABLE1 : DATATYPE1 = VARIABLE0.toDATATYPE1() // 자료형을 바꾸어 저장
```
 * 참조 주소 비교
   * ```VAR0 === VAR1``` : ```VAR0```과 ```VAR1``` 변수의 참조 주소가 같으면 ```true```, 그렇지 않으면 ```false```
   * String 자료형의 경우,
     * Kotlin에서는 heap 영역의 String Pool에 문자열을 저장하기 때문에, 일반적으로 동일한 문자열은 참조 주소가 동일함.
     * ```STRING0 === STRING1``` : ```STRING0```과 ```STRING1``` 문자열의 참조 주소가 같으면 ```true```, 그렇지 않으면 ```false```
 * ```Any```와 ```?```
   * ```Any```는 일반적으로 어떤 자료형이든 가능하다는 것을 의미
   * ```?```는 ```null``` 값을 허용한다는 의미
   * ```Any?```는 어떤 자료형이든 가능하며, ```null``` 값이 허용된다는 의미
   * 참고:
     * [변수의 null](Kotlin%20Basics/002_null.md)
     * [Any와 변수의 자료형 확인](Kotlin%20Basics/003_AnyAndTypecheck.md)

## 예제 코드
```kotlin
fun main() {
    
   	// 형 변환
    var a : Int = 123
    var b : Long = a.toLong() // Int 자료형인 a를 Long 자료형으로 변환하여 b에 저장
    var c : Char = a.toChar() // Int 자료형인 a를 Char 자료형으로 변환하여 c에 저장
    var d : String = c.toString() // Char 자료형인 c를 String 자료형으로 변환하여 d에 저장
    
    println(String.format("a=%d, b=%d, c=%c, d=%s", a, b, c, d))
    
    var e : String = "4321"
    var f : String = "2.12"
    var g : Int = e.toInt() // String 자료형인 e를 Int 자료형으로 변환하여 g에 저장
    var h : Double = f.toDouble() // String 자료형인 f를 Double 자료형으로 변환하여 h에 저장
    
    println(String.format("e=%s, f=%s, g=%d, h=%f", e, f, g, h))
    
    var i : Double = g + h // g + h의 값을 계산하여 i에 저장
    var j : Float = (g + h).toFloat() // g + h의 값을 계산한 후 Float 형으로 변환하여 j에 저장
    var k : Double = (g + h).toDouble() // g + h의 값을 계산한 후 Double 형으로 변환하여 k에 저장
    
    println(String.format("i=%f, j=%f, k=%f", i, j, k))
    
    // String의 참조 주소 비교
    var str0 : String = "aaa"
    var str1 : String = "bbb"
    var str2 : String = "aaa"
    
    var ref01 : Boolean = (str0 === str1) // "aaa", "bbb"로 다른 string이므로 참조 주소도 다름
    var ref02 : Boolean = (str0 === str2) // "aaa", "aaa"로 같은 string이므로 참조 주소도 같음
    var ref12 : Boolean = (str1 === str2) // "bbb", "aaa"로 다른 string이므로 참조 주소도 다름
    
    println(String.format("%s %s %s", ref01, ref02, ref12))
}
```

## 실행 결과
```
a=123, b=123, c={, d={
e=4321, f=2.12, g=4321, h=2.120000
i=4323.120000, j=4323.120117, k=4323.120000
false true false
```
