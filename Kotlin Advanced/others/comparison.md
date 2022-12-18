## 코드
```kotlin
data class Member (
    var name: String = "",
    var birth_year: Int = 0
)

fun main() {
	val ive = listOf<Member> (
        Member("Yujin", 2003),
        Member("Gaeul", 2002),
        Member("Rei", 2004),
        Member("Wonyoung", 2004),
        Member("Liz", 2004),
        Member("Leeseo", 2007)
    )
    
    // compareBy
    val test00 = ive.sortedWith(compareBy(
        { it.birth_year },
        { it.name }
    ))
    println("compareBy                : ${test00}")
    
    val test01 = ive.sortedWith(compareBy(
        { it.name.length },
        { it.birth_year }
    ))
    println("compareBy                : ${test01}")
    
    // compareByDescending
    val test02 = ive.sortedWith(compareByDescending { it.name } )
    println("\ncompareByDescending      : ${test02}")
    
    val test03 = ive.sortedWith(compareByDescending { it.name.length } )
    println("compareByDescending      : ${test03}")
    
    // compareValues
    val test04 = compareValues("Yujin", "Gaeul")
    val test05 = compareValues("Gaeul", "Wonyoung")
    val test06 = compareValues("Yujin", "Wonyoung")
    println("\ncompareValues            : (Yujin, Gaeul) ${test04}")
    println("compareValues            : (Gaeul, Wonyoung) ${test05}")
    println("compareValues            : (Yujin, Wonyoung) ${test06}")
    
    // compareValuesBy
    fun compareBirthYearThenName(a: Member, b: Member): Int =
        compareValuesBy(a, b, { it.birth_year }, { it.name })
    
    val test07 = compareBirthYearThenName(ive[0], ive[4])
    val test08 = compareBirthYearThenName(ive[4], ive[3])
    val test09 = compareBirthYearThenName(ive[0], ive[3])
    println("\nusing comparator, compare birth year then name")
    println("compareValuesBy          : (${ive[0].name}, ${ive[4].name}) ${test07}")
    println("compareValuesBy          : (${ive[4].name}, ${ive[3].name}) ${test08}")
    println("compareValuesBy          : (${ive[0].name}, ${ive[3].name}) ${test09}")
    
    // compareBy with thenBy and thenByDescending
    val comparator1 = compareBy<Member> { it.birth_year }.thenBy { it.name }
    val comparator2 = compareBy<Member> { it.birth_year }.thenByDescending { it.name }
    val test10 = ive.sortedWith(comparator1)
    val test11 = ive.sortedWith(comparator2)
    println("\ncompare thenBy           : ${test10}")
    println("compare thenByDescending : ${test11}")
    
    // compareBy with then and thenDescending
    val comparator3 = compareBy<Member> { it.name.length }.then(comparator1)
    val comparator4 = compareBy<Member> { it.name.length }.thenDescending(comparator1) // NOT comparator2
    val test12 = ive.sortedWith(comparator3)
    val test13 = ive.sortedWith(comparator4)
    println("compare then             : ${test12}")
    println("compare thenDescending   : ${test13}")
    
    // naturalOrder
    val lengthThenNatural = compareBy<String> { it.length }.then (naturalOrder())
    val firstLetterThenNatural = compareBy<String> { it[0] }.then (naturalOrder())
    
    val test14 = ive.map { it.name }.sortedWith(lengthThenNatural)
    val test15 = ive.map { it.name }.sortedWith(firstLetterThenNatural)
    println("\nnatural order            : (length) ${test14}")
    println("natural order            : (first letter) ${test15}")
    
    // nullsFirst and reverseOrder
    val girlgroups = listOf("IVE", null, "BLACKPINK", "aespa", null, "Red Velvet", "LE SSERAFIM", "(G)-IDLE", "KARA", null)
    
    val test16 = girlgroups.sortedWith(nullsFirst())
    val test17 = girlgroups.sortedWith(nullsFirst(reverseOrder()))
    val test18 = girlgroups.sortedWith(nullsLast())
    val test19 = girlgroups.sortedWith(nullsLast(reverseOrder()))
    
    println("\nnulls first              : ${test16}")
    println("nulls first (Reverse)    : ${test17}")
    println("nulls last               : ${test18}")
    println("nulls last  (Reverse)    : ${test19}")
    
    // reversed
    val ive_members_name = ive.map { it.name }
    val lengthThenString = compareBy<String> { it.length }.thenBy { it }
    
    val test20 = ive_members_name.sortedWith(lengthThenString)
    val test21 = ive_members_name.sortedWith(lengthThenString.reversed())
    println("\nsorted with              : ${test20}")
    println("sorted with (Reverse)    : ${test21}")
}
```

## 실행 결과
```kotlin
compareBy                : [Member(name=Gaeul, birth_year=2002), Member(name=Yujin, birth_year=2003), Member(name=Liz, birth_year=2004), Member(name=Rei, birth_year=2004), Member(name=Wonyoung, birth_year=2004), Member(name=Leeseo, birth_year=2007)]
compareBy                : [Member(name=Rei, birth_year=2004), Member(name=Liz, birth_year=2004), Member(name=Gaeul, birth_year=2002), Member(name=Yujin, birth_year=2003), Member(name=Leeseo, birth_year=2007), Member(name=Wonyoung, birth_year=2004)]

compareByDescending      : [Member(name=Yujin, birth_year=2003), Member(name=Wonyoung, birth_year=2004), Member(name=Rei, birth_year=2004), Member(name=Liz, birth_year=2004), Member(name=Leeseo, birth_year=2007), Member(name=Gaeul, birth_year=2002)]
compareByDescending      : [Member(name=Wonyoung, birth_year=2004), Member(name=Leeseo, birth_year=2007), Member(name=Yujin, birth_year=2003), Member(name=Gaeul, birth_year=2002), Member(name=Rei, birth_year=2004), Member(name=Liz, birth_year=2004)]

compareValues            : (Yujin, Gaeul) 18
compareValues            : (Gaeul, Wonyoung) -16
compareValues            : (Yujin, Wonyoung) 2

using comparator, compare birth year then name
compareValuesBy          : (Yujin, Liz) -1
compareValuesBy          : (Liz, Wonyoung) -11
compareValuesBy          : (Yujin, Wonyoung) -1

compare thenBy           : [Member(name=Gaeul, birth_year=2002), Member(name=Yujin, birth_year=2003), Member(name=Liz, birth_year=2004), Member(name=Rei, birth_year=2004), Member(name=Wonyoung, birth_year=2004), Member(name=Leeseo, birth_year=2007)]
compare thenByDescending : [Member(name=Gaeul, birth_year=2002), Member(name=Yujin, birth_year=2003), Member(name=Wonyoung, birth_year=2004), Member(name=Rei, birth_year=2004), Member(name=Liz, birth_year=2004), Member(name=Leeseo, birth_year=2007)]
compare then             : [Member(name=Liz, birth_year=2004), Member(name=Rei, birth_year=2004), Member(name=Gaeul, birth_year=2002), Member(name=Yujin, birth_year=2003), Member(name=Leeseo, birth_year=2007), Member(name=Wonyoung, birth_year=2004)]
compare thenDescending   : [Member(name=Rei, birth_year=2004), Member(name=Liz, birth_year=2004), Member(name=Yujin, birth_year=2003), Member(name=Gaeul, birth_year=2002), Member(name=Leeseo, birth_year=2007), Member(name=Wonyoung, birth_year=2004)]

natural order            : (length) [Liz, Rei, Gaeul, Yujin, Leeseo, Wonyoung]
natural order            : (first letter) [Gaeul, Leeseo, Liz, Rei, Wonyoung, Yujin]

nulls first              : [null, null, null, (G)-IDLE, BLACKPINK, IVE, KARA, LE SSERAFIM, Red Velvet, aespa]
nulls first (Reverse)    : [null, null, null, aespa, Red Velvet, LE SSERAFIM, KARA, IVE, BLACKPINK, (G)-IDLE]
nulls last               : [(G)-IDLE, BLACKPINK, IVE, KARA, LE SSERAFIM, Red Velvet, aespa, null, null, null]
nulls last  (Reverse)    : [aespa, Red Velvet, LE SSERAFIM, KARA, IVE, BLACKPINK, (G)-IDLE, null, null, null]

sorted with              : [Liz, Rei, Gaeul, Yujin, Leeseo, Wonyoung]
sorted with (Reverse)    : [Wonyoung, Leeseo, Yujin, Gaeul, Rei, Liz]
```

## 참고 자료
https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/
