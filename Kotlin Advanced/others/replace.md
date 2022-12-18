## 코드
```kotlin
fun main() {
    val test = "sentence entertainment"
    
	// replace
	val test00 = test.replace("en", "EN")
    val test01 = test.replace(Regex("[a-z]n"), "EN")
	println("replace           : ${test00}")
    println("replace           : ${test01}")
    
    // replaceAfter, replaceAfterLast, replaceBefore, replaceBeforeLast
    val test02 = test.replaceAfter("en", "[like]")
    val test03 = test.replaceAfterLast("en", "[like]")
    val test04 = test.replaceBefore("en", "[love]")
    val test05 = test.replaceBeforeLast("en", "[love]")
    
    println("\nreplaceAfter      : ${test02}")
    println("replaceAfterLast  : ${test03}")
    println("replaceBefore     : ${test04}")
    println("replaceBeforeLast : ${test05}")
    
    // replaceFirst
    val new_str = "Internet Login Connection Failed."
    val test06 = new_str.replaceFirst("in", "**")
    val test07 = new_str.replaceFirst(Regex("[a-z]n"), "**")
    val test08 = new_str.replaceFirst("in", "**", ignoreCase=true)
	
    println("\nreplaceFirst      : ${test06}")
    println("replaceFirst      : ${test07}")
    println("replaceFirst      : ${test08}")
    
    // replaceFirstChar
    val charToAnotherChar: ((Char) -> Char) = {
        if ((it >= 'a' && it <= 'c') || (it >= 'A' && it <= 'C')) {
            it + 23
        } else {
            it - 3
        }
    }
    
    val test09 = "Android".replaceFirstChar { charToAnotherChar(it) }
    val test10 = "Java".replaceFirstChar { charToAnotherChar(it) }
    val test11 = "Kotlin".replaceFirstChar { charToAnotherChar(it) }
    val test12 = "android".replaceFirstChar { charToAnotherChar(it) }
    val test13 = "java".replaceFirstChar { charToAnotherChar(it) }
    val test14 = "kotlin".replaceFirstChar { charToAnotherChar(it) }
    
    println("\nreplaceFirstChar  : ${test09}")
    println("replaceFirstChar  : ${test10}")
    println("replaceFirstChar  : ${test11}")
    println("replaceFirstChar  : ${test12}")
    println("replaceFirstChar  : ${test13}")
    println("replaceFirstChar  : ${test14}")
    
    // replaceIndent and replaceIndentWithMargin
    val indentString1 = "        indent"
    val indentString2 = "++++indent"
    val test15 = indentString1.replaceIndent("-=-")
    val test16 = indentString2.replaceIndentByMargin(">--_--<", "++++")
    println("\nreplaceIndent     : ${test15}")
    println("replaceIndent     : ${test16}")
    
    // replaceRange
    val test17 = test.replaceRange(3, 7, "[ forbidden ]")
    val test18 = new_str.replaceRange(9, 14, "Authentication")
    val test19 = new_str.replaceRange(0, 8, "Authentication")
    println("\nreplaceRange      : ${test17}")
    println("replaceRange      : ${test18}")
    println("replaceRange      : ${test19}")
}
```

## 실행 결과
```kotlin
replace           : sENtENce ENtertainmENt
replace           : sENtENce ENtertaENmENt

replaceAfter      : sen[like]
replaceAfterLast  : sentence entertainmen[like]
replaceBefore     : [love]entence entertainment
replaceBeforeLast : [love]ent

replaceFirst      : Internet Log** Connection Failed.
replaceFirst      : Inte**et Login Connection Failed.
replaceFirst      : **ternet Login Connection Failed.

replaceFirstChar  : Xndroid
replaceFirstChar  : Gava
replaceFirstChar  : Hotlin
replaceFirstChar  : xndroid
replaceFirstChar  : gava
replaceFirstChar  : hotlin

replaceIndent     : -=-indent
replaceIndent     : >--_--<indent

replaceRange      : sen[ forbidden ]e entertainment
replaceRange      : Internet Authentication Connection Failed.
replaceRange      : Authentication Login Connection Failed.
```
