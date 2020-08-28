# Today I learned



### git `remote` & `branch`

* 리모트 저장소를 Pull 하거나 Fetch 하기

  * git `pull` or `fetch` <리모트 저장소 이름>

    * `pull`과 `fetch`의 차이는 pull은 merge시킨다.

  * 이 명령은 로컬에는 없지만, 리모트 저장소에 있는 데이터를 모두 가져온다.

    > *fatal: refusing to merge unrelated histories*

    * 위의 오류 메시지가 나올때는 관련없는 기록 병합을 거부 하는 것인데 아래의 명령으로 해결 가능

    * git pull <리모트 저장소 이름> <브랜치 이름> --allow-unrelated-histories

  

* 리모트 저장소에 Push 하기

  * git push <리모트 저장소 이름> <브랜치 이름>
  * 다른 사람이 먼저 `Push` 했다면, 다른 사람이 작업한 내용을 `Merge` 시킨 후, Push가 가능

  

* 리모트 저장소 정보 확인하기

  * `git remote show <리모트 저장소 이름>
  * 이 명령은 remote 저장소의 구체적인 정보를 확인할 수 있다.

  

* 리모트 저장소 이름 바꾸기

  * git remote rename <바꾸고 싶은 리모트 이름> <바꿀 이름>



### git `Refs`

* `Refs`는 리모트 저장소에 있는 브랜치, 태그 등등을 의미한다.
* `git ls-remote` <리모트 저장소 이름>
  * 이 명령으로 모든 리모트 Refs를 조회할 수 있다.

