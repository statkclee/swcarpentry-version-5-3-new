---
layout: lesson
root: ../..
title: 쉘 변수(Shell Variables)
---
쉘도 단순한 프로그램이다. 그래서 다른 프로그램처럼 변수도 갖는다. 변수의 역할은 프로그램 실행을 제어한다. 그래서 변수의 값을 변경해서, 쉘과 다른 프로그램의 동작을 변경한다.

`set` 명령어를 실행해서 전형적인 쉘 세션에 있는 변수 몇가지를 살펴보는 것으로 시작하자.

~~~
$ set
~~~
{:class="in"}
~~~
COMPUTERNAME=TURING
HOME=/home/vlad
HOMEDRIVE=C:
HOSTNAME=TURING
HOSTTYPE=i686
NUMBER_OF_PROCESSORS=4
OS=Windows_NT
PATH=/Users/vlad/bin:/usr/local/git/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin
PWD=/home/vlad
UID=1000
USERNAME=vlad
...
~~~
{:class="out"}

보게되면, 몇가지가 있다. 사실 상기 보여진 것보다 4배 혹은 5배 더 많다.
컴퓨터에 있는 것을 *보기 위해서* `set`를 사용하는 것이 유닉스 조차도 약간 이상해 보인다. 하지만, 만약 어떤 인자도 설정하기 않는다면, 설정할 수 있는 모든 것을 보여준다.

모든 변수는 이름이 있다. 관례로, 항상 존재하는 변수는 대문자 이름이 주어진다. 모든 쉘 변수 값은 `UID` 같은 숫자처럼 보이는 것 조차도 문자열이다.
필요 시에 문자열을 다른 형으로 변환하는 것은 프로그램에 달려있다. 예를 들어, 만약 컴퓨터가 얼마나 많은 프로세서를 가지고 있는지 알고자 한다면, `NUMBER_OF_PROCESSORS` 변수값을 문자열에서 정수형으로 변환한다.

마찬가지로, `PATH` 같은 변수는 값을 리스트로 저장한다. 이 경우에 관례는 구분자로 콜론(':')을 사용하는 것이다. 만약 프로그램이 경로 정보를 저장한 리스트에서 각 항목 요소를 뽑아내고자 한다면, 변수 문자열 값을 조각내서 쪼개 꺼내는 것은 프로그램이 할 일이다.

#### 경로(`PATH`) 변수

`PATH` 변수만 좀더 자세히 살펴보자. 경로변수 값은 쉘의 [검색 경로(search path)](../../gloss.html#search-path)를 정의한다. 즉, 특정 디렉토리에 있는 프로그램을 지정하지 않고 타이핑할 때, 쉘이 실행가능한 프로그램 대상으로 찾는 디렉토리 리스트다. 

예를 들어, `analyze` 같은 명령어를 타이핑할 때, 쉘은 둘중 `./analyze`  혹은 `/bin/analyze`을 실행할지를 결정해야 한다. 쉘이 사용하는 규칙은 단순하다. `PATH` 변수에 있는 디렉토리를 순차적으로 쉘이 확인하고, 특정 디렉토리에 요청한 이름을 가진 프로그램을 찾는다. 매칭되는 프로그램을 발견하자마자, 검색을 중지하고 프로그램을 실행한다.

어떻게 동작하는지 보여주기 위해서, 경로(`PATH`)의 컴포넌트를 한 줄에 하나씩 
리스트했다.

~~~
/Users/vlad/bin
/usr/local/git/bin
/usr/bin
/bin
/usr/sbin
/sbin
/usr/local/bin
~~~
{:class="out"}

컴퓨터에 사실 3군데 다른 디렉토리(`/bin/analyze`,
`/usr/local/bin/analyze`,`/users/vlad/analyze`)에 `analyze`로 불리는 프로그램이 3개 있다.
`PATH`에 목록으로 올라간 순서로 쉘이 디렉토리를 검색하기 때문에 가장 먼저 `/bin/analyze`을 찾아 실행시킨다.
프로그램의 전체 경로명을 타이핑하지 않는다면, `/users/vlad/analyze` 프로그램을 *결코* 찾지 못한다는 것을 주목하라. 왜냐하면 `/users/vlad` 디렉토리가 `PATH` 목록에 있지 않기 때문이다.

#### 변수 값 표시하기

변수 `HOME` 변수 값을 표시하자.

~~~
$ echo HOME
~~~
{:class="in"}
~~~
HOME
~~~
{:class="out"}

"HOME"만 출력하는데 원하는 것이 아니다. (설사 실제로 요청한 것이기는 하다.)
대신에 다음과 같이 시도해보자.

~~~
$ echo $HOME
~~~
{:class="in"}
~~~
/home/vlad
~~~
{:class="out"}

달러 기호는 변수 이름말고 변수의 *값*을 원한다고 쉘에 알려준다. 달러 기호는 마치 와일드카드처럼 동작한다. 프로그램을 실행하기 *전에* 요청한 것에 대해서 치환을 쉘이 자동으로 한다. 이런 확장기능 덕분에 실제로 실행한 것은 `echo /home/vlad`이고 정확한 것을 표시한다.

#### 변수 생성하고 변경하기

변수를 생성하는 것은 쉽다. "="을 사용해서 값을 이름에 할당한다.

~~~
$ SECRET_IDENTITY=Dracula
$ echo $SECRET_IDENTITY
~~~
{:class="in"}
~~~
Dracula
~~~
{:class="out"}

값을 바꾸기 위해서는 단지 새로운 값을 할당한다.

~~~
$ SECRET_IDENTITY=Camilla
$ echo $SECRET_IDENTITY
~~~
{:class="in"}
~~~
Camilla
~~~
{:class="out"}

매번 쉘을 실행할 때마다 특정 변수를 자동으로 설정하려고 한다면, 홈 디렉토리에 `.bashrc` 파일에 명령어를 저장한다.
`-a` 옵션을 특별히 사용하지 않는다면, 앞쪽의 '.'문자는 `ls` 명령어가 파일 목록을 출력할 때 이 파일을 제외시킨다. 일반적으로 이러한 것에 대해서는 걱정하고 싶지 않다. 끝쪽의 "rc"는 "run control(실행 제어)"의 약자로 수십년 전에는 매우 중요한 무언가를 의미했지만, 지금은 어떻게 동작하고 왜 사용하는지 이해하지 않고 모든 사람이들이 따라가는 관례가 되었다.

예를 들어, `/home/vlad/.bashrc` 파일에 다음이 있다.

<div class="file" markdown="1">
~~~
export SECRET_IDENTITY=Dracula
export TEMP_DIR=/tmp
export BACKUP_DIR=$TEMP_DIR/backup
~~~
</div>

상기 3라인은 변수 `SECRET_IDENTITY`, `TEMP_DIR`, `BACKUP_DIR`을 생성하고 내보내기를 해서 쉘이 실행하는 임의의 프로그램도 변수를 볼 수 있다.
`BACKUP_DIR` 정의는 `TEMP_DIR` 값에 의존하는 것을 주목하세요. 그래서 만약 임시 파일 위치를 변경한다면, 백업이 자동적으로 재지정된다. 

`alias` 명령어를 사용해서 자주 타이핑하는 것에 대한 단축키를 생성하는 것도 흔하다. 예를 들어, 특정 인자 집합으로 `/bin/zback`을 실행하는데 별칭(alias)으로 `backup`을 정의할 수도 있다.

<div class="file" markdown="1">
~~~
alias backup=/bin/zback -v --nostir -R 20000 $HOME $BACKUP_DIR
~~~
</div>

별칭(alias)는 많은 타이핑을 줄여주고 그렇게 함으로써 타이핑 실수도 줄여주는 효과가 있다. 자주 사용하는 검색엔진에서 "sample bashrc"를 검색어로 넣고 검색하면 다른 별칭(alias)와 bash 기법에 대한 흥미로운 사실을 많이 알게된다.
