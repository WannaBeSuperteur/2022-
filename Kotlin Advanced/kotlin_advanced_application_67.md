## 요구사항 분석
어느 검색 포털 사이트에서는 다음과 같은 기준을 이용하여 블로그 포스팅의 노출 순위를 결정한다.

* ```K``` : 제목 키워드 점수
  * 제목에 키워드를 구성하는 각 단어(``` ```로 구분)가 그 순서대로 정확히 일치하는 것이 있으면, 해당 부분의 제목에서의 시작 인덱스 ```i```에 따른 ```W(i)``` 값을 이용한다.
  * 그렇지 않으면, 키워드를 구성하는 각 단어별로, 해당 단어의 제목에서의 가장 이른 시작 인덱스 ```i```에 따른 ```W(i)``` 값에 ```1 / (해당 키워드 단어의 개수)```를 곱한 값을 더한다.
  * ```W(i)```는 다음과 같다.
    * ```i < 20```인 경우 : ```200 / (i + 20)```을 가장 가까운 정수로 내림한 값
    * ```i >= 20```인 경우 : ```5```
* ```L``` : 제목의 길이 점수
  * 제목이 20글자 이내인 경우 ```100```, 20글자 이상 30글자 미만의 N글자인 경우 ```100 - 5 * (N - 20)```, 30글자 이상인 경우 ```50```이다.
* ```B``` : 블로그 점수
  * 초기값을 ```100```으로 하고, 다음과 같이 감점해 나간다.
    * 제목에 특정 단어가 2번 이상 반복될 때, 해당하는 각각의 반복되는 단어들에 대해 ```반복 횟수 * 300 / (20 + 해당 단어의 제목에서의 첫 등장 인덱스)```만큼 차감한다.
  * 계산된 값이 ```20```에 이르면 더 이상 점수를 차감하지 않고 그 값을 ```20```으로 한다.

최종 결과값은 ```K * L * B```이다.

### 데이터 클래스
데이터 클래스는 다음과 같다.
```kotlin
// 검색 결과
data class SearchResult (
    val keyword: String, // 키워드
    val posts: List<Post> // 검색 결과 블로그 포스팅의 목록
)

// 블로그 포스팅
data class Post (
    val blog_title: String, // 블로그 이름
    val title: String, // 제목
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 검색 결과
data class SearchResult (
    val keyword: String, // 키워드
    val posts: List<Post> // 검색 결과 블로그 포스팅의 목록
)

// 블로그 포스팅
data class Post (
    val blog_title: String, // 블로그 이름
    val title: String, // 제목
)

// 인덱스별 W의 값 구하기
fun getW(idx: Int): Int {
    return if (idx < 20) { 200 / (idx + 20) } else { 5 }
}

// 해당 키워드가 정확히 일치하는 최초의 인덱스 찾기
fun findFirstIndex(title: String, keyword: String): Int {
    val result = (0 until title.length - keyword.length + 1).indexOfFirst {
        title.substring(it, it + keyword.length) == keyword
    }
    return if (result == null) { -1 } else { result }
}

// 텍스트를 모두 소문자로 + 알파벳과 공백만 남기기
fun handleText(text: String): String {
    val lowered = text.toLowerCase()
    return Regex("[^a-z ]").replace(lowered, "").trim()
}

// 각 블로그의 블로그 점수 계산
fun findBlogScores(posts: List<Post>): Map<String, Int> {
    val blogScores = mutableMapOf<String, Int>()
    
    posts.forEach {
        val blogTitle = it.blog_title
        val title = it.title
        val modifiedTitle = handleText(it.title) // 반복 키워드 판정용
        val titleSplit = modifiedTitle.split(" ")
        
        // 고유 항목별 개수를 이용하기 위하여 groupBy -> filter -> map 을 이용
        val repeatedWords = titleSplit.groupBy { it }.filter {
            it.value.size >= 2
        }.map {
            listOf(it.key, it.value.size)
        }
        
        // 해당 포스트에 대한 감점
        val minusScore = repeatedWords.sumOf {
            val firstIndex = findFirstIndex(title.toLowerCase(), it[0] as String)
            val repeatCount = it[1] as Int
            val result = repeatCount * 300 / (20 + firstIndex)
            
            println("2. blog = ${blogTitle}, firstIndex = ${firstIndex}, repeated words = ${repeatedWords}, score = ${result}")
            result
        }
        
        if (blogScores.containsKey(blogTitle)) {
            blogScores.put(blogTitle, Math.max(20, blogScores.get(blogTitle)!! - minusScore))
        } else {
            blogScores.put(blogTitle, Math.max(20, 100 - minusScore))
        }
    }
    
    println("3. final blog scores = ${blogScores}\n")
    return blogScores
}

// 검색 노출 점수 및 순위 출력
fun rankPosts(searchResult: SearchResult) {
    val keyword = handleText(searchResult.keyword)
    val words = keyword.split(" ")
    println("1. words = ${words}\n")
    
    // 블로그 점수 계산
    val blogScores = findBlogScores(searchResult.posts)
    
    // 검색 순위 점수 계산
    val postsWithRank = searchResult.posts.map {
        
        // 포스팅 제목을 키워드에 맞게 소문자로 수정
        val modifiedTitle = it.title.toLowerCase()
        println("4. title = [${modifiedTitle}], keyword = [${keyword}]")
        
        // K : 제목 키워드 점수
        val firstIndex = findFirstIndex(modifiedTitle, keyword)
        val firstIndices = mutableListOf<Int>() // 출력용
        
        val K = if (firstIndex >= 0) {
            getW(firstIndex)
        } else {
            val valueOfK = words.sumOf { w ->
                val firstIndexOfWord = findFirstIndex(modifiedTitle, w)
                val weight = getW(firstIndexOfWord)
                firstIndices.add(firstIndexOfWord)
                
                val result = if (firstIndexOfWord < 0) { // 키워드 없음 -> indexOfFirst = -1
                    0
                } else {
                    getW(firstIndexOfWord) / words.size 
                }
                
                println("5. word = ${w}, first index = ${firstIndexOfWord}, weight = ${weight}, score = ${result}")
                result
            }
            valueOfK
        }
        println("\n6. K = ${K}, first keyword index = ${firstIndex}, first word indices = ${firstIndices}")
        
        // L : 제목의 길이 점수
        val L = when (modifiedTitle.length) {
            in 0..20 -> 100
            in 21..29 -> 100 - 5 * (modifiedTitle.length - 20)
            else -> 50
        }
        println("7. L = ${L}, length of modified title = ${modifiedTitle.length}")
        
        // B : 블로그 점수
        val B = blogScores.get(it.blog_title)!!
        println("8. B = ${B}, blog = ${it.blog_title}")
        
        val finalScore = K * L * B
        println("9. final K * L * B = ${finalScore}\n")
        
        listOf(it, finalScore)
    }
    
    println(postsWithRank)
    
    // 검색 순위 점수에 따라 정렬 및 출력
    val postsWithRankSorted = postsWithRank.sortedByDescending { it[1] as Int }
    
    println("\nFINAL RANKING :")
    postsWithRankSorted.forEachIndexed { idx, it ->
        val thisPost = it[0] as Post
        println("${idx + 1} (score: ${it[1]}): ${thisPost.title} (by ${thisPost.blog_title})")
    }
}

fun main() {
    
    // 임시 검색 결과
    val tempSearchResult = SearchResult(
        "Kotlin tip",
        listOf<Post> (
            Post("Kotlin Pro", "Kotlin is a very good language"),
            Post("Kotlin Pro", "5 Kotlin tips you must know"),
            Post("Kotlin Pro", "Very good tips for Kotlin"),
            Post("Kotlin Pro", "Useful Kotlin tip collection is here!"),
            Post("Kotlin Pro", "Kotlin tip #1"),
            Post("Kotlin Pro", "Kotlin tip (array)"),
            Post("Kotliner", "[Kotlin Tip] Kotlin? What's that?"),
            Post("Kotliner", "[Kotlin Tip] We can be Kotlin master!!"),
            Post("Kotlin Pro", "Kotlin tip (how to handle string)"),
            Post("Kotlin Pro", "Kotlin with good tips"),
            Post("Kotlin Pro", "Tips for Java"),
            Post("Kotlin Pro", "Tips for being a good programmer"),
            Post("Kotlin Pro", "Good tips that help you using Kotlin better"),
            Post("Kotliner", "[Kotlin Tip] how to make an array"),
            Post("Kotliner", "[Kotlin Tip] 3 useful Kotlin tips"),
            Post("Kotliner", "[Kotlin Tip] Kotlin, Kotlin, Kotlin??"),
            Post("Kotlin King", "[Kotlin] How to make Kotlin list"),
            Post("Kotlin King", "[Kotlin] Tips for programming using Kotlin")
        )
    )
    
    // 검색 노출 점수 및 순위 출력
    rankPosts(tempSearchResult)
}
```

### 실행 결과
```kotlin
1. words = [kotlin, tip]

2. blog = Kotliner, firstIndex = 1, repeated words = [[kotlin, 2]], score = 28
2. blog = Kotliner, firstIndex = 1, repeated words = [[kotlin, 2]], score = 28
2. blog = Kotliner, firstIndex = 1, repeated words = [[kotlin, 2]], score = 28
2. blog = Kotliner, firstIndex = 1, repeated words = [[kotlin, 4]], score = 57
2. blog = Kotlin King, firstIndex = 1, repeated words = [[kotlin, 2]], score = 28
2. blog = Kotlin King, firstIndex = 1, repeated words = [[kotlin, 2]], score = 28
3. final blog scores = {Kotlin Pro=100, Kotliner=20, Kotlin King=44}

4. title = [kotlin is a very good language], keyword = [kotlin tip]
5. word = kotlin, first index = 0, weight = 10, score = 5
5. word = tip, first index = -1, weight = 10, score = 0

6. K = 5, first keyword index = -1, first word indices = [0, -1]
7. L = 50, length of modified title = 30
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 25000

4. title = [5 kotlin tips you must know], keyword = [kotlin tip]

6. K = 9, first keyword index = 2, first word indices = []
7. L = 65, length of modified title = 27
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 58500

4. title = [very good tips for kotlin], keyword = [kotlin tip]
5. word = kotlin, first index = 19, weight = 5, score = 2
5. word = tip, first index = 10, weight = 6, score = 3

6. K = 5, first keyword index = -1, first word indices = [19, 10]
7. L = 75, length of modified title = 25
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 37500

4. title = [useful kotlin tip collection is here!], keyword = [kotlin tip]

6. K = 7, first keyword index = 7, first word indices = []
7. L = 50, length of modified title = 37
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 35000

4. title = [kotlin tip #1], keyword = [kotlin tip]

6. K = 10, first keyword index = 0, first word indices = []
7. L = 100, length of modified title = 13
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 100000

4. title = [kotlin tip (array)], keyword = [kotlin tip]

6. K = 10, first keyword index = 0, first word indices = []
7. L = 100, length of modified title = 18
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 100000

4. title = [[kotlin tip] kotlin? what's that?], keyword = [kotlin tip]

6. K = 9, first keyword index = 1, first word indices = []
7. L = 50, length of modified title = 33
8. B = 20, blog = Kotliner
9. final K * L * B = 9000

4. title = [[kotlin tip] we can be kotlin master!!], keyword = [kotlin tip]

6. K = 9, first keyword index = 1, first word indices = []
7. L = 50, length of modified title = 38
8. B = 20, blog = Kotliner
9. final K * L * B = 9000

4. title = [kotlin tip (how to handle string)], keyword = [kotlin tip]

6. K = 10, first keyword index = 0, first word indices = []
7. L = 50, length of modified title = 33
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 50000

4. title = [kotlin with good tips], keyword = [kotlin tip]
5. word = kotlin, first index = 0, weight = 10, score = 5
5. word = tip, first index = 17, weight = 5, score = 2

6. K = 7, first keyword index = -1, first word indices = [0, 17]
7. L = 95, length of modified title = 21
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 66500

4. title = [tips for java], keyword = [kotlin tip]
5. word = kotlin, first index = -1, weight = 10, score = 0
5. word = tip, first index = 0, weight = 10, score = 5

6. K = 5, first keyword index = -1, first word indices = [-1, 0]
7. L = 100, length of modified title = 13
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 50000

4. title = [tips for being a good programmer], keyword = [kotlin tip]
5. word = kotlin, first index = -1, weight = 10, score = 0
5. word = tip, first index = 0, weight = 10, score = 5

6. K = 5, first keyword index = -1, first word indices = [-1, 0]
7. L = 50, length of modified title = 32
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 25000

4. title = [good tips that help you using kotlin better], keyword = [kotlin tip]
5. word = kotlin, first index = 30, weight = 5, score = 2
5. word = tip, first index = 5, weight = 8, score = 4

6. K = 6, first keyword index = -1, first word indices = [30, 5]
7. L = 50, length of modified title = 43
8. B = 100, blog = Kotlin Pro
9. final K * L * B = 30000

4. title = [[kotlin tip] how to make an array], keyword = [kotlin tip]

6. K = 9, first keyword index = 1, first word indices = []
7. L = 50, length of modified title = 33
8. B = 20, blog = Kotliner
9. final K * L * B = 9000

4. title = [[kotlin tip] 3 useful kotlin tips], keyword = [kotlin tip]

6. K = 9, first keyword index = 1, first word indices = []
7. L = 50, length of modified title = 33
8. B = 20, blog = Kotliner
9. final K * L * B = 9000

4. title = [[kotlin tip] kotlin, kotlin, kotlin??], keyword = [kotlin tip]

6. K = 9, first keyword index = 1, first word indices = []
7. L = 50, length of modified title = 37
8. B = 20, blog = Kotliner
9. final K * L * B = 9000

4. title = [[kotlin] how to make kotlin list], keyword = [kotlin tip]
5. word = kotlin, first index = 1, weight = 9, score = 4
5. word = tip, first index = -1, weight = 10, score = 0

6. K = 4, first keyword index = -1, first word indices = [1, -1]
7. L = 50, length of modified title = 32
8. B = 44, blog = Kotlin King
9. final K * L * B = 8800

4. title = [[kotlin] tips for programming using kotlin], keyword = [kotlin tip]
5. word = kotlin, first index = 1, weight = 9, score = 4
5. word = tip, first index = 9, weight = 6, score = 3

6. K = 7, first keyword index = -1, first word indices = [1, 9]
7. L = 50, length of modified title = 42
8. B = 44, blog = Kotlin King
9. final K * L * B = 15400

[[Post(blog_title=Kotlin Pro, title=Kotlin is a very good language), 25000], [Post(blog_title=Kotlin Pro, title=5 Kotlin tips you must know), 58500], [Post(blog_title=Kotlin Pro, title=Very good tips for Kotlin), 37500], [Post(blog_title=Kotlin Pro, title=Useful Kotlin tip collection is here!), 35000], [Post(blog_title=Kotlin Pro, title=Kotlin tip #1), 100000], [Post(blog_title=Kotlin Pro, title=Kotlin tip (array)), 100000], [Post(blog_title=Kotliner, title=[Kotlin Tip] Kotlin? What's that?), 9000], [Post(blog_title=Kotliner, title=[Kotlin Tip] We can be Kotlin master!!), 9000], [Post(blog_title=Kotlin Pro, title=Kotlin tip (how to handle string)), 50000], [Post(blog_title=Kotlin Pro, title=Kotlin with good tips), 66500], [Post(blog_title=Kotlin Pro, title=Tips for Java), 50000], [Post(blog_title=Kotlin Pro, title=Tips for being a good programmer), 25000], [Post(blog_title=Kotlin Pro, title=Good tips that help you using Kotlin better), 30000], [Post(blog_title=Kotliner, title=[Kotlin Tip] how to make an array), 9000], [Post(blog_title=Kotliner, title=[Kotlin Tip] 3 useful Kotlin tips), 9000], [Post(blog_title=Kotliner, title=[Kotlin Tip] Kotlin, Kotlin, Kotlin??), 9000], [Post(blog_title=Kotlin King, title=[Kotlin] How to make Kotlin list), 8800], [Post(blog_title=Kotlin King, title=[Kotlin] Tips for programming using Kotlin), 15400]]

FINAL RANKING :
1 (score: 100000): Kotlin tip #1 (by Kotlin Pro)
2 (score: 100000): Kotlin tip (array) (by Kotlin Pro)
3 (score: 66500): Kotlin with good tips (by Kotlin Pro)
4 (score: 58500): 5 Kotlin tips you must know (by Kotlin Pro)
5 (score: 50000): Kotlin tip (how to handle string) (by Kotlin Pro)
6 (score: 50000): Tips for Java (by Kotlin Pro)
7 (score: 37500): Very good tips for Kotlin (by Kotlin Pro)
8 (score: 35000): Useful Kotlin tip collection is here! (by Kotlin Pro)
9 (score: 30000): Good tips that help you using Kotlin better (by Kotlin Pro)
10 (score: 25000): Kotlin is a very good language (by Kotlin Pro)
11 (score: 25000): Tips for being a good programmer (by Kotlin Pro)
12 (score: 15400): [Kotlin] Tips for programming using Kotlin (by Kotlin King)
13 (score: 9000): [Kotlin Tip] Kotlin? What's that? (by Kotliner)
14 (score: 9000): [Kotlin Tip] We can be Kotlin master!! (by Kotliner)
15 (score: 9000): [Kotlin Tip] how to make an array (by Kotliner)
16 (score: 9000): [Kotlin Tip] 3 useful Kotlin tips (by Kotliner)
17 (score: 9000): [Kotlin Tip] Kotlin, Kotlin, Kotlin?? (by Kotliner)
18 (score: 8800): [Kotlin] How to make Kotlin list (by Kotlin King)
```
