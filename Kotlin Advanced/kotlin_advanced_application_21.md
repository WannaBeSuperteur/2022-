## 요구사항 분석
워너비슈퍼추어소프트 인사팀에서는 다음의 요구 조건을 만족하는, 데이터베이스와 유사한 형태의 정보 체계를 구축하려고 한다.
* 워너비슈퍼추어소프트에는 1개 이상의 부서가 있다.
* 각 부서는 1명 이상의 사원으로 구성되고, 각 사원은 하나의 부서에만 반드시 속한다.
* 각 프로젝트는 1명 이상의 사원으로 구성되고, 각 사원은 1개 이상의 프로젝트에 반드시 속한다. (=참여한다)
* 각 부서에서는 1개 이상의 프로젝트를 진행하고, 각 프로젝트는 1개 이상의 부서에서 진행한다.
* 각 사무실은 1명 이상의 사원이 속해 있고, 각 사원은 1개의 사무실에만 속해 있다.
* 하나의 부서는 1개 이상의 사무실을 사용할 수 있고, 각 사무실을 사용하는 부서는 오직 1개뿐이다.
* 정보 체계는 아래 **데이터 클래스**와 같이 표현된다.

다음을 출력하는 프로그램을 작성하시오.
* 사원 ID ```1000```이 참여하는 모든 프로젝트에 대한 정보
* 여러 개의 부서에서 진행하는 프로젝트를 ```부서 간 협업 프로젝트```라고 할 때, 모든 ```부서 간 협업 프로젝트```에 대한 정보
* 사무실 ```A101```에 속한 부서에서 참여하는 모든 프로젝트에 대한 정보
* 사무실 ```A101```에 속한 사원들이 참여하는 모든 프로젝트에 대한 정보
* ```서버개발부```가 속한 사무실 중 속한 사원이 3명 이상인 모든 사무실에 대한 정보
* 2개 이상의 프로젝트를 진행하는 사무실 중 속한 사원이 2명 이상인 사무실을 사용하는 모든 부서에 대한 정보

단, 프로젝트에 대한 정보는 참여 부서 수 및 참여 인원 수를 포함하여 출력하고, 사무실과 부서에 대한 정보는 해당 사무실 또는 부서에 속한 사원의 수를 포함하여 출력한다.

### 데이터 클래스
```kotlin
// 부서 정보
data class Department (
    val id: Int, // 부서 ID
    val name: String // 부서 이름
)

// 프로젝트 정보
data class Project (
    val id: Int, // 프로젝트 ID
    val name: String, // 프로젝트 이름
    val important: Char // 프로젝트 중요도 (A, B, C, D)
)

// 사원 정보
data class Employee (
    val id: Int, // 사원 ID
    val name: String, // 사원 이름
    val gender: Char, // 성별 (남:M, 여:W)
    val age: Int, // 나이,
    val office_id: Int, // 소속 사무실의 ID
)

// 사무실 정보
data class Office (
    val id: Int, // 사무실 ID,
    val name: String, // 사무실 이름,
    val department_id: Int // 소속 부서의 ID
)

// 프로젝트-부서 정보
data class ProjectDepartment (
    val project_id: Int, // 프로젝트 ID
    val department_id: Int // 부서 ID
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 부서 정보
data class Department (
    val id: Int, // 부서 ID
    val name: String // 부서 이름
)

// 프로젝트 정보
data class Project (
    val id: Int, // 프로젝트 ID
    val name: String, // 프로젝트 이름
    val important: Char // 프로젝트 중요도 (A, B, C, D)
)

// 사원 정보
data class Employee (
    val id: Int, // 사원 ID
    val name: String, // 사원 이름
    val gender: Char, // 성별 (남:M, 여:W)
    val age: Int, // 나이,
    val office_id: Int, // 소속 사무실의 ID
)

// 사무실 정보
data class Office (
    val id: Int, // 사무실 ID,
    val name: String, // 사무실 이름,
    val department_id: Int // 소속 부서의 ID
)

// 프로젝트-부서 정보
data class ProjectDepartment (
    val project_id: Int, // 프로젝트 ID
    val department_id: Int // 부서 ID
)

// 프로젝트 정보 출력 함수
fun printProjectInfo(
    projectId: Int,
    projects: List<Project>,
    projectDepartmentCount: Map<Int, Int>,
    projectEmployeeCount: Map<Int, Int>
) {
    val project = projects.find { pj ->
        projectId == pj.id
    }!!
    val departmentCount = projectDepartmentCount.get(projectId)!!
    val employeeCount = projectEmployeeCount.get(projectId)!!
    
    println("프로젝트 ID: ${projectId}, 이름: ${project.name}, 중요도: ${project.important}, 부서 수: ${departmentCount}, 인원: ${employeeCount}")
}

// 사원 ID 1000이 참여하는 모든 프로젝트에 대한 정보
// = 사원 ID 1000이 속한 부서에서 참여하는 모든 프로젝트에 대한 정보
fun findProjectsOfEmployee(
    employeeId: Int,
    employees: List<Employee>,
    offices: List<Office>,
    projectDepartments: List<ProjectDepartment>,
    projects: List<Project>,
    projectDepartmentCount: Map<Int, Int>,
    projectEmployeeCount: Map<Int, Int>
) {
    println("--------\n")
    
    val officeId = employees.find {
        it.id == employeeId
    }!!.office_id
    println("5. 사무실 ID: ${officeId}")
    
    val departmentId = offices.find {
        it.id == officeId
    }!!.department_id
    println("6. 부서 ID: ${departmentId}\n")
    
    // 프로젝트 정보 출력
    projectDepartments.forEach {
        if (it.department_id == departmentId) {
            printProjectInfo(
                it.project_id, projects,
                projectDepartmentCount, projectEmployeeCount
            )
        }
    }
}

// 여러 개의 부서에서 진행하는 프로젝트를 부서 간 협업 프로젝트라고 할 때, 모든 부서 간 협업 프로젝트에 대한 정보
fun findProjectsOfMultipleDepartments(
    projectDepartments: List<ProjectDepartment>,
    projects: List<Project>,
    projectDepartmentCount: Map<Int, Int>,
    projectEmployeeCount: Map<Int, Int>
) {
    println("\n--------\n")
    
    val collaboProjects = projects.filter { pj ->
        projectDepartments.count {
            it.project_id == pj.id
        } >= 2
    }
    
    collaboProjects.forEach {
        printProjectInfo(
            it.id, projects,
            projectDepartmentCount, projectEmployeeCount
        )
    }
}

// 사무실 A101에 속한 부서에서 참여하는 모든 프로젝트에 대한 정보
// 부서 > 사무실 > 사원, 부서 <-> 프로젝트이므로, 사무실 A101에 속한 사원들이 참여하는 모든 프로젝트에 대한 정보와 동일
fun findProjectsOfOffice(
    officeName: String,
    offices: List<Office>,
    projectDepartments: List<ProjectDepartment>,
    projects: List<Project>,
    projectDepartmentCount: Map<Int, Int>,
    projectEmployeeCount: Map<Int, Int>
) {
    println("\n--------\n")
    
    val departmentId = offices.find {
        it.name == officeName
    }!!.department_id
    println("7. 부서 ID: ${departmentId}\n")
    
    // 프로젝트 정보 출력
    projectDepartments.forEach {
        if (it.department_id == departmentId) {
            printProjectInfo(
                it.project_id, projects,
                projectDepartmentCount, projectEmployeeCount
            )
        }
    }
}

// 서버개발부가 속한 사무실 중 속한 사원이 3명 이상인 모든 사무실에 대한 정보
fun findOfficesOfDepartments(
    departmentName: String,
    offices: List<Office>,
    departments: List<Department>,
    officeEmployeeCount: Map<Int, Int>
) {
    println("\n--------\n")
    
    val matchedOffices = offices.filter {
        val isDepartmentNameMatch = it.department_id == departments.find {
            it.name == departmentName
        }!!.id
        val isAtLeast3 = officeEmployeeCount.get(it.id)!! >= 3
        
        isDepartmentNameMatch && isAtLeast3
    }
    
    matchedOffices.forEach {
        println("사무실 ID: ${it.id}, 이름: ${it.name}, 부서 ID: ${it.department_id}, 인원수: ${officeEmployeeCount.get(it.id)!!}")
    }
}

// 2개 이상의 프로젝트를 진행하는 사무실 중 속한 사원이 2명 이상인 사무실을 사용하는 모든 부서에 대한 정보
// 2개 이상의 프로젝트를 진행하는 "부서에 속한" 사무실 중 ... 과 동일
fun findDepartmentInfoOfOfficesWithMultipleProjects(
    offices: List<Office>,
    projectDepartments: List<ProjectDepartment>,
    departments: List<Department>,
    officeEmployeeCount: Map<Int, Int>,
    departmentEmployeeCount: Map<Int, Int>
) {
    println("\n--------\n")
    
    // 조건을 만족시키는 사무실만 선택
    val selectedOffices = offices.filter { o ->
        val isMultipleProjects = projectDepartments.count { pj ->
            pj.department_id == o.department_id
        } >= 2
        val isAtLeast2 = officeEmployeeCount.get(o.id)!! >= 2
        
        isMultipleProjects && isAtLeast2
    }
    print("8. 조건 만족 사무실들: ${selectedOffices}\n")
    
    // 해당 사무실들이 속한 모든 부서의 합집합 구하기
    val departmentIdSet = selectedOffices.map {
        it.department_id
    }.toSet()
    println("9. 부서 ID 목록: ${departmentIdSet}\n")
    
    // 해당 부서들의 정보 출력
    departmentIdSet.forEach {
        val dep = departments.find { d ->
            d.id == it
        }!!
        println("부서 ID: ${dep.id}, 부서 이름: ${dep.name}, 인원수: ${departmentEmployeeCount.get(dep.id)!!}")
    }
}

fun main() {
    
    // 임시 부서 정보
    val tempDepartments = listOf<Department> (
        Department(100, "기획부"),
        Department(101, "마케팅부"),
        Department(102, "클라개발부"),
        Department(103, "서버개발부")
    )
    
    // 임시 프로젝트 정보
    val tempProjects = listOf<Project> (
        Project(2110, "워너비퍼즐 서버 개발", 'B'),
        Project(2111, "워너비퍼즐 앱 개발", 'A'),
        Project(2112, "워너비퍼즐 안드로이드 개발", 'B'),
        Project(2113, "워너비퍼즐 iOS 개발", 'B'),
        Project(2114, "코딩왕국 앱개발", 'A'),
        Project(2115, "코딩왕국 웹개발", 'C'),
        Project(2200, "앱 마케팅", 'A'),
        Project(2201, "홈페이지 마케팅", 'B')
    )
    
    // 임시 프로젝트-부서 정보
    val tempProjectDepartments = listOf<ProjectDepartment> (
        ProjectDepartment(2110, 103),
        ProjectDepartment(2111, 102),
        ProjectDepartment(2111, 103),
        ProjectDepartment(2112, 102),
        ProjectDepartment(2113, 102),
        ProjectDepartment(2114, 100),
        ProjectDepartment(2114, 102),
        ProjectDepartment(2114, 103),
        ProjectDepartment(2115, 103),
        ProjectDepartment(2200, 100),
        ProjectDepartment(2200, 101),
        ProjectDepartment(2201, 101),
        ProjectDepartment(2201, 103)
    )
    
    // 임시 사무실 정보
    val tempOffices = listOf<Office> (
        Office(501, "A101", 102),
        Office(502, "A102", 103),
        Office(503, "A103", 100),
        Office(504, "A104", 101),
        Office(505, "A201", 103),
        Office(506, "A202", 103),
        Office(507, "A203", 102),
        Office(508, "A204", 102)
    )
    
    // 임시 사원 정보
    val tempEmployees = listOf<Employee> (
        Employee(1000, "hong", 'M', 30, 501),
        Employee(1001, "min", 'F', 28, 501),
        Employee(1002, "seok", 'M', 29, 502),
        Employee(1003, "hyuk", 'M', 32, 503),
        Employee(1004, "jeong", 'F', 25, 503),
        Employee(1005, "yoon", 'M', 26, 504),
        Employee(1006, "jin", 'M', 28, 504),
        Employee(1007, "hyo", 'F', 28, 504),
        Employee(1008, "gee", 'M', 30, 505),
        Employee(1009, "tae", 'M', 27, 506),
        Employee(1010, "soo", 'F', 29, 506),
        Employee(1011, "yong", 'M', 32, 506),
        Employee(1012, "taek", 'M', 31, 507),
        Employee(1013, "hyun", 'M', 31, 507),
        Employee(1014, "yeon", 'F', 28, 508)
    )
    
    // 사무실 -> 사원 수 매핑
    val officeEmployeeCount = mutableMapOf<Int, Int>()
    
    tempEmployees.forEach {
        if (officeEmployeeCount.containsKey(it.office_id)) {
            val acc = officeEmployeeCount.get(it.office_id)!!
            officeEmployeeCount.put(it.office_id, acc + 1)
        } else {
            officeEmployeeCount.put(it.office_id, 1)
        }
    }
    
    println("1. ${officeEmployeeCount}")
    
    // 부서 -> 사원 수 매핑
    val departmentEmployeeCount = mutableMapOf<Int, Int>()
    
    tempOffices.forEach {
        if (departmentEmployeeCount.containsKey(it.department_id)) {
            val acc = departmentEmployeeCount.get(it.department_id)!!
            departmentEmployeeCount.put(it.department_id, acc + officeEmployeeCount.get(it.id)!!)
        } else {
            departmentEmployeeCount.put(it.department_id, officeEmployeeCount.get(it.id)!!)
        }
    }
    
    println("2. ${departmentEmployeeCount}")
    
    // 프로젝트 -> 참여 부서 수 매핑
    // 프로젝트 -> 참여 인원 수 매핑
    val projectDepartmentCount = mutableMapOf<Int, Int>()
    val projectEmployeeCount = mutableMapOf<Int, Int>()
    
    tempProjectDepartments.forEach {
        if (projectDepartmentCount.containsKey(it.project_id)) {
            val accDepartment = projectDepartmentCount.get(it.project_id)!!
            val accEmployee = projectEmployeeCount.get(it.project_id)!!
            
            projectDepartmentCount.put(it.project_id, accDepartment + 1)
            projectEmployeeCount.put(it.project_id, accEmployee + departmentEmployeeCount.get(it.department_id)!!)
        } else {
            projectDepartmentCount.put(it.project_id, 1)
            projectEmployeeCount.put(it.project_id, departmentEmployeeCount.get(it.department_id)!!)
        }
    }
    
    println("3. ${projectDepartmentCount}")
    println("4. ${projectEmployeeCount}\n")
    
    // 사원 ID 1000이 참여하는 모든 프로젝트에 대한 정보
    findProjectsOfEmployee(
        1000, tempEmployees, tempOffices, tempProjectDepartments, tempProjects,
        projectDepartmentCount, projectEmployeeCount
    )

    // 여러 개의 부서에서 진행하는 프로젝트를 부서 간 협업 프로젝트라고 할 때, 모든 부서 간 협업 프로젝트에 대한 정보
    findProjectsOfMultipleDepartments(
        tempProjectDepartments, tempProjects,
        projectDepartmentCount, projectEmployeeCount
    )

    // 사무실 A101에 속한 부서에서 참여하는 모든 프로젝트에 대한 정보
    // = 사무실 A101에 속한 사원들이 참여하는 모든 프로젝트에 대한 정보
    findProjectsOfOffice(
        "A101", tempOffices, tempProjectDepartments, tempProjects,
        projectDepartmentCount, projectEmployeeCount
    )
    
    // 서버개발부가 속한 사무실 중 속한 사원이 3명 이상인 모든 사무실에 대한 정보
    findOfficesOfDepartments("서버개발부", tempOffices, tempDepartments, officeEmployeeCount)
    
    // 2개 이상의 프로젝트를 진행하는 사무실 중 속한 사원이 2명 이상인 사무실을 사용하는 모든 부서에 대한 정보
    findDepartmentInfoOfOfficesWithMultipleProjects(
        tempOffices, tempProjectDepartments, tempDepartments,
        officeEmployeeCount, departmentEmployeeCount
    )
}
```

### 실행 결과
```kotlin
1. {501=2, 502=1, 503=2, 504=3, 505=1, 506=3, 507=2, 508=1}
2. {102=5, 103=5, 100=2, 101=3}
3. {2110=1, 2111=2, 2112=1, 2113=1, 2114=3, 2115=1, 2200=2, 2201=2}
4. {2110=5, 2111=10, 2112=5, 2113=5, 2114=12, 2115=5, 2200=5, 2201=8}

--------

5. 사무실 ID: 501
6. 부서 ID: 102

프로젝트 ID: 2111, 이름: 워너비퍼즐 앱 개발, 중요도: A, 부서 수: 2, 인원: 10
프로젝트 ID: 2112, 이름: 워너비퍼즐 안드로이드 개발, 중요도: B, 부서 수: 1, 인원: 5
프로젝트 ID: 2113, 이름: 워너비퍼즐 iOS 개발, 중요도: B, 부서 수: 1, 인원: 5
프로젝트 ID: 2114, 이름: 코딩왕국 앱개발, 중요도: A, 부서 수: 3, 인원: 12

--------

프로젝트 ID: 2111, 이름: 워너비퍼즐 앱 개발, 중요도: A, 부서 수: 2, 인원: 10
프로젝트 ID: 2114, 이름: 코딩왕국 앱개발, 중요도: A, 부서 수: 3, 인원: 12
프로젝트 ID: 2200, 이름: 앱 마케팅, 중요도: A, 부서 수: 2, 인원: 5
프로젝트 ID: 2201, 이름: 홈페이지 마케팅, 중요도: B, 부서 수: 2, 인원: 8

--------

7. 부서 ID: 102

프로젝트 ID: 2111, 이름: 워너비퍼즐 앱 개발, 중요도: A, 부서 수: 2, 인원: 10
프로젝트 ID: 2112, 이름: 워너비퍼즐 안드로이드 개발, 중요도: B, 부서 수: 1, 인원: 5
프로젝트 ID: 2113, 이름: 워너비퍼즐 iOS 개발, 중요도: B, 부서 수: 1, 인원: 5
프로젝트 ID: 2114, 이름: 코딩왕국 앱개발, 중요도: A, 부서 수: 3, 인원: 12

--------

사무실 ID: 506, 이름: A202, 부서 ID: 103, 인원수: 3

--------

8. 조건 만족 사무실들: [Office(id=501, name=A101, department_id=102), Office(id=503, name=A103, department_id=100), Office(id=504, name=A104, department_id=101), Office(id=506, name=A202, department_id=103), Office(id=507, name=A203, department_id=102)]
9. 부서 ID 목록: [102, 100, 101, 103]

부서 ID: 102, 부서 이름: 클라개발부, 인원수: 5
부서 ID: 100, 부서 이름: 기획부, 인원수: 2
부서 ID: 101, 부서 이름: 마케팅부, 인원수: 3
부서 ID: 103, 부서 이름: 서버개발부, 인원수: 5
```
