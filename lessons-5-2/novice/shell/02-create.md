---
layout: page
title: 파일과 디렉토리 생성
---

<div class="objectives" markdown="1">

#### 목표
*   주어진 도표에 맞는 계층적 디렉토리 구조 생성하기.
*   편집기 혹은 이미 만들어진 파일을 복사하거나 이름을 바꾸어서 상기 계층적 디렉토리에 파일을 생성하기.
*   명령 라인을 사용해서 디렉토리의 내용 화면에 출력하기.
*   특정 파일과 디렉토리 혹은 각각을 따로 삭제하기.  

</div>
이제는 어떻게 파일과 디렉토리를 살펴보는지 알게되었지만, 어떻게 파일과 디렉토리를 먼저 생성할 수 있을까요? Nelle의 홈 디렉토리, `/users/nelle`, 로 돌아가서  `ls -F` 명령어를 사용하여 무엇을 담고 있는지 살펴봅시다.

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle
~~~
{:class="out"}
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

명령어 `mkdir thesis`을 사용하여 새 디렉토리 `thesis`를 생성합시다.(출력되는 것은 하나도 없습니다.)

~~~
$ mkdir thesis
~~~
{:class="in"}

이름에서 유출를 할 수도 하지 못할 수도 있지만, `mkdir`은 "make directory(디렉토리 생성하기)"를 의미한다. `thesis`는 상대 경로여서(즉, 앞선 슬래쉬가 없음) 새로운 디렉토리는 현재 작업 디렉토리 밑에 만들어진다.

~~~
$ ls -F
~~~
{:class="in"}
~~~
creatures/  north-pacific-gyre/  thesis/
data/       notes.txt            writing/
Desktop/    pizza.cfg
molecules/  solar.pdf
~~~
{:class="out"}

하지만 `thesis` 디렉토리에는 아직 아무것도 없다.

~~~
$ ls -F thesis
~~~
{:class="in"}

`cd` 명령어를 사용하여 `thesis`로 작업 디렉토리를 변경하자.
   Nano 텍스트 편집기를 실행해서 `draft.txt` 파일을 생성하자.

~~~
$ cd thesis
$ nano draft.txt
~~~
{:class="in"}

> #### 어느 편집기가 좋을까요?
> 
>`nano`가 텍스트 편집기다"라고 말할 때, 정말 "텍스트"만 의미한다. 즉, 일반 문자 데이터만
> 작업할 수 있고, 테이블, 이미지, 혹은 다른 사람 친화적 미디어는 작업할 수 없다. 
> 예제로 `nano`를 사용하는데 이유는 거의 누구나 훈련없이 사용할 수 있기 때문이다. 
> 하지만 실제 작업에는 좀더 강력한 편집기를 사용하기 바란다.
> 유닉스 시스템 계열(맥 OS X, 리눅스)에서는 많은 프로그래머가 [Emacs](http://www.gnu.org/software/emacs/) 혹은 [Vim](http://www.vim.org/)을 사용하는데 둘다 완전히
> 비직관적이고 심지어 유닉스 표준을 따른다. 혹은 그래픽 편집기로
> [Gedit](http://projects.gnome.org/gedit/)를 사용한다. 
> 윈도우에서는 [Notepad++](http://notepad-plus-plus.org/)를 사용하는 것도 좋다.
> 
> 무슨 편집기를 사용하든, 파일을 검색하고 저장하는 것을 앞 필요가 있다. 쉘에서 편집기를 시작하면,
> (아마도) 현재 작업 디렉토리가 디폴트 위치가 된다.
>  컴퓨터 시작 메뉴에서 시작한다면, 대신에 데스크톱 혹은 문서 디렉토리에 파일을 저장하고 싶을지도 모른다. 
> "다른이름으로 저장하기"로 다른 디렉토리로 이동하여 작업 디렉토리를 변경할 수 있다.

텍스트 몇 줄을 타이핑하고, 컨트롤+O (Control-O)를 눌러서 데이터를 디스크에 쓰면 저장된다.

<img src="img/nano-screenshot.png" alt="Nano in Action" />

파일이 저장되면, 컨트롤+X (Control-X)를 사용하여 편집기를 끝내고 쉘로 돌아간다. (유닉스 문서에서 `^A`로 줄여서 "컨트롤+A(control-A)"를 표기한다.) `nano`는 화면에 어떤 출력도 뿌려주지 않고 끝내지만, `ls` 명령어를 사용하여 `draft.txt` 파일이 생성된 것을 확인할 수 있다.

~~~
$ ls
~~~
{:class="in"}
~~~
draft.txt
~~~
{:class="out"}

`rm draft.txt`을 실행해서 깨끗이 정리합시다.

~~~
$ rm draft.txt
~~~
{:class="in"}

이 명령문은 파일을 제거한다. (rm은 "remove"를 줄였다.)
`ls`를 다시 실행하면, 화면에 출력되는 것은 없고 파일이 사라진 것을 확인할 수 있다.

~~~
$ ls
~~~
{:class="in"}

> #### 삭제는 영원하다.
> 
> 유닉스에는 휴지통이 없다. 파일을 삭제하면 파일시스템의 관리대상에서 빠져서 디스트 저장공간이 
> 다시 재사용되게 한다. 삭제된 파일을 찾아 되살리는 툴이 존재하지만, 어느 상황에서나 동작한다는
> 보장은 없다. 왜냐하면 컴퓨터가 파일이 저장된 공간을 바로 재사용할지 모르기 때문이다.

이번에는 파일을 다시 생성해서 `cd ..`를 사용하여 `/users/nelle` 상위 디렉토리로 이동해보자.

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle/thesis
~~~
{:class="out"}
~~~
$ nano draft.txt
$ ls
~~~
{:class="in"}
~~~
draft.txt
~~~
{:class="out"}
~~~
$ cd ..
~~~
{:class="in"}

`rm thesis`을 사용하여 전체 `thesis` 디렉토리를 제거하려고 하면 오류 메시지가 생긴다.

~~~
$ rm thesis
~~~
{:class="in"}
~~~
rm: cannot remove `thesis': Is a directory
~~~
{:class="err"}

`rm` 명령어는 파일에만 동작하고 디렉토리에는 동작하지 않기 때문에 오류가 발생한다.
 올바른 명령어는 `rmdir`이고 "remove directory(디렉토리 제거하기)"를 줄여서 표현한다.
 하지만 이것도 동작하지 않는데 이유는 삭제하려는 디렉토리가 비어있지 않기 때문이다.

~~~
$ rmdir thesis
~~~
{:class="in"}
~~~
rmdir: failed to remove `thesis': Directory not empty
~~~
{:class="err"}

이 작은 안전 기능이 많은 사람에게서 정말 많은 슬픔에서 구해줬다. 특히, 여러분이 타이핑에 초보라면 더욱 그렇다.
`thesis` 디렉토리를 제거하려면, 먼저 `draft.txt` 파일을 삭제해야 한다.

~~~
$ rm thesis/draft.txt
~~~
{:class="in"}

The directory is now empty, so `rmdir` can delete it:

~~~
$ rmdir thesis
~~~
{:class="in"}

> #### 큰 힘에는 큰 책임이 따른다. (With Great Power Comes Great Responsibility)
> 
> 디렉토리에 파일을 제거하고 디렉토리를 제거하는 방식은 지루하고 시간이 많이 걸린다. 
> 대신에 `-r` 옵션을 가진 `rm` 명령어를 사용할 수 있다. 
> `-r` 옵션은 "recursive(재귀적)"을 나타낸다.
> 
> ~~~
> $ rm -r thesis
> ~~~
> 
> 디렉토리에 모든 것을 삭제하고 나서 디렉토리 자체도 삭제한다. 
> 만약 디렉토리가 하위 디렉토리를 가지고 있다면, `rm -r`은 하위 디렉토리에도 같은 작업을 
> 반복한다. 매우 편리하지만, 부주위하게 사용되면 피해는 엄청나다.

다시한번 디렉토리와 파일을 생성하자. 이번에는 `thesis/draft.txt` 경로에서 `nano`를 실행함을 주목하자. 이전에는 `thesis`디렉토리로 가서 `draft.txt`에 `nano`를 실행했다.

~~~
$ pwd
~~~
{:class="in"}
~~~
/users/nelle
~~~
{:class="out"}
~~~
$ mkdir thesis
~~~
{:class="in"}
~~~
$ nano thesis/draft.txt
$ ls thesis
~~~
{:class="in"}
~~~
draft.txt
~~~
{:class="out"}

`draft.txt`는 특히 정보를 주는 이름이 아니어서 `mv`를 사용하여 파일 이름을 변경하자. `mv`는 "move"의 줄임말이다.

~~~
$ mv thesis/draft.txt thesis/quotes.txt
~~~
{:class="in"}

첫 번째 매개변수는 `mv` 명령어에게 이동하려는 대상을 두번째 매개변수는 어디로 이동되는지를 나타낸다. 이번 경우에는 `thesis/draft.txt`을 `thesis/quotes.txt`으로 이동한다.
이렇게 파일을 이동하는 것은 파일 이름을 바꾸는 것과 동일한 효과를 가진다. 
`ls` 명령어를 사용하여 `thesis` 디렉토리가 이제 `quotes.txt` 한 파일만을 가지고 있음을 확인할 수 있다.


~~~
$ ls thesis
~~~
{:class="in"}
~~~
quotes.txt
~~~
{:class="out"}

일관되지는 않는데 `mv` 명령어는 디렉토리에도 쓸수 있다. 하지만, `mvdir` 명령어는 없다.

`quotes.txt` 파일을 현재 작업 디렉토리로 이동합시다. `mv`를 다시 사용한다. 하지만 이번에는 
두번째 매개변수로 디렉토리 이름을 사용해서 파일이름을 바꾸지 않고 새로운 장소에 놓는다. (이것이 왜 명령어가 "move(이동)"으로 불리는 이유다.)
이번 경우에 사용하는 디렉토리 이름이 앞에서 언급한 특수 디렉토리 이름 `.`이다.

~~~
$ mv thesis/quotes.txt .
~~~
{:class="in"}

효과는 과거 있었던 디렉토리의 파일을 현재 작업 디렉토리로 옮기는 것이다. 
`ls` 명령어가 `thesis` 디렉토리가 비였음을 보여준다.

~~~
$ ls thesis
~~~
{:class="in"}

더 나아가, `ls` 명령어를 파일 이름 혹은 디렉토리 이름의 매개변수로 사용하면 그 해당 파일 혹은 디렉토리만 화면에 보여준다.
이것을 사용하여 `quotes.txt` 파일이 현재 작업 디렉토리에 있음을 볼 수 있다.

~~~
$ ls quotes.txt
~~~
{:class="in"}
~~~
quotes.txt
~~~
{:class="out"}

`cp` 명령어는 `mv` 명령어와 거의 동일하게 동작한다.
차이점은 이동하는 대신에 복사한다는 것이다. 
매개변수로 두개의 경로를 가진 `ls` 명령어로 제대로 작업을 했는지 확인할 수 있다.대부분의 유닉스 명령어와 마찬가지로 `ls` 명령어에 한번에 수천개의 경로가 주어질 수 있다.

~~~
$ cp quotes.txt thesis/quotations.txt
$ ls quotes.txt thesis/quotations.txt
~~~
{:class="in"}
~~~
quotes.txt   thesis/quotations.txt
~~~
{:class="out"}

복사를 수행했는지 증명하기 위해서 현재 작업 디렉토리에서 `quotes.txt` 파일을 삭제하고 동일한 `ls` 명령어를 실행한다.
이번에는 현재 디렉토리에서 `quotes.txt` 파일을 찾을 수 없지만, 삭제하지 않은 `thesis` 폴더의 복사본은 찾아서 보여준다.

~~~
$ ls quotes.txt thesis/quotations.txt
~~~
{:class="in"}
~~~
ls: cannot access quotes.txt: No such file or directory
thesis/quotations.txt
~~~
{:class="err"}

> #### 또다른 유용한 축약 팁
> 
> 쉘은 경로의 시작 `~` (틸드,tilde) 문자를 "현재 사용자 홈 디렉토리"로 해석한다.
> 예를 들어, Nelle의 홈 디렉토리가 `/home/nelle`이면, `~/data`은 
> `/home/nelle/data`과 동일한다.
> 경로에 첫번째 문자일 경우에만 동작해서 `here/there/~/elsewhere`은 
> `/home/nelle/elsewhere`이 *아니다.*

<div class="keypoints" markdown="1">

#### 주요점
*   유닉스 문서는 '^A'이 "컨트롤+A(control-A)"를 의미한다.
*   쉘은 휴지통 개념이 없다. 무언가 삭제되면, 정말 삭제된다.
*   Nano는 매우 간단한 텍스트 편집기다. 실제 작업에는 제발 다른 편집기를 사용하세요.

</div>

<div class="challenge" markdown="1">
아래 보여진 일련의 명령문에 끝에 `ls`명령어의 출력값은 무엇일까요?

~~~
$ pwd
/home/jamie/data
$ ls
proteins.dat
$ mkdir recombine
$ mv proteins.dat recombine
$ cp recombine/proteins.dat ../proteins-saved.dat
$ ls
~~~
</div>

<div class="challenge" markdown="1">
다음이 주어졌다고 가정합니다.

~~~
$ ls -F
analyzed/  fructose.dat    raw/   sucrose.dat
~~~

무슨 명령어를 실행해야 아래 명령어를 실행했을 때 다음에 보여지는 출력을 생성할까요?

~~~
$ ls
analyzed   raw
$ ls analyzed
fructose.dat    sucrose.dat
~~~
</div>

<div class="challenge" markdown="1">

다음과 같이 몇개의 파일 이름과 디렉토리 이름이 주어졌을 때 `cp` 명령어는 무엇을 수행할까요?

~~~
$ mkdir backup
$ cp thesis/citations.txt thesis/quotations.txt backup
~~~

다음과 같이 세개 혹은 그 이상의 파일 이름이 주어졌을 때 `cp`는 무엇을 수행할까요?

~~~
$ ls -F
intro.txt    methods.txt    survey.txt
$ cp intro.txt methods.txt survey.txt
~~~
</div>

<div class="challenge" markdown="1">
`ls -R` 명령어는 디렉토리의 내용을 재귀적으로 화면에 출력한다. 즉, 하위 디렉토리, 하위의 하위 디렉토리 등등 알파벳 순으로 계층적 수준으로 뿌려준다. `ls -t` 명령어는 마지막 변경사항의 시간 순으로 내용을 화면에 출력한다. 즉, 가장 최근에 변경된 파일 혹은 디렉토리를 먼저 정렬하여 화면에 뿌려준다. `ls -R -t`은 파일과 디렉토리를 어떤 순서로 화면에 보여줄까요?
</div>
