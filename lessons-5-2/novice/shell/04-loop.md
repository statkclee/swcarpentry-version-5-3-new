---
layout: page
title: 루프(Loops)
---
<div class="objectives" markdown="1">

#### 목표
*   파일 집합에서 각 파일에 따로 따로 나누어서 하나 혹은 그 이상의 명령어를 적용하는 루프를 작성하기
*   루프가 실행되는 동안에 루프 변수가 취하는 값을 추적하기
*   변수 이름과 값의 차이에 대해 설명하기
*   공백과 어떤 문자부호 문자는 파일 이름에 사용되지 말아야 되는지 설명하기
*   무슨 명령어가 최근에 실행되었는지를 어떻게 확인하는지 시범으로 보여주기
*   명령어를 다시 타이핑하지 않고 최근에 실행된 명령어를 다시 실행하기

</div>
와일드카드와 탭 자동완성은 타이핑을 (타이핑 실수를) 줄이는 두가지 방법이다. 또다른 것은 쉘이 반복해서 어떤것을 수행하게 하는 것이다.
`basilisk.dat`, `unicorn.dat` 등등으로 이름 붙여진 수백개의 게놈 데이터 파일이 있다고 가정하자.
이번 예제애서, 단지 두개의 예제 파일만 있는 `creatures` 디렉토리를 사용할 것이지만 원칙은 훨씬 더 많은 파일에 즉시 적용될 수 있다.
신규 파일이 도착할 때, 기존 파일을 `original-basilisk.dat`과 `original-unicorn.dat`으로 이름을 변경하고 싶다. 하지만 다음을 사용할 수는 없다.

~~~
$ mv *.dat original-*.dat
~~~
{:class="in"}

왜냐하면 두 파일 경우에도 전개가 다음과 같이 될 것이다.

~~~
$ mv basilisk.dat unicorn.dat
~~~
{:class="in"}

이것은 파일을 백업하지 않고, `basilisk.dat` 파일 무엇이든지 `unicorn.dat` 파일의 내용으로 교체할 것이다.

대신에 [루프(loop)](../../gloss.html#for-loop)를 사용해서 리스트의 각 파일에 대해서 임의의 연산을 수행할 수 있다. 여기 간단한 예제로 교대로 각 파일의 첫 3줄만을 화면에 출력한다.

~~~
$ for filename in basilisk.dat unicorn.dat
> do
>    head -3 $filename
> done
~~~
{:class="in"}
~~~
COMMON NAME: basilisk
CLASSIFICATION: basiliscus vulgaris
UPDATED: 1745-05-02
COMMON NAME: unicorn
CLASSIFICATION: equus monoceros
UPDATED: 1738-11-24
~~~
{:class="out"}

쉘이 키워드 `for`를 보게 되면, 쉘은 리스트의 각각에 대해서 명령문(혹은 명령문 그룹)을 반복할 것이라는 것을 안다.
이번 경우에는 리스트는 두 파일이름이다. 루프를 반복할 때마다, 현재 작업하고 있는 파일의 이름은 `filename`으로 불리는 [변수(variable)](../../gloss.html#variable)에 할당된다. 루프 내부에 변수 이름 앞에 `$` 기호를 붙여 변수의 값을 얻는다. 즉, `$filename` 루프가 첫번째 돌 때 `basilisk.dat`이 되고, `unicorn.dat`이 두번째가 되고 계속 이어진다.
마지막으로 실제 실행되는 명령어는 오랜 친구인 `head`가 되어서, 루프는 교대로 각 데이터 파일의 첫 3줄을 출력한다.

> #### 프롬프트 따라가기
> 쉘 프롬프트가 `$`에서 `>`으로 바뀌고 루프안에서 타이핑을 할 때 다시 계속된다.
> 두번째 프롬프트는, `>`, 완전한 명령문을 타이핑하는 것을 끝마치지 않았다는 것을 상기시키려고 다르다.

목적을 좀더 사람 독자에게 명확히 하기 위해서 루프의 변수를 `filename`로 했다. 쉘 자체는 변수가 어떻게 작명되든지 문제삼지 않는다.
만약 루프를 다음과 같이 작성하거나,

~~~
for x in basilisk.dat unicorn.dat
do
    head -3 $x
done
~~~
{:class="in"}

혹은

~~~
for temperature in basilisk.dat unicorn.dat
do
    head -3 $temperature
done
~~~
{:class="in"}

둘다 정확하게 동일하게 동작한다. *이렇게는 절대 하지 마세요.* 사람이 프로그램을 이해할 수 있을 때만 프로그램은 유용해서,
`x`같은 의미없는 이름이나, `temperature`같은 오해를 줄 수 있는 이름은 프로그램을 읽는 사람이 생각하기에 프로그램이 수행해야 할 것을 프로그램이 수행하지 못할 가능성을 높인다.

다음에 좀더 복잡한 루프가 있다.

~~~
for filename in *.dat
do
    echo $filename
    head -100 $filename | tail -20
done
~~~
{:class="in"}

쉘이 `*.dat`을 전개해서 쉘이 처리할 파일 리스트르 생성한다. 그리고 나서 [루프 몸통(loop body)](../../gloss.html#loop-body) 부분이 파일 각각에 대해서 두 명령어를 실행한다. 첫 명령어 `echo`는 명령 라인 매개변수를 표준 출력으로 화면에 뿌려준다. 예를 들어, 

~~~
$ echo hello there
~~~
{:class="in"}

다음을 화면에 출력한다.

~~~
hello there
~~~
{:class="out"}

이 사례에서, 쉘이 파일 이름으로 `$filename`을 전개했기 때문에, `echo $filename`은 단지 파일의 이름만 화면에 출력한다. 다음과 같이 작성할 수 없는 것에 주목한다.

~~~
for filename in *.dat
do
    $filename
    head -100 $filename | tail -20
done
~~~
{:class="in"}

왜냐하면, `$filename`이 `basilisk.dat`으로 전개될 때 루프의 처음에 쉘은 프로그램을 `basilisk.dat`을 실행하려고 한다. 마지막으로, `head`와 `tail` 조합은 무슨 파일이 처리되든지 81-100줄만 선택해서 화면에 뿌려준다.

> #### 파일, 디렉토리, 변수 등 이름에 공백
> 
> 루프의 파일 이름 전개는 파일 이름에 공백을 사용하지 말아야 하는 또다른 이유다. 데이터 파일이 
> 다음과 같은 이름으로 되었다고 가정하자.
> 
> ~~~
> basilisk.dat
> red dragon.dat
> unicorn.dat
> ~~~
> 
> 다음을 사용하여 파일을 처리하려고 한다면,
> 
> ~~~
> for filename in *.dat
> do
>     head -100 $filename | tail -20
> done
> ~~~
> 
> 쉘은 `*.dat`을 전개해서 다음을 생성한다.
> 
> ~~~
> basilisk.dat red dragon.dat unicorn.dat
> ~~~
> 
> 좀 오래된 Bash 혹은 대부분의 쉘과 마찬가지로, `filename`이 순차적으로 다음 값으로 할당될 것이다.
> 
> ~~~
> basilisk.dat
> red
> dragon.dat
> unicorn.dat
> ~~~
>
> 이것이 문제다. `head`는 `red`과 `dragon.dat` 파일을 읽을 수가 없다. 
> 왜냐하면 파일이 존재하지 않고 `red dragon.dat` 파일을 읽도록 할 수도 없다.
>
> 변수 사용을 [인용부호(quoting)](../../gloss.html#quoting) 처리해서 약간 더 강건하게 스크립트를 작성할 수 있다.
> 
> ~~~
> for filename in *.dat
> do
>     head -100 "$filename" | tail -20
> done
> ~~~
>
> 하지만, 파일명에 공백 혹은 다른 특수 문자의 사용을 피하는 것이 훨씬 더 간단하다.

원본 파일의 이름을 바꾸는 문제로 다시 돌아가서, 다음 루프를 사용해서 문제를 해결할 수 있다.

~~~
for filename in *.dat
do
    mv $filename original-$filename
done
~~~
{:class="in"}

상기 루프는 `mv` 명령문을 각 파일이름에 실행한다. 처음에 `$filename`이 `basilisk.dat`로 전개될 때, 쉘은 다음을 실행한다.

~~~
mv basilisk.dat original-basilisk.dat
~~~
{:class="in"}

두번째에는 명령문이 다음과 같다.

~~~
mv unicorn.dat original-unicorn.dat
~~~
{:class="in"}

> #### 두번 측정, 한번 실행
> 
> 루프는 많은 작업을 한번에 수행하는 방법이다. 혹은 만약 잘못된 작업을 수행한다면, 
> 한번에 많은 실수를 저지른다.
> 루프가 수행하는 것을 확인하는 한 방법은 실제로 실행하는 대신에 실행할 명령어를 echo를 사용하여 메아리 치는 것이다.
> 예를 들어, 다음과 가같이 파일 이름을 바꾸는 루프를 작성할 수 있다.
> 
> ~~~
> for filename in *.dat
> do
>     echo mv $filename original-$filename
> done
> ~~~
> 
> `mv`을 실행하는 대신에, 루프가 `echo`을 실행해서 실제 명령어를 *실행하지 않고* 다음을 화면에 출력한다.
> 
> ~~~
> mv basilisk.dat original-basilisk.dat
> mv unicorn.dat original-unicorn.dat
> ~~~
> 
> 그리고 나서, 위쪽 화살표를 사용해서 루프를 다시 화면에 출력하고, 뒤쪽 화살표를 사용해서 
> `echo` 단어에 도달해서 삭제하고 실제 `mv` 명령어로 루프를 실행하기 위해서 
> "엔터(enter)"키를 누른다. 이 방법은 실패하지 않는 완전한 것은 아니지만, 
> 루프가 어떻게 동작하고 있느지를 학습할 때, 어떤 일이 일어나고 있는지를 살펴볼 수 있는 간편한 방법이다.


### Nelle의 파이프라인: 파일 처리하기

Nelle은 지금 데이터 파일을 처리할 준비가 되었다. 아직 쉘을 어떻게 사용하는지 학습단계에 있기 때문에, 단계별로 요구되는 명령어를 차근히 작성하기로 마음먹었다. 첫번째 단계는 적합한 파일을 선택했는지를 확인하는 것이다. 'Z'가 아닌 'A' 혹은 'B'로 파일이름이 끝나는 것이 적합한 파일이라는 것을 명심하세요.

~~~
$ cd north-pacific-gyre/2012-07-03
$ for datafile in *[AB].txt
> do
>     echo $datafile
> done
~~~
{:class="in"}
~~~
NENE01729A.txt
NENE01729B.txt
NENE01736A.txt
...
NENE02043A.txt
NENE02043B.txt
~~~
{:class="out"}

다음 단계는 `goostats` 분석 프로그램이 생성할 파일이름을 무엇으로 할지 결정하는 것이다. 
"stat"을 각 입력 파일에 접두어로 두는 것은 간단해 보이는 작업을 수행하도록 루프를 변경한다.

~~~
$ for datafile in *[AB].txt
> do
>     echo $datafile stats-$datafile
> done
~~~
{:class="in"}
~~~
NENE01729A.txt stats-NENE01729A.txt
NENE01729B.txt stats-NENE01729B.txt
NENE01736A.txt stats-NENE01736A.txt
...
NENE02043A.txt stats-NENE02043A.txt
NENE02043B.txt stats-NENE02043B.txt
~~~
{:class="out"}

`goostats`을 아직 실행하지는 않았지만, 이제는 적합한 파일을 선택해서 올바른 출력 파일이름을 생성하는 것을 확신할 수 있다.

명령어를 반복적으로 타이핑하는 것은 귀찮은 일이지만 Nelle은 실수를 하는 것에 대해서 적정하고 있다.
그래서 루프를 다시 입력하는 대신에 위쪽 화살표를 누른다. 위쪽 화살표에 대응해서, 쉘은 한줄에 전체 루프를 다시 보여준다. (스크립트 부분을 구분하기 위해서 세미콜론을 사용)

~~~
$ for datafile in *[AB].txt; do echo $datafile stats-$datafile; done
~~~
{:class="in"}

왼쪽 화살표 키를 사용해서 Nelle은 백업하고 `echo`명령어에서 `goostats`으로 변경한다.

~~~
$ for datafile in *[AB].txt; do bash goostats $datafile stats-$datafile; done
~~~
{:class="in"}

엔터키를 누를 때, 쉘은 수정된 명령어를 실행한다. 하지만, 어떤 것도 일어나지 않는 것처럼 보인다. 출력이 아무것도 없다.
잠시후에 Nelle은 작성한 스크립트가 화면에 아무것도 출력하지 않아서, 실행되고 있는지, 얼마나 빨리 실행되는지에 대한 정보가 없다는 것을 깨닫는다.
컨트롤+C(Control-C)를 눌러서 작업을 종료하고 반복할 명령문을 위쪽 화살표로 선택하고 편빚해서 다음과 같이 작성한다.

~~~
$ for datafile in *[AB].txt; do echo $datafile; bash goostats $datafile stats-$datafile; done
~~~
{:class="in"}

> #### 시작과 끝
>
> 쉘에 `^A`, 콘트롤+A(Control-A)를 타이핑해서 라인의 처음으로 가고 `^E`를 쳐서 라인의
> 끝으로 이동한다.

이번에 프로그램을 실행할 때, 매 5초간격으로 한줄을 출력한다.

~~~
NENE01729A.txt
NENE01729B.txt
NENE01736A.txt
...
~~~
{:class="out"}

1518 곱하기 5초를 60으로 나누면 작성한 스크립트는 약 2시간 정도 실행한다고 볼 수 있다. 마지막 점검으로 또다른 터미널 윈도우를 열어서, `north-pacific-gyre/2012-07-03` 디렉토리로 가서 `cat stats-NENE01729B.txt`을 사용해서 출력 파일 중의 하나를 면밀히 조사한다.
출력결과가 좋아보여서 커피를 마시고 좀더 논문을 읽기로 한다.


> #### 역사를 아는 사람은 반복할 수 있다.
> 
> 앞선 작업을 반복하는 또다른 방법은 `history` 명령어를 사용해서 실행된 마지막 수백개의
> 명령어의 리스트를 얻고나서, 이들 명령어 중의 하나를 반복하기 위해서 `!123`("123"은 
> 명령 숫자로 교체된다.)을 사용하는 것이다. 예를 들어 Nelle이 다음과 같이 타이핑한다.
> 
> ~~~
> $ history | tail -5
>   456  ls -l NENE0*.txt
>   457  rm stats-NENE01729B.txt.txt
>   458  bash goostats NENE01729B.txt stats-NENE01729B.txt
>   459  ls -l NENE0*.txt
>   460  history
> ~~~
> 
> 그러면 단순히 `!458`을 타이핑함으로써 `NENE01729B.txt` 파일에 `goostats`을 
> 다시 실행할 수 있다.

<div class="keypoints" markdown="1">

#### 주요점
*   `for`루프는 리스트에 있는 모든 것에 명령어를 한번씩 반복한다.
*   모든 `for` 루프는 현재 "것(thing)"을 참조하는 변수를 필요로 한다.
*   `$name`을 사용해서 변수를 전개하라. (즉, 값을 얻어라.)
*   공백, 인용부호, 혹은 '*', '?'같은 와일드카드 문자를 파일이름에 사용하지 마라. 변수 전개를 복잡하게 한다.
*   파일에 일관된 이름을 부여해서 루프를 돌릴 때 와일드카드 패턴으로 파일을 찾기 용이하게 하라.
*   명령어를 편집하고 반복하기 위해서, 위쪽 화살표를 사용해서 앞선 명령어를 스크롤하라.
*   `history` 명령어를 사용해서 최근 명령어를 화면에 출력하고, `!number`를 사용해서 숫자로 명령을 반복하라.

</div>

<div class="challenge" markdown="1">
`ls` 명령어가 초기 다음과 같이 화면에 출력한다고 가정하자.

~~~
fructose.dat    glucose.dat   sucrose.dat
~~~

다음 스크립트의 출력결과는 무엇인가요?

~~~
for datafile in *.dat
do
    ls *.dat
done
~~~
</div>

<div class="challenge" markdown="1">
같은 디렉토리에서, 다음 루프의 효과는 무엇인가요?

~~~
for sugar in *.dat
do
    echo $sugar
    cat $sugar > xylose.dat
done
~~~

1.  `fructose.dat`, `glucose.dat`, `sucrose.dat`을 출력하고, 
      `sucrose.dat`을 복사해서 `xylose.dat`을 생성한다.
2.   `fructose.dat`, `glucose.dat`, `sucrose.dat`을 출력하고, 
      모든 파일 3개를 합쳐서 `xylose.dat`을 생성한다.
3.   `fructose.dat`, `glucose.dat`, `sucrose.dat`, `xylose.dat`을 출력하고,
      `sucrose.dat`을 복사해서 `xylose.dat`을 생성한다.
4.   위 어느 것도 아니다.

</div>

<div class="challenge" markdown="1">
`expr` 명령어는 명령-라인 매개변수를 사용하여 간단한 연산을 한다.

~~~
$ expr 3 + 5
8
$ expr 30 / 5 - 2
4
~~~

다음이 주어진 상태에서 다음의 출력값은 무엇인가?

~~~
for left in 2 3
do
    for right in $left
    do
        expr $left + $right
    done
done
~~~
</div>

<div class="challenge" markdown="1">
다음 루프가 무슨 작업을 하는지 말로 설명하세요.

~~~
for how in frog11 prcb redig
do
    $how -limit 0.01 NENE01729B.txt
done
~~~
</div>
