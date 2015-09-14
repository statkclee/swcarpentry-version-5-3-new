---
layout: lesson
root: ../..
title: 원격 작업(Working Remotely)
---

#### 목표
*   SSH가 무엇인지 설명한다.
*   SSH 키(SSH Key)가 무엇인지 설명한다.
*   SSH 키 페어(SSH Key Pair)를 생성한다.
*   SSH 키를 원격 서버에 추가한다.
*   SSH 키를 어떻게 사용하는지 배운다.

데스크탑이나 노트북 컴퓨터에 쉘을 사용할 때 무슨일이 생기는 좀더 자세히 살펴보자.
첫번째 단계는 로그인(login)해서 운영체제가 접속한 사용자가 누구이고 무슨 작업을 하도록 허락되었는지를 확인한다. 사용자 이름과 비밀번호를 타이핑해서 수행하게 된다. 즉, 운영체제는 레코드에 저장된 입력한 값을 확인하고, 만약 일치하게 된다면 사용자를 위해서 쉘을 실행한다.

명령어를 타이핑할 때, 타이핑하는 문자를 나타내는 0과 1이 키보드에서 쉘로 전달된다. 쉴은 사용자가 타이핑한 것을 나타내기 위해 문자를 화면에 출력한다. 그리고 나서 만약 타이핑한 것이 명령어면, 쉘은 명령어를 실행하고 출력결과를 (만약 출력할 것이 있다면) 화면에 출력한다.

실험결과 데이터베이스를 관리하는 지하실 서버 같은 다른 컴퓨터에 명령어를 실행하고자 한다면 어떨까? 이를 위해서, 먼저 해당 컴퓨터에 로그인 해야한다. 
이를 [원격 로그인(remote login)](../../gloss.html#remote-login)이라고 하고 원격 컴퓨터(remote computer)라고 부른다. 로컬 쉘과 동일하게 사용자를 위해서 원격 컴퓨터에서 명령어를 실행하고 결과를 로컬 컴퓨터 화면에 출력한다.

원격 로그인하기 위해서 사용하는 툴이 [시큐어 쉘(secure shell)](../../gloss.html#secure-shell) 즉, SSH다. 
특히, `ssh username@computer` 명령어는 SSH를 실행해서 지정한 원격 컴퓨터에 접속한다.
로그인한 후에 원격 컴퓨터의 파일과 디렉토리를 사용하기 위해서 원격 쉘을 이용할 수 있다. 
`exit`나 컨트롤-D(Control-D)를 타이핑하여 원격 쉘을 끝내고 이전 셀로 돌아온다.

다음 예제에서, 원격 컴퓨터의 명령어 프롬프트가 `$` 대신에 `moon>`다. 어느 컴퓨터가 무엇을 하는지 좀더 명확히하기 위해서, 원격 컴퓨터에 보내는 명령어와 출력결과를 들여쓰기 한다.

~~~  {.shell}
$ pwd
~~~

~~~ {.output}
/users/vlad
~~~

~~~ {.shell}
$ ssh vlad@moon.euphoric.edu
Password: ********
~~~

~~~ {.shell}
    moon> hostname
~~~

~~~ {.output}
    moon
~~~

~~~  {.shell}
    moon> pwd
~~~

~~~ {.output}
    /home/vlad
~~~

~~~ {.shell}
    moon> ls -F
~~~

~~~ {.output}
    bin/     cheese.txt   dark_side/   rocks.cfg
~~~

~~~ {.shell}
    moon> exit
~~~

~~~ {.shell}
$ pwd
~~~

~~~ {.output}
/users/vlad
~~~

시큐어 쉘은 원격 쉘(remote shell)을 나타내는 오래된 `rsh` 프로그램과 대조하기 위해서 "시큐어(secure)"라고 부른다. 과거에 모든 사람이 서로를 신뢰하고, 이름만으로 컴퓨터의 모든 칩을 알고 있을 때, 네트워크를 타고 정보를 전송할 때 가장 민감한 정보를 제외하고 어떤 것도 암호화하지 않았다. 하지만, 악의를 가진 사람이 네트워크 트래픽을 볼 수 있고, 사용자이름과 비밀번호를 훔칠 수 있고, 악의를 가지고 이용할 수도 있다는 것을 뜻한다. SSH는 이러한 점을 방지하거나, 최소한 늦추기 위해서 고안되었다. SSH는 다른 컴퓨터 사이에 주고 받는 메시지에 무엇이 있는지 외부인이 볼 수 없도록 정교하고, 수많은 검증을 거친 암호화 프로토콜을 사용한다.

"시큐어 복사(secure copy)"를 나타내는 `scp`로 불리는 동반 프로그램도 `ssh`는 가지고 있다. SSH같은 종류의 연결을 사용하여 원격 컴퓨터에서 혹은 컴퓨터로 파일을 복사할 수 있게 한다. 명령어 이름은 `cp`와 `ssh`를 조합해서 작업을 수행한다. 파일을 복사하기 위해서 원천(소스, source)와 목적 경로를 명기해야한다. 경로명에는 컴퓨터의 이름을 포함할 수도 있다. 만약 컴퓨터 이름을 생략한다면, `scp`는 지금 실행중인 로컬 컴퓨터를 가정한다. 예를 들어, 다음 명령어는 최신 결과를 복사해서 지하실에 있는 백업 서버로 복사하면서 진행사항을 화면에 출력한다. 

~~~ {.shell}
$ scp results.dat vlad@backupserver:backups/results-2011-11-11.dat
Password: ********
~~~
{:class="in"}

~~~ {.output}
results.dat              100%  9  1.0 MB/s 00:00
~~~

전체 디렉토리를 복사하는 것도 유사하다. `-r` 옵션을 사용해서 재규적으로 복사한다는 신호를 전달한다. 예를 들어, 다음 명령어는 백업서버에서 모든 결과를 노트북으로 복사하여 가져온다.

~~~  {.shell}
$ scp -r vlad@backupserver:backups ./backups
Password: ********
~~~

~~~ {.output}
results-2011-09-18.dat              100%  7  1.0 MB/s 00:00
results-2011-10-04.dat              100%  9  1.0 MB/s 00:00
results-2011-10-28.dat              100%  8  1.0 MB/s 00:00
results-2011-11-11.dat              100%  9  1.0 MB/s 00:00
~~~

SSH로 할 수 있는 것이 하나 더 있다. 백업서버에 `backups/results-2011-11-12.dat` 파일을 이미 생성했는지 확인한다고 가정하자. 로그인에서 `ls` 명령어를 타이핑하는 대신에 다음과 같이 할 수 있다.

~~~ {.shell}
$ ssh vlad@backupserver "ls results"
Password: ********
~~~

~~~ {.output}
results-2011-09-18.dat  results-2011-10-28.dat
results-2011-10-04.dat  results-2011-11-11.dat
~~~

SSH가 원격 사용자이름 다음에 인자를 받아서 원격 컴퓨터의 쉘에 전달한다. (단일 인자처럼 보이게 하기 위해서 인용부호 처리를 한다.) 이러한 방식의 인자는 적합한 명령어이기 때문에 원격 쉘이 `ls results`를 실행하고 결과를 다시 사용자의 로컬 쉘의 화면에 출력한다.

### SSH 키 (SSH Key)

비밀번호를 반복해서 매번 타이핑하는 것은 성가시다. 특히, 원격에서 실행하려고 하는 명령어가 루프로 반복된다면 더욱 그렇다. 비밀번호를 반복해서 타이핑하는 필요를 제거하기 위해 [SSH 키(SSH key)](../../gloss.html#ssh-key)를 생성해서 원격 컴퓨터에 접속하는 것을 항상 신뢰하도록 할 수 있다.

SSH 키는 GitHub 같은 서비스와 공유되는 공개키(public key)와 사용자의 로컬에만 저장되는 개인키(private key)로 짝(pair)으로 되어 있다. 만약 두 키가 일치한다면, 접속이 허락된다.

SSH 키를 지탱하는 암호기술이 공개키로부터 개인키를 역공학(reverse engineering)할 수 없게 보증한다.

SSH 인증을 사용하는 첫 단계는 자신만의 개인키와 공개키 짝을 생성하는 것이다.

로컬 컴퓨터에 SSH 키 짝을 이미 가지고 있을 수도 있다. `.ssh` 디렉토리로 가서 디렉토리 내부 목록을 출력해서 SSH 키 짝이 존재하는지 확인할 수 있다.

~~~ {.shell}
$ cd ~/.ssh
$ ls
~~~

만약 `id_rsa.pub`을 본다면, 이미 SSH 키 짝을 가지고 있어서 새로 생성할 필요는 없다.

만약 `id_rsa.pub`을 볼 수 없다면, 다음 명령어를 사용해서 새로운 SSH 키 짝을 생성할 수 있다. `your@email.com` 부분을 사용자 여러분의 전자우편 주소로 바꾸도록 확인하세요.

~~~ {.shell}
$ ssh-keygen -t rsa -C "your@email.com"
~~~

새로운 키를 어디에 저장할지 물었을 때, 디폴트 장소에 동의하면 "엔터(enter)"를 치세요.

~~~ {.output}
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/username/.ssh/id_rsa):
~~~

그리고 나서, 선택적인 암호(passphrase)를 입력하도록 요청받을 것이다. SSH키를 좀더 안전하게 만드는데 사용될 수 있지만, 만약 여러분이 원하는 것이 매번 비밀번호를 타이핑하는 것을 피하려고 한다면, "엔터(enter)"를 두번 쳐서 건너뛸 수 있다.

~~~ {.output}
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
~~~

SSH 키 생성이 완료되면, 다음 확인 결과를 보게 된다.

~~~ {.output}
Your identification has been saved in /Users/username/.ssh/id_rsa.
Your public key has been saved in /Users/username/.ssh/id_rsa.pub.
The key fingerprint is:
01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your@email.com
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|        . E +    |
|       . o = .   |
|      . S =   o  |
|       o.O . o   |
|       o .+ .    |
|      . o+..     |
|       .+=o      |
+-----------------+
~~~

상기 임의 예술 이미지가 SSH 키를 일치시키는 대안이지만 여러분은 필요하치 않을 것이다.

이제 생성한 공개키를 접속하려는 서버에 보낼 필요가 있다. `cat` 명령어로 새로 생성한 공개키의 내용을 화면에 출력하자.

~~~ {.shell}
$ cat ~/.ssh/id_rsa.pub
~~~

~~~ {.output}
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA879BJGYlPTLIuc9/R5MYiN4yc/YiCLcdBpSdzgK9Dt0Bkfe3rSz5cPm4wmehdE7GkVFXrBJ2YHqPLuM1yx1AUxIebpwlIl9f/aUHOts9eVnVh4NztPy0iSU/Sv0b2ODQQvcy2vYcujlorscl8JjAgfWsO3W4iGEe6QwBpVomcME8IU35v5VbylM9ORQa6wvZMVrPECBvwItTY8cPWH3MGZiK/74eHbSLKA4PY3gM4GHI450Nie16yggEg2aTQfWA1rry9JYWEoHS9pJ1dnLqZU3k/8OWgqJrilwSoC5rGjgp93iu0H8T6+mEHGRQe84Nk1y5lESSWIbn6P636Bl3uQ== your@email.com
~~~

출력결과의 내용 콘텐츠를 복사한다. SSH 키를 사용하여 접속하려는 서버에 로그인한다.

~~~ {.shell}
$ ssh vlad@moon.euphoric.edu
Password: ********
~~~

복사한 콘텐트를 `~/.ssh/authorized_keys` 끝에 붙여놓는다.

~~~  {.shell}
    moon> nano ~/.ssh/authorized_keys`.
~~~

콘텐트를 파일에 추가한 후에 원격 컴퓨터에서 로그 아웃하고 다시 로그인한다. 만약 SSH 키 초기 설정이 올바르게 되었다면, 비밀번호를 다시 타이핑할 필요가 없다.

~~~  {.shell}
    moon> exit
~~~

~~~ {.output}
$ ssh vlad@moon.euphoric.edu
~~~

<div class="keypoints" markdown="1">

#### 주요점
*  SSH는 사용자명/비밀번호 인증의 안전한 대안이다.
*  SSH 키는 공개/개인 키 짝으로 생성된다. 공개 키는 다른 사람, 컴퓨터와 공유될 수 있다. 개인키는 여러분의 로컬 컴퓨터에만 체재한다.
</div>
