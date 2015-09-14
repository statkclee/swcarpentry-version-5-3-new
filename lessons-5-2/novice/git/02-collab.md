---
layout: lesson
root: ../..
title: 협업(Collaboration)
---
<div class="objectives" markdown="1">

#### 목표
*   원격 저장소가 무엇인지 그리고 왜 원격 저장소가 유용한지 설명한다.
*   원격 저장소가 복제(clone)되었을 때 무엇이 발생하는지 설명한다.
*   원격 저장소에서 변경사항이 푸쉬(push)와 풀(pull)되었을 때 무엇이 발생하는지 설명한다.

</div>

버젼 제어(version control)는 다른 사람과 협업할 때 진정 찾아온다. 우리는 이미 버젼 제어를 위해서 필요한 컴퓨터의 거의 모든 것을 가졌다. 한가지 빠진 것은 한 저장소에서 다른 저장소로 변경사항을 복사하는 것이다.

Git 같은 시스템은 임의의 두 저장소 사이에 작업을 옮길 수 있게 한다. 하지만, 실무에서는 중앙 허브에 하나의 원본을 두고 다른 사람의 노트북이나 PC보다는 웹에 두고 사용하는 것이 가장 쉽다. 대부분의 프로그래머는 프로그램 마스터 원본을 [GitHub](http://github.com) 혹은 [BitBucket](http://bitbucket.org)같은 호스팅 서비스에 두고 사용한다. 이번 학습의 마지막 부분에서 이러한 접근법의 장점과 단점을 살펴본다.

세상 사람들과 현재 프로젝트에서 변경한 사항을 공유하는 것에서 시작하자. GitHub에 로그인하고 우측 상단의 아이콘을 클릭해서 `planets`으로 신규 저장소를 생성하자.

<img src="img/github-create-repo-01.png" alt="Creating a Repository on GitHub (Step 1)" />

저장소 이름을 "planets"으로 만들고 "Create Repostiory"를 클릭한다.

<img src="img/github-create-repo-02.png" alt="Creating a Repository on GitHub (Step 2)" />

저장소가 생성되자 마자, Git는 URL을 가진 페이지와 사용자의 로컬 저장소 환경설정을 어떻게 하는지 정보를 화면에 출력한다.

<img src="img/github-create-repo-03.png" alt="Creating a Repository on GitHub (Step 3)" />

다음 명령어는 효과적으로 GitHub 서버에 다음을 수행한다.

~~~
$ mkdir planets
$ cd planets
$ git init
~~~
{:class="in"}

현재 로컬 저장소는 여전히 `mars.txt` 파일의 초기 작업을 담고 있다. 하지만, GitHub의 원격 저장소는 아직 어떠한 파일도 담고 있지는 않다.

<img src="img/git-freshly-made-github-repo.svg" alt="Freshly-Made GitHub Repository" />

다음 단계는 두 저장소를 연결하는 것이다. 로컬 저장소에 GitHub 저장소를 [원격(remote)](../../gloss.html#remote-repository)으로 만들어서 두 저장소를 연결한다.
GitHub에 저장소 홈페이지는 문자열을 포함하고 있어서 식별할 수 있다.

<img src="img/github-find-repo-string.png" alt="Where to Find Repository URL on GitHub" />

SSH에서 HTTPS로 [프로토콜(protocol)](../../gloss.html#protocol)을 변경하려면 'HTTPS' 링크를 클릭한다.

> #### HTTPS vs SSH
>
> 부가적인 설정이 필요하지 않아서 여기서는 HTTPS를 사용한다. 워크샵 후에 
> SSH 접근 설정을 원할지도 모른다. SSH 접근이 좀더 안전하다. 
> [GitHub](https://help.github.com/articles/generating-ssh-keys),
> [Atlassian/BitBucket](https://confluence.atlassian.com/display/BITBUCKET/Set+up+SSH+for+Git)
> 그리고 [GitLab](https://about.gitlab.com/2014/03/04/add-ssh-key-screencast/)
> (this one has a screencast)에 훌륭한 지도서 중 하나를 따라하는 것도 좋다.
>
> SSH에 대해서 좀더 알고자 하면 [작은 학습](../extras/06-ssh.html)에 
> 여러분을 초대한다.

<img src="img/github-change-repo-string.png" alt="Changing the Repository URL on GitHub" />

웹 브라우져에서 URL을 복사하고 `planets` 저장소로 가서 다음 명령어를 실행한다.

~~~
$ git remote add origin https://github.com/vlad/planets
~~~
{:class="in"}

Vlad가 아니고 여러분의 저장소의 URL을 사용했는지 확인한다.
유일한 차이점은 `vlad` 대신에 여러분의 사용자이름(username)이다.

`git remote -v` 실행해서 명령어가 제대로 작동했는지 확인한다.

~~~
$ git remote -v
~~~
{:class="in"}
~~~
origin   https://github.com/vlad/planets.git (push)
origin   https://github.com/vlad/planets.git (fetch)
~~~
{:class="out"}

`origin` 이름은 원격 저장소의 로컬 별명이다. 원한다면 다른 이름을 사용할 수도 있지만, `origin` 이름은 가장 일반적인 선택이다. 

별명이 `origin`으로 설정되면, 다음 명령어가 변경사항을 로컬 저장소에서 GitHub 원격 저장소로 밀어 넣어 푸쉬(push)한다.

~~~
$ git push origin master
~~~
{:class="in"}
~~~
Counting objects: 9, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 821 bytes, done.
Total 9 (delta 2), reused 0 (delta 0)
To https://github.com/vlad/planets
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
~~~
{:class="out"}

> ##### 프록시(Proxy)
>
> 만약 연결된 네트워크가 프록시를 사용한다면, 오류 메시지로 
> "Could not resolve hostname"으로 마지막 명령어가 실패할 가능성이 있다. 
> 이것을 해결하기 위해서 프록시에 대한 사항을 Git에 전달할 필요가 있다.
>
> ~~~
> $ git config --global http.proxy http://user:password@proxy.url
> $ git config --global https.proxy http://user:password@proxy.url
> ~~~
> {:class="in"}
>
> 프록시를 사용하지 않는 또다른 네트워크에 연결될 때, Git에게 프록시 기능을 사용하지 않도록 다음 명령어르 사용하여 전달한다.
>
> ~~~
> $ git config --global --unset http.proxy
> $ git config --global --unset https.proxy
> ~~~
> {:class="in"}

> #### 비밀번호 관리자(password manager)
>
> 운영체제가 비밀번호 관리자로 설정이 되어있다면, 사용자이름(username)과 
> 비밀번호(passord)가 필요할 때, `git push` 명령어는 사용하려고 한다. 
> 비밀번호 관리자를 사용하는 대신에 터미널에서 사용자이름과 비밀번호를
> 입력하려고 한다면, 다음과 같이 타이핑한다.
>
> ~~~
> $ unset SSH_ASKPASS
> ~~~
> {:class="in"}
>
> 디폴트로 설정하기 위해서는 `~/.bashrc` 끝에 상기 명령어를 추가하면 된다.

이제 로컬 저장소와 원격 저장소는 다음과 같은 상태가 된다.

<img src="img/github-repo-after-first-push.svg" alt="GitHub Repository After First Push" />

> #### '-u' 플래그(flag)
>
> Git 문서에 `git push`과 함께 사용되는 `-u` 옵션을 볼 수 있다. 중급 학습에서 
> 다루는 개념과 연관되어서 여기서는 넘어가도록 한다.

또한, 원격 저장소에서 로컬 저장소를 변경사항을 풀(pull)해서 가져올 수도 있다.

~~~
$ git pull origin master
~~~
{:class="in"}
~~~
From https://github.com/vlad/planets
 * branch            master     -> FETCH_HEAD
Already up-to-date.
~~~
{:class="out"}

이 경우 가져오는 풀(pull)은 아무런 결과가 없는데 두 저장소가 이미 동기화가 되어서다. 하지만, 만약 누군가 GitHub 저장소에 변경사항을 푸쉬했다면, 상기 명령어는 변경된 사항을 로컬 저장소로 다운로드한다.

다음 단계로, 함께 작업하는 것을 살펴보자. 협업을 위해서 사용할 GitHub의 저장소 하나를 고른다.

> #### 혼자 훈련하기
>
> 스스로 학습을 해왔다면, 두번째 터미널 윈도우를 열고 다른 
> 디렉토리(예를 들어, `/tmp`)로 바꾸어 계속 진행할 수 있다.
> 이 두번째 윈도우가 다른 컴퓨터에서 작업하는 여러분의 협력자를 대표한다. 
> GitHub에 접근권한을 다른 사람에게 줄 필요는 없다. 
> 왜냐하면 두 윈도우 모두 여러분이기 때문이다.

사용되고 있는 저장소의 협력자가 다른 사람에게 접근할 수 있도록 권한을 줄 필요가 있다. GitHub에 오른쪽에 'setting' 버튼을 클릭해서 협력자의 사용자이름을 입력한다.

<img src="img/github-add-collaborators.png" alt="Adding collaborators on Github" />

또 다른 협력자는 `cd` 명령어로 또 다른 디렉토리로 가서 (`ls` 명령어로 `planets` 폴더를 볼 수 없다.), 자신의 컴퓨터에 저장소 사본을 만든다.

~~~
$ git clone https://github.com/vlad/planets.git
~~~
{:class="in"}

'vlad'를 여러분 협력자의 사용자이름(저장소를 소유하고 있는 사람)으로 바꾸세요.

`git clone` 명령어는 원격 저장소와 동일한 새로운 로컬 사본을 생성한다.

<img src="img/github-collaboration.svg" alt="After Creating Clone of Repository" />

이제 새로운 협력자가 자신의 저장소에 변경사항을 만들 수 있다.

~~~
$ cd planets
$ nano pluto.txt
$ cat pluto.txt
~~~
{:class="in"}
~~~
It is so a planet!
~~~
{:class="out"}
~~~
$ git add pluto.txt
$ git commit -m "Some notes about Pluto"
~~~
{:class="in"}
~~~
 1 file changed, 1 insertion(+)
 create mode 100644 pluto.txt
~~~
{:class="out"}

그리고, 변경사항을 GitHub에 푸쉬한다.

~~~
$ git push origin master
~~~
{:class="in"}
~~~
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 306 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/vlad/planets.git
   9272da5..29aba7c  master -> master
~~~
{:class="out"}

주목할 점은 `origin` 이라는 원격 저장소를 생성할 필요는 없다. 저장소를 복제(clone)할때 사용한 이름을 Git이 자동적으로 사용해서 수행한다. (수작업으로 원격 설정을 할 때 앞에서 왜 `origin` 이름이 현명한 선택인 이유다.)

이제 우리의 컴퓨터에 GitHub 원본 저장소의 변경사항을 다운로드할 수 있다.

~~~
$ git pull origin master
~~~
{:class="in"}
~~~
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0)
Unpacking objects: 100% (3/3), done.
From https://github.com/vlad/planets
 * branch            master     -> FETCH_HEAD
Updating 9272da5..29aba7c
Fast-forward
 pluto.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 pluto.txt
~~~
{:class="out"}

<div class="keypoints" markdown="1">

#### 주요점
*   로컬 Git 저장소는 하나 혹은 그 이상의 원격 저장소에 연결될 수 있다.
*   SSH를 어떻게 설정하는지 배울 때까지 HTTPS 프로토콜을 사용해서 원격 저장소에 연결할 수 있다.
*   `git push` 명령어는 로컬 저장소의 변경사항을 원격 저장소에 복사한다.
*   `git pull` 명령어는 원격 저장소의 변경사항을 로컬 저장소에 복사한다.
*   `git clone` 명령어는 원격 저장소를 복사해서 `origin`을 원격 저장소로 자동으로 설정된 로컬 저장소를 생성한다.

</div>

<div class="challenge" markdown="1">
GitHub 저장소를 생성하고, 복제하고, 파일을 추가하고, 파일 추가 변경사항을 GitHub에 푸쉬하고, 그리고 나서 GitHub 변경사항 [타임스탬프(timestamp)](../../gloss.html#timestamp)를 살펴보자. GitHub는 시간을 어떻게 기록하고 왜 할까?

</div>
