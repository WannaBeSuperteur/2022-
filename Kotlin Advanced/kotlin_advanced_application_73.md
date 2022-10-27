## 요구사항 분석
다음과 같은 형태의 문장에 근거하여 각 컴포넌트들의 왼쪽 위 끝 지점과 오른쪽 아래 끝 지점들을 출력하는 함수 ```printComponentLocations(sentences)```를 작성하시오.

```kotlin
전체 보드의 크기는 가로 a, 세로 b이다.
컴포넌트 component_id는 위쪽 경계선이 전체 보드의 위쪽 경계선과 일치하고, 왼쪽 경계선이 컴포넌트 component_id의 왼쪽 경계선과 일치하며, 그 크기는 가로 a, 세로 b이다.
컴포넌트 component_id는 위쪽 경계선이 전체 보드의 위쪽 경계선보다 10만큼 아래에 있고, 왼쪽 경계선이 컴포넌트 component_id의 오른쪽 경계선보다 10만큼 오른쪽에 있으며, 그 크기는 가로 c, 세로 d이다.
```

## 코드 및 실행 결과
### 코드
```kotlin
// 경계선 정보 (예: 컴포넌트 comp_main2의 위쪽 경계선보다 5만큼 아래에 있고) 를 파싱해서 위치 찾기
fun findPositionOfBorder(info: String, componentInfo: Map<String, List<Int>>): Int? {
    
    // 해당 컴포넌트의 정보 찾기
    val componentName = info.substringBefore("의 ").substringAfter("컴포넌트 ")
    val componentId = if (componentName.contains("전체 보드")) { "entire_board" } else { componentName }
    val componentBorderLocation = componentInfo.get(componentId)
    
    if (componentBorderLocation == null) {
        return null
    }
    
    // 해당 컴포넌트의 알맞은 경계선 정보 찾기
    val borderLocation = listOf("위", "아래", "왼", "오른").foldIndexed (-1) { idx, acc, e ->
        if (info.contains("${e}쪽 경계선")) {
            componentBorderLocation[idx]
        } else {
            acc
        }
    }
    
    // 경계선으로부터의 거리를 이용하여 최종 결과를 계산
    if (info.contains("과 일치")) {
        return borderLocation
    } else {
        val value = info.substringAfter("보다 ").substringBefore("만큼 ").toInt()
        if (info.contains("아래에") || info.contains("오른쪽에")) {
            return borderLocation + value
        } else {
            return borderLocation - value
        }
    }
}

// 각 컴포넌트의 위치 찾기
fun findComponentLocations(
    sentences: List<String>,
    componentInfo: MutableMap<String, List<Int>>,
    isChecked: MutableMap<String, Boolean>
) {
    sentences.forEachIndexed { _, it -> // return 용
        
        // 전체 보드의 크기인 경우
        if (it.contains("전체 보드의 크기는")) {
            val width = it.substringAfter("가로 ").substringBefore(", ").toInt()
            val height = it.substringAfter("세로 ").substringBefore("이다.").toInt()
            
            componentInfo.put("entire_board", listOf(0, height, 0, width))
            isChecked["entire_board"] = true
        }
        
        // 다른 정보인 경우
        else {
            
            // 컴포넌트 이름
            val namePart = it.substringBefore("는 ")
            val componentName = namePart.substringAfter("컴포넌트 ")
            
            // 컴포넌트 좌표 정보
            val infoPart = it.substringAfter("는 ")
            
            // (위쪽, 아래쪽, 왼쪽, 오른쪽, 가로, 세로)의 정보 (-1이면 null)
            val info = mutableListOf(-1, -1, -1, -1, -1, -1)
            val infoSplit = infoPart.split(", ")
            
            infoSplit.forEach {
                
                // 위쪽, 아래쪽, 왼쪽, 오른쪽 경계선 정보인 경우
                if (it.contains("쪽 경계선이")) {
                    val borderLocation = findPositionOfBorder(it.substringAfter("경계선이"), componentInfo)
                    if (borderLocation == null) { return@forEachIndexed }
                    val direction = it.substringBefore("쪽 경계선이")
                    
                    when (direction) {
                        "위" -> info[0] = borderLocation
                        "아래" -> info[1] = borderLocation
                        "왼" -> info[2] = borderLocation
                        "오른" -> info[3] = borderLocation
                    }
                }
                
                // 가로, 세로 크기 관련 정보인 경우
                else if (it.contains("가로 ")) {
                    val componentWidth = it.substringAfter("가로 ").substringBefore("이다").toInt()
                    info[4] = componentWidth
                }
                else if (it.contains("세로 ")) {
                    val componentWidth = it.substringAfter("세로 ").substringBefore("이다").toInt()
                    info[5] = componentWidth
                }
            }
            
            // (위쪽 [0], 아래쪽 [1], 세로 [5]) 중 2가지 정보를 이용하여 위, 아래 경계선의 위치 구하기
            val topAndBottom = if (info[0] == -1) {
                listOf(info[1] - info[5], info[1])
            } else if (info[1] == -1) {
                listOf(info[0], info[0] + info[5])
            } else { // info[5] == -1
                listOf(info[0], info[1])
            }
            
            // (왼쪽 [2], 오른쪽 [3], 가로 [4]) 중 2가지 정보를 이용하여 왼쪽, 오른쪽 경계선의 위치 구하기
            val leftAndRight = if (info[2] == -1) {
                listOf(info[3] - info[4], info[3])
            } else if (info[3] == -1) {
                listOf(info[2], info[2] + info[4])
            } else { // info[4] == -1
                listOf(info[2], info[3])
            }
            
            // 컴포넌트 정보에 최종 추가
            componentInfo.put(componentName, listOf(topAndBottom[0], topAndBottom[1], leftAndRight[0], leftAndRight[1]))
            isChecked[componentName] = true
        }
    }
}

// 각 컴포넌트의 위치 출력하기
fun printComponentLocations(sentences: List<String>) {
    
    // 전체 컴포넌트에 대한 체크 여부
    val isChecked = mutableMapOf<String, Boolean>()
    isChecked.put("entire_board", false)
    
    sentences.forEach {
        if (it.startsWith("컴포넌트")) {
            val componentName = it.substringBefore("는").substringAfter("컴포넌트 ")
            isChecked.put(componentName, false)
        }
    }
    println("\n초기 체크 여부 : ${isChecked}\n")
    
    // 컴포넌트 정보 (ID -> 위, 아래, 왼쪽, 오른쪽 경계선의 위치)
    val componentInfo = mutableMapOf<String, List<Int>>()
    
    while (!isChecked.all { it.value }) {
        findComponentLocations(sentences, componentInfo, isChecked)
        println("is checked = ${isChecked}")
    }
    
    // 컴포넌트 정보 출력
    println("\n전체 컴포넌트 정보 : ")
    componentInfo.forEach {
        println("${it.key} -> ${it.value}")
    }
    
    // 각 컴포넌트의 왼쪽 위 끝, 오른쪽 아래 끝 지점 출력
    println("\n컴포넌트의 각 끝 지점 출력 : ")
    componentInfo.forEach {
        println("컴포넌트 이름 : ${it.key}, 왼쪽 위 끝 : (x=${it.value[2]}, y=${it.value[0]}), 오른쪽 아래 끝 : (x=${it.value[3]}, y=${it.value[1]})")
    }
}

fun main() {
    
    // 임시 문장 목록 (가정 : 위쪽부터 차례대로 읽어나갈 때 잘못된 참조가 없음 + 컴포넌트명은 "entire_board" 사용 불가)
    val testSentences = listOf<String> (
        "전체 보드의 크기는 가로 300, 세로 200이다.",
        "컴포넌트 comp_main는 위쪽 경계선이 전체 보드의 위쪽 경계선과 일치하고, 왼쪽 경계선이 전체 보드의 왼쪽 경계선과 일치하며, 아래쪽 경계선이 전체 보드의 아래쪽 경계선과 일치하고, 오른쪽 경계선이 전체 보드의 오른쪽 경계선과 일치한다.",
        "컴포넌트 comp_main2는 위쪽 경계선이 전체 보드의 위쪽 경계선보다 10만큼 아래에 있고, 왼쪽 경계선이 전체 보드의 왼쪽 경계선보다 10만큼 오른쪽에 있으며, 아래쪽 경계선이 전체 보드의 아래쪽 경계선보다 15만큼 위에 있고, 오른쪽 경계선이 전체 보드의 오른쪽 경계선보다 15만큼 왼쪽에 있다.",
        "컴포넌트 comp_main3는 위쪽 경계선이 컴포넌트 comp_main2의 위쪽 경계선보다 5만큼 아래에 있고, 왼쪽 경계선이 컴포넌트 comp_main2의 왼쪽 경계선보다 5만큼 오른쪽에 있으며, 아래쪽 경계선이 컴포넌트 comp_main2의 아래쪽 경계선보다 10만큼 위에 있고, 오른쪽 경계선이 컴포넌트 comp_main2의 오른쪽 경계선보다 10만큼 왼쪽에 있다.",
        "컴포넌트 comp_1는 위쪽 경계선이 컴포넌트 comp_main3의 위쪽 경계선과 일치하고, 왼쪽 경계선이 컴포넌트 comp_main3의 왼쪽 경계선보다 10만큼 오른쪽에 있으며, 그 크기는 가로 100, 세로 30이다.",
        "컴포넌트 comp_2는 위쪽 경계선이 컴포넌트 comp_1의 아래쪽 경계선과 일치하고, 왼쪽 경계선이 컴포넌트 comp_1의 왼쪽 경계선보다 10만큼 오른쪽에 있으며, 그 크기는 가로 100, 세로 20이다.",
        "컴포넌트 comp_3는 위쪽 경계선이 컴포넌트 comp_2의 아래쪽 경계선보다 7만큼 아래에 있고, 왼쪽 경계선이 컴포넌트 comp_2의 왼쪽 경계선과 일치하며, 그 크기는 가로 80, 세로 30이다.",
        "컴포넌트 comp_4는 왼쪽 경계선이 컴포넌트 comp_3의 오른쪽 경계선보다 3만큼 오른쪽에 있고, 아래쪽 경계선이 컴포넌트 comp_main3의 아래쪽 경계선과 일치하며, 오른쪽 경계선이 컴포넌트 comp_main2의 오른쪽 경계선보다 3만큼 왼쪽에 있고, 그 크기는 세로 50이다.",
        "컴포넌트 comp_5는 위쪽 경계선이 컴포넌트 comp_3의 위쪽 경계선과 일치하고, 오른쪽 경계선이 컴포넌트 comp_main3의 오른쪽 경계선과 일치하며, 아래쪽 경계선이 컴포넌트 comp_4의 위쪽 경계선보다 15만큼 위쪽에 있고, 그 크기는 가로 60이다."
    )
    
    // 문장들로부터 각 컴포넌트의 위치 출력하기
    printComponentLocations(testSentences)
    
    // 임시 문장 목록 2 (가정 : 위쪽부터 차례대로 읽어나갈 때 잘못된 참조가 있을 수 있음)
    val testSentences2 = testSentences.reversed()
    
    // 문장들로부터 각 컴포넌트의 위치 출력하기
    printComponentLocations(testSentences2)
}
```

### 실행 결과
```kotlin

초기 체크 여부 : {entire_board=false, comp_main=false, comp_main2=false, comp_main3=false, comp_1=false, comp_2=false, comp_3=false, comp_4=false, comp_5=false}

is checked = {entire_board=true, comp_main=true, comp_main2=true, comp_main3=true, comp_1=true, comp_2=true, comp_3=true, comp_4=true, comp_5=true}

전체 컴포넌트 정보 : 
entire_board -> [0, 200, 0, 300]
comp_main -> [0, 200, 0, 300]
comp_main2 -> [10, 185, 10, 285]
comp_main3 -> [15, 175, 15, 275]
comp_1 -> [15, 45, 25, 125]
comp_2 -> [45, 65, 35, 135]
comp_3 -> [72, 102, 35, 115]
comp_4 -> [125, 175, 118, 282]
comp_5 -> [72, 110, 215, 275]

컴포넌트의 각 끝 지점 출력 : 
컴포넌트 이름 : entire_board, 왼쪽 위 끝 : (x=0, y=0), 오른쪽 아래 끝 : (x=300, y=200)
컴포넌트 이름 : comp_main, 왼쪽 위 끝 : (x=0, y=0), 오른쪽 아래 끝 : (x=300, y=200)
컴포넌트 이름 : comp_main2, 왼쪽 위 끝 : (x=10, y=10), 오른쪽 아래 끝 : (x=285, y=185)
컴포넌트 이름 : comp_main3, 왼쪽 위 끝 : (x=15, y=15), 오른쪽 아래 끝 : (x=275, y=175)
컴포넌트 이름 : comp_1, 왼쪽 위 끝 : (x=25, y=15), 오른쪽 아래 끝 : (x=125, y=45)
컴포넌트 이름 : comp_2, 왼쪽 위 끝 : (x=35, y=45), 오른쪽 아래 끝 : (x=135, y=65)
컴포넌트 이름 : comp_3, 왼쪽 위 끝 : (x=35, y=72), 오른쪽 아래 끝 : (x=115, y=102)
컴포넌트 이름 : comp_4, 왼쪽 위 끝 : (x=118, y=125), 오른쪽 아래 끝 : (x=282, y=175)
컴포넌트 이름 : comp_5, 왼쪽 위 끝 : (x=215, y=72), 오른쪽 아래 끝 : (x=275, y=110)

초기 체크 여부 : {entire_board=false, comp_5=false, comp_4=false, comp_3=false, comp_2=false, comp_1=false, comp_main3=false, comp_main2=false, comp_main=false}

is checked = {entire_board=true, comp_5=false, comp_4=false, comp_3=false, comp_2=false, comp_1=false, comp_main3=false, comp_main2=false, comp_main=false}
is checked = {entire_board=true, comp_5=false, comp_4=false, comp_3=false, comp_2=false, comp_1=false, comp_main3=false, comp_main2=true, comp_main=true}
is checked = {entire_board=true, comp_5=false, comp_4=false, comp_3=false, comp_2=false, comp_1=false, comp_main3=true, comp_main2=true, comp_main=true}
is checked = {entire_board=true, comp_5=false, comp_4=false, comp_3=false, comp_2=false, comp_1=true, comp_main3=true, comp_main2=true, comp_main=true}
is checked = {entire_board=true, comp_5=false, comp_4=false, comp_3=false, comp_2=true, comp_1=true, comp_main3=true, comp_main2=true, comp_main=true}
is checked = {entire_board=true, comp_5=false, comp_4=false, comp_3=true, comp_2=true, comp_1=true, comp_main3=true, comp_main2=true, comp_main=true}
is checked = {entire_board=true, comp_5=false, comp_4=true, comp_3=true, comp_2=true, comp_1=true, comp_main3=true, comp_main2=true, comp_main=true}
is checked = {entire_board=true, comp_5=true, comp_4=true, comp_3=true, comp_2=true, comp_1=true, comp_main3=true, comp_main2=true, comp_main=true}

전체 컴포넌트 정보 : 
entire_board -> [0, 200, 0, 300]
comp_main2 -> [10, 185, 10, 285]
comp_main -> [0, 200, 0, 300]
comp_main3 -> [15, 175, 15, 275]
comp_1 -> [15, 45, 25, 125]
comp_2 -> [45, 65, 35, 135]
comp_3 -> [72, 102, 35, 115]
comp_4 -> [125, 175, 118, 282]
comp_5 -> [72, 110, 215, 275]

컴포넌트의 각 끝 지점 출력 : 
컴포넌트 이름 : entire_board, 왼쪽 위 끝 : (x=0, y=0), 오른쪽 아래 끝 : (x=300, y=200)
컴포넌트 이름 : comp_main2, 왼쪽 위 끝 : (x=10, y=10), 오른쪽 아래 끝 : (x=285, y=185)
컴포넌트 이름 : comp_main, 왼쪽 위 끝 : (x=0, y=0), 오른쪽 아래 끝 : (x=300, y=200)
컴포넌트 이름 : comp_main3, 왼쪽 위 끝 : (x=15, y=15), 오른쪽 아래 끝 : (x=275, y=175)
컴포넌트 이름 : comp_1, 왼쪽 위 끝 : (x=25, y=15), 오른쪽 아래 끝 : (x=125, y=45)
컴포넌트 이름 : comp_2, 왼쪽 위 끝 : (x=35, y=45), 오른쪽 아래 끝 : (x=135, y=65)
컴포넌트 이름 : comp_3, 왼쪽 위 끝 : (x=35, y=72), 오른쪽 아래 끝 : (x=115, y=102)
컴포넌트 이름 : comp_4, 왼쪽 위 끝 : (x=118, y=125), 오른쪽 아래 끝 : (x=282, y=175)
컴포넌트 이름 : comp_5, 왼쪽 위 끝 : (x=215, y=72), 오른쪽 아래 끝 : (x=275, y=110)
```
