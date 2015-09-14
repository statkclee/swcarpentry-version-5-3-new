---
layout: lesson
root: ../..
title: 예외 처리 (Exceptions)
---

가정 설정문(assertions)을 이용하여 코드의 오류를 잡는데 도움이 되지만 파일이 사라지거나 잘못된 파일 형식같은 다른 이유로 뭔가 잘못될 수 있다.
대부분의 최신 프로그래밍 언어는 프로그래머가 [예외 처리(exceptions)](../../gloss.html#exception)를 사용해서 만약 모든 것이 정상이면 프로그램이 무엇을 수행하고, 만약 무엇인가 잘못되면 프로그램이 수행하는 것을 구별한다.
예외처리를 하는 것은 양쪽 경우 모두 작성한 코드를 읽기 쉽고, 이해하기 쉽게 한다.

예를 들어, 두개의 별개 파일에서 매개변수(parameter)와 그리드(grid)를 읽어들이고, 만약 파일을 읽는데 둘중에 하나라도 뭔가 잘못되면 오류를 보고하는 코드 일부가 다음에 있다.

~~~
try:
    params = read_params(param_file)
    grid = read_grid(grid_file)
except:
    log.error('Failed to read input file(s)')
    sys.exit(ERROR)
~~~
{:class="in"}

`try` 와 `except` 키워드를 사용하여 정상인 경우와 오류 처리 코드를 합쳤다.
`if` 와 `else` 와 마찬가지로 함께 동작한다. 
`try` 다음의 문장은 만약 모든 것이 잘 동작한다면 무엇을 해야하는지를 나타내는 반면에 `except` 다음의 문장은 뭔가 잘못된다면 프로그램이 무엇을 해야하는지 나타낸다.

사실 알지 못하게 앞에서 예외처리를 보았습니다. 왜냐하면, 예외사항이 발생했을 때, 파이썬은 예외사항을 출력하고 프로그램을 정지한다. 예를 들어, 존재하지 않는 파일을 열게 될 때, `IOError` 라는 일종의 예외를 발생시킨다. 반면에 존재하지 않는 리스트 항목에 접근할 때는 `IndexError` 를 발생시킨다.

~~~
open('nonexistent-file.txt', 'r')
~~~
{:class="in"}
~~~
---------------------------------------------------------------------------
IOError                                   Traceback (most recent call last)

<ipython-input-13-58cbde3dd63c> in <module>()
----> 1 open('nonexistent-file.txt', 'r')

IOError: [Errno 2] No such file or directory: 'nonexistent-file.txt'
~~~
{:class="err"}
~~~
values = [0, 1, 2]
print values[999]
~~~
{:class="in"}
~~~
---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)

<ipython-input-14-7fed13afc650> in <module>()
1 values = [0, 1, 2]
----> 2 print values[999]

IndexError: list index out of range
~~~
{:class="err"}

만약 단순히 프로그램이 갑자기 중단되는 것 말고 뭔가 다른 것을 원한다면, `try` 와 `except`를 사용하여 직접 오류를 다룰 수 있다.

~~~
try:
    reader = open('nonexistent-file.txt', 'r')
except IOError:
    print 'Whoops!'
~~~
{:class="in"}
~~~
Whoops!
~~~
{:class="err"}

파이썬이 상기 코드를 실행할 때, `try` 내부의 문장을 실행한다. 만약 정상적으로 동작하면, `except` 블록 문장을 실행하지 않고 건너 뛴다.
하지만 만약 `try` 블록 내부에 예외사항이 발생하면, 파이썬이 `except`에 명시된 유형과 예외 유형을 비교한다.
만약 둘 사이가 매칭되면, `except` 블록 내부 코드를 실행한다.

존재하지 않는 파일 혹은 읽어들이는데 권한이 없는 프로그램 같은 경우 입출력과 관련된 문제가 발생할 때, 파이썬이 사용하는 예외 유형은 `IOError`이다.
`if`문에 많은 문장을 쓸 수 있듯이, `try` 블록에 원하는 만큼 코드를 쓸 수 있다.
또한 몇가지 다른 유형의 오류도 처리할 수 있다. 예를 들어, 그리드 각지점의 엔트로피를 계산하는 코드가 다음에 있다.

~~~
try:
    params = read_params(param_file)
    grid = read_grid(grid_file)
    entropy = lee_entropy(params, grid)
    write_entropy(entropy_file, entropy)
except IOError:
    report_error_and_exit('IO error')
except ArithmeticError:
    report_error_and_exit('Arithmetic error')
~~~
{:class="in"}

`try` 내부 함수 4개를 평소와 마찬가지로 파이썬이 실행한다.
만약 오류가 함수중에서 발생한다면, 파이썬은 즉시 다음으로 내려가서 
`except`에서 해당하는 예외 유형을 검색한다. 
만약 예외 유형이 `IOError`이면, 파이썬은 첫번째 오류 처리(error handler)하는 곳으로 간다. 만약 예외 유형이 `ArithmeticError`이면, 대신에 파이썬은 두번째 오류 처리(error handler)하는 곳으로 간다.
파이썬이 일련의 `if`/`elif`/`else` 문중에서 하나의 분기만 수행하는 것과 마찬가지로, 여러 예외 유형 중에서 하나만 처리한다.

이와 같이 코드를 배치하여 가독성은 좋게 만들었지만, 무엇인가 중요한 것을 잃어버렸다. 
`IOError` 분기점에서 출력되는 메시지가 무슨 파일이 문제를 발생시켰는지 알려주지 않는다. 오류에 대한 정보를 저장하기 위해서 파이썬이 생성하는 개체를 잡아서 꼭 붙들 수만 있다면 더욱 잘 할 수 있다.

~~~
try:
    params = read_params(param_file)
    grid = read_grid(grid_file)
    entropy = lee_entropy(params, grid)
    write_entropy(entropy_file, entropy)
except IOError as err:
    report_error_and_exit('Cannot read/write' + err.filename)
except ArithmeticError as err:
    report_error_and_exit(err.message)
~~~
{:class="in"}

`try`에서 뭔가 잘못되면, 파이썬이 예외 개체를 생성하고, 정보를 채워서 변수 `err`에 할당한다. (변수이름에 관해서 특별한 것은 없다. 원하는 임의의 이름을 사용한다.) 정확하게 무슨 정보가 기록되는지는 어떤 종류의 오류가 발생하느냐에 달려있다. 파이썬 문서에는 자세하게 각 오류 유형별 특징을 기술하고 있지만, 여기서는 예외 개체만 출력한다. 입출력(I/O) 오류의 경우 물제를 일으킨 파일 이름을 출력한다. 연산 오류의 경우에는 예외 개체에 내장된 메세지를 출력하는데 이것은 어쨌든 파이썬이 수행했을 것과 동일하다.

예외처리가 어떻게 동작하는지 많은 것을 학습했다. 어떻게 사용할 수 있을까요? 몇몇 프로그래머는 `try` 와 `except`를 사용해서 프로그램에 기본 동작으로 사용한다. 예를 들어, 만약 사용자가 요청한 그리드 파일을 코드가 읽을 수 없다면,
대신에 기본 그리드를 생성한다.

~~~
try:
    grid = read_grid(grid_file)
except IOError:
    grid = default_grid()
~~~
{:class="in"}

다른 프로그래머는 명시적으로 그리드 파일을 테스트하고 
제어 흐름에 `if` 와 `else` 를 사용한다.

~~~
if file_exists(grid_file):
    grid = read_grid(grid_file)
else:
    grid = default_grid()
~~~
{:class="in"}

거의 취향의 문제이지만, 두번째 스타일을 추천한다. 규칙으로서 
예외처리는 예외적인 경우를 다루는데만 사용되어야 한다.
만약 프로그램이 기본 그리드로 어떻게 돌아가는지 알고 있다면, 예상하지 못한 이벤트는 아니다. 설사 동일한 것이라도, `try`와 `except` 대신에 `if` 와 `else`를 사용하는 것은 코드를 읽는 사람에게 다른 신호를 전달한다.

종종 초보자가 예외처리 스타일에 관한 질문을 한다. 이런 질문을 다루기 전에 상기 예제에 여러분들이 간과한 것이 있다. 예외사항은 실질적으로 장기적 관점으로 처리되어야 한다. 즉시 처리하지 말아야 한다. 코드를 다른 관점으로 살펴보자. 

~~~
try:
    params = read_params(param_file)
    grid = read_grid(grid_file)
    entropy = lee_entropy(params, grid)
    write_entropy(entropy_file, entropy)
except IOError as err:
    report_error_and_exit('Cannot read/write' + err.filename)
except ArithmeticError as err:
    report_error_and_exit(err.message)
~~~
{:class="in"}

`try` 블록의 4 줄은 모두 함수 호출이다.
4개 함수 모두 예외사항을 잡아서 처리할 수도 있다.
하지만, 만약 내부적으로 처리되지 *않은* 예외사항이 하나라도 발생한다면,
파이썬은 `except`와 매칭하려고 호출 코드를 검색한다. 
만약 거기서 예외처리 코드를 찾지 못한다면, 함수 호출자를 들여다본다. 
만약 예외처리기를 찾지 못하고 메인 프로그램으로 다시 돌아온다면, 파이썬 기본 동작은 줄곧 보아왔던 오류 메시지를 출력한다.

이러한 규칙이 [throw low, catch high](../rules.html#throw-low-catch-high) 규칙의 기원이다. 작성한 프로그램에는 오류가 발생할 수 있는 장소가 많다.
하지만 오류를 눈에 띄게 처리할 수 있는 것은 몇개에 불과하다.
예를 들어, 선형대수 라이브러리는 파이썬 인터프리터에서 직접 호출이 되었는지 혹은 좀더 큰 프로그램의 컴포넌트로 사용되는지 확인할 길이 없다.
후자의 경우 선형대수 라이브러리가 호출하는 프로그램이 명령-라인 인터페이스에서인지 아니면 GUI 인터페이스에서 실행되는지도 알수가 없다.
그래서, 라이브러리는 오류를 그 자체로 다루거나 보고하지 말아야 한다. 왜냐하면 예외사항을 처리할 올바른 방법이 무엇인지 알지 못하기 때문이다. 대신에 예외를 [발생(raise)](../../gloss.html#raise)시키고, 호출하는 쪽에서 어떻게 처리하는 것이 가장 좋은 것인지 해결하게 한다.

마지막으로, 만약 할 수만 있다면, 예외사항을 발생시킨다. 사실 이렇게 *해야 한다*. 왜냐하면, 파이썬에서 무언가 잘못되었을 때 신호를 보내는 표준 방법이기 때문이다. 예를 들어, 다음에 그리드를 읽어 들이고, 일관성을 점검하는 함수가 있다.

~~~
def read_grid(grid_file):
    data = read_raw_data(grid_file)
    if not grid_consistent(data):
        raise Exception('Inconsistent grid: ' + grid_file)
    result = normalize_grid(data)
    return result
~~~
{:class="in"}

`raise` 문은 의미있는 오류 메시지로 새로운 예외를 생성한다.
`read_grid` 자체는 `try`/`except` 블록을 포함하고 있지 않기 때문에,
예외사항은 항상 발생하고 함수 밖에서 `read_grid`을 호출자에 의해서 예외를 잡아서 처리해야 한다.
만약 할수 만 있다면, 새로운 형식의 예외를 정의할 수 있다. 그리고 그렇게 해야 한다. 그래서 코드의 오류가 다른 사람 코드의 오류와 구별될 수 있다. 하지만, 이렇게 하는 것은 클래스와 객체와 관련되는데 이번 학습 범위 밖이다.
