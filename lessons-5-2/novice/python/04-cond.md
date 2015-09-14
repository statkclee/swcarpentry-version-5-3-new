---
layout: page
root: ../..
---

## 조건 선택


앞선 학습에서 어떻게 데이터를 다루고, 함수를 정의하고, 반복하는지를 배웠다. 하지만, 지금까지 작성한 프로그램은 무슨 데이터가 주어지든지 관계없이 항상 동일한 것을 수행한다. 프로그램이 다루는 값에 기반하여 선택하도록 만들고 싶다.
프로그램이 무슨 결정을 하는지 이해를 돕기 위해서, 어떻게 컴퓨터가 이미지(image)를 다루는지 살펴보면서 시작하자.


<div class="objectives" markdown="1">
#### 목표

*   색상 블록에서 간단한 "이미지(image)"를 생성한다.
*   RGB 모델이 어떻게 색상을 표현하는지 설명한다.
*   튜플(tuple)과 리스트(list)의 차이점과 유사점을 설명한다.
*   `if`, `elif`, `else` 분기를 포함하는 조건문을 작성한다.
*   `and`와 `or`를 포함하는 표현식을 올바르게 평가한다.
*   중첩 루프와 조건문을 포함하는 코드를 올바르게 작성하고 해석한다.
*   자주 변경되는 코드를 함수에 넣어 작성하는 장점을 설명한다.
</div>

### 이미지 그리드 (Image Grids)


`ipythonblocks`으로 불리는 라이브러리를 사용하여 간단한 히트 맵(heat map)을 생성해보자.
첫번째 단계는 자신만의 "이미지(image)"를 직접 생성하는 것이다.


<pre class="in"><code>from ipythonblocks import ImageGrid</code></pre>


앞에서 살펴본 `import` 문장과는 다르게, 전체 `ipythonblocks` 라이브러리를 로드하지 않는다.
대신에, 라이브러리에서 `ImageGrid`만 로드한다. 왜냐하면 지금 당장 필요한 유일한 것이기 때문디ㅏ.

`ImageGrid`가 로드되면, 매우 단순한 색깔 격자(grid)를 생성하는데 사용할 수 있다.


<pre class="in"><code>grid = ImageGrid(5, 3)
grid.show()</code></pre>




NumPy 배열처럼, `ImageGrid`는 몇몇 속성(property)이 있어서 관련 정보를 저장할 수 있다.


<pre class="in"><code>print &#39;grid width:&#39;, grid.width
print &#39;grid height:&#39;, grid.height
print &#39;grid lines on:&#39;, grid.lines_on</code></pre>

<div class="out"><pre class='out'><code>grid width: 5
grid height: 3
grid lines on: True
</code></pre></div>


격자를 가지고 할 수 있는 명백한 것은 셀안에 색깔이지만, 이를 위해서 먼저 어떻게 컴퓨터가 색깔을 표현하는지 알 필요가 있다.
가장 일반적인 방식은 [RGB](../../gloss.html#rgb) 방식이고, RGB는 Red, Green, Blue를 나타낸다. 
RGB는 [가색 모델(additive color model)](../../gloss.html#additive-color-model)이다. 
모든 색은 적색, 녹색, 청색의 세기를 일부 조합한 것이다. 이 세가지 색을 직육면체의 각 축으로 생각할 수 있다.

<img src="img/color-cube.png" alt="RGB Color Cube" />

RGB 색깔은 다중 값의 예이다. 직교 좌표계처럼 몇개의 부분으로 구성된다.
파이썬에서 다중 값을 표현하는 방식은 [튜플(tuple)](../../gloss.html#tuple)을 사용하는 것인데,
리스트에서 사용한 꺾쇠 괄호 대신에 괄호를 사용하여 표현한다.


<pre class="in"><code>position = (12.3, 45.6)
print &#39;position is:&#39;, position
color = (10, 20, 30)
print &#39;color is:&#39;, color</code></pre>

<div class="out"><pre class='out'><code>position is: (12.3, 45.6)
color is: (10, 20, 30)
</code></pre></div>


리스트와 배열에서와 동일한 방식으로 인덱스를 사용하여 튜플에서 요소를 선택한다.


<pre class="in"><code>print &#39;first element of color is:&#39;, color[0]</code></pre>

<div class="out"><pre class='out'><code>first element of color is: 10
</code></pre></div>


하지만, 리스트와 배열과 달리 튜플은 생성된 후에는 변경할 수 없다. 전문 용어로 튜플은 [변경불가능(immutable)](../../gloss.html#immutable)하다.


<pre class="in"><code>color[0] = 40
print &#39;first element of color after change:&#39;, color[0]</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
&lt;ipython-input-11-9c3dd30a4e52&gt; in &lt;module&gt;()
----&gt; 1 color[0] = 40
      2 print &#39;first element of color after change:&#39;, color[0]

TypeError: &#39;tuple&#39; object does not support item assignment</code></pre></div>


튜플로 RGB 색깔을 표현한다면, 적색, 녹색, 청색 구성요소는 0 ~ 255 사이의 값을 취한다.
상한이 약간 이상하게 보일지 모르지만, 8-비트 바이트 (즉, 2<sup>8</sup>-1)로 표현할 수 있는 가장 큰 숫자다.
이 방식은 거의 대부분의 사람의 눈을 속일만큼 충분한 색채의 농담을 주면서 컴퓨터로 색깔을 다루기 쉽게 한다.

실제로 몇몇 RGB 색깔이 어떻게 보이는지 살펴보자.


<pre class="in"><code>row = ImageGrid(8, 1)
row[0, 0] = (0, 0, 0)   # no color =&gt; black
row[1, 0] = (255, 255, 255) # all colors =&gt; white
row[2, 0] = (255, 0, 0) # all red
row[3, 0] = (0, 255, 0) # all green
row[4, 0] = (0, 0, 255) # all blue
row[5, 0] = (255, 255, 0) # red and green
row[6, 0] = (255, 0, 255) # red and blue
row[7, 0] = (0, 255, 255) # green and blue
row.show()</code></pre>



`(0,255,0)`같은 단순한 색깔은 약간의 실습으로 쉽게 해독할수 있지만, `(214,90,127)`은 무슨 색깔일까?
사용자를 돕기 위해서, `ipythonblocks` 라이브러리는 `show_color` 함수를 제공한다.


<pre class="in"><code>from ipythonblocks import show_color
show_color(214, 90, 127)</code></pre>

<div class="out"><div style="height: 60px; min-width: 200px; background-color: rgb(214, 90, 127)"></div></div>


또한 표준 색상표도 제공한다.


<pre class="in"><code>from ipythonblocks import colors
c = ImageGrid(3, 2)
c[0, 0] = colors[&#39;Fuchsia&#39;]
c[0, 1] = colors[&#39;Salmon&#39;]
c[1, 0] = colors[&#39;Orchid&#39;]
c[1, 1] = colors[&#39;Lavender&#39;]
c[2, 0] = colors[&#39;LimeGreen&#39;]
c[2, 1] = colors[&#39;HotPink&#39;]
c.show()</code></pre>



<div class="challenges" markdown="1">
#### 도전 과제

1.  아래 코드에서 `____` 을 채워 어두운 청색에서 검정색으로 색깔이 변하는 막대를 만드세요.

    ~~~python
    bar = ImageGrid(10, 1)
    for x in range(10):
        bar[x, 0] = (0, 0, ____)
    bar.show()
    ~~~

2.  왜 컴퓨터는 원색으로 적색, 녹색, 청색을 사용할까요?
</div>

### 조건문 (Conditionals)


작성한 히트 맵을 생성하기 위해서 필요한 또 다른 사항은 데이터 값에 따라 색깔을 선택하는 방식이다.
파이썬이 이를 위해서 제공하는 도구는 [조건문(conditional statement)](../../gloss.html#conditional-statement)이라고 불리고, 다음과 같다.


<pre class="in"><code>num = 37
if num &gt; 100:
    print &#39;greater&#39;
else:
    print &#39;not greater&#39;
print &#39;done&#39;</code></pre>

<div class="out"><pre class='out'><code>not greater
done
</code></pre></div>


코드의 두번째 줄이 `if`문을 사용하여 파이썬에게 사용자가 선택을 하고 싶다고 전한다. 만약 다음 시험(test)가 참(true)이면, 
`if`문의 몸통부분(바로 아래 들여쓰기한 행들)이 실행된다. 만약 시험이 거짓(false)이면, `else`의 몸통부분이 대신 실행된다. 오직 하나와 다른 것만이 실행된다.


<img src="img/python-flowchart-conditional.svg" alt="Executing a Conditional" />


조건문이 반듯이 `else`를 포함할 필요는 없다.
만약 `else`가 없는 상태에서 시험(test)가 거짓이라면 파이썬은 아무것도 수행하지 않는다.


<pre class="in"><code>num = 53
print &#39;before conditional...&#39;
if num &gt; 100:
    print &#39;53 is greater than 100&#39;
print &#39;...after conditional&#39;</code></pre>

<div class="out"><pre class='out'><code>before conditional...
...after conditional
</code></pre></div>


또한, 두가지 이상의 선택사항이 있다면, `elif`("else if")를 사용하여 몇개의 시험을 연쇄적으로 할 수 있다. 
이렇게 하는 것이 숫자부호를 반환하는 함수 작성을 단순하게 한다.


<pre class="in"><code>def sign(num):
    if num &gt; 0:
        return 1
    elif num == 0:
        return 0
    else:
        return -1

print &#39;sign of -3:&#39;, sign(-3)</code></pre>

<div class="out"><pre class='out'><code>sign of -3: -1
</code></pre></div>


상기 코드에서 주목할 중요한 사항은 동치 시험을 위해 한개의 등호 기호 대신에 등호 기호(`==`) 두개를 사용한 것이다.
왜냐하면 하나의 등호 기호는 할당을 의미한다.
이러한 관습은 C 언어에서 전해졌고, 많은 다른 프로그래밍 언어가 동일한 방식으로 동작하기 때문에, 익숙해질 필요는 있다.

`and` 와 `or`를 사용해서 시험을 조합할 수도 있다. `and`는 양쪽이 모두 참일 때만 참이다.


<pre class="in"><code>if (1 &gt; 0) and (-1 &gt; 0):
    print &#39;both parts are true&#39;
else:
    print &#39;one part is not true&#39;</code></pre>

<div class="out"><pre class='out'><code>one part is not true
</code></pre></div>


반면에 `or`는 양쪽 중 하나만 참이면 참이다.


<pre class="in"><code>if (1 &lt; 0) or (&#39;left&#39; &lt; &#39;right&#39;):
    print &#39;at least one test is true&#39;</code></pre>

<div class="out"><pre class='out'><code>at least one test is true
</code></pre></div>


이 경우, 둘 중에 하나는 둘중 하나 혹은 둘다를 의미한다. 하지만, 이쪽이나 저쪽 혹은 둘다 아니다를 의미하지는 않는다.


<div class="challenges" markdown="1">
#### 도전 과제

1.  `True`와 `False`가 파이썬에서 참과 거짓으로 사용되는 유일한 값은 아니다.
    사실 *임의*의 값도 `if` 혹은 `elif`에 사용될 수 있다.
    다음의 코드를 읽고 실행한 후에, 어느 값이 참이고, 어느 값이 거짓인지에 대한 규칙을 설명하세요.
    (만약 조건문의 몸통 부문이 하나의 문장으로 표현된다면, `if`와 같은 행에 코드를 작성할 수 있음을 주목하세요.)
    
    ~~~python
    if '': print 'empty string is true'
    if 'word': print 'word is true'
    if []: print 'empty list is true'
    if [1, 2, 3]: print 'non-empty list is true'
    if 0: print 'zero is true'
    if 1: print 'one is true'
    ~~~

2.  만약 첫번째 매개변수가 두번째 매개변수의 10% 내에 들면 `True`를 반환하고 그렇지 않으면 `False`를 반환하는 `near` 함수를 작성하세요.
    여러분이 작성한 코드와 동료의 코드와 비교하세요.
    모든 가능한 숫자 쌍에 대해서 동일한 결과를 반환하나요?
</div>

### 중첩 (Nesting)


인식해야 될 또다른 사항은 `if` 문이 함수와 쉽게 조합되듯이 루프와도 조합될 수 있다는 것이다.
예를 들어, 리스트의 양수를 더하고자 한다면 다음과 같이 코드를 작성한다.


<pre class="in"><code>numbers = [-5, 3, 2, -1, 9, 6]
total = 0
for n in numbers:
    if n &gt;= 0:
        total = total + n
print &#39;sum of positive values:&#39;, total</code></pre>

<div class="out"><pre class='out'><code>sum of positive values: 20
</code></pre></div>


동등하게 하나의 루프 안에서 양수의 합과 음수의 합도 계산할 수 있다.


<pre class="in"><code>pos_total = 0
neg_total = 0
for n in numbers:
    if n &gt;= 0:
        pos_total = pos_total + n
    else:
        neg_total = neg_total + n
print &#39;negative and positive sums are:&#39;, neg_total, pos_total</code></pre>

<div class="out"><pre class='out'><code>negative and positive sums are: -6 20
</code></pre></div>


심지어 루프 안에 루프를 하나 더 놓을 수도 있다.


<pre class="in"><code>for consonant in &#39;bcd&#39;:
    for vowel in &#39;ae&#39;:
        print consonant + vowel</code></pre>

<div class="out"><pre class='out'><code>ba
be
ca
ce
da
de
</code></pre></div>


다음 다이어그램에서 볼 수 있듯이, 
[외부 루프(outer loop)](../../gloss.html#outer-loop)가 한번 실행될 때마다 
[내부 루프(inner loop)](../../gloss.html#inner-loop)는 처음부터 끝까지 실행된다.


<img src="img/python-flowchart-nested-loops.svg" alt="Execution of Nested Loops" />


이미지에 패턴을 생성하기 위해서 중첩과 조건문을 조합할 수 있다.


<pre class="in"><code>square = ImageGrid(5, 5)
for x in range(square.width):
    for y in range(square.height):
        if x &lt; y:
            square[x, y] = colors[&#39;Fuchsia&#39;]
        elif x == y:
            square[x, y] = colors[&#39;Olive&#39;]
        else:
            square[x, y] = colors[&#39;SlateGray&#39;]
square.show()</code></pre>




처음으로 직접 작성한 데이터 시각화 사례다.
색상이 `x`가 `y` 보다 작거나, 같거나, 큰 것을 보여준다.


<div class="challenges" markdown="1">
#### 도전 과제

1.  상기 코드 루프의 중첩을 변경(X축 루프를 Y축 루프로 감싸는 것)하는 것이 최종 이미지를 바꾸나요? 왜 그런가요? 아니면 왜 그렇지 않나요?

2.  파이썬 (그리고 C 언어 계열의 다른 언어)은 다음과 같이 동작하는 
    [인-플레이스 연산자(in-place operators)](../../gloss.html#in-place-operator)를 제공한다.
    
    ~~~python
    x = 1  # original value
    x += 1 # add one to x, assigning result back to x
    x *= 3 # multiply x by 3
    print x
    6
    ~~~
    
    인-플레이스 연산자를 사용하여 리스트의 양수와 음수를 합하는 코드를 다시 작성하세요.
    이렇게 작성된 코드가 처음 작성한 코드보다 가독성이 더 좋나요? 아니면 더 떨어지나요?
</div>

### 히트 맵 (Heat Map) 생성하기


마지막 단계는 데이터를 사용자가 볼 수 있도록 바꾸는 것이다. 선행 학습과 마찬가지로, 첫번째 단계는 데이터를 주기억장치(메모리)에 올리는 것이다.


<pre class="in"><code>import numpy as np
data = np.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)
print &#39;data shape:&#39;, data.shape</code></pre>

<div class="out"><pre class='out'><code>data shape: (60, 40)
</code></pre></div>


두번째 단계는 데이터와 동일한 크기를 가지는 이미지 그리드를 생성하는 것이다.


<pre class="in"><code>width, height = data.shape
heatmap = ImageGrid(width, height)</code></pre>


(상기 코드의 첫번째 라인은 깔끔한 기교를 이용한 것이다. 즉, 튜플의 값을 풀어서 튜플의 항목 수 만큼 변수에 할당한다.

세번째 단계는 히트 맵에 셀을 어떻게 색칠을 할 것인지 결정한다. 간략화 해서, 적색, 녹색, 청색을 주요 색으로 사용한다.
데이터셋의 평균과 비교하여 색칠을 한다. 코드가 다음에 있다.


<pre class="in"><code>for x in range(width):
    for y in range(height):
        if data[x, y] &lt; data.mean():
            heatmap[x, y] = colors[&#39;Red&#39;]
        elif data[x, y] == data.mean():
            heatmap[x, y] = colors[&#39;Green&#39;]
        else:
            heatmap[x, y] = colors[&#39;Blue&#39;]
heatmap.show()</code></pre>



이제 키보드 몇번으로 데이터를 탐색적으로 볼 수 있다.
즉, 프로그래밍이 아닌 과학에 집중할 수 있다.


<div class="challenges" markdown="1">
#### 도전 과제

1.  히트 맵 함수 외곽에 데이터를 왜 전치하나요?
    왜 함수가 전치를 할수 없게 했나요?
    
2.  히트 맵 함수는 왜 즉시 화면에 출력하는 대신에 그리드를 반환하여 사용할까요?
    이런 결정이 여러분이 생각하기에 좋은 혹은 나쁜 디자인 선택이라고 보나요?
    
3. 다음 코드의 전반적인 효과가 무엇인지 설명하세요.

    ~~~
    temp = left
    left = right
    right = temp
    ~~~
    
    상기 코드를 다음과 비교하세요.
    
    ~~~
    left, right = right, left
    ~~~
    
    두개 코드가 동일한 것을 수행하나요? 어느 코드가 가독성이 낫다고 보십니까?
</div>


<div class="keypoints" markdown="1">
#### 주요점

*   색칠된 블록으로 단순한 "이미지"를 생성할 때 `ipythonblocks` 라이브러리에서 `ImageGrid`를 사용하세요.
*   3가지 색상(적색, 녹색, 청색)과 각 색상은 0..255 범위의 정수를 갖도록 설정하세요.
*   조건문 시작은 `if condition`문을, 부가 테스트는 `elif condition`문을, 그리고 디폴트는 `else`문을 사용하세요.
*   조건문의 분기 몸통부문은 들여쓰기 해야됩니다.
*   동치를 시험은 `==`을 사용하세요.
*   `X and Y`은 `X`와 `Y`가 모두 참이여야만 참입니다.
*   `X or Y`은 `X`와 `Y` 둘중하나가 참이거나 모두 참이여야만 참입니다.
*   0, 빈 문자열, 그리고 빈 리스트는 거짓(false)으로 다른 모든 숫자, 문자열, 리스트는 참(true)으로 간주된다.
*   다차원 데이터의 연산은 중첩 루프를 사용한다.
*   자주 바뀌는 매개변수를 함수에 넣고, 프로그램의 행동을 사용자 정의에 맞추도록 다른 매개변수로 호출하라.
</div>


#### 다음 단계

최종 히트 맵 함수는 17줄이다. 이것이 의미하는 것은 만약 각 코드 라인이 95%의 가능성으로 맞다면, 전체 함수가 올바르게 동작할 가능성은 41%에 불과하다.
좀더 학습을 진행하기 전에 코드가 프로그래머가 동작하길 기대하는 것을 수행하는지 어떻게 시험하는지 배울 것이다. 이것이 다음 학습의 주제이다.


<pre class="in"><code></code></pre>
