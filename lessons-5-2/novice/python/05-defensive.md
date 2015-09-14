---
layout: lesson
root: ../..
---

## 방어적 프로그래밍 (Defensive Programming)


앞선 학습에서 프로그래밍의 기본적인 도구를 소개했다. 변수와 리스트, 파일 입력출력(I/O), 루프, 조건문, 그리고 함수.
아직 수행하지 *않은* 것은 프로그램이 정답을 얻었는지를 어떻게 보여주고 프로그램을 변경하고 수정하면서 *여전히* 정답을 얻고있는지를 보여주는 것이다.

이를 달성하기 위해서, 다음이 필요하다.

*   자신의 연산을 확인하는 프로그램을 작성한다.
*   널리 사용되는 함수에 대한 테스트를 작성하고 수행한다.
*   "정답"이라는 것이 실제로 무엇을 의미하는지 알고 있어야 한다.

좋은 소식은 이런 것들을 수행하는 것은 프로그래밍의 속도를 늦추지 않고 가속화한다. 
실제 목공에서 나무를 자르기 전에 주의깊게 측정해서 절약되는 시간이 측정하는데 걸리는 시간보다 훨씬 크다.


#### 목표

*   가정 설정문(assertion)이 무엇인지 설명한다.
*   프로그램의 상태를 올바르게 확인하는 가정 설정문(assertion)을 프로그램에 추가한다.
*   함수에 전제조건과 사후조건 가정 설정문(assertion)에 올바르게 추가한다.
*   테스트 주도 개발(test-driven development)가 무엇인지 설명하고, 새로운 함수를 생성할 때 사용한다.
*   왜 변수를 초기화하는데 임의의 상수보다 실제 데이터를 사용하는지 설명한다.
*   체계적으로 오류를 포함하는 코드를 디버그한다.

### 가정 설정문 (Assertions)


프로그램에서 정답을 얻는 첫번째 단계는 실수는 *일어난다고* 가정하고 이에 대비하여 방지하는 것이다.
이것을 [방어적 프로그래밍(defensive programming)](../../gloss.html#defensive-programming)이라고 부르고,
가장 일반적인 방식은 코드에 [가정 설정문(assertions)](../../gloss.html#assertion)을 추가해서 실행시에 점검한다.
가정 설정문은 단순하게 프로그램의 특정 지점에서 항상 참이어야 하는 문장이다.
파이썬이 가정 설정문을 만나게 될 때, 가정 설정문의 조건을 확인한다. 만약 참이면, 파이썬은 아무것도 하지 않는다.
하지만 거짓이면, 파이썬은 즉시 프로그램을 정지시키고 마련된 오류 메시지를 출력한다.
예를 들어, 루프가 양수가 아닌 값을 마주치자 마자 바로 이 코드 부분이 정지한다.


<pre class="in"><code>numbers = [1.5, 2.3, 0.7, -0.001, 4.4]
total = 0.0
for n in numbers:
    assert n &gt;= 0.0, &#39;Data should only contain positive values&#39;
    total += n
print &#39;total is:&#39;, total</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
&lt;ipython-input-19-33d87ea29ae4&gt; in &lt;module&gt;()
      2 total = 0.0
      3 for n in numbers:
----&gt; 4     assert n &gt;= 0.0, &#39;Data should only contain positive values&#39;
      5     total += n
      6 print &#39;total is:&#39;, total

AssertionError: Data should only contain positive values</code></pre></div>


파이어 폭스 웹브라우져 같은 프로그램은 가정 설정문(assertion)으로 가득차 있다.
코드의 10-20%는 다른 80-90%의 코드가 올바르게 동작하는지 확인하기 위해서 존재한다.
대체로 가정 설정문은 다음 3개 범주안에 들어간다.

-   올바르게 동작하기 위해서 함수의 시작점에서 참이여 되는 것은 [사전 조건(precondition)](../../gloss.html#precondition)이다. 
-   함수가 끝날 때 참을 보증하는 것이 [사후 조건(postcondition)](../../gloss.html#postcondition)이다.
-   부분 코드 내부 특정한 지점에서 항상 참이어야 하는 것이 [불변식(invariant)](../../gloss.html#invariant)이다.

예를 들어, 4개의 좌표 `(x0, y0, x1, y1)`로 구성된 튜플을 사용하여 직사각형을 표현한다고 가정하자.
연산을 수행하기 위해서, 정사각형을 정규화해서 원점과 가장 긴 축을 따라 1.0 단위를 가진다.
함수가 정규화를 하지만 입력값이 올바른 형식인지 결과가 의미가 있는지 점검한다.


<pre class="in"><code>def normalize_rectangle(rect):
    &#39;&#39;&#39;Normalizes a rectangle so that it is at the origin and 1.0 units long on its longest axis.&#39;&#39;&#39;
    assert len(rect) == 4, &#39;Rectangles must contain 4 coordinates&#39;
    x0, y0, x1, y1 = rect
    assert x0 &lt; x1, &#39;Invalid X coordinates&#39;
    assert y0 &lt; y1, &#39;Invalid Y coordinates&#39;
    
    dx = x1 - x0
    dy = y1 - y0
    if dx &gt; dy:
        scaled = float(dx) / dy
        upper_x, upper_y = 1.0, scaled
    else:
        scaled = float(dx) / dy
        upper_x, upper_y = scaled, 1.0

    assert 0 &lt; upper_x &lt;= 1.0, &#39;Calculated upper X coordinate invalid&#39;
    assert 0 &lt; upper_y &lt;= 1.0, &#39;Calculated upper Y coordinate invalid&#39;

    return (0, 0, upper_x, upper_y)</code></pre>


주석을 제외한 2, 4, 5번 행의 사전 조건은 잘못된 입력을 잡아낸다.


<pre class="in"><code>print normalize_rectangle( (0.0, 1.0, 2.0) ) # missing the fourth coordinate</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
&lt;ipython-input-21-3a97b1dcab70&gt; in &lt;module&gt;()
----&gt; 1 print normalize_rectangle( (0.0, 1.0, 2.0) ) # missing the fourth coordinate

&lt;ipython-input-20-408dc39f3915&gt; in normalize_rectangle(rect)
      1 def normalize_rectangle(rect):
      2     &#39;&#39;&#39;Normalizes a rectangle so that it is at the origin and 1.0 units long on its longest axis.&#39;&#39;&#39;
----&gt; 3     assert len(rect) == 4, &#39;Rectangles must contain 4 coordinates&#39;
      4     x0, y0, x1, y1 = rect
      5     assert x0 &lt; x1, &#39;Invalid X coordinates&#39;

AssertionError: Rectangles must contain 4 coordinates</code></pre></div>


<pre class="in"><code>print normalize_rectangle( (4.0, 2.0, 1.0, 5.0) ) # X axis inverted</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
&lt;ipython-input-22-f05ae7878a45&gt; in &lt;module&gt;()
----&gt; 1 print normalize_rectangle( (4.0, 2.0, 1.0, 5.0) ) # X axis inverted

&lt;ipython-input-20-408dc39f3915&gt; in normalize_rectangle(rect)
      3     assert len(rect) == 4, &#39;Rectangles must contain 4 coordinates&#39;
      4     x0, y0, x1, y1 = rect
----&gt; 5     assert x0 &lt; x1, &#39;Invalid X coordinates&#39;
      6     assert y0 &lt; y1, &#39;Invalid Y coordinates&#39;
      7 

AssertionError: Invalid X coordinates</code></pre></div>


사후 조건은 계산 결과가 올바르지 않을 때 신호를 줌으로써 버그를 잡도록 도와준다.
예를 들어, 너비보다 더 큰 직사각형을 정규화한다면 모든 것이 OK 처럼 보인다.


<pre class="in"><code>print normalize_rectangle( (0.0, 0.0, 1.0, 5.0) )</code></pre>

<div class="out"><pre class='out'><code>(0, 0, 0.2, 1.0)
</code></pre></div>


하지만, 높이보다 더 넓은 정사각형을 정규화한다면 사전 선언문이 자동으로 실행된다.


<pre class="in"><code>print normalize_rectangle( (0.0, 0.0, 5.0, 1.0) )</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
&lt;ipython-input-24-5f0ef7954aeb&gt; in &lt;module&gt;()
----&gt; 1 print normalize_rectangle( (0.0, 0.0, 5.0, 1.0) )

&lt;ipython-input-20-408dc39f3915&gt; in normalize_rectangle(rect)
     16 
     17     assert 0 &lt; upper_x &lt;= 1.0, &#39;Calculated upper X coordinate invalid&#39;
---&gt; 18     assert 0 &lt; upper_y &lt;= 1.0, &#39;Calculated upper Y coordinate invalid&#39;
     19 
     20     return (0, 0, upper_x, upper_y)

AssertionError: Calculated upper Y coordinate invalid</code></pre></div>


작성한 함수를 다시 읽게되면, 10번째 행이 `dx`가 `dy`로 나누어지기 보다 `dy`가 `dx`로 나누어져야 한다.
(Ctrl+M 그리고 L을 타이핑해서 행번호를 화면에 출력할 수 있다.)
만약 함수의 끝에 가정 설정문을 생략한다면, 유효한 답변으로 올바른 모양을 가진 무언가를 생성하고 반환해야할 것이지만 하지는 그렇게 하지는 않는다.
버그를 탐지하고 디버깅하는 것은 거의 항상 가정 설정문을 작성하는 것보다 장기적으로 더 많은 시간이 걸린다.

하지만 가정 설정문이 오류를 잡아내는 것만 하는 것은 아니고 사람들로 하여금 프로그램을 이해하는데 도움도 준다. 
각각의 가정 설정문은 프로그램을 읽는 사람에게 코드가 동작하는 것과 프로그램을 이해하는 것이 매칭되는지 확인할 수 있는 기회의 장을 제공하기도 한다.

대부분의 좋은 프로그래머는 코드에 가정 설정문을 추가할 때 두가지 규칙을 따른다. 
하나는 "[미리 실패하고, 자주 실패하라(fail early, fail often)](../../rules.html#fail-early-fail-often)"는 것이다.
오류가 발생하는 시간과 장소와 인지하는 시점과 거리가 크면 클수록, 오류를 디버그하기가 더욱 어렵다. 
그래서 좋은 코드는 가능한 이른 시점에 오류와 실수를 잡아낸다.

두번째 규칙은 "[버그를 가정 설정문과 테스트로 변환하라(turn bugs into assertions or tests)](../../rules.html#turn-bugs-into-assertions-or-tests)"는 것이다. 만약 코드 일부분에 실수를 하게된다면, 근처에서 다른 실수를 하거나 다음번에 코드를 변경할 때 동일한 혹은 관련된 실수를 저지를 가능성이 높다.
여러분이 [회귀(regressed)](../../gloss.html#regression)가 되지 않도록 (즉, 이전 문제를 다시 발생하지 않도록) 가정 설정문을 작성하는 것은 장기적으로 엄청난 시간을 절약할 수 있고 미래의 자신을 포함하여 까다로운 코드를 읽는 사람에게 경고를 주는데도 도움이 된다.


#### 도전 과제

1. 리스트 숫자의 평균을 계산하는 함수 `average`를 작성한다고 가정하자.
   사전 조건과 사후 조건으로 함수 `average`에 대해 무엇을 작성할까요?
   여러분이 작성한 것과 주위 동료의 것과 비교하세요. 
   여러분의 테스트를 통과했으나 동료의 테스트는 통과하지 못한 혹은 반대 경우의 함수를 생각할 수 있나요?

2. 코드에 사전 설정문이 확인하는 것이 무엇인지 일상적인 말로 설명하세요. 그리고, 각각에 대해서 사전 설정문이 실패하게 되는 입력값의 예를 주세요.
 
  ~~~python
  def running(values):
      assert len(values) > 0
      result = [values[0]]
      for v in values[1:]:
          assert result[-1] >= 0
          result.append(result[-1] + v)
          assert result[-1] >= result[0]
      return result
  ~~~

### 테스트 주도 개발 (Test-Driven Development)


가정 설정문은 프로그램의 특정한 지점에서 무엇인가 참인지 확인하는데 도움이 된다.
다음 단계는 코드 일부분의 전반적인 동작을 확인하는 것이다. 즉, 특정한 입력값이 주어졌을 때,
올바른 출력값을 만들어 내는지 확인한다.
예를 들어, 두개 혹은 그 이상의 시계열이 중첩되는지 발견할 필요가 있다고 가정하자.
각 시계열의 범위는 숫자 짝으로 표현되고 시작과 끝을 표현하는 시간 간격이 있다.
출력값은 모든 시간을 포함하는 가장 큰 범위다.


<img src="img/python-overlapping-ranges.svg" alt="Overlapping Ranges" />


대부분의 초보 프로그래머는 상기 문제를 다음과 같이 푼다.

1. `range_overlap` 함수를 작성한다.
2. 두개 혹은 3개의 입력값에 대해서 함수를 인터랙티브하게 호출한다.
3. 만약 함수가 잘못된 대답을 준다면, 함수의 잘못된 것을 고치고 다시 테스트를 시행한다.

명확하게 이 방식은 동작하지만 더 좋은 방식이 있다. (수천명의 과학자가 지금 이와 같이 작업을 하고 있다.)

1. 각 테스트에 대해서 짧은 함수를 작성한다.
2. 상기 테스트를 통한 `range_overlap` 함수를 작성한다.
3. 만약 함수 `range_overlap`가 잘못된 대답을 준다면, 함수의 잘못된 것을 고치고 다시 테스트를 시행한다.

함수를 작성하기 *전에* 테스트를 작성하는 것을 [테스트 주도 개발(test-driven development)](../../gloss.html#test-driven-development) (TDD)라고 한다.
TDD 지지하시는 분들은 이 방식이 더 빠르게 더 좋은 코드를 만들어낸다고 믿고 있다. 왜냐 하면,

1. 만약 테스트 대상 코드를 작성한 후에 테스트를 작성하게 된다면, 확증 편향(confirmation bias)에 빠지기 쉽다. 
   즉, 무의식적으로 오류를 발견하기봐 작성한 코드가 옳다는 것을 증명하기 위한 테스트를 작성한다.
2. 테스트를 작성하는 것은 프로그래머가 함수가 실질적으로 무엇을 수행해야 하는지에 대해 파악하는데 도움을 준다.

`range_overlap`에 대한 3개의 테스트 함수가 있다.


<pre class="in"><code>assert range_overlap([ (0.0, 1.0) ]) == (0.0, 1.0)
assert range_overlap([ (2.0, 3.0), (2.0, 4.0) ]) == (2.0, 3.0)
assert range_overlap([ (0.0, 1.0), (0.0, 2.0), (-1.0, 1.0) ]) == (0.0, 1.0)</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
&lt;ipython-input-25-d8be150fbef6&gt; in &lt;module&gt;()
      1 assert range_overlap([ (0.0, 1.0) ]) == (0.0, 1.0)
----&gt; 2 assert range_overlap([ (2.0, 3.0), (2.0, 4.0) ]) == (2.0, 3.0)
      3 assert range_overlap([ (0.0, 1.0), (0.0, 2.0), (-1.0, 1.0) ]) == (0.0, 1.0)

AssertionError: </code></pre></div>


오류는 사실 안심을 준다. 아직 `range_overlap`을 작성하지는 않아서 만약 테스트가 통과된다면, 
누군가 함수를 작성했고, 우연히 여러분이 함수를 사용한다는 표시다.

테스트를 작성하는 것에 대한 보너스로 암묵적으로 입력과 출력이 무엇인지 정의한다는 것이다.
쌍으로 구성된 여러 리스트를 입력받아 하나의 리스트로 출력하는 것이다.

하지만 중요한 것이 빠졌다. 범위가 전혀 중첩되지 않는 경우에 대해서 어떠한 테스트도 준비하지 않았다.

~~~
assert range_overlap([ (0.0, 1.0), (5.0, 6.0) ]) == ???
~~~

상기 경우에 `range_overlap`은 무엇을 해야할까? 오류 메시지 실패로 종료, 
중첩되지 않는다는 신호로 `(0.0, 0.0)` 같은 값을 출력, 혹은 다른 어떤 것을 수행.
함수를 실제로 구현할 때 여러 경우의 수 중에서 하나를 작성한다.
이슈가 있다는 것을 알아차리기 이전에 감정적으로 무언가 작성하는데 투자를 일으키기 *전에*
먼저 테스트를 작성하는 것은 무엇이 가장 최선이지 파악하는데 도움을 준다.

다음 사례의 경우는 어떻게 처리할까?

~~~
assert range_overlap([ (0.0, 1.0), (1.0, 2.0) ]) == ???
~~~

끝점을 맞대고 있는 두 부분은 중첩된 것인가 아닌가? 수학자는 대체로 "예 맞습니다"라고 하지만,
공학자는 대체로 "아닙니다"라고 말한다. 
최선의 답은 "프로그램의 나머지 부분에서 무엇이든지 가장 유용한 것이 될 것이다".
하지만, 다시 한번 `range_overlap`의 실제 구현은 *무언가* 수행하는 것이고, 
구현이 무엇이든지 관계없이 전혀 중첩되는 것이 없을 때 수행되는 것과 일관성이 있어야 한다.

시계열 차트에서 X축으로 함수는 반환하는 범위를 사용하려고 계획하고 있기 때문에, 다음과 같이 결정한다.

1. 모든 중첩은 0 이 아닌 너비를 가져야 한다.
2. 중첩되는 것이 없을 때, 특수값 `None` 을 반환한다.

`None`은 파이썬에 내장되어져 있고, "여기에 아무것도 없어요(nothing here)"를 의미한다. 
다른 언어는 종종 상응하는 값으로 `null` 혹은 `nil` 이라고 한다.
상기 결정 사항을 가지고, 마지막 두 테스트 작성을 마칠 수 있다.


<pre class="in"><code>assert range_overlap([ (0.0, 1.0), (5.0, 6.0) ]) == None
assert range_overlap([ (0.0, 1.0), (1.0, 2.0) ]) == None</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
&lt;ipython-input-26-d877ef460ba2&gt; in &lt;module&gt;()
----&gt; 1 assert range_overlap([ (0.0, 1.0), (5.0, 6.0) ]) == None
      2 assert range_overlap([ (0.0, 1.0), (1.0, 2.0) ]) == None

AssertionError: </code></pre></div>


다시, 함수를 작성하지 않아서 오류가 생겼다. 하지만 이제 함수를 작성할 준비가 되었다.


<pre class="in"><code>def range_overlap(ranges):
    &#39;&#39;&#39;Return common overlap among a set of [low, high] ranges.&#39;&#39;&#39;
    lowest = 0.0
    highest = 1.0
    for (low, high) in ranges:
        lowest = max(lowest, low)
        highest = min(highest, high)
    return (lowest, highest)</code></pre>


(잠시 시간을 가지고 왜  `lowest`에 `max`를 사용하고,  `highest`에 `min`을 사용하는지 생각해보자.
테스트를 다시 실행하고자 하지만, 3개의 다른 셀에 여기저기 분산되어 있다 테스트를 좀더 쉽게 실행하도록 함수에 테스트 케이스를 모두 모아 놓자.


<pre class="in"><code>def test_range_overlap():
    assert range_overlap([ (0.0, 1.0), (5.0, 6.0) ]) == None
    assert range_overlap([ (0.0, 1.0), (1.0, 2.0) ]) == None
    assert range_overlap([ (0.0, 1.0) ]) == (0.0, 1.0)
    assert range_overlap([ (2.0, 3.0), (2.0, 4.0) ]) == (2.0, 3.0)
    assert range_overlap([ (0.0, 1.0), (0.0, 2.0), (-1.0, 1.0) ]) == (0.0, 1.0)</code></pre>


하나의 함수 호출로 `range_overlap`을 이제는 테스트할 수 있다.


<pre class="in"><code>test_range_overlap()</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
&lt;ipython-input-29-cf9215c96457&gt; in &lt;module&gt;()
----&gt; 1 test_range_overlap()

&lt;ipython-input-28-5d4cd6fd41d9&gt; in test_range_overlap()
      1 def test_range_overlap():
----&gt; 2     assert range_overlap([ (0.0, 1.0), (5.0, 6.0) ]) == None
      3     assert range_overlap([ (0.0, 1.0), (1.0, 2.0) ]) == None
      4     assert range_overlap([ (0.0, 1.0) ]) == (0.0, 1.0)
      5     assert range_overlap([ (2.0, 3.0), (2.0, 4.0) ]) == (2.0, 3.0)

AssertionError: </code></pre></div>


`None`을 만들어내야 하는 첫번째 테스트는 실패해서, 작성한 함수에 뭔가 잘못된 것을 알게된다.
알지 *못하는* 것은 다른 4개의 테스트가 통과되었는지 실패했는지다. 왜냐하면 파이썬은 첫번째 오류를 탐지하지마자 프로그램을 정지한다.
여전히 약간의 정보가 없는 것보다는 낫다. 
만약 그 입력값으로 함수의 동작을 추적하면 초기값을 입력값에 관계없이 `lowest`를 0.0 `highest`를 1.0 으로 각각 초기 설정함을 알게된다. 
이것은 또 다른 중요한 프로그래밍 규칙("[항상 데이터로 초기화하라(always initialize from data)](../../rules.html#always-initialize-from-data)")을 위반하게 된다. 함수 `range_overlap` 을 고치는 것은 연습으로 남겨둔다.


<div class="challenges" markdown="1">
#### 도전 과제

1. `range_overlap`을 고치시오. 변경을 한 후에 `test_range_overlap`을 다시 실행하세요.
</div>

### 디버깅 (Debugging)


테스팅을 통해서 문제를 발견하게 되면, 다음 단계는 문제를 고치는 것이다. 
많은 초보자는 올바른 답을 만드는 것처럼 보일 때까지 대체로 랜덤(random)하게 변경을 해서 문제를 해결한다.
하지만 이러한 접근법은 매우 비효율적이고 결과는 대체로 테스팅하고 있는 경우에 대해서만 적합하다.
프로그래머가 좀더 경험이 많을수록, 좀더 체계적으로 디버그한다. 대부분의 경험있는 프로그래머는 다음에 설명된 규칙과 변형을 따른다.


#### 프로그램이 수행하게 되어 있는 것을 이해한다.

무언가를 디버깅하는 첫번째 단계는 [프로그램이 수행하게 되어 있는 것을 이해(know what it's supposed to do)](../../rules.html#know-what-its-supposed-to-do)하는 것이다.
"작성한 프로그램이 동작을 하지 않는다"는 충분하지 않다. 문제를 진단하고 고치기 위해서는 올바른 결과와 잘못된 결과를 구별할 필요가 있다.
만약 실패한 케이스에 대해서 테스트 케이스를 작성한다면 - 즉 *이것을* 입력으로 가정 설정한다면, 함수는 *저것을* 결과로 산출한다 - 
그러면 디버깅을 시작할 준비가 되었다. 만약 이렇게 할수 없다면, 무엇인가 고칠 때 어떻게 고칠지에 대해서 파악할 필요가 있다.

하지만, 과학적 소프트웨어에 대해서 테스트 케이스를 작성하는 것은 상용 응용프로그램에 대한 테스트 케이스를 작성하는 것보다 종종 더 힘들다.
왜냐하면, 만약 과학적 소프트웨어 코드의 결과가 무엇이 되어야 하는지 알고 있다면, 소프트웨어를 실행하지 않아도 된다. 
그래서 결과를 작성하고 다음 프로그램으로 옮겨간다. 실무에서 과학자들은 다음을 수행하는 경향이 있다.


1. *단순화된 데이터로 테스트한다.*
 실제 데이터섹에 통계분석을 수행하기 전에, 하나의 레코드에 대해서, 두개의 동일한 레코드에 대해서, 두개의 레코드인데 한 단계가 차이가 나는, 혹은 
 수작업으로 정답을 계산할 수 있는 레코드에 한해서 통계를 계산한다.

2. *단순화된 케이스를 테스트한다.*
 만약 프로그램이 매우 빨리 회전하는 작은 방울의 과냉각된 헬륨에 자기장 소용돌이를 모의실험하려고 한다면, 첫번째 테스트는 회전하지 않는 
 작은 방울 헬륨이여야 하고, 어떤 외부 전자기장에 영향을 받지 말아야 한다. 마찬가지로, 만약 어떤 종에 대한 기후변화의 효과를 살펴보려고 한다면,
 첫번째 테스트는 온도, 습도, 그리고 다른 요소를 상수로 고정하여야 한다.

3. *절대적인(oracle) 것과 비교한다.*
 [테스트 오라클(test oracle)](../../gloss.html#test-oracle)은 새로운 프로그램의 결과와 비교할 수 있는 무엇이다. 
 즉, 실험 데이터, 결과를 신뢰할 수 있는 이전 프로그램, 혹은 심지어 전문가도 될 수 있다.
 만약 테스트 오라클이 있다면, 특별한 케이스에 대해서 출력 결과를 저장해서 프로그램을 다시 실행하지 않고 원하는만큼 자주 새로운 결과값과 비교한다.

4. *보전 법칙을 확인하라.*
 질량, 에너지, 그리고 기타 양적 정보는 물리 시스템에서 보존된다. 프로그램에서도 또한 보존되어야 한다.
 마찬가지로, 만약 환자 데이터를 분석한다면, 레코드 숫자는 같은 수가 유지되거나 다음 분석으로 옮겨가게 되면 줄어든다.
 (왜냐하면 결측값을 가진 레코드나 아웃라이어를 버려버리기 때문이다.) 만약, 파이프라인을 따라서 옮겨가다가 갑자기 "새로운" 환자가 값자기 나타난다면, 
 아마도 무언가 잘못되고 있다는 신호다.

5. *시각화하라.*
 데이터 분석자는 종종 간단한 시각화를 사용하여 수행하고 있는 과학과 코드의 정합성에 대해서 점검한다. 
 (파이썬 학습의 [도입 학습](01-numpy.html)과 마찬가지로). 하지만, 이 방법은 디버깅에 대해서 최후의 수단이 되어야 한다. 
 왜냐하면, 자동적으로 두개의 시각화 결과를 비교하는 것은 매우 어렵다.

#### 매번 실패하게 만들기 (Make It Fail Every Time)

실패할 때만 무언가 디버그할 수 있다. 그래서 두번째 단계는 항상 매번 실패하게 만드는 테스트 케이스를 찾는 것이다.
"매번(every time)"이 중요한데, 이유는 간헐적인 문제를 디버깅하는 것보다 더 좌절을 주는 것이 없기 때문이다.
만약 한번의 실패를 만들기 위해서 12번 함수를 호출해야 한다면, 실패가 실제로 일어났을 때로 스크롤하여 실패를 찾는 것이 확률적으로 높다.

이것과 관련해서, 코드가 "연결되어 있는지(plugged-in)" 확인하는 것이 중요하다. 즉, 실제로 우리가 생각하기에 문제인 것을 다룬다.
모든 프로그래머는 버그를 쫓아서 몇시간을 보내는데 단지 잘못된 데이터나 잘못된 환경설정 매개변수에 코드를 호출하거나 완전히 잘못된 소프트웨어 버젼을 
사용한 것을 깨단기 위해서다. 이와 같은 실수는 특히 피곤할 때, 좌절했을 때, 마감시한에 임박했들 때 발생할 듯 하다. 이런 이유로 밤 늦게 혹은 밤새도록 
코딩을 하는 것은 거의 가치없어서 지양해야된다.

#### 빨리 실패하게 만들기 (Make It Fail Fast)

만약 버그가 표면에 나오는데 20분 걸린다면, 한시간에 3회 실험을 할 수 있다. 이것이 더 많은 시간에 더 적은 데이터를 갖는다는 것을 의미하지 말아야 한다.
프로그램이 실패하기를 기다리면서 다른 것에 의해서 산만하게 더 될 듯하다. 이것은 프로그래머가 문제에 사용하는 시간의 집중도가 떨어진다는 것을 의미한다.
그러므로 [빨리 실패하게 만드는 것(Make It Fail Fast)](../../rules.html#make-it-fail-fast)이 매우 중요하다.

프로그램을 시간내에 빨리 실패하게 만드는 것 뿐만 아니라 공간적인 측면에서 프로그램을 빨리 실패하게 만들고 싶다. 즉, 가능하면 적은 코드 지역에 실패를 국지화하고자 한다.

1.  원인과 결과 사이의 간격이 작으면 작을수록, 연결점을 발견하기는 더욱 쉽다. 그래서 많은 프로그래머는 버그를 찾기 위해서 
    분리 정복 전략(divide and conquer strategy)를 사용한다. 만약 함수의 출력이 잘못된다면, 중간에 있는 것이 OK 인지 점검하고 나서 
    앞쪽 혹은 뒤쪽에 점검하고 이를 반복한다.

2.  N개는 N<sup>2/2</sup> 다른 방식으로 상호작용한다.
    그래서 테스트 부분으로 실행되지 *않는* 모든 코드 라인은 특별히 걱정할 필요가 없는 것 이상을 의미한다.

#### 이유를 가지고 한번에 하나씩 변경하라.

임의의 코드 덩어리를 교체하는 것은 좋은 일을 하지 못할 것 같다.
(결국, 처음에 잘못되면, 아마도 두번째 세번째도 잘못될 것이다.)
그래서 좋은 프로그래머는 [이유를 가지고 한번에 하나씩 변경(change one thing at a time, for a reason)](../../rules.html#change-one-thing-at-a-time)한다.
좋은 프로그래머는 좀더 정보를 수집(루프의 순서를 변경한다면 버그가 여전히 남아있을까?)하거나 고친 부분을 테스트(처리하기 전에 데이터를 정렬함으로써 버그를 없앨 수 있을까?)한다.

아무리 작을더라도 매번 변경을 할때마다, 즉시 테스트를 다시 돌려야한다. 왜냐하면 한번에
변경한 것이 더 많으면 많을수록, 무엇이 무엇에 (N<sup>2</sup> 상호작용) 대해서 책임이 있는지 알아내기가 더더욱 힘들다.

그리고 *모든* 테스트를 다시 실행하게 된다면, 코드를 수정한 절반이상은 버그를 생성 혹은 재생성하게된다.
그래서 모든 테스트를 다시 실행을 통해서
[회귀(regressed)](../../gloss.html#regression)했는지 즉, 이전 문제를 다시 발생하게 했는지도 알 수 있다.
 
#### 작업한 것을 기록하라.

훌륭한 과학자는 작업한 것을 기록한다. 그래서 작업한 것을 다시 재생성할 수 있고 
동일한 실험을 반복하거나, 결과가 신통지 못한 것을 다시 실행하는데 시간을 낭비하지 않는다.
마찬가지로, 디버깅도 [작업한 것을 기록(keep track of what we've done)](../../rules.html#keep-track-of-what-youve-done)하고 어떻게 잘 동작했는지도
기록할 때 가장 잘 된다. 만약 여러분이 다음과 같은 질문을 한다면,
왼쪽에서 오른쪽으로 코드 라인 홀수가 시스템 충돌을 일으켰는지? 
오른쪽에서 왼쪽이 충돌을 일으켰는지? 
코드라인 짝수를 사용하다가 발생했는지?,  
컴퓨터에서 잠시 떨어져서, 숨을 깊이 들이 마시고, 좀더 체계적으로 일을 시작해야하는 시간이다.

시간이 흘러 도움을 요청할 때 기록은 특히 유용하다. 명확하게 했던 것을 설명할 때 사람들은 좀더 귀를 기울여 듣는다. 그리고 사람들이 필요로 하는 유용한 정보를 좀더 잘 전달할 수 있다.

> #### 버젼 제어 (version control) 재방문
>
> 버젼 제어 (version control)는 종종 디버깅 동안 이전 특정 상태로 소프트웨어를 다시 
원복하는데 사용된다. 그리고 버그와 연관된 코드 최근 변경을 사항을 탐색하는데도 사용된다.
특히, 대부분의 버젼 제어 시스템은 `blame` 명령어가 있어서 특정한 코드 라인에 누가 마지막에
변경을 했는지도 확인할 수 있다.

#### 겸손하라 (Be Humble)

그리고, 도움을 말하라. 만약 10분내로 버그를 발견할 수 없다면, [겸손(be humble)](../../rules.html#be-humble)하게 도움을 요청하라. 
문제를 크게 설명하는 것만으로도 종종 도움이 된다.
왜냐하면, 생각하는 것을 듣는 것만으로도 일관되지 못한 것과 숨겨진 가정을 발견하는데 도움이 된다.

도움을 요청하는 것은 또한 확증 편향(confirmation bias)을 줄여준다.
만약 복잡한 프로그램을 작성하는데 한시간을 썼다면, 잘 동작하길 원해서
동작하지 않는 이유를 찾기보다는 왜 동작해야 하는지에 대해서 계속 본인 스스로에게 최면을 건다.
코드에 감정적으로 투자를 하지 않은 사람은 좀더 객관적일 수 있다. 이것이 왜
외부 사람이 간과한 간단한 실수를 종종 탐지하는 이유다.

겸손의 일부는 실수로부터 배우는 것이다. 프로그래머는 동일한 것을 반복해서 잘못하는 경향이 있다.
작업하는 언어와 라이브러리를 이해하지 못하거나, 프로그램이 어떻게 동작하는지에 대한 모델이 잘못된 것도 이유다.
어느 경우에나 왜 오류가 발생했는지 메모를 해두어 다음번에 점검하는 것이 실수를 다시 하지 않게 돌리는 빠른 방법이다.

그리고 이러한 방법이 장기적으로 좀더 생산적으로 여러분을 만든다.
외국 속담에 "[한주의 노력은 한 시간의 생각을 절약해준다(A week of hard work can sometimes save you an hour of thought(](../../rules.html#week-hard-work-hour-thought)는 말이 있다.
어떤 유형의 실수를 피하도록, 코드를 모듈화하고, 테스트할 수 있는 덩어리로 만들고, 
모든 가정(혹은 실수)을  가정 설정문(assertion)으로 만든다면, 더 많이 만들지는 못하지만 
동작하는 프로그램을 만드는데 *더 적은* 시간이 걸릴 것이다.


<div class="keypoints" markdown="1">
#### 주요점

*   방어적으로 프로그램하라. 즉, 오류가 발생한다고 가정하고 오류가 발생할 때 오류를 탐지하도록 코드를 작성하다.
*   프로그램에 가정 설정문을 넣어서 프로그램이 실행될 때 상태를 점검하게 하라.
    그리고 프로그램을 읽는 사람이 작성한 프로그램이 어떻게 동작을 하는 것인지 이해할 수 있도록 도움을 줘라.
*   사전 조건을 사용해서 함수 입력값을 사용해도 안전한지 점검하라.
*   사후 조건을 사용해서 함수 출력값을 사용해도 안전한지 점검하라.
*   코드를 작성하기 전에 테스트를 작성해서 정확하게 코드가 무엇을 수행해야되는지 결정하도록 하라.
*   코드를 디버그하기 *전에* 코드가 무엇을 수행해야하는지 파악하라.
*   매번 실패하게 만들어라.
*   빨리 실패하게 만들어라.
*   이유를 가지고 한번에 하나씩 변경하라.
*   작업한 것을 기록하라.
*   겸손하라.
</div>


#### 다음 단계

IPython Notebook으로 파이썬 코드를 작성하고 테스트하는 기초를 학습했다.
학습할 필요가 있는 마지막 것은 파이프라인과 쉘 스크립트에서 사용할 수 있는 
명령-라인 프로그램을 어떻게 작성하는 것이다.
그렇게 함으로써 다른 사람이 작업한 것을 여러분의 도구와 통합할 수 있다.
이것이 다음 학습의 주제이며 마지막 학습이다.


<pre class="in"><code></code></pre>
