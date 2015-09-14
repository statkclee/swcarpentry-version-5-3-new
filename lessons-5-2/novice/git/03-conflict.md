---
layout: lesson
root: ../..
title: 충돌(Conflicts)
---
<div class="objectives" markdown="1">

#### 목표
*   충돌이 무엇이고, 언제 생기는지를 설명한다.
*   병합(머지, merge)로부터 생기는 충돌을 해결한다.

</div>

사람들이 병렬로 작업을 할 수 있게 됨에 따라, 사람들이 누군가의 영역을 침범하게 된다.
혼자서 작업할 경우에도 이런 현상이 발생한다.
소프트웨어 개발을 노트북과 연구실의 서버에서 작업한다면, 각 작업본에 다른 변경사항을 만들 수 있다.
버젼 제어(version control)가 중복 변경사항을 [해결(resolve)](../../gloss.html#resolve)할 수 있는 툴을 제공함으로서 이러한 [충돌(conflicts)](../../gloss.html#conflict)을 관리할 수 있게 한다.

충돌을 어떻게 해소할 수 있는지 확인하기 위해서, 먼저 파일을 생성하자. `mars.txt` 파일은 현재 두 협업하는 사람의 `planets` 저장소에서 다음과 같이 보인다.

~~~
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
~~~
{:class="out"}

**협업하는 한 사람만의 작업본만** 한 줄을 추가하자.

~~~
$ nano mars.txt
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
This line added to Sarah's copy
~~~
{:class="out"}

그리고 변경사항을 GitHub에 푸쉬하자.

~~~
$ git add mars.txt
$ git commit -m "Adding a line in our home copy"
~~~
{:class="in"}
~~~
[master 5ae9631] Adding a line in our home copy
 1 file changed, 1 insertion(+)
~~~
{:class="out"}
~~~
$ git push origin master
~~~
{:class="in"}
~~~
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 352 bytes, done.
Total 3 (delta 1), reused 0 (delta 0)
To https://github.com/vlad/planets
   29aba7c..dabb4c8  master -> master
~~~
{:class="out"}

이제 또다른 협업하는 사람이 GitHub에서 갱신(update)하지 *않고* 변경사항을 작업파일에 만든다.

~~~
$ cd /tmp/planets
$ nano mars.txt
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
We added a different line in the other copy
~~~
{:class="out"}

로컬의 변경사항을 커밋한다.

~~~
$ git add mars.txt
$ git commit -m "Adding a line in my copy"
~~~
{:class="in"}
~~~
[master 07ebc69] Adding a line in my copy
 1 file changed, 1 insertion(+)
~~~
{:class="out"}

하지만 GitHub에는 푸쉬할 수 없다.

~~~
$ git push origin master
~~~
{:class="in"}
~~~
To https://github.com/vlad/planets.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://github.com/vlad/planets.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
hint: before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
~~~
{:class="out"}

<img src="img/conflict.svg" alt="The conflicting changes" />

작업해서 변경한 사항이 다른 사람이 작업한 변경사항과 중첩되는 것을 Git이 탐지해서 앞에서 작업한 것을 뭉개지 않게 정지시킨다.
이제 해야될 일은 GitHub에서 변경사항을 풀(Pull)해서 가져오고 현재 작업중인 작업본과 병합(merge)해서 푸쉬한다. 풀(Pull)부터 시작하자.

~~~
$ git pull origin master
~~~
{:class="in"}
~~~
remote: Counting objects: 5, done.        
remote: Compressing objects: 100% (2/2), done.        
remote: Total 3 (delta 1), reused 3 (delta 1)        
Unpacking objects: 100% (3/3), done.
From https://github.com/vlad/planets
 * branch            master     -> FETCH_HEAD
Auto-merging mars.txt
CONFLICT (content): Merge conflict in mars.txt
Automatic merge failed; fix conflicts and then commit the result.
~~~
{:class="out"}

`git pull` 수행결과 충돌이 있고, 해당 파일에 충돌되는 부분을 표시한다.

~~~
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
<<<<<<< HEAD
We added a different line in the other copy
=======
This line added to Sarah's copy
>>>>>>> dabb4c8c450e8475aee9b14b4383acc99f42af1d
~~~
{:class="out"}

`<<<<<<<` 다음에 `HEAD`에 있는 우리의 변경사항이 있다.
Git이 자동으로 `=======`을 넣어서 충돌되는 변경사항 사이에 구분자로 넣고,  
`>>>>>>>`으로 GitHub에서 다운로드된 파일 내용의 마지막을 표시한다.
(`>>>>>>>` 표시자 다음에 문자와 숫자로 구성된 문자열은 방금 다운로드한 수정 확장자다.)

파일을 편집해서 표시자/구분자를 제거하고 변경사항을 일치하는 것은 전적으로 우리에게 달려있다.
원하는 무엇이든지 할 수 있다. 예를 들어, 로컬 저장소의 변경사항을 반영하든, 원격 저장소의 변경사항을 반영하든, 로컬과 원격 저장소의 내용을 대체하는 새로운 것을 작성하든, 혹은 변경사항을 완전히 제거하는 것도 가능하다. 로컬과 원격 모두 대체해서 파일이 다음과 같이 보이도록 하자.

~~~
$ cat mars.txt
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
We removed the conflict on this line
~~~
{:class="out"}

병합을 마무리하기 위해서, 병합으로 생성된 변경사항을 `mars.txt` 파일에 추가하고 커밋하자.

~~~
$ git add mars.txt
$ git status
~~~
{:class="in"}
~~~
# On branch master
# All conflicts fixed but you are still merging.
#   (use "git commit" to conclude merge)
#
# Changes to be committed:
#
#	modified:   mars.txt
#
~~~
{:class="out"}
~~~
$ git commit -m "Merging changes from GitHub"
~~~
{:class="in"}
~~~
[master 2abf2b1] Merging changes from GitHub
~~~
{:class="out"}

이제 변경사항을 GitHub에 푸쉬할 수 있다. 

~~~
$ git push origin master
~~~
{:class="in"}
~~~
Counting objects: 10, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 697 bytes, done.
Total 6 (delta 2), reused 0 (delta 0)
To https://github.com/vlad/planets.git
   dabb4c8..2abf2b1  master -> master
~~~
{:class="out"}

Git가 병합하면서 수행한 것을 모두 추적하고 있어서, 수작업으로 다시 고칠 필요는 없다.
처음 변경사항을 만든 협력하는 프로그래머가 다시 풀하게 되면, 

~~~
$ git pull origin master
~~~
{:class="in"}
~~~
remote: Counting objects: 10, done.        
remote: Compressing objects: 100% (4/4), done.        
remote: Total 6 (delta 2), reused 6 (delta 2)        
Unpacking objects: 100% (6/6), done.
From https://github.com/vlad/planets
 * branch            master     -> FETCH_HEAD
Updating dabb4c8..2abf2b1
Fast-forward
 mars.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
~~~
{:class="out"}

병합된 파일을 얻게 된다.

~~~
$ cat mars.txt 
~~~
{:class="in"}
~~~
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
We removed the conflict on this line
~~~
{:class="out"}

다시 병합할 필요는 없는데, Git가 다른 누군가 작업을 했다는 것을 알기 때문이다.

충돌되는 변경사항을 병합하는 버젼 제어 기능으로 인해서 사용자가 프로그램이나 논문을 다중 파일로 쪼개서 작업하는 또다른 이유다.
또다른 좋은 점도 있다. 특정 파일에 반복되는 충돌이 있을 때마다, 버젼 제어 시스템은 본직적으로 사용자에게 누가 무엇에 책임이 있는지, 작업을 다르게 나누는 방법을 찾을 수 있도록 명확히 하게 말해준다.

<div class="keypoints" markdown="1">

#### 주요점
*   충돌은 두명 혹은 그 이상의 사람이 동시에 동일한 파일에 변경을 할 때 발생한다.
*   버젼 제어 시스템은 사람들이 모르고 서로의 변경사항을 덮어쓰게 하지 못하게 한다. 대신에 충돌되는 부분을 눈에 부각시켜서 해소되어 일치할 수 있게 한다.

</div>

<div class="challenge" markdown="1">
강사가 생성한 저장소를 복제하세요. 저장소에 새 파일을 추가하고, 기존 파일을 변경하세요. (강사가 변경할 기존 파일이 어느 것인지 알려줄 것이다.) 강사의 말에 따라 충돌을 생성하는 연습을 하기 위해서 저장소에서 변경사항을 가져오도록 풀(Pull)하세요. 그리고 해소해서 일치해 보세요.
</div>

<div class="challenge" markdown="1">
버젼 제어 저장소의 이미지 파일이나 혹은 다른 텍스트가 아닌 파일에 충돌이 발생할 때, Git는 무엇을 하나요?
</div>
