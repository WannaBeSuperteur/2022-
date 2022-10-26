## 요구 사항 분석
다음과 같은 형식의 문장에 따라 무방향 간선들로 구성된 트리 자료구조를 구성한다. 트리는 언급된 모든 정점들로 구성된다.
```
A의 이웃 정점에 B, C, D를 추가한다.
```

트리가 완성된 후, 다음과 같은 형식의 질문들에 답하는 코드를 작성하시오.
```
A의 이웃 정점을 나열하면?
B의 이웃 정점은 몇 개인가?
정점 C와 D 사이의 거리는?
```

* 트리 자료구조를 구성하는 함수는 ```createTree(sentences: List<String>)```이다.
* 완성된 트리에서 질문에 답하는 함수는 ```findAnswer(questions: List<String>, tree: Tree)```이다.

## 코드 및 실행 결과
### 코드
```kotlin
// 트리 자료구조
data class Tree (
    val vertices: List<Char>, // 정점
    val edges: Map<Char, List<Char>> // 간선
)

// 간선 추가하기
fun addEdge(source: Char, neighbor: Char, edges: MutableMap<Char, MutableList<Char>>) {
    if (edges.containsKey(source)) {
        edges.get(source)!!.add(neighbor)
    } else {
        edges.put(source, mutableListOf(neighbor))
    }
}

// 트리 자료구조 구성 함수
fun createTree(sentences: List<String>): Tree {
    val v = mutableListOf<Char>()
    val e = mutableMapOf<Char, MutableList<Char>>()
    
    sentences.forEach {
        val source = it[0]
        val neighbors = it.substringAfter("정점에 ").substringBefore("를 ").split(", ")
        
        // 해당 정점 및 간선들을 추가
        v.add(source)
        neighbors.forEach { n ->
            v.add(n[0])
            addEdge(source, n[0], e)
            addEdge(n[0], source, e)
        }
    }
    val vSet = v.toSet().toList()
    
    println("1. vertices = ${vSet}")
    println("2. edges = ${e}")
    
    return Tree(vSet, e)
}

// v0 -> v1 이후 도달 가능한 모든 점 P에 대해 ((v0 -> P) to v1)
fun markAllReachable(
    v0: Char, v1: Char, P: Char,
    edges: Map<Char, List<Char>>,
    nearestNeighbor: MutableMap<Char, MutableMap<Char, Char>>
) {
    // v0과 P가 일치하는 경우, 즉 자기 자신으로 가는 예외 처리
    if (v0 == P) {
        return
    }
    
    // 해당 정점 P를 추가하기
    if (nearestNeighbor.containsKey(v0)) {
        nearestNeighbor.get(v0)!!.put(P, v1)
    } else {
        nearestNeighbor.put(v0, mutableMapOf(P to v1))
    }
    
    // P와 이웃한 정점들에 대해 반복하기 (v0 제외)
    edges.get(P)!!.forEach {
        if (nearestNeighbor.get(v0)?.get(it) == null) {
            markAllReachable(v0, v1, it, edges, nearestNeighbor)
        }
    }
}

// 완성된 트리에서 질문에 답하는 함수
fun findAnswer(questions: List<String>, tree: Tree) {
    
    // 해당 정점으로 가려고 할 때 거쳐야 하는 이웃 노드 정보를 미리 저장
    val nearestNeighbor = mutableMapOf<Char, MutableMap<Char, Char>>()
    val vs = tree.vertices
    val es = tree.edges
    
    vs.forEach { v0 ->
        val neighbors = es.get(v0)!!
        
        // v0 -> v1 -> ... 으로 도달할 수 있는 모든 점 P에 대해 ((v0 -> P) to v1)
        neighbors.forEach { v1 ->
            markAllReachable(v0, v1, v1, es, nearestNeighbor)
        }
    }
    
    println("\n3. nearest neighbor info :")
    nearestNeighbor.forEach {
        println("4. ${it.key} / ${it.value.map { "${it.key}(${it.value})" }}")
    }
    
    // 질문에 답하기
    questions.forEach { q ->
        val qDrop = q.dropLast(1) // 마지막 괄호 제거
        println("\n질문 : ${q}")
        
        // A의 이웃 정점을 나열하면?
        val answer = if (qDrop.matches(Regex(".*의 이웃 정점을 나열하면"))) {
            es.get(q[0])!!.joinToString(", ")
        }
        
        // B의 이웃 정점은 몇 개인가?
        else if (qDrop.matches(Regex(".*의 이웃 정점은 몇 개인가"))) {
            es.get(q[0])!!.size
        }
        
        // 정점 C와 D 사이의 거리는?
        else if (qDrop.matches(Regex("정점 .*와 .* 사이의 거리는"))) {
            val v0 = q.substringAfter("정점 ").substringBefore("와 ")[0]
            val v1 = q.substringAfter("와 ").substringBefore(" 사이의")[0]
            
            if (v0 == v1) {
                0
            } else {
                var count = 0
                var currentPoint = v0
                
                while (currentPoint != v1) {
                    val nextPoint = nearestNeighbor.get(currentPoint)!!
                    val nextPoint2 = nextPoint.get(v1)!!
                    println("movement : from ${currentPoint} to ${nextPoint2}")
                    
                    currentPoint = nextPoint2
                    count++
                }
                count
            }
        }
        
        else {
            "이해하기 어려운 질문입니다."
        }
        
        println("답변 : ${answer}") 
    }
}

fun main() {
    
    // 임시 문장 목록
    val testSentences = listOf (
        "A의 이웃 정점에 B, C, D를 추가한다.",
        "E의 이웃 정점에 A를 추가한다.",
        "B의 이웃 정점에 F, G를 추가한다.",
        "C의 이웃 정점에 H를 추가한다.",
        "B의 이웃 정점에 I를 추가한다.",
        "J의 이웃 정점에 D를 추가한다.",
        "K의 이웃 정점에 C, L를 추가한다.",
        "N의 이웃 정점에 M를 추가한다.",
        "O의 이웃 정점에 M를 추가한다.",
        "P의 이웃 정점에 N, A를 추가한다."
    )
    
    // 임시 질문 목록
    val testQuestions = listOf (
        "A의 이웃 정점을 나열하면?",
        "A의 이웃 정점은 몇 개인가?",
        "B의 이웃 정점을 나열하면?",
        "B의 이웃 정점은 몇 개인가?",
        "D의 이웃 정점을 나열하면?",
        "N의 이웃 정점을 나열하면?",
        "정점 A와 A 사이의 거리는?",
        "정점 A와 D 사이의 거리는?",
        "정점 D와 B 사이의 거리는?",
        "정점 B와 J 사이의 거리는?",
        "정점 K와 N 사이의 거리는?",
        "정점 N와 L 사이의 거리는?",
        "정점 L와 O 사이의 거리는?",
        "정점 O와 L 사이의 거리는?",
        "모든 정점 사이의 거리의 합은?",
        "모든 정점 사이의 거리의 차의 최솟값은?"
    )
    
    // 트리 생성 및 질문에 답하기
    val testTree = createTree(testSentences)
    findAnswer(testQuestions, testTree)
}
```

### 실행 결과
```kotlin
1. vertices = [A, B, C, D, E, F, G, H, I, J, K, L, N, M, O, P]
2. edges = {A=[B, C, D, E, P], B=[A, F, G, I], C=[A, H, K], D=[A, J], E=[A], F=[B], G=[B], H=[C], I=[B], J=[D], K=[C, L], L=[K], N=[M, P], M=[N, O], O=[M], P=[N, A]}

3. nearest neighbor info :
4. A / [B(B), F(B), G(B), I(B), C(C), H(C), K(C), L(C), D(D), J(D), E(E), P(P), N(P), M(P), O(P)]
4. B / [A(A), C(A), H(A), K(A), L(A), D(A), J(A), E(A), P(A), N(A), M(A), O(A), F(F), G(G), I(I)]
4. C / [A(A), B(A), F(A), G(A), I(A), D(A), J(A), E(A), P(A), N(A), M(A), O(A), H(H), K(K), L(K)]
4. D / [A(A), B(A), F(A), G(A), I(A), C(A), H(A), K(A), L(A), E(A), P(A), N(A), M(A), O(A), J(J)]
4. E / [A(A), B(A), F(A), G(A), I(A), C(A), H(A), K(A), L(A), D(A), J(A), P(A), N(A), M(A), O(A)]
4. F / [B(B), A(B), C(B), H(B), K(B), L(B), D(B), J(B), E(B), P(B), N(B), M(B), O(B), G(B), I(B)]
4. G / [B(B), A(B), C(B), H(B), K(B), L(B), D(B), J(B), E(B), P(B), N(B), M(B), O(B), F(B), I(B)]
4. H / [C(C), A(C), B(C), F(C), G(C), I(C), D(C), J(C), E(C), P(C), N(C), M(C), O(C), K(C), L(C)]
4. I / [B(B), A(B), C(B), H(B), K(B), L(B), D(B), J(B), E(B), P(B), N(B), M(B), O(B), F(B), G(B)]
4. J / [D(D), A(D), B(D), F(D), G(D), I(D), C(D), H(D), K(D), L(D), E(D), P(D), N(D), M(D), O(D)]
4. K / [C(C), A(C), B(C), F(C), G(C), I(C), D(C), J(C), E(C), P(C), N(C), M(C), O(C), H(C), L(L)]
4. L / [K(K), C(K), A(K), B(K), F(K), G(K), I(K), D(K), J(K), E(K), P(K), N(K), M(K), O(K), H(K)]
4. N / [M(M), O(M), P(P), A(P), B(P), F(P), G(P), I(P), C(P), H(P), K(P), L(P), D(P), J(P), E(P)]
4. M / [N(N), P(N), A(N), B(N), F(N), G(N), I(N), C(N), H(N), K(N), L(N), D(N), J(N), E(N), O(O)]
4. O / [M(M), N(M), P(M), A(M), B(M), F(M), G(M), I(M), C(M), H(M), K(M), L(M), D(M), J(M), E(M)]
4. P / [N(N), M(N), O(N), A(A), B(A), F(A), G(A), I(A), C(A), H(A), K(A), L(A), D(A), J(A), E(A)]

질문 : A의 이웃 정점을 나열하면?
답변 : B, C, D, E, P

질문 : A의 이웃 정점은 몇 개인가?
답변 : 5

질문 : B의 이웃 정점을 나열하면?
답변 : A, F, G, I

질문 : B의 이웃 정점은 몇 개인가?
답변 : 4

질문 : D의 이웃 정점을 나열하면?
답변 : A, J

질문 : N의 이웃 정점을 나열하면?
답변 : M, P

질문 : 정점 A와 A 사이의 거리는?
답변 : 0

질문 : 정점 A와 D 사이의 거리는?
movement : from A to D
답변 : 1

질문 : 정점 D와 B 사이의 거리는?
movement : from D to A
movement : from A to B
답변 : 2

질문 : 정점 B와 J 사이의 거리는?
movement : from B to A
movement : from A to D
movement : from D to J
답변 : 3

질문 : 정점 K와 N 사이의 거리는?
movement : from K to C
movement : from C to A
movement : from A to P
movement : from P to N
답변 : 4

질문 : 정점 N와 L 사이의 거리는?
movement : from N to P
movement : from P to A
movement : from A to C
movement : from C to K
movement : from K to L
답변 : 5

질문 : 정점 L와 O 사이의 거리는?
movement : from L to K
movement : from K to C
movement : from C to A
movement : from A to P
movement : from P to N
movement : from N to M
movement : from M to O
답변 : 7

질문 : 정점 O와 L 사이의 거리는?
movement : from O to M
movement : from M to N
movement : from N to P
movement : from P to A
movement : from A to C
movement : from C to K
movement : from K to L
답변 : 7

질문 : 모든 정점 사이의 거리의 합은?
답변 : 이해하기 어려운 질문입니다.

질문 : 모든 정점 사이의 거리의 차의 최솟값은?
답변 : 이해하기 어려운 질문입니다.
```
