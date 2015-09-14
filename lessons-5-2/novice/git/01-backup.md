---
layout: lesson
root: ../..
title: 더 훌륭한 백업 방법
---
<div class="objectives" markdown="1">

#### 목표
*    컴퓨터마다 요구되는 초기화 및 설정단계, 그리고 저장소마다 필요한 것이 무엇인지 설명한다.
*   파일 한개 혹은 여려개의 파일에 대해서 변경-추가-커밋(modify-add-commit) 주기를 수행하고, 각 단계별로 정보가 어디에 저장되는지를 설명한다.
*   Git 변경 번호를 확인하고 사용한다.
*   파일을 동일한 옛 버젼 파일과 비교한다.
*   옛 버젼 파일을 복원한다.
*   Git 설정을 변경하여 특정 파일을 버젼관리에서 제외하고 왜 제외하는 것이 때때로 유용한지 설명한다.

</div>

누군가가 무엇을 했는지, 언제 했는지를 기억하기 위해서 버젼 관리를 어떻게 사용할 수 있는지 탐험을 시작할 것이다. 다른 사람과 협업을 하지 않더라도, 버젼관리는 훨씬 더 낫다.

<div>
  <a href="http://www.phdcomics.com"><img src="img/phd101212s.gif" alt="Piled Higher and Deeper by Jorge Cham, http://www.phdcomics.com" /></a>
  <p>"Piled Higher and Deeper" by Jorge Cham, http://www.phdcomics.com</p>
</div>

### 설정

처음 Git를 새로운 컴퓨터에 사용할 때, 몇가지 설정이 필요하다.
다음은 Dracula가 새로 구입한 노트북에 어떻게 설정하는지 보여준다.

~~~
$ git config --global user.name "Vlad Dracula"
$ git config --global user.email "vlad@tran.sylvan.ia"
$ git config --global color.ui "auto"
$ git config --global core.editor "nano"
~~~
{:class="in"}

(드라큘라 정보 대신에 자신의 이름과 전자우편 주소를 사용하세요. 윈도우에서는 `notepad` 같은 실제 컴퓨터에서 작동하는 편집기를 선택했는지 확인하세요.)

Git 명령어는 `git verb` 형식으로 작성되고 `verb` 가 실제로 수행하고자 하는 명령어다. 상기의 경우 Git에게 다음을 명령한다.

*   자신의 이름과 전자우편 주소
*   출력 결과를 색깔을 넣어 표현한다.
*   주 사용 텍스트 편집기
*   설정 사항을 전역으로 한다. (즉, 모든 프로젝트에 적용)

상기 4개 명령어는 한번만 실행되면 된다. `--global` 플래그는 Git에게 해당 컴퓨터에서 실행되는 모든 프로젝트에 적용되도록 설정한다.

> #### 프록시(Proxy)
>
> 몇몇 네트워크에서 프록시를 사용할 필요가 있다. 만약 이런 경우라면, 
> 프록시에 대해서 Git 설정을 다음과 같이 변경한다.
>
> ~~~
> $ git config --global http.proxy proxy-url
> $ git config --global https.proxy proxy-url
> ~~~
> {:class="in"}
>
> 프록시를 사용하지 않기 위해서 다음을 사용한다.
>
> ~~~
> $ git config --global --unset http.proxy
> $ git config --global --unset https.proxy
> ~~~
> {:class="in"}

### 저장소 생성하기

Git 설정이 완료되면, Git를 사용할 수 있다. 작업을 위해서 디렉토리를 생성하자.

~~~
$ mkdir planets
$ cd planets
~~~
{:class="in"}

생성된 디렉토리를 Git [저장소(repository)](../../gloss.html#repository)로 만든다. 저장소는 Git가 파일의 옛 버젼을 저장하는 장소다.

~~~
$ git init
~~~
{:class="in"}

`ls`를 사용해서 디렉토리의 내용을 살펴보면, 아무것도 변경된 것이 없는 것처럼 보인다.

~~~
$ ls
~~~
{:class="in"}

하지만, `-a` 플래그를 추가해서 모든 것이 보이도록 한다면, Git가 `.git` 로 불리는 숨겨진 디렉토리를 생성한 것을 볼 수 있다.

~~~
$ ls -a
~~~
{:class="in"}
~~~
.	..	.git
~~~
{:class="out"}

Git는 이 특별한 하위 디렉토리에 프로젝트에 대한 정보를 저장한다. 만약 `.git`를 삭제한다면, 프로젝트 이력을 모두 잃어버린다.

모든 것이 제대로 설정되었는지를 확인을 다음과 같이 프로젝트 상태를 Git 명령어로 수행한다.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
#
# Initial commit
#
nothing to commit (create/copy files and use "git add" to track)
~~~
{:class="out"}

### 파일 변경사항 추적하기

전진기지로서 화성의 적합성에 관한 기록을 담고 있는 `mars.txt` 파일을 생성한다. 
(파일 편집을 위해서 `nano` 편집기를 사용한다. 원하면 무슨 편집기를 사용해도 된다. 특히, 앞에서 전역으로 설정한 core.editor가 될 필요도 없다.)

~~~
$ nano mars.txt
~~~
{:class="in"}

`mars.txt` 파일에 다음 텍스트를 타이핑한다.

~~~
Cold and dry, but everything is my favorite color
~~~
{:class="in"}

`mars.txt` 파일은 이제 한 줄을 포함하고 있다.

~~~
$ ls
~~~
{:class="in"}
~~~
mars.txt
~~~
{:class="out"}
~~~
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
~~~
{:class="out"}

다시 한번 프로젝트의 상태를 확인하고자 하면, Git가 새로운 파일을 하나를 알아차렸다고 말하고 있다.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	mars.txt
nothing added to commit but untracked files present (use "git add" to track)
~~~
{:class="out"}

"untracked files" 메시지가 의미하는 것은 Git가 추적하고 있지 않는 파일 하나가 디렉토리에 있다는 것이다. `git add`를 사용해서 Git에게 추적관리하라고 작업명령을 낼 수 있다.

~~~
$ git add mars.txt
~~~
{:class="in"}

그리고 나서 올바르게 되었는지 확인한다.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#	new file:   mars.txt
#
~~~
{:class="out"}

Git는 `mars.txt` 파일을 추적할 것이라는 것을 알고 있지만, 아직 저장소에는 어떤 변경사항도 기록하지는 않았다. 이를 위해서 명령어 하나를 더 실행할 필요가 있다.

~~~
$ git commit -m "Starting to think about Mars"
~~~
{:class="in"}
~~~
[master (root-commit) f22b25e] Starting to think about Mars
 1 file changed, 1 insertion(+)
 create mode 100644 mars.txt
~~~
{:class="out"}

`git commit`을 실행할 때, Git는 `git add`를 사용해서 저장하려고 하는 모든 대상을 받아서 `.git` 디렉토리 내부에 영구 사본으로 저장한다.
이 영구 사본을 [수정(revision)](../../gloss.html#revision)이라고 하고, 짧은 식별자는 `f22b25e`이다. (여러분의 수정번호의 짧은 확장자는 다를 수 있다.)

`-m` ("message"를 위미) 플래그를 사용해서 나중에 무엇을 왜 했는지 기억에 도움이 될 수 있는 주석을 기록한다. `-m` 옵션 없이 `git commit`을 실행하면, Git는 `nano`(혹은 시초에 설정한 다른 편집기)를 실행해서 좀더 긴 메시지를 작성할 수 있다.

이제 `git status`를 시작하면, 

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
nothing to commit, working directory clean
~~~
{:class="out"}

모든 것이 최신 상태라고 보여준다. 최근에 작업한 것을 알고자 한다면, `git log`를 사용해서 프로젝트 이력을 보여주도록 Git에게 명령어를 보내다.

~~~
$ git log
~~~
{:class="in"}
~~~
commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
Author: Vlad Dracula <vlad@tran.sylvan.ia>
Date:   Thu Aug 22 09:51:46 2013 -0400

    Starting to think about Mars
~~~
{:class="out"}

`git log`는 역 시간순으로 저장소의 모든 변경사항을 나열한다.
각 수정사항 목록은 전체 수정 식별자(앞서 `git commit` 명령어로 출력한 짧은 문자와 동일하게 시작), 수정한 사람, 언제 생성되었는지, 생성할 때 Git에 작성한 로그 메시지를 포함한다. 

> #### 내 변경사항은 어디로 갔을까?
>
> 이 시점에서 `ls` 명령어르 다시 실행하면, `mars.txt` 파일만 덩그러니 보게 된다. 
> 왜냐하면, Git이 앞에서 언급한 `.git` 특수 디렉토리에 파일 변경 이력 정보를 저장했기 때문이다.
> 그래서 파일 시스템이 뒤죽박죽되지 않게 된다. (따라서, 옛 버젼을 실수로 편집하거나 삭제할 수 없다.)

### 파일 변경하기

이제 드라큘라가 파일에 정보를 좀더 추가했다고 가정하자. (다시 한번 `nano`편집기로 편집하고 나서 `cat`으로 파일 내용을 살펴본다. 다른 편집기를 사용할 수도 있고, `cat`으로 파일 내용을 반듯이 볼 필요도 없다.)

~~~
$ nano mars.txt
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
~~~
{:class="out"}

`git status`를 실행하면, 이미 알고 있는 파일이 변경되었다고 알려준다.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   mars.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
~~~
{:class="out"}

마지막 줄이 중요한 문구다: "no changes added to commit".
`mars.txt` 파일을 변경했지만, 아직 Git에게는 변경을 사항(`git add`로 수행)을 저장한다고 말하지는 않았다. `git diff`를 사용해서 작업 내용을 두번 검증한다. `git diff`는 현재 파일의 상태와 가장 최근에 저장된 버젼의 차이를 보여준다.

~~~
$ git diff
~~~
{:class="in"}
~~~
diff --git a/mars.txt b/mars.txt
index df0654a..315bf3a 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1 +1,2 @@
 Cold and dry, but everything is my favorite color
+The two moons may be a problem for Wolfman
~~~
{:class="out"}

출력 결과가 아리송한데 실제로 다른 파일이 주어졌을 때 파일 하나를 어떻게 재구성하는지를 말해주는 `patch`와 편집기 같은 도구를 위한 일련의 명령이기 때문이다.

1.  첫번째 행은 Git이 신규와 옛 버젼 파일을 비교하는 유닉스 `diff` 명령어와 유사한 출력결과를 생성하게 한다.
2.  두번째 행은 정확하게 Git 파일 어느 [수정(revisions)](../../gloss.html#revision)본을 비교하는지 알려준다. `df0654a`과 `315bf3a`은 수정을 위한 목적으로 중복되지 않게 컴퓨터가 생성한 표식이다.
3.  나머지 행은 실제 차이가 나는 것과 어디 행에서 발생했는지 보여준다. 특히 첫번째 열의 `+` 기호는 어디서 행이 추가 되었는지 보여준다.

변경사항을 커밋(commit)하자.

~~~
$ git commit -m "Concerns about Mars's moons on my furry friend"
~~~
{:class="in"}
~~~
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   mars.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
~~~
{:class="out"}

이럴 수가, `git add`을 먼저 하지 않아서 Git가 커밋을 할 수 없다.
고쳐봅시다.

~~~
$ git add mars.txt
$ git commit -m "Concerns about Mars's moons on my furry friend"
~~~
{:class="in"}
~~~
[master 34961b1] Concerns about Mars's moons on my furry friend
 1 file changed, 1 insertion(+)
~~~
{:class="out"}

실제로 무엇을 커밋하기 전에 커밋하고자하는 파일을 먼저 추가하라고 Git이 주장하는데, 왜냐하면 한번에 모든것을 커밋하지 싶지 않을지도 모르기 때문이다.
예를 들어, 작성하고 있는 논문에 지도교수 작업 몇몇 인용을 추가한다고 가정하자. 논문 중간에 인용되는 추가부분과 상응하는 참고 문헌 부분을 커밋하고는 싶지만 결론 부분을 커밋하고는 싶지 *않다*. (아직 결론은 완성되지 않았다.) 

이런 부분을 고려해서, Git은 특별한 준비 영역(staging)이 있어서 현재 [변경부분(change set)](../../gloss.html#change-set)을 추가는 했으나 아직 커밋하지 않는 것을 준비 영역에 기억하고 있다. 
`git add`를 하면 이 영역에 작업 중 해당하는 것을 놓고 
`git commit`이 커밋으로 장기 저장소로 복사한다.

<img src="img/git-staging-area.svg" alt="The Git Staging Area" />

편집기의 파일을 준비 영역으로 그리고, 장기 저장소로 옮기는 것을 살펴보자.
먼저 파일에 또 하나의 행을 추가한다.

~~~
$ nano mars.txt
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}
~~~
$ git diff
~~~
{:class="in"}
~~~
diff --git a/mars.txt b/mars.txt
index 315bf3a..b36abfd 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1,2 +1,3 @@
 Cold and dry, but everything is my favorite color
 The two moons may be a problem for Wolfman
+But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}

지금까지 좋다. 파일의 끝에 행을 하나 추가했다. (첫 열에 `+`이 보인다.)
이제, 준비영역에 변경 사항을 놓고, `git diff` 명령어가 보고하는 것을 살펴보자.

~~~
$ git add mars.txt
$ git diff
~~~
{:class="in"}

출력결과가 없다. Git말할 수 있는 것은 영구히 저장되는 것과 현재 디렉토리에 작업하고 있는 것과 차이가 없다. 하지만, 다음과 같이 명령어를 친다면,

~~~
$ git diff --staged
~~~
{:class="in"}
~~~
diff --git a/mars.txt b/mars.txt
index 315bf3a..b36abfd 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1,2 +1,3 @@
 Cold and dry, but everything is my favorite color
 The two moons may be a problem for Wolfman
+But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}

마지막으로 커밋된 변경사항과 준비 영역(Staging)에 있는 것과 차이를 보여준다. 변경사항을 저장하자.

~~~
$ git commit -m "Thoughts about the climate"
~~~
{:class="in"}
~~~
[master 005937f] Thoughts about the climate
 1 file changed, 1 insertion(+)
~~~
{:class="out"}

현재 상태를 확인하자.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
nothing to commit, working directory clean
~~~
{:class="out"}

그리고 지금까지 작업한 이력을 살펴보자.

~~~
$ git log
~~~
{:class="in"}
~~~
commit 005937fbe2a98fb83f0ade869025dc2636b4dad5
Author: Vlad Dracula <vlad@tran.sylvan.ia>
Date:   Thu Aug 22 10:14:07 2013 -0400

    Thoughts about the climate

commit 34961b159c27df3b475cfe4415d94a6d1fcd064d
Author: Vlad Dracula <vlad@tran.sylvan.ia>
Date:   Thu Aug 22 10:07:21 2013 -0400

    Concerns about Mars's moons on my furry friend

commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
Author: Vlad Dracula <vlad@tran.sylvan.ia>
Date:   Thu Aug 22 09:51:46 2013 -0400

    Starting to think about Mars
~~~
{:class="out"}

요약하면, 변경사항을 저장소에 추가하고자 할 때, 먼저 변경된 파일을 준비 영역(Staging)에 `git add` 명령어를 추가하고, 준비 영역의 변경사항을 저장소에 `git commit` 명령어로 최종 커밋한다.

<img src="img/git-committing.svg" alt="The Git Commit Workflow" />

### 이력 탐험하기.

언제 변경한 것을 변경했는지를 알기 위해서는 `git diff`를 다시 사용한다. 하지만 오래된 버젼은 `HEAD~1`, `HEAD~2`, 등등 표기를 사용하여 참조한다.

~~~
$ git diff HEAD~1 mars.txt
~~~
{:class="in"}
~~~
diff --git a/mars.txt b/mars.txt
index 315bf3a..b36abfd 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1,2 +1,3 @@
 Cold and dry, but everything is my favorite color
 The two moons may be a problem for Wolfman
+But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}
~~~
$ git diff HEAD~2 mars.txt
~~~
{:class="in"}
~~~
diff --git a/mars.txt b/mars.txt
index df0654a..b36abfd 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1 +1,3 @@
 Cold and dry, but everything is my favorite color
+The two moons may be a problem for Wolfman
+But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}

이런 방식으로, 연쇄 수정 사슬을 만들 수 있다. 가장 최근 사슬의 끝값은 `HEAD`로 참조된다. `~` 표기법을 사용하여 앞선 수정을 참조할 수 있다. 그래서 `HEAD~1`("head" 빼기 1로 읽는다.)은 "바로 앞선 수정"을 의미하고, `HEAD~123`은 지금 있는 위치에서 123번째 이전 수정으로 간다는 의미다.

수정된 것을 `git log` 명령어가 화면에 뿌려주는 숫자와 문자로 구성된 긴 문자열을 사용하여 참조할 수도 있다. 변경사항에 대한 중복되지 않는 ID로 "중복되지 않는(unique)"의 의미는 정말 유일하다는 의미다. 특정 컴퓨터에 있는 임의의 파일 집합에 모든 변경사항은 중복되지 않는 40 문자 식별자가 붙어있다. 첫번째 커밋은 ID로 f22b25e3233b4645dabd0d81e651fe074bd8e73b 이 주어졌다. 그래서 다음과 같이 시도하자.

~~~
$ git diff f22b25e3233b4645dabd0d81e651fe074bd8e73b mars.txt
~~~
{:class="in"}
~~~
diff --git a/mars.txt b/mars.txt
index df0654a..b36abfd 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1 +1,3 @@
 Cold and dry, but everything is my favorite color
+The two moons may be a problem for Wolfman
+But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}

올바든 정답이지만, 난수 40 문자로 된 문자열을 타이핑하는 것은 매우 귀찮은 일이다. 그래서 Git 앞의 몇개 문자만으로 사용할 수 있게 했다.

~~~
$ git diff f22b25e mars.txt
~~~
{:class="in"}
~~~
diff --git a/mars.txt b/mars.txt
index df0654a..b36abfd 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1 +1,3 @@
 Cold and dry, but everything is my favorite color
+The two moons may be a problem for Wolfman
+But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}

### 옛 버젼 복구하기

좋았어요. 파일에 변경사항을 저장할 수 있고 변경된 것을 확인할 수 있다. 어떻게 옛 버젼의 파일을 되살릴 수 있을까? 우연히 파일을 덮어썼다고 가정하자.

~~~
$ nano mars.txt
$ cat mars.txt
~~~
{:class="in"}
~~~
We will need to manufacture our own oxygen
~~~
{:class="out"}

이제 `git status`를 통해서 파일이 변경되었다고 하지만, 변경사항은 아직 준비영역(Stagin)에 옮겨지지 않은 것을 확인한다.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   mars.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
~~~
{:class="out"}

`git checkout` 명령어를 사용해서 과거에 있던 상태로 파일을 돌려 놓을 수 있다.

~~~
$ git checkout HEAD mars.txt
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}

이름에서 유추할 수 있듯이, `git checkout` 명령어는 파일의 옛 버젼을 확인한다. 즉, 되살린다. 이 경우 `HEAD`에 기록된 가장 최근에 저장된 파일 버젼을 되살린다. 좀더 오래된 버젼을 되살리고자 한다면, 대신에 수정 식별자를 사용한다.

~~~
$ git checkout f22b25e mars.txt
~~~
{:class="in"}

실행 취소를 하는 변경을 하기 *전에** 저장소 상태를 확인하는 수정 번호를 사용해야 한다는 것을 기억하는 것은 중요하다. 흔한 실수는 제거하려는 변경하려고 사용한 커밋 수정 번호를 사용하는 것이다.  아래 예제에서는 수정 번호가 `f22b25e`인 가장 최신 커밋(`HEAD~1`) 앞의 상태로 다시 돌아가고자 한다.

<img src="img/git-checkout.svg" alt="Git Checkout" />

다음 도표는 파일 이력이 어떻게 보이는지 예시로 보여준다. (가장 최근에 커밋된 버젼 `HEAD`에서 거슬러 올라간다.)

<img src="img/git-when-revisions-updated.svg" alt="When Git Updates Revision Numbers" />

> #### 흔한 경우를 간단히
>
> `git status` 출력 결과를 주의 깊이 읽게 되면, 힌트를 포함한 것을 알게 된다.
>
> ~~~
> (use "git checkout -- <file>..." to discard changes in working directory)
> ~~~
> {:class="in"}
>
> 출력 결과가 말해주듯이, 버젼 식별자 없는 `git checkout` 명령어가 `HEAD`에 
> 저장된 상태로 파일을 되돌린다.
> 이중 대쉬 `--`가 명령어와 구분하기 위해서 파일 이름과 되살리려는 파일 이름을 구별하는데 필요하다.
> 이중 대쉬가 없다면, Git는 파일 이름을 수정 식별자로 사용하려고 한다.

파일이 하나씩 하나씩 옛 상태로 돌아간다는 사실이 사람들이 작업을 조직하는 방식에 변화를 주는 경향이 있다. 모든 것이 하나의 큰 문서로 되어있다면, 결론부분에 후에 만든 변경없이 소개부분에 변경을 다시 되돌리기가 어렵다.(하지만 불가능하지는 않다.) 다른 한편으로 만약 소개부분과 결론부분이 다른 파일에 저장되어 있다면, 시간 앞뒤로 이동하기가 훨씬 쉽다.

### 파일 무시하기

만약 Git가 추적하기 원하지 않는 파일이 있다면 어떨까요? 편집기에서 자동 생성되는 백업파일 혹은 자료 분석 중에 생성되는 중간물 파일을 예가 된다. 몇개 더미(dummy) 파일을 생성하자.

~~~
$ mkdir results
$ touch a.dat b.dat c.dat results/a.out results/b.out
~~~
{:class="in"}

그려면 Git은 다음을 보여준다.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	a.dat
#	b.dat
#	c.dat
#	results/
nothing added to commit but untracked files present (use "git add" to track)
~~~
{:class="out"}

벼젼 관리아래 파일을 놓는 것은 디스크 공간 낭비다. 더 좋은 않는 것은 파일을 모두 목록에 넣는 것은 실질적으로 중요한 변경사항을 놓치게 할 수도 있다. 그래서 Git에게 중요하지 않는 파일을 무시하게 하자.

`.gitignore`로 불리는 파일을 프로젝트 루트 디렉토리에 생성해서 무시할 것을 명기해서 수행한다.

~~~
$ nano .gitignore
$ cat .gitignore
~~~
{:class="in"}
~~~
*.dat
results/
~~~
{:class="out"}

상기 패턴은 `.dat` 확장자를 가지는 임의의 파일과 `results` 디렉토리에 있는 모든 것을 무시한다. (하지만, 이들 파일 중의 일부가 이미 추적되고 있다면, Git는 계속 추적한다.)

`.gitignore` 파일을 생성하자마자 `git status` 출력결과는 훨씬 깨끗하다.

~~~
$ git status
~~~
{:class="in"}
~~~
# On branch master
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	.gitignore
nothing added to commit but untracked files present (use "git add" to track)
~~~
{:class="out"}

이제 Git가 알아차리는 유일한 것은 새로 생성된 `.gitignore` 파일이다. 우리는 이들 파일을 추적하여 관리하지 않는다고 생각할 수도 있지만, 우리와 저장소를 공유하고 있는 모든 사람은 우리가 추적관리하지 않는 동일한 것을 추적관리하고 싶지 않을 것이다. `.gitignore` 를 추가해서 커밋하자.

~~~
$ git add .gitignore
$ git commit -m "Add the ignore file"
$ git status
~~~
{:class="in"}
~~~
# On branch master
nothing to commit, working directory clean
~~~
{:class="out"}

보너스로, `.gitignore`는 실수로 원하지 않는 파일을 저장소에 추가하는 것을 피하게 돕는다.

~~~
$ git add a.dat
~~~
{:class="in"}
~~~
The following paths are ignored by one of your .gitignore files:
a.dat
Use -f if you really want to add them.
fatal: no files added
~~~
{:class="out"}

만약 `.gitignore` 설정을 우선하여 파일을 추가하려면, `git add -f`를 사용해서 강제로 Git에 파일을 추가할 수 있다. 추적관리되지 않는 파일의 상태를 항상 보려면 다음을 사용한다.

~~~
$ git status --ignored
~~~
{:class="in"}
~~~
# On branch master
# Ignored files:
#  (use "git add -f <file>..." to include in what will be committed)
#
#        a.dat
#        b.dat
#        c.dat
#        results/

nothing to commit, working directory clean
~~~
{:class="out"}

<div class="keypoints" markdown="1">

#### 주요점
*   컴퓨터마다 사용자 이름, 전자우편, 편집기 그리고 다른 환경설정 사항을 `git config`로 지정한다. 
*   `git init` 명령어는 저장소를 초기화한다.
*   `git status` 명령어는 저장소 상태를 보여준다.
*   파일은 사용자가 볼 수 있는 프로젝트 작업 디렉토리, 다음번 커밋이 구축되는 준비 영역(Staging), 그리고 스냅샷(snapshot)이 항상 기록되는 로컬 저장소에 저장될 수 있다.
*   `git add` 명령어는 파일을 준비 영역에 놓는다.
*   `git commit` 명령어는 준비 영역의 스냅샷을 로컬 저장소에 생성한다.
*   변경사항을 커밋할 때마다 항상 로그 메시지를 작성하라.
*   `git diff` 명령어는 수정사항 사이의 차이를 화면에 출력한다.
*   `git checkout` 명령어는 옛 버전 파일을 복구한다.
*   `.gitignore` 파일은 Git가 무시하여 기록관리하지 않는 파일 정보를 담고 있다.

</div>

<div class="challenge" markdown="1">

컴퓨터에 `bio`라는 신규 Git 저장소를 생성하세요. `me.txt` 파일에 3줄 자신의 약력을 작성하고 변경사항을 커밋하세요. 그리고 나서, 한줄을 변경하고 4번째 행을 추가해서 변경상태와 최초 상태의 차이를 화면에 출력하세요.
</div>

<div class="challenge" markdown="1">
다음 일련의 명령어는 Git 저장소 내부에 또다른 Git 저장소를 생성하는 것이다.

~~~
cd           # return to home directory
mkdir alpha  # make a new directory alpha
cd alpha     # go into alpha
git init     # make the alpha directory a Git repository
mkdir beta   # make a sub-directory alpha/beta
cd beta      # go into alpha/beta
git init     # make the beta sub-directory a Git repository
~~~
{:class="in"}

이렇게 하는 것이 왜 좋은 생각이 아닐까요?
</div>
