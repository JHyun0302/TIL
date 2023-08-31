#git 학습

## 스테이징 영역

- 스테이징 영역은 커밋 전, 커밋에 포함시킬 파일들만 임시로 올려두는 영역
    - stage : 무대에 올리다.
- `.gitignore` 파일
    - 커밋에 포함되면 안되는 파일들을 관리하는 파일
    - 저장소마다 1개씩 세팅되어있는게 일반적임
    - [Toptal의 gitignore generator](https://www.toptal.com/developers/gitignore)를 사용해서 운영체제, 개발 환경(IDE), 사용하는 프로그래밍 언어에
      맞는 gitignore 파일을 생성할 수 있음
        - 이 외에도 VSCode에 Extension을 설치해 gitignore 파일을 관리하기도 함

  ## 브랜치
  - 
    - branch: (영한사전 정의)나뭇가지
    - (일반적으로) 일감을 해결할 때 일감에 해당하는 이슈를 만들고, 이슈에 해당하는 브랜치를 생성함

  ### 브랜치 병합
    - 이슈에 해당하는 브랜치에서 작업을 끝내고 주요 브랜치인  `main`, `development`등에 병합 요청을 하게된다.
    - 병합 요청이 승인이 되면 내가 작업하는 브랜치가 주요 브랜치로 병합된다.

  #### 충돌
    - 두 브랜치에서 같은 코드를 수정한 후, 병합 하려고 할 때 발생한다.
    - 충돌이 발생하면, 충돌이 발생한 파일을 열어서 최종적으로 남기고 싶은 코드만 남긴다(특수 문자는 삭제).
    - `git add`, `git commit` 으로 병합을 마무리한다.

  ### 명령어
    - 브랜치 생성: `git branch 브랜치명`
    - 생성된 브랜치 확인: `git branch`
    - 브랜치 전환
        1. `git switch 브랜치명`
        2. `git checkout 브랜치명`
    - 브랜치 생성 및 전환
        1. `git checkout -b 브랜치명`
    - 브랜치 병합: `git merge`
        - 주의: `A` 브랜치를 `B`브랜치로 병합 하려고 할 때, **`B` 브랜치가 체크아웃** 되어있는 상태에서 `git merge A`를 입력해야 함

  ### 강제 커밋
    - reset이나 revert를 해야하는 경우 간혹 발생
    - 예제 시나리오
        1. 리모트(remote)에서 로컬로 `git pull <branch name>` 을 통해 로컬 브랜치로 동기화
        2. `git reset --hard HEAD~` 으로 이전 커밋으로 변경
        3. `git push origin <branch name>` 으로 리모트(remote) 서버에 업데이트 시도
    - 문제 발생 : 리모트(remote)가 로컬 브랜치보다 최신정보 가지고 있기 때문
    - 즉, `git pull ...` 을 통해 로컬을 최신화 시키라는 의미

    - **강제 푸시** : `git --force push origin <branch name>`

  ### Cherry-pick
    - 예제 시나리오
        1. A 브랜치에서 회원가입 기능 개발해야됨
        2. 실수로 B 브랜치에서 회원가입 기능 개발하고 커밋까지 해버림
    - 문제 : B 브랜치에 커밋 내용을 A 브랜치로 가져오고 싶음

    - **Cherry-pick** : `git cherry-pick <commit hash>`
    - 단, A 브랜치에서 실행 할 것!
    - 여러 커밋 한번에 반영
        1. `git cherry-pick <commit hash1> <commit hash2> <commit hash3>`
        2. `git cherry-pick <commit hash1>...<commit hash3>`
            - 단, 2번 같은 경우 1번 커밋은 반영되지 않음. hash2 ~ hash3만 적용됨

  #### Cherry-pick 중 충돌 발생
    - Cherry-pick 중단 : `git cherry-pick -abort`
    - Cherry-pick 실행하기 전으로 돌아감

    - Cherry-pick 충돌 수정 후 실행
        1. vi $file_path # 파일 수정
        2. git add $file_path
        3. `git cherry-pick -continue`

  #### merge 커밋 Cherry-pick
    - `git cherry-pick -m 1 <merge 커밋 해시>`

  ### 최근 커밋 메시지 변경
    - 가장 최근 커밋 메시지 변경 : `git commit --amend`
    - 편집기 열지 않고 메시지 변경 : `git commit --amend -m "an updated commit message"`

  ### Reset
    - 커밋을 되돌리는 명령어 (단, 나만 작업한 브랜치를 되돌릴 때 사용)
    - 커밋 바로 이전 상황으로 되돌리기 : `git reset HEAD^`
    - 여러개 커밋 되돌리기 : `git reset HEAD~2`

  #### Reset 옵션
    1. **--hard** : 돌아간 커밋 이후의 변경 이력 모두 삭제
        - `git reset --hard [commit hash1]` : 1번 커밋으로 변경되면서 변경 이력 모두 삭제
    2. **--mixed** : 변경 이력 모두 삭제하지만 변경 내용은 남고 unStage 상태로 둠
        - `git reset --mixed [commit hash1]` : 변경 이력은 삭제되지만 코드는 unStage 상태로 남음 (git add 필요 O)
    3. **--soft** : 변경 이력 모두 삭제하지만 변경 내용은 남고 stage 상태로 둠
        - `git reset --soft [commit hash1]` : 변경 이력은 삭제되지만 코드는 stage 상태로 남음 (git add 필요 X)

  ### **reset --hard**로 잘못 삭제한 커밋 되살리기 : `git reflog`
    1. `git reflog` : 이전 커밋 목록 모두 출력
    2. `git checkout [commit hash]` : 복원하고 싶은 커밋 해시 체크 아웃

  ### Revert
    - 커밋을 되돌리는 명령어 (단, 다른 사람과 코드를 공유한 경우 되돌릴 때 사용)
    - **revert 시 해당 커밋의 내용만 삭제**
    - git log : `Revert "commit hash1"`
    - 예제 시나리오
        1. A 브랜치에서 작성하고 main 브랜치에 merge
        2. 몇일 후 merge 코드 때문에 버그가 발생
        3. 하지만 main 브랜치이기 때문에 협업하는 사람과 코드를 공유해서 버그가 번짐

    - **revert** : `git revert [commit hash1]`
    - 여러 커밋 되돌리기
        - `git revert [commit hash1]..[commit hash3]`
        - git log : `Revert "commit hash1"  Revert "commit hash2"  Revert "commit hash3"`

    - revert 커밋 메시지 변경
        1. `git revert --no-commit [commit hash]`
        2. `git commit -m "revert한 이유"`
        3. `git push`

  ### Rebase
    - A 브랜치에서 작업한 내용을 main 브랜치로 이동하고 싶을 때

    - **rebase** : main 브랜치를 base 삼아 A 브랜치를 이동
        - `git checkout A`
        - `git rebase main`
        - 이후
        - `git checkout main`
        - `git merge A`

  #### Rebase 장점
    1. 공유 브랜치에 최신 커밋을 반영하면서 작업해야 할 때
    2. 커밋 이력이 남지 않으므로 커밋 History가 깔끔해짐

  #### Rebase 단점 아닌 단점 : **A 브랜치 각각의 커밋마다 충돌을 해결해야 함**
    - 머지는 충돌 발생시 1번만에 처리 가능하지만 Rebase는 커밋 전체를 옮기기 때문에 각각의 커밋마다 충돌을 해결해야함.
    - 하지만, 어느 커밋에서 충돌난지 모른채 하나의 커밋으로 충돌을 해결하는 것은 오히려 독이 될 수 있다.

  ### Stash : 아직 마무리되지 않은 작업을 스택에 잠시 저장하는 명령어
    - 예제 시나리오 : A 브랜치에서 작업 하던 중 main 브랜치에 에러 발생해서 main 브랜치로 이동해야 함
    - A 브랜치에서 : `git stash`
    - `git checkout main` & 작업 진행
    - 작업 끝나면 `git checkout A_branch`
    - `git stash apply`

  #### Stash 관련 명령어
    - `git stash list` : stash 목록 확인
    - `git stash apply` : 가장 최근에 저장한 작업 불러오기
        - `git stash apply [stash 이름]` : 해당 stash 불러오기 (ex. stash@[2])
        - `git stash apply --index` : Staged 상태로 복원
    - `git stash drop` : 가장 최근에 저장한 작업 삭제
        - `git stash apply [stash 이름]` : 해당 stash 삭제 (ex. stash@[2])
        - `git stash pop` : **apply + drop**
    - `git stash show -p | git apply -R` : stash 되돌리기 (가장 최근에 저장한 작업으로 패치 만들고 거꾸로 적용)

    
