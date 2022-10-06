## 요구사항 분석
워너비슈퍼추어소프트에서는 명령 프롬프트와 같은 형식으로 명령어를 입력하면 이에 알맞은 파일의 목록을 보여주는 소프트웨어를 개발하려고 한다.

파일에 대한 정보는 다음과 같다.
* 확장자를 포함한 이름 (예: ```kotlin_example.txt```)
* 확장자에 따른 유형
  * ```*.txt``` : ```텍스트 문서```
  * ```*.kt``` : ```코틀린 파일```
  * ```*.xlsx``` : ```엑셀 파일```
  * ```*.docx``` : ```워드 파일```
  * ```*.pptx``` : ```파워포인트 파일```
  * 위에 나오지 않은 확장자의 경우, 확장자를 ```abc```라고 하면 이것을 대문자로 하여 ```ABC 파일```과 같이 표시한다.
* 수정한 날짜 (예: ```2022-10-01 13:45:24```)
* 바이트 단위의 파일 크기 (예: ```10240```)
* 숨김 파일 여부 (```Y```: 예, ```N```: 아니오)
* 작성자 (예: ```WannaBeSuperteur```)

명령어 형식은 다음과 같다.
* ```ls``` : 모든 파일에 대해, 파일의 확장자를 포함한 이름, 확장자에 따른 유형, 수정한 날짜, 바이트 단위의 파일 크기, 숨김 파일 여부, 작성자를 차례로 보여준다.
* ```lsn (name)``` : 이름이 ```(name)```을 포함하는 모든 파일에 대해 ```ls```와 같이 한다.
* ```lse (extension)``` : 확장자가 ```(extension)```인 모든 파일에 대해 ```ls```와 같이 한다.
* ```lsm (date0) (date1)``` : 수정한 날짜가 ```(date0)``` 이후 ```(date1)``` 이전(양 끝 포함)인 모든 파일에 대해 ```ls```와 같이 한다.
  * ```(date0)```과 ```(date1)```의 형식은 ```2022-10-01```과 같다.
  * ```(date0)```의 값이 ```*```이면 수정한 날짜가 ```(date1)``` 이전인 모든 파일에 대해 ```ls```와 같이 한다.
  * ```(date1)```의 값이 ```*```이면 수정한 날짜가 ```(date0)``` 이후인 모든 파일에 대해 ```ls```와 같이 한다.
* ```lss (size0) (size1)``` : 바이트 단위의 파일 크기가 ```(size0)``` 이상 ```(size1)``` 이하인 모든 파일에 대해 ```ls```와 같이 한다.
  * ```lss (size0)``` : 바이트 단위의 파일 크기가 ```(size0)``` 이상인 모든 파일에 대해 ```ls```와 같이 한다.
* ```lsh Y```, ```lsh N``` : 숨김 파일 여부가 각각 '예', '아니오'인 모든 파일에 대해 ```ls```와 같이 한다.
* ```lsc (creator)``` : 작성자의 이름이 ```(creator)```인 모든 파일에 대해 ```ls```와 같이 한다.
* ```lsci (creator)``` : 작성자의 이름이 ```(creator)```를 포함하는 모든 파일에 대해 ```ls```와 같이 한다.
* ```ls```를 제외하고, 위 명령어 여러 개를 조합하여 여러 조건을 모두 만족하는 파일의 목록을 출력할 수 있다.
  * 예를 들어, ```lsn (name) lse (extension) lss (size0) (size1)```은 이름이 ```(name)```을 포함하고, 확장자가 ```(extension)```이면서, 동시에 파일 크기가 ```(size0)```바이트 이상 ```(size1)```바이트 이하인 모든 파일에 대해 ```ls```와 같이 한다.
* ```ls```를 제외하고, 각 명령어 사이에 ```+``` 기호를 넣어서 각 명령어의 조건을 만족시키는 파일의 합집합을 출력할 수 있다.
  * 예를 들어, ```lsn (name) + lse (extension) + lss (size0) (size1)```은 이름이 ```(name)```을 포함하거나, 확장자가 ```(extension)```이거나, 또는 파일 크기가 ```(size0)```바이트 이상 ```(size1)```바이트 이하인 모든 파일에 대해 ```ls```와 같이 한다.
* ```ls```를 제외하고, 위 두 방법을 조합해서 사용할 수 있다. 단, ```+``` 기호는 명령어 조합보다 나중에 처리한다.
  * 예를 들어, ```lsn (name) lse (extension) + lss (size0) (size1)```은 이름이 ```(name)```을 포함하면서 확장자가 ```(extension)```이거나, 파일 크기가 ```(size0)```바이트 이상 ```(size1)```바이트 이하인 모든 파일에 대해 ```ls```와 같이 한다.

파일에 대한 정보가 아래의 데이터 클래스 형식과 같을 때, 위의 명령어를 처리하는 프로그램을 작성하시오. 파일 이름, 확장자, 작성자 이름은 모두 숫자, 한글, 알파벳 또는 ```_```로만 구성되어 있다.

### 데이터 클래스
```kotlin
// 파일 정보
data class File (
    val name: String, // 확장자를 포함한 파일 이름
    val modified_date: String, // 수정한 날짜
    val size: Int, // 파일 크기
    val hidden: Boolean, // 숨김 파일 여부 (예: true, 아니오: false)
    val creator: String // 작성자 이름
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 파일 정보
data class File (
    val name: String, // 확장자를 포함한 파일 이름
    val modified_date: String, // 수정한 날짜
    val size: Int, // 파일 크기
    val hidden: Boolean, // 숨김 파일 여부 (예: true, 아니오: false)
    val creator: String // 작성자 이름
)

// lsn (name) : 이름이 (name)을 포함하는 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByName(files: List<File>, name: String): Set<Int> {
    val indices = mutableListOf<Int>()
    
    files.forEachIndexed { idx, it ->
        if (it.name.contains(name)) {
            indices.add(idx)
        }
    }
    
    return indices.toSet()
}

// lse (extension) : 확장자가 (extension)인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByExtension(files: List<File>, extension: String): Set<Int> {
    val indices = mutableListOf<Int>()
    
    files.forEachIndexed { idx, it ->
        if (it.name.substringAfterLast(".") == extension) {
            indices.add(idx)
        }
    }
    
    return indices.toSet()
}

// lsm (date0) (date1) : 수정한 날짜가 (date0) 이후 (date1) 이전(양 끝 포함)인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByDate(files: List<File>, date0: String, date1: String): Set<Int> {
    val indices = mutableListOf<Int>()
    
    val newDate0 = if (date0 == "*") { "0000-01-01" } else { date0 }
    val newDate1 = if (date1 == "*") { "9999-12-31" } else { date1 }
    
    files.forEachIndexed { idx, it ->
        val compare0 = it.modified_date.substringBefore(" ").compareTo(newDate0)
        val compare1 = it.modified_date.substringBefore(" ").compareTo(newDate1)
        
        if (compare0 >= 0 && compare1 <= 0) {
            indices.add(idx)
        }
    }
    
    return indices.toSet()
}

// lss (size0) (size1) : 바이트 단위의 파일 크기가 (size0) 이상 (size1) 이하인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesBySize(files: List<File>, size0: Int, size1: Int? = null): Set<Int> {
    val indices = mutableListOf<Int>()
    
    files.forEachIndexed { idx, it ->
        if (size1 != null && it.size >= size0 && it.size <= size1) {
            indices.add(idx)
        } else if (size1 == null && it.size >= size0) {
            indices.add(idx)
        }
    }
    
    return indices.toSet()
}

// lsh Y, lsh N : 숨김 파일 여부가 각각 '예', '아니오'인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByHidden(files: List<File>, isHidden: Boolean): Set<Int> {
    val indices = mutableListOf<Int>()
    
    files.forEachIndexed { idx, it ->
        if (it.hidden == isHidden) {
            indices.add(idx)
        }
    }
    
    return indices.toSet()
}

// lsc (creator) : 작성자의 이름이 (creator)인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByCreatorName(files: List<File>, creatorName: String): Set<Int> {
    val indices = mutableListOf<Int>()
    
    files.forEachIndexed { idx, it ->
        if (it.creator == creatorName) {
            indices.add(idx)
        }
    }
    
    return indices.toSet()
}

// lsci (creator) : 작성자의 이름이 (creator)를 포함하는 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByIncludingCreatorName(files: List<File>, creatorName: String): Set<Int> {
    val indices = mutableListOf<Int>()
    
    files.forEachIndexed { idx, it ->
        if (it.creator.contains(creatorName)) {
            indices.add(idx)
        }
    }
    
    return indices.toSet()
}

// 명령어 조합에 맞는 파일의 인덱스 구하기
fun findIntersectionIndices(files: List<File>, command: String): Set<Int> {
    
    // 명령어에 해당하는 파일의 인덱스의 교집합 구하기
    var idxIntersect = (0..files.size - 1).toMutableSet()
    
    val commandSplit = command.split(" ")
    commandSplit.forEachIndexed { idx, part ->
        
        // 함수를 이용하여 인덱스 찾기
        val indices = when (part) {
            "lsn" -> {
                findIndicesByName(files, commandSplit[idx + 1])
            }
            "lse" -> {
                findIndicesByExtension(files, commandSplit[idx + 1])
            }
            "lsm" -> {
                findIndicesByDate(files, commandSplit[idx + 1], commandSplit[idx + 2])
            }
            "lss" -> {
                if (commandSplit.size <= idx + 2 || commandSplit[idx + 2].contains("ls")) {
                    findIndicesBySize(files, commandSplit[idx + 1].toInt())
                } else {
                    findIndicesBySize(files, commandSplit[idx + 1].toInt(), commandSplit[idx + 2].toInt())
                }
            }
            "lsh" -> {
                findIndicesByHidden(files, commandSplit[idx + 1] == "Y")
            }
            "lsc" -> {
                findIndicesByCreatorName(files, commandSplit[idx + 1])
            }
            "lsci" -> {
                findIndicesByIncludingCreatorName(files, commandSplit[idx + 1])
            }
            else -> {
                null
            }
        }
        
        indices?.let {
            idxIntersect = idxIntersect.intersect(indices).toMutableSet()
        }
    }
    
    println("sub-command : ${commandSplit}, indices : ${idxIntersect}")
    return idxIntersect
}

// 파일 정보 출력
fun printFileInfo(f: File) {
    val typeMap = mapOf(
        "txt" to "텍스트 문서",
        "kt" to "코틀린 파일",
        "xlsx" to "엑셀 파일",
        "docx" to "워드 파일",
        "pptx" to "파워포인트 파일"
    )

    val extension = f.name.substringAfterLast(".")
    val typeStr = typeMap.getOrElse(extension) {
        "${extension.toUpperCase()} 파일"
    }
    val isHidden = if (f.hidden) { "숨김" } else { "숨기지 않음" }
    
    println("${f.name} / ${typeStr} / ${f.modified_date} / ${f.size} / ${isHidden} / ${f.creator}")
}

// 명령어 처리 통합 함수
fun handleCommand(files: List<File>, command: String) {
    
    lateinit var idxSorted: List<Int>
    
    // 명령어가 ls이면 모든 파일의 정보 출력
    if (command == "ls") {
        println("\n <<< command : ls >>>\n")
        idxSorted = (0..files.size - 1).toList()
    }
    else {
        // 명령어에 해당하는 파일의 인덱스의 합집합 구하기
        var idxUnion = mutableSetOf<Int>()
    
        val commandSplit = command.split(" + ")
        println("\n <<< command : ${commandSplit} >>>\n")
        commandSplit.forEach {
            idxUnion = idxUnion.union(findIntersectionIndices(files, it)).toMutableSet()
        }
        
        // 찾은 인덱스를 정렬
        idxSorted = idxUnion.toList().sorted()
        println("command indices (sorted) : ${idxSorted}\n")
    }
    
    // 파일 정보 출력
    println("File List :")
    println("이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자")
    println("-".repeat(104))
    idxSorted.forEach {
        printFileInfo(files[it])
    }
}

fun main() {
    
    // 임시 파일 목록
    val files = listOf<File> (
        File("kotlin_study.txt", "2022-10-03 10:08:29", 7665, false, "hong"),
        File("java_study.txt", "2022-10-04 13:22:10", 3045, false, "hong_sub"),
        File("python_study.txt", "2022-10-05 19:20:41", 9021, false, "hong_sub"),
        File("memo.txt", "2022-10-01 09:28:46", 37, true, "hong"),
        File("test_result.xlsx", "2022-10-03 10:08:29", 283172, false, "muk"),
        File("example.docx", "2022-10-03 10:08:29", 19650, false, "muk"),
        File("kotlin_study_presentation.pptx", "2022-09-24 23:59:48", 937054, false, "hong"),
        File("final.pptx", "2022-09-26 18:33:12", 859378, false, "hong_second"),
        File("final_2.pptx", "2022-09-26 20:05:27", 1001034, true, "hong_second"),
        File("final_3.pptx", "2022-09-26 20:14:07", 1001116, true, "hong_second"),
        File("real_final.pptx", "2022-09-26 21:13:16", 1048580, true, "hong_second"),
        File("real_real_final.pptx", "2022-09-26 22:15:00", 1049624, false, "hong_second"),
        File("real_real_real_final.pptx", "2022-09-26 23:39:31", 1103024, false, "hong_second"),
        File("ai_project.png", "2022-09-25 12:04:28", 391124, false, "hongmuk2"),
        File("test.kt", "2022-10-05 08:10:22", 445, false, "young"),
        File("ai_project.xtx", "2022-09-25 10:24:59", 18048, false, "hongmuk2")
    )
    
    // 명령어 목록
    val commands = listOf<String> (
        "ls",
        "lsn study",
        "lsn final",
        "lse txt",
        "lsm 2022-09-26 2022-10-03",
        "lsm 2022-09-26 *",
        "lsm * 2022-10-03",
        "lss 307200 1048576",
        "lss 1048576",
        "lsh Y",
        "lsh N",
        "lsc muk",
        "lsci muk",
        "lsc hong_second lsh Y",
        "lsc hong_second + lsh Y",
        "lsn final lss 524288 1048576",
        "lsn final + lss 524288 1048576",
        "lsc hong_second lsh Y + lsci muk",
        "lsc hong_second lsh Y + lsci muk lsn ai_project",
        "lss 1048576 + lsm 2022-10-04 * + lsci hongmuk"
    )
    
    commands.forEach {
        handleCommand(files, it)
    }
}
```

### 실행 결과
```kotlin

 <<< command : ls >>>

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study.txt / 텍스트 문서 / 2022-10-03 10:08:29 / 7665 / 숨기지 않음 / hong
java_study.txt / 텍스트 문서 / 2022-10-04 13:22:10 / 3045 / 숨기지 않음 / hong_sub
python_study.txt / 텍스트 문서 / 2022-10-05 19:20:41 / 9021 / 숨기지 않음 / hong_sub
memo.txt / 텍스트 문서 / 2022-10-01 09:28:46 / 37 / 숨김 / hong
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk
kotlin_study_presentation.pptx / 파워포인트 파일 / 2022-09-24 23:59:48 / 937054 / 숨기지 않음 / hong
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2
test.kt / 코틀린 파일 / 2022-10-05 08:10:22 / 445 / 숨기지 않음 / young
ai_project.xtx / XTX 파일 / 2022-09-25 10:24:59 / 18048 / 숨기지 않음 / hongmuk2

 <<< command : [lsn study] >>>

sub-command : [lsn, study], indices : [0, 1, 2, 6]
command indices (sorted) : [0, 1, 2, 6]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study.txt / 텍스트 문서 / 2022-10-03 10:08:29 / 7665 / 숨기지 않음 / hong
java_study.txt / 텍스트 문서 / 2022-10-04 13:22:10 / 3045 / 숨기지 않음 / hong_sub
python_study.txt / 텍스트 문서 / 2022-10-05 19:20:41 / 9021 / 숨기지 않음 / hong_sub
kotlin_study_presentation.pptx / 파워포인트 파일 / 2022-09-24 23:59:48 / 937054 / 숨기지 않음 / hong

 <<< command : [lsn final] >>>

sub-command : [lsn, final], indices : [7, 8, 9, 10, 11, 12]
command indices (sorted) : [7, 8, 9, 10, 11, 12]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second

 <<< command : [lse txt] >>>

sub-command : [lse, txt], indices : [0, 1, 2, 3]
command indices (sorted) : [0, 1, 2, 3]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study.txt / 텍스트 문서 / 2022-10-03 10:08:29 / 7665 / 숨기지 않음 / hong
java_study.txt / 텍스트 문서 / 2022-10-04 13:22:10 / 3045 / 숨기지 않음 / hong_sub
python_study.txt / 텍스트 문서 / 2022-10-05 19:20:41 / 9021 / 숨기지 않음 / hong_sub
memo.txt / 텍스트 문서 / 2022-10-01 09:28:46 / 37 / 숨김 / hong

 <<< command : [lsm 2022-09-26 2022-10-03] >>>

sub-command : [lsm, 2022-09-26, 2022-10-03], indices : [0, 3, 4, 5, 7, 8, 9, 10, 11, 12]
command indices (sorted) : [0, 3, 4, 5, 7, 8, 9, 10, 11, 12]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study.txt / 텍스트 문서 / 2022-10-03 10:08:29 / 7665 / 숨기지 않음 / hong
memo.txt / 텍스트 문서 / 2022-10-01 09:28:46 / 37 / 숨김 / hong
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second

 <<< command : [lsm 2022-09-26 *] >>>

sub-command : [lsm, 2022-09-26, *], indices : [0, 1, 2, 3, 4, 5, 7, 8, 9, 10, 11, 12, 14]
command indices (sorted) : [0, 1, 2, 3, 4, 5, 7, 8, 9, 10, 11, 12, 14]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study.txt / 텍스트 문서 / 2022-10-03 10:08:29 / 7665 / 숨기지 않음 / hong
java_study.txt / 텍스트 문서 / 2022-10-04 13:22:10 / 3045 / 숨기지 않음 / hong_sub
python_study.txt / 텍스트 문서 / 2022-10-05 19:20:41 / 9021 / 숨기지 않음 / hong_sub
memo.txt / 텍스트 문서 / 2022-10-01 09:28:46 / 37 / 숨김 / hong
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second
test.kt / 코틀린 파일 / 2022-10-05 08:10:22 / 445 / 숨기지 않음 / young

 <<< command : [lsm * 2022-10-03] >>>

sub-command : [lsm, *, 2022-10-03], indices : [0, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 15]
command indices (sorted) : [0, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 15]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study.txt / 텍스트 문서 / 2022-10-03 10:08:29 / 7665 / 숨기지 않음 / hong
memo.txt / 텍스트 문서 / 2022-10-01 09:28:46 / 37 / 숨김 / hong
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk
kotlin_study_presentation.pptx / 파워포인트 파일 / 2022-09-24 23:59:48 / 937054 / 숨기지 않음 / hong
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2
ai_project.xtx / XTX 파일 / 2022-09-25 10:24:59 / 18048 / 숨기지 않음 / hongmuk2

 <<< command : [lss 307200 1048576] >>>

sub-command : [lss, 307200, 1048576], indices : [6, 7, 8, 9, 13]
command indices (sorted) : [6, 7, 8, 9, 13]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study_presentation.pptx / 파워포인트 파일 / 2022-09-24 23:59:48 / 937054 / 숨기지 않음 / hong
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2

 <<< command : [lss 1048576] >>>

sub-command : [lss, 1048576], indices : [10, 11, 12]
command indices (sorted) : [10, 11, 12]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second

 <<< command : [lsh Y] >>>

sub-command : [lsh, Y], indices : [3, 8, 9, 10]
command indices (sorted) : [3, 8, 9, 10]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
memo.txt / 텍스트 문서 / 2022-10-01 09:28:46 / 37 / 숨김 / hong
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second

 <<< command : [lsh N] >>>

sub-command : [lsh, N], indices : [0, 1, 2, 4, 5, 6, 7, 11, 12, 13, 14, 15]
command indices (sorted) : [0, 1, 2, 4, 5, 6, 7, 11, 12, 13, 14, 15]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study.txt / 텍스트 문서 / 2022-10-03 10:08:29 / 7665 / 숨기지 않음 / hong
java_study.txt / 텍스트 문서 / 2022-10-04 13:22:10 / 3045 / 숨기지 않음 / hong_sub
python_study.txt / 텍스트 문서 / 2022-10-05 19:20:41 / 9021 / 숨기지 않음 / hong_sub
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk
kotlin_study_presentation.pptx / 파워포인트 파일 / 2022-09-24 23:59:48 / 937054 / 숨기지 않음 / hong
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2
test.kt / 코틀린 파일 / 2022-10-05 08:10:22 / 445 / 숨기지 않음 / young
ai_project.xtx / XTX 파일 / 2022-09-25 10:24:59 / 18048 / 숨기지 않음 / hongmuk2

 <<< command : [lsc muk] >>>

sub-command : [lsc, muk], indices : [4, 5]
command indices (sorted) : [4, 5]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk

 <<< command : [lsci muk] >>>

sub-command : [lsci, muk], indices : [4, 5, 13, 15]
command indices (sorted) : [4, 5, 13, 15]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2
ai_project.xtx / XTX 파일 / 2022-09-25 10:24:59 / 18048 / 숨기지 않음 / hongmuk2

 <<< command : [lsc hong_second lsh Y] >>>

sub-command : [lsc, hong_second, lsh, Y], indices : [8, 9, 10]
command indices (sorted) : [8, 9, 10]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second

 <<< command : [lsc hong_second, lsh Y] >>>

sub-command : [lsc, hong_second], indices : [7, 8, 9, 10, 11, 12]
sub-command : [lsh, Y], indices : [3, 8, 9, 10]
command indices (sorted) : [3, 7, 8, 9, 10, 11, 12]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
memo.txt / 텍스트 문서 / 2022-10-01 09:28:46 / 37 / 숨김 / hong
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second

 <<< command : [lsn final lss 524288 1048576] >>>

sub-command : [lsn, final, lss, 524288, 1048576], indices : [7, 8, 9]
command indices (sorted) : [7, 8, 9]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second

 <<< command : [lsn final, lss 524288 1048576] >>>

sub-command : [lsn, final], indices : [7, 8, 9, 10, 11, 12]
sub-command : [lss, 524288, 1048576], indices : [6, 7, 8, 9]
command indices (sorted) : [6, 7, 8, 9, 10, 11, 12]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
kotlin_study_presentation.pptx / 파워포인트 파일 / 2022-09-24 23:59:48 / 937054 / 숨기지 않음 / hong
final.pptx / 파워포인트 파일 / 2022-09-26 18:33:12 / 859378 / 숨기지 않음 / hong_second
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second

 <<< command : [lsc hong_second lsh Y, lsci muk] >>>

sub-command : [lsc, hong_second, lsh, Y], indices : [8, 9, 10]
sub-command : [lsci, muk], indices : [4, 5, 13, 15]
command indices (sorted) : [4, 5, 8, 9, 10, 13, 15]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
test_result.xlsx / 엑셀 파일 / 2022-10-03 10:08:29 / 283172 / 숨기지 않음 / muk
example.docx / 워드 파일 / 2022-10-03 10:08:29 / 19650 / 숨기지 않음 / muk
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2
ai_project.xtx / XTX 파일 / 2022-09-25 10:24:59 / 18048 / 숨기지 않음 / hongmuk2

 <<< command : [lsc hong_second lsh Y, lsci muk lsn ai_project] >>>

sub-command : [lsc, hong_second, lsh, Y], indices : [8, 9, 10]
sub-command : [lsci, muk, lsn, ai_project], indices : [13, 15]
command indices (sorted) : [8, 9, 10, 13, 15]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
final_2.pptx / 파워포인트 파일 / 2022-09-26 20:05:27 / 1001034 / 숨김 / hong_second
final_3.pptx / 파워포인트 파일 / 2022-09-26 20:14:07 / 1001116 / 숨김 / hong_second
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2
ai_project.xtx / XTX 파일 / 2022-09-25 10:24:59 / 18048 / 숨기지 않음 / hongmuk2

 <<< command : [lss 1048576, lsm 2022-10-04 *, lsci hongmuk] >>>

sub-command : [lss, 1048576], indices : [10, 11, 12]
sub-command : [lsm, 2022-10-04, *], indices : [1, 2, 14]
sub-command : [lsci, hongmuk], indices : [13, 15]
command indices (sorted) : [1, 2, 10, 11, 12, 13, 14, 15]

File List :
이름 / 형식 / 수정한 날짜 / 크기 / 숨김여부 / 작성자
--------------------------------------------------------------------------------------------------------
java_study.txt / 텍스트 문서 / 2022-10-04 13:22:10 / 3045 / 숨기지 않음 / hong_sub
python_study.txt / 텍스트 문서 / 2022-10-05 19:20:41 / 9021 / 숨기지 않음 / hong_sub
real_final.pptx / 파워포인트 파일 / 2022-09-26 21:13:16 / 1048580 / 숨김 / hong_second
real_real_final.pptx / 파워포인트 파일 / 2022-09-26 22:15:00 / 1049624 / 숨기지 않음 / hong_second
real_real_real_final.pptx / 파워포인트 파일 / 2022-09-26 23:39:31 / 1103024 / 숨기지 않음 / hong_second
ai_project.png / PNG 파일 / 2022-09-25 12:04:28 / 391124 / 숨기지 않음 / hongmuk2
test.kt / 코틀린 파일 / 2022-10-05 08:10:22 / 445 / 숨기지 않음 / young
ai_project.xtx / XTX 파일 / 2022-09-25 10:24:59 / 18048 / 숨기지 않음 / hongmuk2
```

### 참고
함수 코드를 다음과 같이 간결하게 만들 수 있다.
```kotlin
// lsn (name) : 이름이 (name)을 포함하는 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByName(files: List<File>, name: String): Set<Int> {
    return (0 until files.size).filter {
        files[it].name.contains(name)
    }.toSet()
}

// lse (extension) : 확장자가 (extension)인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByExtension(files: List<File>, extension: String): Set<Int> {
    return (0 until files.size).filter {
        files[it].name.substringAfterLast(".") == extension
    }.toSet()
}

// lsm (date0) (date1) : 수정한 날짜가 (date0) 이후 (date1) 이전(양 끝 포함)인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByDate(files: List<File>, date0: String, date1: String): Set<Int> {
    val newDate0 = if (date0 == "*") { "0000-01-01" } else { date0 }
    val newDate1 = if (date1 == "*") { "9999-12-31" } else { date1 }
    
    return (0 until files.size).filter {
        val compare0 = files[it].modified_date.substringBefore(" ").compareTo(newDate0)
        val compare1 = files[it].modified_date.substringBefore(" ").compareTo(newDate1)
        compare0 >= 0 && compare1 <= 0
    }.toSet()
}

// lss (size0) (size1) : 바이트 단위의 파일 크기가 (size0) 이상 (size1) 이하인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesBySize(files: List<File>, size0: Int, size1: Int? = null): Set<Int> {
    return (0 until files.size).filter {
        with (files[it].size) {
            val atLeastAndAtMostCond = size1 != null && this >= size0 && this <= size1
            val atLeastOnlyCond = size1 == null && this >= size0
            atLeastAndAtMostCond || atLeastOnlyCond
        }
    }.toSet()
}

// lsh Y, lsh N : 숨김 파일 여부가 각각 '예', '아니오'인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByHidden(files: List<File>, isHidden: Boolean): Set<Int> {
    return (0 until files.size).filter {
        files[it].hidden == isHidden
    }.toSet()
}

// lsc (creator) : 작성자의 이름이 (creator)인 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByCreatorName(files: List<File>, creatorName: String): Set<Int> {
    return (0 until files.size).filter {
        files[it].creator == creatorName
    }.toSet()
}

// lsci (creator) : 작성자의 이름이 (creator)를 포함하는 모든 파일에 대해 ls와 같이 한다.
fun findIndicesByIncludingCreatorName(files: List<File>, creatorName: String): Set<Int> {
    return (0 until files.size).filter {
        files[it].creator.contains(creatorName)
    }.toSet()
}
```
