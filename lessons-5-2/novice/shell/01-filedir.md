---
layout: page
title: 파일과 디렉토리
---
<div class="objectives" markdown="1">

#### 목표
*   파일과 디렉토리의 차이점과 다른점을 설명한다.
*   절대경로를 상대경로로 변환하고 반대로 상대경로를 절대경로로 변환한다.
*   특정 파일과 디렉토리를 확인할 수 있는 절대경로와 상대경로를 구성한다.
*   쉘의 읽기-실행-출력(read-run-print) 주기를 각 단계별로 설명한다.
*   명령 라인 호출에 실제 명령어, 플래그, 파일명을 확인한다.
*   탭 완성기능을 시연하고 장점을 설명한다.

</div>
파일과 디렉토리를 관리를 담당하고 있는 운영체제 부분을 [파일 시스템(file system)](../../gloss.html#filesystem)이라고 한다. 파일 시스템은 데이터를 정보를 담고 있는 파일과 파일 혹은 다른 디렉토리를 담고 있는 디렉토리(혹은 폴더)로 조직한다.
  
파일과 디렉토리를 생성, 검사, 새이름, 삭제하는데 몇가지 명령어가 자주 사용된다.
명령어를 살펴보기 위해서 쉘 윈도우를 엽니다.

~~~
$
~~~
{:class="in"}
달러 기호 ($)는 [프롬프트(prompt)](../../gloss.html#prompt)로 쉘이 입력을 기다리는 것을 보여준다. 여러분의 쉴이 좀더 정교한 다른 것을 보여줄수도 있다.

`whoami` 명령어를 타이핑하고, 명령을 쉘에 보내기 위해서 엔터키(Enter Key, 종종 키보드에 따라 Return으로 표기도 됨)를 누룹니다. 명령어의 출력은 현재 사용자의 ID가 됩니다. 즉, 쉘이 생각하는 사용자가 누구인지를 보여줍니다.

~~~
$ whoami
~~~
{:class="in"}
~~~
nelle
~~~
{:class="out"}

좀더 구체적으로, `whoami`를 타이핑할 때, 쉘은

1.  `whoami` 프로그램을 찾고,
2.  프로그램을 실행하고,
3.  프로그램 실행 결과를 출력하고,
4.  새로운 프롬프트를 화면에 출력해서 더 많은 명령어를 받을 준비가 되어 있음을 알려줍니다.

다음으로 `pwd` 명령어를 실행하여 지금 어디에 있는지 알아봅시다.
  `pwd`는 "print working directory"의 첫 글짜를 땄습니다.
언제든지 [현재 작업 디렉토리(current working directory)](../../gloss.html#current-working-directory)는 현재 디폴트 디렉토리가 된다.
즉, 명시적으로 다른 곳으로 지정하지 않았다면, 사용자가 명령어를 실행하는 디렉토리가 컴퓨터가 가정하는 디렉토리가 된다.
다음에서, 컴퓨터의 응답은 `/users/nelle`으로 넬(Nelle)의 [홈 디렉토리(home directory)](../../gloss.html#home-directory)다.

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle
~~~
{:class="out"}

> #### 알파벳 수프 (Alphabet Soup)
> 
> 사용자가 누구인지를 파악하는데는 `whoami`가 사용되면, 
> 사용자가 어디 있는지를 파악하는 명령어는 `whereami`가 되어야 한다.
> 왜 `pwd`가 대신에 사용될까? 일반적인 대답은 다음과 같다.
> 1970년대 초에 UNIX가 처음 개발되었을 때, 모든 키입력은 카운트되었고, 
> 그시절 장비는 느리고, 텔레타이프의 백스페이스는 너무나도 고생스러워서, 
> 타이핑 오류 숫자를 줄이기 위해서 키입력의 숫자를 줄이는 것이 사용자 
> 편의성(usability)의 진정한 승자가 되었다.
> 현실은 명령어가 종합계획없이 전문어와 은어에 몰입된 사람들에 의해서 
> 유닉스에 하나씩 하나씩 추가되었다. 결과는 일관성이 없지만, 지금 우리는 
> 뗄래야 뗄 수 없는 상태가 되었다.

"홈 디렉토리(home directory)"를 이해하기 위해서, 파일 시스템이 전체로 어떻게 구성되었는지 살펴보자. 최상단에 다른 모든 것을 담고 있는 [루트 디렉토리(root directory)](../../gloss.html#root-directory)가 있다. 슬래쉬 `/` 문자로 나타내고, `/users/nelle`에서 맨 앞에 슬래쉬이기도 하다.



홈 디렉토리 안쪽에 몇가지 다른 디렉토리가 있다.  
`bin` (몇몇 내장 프로그램이 저장된 디렉토리),
`data` (여러가지 데이터 파일이 저장된 디렉토리),
`users` (사용자의 개인 디렉토리가 위한 디렉토리),
`tmp` (장기간 저장될 필요가 없는 임시 파일을 위한 디렉토리), 등등.

<img src="img/filesystem.svg" alt="The Filesystem" />

현재 작업 디렉토리`/users/nelle`는 `/users` 내부에 저장되어 있는데 `/users`가 이름의 처음 부분이기 때문에 알 수 있다. 마찬가지로 `/users`는 루트 디렉토리 내부에 저장되어 있는데 이름이 `/`으로 시작되기 때문이다.

`/users` 하단에, 컴퓨터 계정의 각 사용자별 디렉토리를 볼 수 있다. 미이라(Mummy) 파일은 `/users/imhotep` 디렉토리에 저장되어 있고, 늑대인가(Wolfman)의 파일은 `/users/larry` 디렉토리에 저장되어 있고 `/users/nelle` 디렉토리에 `nelle`의 정보가 저장되어 있는데 이것이 왜 `nelle`이 디렉토리 이름의 마지막 부분인 이유다.

<img src="img/home-directories.svg" alt="Home Directories" />

> 문자 `/` 에 두가지 의미가 있음을 주목한다. 파일 
> 혹은 디렉토리 이름의 처음에 나타날 때는 루트 디렉토리를 의미한다.
> 이름 *중간에* 나타날 때는 단지 구분자임을 나타낸다.

Nelle의 홈 디렉토리에 무엇이 있는지 `ls` 명령어를 실행해서 살펴보자.`ls`는 "목록보기(listing)"를 나타낸다.

~~~
$ ls
~~~
{:class="in"}
~~~
creatures  molecules           pizza.cfg
data       north-pacific-gyre  solar.pdf
Desktop    notes.txt           writing
~~~
{:class="out"}

<img src="img/homedir.svg" alt="Nelle's Home Directory" />

`ls`는 알파벳 순서로 깔끔하게 열로 정렬하여 현재 디렉토리의 파일과 디렉토리 이름을 출력한다.[플래그(flag)](../../gloss.html#flag) `-F`를 추가하여 출력을 좀더 포괄적으로 생성할 수 있다. `ls`으로 하여금 디렉토리 이름 뒤에 `/`을 추가하게 한다.

~~~
$ ls -F
~~~
{:class="in"}
~~~
creatures/  molecules/           pizza.cfg
data/       north-pacific-gyre/  solar.pdf
Desktop/    notes.txt            writing/
~~~
{:class="out"}

`/users/nelle` 디렉토리는 7개의 [서브 디렉토리(sub-directories)](../../gloss.html#sub-directory)를 담고 있다. 뒤에 슬래쉬를 갖지 않은 이름, 예를 들어 `notes.txt`, `pizza.cfg`, `solar.pdf`은 단순한 파일이다. `ls`과 `-F` 사이에 공백이 있음을 주목한다. 공백이 없으면 쉘은 존재하지 않는 `ls-F` 명령어를 실행한다고 생각한다.

> #### 이름에는 무엇이 있나요?
> 
> Nelle의 파일 이름이 "무엇.무엇"으로 된 것을 알아챘을지 모르겠다.
> 이것은 단지 관례다. 파일 이름을 `mythesis` 혹은 원하는 무엇이든지 지을 수 있다.
> 하지만, 대부분의 사람들은 두 부분으로 구분된 이름을 사용하여 사람이나 
> 프로그램이 다른 종류의 파일임을 구분하도록 돕는다.
> 이름의 두번째 부분은 [파일 확장자(filename extension)](../../gloss.html#filename-extension)라고 불리며,
>  파일이 무슨 종류의 데이터를 담고 있는지 나타낸다. `.txt` 확장자는 텍스트
>  파일임을, `.pdf`는 PDF 문서임을, `.cfg` 확장자는 어떤 프로그램의 
>  구성정보를 담고 있는 형상관리 파일임을 나타낸다.> 
>
> 단지 관습이기는 하지만 중요하다. 파링은 바이트(byte)를 담고 있다. PDF 문서, 
> 이미지, 등등을 규칙에 따라 바이트를 해석하는 것은 우리와 우리의 프로그램에 
> 맡겨졌다.
>
> `whale.mp3`같은 고래 PNG 이미지의 이름이 고래 노래의 음성파일로 변환되는 
> 마술은 없다. 설사 누군가 두번 클릭할 때, 운영체제가 음악 재생기로 열어 
> 실행할 수는 있지만 작동은 하지 않을 것이다.

이제 `ls -F data`(즉, 명령어 `ls`와 [인수(arguments)](../../gloss.html#argument) `-F`과 `data`)을 실행하여 Nelle의 <code>data</code> 디렉토리에 무엇이 있는지 살펴보자.
`ls` 명령어의 앞에 대쉬('&mdash;')가 *없는* 두번째 인수는 현재 디렉토리 이외의 목록을 보고자 한다고 것을 나타낸다. 

~~~
$ ls -F data
~~~
{:class="in"}
~~~
amino-acids.txt   elements/     morse.txt
pdb/              planets.txt   sunspot.txt
~~~
{:class="out"}

명령어 실행결과는 4개의 텍스트 파일과 두개의 하위 디렉토리가 있다는 것을 보여준다. 이렇게 계층적으로 조직하여 관리하는 것은 작업을 체계적으로 추적할 수 있게 도움을 준다. 홈 디렉토리에 수백의 파일을 놓는 것도 가능하다. 하지만, 마치 책상위에 수백장의 종이를 쌓는 것과 유사해서, 이런 전략은 본인을 파멸로 이끌 수도 있다.

그런데 `data` 디렉토리 이름을 적은 것에 주목하세요. 끝에 슬래쉬가 없습니다. `-F` 플래그를 사용해서 파일과 디렉토리를 구별했을 때 사용한 `ls` 명령어에 디렉토리 이름 뒤에 붙었습니다. 그리고 [상대 경로(relative path)](../../gloss.html#relative-path)이기 때문에 슬래쉬로 시작도 하지 않았습니다. 즉, `ls`에게 파일의 루트에서 보다는 현재 위치에서 찾으라고 합니다.

> #### 매개 변수(Parameters) 대 인수(Arguments)
> 
> [위키피디아(Wikipedia)](https://en.wikipedia.org/wiki/Parameter_(computer_programming)#Parameters_and_arguments)에 따르면,
> [인수(argument)](../../gloss.html#argument) and [매개 변수(parameter)](../../gloss.html#parameter)는 약간 다른 것을 의미한다. 
> 하지만, 실무에서 대부분의 사람들은 상호 호환적으로 혹은 일관성 없이 사용한다.
> 여기서도 구별없이 사용할 것이다.

`ls -F /data`을 실행(*앞에 슬래쉬*를 가지고)하면, 다른 답을 얻게 되는데, 왜냐하면 `/data`은 [절대 경로(absolute path)](../../gloss.html#absolute-path)이기 때문이다.

~~~
$ ls -F /data
~~~
{:class="in"}
~~~
access.log    backup/    hardware.cfg
network.cfg
~~~
{:class="out"}

앞의 `/`` 슬래쉬는 컴퓨터에게 파일시스템의 루트의 경로를 따르게 명령한다. 그래서 명령어를 실행할 때 어느 위치에 있는지 관계없이 항상 정확하게 한 디렉토리만을 참조한다.

현재 작업 디렉토를 바꾸려고 하면 어떨까요? 작업 디렉토리를 변경하기 전에 `pwd` 명령어는 현재 `/users/nelle`에 있다고 보여주고 인수가 없는 `ls` 명령어는 디렉토리의 내용을 보여준다. 

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle
~~~
{:class="out"}
~~~
$ ls
~~~
{:class="in"}
~~~
creatures  molecules           pizza.cfg
data       north-pacific-gyre  solar.pdf
Desktop    notes.txt           writing
~~~
{:class="out"}

작업 디렉토리를 변경하기 위해서 `cd` 다음에 디렉토리 이름을 사용한다. `cd`는 "change directory"의 두문어다. 하지만 약간 오해의 소지가 있다. 명령어 자체가 디렉토리를 변경하지는 않고, 단지 사용자가 어느 디렉토리에 있는지에 대한 쉘의 생각을 바꾸는 것이다.

~~~
$ cd data
~~~
{:class="in"}

`cd`는 어떤 것도 출력하지 않지만, `cd`명령어 다음에 `pwd`를 실행하면, `/users/nelle/data` 디렉토리에 있음을 확인할 수 있다. `ls` 명령어를 실행하면, `/users/nelle/data` 디렉토리 내용을 볼 수 있다. 왜냐하면 이 디렉토리가 현재 사용자가 있는 디렉토리이기 때문이다.

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle/data
~~~
{:class="out"}
~~~
$ ls -F
~~~
{:class="in"}
~~~
amino-acids.txt   elements/     morse.txt
pdb/              planets.txt   sunspot.txt
~~~
{:class="out"}

이제 디렉토리를 따라 아래로 어떻게 갈 수 있는지를 알게되었다. 어떻게 상위 디렉토리로 갈 수 있을까요? 절대 경로를 사용할 수 있습니다.

~~~
$ cd /users/nelle
~~~
{:class="in"}

하지만, 상위 디렉토리로 가기 위해서는 `cd ..` 명령어를 사용하는 것이 거의 항상 더 간단하다.

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle/data
~~~
{:class="out"}
~~~
$ cd ..
~~~
{:class="in"}

`..`은 특수 디렉토리 이름으로 "이것을 포함하는 디렉토리(the directory containing this one)"를 의미한다. 
  좀더 간단하게 현재 디렉토리의 [부모(parent)](../../gloss.html#parent-directory)를 지칭한다.
  당연하지만, `cd ..` 다음에 `pwd`을 실행하면, 다시 `/users/nelle`로 돌아온다.

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle
~~~
{:class="out"}

`ls` 명령어를 실행할 때 특수 디렉토리 `..`는 보통 보여지지 않는다. 만약 화면에 출력하고자 한다면, `ls` 다음에 `-a` 플래그를 줄 수 있다.

~~~
$ ls -F -a
~~~
{:class="in"}
~~~
./          Desktop/             pizza.cfg
../         molecules/           solar.pdf
creatures/  north-pacific-gyre/  writing/
data/       notes.txt
~~~
{:class="out"}

`-a`은 "모두 보여주기(show all)"를 나타낸다. `ls`명령어로 `..`같은 `.`로 시작하는 디렉토리와 파일을 보여준다. `..`은 만약 `/users/nelle`에 위치하고 있다면, `/users`을 나타낸다.
`.`은 "현재 작업 디렉토리"를 의미하는 또다른 특수 디렉토리를 보여준다. 좀 중복스럽게 보이지만, 곧 어떻게 사용되는지 알게 될 것이다.

> #### 직교(Orthogonality)
> 
> 특수 이름 `.`과 `..`는 `ls`에만 속하는 것이 아니고 모든 프로그램에서 같은 방식으로 해석된다.
> 예를 들어, `/users/nelle/data` 디렉토리에 있는데 `ls ..` 명령어는 `/users/nelle`의 목록을 보여줄 것이다.
> 어떻게 조합되든지 상관없이 동일한 의미를 가지게 될 때, 프로그래머는 [직교(orthogonal)](../../gloss.html#orthogonal)한다고 부른다. 
> 직교 시스템은 사람들이 배우기 훨씬 쉬운데 왜냐하면 기억해야할 특수 사례와 예외가 더 적기 때문이다.

### Nelle의 파이프라인: 파일 구성하기

파일과 디렉토리에 대해서 알았으니, Nelle은 단백질 분석기가 생성하는 파일을 구성할 준비를 마쳤다.
우선 `north-pacific-gyre` 디렉토를 생성해서 데이터가 어디에서 왔는지를 상기하도록 한다.
`2012-07-03` 디렉토리를 생성해서 시료 처리를 시작한 날짜를 명기한다.
`conference-paper`와 `revised-results`같은 이름을 사용하곤 했다.
하지만, 몇년이 지난 후에 이해하기 어렵다는 것을 발견했다. (마지막 지푸라기는 `revised-revised-results-3` 디렉토리를 자신이 생성한 것을 발견했을 때였다.)

> Nelle은 월과 일에 0을 앞에 붙여 디렉토리를 "년-월-일(year-month-day)" 이름지었다. 
> 왜냐하면 쉘은 알파벳 순으로 파일과 디렉토리 이름을 화면에 출력하기 때문이다.
>  만약 월이름을 사용한다면, 12월(December)이 7월(July) 앞에 위치할 것이다. 만약 앞에 0을 붙이지 않으면 11월이 7월 앞에 올 것이다.

각각의 물리적 시료는 "NENE01729A"처럼 10자리 중복되지 않는 ID 연구실 관례에 따라 표식을 붙였다. 시료의 장소, 시간, 깊이, 그리고 다른 특징을 기록하기 위해서 수집 기록에 사용한 것과 동일하다. 그래서 각 파일의 이름으로 사용하기로 결정했다. 분석기의 출력값은 텍스트 형식이기 때문에 `NENE01729A.txt`, `NENE01812A.txt`, ... 같이 확장자를 붙였다. 1520개 파일 모두 동일한 디렉토리에 저장되었다.

홈 디렉토리에서 Nelle은 다음 명령어 무슨 파일이 있는지 확인할 수 있다.

~~~
$ ls north-pacific-gyre/2012-07-03/
~~~
{:class="in"}

엄청나게 많은 타이핑이지만 쉘에게 많은 일을 시킬 수도 있다. 만약 다음과 같이 타이핑하고,

~~~
$ ls nor
~~~
{:class="in"}

그리고 나서 탭을 누르면, 자동적으로 쉘이 디렉토리 이름을 자동완성한다.

~~~
$ ls north-pacific-gyre/
~~~
{:class="in"}

탭을 다시 누르면, Bash가 명령문에 `2012-07-03/`을 추가하는데, 왜냐하면 유일한 가능한 완성조건이기 때문이다.
한번더 탭을 누려면 아무것도 없다. 왜냐하면 1520가지 경우의 수가 있기 때문이다. 탭을 두번 누르면 모든 파일 목록을 가져온다. 
이것을 [탭 완성(tab completion)](../../gloss.html#tab-completion)이라고 부르고 앞으로도 다른 많은 툴에서도 많이 볼 것이다.

<div class="keypoints" markdown="1">

#### 주요점
*   디스크의 정보를 관리를 담당하는 것은 파일 시스템이다.
*   정보는 파일에 저장되고, 파일은 디렉토리(폴더)에 저장된다.
*   디렉토리는 또한 다른 디렉토리에 저장될 수 있고, 디렉토리 트리를 구성하게 된다.
*   `/`` 자체는 전체 파일시스템의 루트 디렉토리다.
*   상대 경로는 현재 지점에서 시작하는 위치를 나타낸다.
*   절대 경로는 파일시스템의 루트에서 위치를 나타낸다.
*   경로의 디렉토리 이름은 유닉스에서는 '/', 하지만 윈도우에서는 '\'이다.
*   '..'은 현재 디렉토리의 상위 디렉토리를 의미하고;'.'자체는 "현재 디렉토리"를 의미한다.
*   대부분의 파일 이름은 파일명.확장자(something.extension)`이다. 확장자가 필수적이지 않고 특정한 것을 보증하지도 않지만, 파일의 데이터 형식을 나타내는 일반적으로 사용된다.
*   대부분의 명령어는 '-'로 시작하는 옵션(플래그)를 갖는다.

</div>

<img src="img/filesystem-challenge.svg" alt="Filesystem for Challenge Questions" />

<div class="challenge" markdown="1">
만약 `pwd` 명령어를 쳤을 때 화면에 `/users/thing`이 출력된다면, `ls ../backup`은 무엇을 출력할까요?

1.   `../backup: No such file or directory`
2.   `2012-12-01 2013-01-08 2013-01-27`
3.   `2012-12-01/ 2013-01-08/ 2013-01-27/`
4.   `original pnas_final pnas_sub`
</div>

<div class="challenge" markdown="1">
만약 `pwd` 명령어를 쳤을 때 화면에 `/users/thing`이 출력되고, 
`-r`은 `ls` 명령어가 역순으로 화면에 출력하게 한다면, 무슨 명령어가 다음을 화면에 출력할까요?

~~~
pnas-sub/ pnas-final/ original/
~~~

1.  `ls pwd`
2.  `ls -r -F`
3.  `ls -r -F /users/backup`
4.  위 \#2 혹은 \#3, 하지만, \#1은 아님.

</div>

<div class="challenge" markdown="1">
디렉토리 이름없는 `cd` 명령어는 무엇을 수행할까요?

1.  아무 것도 하지 않는다.
2.  작업 디렉토리를 루트 `/`으로 변경한다.
3.  작업 디렉토리를 사용자의 홈 디렉토리로 변경한다.
4.  오류 메시지를 출력한다.
</div>

<div class="challenge" markdown="1">
`ls` 명령어가 -s과 -h 을 인수로 사용되면 무슨 작업을 수행할까요?
</div>
