---
layout: page
title: 파일, 문자, 디렉토리  등 찾기
---
<div class="objectives" markdown="1">

#### 목표
*   `grep` 명령어를 사용해서 간단한 패턴과 매칭되는 행을 텍스트 파일에서 선택한다.
*   `find` 명령어를 사용해서 간단한 패턴과 매칭하는 파일을 찾는다.
*   한 명령어의 출력결과를 다른 명령어의 명령-라인 매개변수로 사용한다.
*   "텍스트(text)"와 "바이너리(binary)" 의미와 많은 툴이 바이너리를 잘 다루지 못하는 이유를 설명한다.   
</div>

검색에 대해서 어떻게 말하는지에 따라 사람의 나이를 유추할 수 있다. 젊은 사람은 동사로 "구글(Google)"을 사용하고 무뚝뚝한 나이든 유닉스 프로그래머는 "grep"을 사용한다. grep은 "global/regular expression/print"의 축약어로 초기 유닉스 편집기에 흔한 일련의 연산이다. 매우 유용한 명령-라인 프로그램의 이름이기도 하다.

`grep`은 패턴과 매칭되는 파일의 행을 찾아 화면에 뿌려준다. 예를 들어, *Salon* 잡지 1988년 경쟁부문에서 3개 하이쿠(haiku, 일본의 전통 단시)를 담고 있는 파일을 사용한다. 이 예제 파일은 "writing" 하위 디렉토리에서 작업을 할 것이다.

~~~
$ cd
$ cd writing
$ cat haiku.txt
~~~
{:class="in"}
~~~
The Tao that is seen
Is not the true Tao, until
You bring fresh toner.

With searching comes loss
and the presence of absence:
"My Thesis" not found.

Yesterday it worked
Today it is not working
Software is like that.
~~~
{:class="out"}

> #### 영원히 혹은 5년
>
> 원본 하이쿠에 링크를 걸지 않았는데 이유는 <em>Salon</em> 사이트에 더 이상 
> 보이는 것 같지 않아서다. [Jeff Rothenberg가 말했듯이,](http://www.clir.org/pubs/archives/ensuring.pdf) 디지털 정보는 어느 것이 먼저 오든 영원한 
> 영속성을 가지거나 혹은 5년이다.

단어 "not"을 포함하지 않는 행을 찾아 봅시다.

~~~
$ grep not haiku.txt
~~~
{:class="in"}
~~~
Is not the true Tao, until
"My Thesis" not found
Today it is not working
~~~
{:class="out"}

여기서 `not`이 찾고자 하는 패턴이다. 무척이나 간단하다. 모든 글자와 숫자를 쓴 문자가 자신에 대해서 매칭을 한다.
찾고자 하는 이름이나 혹은 파일의 이름이 나온 후에, 출력값은 "not"을 포함하는 파일에 3개 행이 된다.

다른 패턴을 시도해 보자. 이번에는 "day"이다.

~~~
$ grep day haiku.txt
~~~
{:class="in"}
~~~
Yesterday it worked
Today it is not working
~~~
{:class="out"}

이번에는 출력값은 "day"를 포함한 "Yesterday", "Today" 단어를 포함하는 행이 된다.
`grep`명령어에 `-w` 옵션을 주면, 단어 경계로 매칭을 제한해서, "day" 단어만을 가진 행만이 화면에 출력된다.

~~~
$ grep -w day haiku.txt
~~~
{:class="in"}

이 경우에, 어떤 것도 없어서, `grep` 출력값은 비게된다.

또다른 유용한 욥션은 `-n`으로 매칭되는 행에 번호를 붙여 출력한다.

~~~
$ grep -n it haiku.txt
~~~
{:class="in"}
~~~
5:With searching comes loss
9:Yesterday it worked
10:Today it is not working
~~~
{:class="out"}

상기에서 5, 9, 10번째 행이 문자 "it"를 포함함을 확인한다.

다른 유닉스 명령어와 마찬자기로 옵션을 조합할 수 있다. 예를 들어, `-i` 옵션은 대소분자 구분없이 매칭하고, `-v` 옵션은 뒤집어서 매칭을 하여, 두 옵션을 사용하여 대소문자에 관계없이 매칭이 되지 않는 행만을 화면에 출력할 수 있다.

~~~
$ grep -i -v the haiku.txt
~~~
{:class="in"}
~~~
You bring fresh toner.

With searching comes loss

Yesterday it worked
Today it is not working
Software is like that.
~~~
{:class="out"}

`grep` 명령어는 옵션이 많다.
옵션에 대해서 알기 위해서 `man grep`을 타이핑한다. `man`은 유닉스 "manual(매뉴얼)" 명령어다.
명령어에 대한 기술과 옵션을 출력하고 (만약 운이 좋다면) 어떻게 사용할 수 있는지 예제도 몇개 제공한다.

~~~
$ man grep
~~~
{:class="in"}
~~~
GREP(1)                                                                                              GREP(1)

NAME
grep, egrep, fgrep - print lines matching a pattern

SYNOPSIS
grep [OPTIONS] PATTERN [FILE...]
grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]

DESCRIPTION
grep  searches the named input FILEs (or standard input if no files are named, or if a single hyphen-
minus (-) is given as file name) for lines containing a match to the given PATTERN.  By default, grep
prints the matching lines.
...        ...        ...

OPTIONS
Generic Program Information
--help Print  a  usage  message  briefly summarizing these command-line options and the bug-reporting
address, then exit.

-V, --version
Print the version number of grep to the standard output stream.  This version number should be
included in all bug reports (see below).

Matcher Selection
-E, --extended-regexp
Interpret  PATTERN  as  an  extended regular expression (ERE, see below).  (-E is specified by
POSIX.)

-F, --fixed-strings
Interpret PATTERN as a list of fixed strings, separated by newlines, any of  which  is  to  be
matched.  (-F is specified by POSIX.)
...        ...        ...
~~~
{:class="out"}

> #### 와일드카드(Wildcards)
>
> `grep`의 진정한 힘은 옵션에서 나오지 않고, 패턴이 와일드카드를 포함할 수 있다는데서 온다. (기술적 명칭은 [정규 표현식(regular expressions)](../../gloss.html#regular-expression)이고, "grep" 내부에 "re"가 축약어로 들어가 있다.)
> 정규 표현식은 복잡하기도 하고 강력하기도 하다. 복잡한 검색을 하고자 한다면, [웹사이트](http://software-carpentry.org)에서 수업을 볼 수 있다. 맛보기로, 
> 다음과 같이 두번째 위치에 'o'를 포함한 행을 찾을 수 있다.> 
>
>     $ grep -E '^.o' haiku.txt
>     You bring fresh toner.
>     Today it is not working
>     Software is like that.
>
> `-E` 옵션을 사용해서 인용부호 안에 패턴을 넣어서 쉘이 해석하는 것을 방지한다.
> (예를 들어, 패턴이 '\*'을 포함한다면, `grep`을 실행하기 전에 쉘이 전개하려 할
> 것이다.) 패턴에서 '\^'은 행의 시작에 매칭을 고정한다. '.'은 한 문자만 
> 매칭하고(쉘의 '?'과 마찬가지로), 'o'는 실제 영문 'o'와 매칭한다.

`grep`이 파일에 행을 찾는 반면에, `find` 명령어는 파일 자체를 검색한다.
다시, `find` 명령어는 정말 옵션이 많다. 가장 간단한 것이 어떻게 동작하는지 보여주기 위해서, 다음에 보여지는 디렉토리 구조를 사용한다.

<img src="img/find-file-tree.svg" alt="File Tree for Find Example" />

Nelle의 `writing` 디렉토리는 `haiku.txt`로 불리는 파일과, 4개의 하위 디렉토리를 포함한다.
`thesis` 디렉토리는 슬프게고 비어있고, `data` 디렉토리는 `one.txt`과 `two.txt`을 포함하고, 
`tools` 디렉토리는 `format`과 `stats` 프로그램을 포함하고 빈 디렉토리 `old`가 있다.

첫 명령어로, `find . -type d`을 실행하자. 항상 그렇듯이, `.` 자체가 의미하는 바는 현재 작업 디렉토리로 검색을 시작하는 디렉토리이기도 하다. `-type d`은 "디렉토리인 것들"을 의미한다. 과연, `find`의 출력은 `.`을 포함하는 상기 작은 디렉토리 나무 구조에서 다섯개의 디렉토리 이름이 된다.

~~~
$ find . -type d
~~~
{:class="in"}
~~~
./
./data
./thesis
./tools
./tools/old
~~~
{:class="out"}

`-type d`에서 `-type f`으로 옵션을 변경하면, 대신에 모든 파일 목록이 나온다.

~~~
$ find . -type f
~~~
{:class="in"}
~~~
./haiku.txt
./tools/stats
./tools/old/oldtool
./tools/format
./thesis/empty-draft.md
./data/one.txt
./data/two.txt
~~~
{:class="out"}

`find` 명령어는 자동적으로 하위 디렉토리, 또 하위 디렉토리의 하위 디렉토리로 가서 주어진 패턴과 매칭되는 모든 것을 찾는다. 이것을 원치 않는다면, `-maxdepth`를 사용해서 검색의 깊이를 제한할 수 있다.

~~~
$ find . -maxdepth 1 -type f
~~~
{:class="in"}
~~~
./haiku.txt
~~~
{:class="out"}

`-maxdepth`의 반대는 `-mindepth`로, `find`에게 단지 일정 깊이 혹은 아래 깊이만 검색해서 출력하게 한다.
그러므로, `-mindepth 2`는 2 혹은 그 이상의 수준 이하에서 모든 파일을 찾게된다.

~~~
$ find . -mindepth 2 -type f
~~~
{:class="in"}
~~~
./data/one.txt
./data/two.txt
./tools/format
./tools/stats
~~~
{:class="out"}

이제 이름으로 매칭을 하자.

~~~
$ find . -name *.txt
~~~
{:class="in"}
~~~
./haiku.txt
~~~
{:class="out"}

모든 텍스트 파일을 찾기를 기대하지만, 단지 `./haiku.txt`만을 화면에 출력한다. 문제는 명령문을 실행하기 *전에*,
`*`같은 와일드카드 문자를 쉘이 전개하는 것이다. 현재 디렉토리에서 `*.txt`을 전개하면 `haiku.txt`이 되기 때문에, 실제 실행하는 명령어는 다음과 같다.

~~~
$ find . -name haiku.txt
~~~
{:class="in"}

`find` 명령어는 사용자가 요청한 것만 수행한다. 사용자는 방금전에 잘못된 것을 요청했다.

사용자가 원하는 것을 얻기 위해서, `grep`을 가지고 한 것을 수행하자. 단일 인용부호에 `*.txt`을 넣어서 쉘이 
와일드카드 `*`을 전개하지 못하게 한다. 이런 방식으로 `find` 명령어는 실질적으로 `*.txt` 패턴을 얻고 `haiku.txt` 파일 이름으로 전개하지 않는다.

~~~
$ find . -name '*.txt'
~~~
{:class="in"}
~~~
./data/one.txt
./data/two.txt
./haiku.txt
~~~
{:class="out"}

> #### 리스트(list) vs. 찾기(find)
>
> 올바른 옵션이 주어진 상태에서 비슷한 작업을 수행하도록 `ls`와 `find` 
> 명령어를 만들 수 있다. 하지만, 정상 상태에서 `ls`는 가능한 모든 것을 
> 목록으로 출력하는 반면에, `find`는 어떤 특성을 가진 것을 검색하고 보여준다는 점에서 차이가 있다. 

앞에서 언급했듯이, 명령-라인(command-line)의 힘은 툴을 조합하는데 있다. 파이프로 어떻게 조합하는지를 살펴봤고, 또 다른 기술을 살펴보자.
방금 보았듯이, `find . -name '*.txt'` 명령어는 현재 디렉토리 및 하위 디렉토리의 모든 텍스트 파일 목록을 보여준다. 어떻게 하면 `wc -l` 명령어와 조합해서 모든 파일의 행을 카운트할 수 있을까?

가장 간단한 방법은 `$()` 내부에 `find` 명령어를 위치하는 것이다.

~~~
$ wc -l $(find . -name '*.txt')
~~~
{:class="in"}
~~~
11 ./haiku.txt
300 ./data/two.txt
70 ./data/one.txt
381 total
~~~
{:class="out"}

쉘이 상기 명령어를 실행할 때, 처음 수행하는 것은 `$()` 내부를 실행하는 것이다. 그리고 나서 `$()` 표현식을 명령어의 출력 결과로 대체한다. `find`의 출력 결과가 3개의 파일 이름, 즉, `./data/one.txt`, `./data/two.txt`, `./haiku.txt`이여서, 쉘은 다음과 같이 명령문을 구성하게 된다.

~~~
$ wc -l ./data/one.txt ./data/two.txt ./haiku.txt
~~~
{:class="in"}

상기 명령문이 사용자가 원하는 것이다. 이 표현식이 `*`과 `?` 같은 와일드카드를 전개할 때 정확하게 쉘이 수행하는 것이다. 
하지만 자신의 "와일드카드"로 사용자가 원하는 임의의 명령어를 사용하자.

`find`와 `grep`을 함께 사용하는 것은 일반적이다. `find`가 패턴을 매칭하는 파이을 찾고 `grep`이 또 다른 패턴과 매칭하는 파일 내부의 행을 찾는다. 예제로 다음에 현재의 부모 디렉토리에서 모든 `.pdb` 파일에 "FE" 문자열을 검색해서 철 원자를 포함하는 PDB파일을 찾을 찾을 수 있다.

~~~
$ grep FE $(find .. -name '*.pdb')
~~~
{:class="in"}
~~~
../data/pdb/heme.pdb:ATOM     25 FE           1      -0.924   0.535  -0.518
~~~
{:class="out"}

> #### 바이너리 파일(Binary File)
>
> 텍스트 파일에 있는 것을 찾는데만 배타적으로 집중했다. 데이터가 만약 
> 이미지로, 데이터베이스로, 혹은 다른 형식으로 저장되어 있다면 어떨까?
> 한가지 선택사항은 `grep` 같은 툴을 확장해서 텍스트가 아닌 형식도 다루게 한다.
> 이 접근법은 일어나지 않았고, 아마도 그러지 않을 것이다. 왜냐하면 지원할 
> 너무나 많은 형식이 존재하기 때문이다.
> 
> 두번째 선택사항은 데이터를 텍스트로 변환하거나 데이터에서 텍스트같은 비트를 추출하는 것이다.
> 아마도 가장 흔한 접근 방법으로 정보를 추출하기 위해서 데이터 형식마다 하나의 툴만 개발하면 되기 때문이다.
> 한편으로, 이 접근법은 간단한 것을 쉽게 할 수 있게 한다. 부정적인 면으로, 
> 복잡한 것은 일반적으로 불가능하다. 예를 들어, `grep`을 이리 저리 사용해서 
> 이미지 파일에서 X와 Y 크기를 추출하는 프로그램을 작성하기는 쉽다. 하지만, 
> 공식을 담고 있는 엑셀 같은 스프레드쉬트 셀에서 값을 찾아내는 것을 어떻게 작성할까?
> 
> 세번째 선택사항은 쉘과 텍스트 처리가 모두 한계를 가지고 있다는 것을 인지하고 
> 대신에 파이썬 같은 프로그램 언어를 사용하는 것이다. 이러한 시점이 왔을 때 
> 쉘에서 너무 고생하지 마세요. 파이썬을 포함한 많은 프로그래밍 언어가 많은 
> 아이디어를 여기에서 가져왔다. 모방은 또한 칭찬의 가장 충심어린 형태이기도 하다.

### 결론

유닉스 쉘은 지금 사용하는 대부분의 사람보다 나이가 많다. 그토록 오랫동안 생존한 이유는 지금까지 만들어진 가장 생산성이 높은 프로그래밍 환경 중 하나 혹은 가장 생산성은 높기 환경이기 때문이다. 구문이 암호스러울 수도 있지만, 숙달한 사람은 다른 명령어를 대화하듯이 실험할 수 있고 나서 자신의 작업을 자동화하기 위해서 학습한 것을 사용한다. 그래픽 사용자 인터페이스(GUI)가 처음에는 더 좋을 수 있지만, 여진히 두번째는 쉘이 최강이다.
화이트헤드(Alfred North Whitehead) 박사가 1911년 썼듯이 "문명은 생각없이 수행할 수 있는 중요한 작업의 수를 확장함으써 발전한다.(Civilization advances by extending the number of important operations which we can perform without thinking about them.")

<div class="keypoints" markdown="1">

#### 주요점
*   `find` 명령어를 사용해서 파일과 디렉토리를 찾고, `grep`을 사용해서 파일에 텍스트 패턴을 찾으세요.
*   `$(command)` 명령어의 출력 결과를 우선 삽입한다.
*   `man command` 주어진 명령어에 대해서 매뉴얼 페이지를 화면에 출력한다.*   

</div>

<div class="challenge" markdown="1">
다음 쉘 스크립트에 대해서 무슨 것을 수행하는지 짧은 설명문을 작성하세요.

<div class="file" markdown="1">
~~~
find . -name '*.dat' | wc -l | sort -n
~~~
</div>
</div>

<div class="challenge" markdown="1">
`grep` 명령어의 `-v` 옵션은 패턴 매칭을 반전해서 패턴과 매칭하지 <em>않는</em> 행만 출력된다.
다음 명령어 중에서 어느 것이 `ose.dat`로 끝나는 (예로, `sucrose.dat` 혹은 `maltose.dat`), 하지만 `temp` 단어는 *포함하지 않는* `/data` 디렉토리에 있는 모든 파일을 찾아낼까요?

1. `find /data -name '*.dat' | grep ose | grep -v temp`

2. `find /data -name ose.dat | grep -v temp`

3. `grep -v temp $(find /data -name '*ose.dat')`

4. 위 어떤 것도 아님.
</div>
