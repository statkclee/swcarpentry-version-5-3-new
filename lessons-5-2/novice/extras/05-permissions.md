---
layout: lesson
root: ../..
title: 권한 (Permissions)
---

*권한(permissions)*을 사용해서 유닉스는 누가 파일을 읽고, 변경할 수 있고, 실행할 수 있는지 제어한다.
이번 학습 후반부에 윈도우에서 어떻게 권한을 관리하는지도 살펴볼 것이다. 개념은 비슷하지만, 규칙은 다르다.

Nelle과 함께 시작하자. Nelle은 유일한 [사용자명(user name)](../../gloss.html#user-name),
`nnemo`, [사용자 ID(user ID)](../../gloss.html#user-id),
1404을 가지고 있다.

> #### 왜 정수형 ID일까?
>
> 왜 정수형 ID일까?
> 다시 한번 대답은 1970년 초반으로 거슬러 올라간다.
> `alan.turing` 같은 문자열은 길이가 변하고, 다른 문자열과 비교하는 것은 많은 명령어를 필요로 한다.
> 반대로, 정수는 매우 적은 저장공간 (일반적으로 문자 4개)만을 사용하고, 하나의 명령어로 비교를 수행할 수 있다.
> 연산을 빠르고 간단하게 하기 위해서
> 프로그래머는 종종 내부적으로 정수를 사용해서 기록을 하고 
> 정수를 표현을 위해서는 사용자가 친근한 텍스트로 변환하는 일종의 조회(lookup) 테이블을 사용한다.
> 종종 프로그래머는 프로그래머라 사용자 친근한 문자열 부분을 건너뛰고 정수만 사용한다.
> 마치 연구실에서 작업하는 연구자가 "아나콘다에 대한 알파 반응 실험" 대신에 "28번 실험"이라고 부르는 것과 유사하다.

사용자는 [그룹(groups)](../../gloss.html#user-group)에 임의의 숫자만큼 속할 수 있다. 각각의 그룹은 중복되지 않는 [그룹이름(group name)](../../gloss.html#user-group-name)과 숫자형식 [그룹 ID(group ID)](../../gloss.html#user-group-id)를 갖는다.
어느 사용자가 어떤 그룹에 속해있는가하는 목록 정보는 `/etc/group` 파일에 일반적으로 저장된다.
(만약 지금 유닉스 컴퓨터 앞에 있다면, 그 파일을 확인하기 위해서 `cat /etc/group` 명령어를 실행해보자.)

이제 파일과 디렉토를 살펴보자. 유닉스 컴퓨터의 모든 파일과 디렉토리는 하나의 소유자와 하나의 그룹에 속한다. 각 파일의 내용물과 함께 운영시스템은 파일을 소유하는 사용자 숫자 ID와 그룹정보를 저정한다. 

사용자-그룹 (user-and-group) 모델은 각 파일마다 시스템의 모든 사용자는 다음 세가지 범주 중에 하나에 속하게 된다: 파일의 소유자, 파일 그룹의 일원, 그리고 그 밖의 모든 사람. 

이 세가지 범주 각각에 대해서, 컴퓨터는 범주에 있는 사용자가 파일을 일고, 쓰고, 실행(만약 프로그램이라면 실행)할 수 있는지를 기록한다.

예를 들어, 만약 파일이 다음과 같은 권한집합으로 구성된다면,

<table class="table table-striped">
<tr><td></td><th>user</th><th>group</th><th>all</th></tr>
<tr><th>read</th><td>yes</td><td>yes</td><td>no</td></tr>
<tr><th>write</th><td>yes</td><td>no</td><td>no</td></tr>
<tr><th>execute</th><td>no</td><td>no</td><td>no</td></tr>
</table>

의미하는 바는 다음과같다.

*   파일 소유자는 파일을 읽고 쓸 수 있지만, 실행할 수는 없다.
*   파일 그룹의 다른 사용자는 읽을 수는 있지만, 변경하거나 실행할 수는 없다.
*   그 밖의 모든 사람은 어떤 것도 할 수 없다.

실제 동작하는 이 모델을 살펴보자.
`cd` 명령어로 디렉토리를 `labs`으로 변경하고 `ls -F`를 실행하면, `setup` 이름 끝에 `*`를 놓여진다.
`setup`이 실행가능하다는 것을 표현하는 방식이다.
즉, (아마도) 컴퓨터가 실행할 수 있는 무엇이다.

~~~
$ cd labs
$ ls -F
~~~
{:class="in"}
~~~
safety.txt    setup*     waiver.txt
~~~
{:class="out"}

> #### 필요하지만 충분하지는 않다.
>
> 실행가틍하다고 표기가 되었다는 사실이 실질적으로 
> 프로그램의 일종을 담고있다는 의미는 아니다.
> 다음에 소개되는 명령어를 사용하여 지금 작성하고 있는
> HTML 파일을 쉽게 실행가능한 파일로 표시할 수 있다.
> 사용하고 있는 운영시스템에 따라서 "실행(run)"을 하면 실행 실패(왜냐하면, 컴퓨터가 인식할 수 있는 명령집합을 담고 있지 않다.) 혹은 운영시스템이 자동으로 파일을 (웹브라우져) 같은 응용프로그램으로 열게 한다.

이제 `ls -l` 명령을 실행해보자:

~~~
$ ls -l
~~~
{:class="in"}
~~~
-rw-rw-r-- 1 vlad bio  1158  2010-07-11 08:22 safety.txt
-rwxr-xr-x 1 vlad bio 31988  2010-07-23 20:04 setup
-rw-rw-r-- 1 vlad bio  2312  2010-07-11 08:23 waiver.txt
~~~
{:class="out"}

`-l` 플래그는 `ls` 명령어가 장문 형식의 리스트 목록을 출력한다. 
정보가 많아서 차례로 칼럼을 하나씩 살펴보자.

오른쪽에 파일 이름이 있다. 왼쪽으로 이동하면 그 옆에 가장 최근에 변경된 시간과 날짜 정보가 있다. 백업 시스템과 다른 도구는 이 정보를 다양한 방식으로 이용한다. 하지만, 언제 여러분 (혹은 권한을 가진 다른 사람)이 마지막에 파일을 변경했는지 확인하는데 사용한다.

변경시간 옆에 바이트로 파일 크기와 파일 소유자와 그룹이름이 있다. (이 경우 `vlad`가 소유자 `bio`가 그룹이 된다)
지금 두번째 칼럼(각 파일마다 `1`을 표시)은 건너뛴다. 왜냐하면 가장 관심을 가져야 하는 것이 첫번째 칼럼이기 때문이다.
첫번째 칼럼은 파일 권한을 보여준다. 즉, 누가 읽고, 쓰고, 실행할 수 있는지 권한을 보여준다.

권한 문자열 중 하나를 좀더 자세히 살펴보자:
`-rwxr-xr-x`.
첫번째 문자는 무슨 타입(type)인지에 대한 정보를 제공한다. '-'은 정규 파일, 'd'은 디렉토리, 다른 문자는 좀더 소수의 사람만 이해할 수 있는 것을 의미한다.

다음 세 문자는 파일 소유자가 무슨 권한을 가지고 있는지 알려준다. 여기서 파일 소유자는 파일을 읽고, 쓰고, 실행도 할 수 있다: `rwx`.
중간의 세쌍둥이는 그룹 권한을 정보를 보여준다.
만약 권한이 없다면, 대쉬로 표현된다. 그래서 `r-x`은 "일고, 실행은 하지만, 쓸수는 없다."는 의미를 가진다.
마지막 세쌍둥이는 파일의 소유자도 파일의 그룹에 있지도 않은 그빡의 누구나 무엇을 할 수 있는지 보여준다.
이 경우, 다시 `r-x`이여서, 시스템의 그 밖의 모든 사람은 파일의 내용을 볼 수 있고 실행도 할 있지만, 변경할 수는 없다. 
권한을 변경하기 위해서, `chmod` 명령어를 사용한다. (`chmod`는 "change mode"의 약자)
Vlad가 강의하는 과정의 최종 점수에 대한 권한을 보여주는 긴 목록 정보가 다음에 있다.

~~~
$ ls -l final.grd
~~~
{:class="in"}
~~~
-rwxrwxrwx 1 vlad bio  4215  2010-08-29 22:30 final.grd
~~~
{:class="out"}

이럴 수가 있나요: 세상의 모든 사람이 읽을 수 있어요&mdash;그리고, 더욱 상황이 않좋게는 변경도 할 수 있어요.
(거의 확실하게 동작하지 않겠지만, 프로그램으로 성적 파일을 실행할도 수 있어요.)
소유자 권한을 `rw-`으로 변경하는 명령어는 다음과 같다.

~~~
$ chmod u=rw final.grd
~~~
{:class="in"}

'u'는 사용자(즉, 파일 소유자)의 권한을 변경한다는 신호다. 그리고 `rw`는 새로운 권한집합이다.
`ls -l` 명령어는 권한 변경이 동작하는 것을 보여준다. 왜냐하면, 소유자 권한이 이제 읽고 쓰는 것으로 설정이 변경되었다.

~~~
$ ls -l final.grd
~~~
{:class="in"}
~~~
-rw-rwxrwx 1 vlad bio  4215  2010-08-30 08:19 final.grd
~~~
{:class="out"}

그룹 권한을 읽을 수만 있도록 변경하기 위해서 `chmod`을 다시 실행하자.

~~~
$ chmod g=r final.grd
$ ls -l final.grd
~~~
{:class="in"}
~~~
-rw-r--rw- 1 vlad bio  4215  2010-08-30 08:19 final.grd
~~~
{:class="out"}

그리고 최종적으로 그 밖의 모든 사람(파일의 소유자도 그룹원도 아닌 시스템의 모든 사람)에게는 어떠한 권한도 주지 말자. 

~~~
$ chmod a= final.grd
$ ls -l final.grd
~~~
{:class="in"}
~~~
-rw-r----- 1 vlad bio  4215  2010-08-30 08:20 final.grd
~~~
{:class="out"}

'a'는 그 밖의 모든 사람("all")의 권한을 변경한다는 신호를 준다. "=" 오른쪽에 아무것도 없기 때문에, 그 밖의 모든 사람의 권한은 없다.

또한 권한으로 검색도 할 수 있다. 예를 들어, 사용자가 실행할 수 있는 파일을 찾기 위해서 `-type f -perm -u=x`을 사용한다.

~~~
$ find . -type f -perm -u=x
~~~
{:class="in"}
~~~
./tools/format
./tools/stats
~~~
{:class="out"}

더 진도를 나아가기 전에, `ls -a -l`을 실행해서 평상시에 숨겨져있는 디렉토리 항목을 포함하는 긴 형식 목록 정보를 얻어보자.

~~~
$ ls -a -l
~~~
{:class="in"}
~~~
drwxr-xr-x 1 vlad bio     0  2010-08-14 09:55 .
drwxr-xr-x 1 vlad bio  8192  2010-08-27 23:11 ..
-rw-rw-r-- 1 vlad bio  1158  2010-07-11 08:22 safety.txt
-rwxr-xr-x 1 vlad bio 31988  2010-07-23 20:04 setup
-rw-rw-r-- 1 vlad bio  2312  2010-07-11 08:23 waiver.txt
~~~
{:class="out"}

`.` 과 `..` (현재 디렉토리와 부모 디렉토리)에 대한 권한은 'd'로 시작한다. 하지만, 뒷쪽 권한부분을 살펴보자: 'x'는 "실행(execute)"기능이 활성화 된다는 의미다. 무슨 의미일까? 디렉토리는 프로그램이 아닌데&mdash; 어떻게 "실행"할 수 있을까?

사실 'x'는 디렉토리에 대해서 다른 무언가를 의미한다. 
디렉토리를 *돌아다닐(traverse)* 수 있는 권리는 있지만, 내용(콘텐츠)은 볼 수는 없다.
차이가 미묘하다. 그래서 예제를 통해서 살펴보자.
Vlad 홈 디렉토리는 `venus`, `mars`, `pluto`라는 3개의 하위 디렉토리를 담고 있다.

<img src="img/x-for-directories.svg" alt="Execute Permission for Directories" />

디렉토리 각각은 `notes`라는 하위 디렉토리가 있다. 하위의 하위 디렉토리는 다양한 파일을 담고 있다. 만약 `venus` 디렉토리에 대한 사용자 권한이 'r-x'라면, 그러면 만약 사용자가 `ls` 명령어를 사용해서 `venus`와 `venus/notes` 내용(콘텐츠)를 보려고 한다면, 컴퓨터는 양쪽을 모두 사용자가 볼 수 있게 한다.
만약 `mars` 디렉토리의 권한이 'r--'만 있다면,
`mars`와 `mars/notes` 디렉토리 내용을 모두 읽을 수 있게 한다.
하지만 `pluto` 디렉토리에 대한 권한이 '--x'만 있으면,
`pluto` 디렉토리에 무엇이 있는지 볼 수 없다.
`ls pluto` 명령어를 실행하면 내용을 볼 수 있는 권한이 없다고 시스템이 응답한다. 하지만, `pluto/notes` 내부를 보고자 한다면, 시스템이 사용자가 볼 수 있게 해준다.  
`pluto`를 통과해서 갈 수는 있으나 내부에 무엇이 있는지를 볼 수 없다. 이와 같은 기법이 그 밖의 모든 것을 공개하지 않고, 전체적으로 바깥 세상에 디렉토리의 일부만 보여주는 방법이다. 

#### 윈도우(Windows)는 어떨까?

상기 살펴본 것이 유닉스 권한관리의 기본이다. 서두에 언급했듯이, 윈도우에서는 다르게 동작한다. 윈도우에서 권한은 [접근 제어 목록(access control lists)](../../gloss.html#access-control-list), ACLs에서 정의된다.
ACT은 둘씩 짝지은 목록으로 각각은 "누가(who)"와 "무엇(what)"을 조합한다. 예를 들어, 미이라(Mummy)에게 읽거나 삭제할 수 있는 권한은 주지 않고 파일에 데이터만 추가하는 권한을 줄 수 있고, 프랑켄슈타인에게는 파일이 무엇을 담고 있는지 볼 수 없지만, 파일을 삭제할 수 있는 권한을 줄 수 있다.

유닉스 모델보다 좀더 유연하지만, 작은 시스템에서 이해하고 관리하기는 좀더 복잡하다. (만약 큰 컴퓨터 시스템이 있다면, *어떤것도* 관리하거나 이해하기 쉽지 않다.) 최신 유닉스 일부 모델은 ACL 뿐만 아니라 이전 읽기-쓰기-실행 권한 모델도 지원하지만, 거의 사용자가 잘 사용하지는 않는다.


An ACL is a list of pairs, each of which combines a "who" with a "what".
For example,
you could give the Mummy permission to append data to a file without giving him permission to read or delete it,
and give Frankenstein permission to delete a file without being able to see what it contains.

This is more flexible that the Unix model,
but it's also more complex to administer and understand on small systems.
(If you have a large computer system,
*nothing* is easy to administer or understand.)
Some modern variants of Unix support ACLs as well as the older read-write-execute permissions,
but hardly anyone uses them.
