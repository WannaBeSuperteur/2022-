## 코드
```kotlin
fun main() {
	val name = listOf("Yujin", "Gaeul", "Rei", "Wonyoung", "Liz", "Leeseo")
    val birth = listOf(2003, 2002, 2004, 2004, 2004, 2007)
    println("1. name zip birth          = ${name zip birth}")
    println("2. name.zip(birth)         = ${name.zip(birth)}")
    println("3. birth zip name          = ${birth zip name}")
    println("4. birth.zip(name)         = ${birth.zip(name)}\n")
    
    val threeMembers = listOf("Yujin", "Gaeul", "Rei")
    println("5. threeMembers.zip(birth) = ${threeMembers.zip(birth)}")
    println("6. birth.zip(threeMembers) = ${birth.zip(threeMembers)}\n")
    
    println("7. ${name.zip(birth) { n, b -> "${n} was born in ${b}." }}")
    println("8. ${birth.zip(name) { b, n -> "${b} is the year ${n} was born." }}\n")
            
    val iveDebutAge = name.zip(birth).map { it.first to 2021 - it.second }
    val iveInverse = name.zip(birth).map { it.second to it.first }
    println("9.  iveDebutAge        = $iveDebutAge")
    println("10. iveInverse         = $iveInverse")
    
    val iveDebutAgeUnzip = iveDebutAge.unzip()
    val iveInverseAgeUnzip = iveInverse.unzip()
    println("11. iveDebutAgeUnzip   = $iveDebutAgeUnzip")
    println("12. iveInverseAgeUnzip = $iveInverseAgeUnzip")
}
```

## 실행 결과
```kotlin
1. name zip birth          = [(Yujin, 2003), (Gaeul, 2002), (Rei, 2004), (Wonyoung, 2004), (Liz, 2004), (Leeseo, 2007)]
2. name.zip(birth)         = [(Yujin, 2003), (Gaeul, 2002), (Rei, 2004), (Wonyoung, 2004), (Liz, 2004), (Leeseo, 2007)]
3. birth zip name          = [(2003, Yujin), (2002, Gaeul), (2004, Rei), (2004, Wonyoung), (2004, Liz), (2007, Leeseo)]
4. birth.zip(name)         = [(2003, Yujin), (2002, Gaeul), (2004, Rei), (2004, Wonyoung), (2004, Liz), (2007, Leeseo)]

5. threeMembers.zip(birth) = [(Yujin, 2003), (Gaeul, 2002), (Rei, 2004)]
6. birth.zip(threeMembers) = [(2003, Yujin), (2002, Gaeul), (2004, Rei)]

7. [Yujin was born in 2003., Gaeul was born in 2002., Rei was born in 2004., Wonyoung was born in 2004., Liz was born in 2004., Leeseo was born in 2007.]
8. [2003 is the year Yujin was born., 2002 is the year Gaeul was born., 2004 is the year Rei was born., 2004 is the year Wonyoung was born., 2004 is the year Liz was born., 2007 is the year Leeseo was born.]

9.  iveDebutAge        = [(Yujin, 18), (Gaeul, 19), (Rei, 17), (Wonyoung, 17), (Liz, 17), (Leeseo, 14)]
10. iveInverse         = [(2003, Yujin), (2002, Gaeul), (2004, Rei), (2004, Wonyoung), (2004, Liz), (2007, Leeseo)]
11. iveDebutAgeUnzip   = ([Yujin, Gaeul, Rei, Wonyoung, Liz, Leeseo], [18, 19, 17, 17, 17, 14])
12. iveInverseAgeUnzip = ([2003, 2002, 2004, 2004, 2004, 2007], [Yujin, Gaeul, Rei, Wonyoung, Liz, Leeseo])
```
