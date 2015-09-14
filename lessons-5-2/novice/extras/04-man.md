---
layout: lesson
root: ../..
title: 매뉴얼 페이지 (Manual Page)
---

`man` (manual의 약어) 명령어로 모든 유닉스 시스템 명령어에 대한 도움말을 얻는다. 예를 들어, `cp` 에 대한 정보를 찾는 명령어가 다음에 있다.

~~~
$ man cp
~~~
{:class="in"}

화면에 표시되는 출력결과를 "매뉴얼 페이지(man page)"라고 한다.

매뉴얼 페이지는 쉘의 기본 파일 뷰어로 표시된다.
대체로 프로그램이 `more`를 호출한다. `more`가 콜론 (':')을 화면에 표시할 때, 다음 페이지를 보려고 스페이스바를, 도움말을 얻는데는 'h'를, 끝내기 위해서는 문자 'q'를 각각 누른다.

사용 지침서보다는 참고자료로 사용되도록 고안되어서,
`man` 출력결과는 보통 필요한 모든 것이 갖춰지도록 완전하지만 간결한다.
대부분의 매뉴얼 페이지는 절(section)으로 나눠진다.

*   NAME(이름):
    명령어의 이름과 간략한 기술을 포함한다.
*   SYNOPSIS(개요):
    옵션 및 필수 매개변수를 포함하여 명령어를 어떻게 실행하는지 설명한다.
    (나중에 구문을 설명한다.)
*   DESCRIPTION(기술):
    명령어의 모든 옵션에 대한 기술을 포함하고, 개요보다 좀더 자세한 기술.
    이번 절은 명령어가 어떻게 동작하는지에 대한 자세한 설명과 예제 사용을 포함할 수도 있다.
*   EXAMPLES(예제):
    따로 설명이 필요없이 예제가 나온다.
*   SEE ALSO(기타 참조):
    유용한 다른 명령어나 도움이 되는 다른 원천 정보를 목록으로 출력한다.

다른 절에는 저자(AUTHOR), 버그 보고(REPORTING BUGS), 저작권(COPYRIGHT), 이력(HISTORY), 버그(known BUGS), 호환(COMPATIBILITY) 정보를 포함한다.

#### 개요(Synopsis)를 읽는 방법

우분투 리눅스상에서 `cp` 명령어에 대한 개요가 다음에 있다.

~~~
SYNOPSIS
   cp [OPTION]... [-T] SOURCE DEST
   cp [OPTION]... SOURCE... DIRECTORY
   cp [OPTION]... -t DIRECTORY SOURCE...
~~~
{:class="out"}

상기 출력결과는 독자에게 명령어를 사용하는 방법이 3가지 있다고 제시한다. 첫번째 사용례를 살펴보자.

~~~
cp [OPTION]... [-T] SOURCE DEST
~~~
{:class="out"}

`[OPTION]`이 의미하는 바는 `cp` 명령은 하나 이상의 옵션 [플래그(flags)](../../gloss.html#command)가 올 수 있다. 꺾쇠괄호 기호로 옵션사항이 되고, 생략(...) 기호로 하나 이상의 옵션 플래그를 사용한다는 의미가 된다. 예를 들어, `[-T]`가 꺾쇠괄호와 생략부호 뒤에 있다는 사실은 선택 사항이지만, 만약 사용이 된다면 다른 모든 옵션 다음에 와야함을 의미한다.

`SOURCE`는 원천 파일 혹은 디렉토리를 나타낸다.
`DEST`는 목적 파일 혹은 디렉토리를 나타낸다.
각각의 정확한 의미는 기술(`DESCRIPTION`) 절 상단에 설명되어 있다.

다른 두가지 사용 예제도 비슷한 방식으로 읽을 수 있다.
주목할 것은 마지막 사용 예제에서
`-t` 옵션은 필수사항이라는 것이다. (왜냐하면 꺾쇠 괄호가 감싸고 있지 않다.)

기술(`DESCRIPTION`)항은 명령어와 사용법을 설명하는 문단 몇개로 시작하고 나서 사용가능한 옵션에 대해서 하나씩 기술해 나간다.

~~~
     The following options are available:

     -a    Same as -pPR options. Preserves structure and attributes of
           files but not directory structure.

     -f    If the destination file cannot be opened, remove it and create
           a new file, without prompting for confirmation regardless of
           its permissions.  (The -f option overrides any previous -n
           option.)

           The target file is not unlinked before the copy.  Thus, any
           existing access rights will be retained.

      ...  ...
~~~
{:class="out"}

#### 특정 옵션에 대한 도움말 찾기

관심있는 옵션으로 건너뛰고자 한다면, 슬래쉬 '/'를 사용해서 검색할 수 있다. (`man` 명령어 부분은 아니고, `more`의 기능이다.) 예를 들어, `-t` 옵션에 대해서 좀더 찾아보고자 한다면, `/-t` 타이핑하고 리턴키를 누른다.
그 후에 원하는 자세한 정보를 찾을 때까지 'n' 키를 눌러 다음 매칭되는 곳으로 이동한다. 

~~~
-t, --target-directory=DIRECTORY
     copy all SOURCE arguments into DIRECTORY
~~~
{:class="out"}

이 옵션은 간략한 형식 `-t`와 긴 형식 `--target-directory`가 있고 인자도 갖는다.
옵션의 의미는 모든 SOURCE 인자를 DIRECTORY로 복사한다. 그래서, 마지막에 디렉토리를 놓는 것 대신에 목적지를 명시적으로 줄수 있다.

#### 매뉴얼 페이지의 한계

매뉴얼 페이지는 명령어를 어떻게 실행하는지에 대한 빠른 확인을 하는데 유용하지만, 가독성은 좋지 않은 것으로 유명하다.
만약 매뉴얼 페이지에서 원하는 것을 찾지 못한다면&mdash;혹은 검색한 것을 이해하지 못한다면&mdash;
"유닉스 명령어를 복사"해서 선호하는 검색엔진에 입력해보라: 종종 더 도움이 되는 결과를 준다.

> #### 다음이 도움이 될지도...
>
> [explainshell.com](http://explainshell.com/) 사이트는 유닉스 명령어를 부분으로 쪼개서 각각이 무엇을 
> 수행하는지 잘 설명한다. 
> 슬프게도, 반대로는 동작하지 않습니다. 
