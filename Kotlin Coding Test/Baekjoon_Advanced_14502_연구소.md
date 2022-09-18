Problem Source : https://www.acmicpc.net/problem/14502

```kotlin
fun searchVirusSpaces(info: List<List<Int>>, ii: Int, jj: Int, N: Int, M: Int, isVirusSpace: MutableList<MutableList<Boolean>>) {
    var toFind = mutableListOf<List<Int>>()
    var i = ii
    var j = jj
    
    isVirusSpace[i][j] = true
    
    while (true) {
        // append to neighbor space list
        if (i > 0) {
            if (!isVirusSpace[i-1][j] && info[i-1][j] == 0) {
                toFind.add(listOf(i-1, j))
                isVirusSpace[i-1][j] = true
            }
        }
        if (i < N-1) {
            if (!isVirusSpace[i+1][j] && info[i+1][j] == 0) {
                toFind.add(listOf(i+1, j))
                isVirusSpace[i+1][j] = true
            }
        }
        if (j > 0) {
            if (!isVirusSpace[i][j-1] && info[i][j-1] == 0) {
                toFind.add(listOf(i, j-1))
                isVirusSpace[i][j-1] = true
            }
        }
        if (j < M-1) {
            if (!isVirusSpace[i][j+1] && info[i][j+1] == 0) {
                toFind.add(listOf(i, j+1))
                isVirusSpace[i][j+1] = true
            }
        }
        // println(toFind)
        
        if (toFind.size > 0) {
            i = toFind[0][0]
            j = toFind[0][1]
            toFind.removeFirst()
        } else {
            break
        }
    }
}

fun checkAnswer(info: List<List<Int>>): Int {

    // initialize
    val N = info.size
    val M = info[0].size
    val isVirusSpace = mutableListOf<MutableList<Boolean>>()
    
    for (i in 0 until N) {
        isVirusSpace.add((0 until M).map { false }.toMutableList())
    }
    
    // find virus spaces
    for (i in 0 until N) {
        for (j in 0 until M) {
            if (info[i][j] == 2) {
                searchVirusSpaces(info, i, j, N, M, isVirusSpace)
            }
        }
    }
    
    var count = 0
    for (i in 0 until N) {
        for (j in 0 until M) {
            if(!isVirusSpace[i][j] && info[i][j] == 0) {
                count += 1
            }
        }
    }
    return count
}

fun main() {
    
    // read data
    val NAndM = readLine().toString().split(" ")
    val N = NAndM[0].toInt()
    val M = NAndM[1].toInt()
    
    val info = mutableListOf<MutableList<Int>>()
    for (i in 0 until N) {
        info.add(readLine().toString().split(" ").map { it.toInt() }.toMutableList())
    }
    
    // get indices of free spaces
    val freeSpaces = (0 until N).flatMap { i ->
        (0 until M).map { j ->
            if (info[i][j] == 0) {
                listOf(i, j)
            } else {
                null
            }
        }
    }.filterNotNull()
    
    // println(info)
    // println(freeSpaces)
    
    // check each case
    val FS = freeSpaces.size
    var result = 0
    
    for (i in 0 until FS) {
        for (j in i+1 until FS) {
            for (k in j+1 until FS) {
                
                // add walls
                listOf(i, j, k).forEach {
                    val y = freeSpaces[it][0]
                    val x = freeSpaces[it][1]
                    info[y][x] = 1
                }
                
                // check and update answer
                // println("${i},${j},${k}")
                result = Math.max(result, checkAnswer(info))
                
                // remove walls
                listOf(i, j, k).forEach {
                    val y = freeSpaces[it][0]
                    val x = freeSpaces[it][1]
                    info[y][x] = 0
                }
            }
        }
    }
    print(result)
}
```
