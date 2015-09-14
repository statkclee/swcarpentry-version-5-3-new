---
layout: lesson
root: ../..
---

## 명령-라인 프로그램 (Command-Line Programs)


IPython Notebook과 다른 인터랙티브 도구는 데이터를 탐색하고 프로토타입 코드를 작성하는데는 훌륭하지만 조만간 파이프라인에서 프로그램을 사용하거나
수천개의 파일을 처리하는데 쉘 스크립트를 실행할 것이다.
이를 위해서 작성한 프로그램이 다른 유닉스 명령-라인 도구와 함께 동작하도록 만들 필요가 있다.
예를 들어, 데이터 셋을 읽고 환자당 평균 염증값을 출력하는 프로그램을 만들고 싶다.

~~~
$ python readings.py --mean inflammation-01.csv
5.45
5.425
6.1
...
6.4
7.05
5.9
~~~

하지만, 첫 4번째 라인의 최소값을 보고자 할지 모른다.

~~~
$ head -4 inflammation-01.csv | python readings.py --min
~~~

혹은 여러개 파일을 순서대로 하나씩 최대 염증값을 보고자 할지 모른다.

~~~
$ python readings.py --max inflammation-*.csv
~~~

전반적인 요구사항은 다음과 같다.

1. 만약 파일 이름이 명령 라인에 주어지지 않는다면, [표준 입력(standard input)](../../gloss.html#standard-input)에서 데이터를 읽는다.
2. 만약 하나 혹은 그 이상의 파일이름이 주어진다면, 데이터를 파일이름에서 읽고 각 파일에 대해서 별도로 통계자료를 보고한다.
3. 무슨 통계치를 출력할 것인지 결정하기 위해서 `--min`, `--mean`, `--max` 옵션 플래그를 사용한다.

상기 요구사항을 만족하는 프로그램을 작성하기 위해서, 프로그램에서 어떻게 명령-라인 인자를 다루는지 그리고 어떻게 표준 입력을 받는지 파악할 필요가 있다.
이러한 질문을 다음에서 순차적으로 다룬다.


<div class="objectives" markdown="1">
#### 목표

*   명령-라인 인자 값을 프로그램에 사용한다.
*   명령-라인 프로그램에 옵션 플래그와 파일을 별도로 다룬다.
*   프로그램에서 표준 입력값을 데이터에서 읽어서 파이프라인에서 사용될 수 있게 한다.
</div>

### 명령-라인 인자 (Command-Line Arguments)


여러분이 선택한 텍스트 편집기를 사용하여, 텍스트 파일에 다음을 저장하세요.


<pre class="in"><code>!cat sys-version.py</code></pre>

<div class="out"><pre class='out'><code>import sys
print &#39;version is&#39;, sys.version
</code></pre></div>


첫번째 행은 "system"을 간략하게 줄인 `sys`라는 라이브러리를 가져온다.
가져온 라이브러리는 `sys.version` 같은 값을 정의하는데 Pythong 버젼이 무엇인지 기술한다.
IPython Notebook 내부에서 다음과 같이 스크립트를 실행할 수 있다.


<pre class="in"><code>%run sys-version.py</code></pre>

<div class="out"><pre class='out'><code>version is 2.7.5 |Anaconda 1.8.0 (x86_64)| (default, Oct 24 2013, 07:02:20) 
[GCC 4.0.1 (Apple Inc. build 5493)]
</code></pre></div>


혹은 다음과 같이도 가능하다.


<pre class="in"><code>!ipython sys-version.py</code></pre>

<div class="out"><pre class='out'><code>version is 2.7.5 |Anaconda 1.8.0 (x86_64)| (default, Oct 24 2013, 07:02:20) 
[GCC 4.0.1 (Apple Inc. build 5493)]
</code></pre></div>


첫번째 방법(`%run`)은 `.py` 파일에 담긴 프로그램을 실행하는데 IPython Notebook에 있는 특수 명령어를 사용한다.
두번째 방법이 좀더 일반적이다. 느낌표(`!`)가 Notebook에 쉘 명령어를 실행한다고 지시한다. 그래서 실행하는 명령어는 스크립트 이름과 `ipython`이 된다.


좀더 흥미로운 것을 수행하는 또다른 스크립트가 다음에 있다.


<pre class="in"><code>!cat argv-list.py</code></pre>

<div class="out"><pre class='out'><code>import sys
print &#39;sys.argv is&#39;, sys.argv
</code></pre></div>


이상한 이름 `argv`는 "argument values"(인자값)을 줄여 표현한 것이다.
파이썬이 프로그램을 실행할 때마다, 명령 라인에 주어진 모든 값을 받아서 
`sys.argv` 리스트에 넣는다. 그렇게 해서 프로그램이 인자값이 무엇인지를 판단할 수 있다.
만약 어떤 인자도 없이 프로그램을 실행한다면,


<pre class="in"><code>!ipython argv-list.py</code></pre>

<div class="out"><pre class='out'><code>sys.argv is [&#39;/Users/gwilson/s/bc/python/novice/argv-list.py&#39;]
</code></pre></div>


리스트의 유일한 것은 스트립트의 전체 경로정보가 되고 항상 `sys.argv[0]`을 차지한다.
하지만, 만약 몇개의 인자를 넣어 실행한다면, 


<pre class="in"><code>!ipython argv-list.py first second third</code></pre>

<div class="out"><pre class='out'><code>sys.argv is [&#39;/Users/gwilson/s/bc/python/novice/argv-list.py&#39;, &#39;first&#39;, &#39;second&#39;, &#39;third&#39;]
</code></pre></div>


그러면 파이썬은 각각의 인자를 마술같은 리스트에 추가한다.


지금까지 학습한 것을 가지고, 단독 데이터 파일에 환자 마다 평균값을 출력하는 `readings.py`를 작성해 보자.
첫번째 단계는 구현에 대한 윤곽을 잡는 함수와 실제 동작하는 함수에 대한 자리를 잡는 코드를 작성한다.
함수 이름을 원하는 무엇이든지 정할 수 있지만, 관례로 함수는 통상 `main`으로 부른다.


<pre class="in"><code>!cat readings-01.py</code></pre>

<div class="out"><pre class='out'><code>import sys
import numpy as np

def main():
    script = sys.argv[0]
    filename = sys.argv[1]
    data = np.loadtxt(filename, delimiter=&#39;,&#39;)
    for m in data.mean(axis=1):
        print m
</code></pre></div>


이 함수는 스크립트 이름을 `sys.argv[0]`에서 얻는데 이유는 그곳이 항상 이름이 놓여지는 장소이기 때문이다.
처리할 파일 이름은 `sys.argv[1]`에서 얻는다. 다음에 간단한 테스트가 있다.


<pre class="in"><code>%run readings-01.py inflammation-01.csv</code></pre>


어떠한 출력도 없는데 이유는 함수를 정의했지만, 실질적으로 호출을 하지 않았기 때문이다. `main`에 호출을 추가하자.


<pre class="in"><code>!cat readings-02.py</code></pre>

<div class="out"><pre class='out'><code>import sys
import numpy as np

def main():
    script = sys.argv[0]
    filename = sys.argv[1]
    data = np.loadtxt(filename, delimiter=&#39;,&#39;)
    for m in data.mean(axis=1):
        print m

main()
</code></pre></div>


그리고 실행하자.


<pre class="in"><code>%run readings-02.py inflammation-01.csv</code></pre>

<div class="out"><pre class='out'><code>5.45
5.425
6.1
5.9
5.55
6.225
5.975
6.65
6.625
6.525
6.775
5.8
6.225
5.75
5.225
6.3
6.55
5.7
5.85
6.55
5.775
5.825
6.175
6.1
5.8
6.425
6.05
6.025
6.175
6.55
6.175
6.35
6.725
6.125
7.075
5.725
5.925
6.15
6.075
5.75
5.975
5.725
6.3
5.9
6.75
5.925
7.225
6.15
5.95
6.275
5.7
6.1
6.825
5.975
6.725
5.7
6.25
6.4
7.05
5.9
</code></pre></div>


> #### 올바른 방법
>
> 만약 작성중인 프로그램이 복잡한 매개변수나 복수의 파일이름을 가진다면, `sys.argv`를 직접적으로 다루지 말아야 한다.
> 대신에 파이썬 `argparse` 라이브러리를 사용한다.
> `argparse` 라이브러리는 체계적으로 일반적인 경우를 처리하고, 또한 사용자를 위해서 프로그래머가 실용적인 오류 메시지를 제공하기 쉽게 만들었다.


<div class="challenges" markdown="1">
#### 도전 과제

1.  덧셈과 뺄셈을 수행하는 명령-라인 프로그램을 작성하세요.

    ~~~
    $ python arith.py 1 + 2
    3
    $ python arith.py 3 - 4
    -1
    ~~~

    만약 프로그램에 `*`을 사용해서 곱셈을 추가하려고 한다면 무슨 잘못이 있을까요?

2.  [03-loop.ipynb](earlier)에서 소개된 `glob` 모듈을 사용해서, 특정 확장자를 가진 파일을 현재 디렉토리에서 출력하는 `ls` 의 간단한 버젼을 작성하세요.
    
    ~~~
    $ python my_ls.py py
    left.py
    right.py
    zero.py
    ~~~
</div>

### 다수 파일 처리하기


다음 단계는 프로그램에게 파일 다수를 어떻게 처리하는지 가르치는 것이다. 파일당 60줄의 출력결과는 페이지를 넘기며 살펴보기에는 많은 불량이여서 3개의 작은 파일로 시작한다. 작은 파일 각각은 두 환자에 대한 3일치 데이터가 있다


<pre class="in"><code>!ls small-*.csv</code></pre>

<div class="out"><pre class='out'><code>small-01.csv small-02.csv small-03.csv
</code></pre></div>


<pre class="in"><code>!cat small-01.csv</code></pre>

<div class="out"><pre class='out'><code>0,0,1
0,1,2
</code></pre></div>


<pre class="in"><code>%run readings-02.py small-01.csv</code></pre>

<div class="out"><pre class='out'><code>0.333333333333
1.0
</code></pre></div>


작은 파일을 입력값으로 사용하는 것은 좀더 쉽게 결과를 확인할 수 있게 한다. 예를 들어, 프로그램이 각 행마다 올바르게 평균을 계산하는지 살펴볼 수 있다. 반면에 전에는 정말 믿음으로만 가지고 있었다. 이것은 또 다른 프로그래밍 규칙이다.
"[간단한 것을 먼저 시험하라(test the simple things first)](../../rules.html#test-simple-first)"

작성한 프로그램이 각각의 파일을 개별로 처리하길 원해서 각 파일 이름마다 한번씩 실행되는 루프가 필요하다.
명령 라인에 파일 이름을 지정한다면, 파일 이름은 `sys.argv`에 저장되지만, 주의가 필요하다.
`sys.argv[0]`는 항상 파일이름이 아니고 스크립트 이름이다.
작성한 프로그램이 임의 갯수의 파일에 대해서 실행될 수 있기 때문에 알수 없는 갯수의 파일이름을 처리할 필요가 있다.

해결책은 `sys.argv[1:]` 내용에 루프를 돌리는 것이다. 
'1'은 파이썬이 1번 위치에서 슬라이스를 시작해서 프로그램 이름이 포함되지 않도록 한다.
상한을 비워두었기 때문에 스라이스 인덱스가 리스트의 끝까지 가서 모든 파일 이름이 포함된다.
다음에 수정된 프로그램이 있다.


<pre class="in"><code>!cat readings-03.py</code></pre>

<div class="out"><pre class='out'><code>import sys
import numpy as np

def main():
    script = sys.argv[0]
    for filename in sys.argv[1:]:
        data = np.loadtxt(filename, delimiter=&#39;,&#39;)
        for m in data.mean(axis=1):
            print m

main()
</code></pre></div>


그리고 실행 결과가 다음에 있다.


<pre class="in"><code>%run readings-03.py small-01.csv small-02.csv</code></pre>

<div class="out"><pre class='out'><code>0.333333333333
1.0
13.6666666667
11.0
</code></pre></div>


Note:
이 지점에서, 스크립트 버젼 3개(`readings-01.py`, `readings-02.py`, `readings-03.py`)를 생성했다. 실무에서는 이렇게 하지는 않을 것이다. 대신에 readings.R 파일만 보관하고 기능향상 작업을 할 때마다 버젼 관리 시스템에 커밋한다. 하지만, 교육 목적으로 나란히 연속된 버젼이


<div class="challenges" markdown="1">
#### 도전 과제

1.  `check.R` 프로그램을 작성해서 인자로 하나 혹은 그 이상의 염증 데이터 파일 이름을 가지고 모든 파일이 동일한 행과 열을 가지는지 검증하게 하세요. 
프로그램을 시험하는 가장 최선의 방법은 무엇인가요?
</div>

### 명령어-라인 플래그(Command-Line Flags) 처리하기


다음 단계는 프로그램이 `--min`, `--mean`, `--max` 옵션 플래그에 관심을 두게 한다. 
플래그는 항상 파일 이름 앞에 위치해서 다음과 같이 수행할 수 있다.


<pre class="in"><code>!cat readings-04.py</code></pre>

<div class="out"><pre class='out'><code>import sys
import numpy as np

def main():
    script = sys.argv[0]
    action = sys.argv[1]
    filenames = sys.argv[2:]

    for f in filenames:
        data = np.loadtxt(f, delimiter=&#39;,&#39;)

        if action == &#39;--min&#39;:
            values = data.min(axis=1)
        elif action == &#39;--mean&#39;:
            values = data.mean(axis=1)
        elif action == &#39;--max&#39;:
            values = data.max(axis=1)

        for m in values:
            print m

main()
</code></pre></div>


작성한 것이 잘 동작한다.


<pre class="in"><code>%run readings-04.py --max small-01.csv</code></pre>

<div class="out"><pre class='out'><code>1.0
2.0
</code></pre></div>


하지만, 몇가지 잘못된 것이 있다.

1.   main 함수가 너무 커서 편안하게 읽기가 쉽지 않다.

2.   `action` 인자가 인정된 3개의 플래그 중에 하나가 아니라면, 프로그램을 각각의 
     파일 로딩(loading)하지만 아무것도 수행하기 않는다. 
     왜냐하면, 조건을 매칭하는 곳에서 어느 분기에도 해당되지 않기 때문이다. 
     이와 같이 [침묵하는 실패(Silent failures)](../../gloss.html#silent-failure)가 항상 디버그하기가 어렵다.
     
새로 작성한 버젼은 각 파일의 처리를 루프에서 빼내서 처리하는 자신만의 함수를 만들었다. 처리를 수행하기 전에 `action`이 사전에 정의된 플래그중의 하나인지를 검사해서 프로그램이 빨리 종료한다.


<pre class="in"><code>!cat readings-05.py</code></pre>

<div class="out"><pre class='out'><code>import sys
import numpy as np

def main():
    script = sys.argv[0]
    action = sys.argv[1]
    filenames = sys.argv[2:]
    assert action in [&#39;--min&#39;, &#39;--mean&#39;, &#39;--max&#39;], \
           &#39;Action is not one of --min, --mean, or --max: &#39; + action
    for f in filenames:
        process(f, action)

def process(filename, action):
    data = np.loadtxt(filename, delimiter=&#39;,&#39;)

    if action == &#39;--min&#39;:
        values = data.min(axis=1)
    elif action == &#39;--mean&#39;:
        values = data.mean(axis=1)
    elif action == &#39;--max&#39;:
        values = data.max(axis=1)

    for m in values:
        print m

main()
</code></pre></div>


상기 프로그램은 앞서 작성한 프로그램보다 더 길다. 
하지만, 좀더 완전히 이해하기 쉬운 8줄과 12줄 프로그램 덩어리로 쪼갰다.


파이썬은 [argparse](http://docs.python.org/dev/library/argparse.html) 라이브러리가 있어서
복잡한 명령어-라인 플래그를 처리하는데 도움이 된다. 
이번 학습에서는 [argparse](http://docs.python.org/dev/library/argparse.html) 라이브러리를 다루지 않을 것이다. 
하지만, 좀더 자세한 사항은 파이썬 공식 문서의 일부인 Tshepang Lekhonkhobe 의 [Argparse tutorial](http://docs.python.org/dev/howto/argparse.html)을 참고바란다.


<div class="challenges" markdown="1">
#### 도전 과제

1. 상기 프로그래을 다시 작성해서 `--min`, `--mean`, `--max` 대신에 `-n`, `-m`, `-x`을 각각 사용하게 하세요. 
    코드가 가독성이 좋습니까? 
    프로그램이 더 이해하기 좋습니까? 

2.  이와는 별도로, 프로그램을 변경해서 만약 어떤 행동(action)이 명기되지 않거나 혹은 잘못된 동작이 주어지면, 어떻게 사용되어야 하는지 설명하는 메시지를 출력하게 하세요.

3.  이와는 별도로, 프로그램을 수정해서 만약 어떤 행동(action)도 명기되지 않으면,
    데이터의 평균을 화면에 출력하게 코드를 작성하세요.
</div>

### 표준 입력 (Standard Input) 처리하기


프로그램이 다음으로 할 작업은 파일 이름이 주어지지 않았다면 표준 입력에서 데이터를 읽는 것이다. 파일이름을 파이프라인에 넣고 입력값으로 되돌려 사용하는 것이 예이다.
또 다른 스크립트를 작성해서 실험을 해보자.


<pre class="in"><code>!cat count-stdin.py</code></pre>

<div class="out"><pre class='out'><code>import sys

count = 0
for line in sys.stdin:
    count += 1

print count, &#39;lines in standard input&#39;
</code></pre></div>


상기 작은 프로그램은 자동으로 프로그램의 표준 입력에 연결되는 `sys.stdin`으로 불리는 
특수 "파일"에서 라인(행)을 읽어들인다.
파일을 별도로 열 필요는 없다. 즉, 프로그램이 시작할 때 파이썬과 운영시스템이 처리해준다.
하지만 정규 파일에서 할 수 있었던 거의 모든 것을 할 수 있게 한다. 
마치 정규 명령어-라인 프로그램인 것처럼 유닉스 쉘에서 실행을 시도해 보자.


<pre class="in"><code>!ipython count-stdin.py &lt; small-01.csv</code></pre>

<div class="out"><pre class='out'><code>2 lines in standard input
</code></pre></div>


`%run`을 사용해서 실행하면 어떨까요?


<pre class="in"><code>%run count-stdin.py &lt; small-01.csv</code></pre>

<div class="out"><pre class='out'><code>0 lines in standard input
</code></pre></div>


결과에서 알 수 있듯이, `%run` 명령어는 파일 되돌리기(redirection)을 이해하지 못한다. 단지 쉘의 무엇으로 이해한다.

흔한 실수는 다음과 같이 표준입력에서 읽어서 무언가 실행하려고 하는 것이다.

~~~
!ipython count_stdin.py small-01.csv
~~~

즉, 표준입력에서 파일로 되돌리는 문자(`<`)를 생략한 것이다. 
이 경우에 표준 입력에는 아무 것도 없어서 프로그램은 누군가 키보드로 무엇인가를 입력하기를 루프 시작에서 기다리기만 한다.
사람이 할 수 있는 것이 아무것도 없기 때문에 작성한 프로그램은 수렁에 빠진 듯 동작할 수 없게 된다.
노트북 `Kernel` 메뉴에서 `Interrupt` 옵션을 사용해서 정지시켜야 한다.

프로그램을 다시 작성해서 만약 어떤 파일이름도 제공된게 없다면 `sys.stdin`에서 데이터를 로딩한다. 
운좋게도, `numpy.loadtxt`는 파일이름 혹은 첫번째 매개변수로 열린 파일을 처리할 수 있다. 
그래서 실질적으로 `process`를 변경할 필요는 없다. `main`을 약간 수정한다.


~~~
def main():
    script = sys.argv[0]
    action = sys.argv[1]
    filenames = sys.argv[2:]
    assert action in ['--min', '--mean', '--max'], \
           'Action is not one of --min, --mean, or --max: ' + action
    if len(filenames) == 0:
        process(sys.stdin, action)
    else:
        for f in filenames:
            process(f, action)
~~~


작성한 프로그램을 시도해 보자. (잠시 후에 왜 `head`로 출력결과를 보냈는지 파악하게 된다.)


<pre class="in"><code>!ipython readings-06.py --mean &lt; small-01.csv | head -10</code></pre>

<div class="out"><pre class='out'><code>[TerminalIPythonApp] CRITICAL | Bad config encountered during initialization:
[TerminalIPythonApp] CRITICAL | Unrecognized flag: &#39;--mean&#39;
=========
 IPython
=========

Tools for Interactive Computing in Python
=========================================

    A Python shell with automatic history (input and output), dynamic object
    introspection, easier configuration, command completion, access to the
    system shell and more.  IPython can also be embedded in running programs.
</code></pre></div>


이럴 수가 있나:
데이터의 행별로 평균값 대신에 IPython 도움말이 왜 나왔을까?
IPython이 실행하는 프로그램에 대한 것과 명령-라인 인자에 대한 것을 분간이 어렵다.
의미를 명확히 하기 위하고 둘을 구분하기 위해서 `--`(이중 대휘)를 사용한다.


<pre class="in"><code>!ipython readings-06.py -- --mean &lt; small-01.csv</code></pre>

<div class="out"><pre class='out'><code>0.333333333333
1.0
</code></pre></div>


더 나아졌다. 이제 완료했다. 프로그램이 처음 기획했던 모든 것을 수행한다.


<div class="challenges" markdown="1">
#### 도전 과제

1.  `line-count.py` 프로그램을 작성해서 유닉스 `wc` 명령어처럼 동작하게 하세요.
    *   만약 어떤 파일이름도 주어지지 않는다면, 표준 입력에 행 숫자만을 보고한다.
    *   만약 하나 혹은 그 이상의 파일이름이 주어지면, 각 파일의 행 숫자와 전체 행 숫자를 보고한다.
</div>


<div class="keypoints" markdown="1">
#### 주요점

*   `sys` 라이브러리는 파이썬 프로그램과 프로그램이 실행되는 시스템을 연결한다.
*   `sys.argv` 리스트는 프로그램이 실행되는데 필요한 명령-라인 인자를 담고 있다.
*   침묵하는 실패(Silent failures)를 피한다.
*   "파일" `sys.stdin`을 사용해서 프로그램의 표준 입력에 연결한다.
*   "파일" `sys.stdout`을 사용해서 프로그램의 표준 출력에 연결한다.
</div>


<pre class="in"><code></code></pre>
