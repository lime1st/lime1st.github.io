---
{"dg-publish":true,"permalink":"/illiterate//git-merge/","tags":["git"],"noteIcon":"","created":"2025-02-19T17:57:00","updated":"2025-02-19T22:25:09+09:00"}
---

Git을 활용한 프로젝트를 진행하고 보니 push, pull, commit등을 새롭게 경험하고 있었는데 merge에 자꾸 오류가 생겨 팀원 분이 공유해 주셔서 해결한 방법

# 순서대로 실행해  보기

### 1. git statsh

 현재 작업 중인 변경사항을 임시로 저장하고, 작업 디렉토리를 깨끗한 상태로 되돌리는 기능
 
### 2. git fetch <원격저장소> <브랜치>

나의 경우 merge 되는 브랜치가 dev 였다.

git fetch origin dev

원격 저장소의 dev 브랜치에서 최신 변경 사항을 가져오지만, 로컬 브랜치에는 적용하지 않는다.

### 3. git merge <병합할_브랜치>

git merge origin/dev

로컬 브랜치를 기준으로 원격저장소(origin)의 dev ==브랜치의 변경 사항==을 병합한다. 로컬 브랜치는 그대로이고, 원격의 최신 변경 사항만 반영된다.

##### 주의: 로컬 dev 브랜치와는 무관하다.
로컬 dev와 origin/dev는 다를 수 있으니 주의!

### 4. git stash apply 

1.에서 저장해 둔 작업 변경 사항을 다시 적용하는 명령어
임시로 보관한 작업을 복원하지만, 스택(stash list)에서는 삭제되지 않는다.
stash 된 것 때문에 오류가 생기면 git stash drop 


### stash 명령

git stash list: 저장된 stash 목록 확인
git stash apply: 저장한 작업을 다시 적용(보관 유지)
git stash pop: 저장한 작업을 다시 적용(보관 삭제)
git stash clear: 모든 stash 제거

git stash apply stash@{1}: 저장된 작업을 지정해 적용한다. stash@{1}는 list 명령을 해보면 나온다.
git stash drop stash@{0}: 적용된 stash 제거, stash가 하나라면 stash@{0}는 생략
