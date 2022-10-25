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
