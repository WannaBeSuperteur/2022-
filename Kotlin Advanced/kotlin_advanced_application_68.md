## 요구사항 분석
```Github```과 비슷하지만 훨씬 단순한 형태의 소프트웨어 개발 협업 플랫폼인 ```WannaBeMini```를 개발하려고 한다. 여기에는 다음 명령어가 포함된다.
* ```push "commit message"``` : 현재 브랜치에 ```commit message```로 커밋함과 동시에 푸시를 한다.
* ```checkout branch-name``` : 공백을 포함하지 않은 ```branch-name```이라는 이름을 가진 브랜치로 이동한다.
  * 해당 브랜치가 없으면 자동으로 생성된 후 즉시 이동한다.
  * 이때의 커밋 메시지는 브랜치가 생성되었을 때는 ```added branch branch-name```, 그렇지 않을 때는 ```moving to branch branch-name```이다.
* ```merge``` : 해당 브랜치를 생성한 시점에 위치해 있었던 브랜치(parent branch)로 현재 브랜치를 ```merge```한다.
  * 이때의 커밋 메시지는 ```merged into (parent branch의 이름)```이다.

각 명령에 대해, 다음과 같이 해당 명령을 수행한 branch와 커밋 메시지, 그리고 (leaf branch에 대해서는) branch 간의 관계를 위쪽부터 차례대로 나타낸다.
```kotlin
info :
master / first commit
master / added branch BRANCH_000
BRANCH_000 / second commit
BRANCH_000 / added branch BRANCH_001
BRANCH_001 / moving to BRANCH_000
BRANCH_000 / added branch BRANCH_002
BRANCH_002 / third commit
BRANCH_002 / moving to master
master / fourth commit
master / fifth commit
master / moving to BRANCH_001
BRANCH_001 / merged into BRANCH_000
BRANCH_000 / test commit
BRANCH_000 / moving to master
master / added branch BRANCH_003

branch :
master > BRANCH_000 > BRANCH_001
master > BRANCH_000 > BRANCH_002
master > BRANCH_003
```

단, 처음에는 ```master``` 브랜치에 위치한다.

### 데이터 클래스
데이터 클래스는 없고, 명령의 집합이 ```List<String>```으로 주어진다.

## 코드 및 실행 결과
### 코드
```kotlin
// 명령 수행 및 브랜치 정보 출력하기
fun printInfo(commands: List<String>) {
    
    // 브랜치 정보
    val branchInfo = mutableMapOf<String, String?>() // parent branch
    val isLeaf = mutableMapOf<String, Boolean>() // leaf branch에 대해서만 브랜치 간 관계 출력
    
    var currentBranch = "master"
    val info = mutableListOf<String>()
    
    // 마스터 브랜치 정보 추가
    branchInfo.put("master", null)
    isLeaf.put("master", true)
    
    commands.forEachIndexed { idx, it ->
        
        // push
        if (it.startsWith("push")) {
            val commitMessage = it.substring(6, it.length - 1)
            info.add("${currentBranch} / ${commitMessage}")
        }
        
        // checkout
        else if (it.startsWith("checkout")) {
            val branchName = it.substring(9)
            
            if (branchInfo.containsKey(branchName)) {
                info.add("${currentBranch} / moving to ${branchName}")
            } else {
                info.add("${currentBranch} / added branch ${branchName}")
                branchInfo.put(branchName, currentBranch)
                isLeaf.put(branchName, true)
                isLeaf.put(currentBranch, false)
            }
            currentBranch = branchName
        }
        
        // merge
        else if (it == "merge") {
            val parentBranch = branchInfo.get(currentBranch)
            
            if (parentBranch == null) {
                println("[ERROR at ${idx}] parent branch is null (current branch: ${currentBranch})")
            } else { // merge한 후 자동으로 parent branch로 이동
                info.add("${currentBranch} / merged into ${parentBranch}")
                currentBranch = parentBranch
            }
        }
    }
    println("\n1. branch info = ${branchInfo}")
    println("2. leaf node info = ${isLeaf}")
    
    val leafNodes = isLeaf.filter { it.value }.map { it.key }
    println("3. leaf node only = ${leafNodes}")
    
    // 정보 출력
    println("\ninfo :")
    info.forEach {
        println(it)
    }
    
    // 각 leaf node의 parent branch 정보 출력
    println("\nbranch :")
    
    leafNodes.forEach {
        val searchedBranches = mutableListOf<String>(it)
        var currentSearchingBranch = it
        
        while (true) {
            val parent = branchInfo.get(currentSearchingBranch)
            if (parent == null) {
                break
            } else {
                currentSearchingBranch = parent
                searchedBranches.add(parent)
            }
        }
        
        // parent branch -> child branch 순으로 출력
        val searchResultString = searchedBranches.fold ( "" ) { acc, e ->
            e + " > " + acc 
        }.dropLast(3)
        println(searchResultString)
    }
}

fun main() {
    
    // 임시 명령들
    val tempCommands = listOf(
        "push \"first commit\"",
        "checkout BRANCH_000",
        "push \"second commit\"",
        "checkout BRANCH_001",
        "checkout BRANCH_000",
        "checkout BRANCH_002",
        "push \"third commit\"",
        "checkout master",
        "merge",
        "push \"fourth commit\"",
        "push \"fifth commit\"",
        "merge",
        "checkout BRANCH_001",
        "merge",
        "push \"test commit\"",
        "checkout master",
        "merge",
        "checkout BRANCH_003"
    )
    
    // 명령 수행 및 브랜치 정보 출력하기
    printInfo(tempCommands)
}
```

### 실행 결과
```kotlin
[ERROR at 8] parent branch is null (current branch: master)
[ERROR at 11] parent branch is null (current branch: master)
[ERROR at 16] parent branch is null (current branch: master)

1. branch info = {master=null, BRANCH_000=master, BRANCH_001=BRANCH_000, BRANCH_002=BRANCH_000, BRANCH_003=master}
2. leaf node info = {master=false, BRANCH_000=false, BRANCH_001=true, BRANCH_002=true, BRANCH_003=true}
3. leaf node only = [BRANCH_001, BRANCH_002, BRANCH_003]

info :
master / first commit
master / added branch BRANCH_000
BRANCH_000 / second commit
BRANCH_000 / added branch BRANCH_001
BRANCH_001 / moving to BRANCH_000
BRANCH_000 / added branch BRANCH_002
BRANCH_002 / third commit
BRANCH_002 / moving to master
master / fourth commit
master / fifth commit
master / moving to BRANCH_001
BRANCH_001 / merged into BRANCH_000
BRANCH_000 / test commit
BRANCH_000 / moving to master
master / added branch BRANCH_003

branch :
master > BRANCH_000 > BRANCH_001
master > BRANCH_000 > BRANCH_002
master > BRANCH_003
```
