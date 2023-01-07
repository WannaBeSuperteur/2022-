## 참고 자료
https://kotlinlang.org/docs/reflection.html

## 코드
```kotlin
data class Member (
    var name: String = "",
    var birth: Int = 0
)

val leader = Member("An Yu Jin", 2003)
val Member.isFearz: Boolean get() = this.birth % 100 == "04".toInt()

fun main() {
    
    // ref : https://kotlinlang.org/docs/reflection.html

    // Callable references > Function references
    fun isNameIncludesN(member: Member) = member.name.contains("n") || member.name.contains("N")
    fun isOddYear(member: Member) = member.birth % 2 == 1
	
    val members = listOf(
        Member("Yujin", 2003), Member("Gaeul", 2002), Member("Rei", 2004),
        Member("Wonyoung", 2004), Member("Liz", 2004), Member("Leeseo", 2007)
    )
    
    println("0. " + members.filter(::isNameIncludesN))
    println("1. " + members.filter(::isOddYear))
    
    // Callable references > Function references > Example : Function composition
    fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
        return { x -> f(g(x)) }
    }
    
    fun isIncludeN(s: String) = s.contains("n") || s.contains("N")
    fun getName(member: Member) = member.name
    
    fun isOdd(n: Int) = n % 2 == 1
    fun getYear(member: Member) = member.birth
    
    val isNameIncludesNCompose = compose(::isIncludeN, ::getName)
    val isOddYearCompose = compose(::isOdd, ::getYear)
    
    println("\n2. " + members.filter(isNameIncludesNCompose))
    println("3. " + members.filter(isOddYearCompose))
    
    // Callable references > Property references
    println("\n4. " + ::leader.get())
    println("5. " + ::leader.name) // leader (NOT 'An Yu Jin')
    println("6. " + ::leader.get().name)
    println("7. " + ::leader.get().birth)
    
    println("\n8. " + members.map { it.name }.map(String::length))
    
    val propName = Member::name
    val propBirth = Member::birth
    println("\n9. " + propName.get(Member("LIZ", 2004)))
    println("10. " + propBirth.get(Member("LIZ", 2004)))
    println("11. (내가 느끼는 이 감정들은) " + Member::isFearz.get(Member("LIZ", 2004)))
    
    // Callable references > Bound function and property references
    val ternaryRegex = "[012]+".toRegex()
    println("\n12. (211201) " + ternaryRegex.matches("211201"))
    println("13. (20211201) " + ternaryRegex.matches("20211201"))
    println("14. (220405) " + ternaryRegex.matches("220405"))
    println("15. (220822) " + ternaryRegex.matches("220822"))
    
    val isTernary = ternaryRegex::matches
    println("\n16. (211201) " + isTernary("211201"))
    println("17. (20211201) " + isTernary("20211201"))
    println("18. (220405) " + isTernary("220405"))
    println("19. (220822) " + isTernary("220822"))
    
    val strings = listOf("0901", "0924", "0203", "0831", "1121", "0221")
    println("\n20. " + strings.filter(ternaryRegex::matches))
    println("21. " + strings.filterNot(ternaryRegex::matches))
    println("22. " + strings.filter(isTernary))
    println("23. " + strings.filterNot(isTernary))
}
```

## 실행 결과
```kotlin
0. [Member(name=Yujin, birth=2003), Member(name=Wonyoung, birth=2004)]
1. [Member(name=Yujin, birth=2003), Member(name=Leeseo, birth=2007)]

2. [Member(name=Yujin, birth=2003), Member(name=Wonyoung, birth=2004)]
3. [Member(name=Yujin, birth=2003), Member(name=Leeseo, birth=2007)]

4. Member(name=An Yu Jin, birth=2003)
5. leader
6. An Yu Jin
7. 2003

8. [5, 5, 3, 8, 3, 6]

9. LIZ
10. 2004
11. (내가 느끼는 이 감정들은) true

12. (211201) true
13. (20211201) true
14. (220405) false
15. (220822) false

16. (211201) true
17. (20211201) true
18. (220405) false
19. (220822) false

20. [1121, 0221]
21. [0901, 0924, 0203, 0831]
22. [1121, 0221]
23. [0901, 0924, 0203, 0831]
```
