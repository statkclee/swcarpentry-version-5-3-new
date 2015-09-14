---
layout: lesson
root: ../..
---

## 함수 생성


만약 분석할 데이터셋이 하나라면, 파일을 스프레드쉬트에 올려서 간단한 통계치를 구하고 그래프를 그리는 것이 아마도 휠씬 빠르다. 하지만, 확인할 파일이 12개이고 앞으로 더 늘어난다면 얘기는 달라진다. 이번 학습에서 함수를 어떻게 작성하는지 배워서 하나의 명령어로 몇개 작업을 반복할 수 있다.


<div class="objectives" markdown="1">
#### 목표

*   매개변수(parameter)를 받는 함수를 작성한다.
*   함수에서 값을 반환한다.
*   함수를 디버깅하고 테스트한다.
*   콜 스택(call stack)이 무엇인지 설명하고 함수가 호출될때 콜 스택에 변경사항을 추적한다.
*   함수 인자로 디폴트 값을 설정한다.
*   왜 프로그램을 작게 단일 목적 함수로 잘게 쪼개는지 설명한다.
</div>

### 함수 정의하기


화씨(Fahrenheit)에서 절대온도(Kelvin)로 온도를 변환하는 `fahr_to_kelvin` 함수를 정의하는 것부터 시작하자.


함수정의는 `def`로 시작하고 함수이름과 매개변수의 이름이 괄호 리스트로 다음에 위치한다.
함수 [몸통부문(body)](../../gloss.html#function-body)은 정의 아래에 통상 공백 4개로 들여쓰기 하는데 함수가 실행하는 문장이다.

함수를 호출할 때, 함수에 전달하는 값은 변수에 할당되어서 함수 내부에서 사용할 수 있다. 함수 내부에 [리턴 문장(return statement)](../../gloss.html#return-statement))을 사용해서 요청하는 곳에 결과를 되돌린다.

작성한 함수를 실행하자. 본인이 작성한 함수를 호출하는 것은 다른 함수를 호출하는 것과 차이가 없다.


<pre class="in"><code>def fahr_to_kelvin(temp):
    return ((temp - 32) * (5/9)) + 273.15</code></pre>


<pre class="in"><code>print &#39;freezing point of water:&#39;, fahr_to_kelvin(32)
print &#39;boiling point of water:&#39;, fahr_to_kelvin(212)</code></pre>

<div class="out"><pre class='out'><code>freezing point of water: 273.15
boiling point of water: 273.15
</code></pre></div>


정의한 함수를 성공적으로 호출해서 반환한 값에 접근할 수 있다. 불행하게도 반환된 값이 올바른 것 같지 않다. 무엇이 잘못된걸까?

### 함수 디버깅


*디버깅*은 올바르게 동작하지 않는 코드의 일부를 수정하는 것이다. 상기의 경우 `fahr_to_kelvin`가 잘못된 답을 주는 것을 알기 때문에 왜 그런지 이유를 파악해 보자.

코드가 매우 큰 경우에 디버깅 과정을 도와주는 *디버거(debugger)*로 불리는 도구가 별도로 있다.

간단하고 짧은 함수여서 매개 변수를 골라, 함수를 작은 부분으로 쪼개서 각 부분에 값을 출력해서 디버그한다.


<pre class="in"><code># We&#39;ll use temp = 212, the boiling point of water, which was incorrect
print &#34;212 - 32:&#34;, 212 - 32</code></pre>

<div class="out"><pre class='out'><code>212 - 32: 180
</code></pre></div>


<pre class="in"><code>print &#34;(212 - 32) * (5/9):&#34;, (212 - 32) * (5/9)</code></pre>

<div class="out"><pre class='out'><code>(212 - 32) * (5/9): 0
</code></pre></div>


문제가 `5/9`을 곱할 때 발생한다. `5/9`의 값이 0 이기 때문이다.


<pre class="in"><code>5/9</code></pre>

<div class="out"><pre class='out'><code>0</code></pre></div>


컴퓨터는 숫자를 두가지 중 한가지로 저장한다. [정수(integers)](../../gloss.html#integer) 혹은 [부동 소수점(floating-point numbers)](../../gloss.html#float-point-number). 정수는 일반적으로 숫자를 세는 숫자이고, 부동 소수점은 실수와 실수의 소수부가 있다.
덧셈, 뺄셈, 곱셈은 정수나 부동 소수점이나 우리가 통상 예측하는 방식으로 동작하지만, 나눗셈은 다르게 동작한다.
정수를 다른 정수로 나누게 된다면, 나머지 없는 몫만 얻는다.


<pre class="in"><code>print &#39;10/3 is:&#39;, 10/3</code></pre>

<div class="out"><pre class='out'><code>10/3 is: 3
</code></pre></div>


다른 한편으로, 분모와 분자 둘중의 하나가 부동 소수점이라면, 컴퓨터는 부동 소수점 결과를 생성한다.


<pre class="in"><code>print &#39;10.0/3 is:&#39;, 10.0/3</code></pre>

<div class="out"><pre class='out'><code>10.0/3 is: 3.33333333333
</code></pre></div>


컴퓨터는 역사적인 이유로 이와 같이 동작한다. 정수 연산은 초기 컴퓨터에서 훨씬 빨랐고, 많은 상황에서 유용했다. 하지만, 여전히 혼란스럽다. 그래서 파이썬 3는 정수로 나눗셈을 할 때 부동 소수점 결과를 반환한다.
파이썬 2.7 버젼을 사용하는 경우, 만약 `5/9` 연산의 올바른 결과를 얻으려면, `5.0/9`, `5/9.0` 혹은 다른 방식으로 연산을 작성해야 한다.

부동 소수점 결과를 얻는 다른 방식은 명시적으로 산출해야하는 결과를 컴퓨터에 지시한다. 숫자 중에 하나를 형변환(cast)한다.


<pre class="in"><code>print &#39;float(10)/3 is:&#39;, float(10)/3</code></pre>

<div class="out"><pre class='out'><code>float(10)/3 is: 3.33333333333
</code></pre></div>


상기 방식의 장점은 변수와 함께 사용될 수 있다는 것이다. 직접 살펴보자.


<pre class="in"><code>a = 10
b = 3
print &#39;a/b is:&#39;, a/b
print &#39;float(a)/b is:&#39;, float(a)/b</code></pre>

<div class="out"><pre class='out'><code>a/b is: 3
float(a)/b is: 3.33333333333
</code></pre></div>


`a` 혹은 `b`를 다시 정의하는 것보다 더 쉽다는 것을 볼 수 있다.

지금까지 학습한 지식으로 `fahr_to_kelvin` 함수를 고쳐보자.


<pre class="in"><code>def fahr_to_kelvin(temp):
    return ((temp - 32) * (5.0/9.0)) + 273.15

print &#39;freezing point of water:&#39;, fahr_to_kelvin(32)
print &#39;boiling point of water:&#39;, fahr_to_kelvin(212)</code></pre>

<div class="out"><pre class='out'><code>freezing point of water: 273.15
boiling point of water: 373.15
</code></pre></div>


잘 동작한다!

### 함수 조합하기


화씨온도를 절대온도로 어떻게 변환하는지 봤기 때문에 절대온도를 섭씨온도로 바꾸는 것은 쉽다.


<pre class="in"><code>def kelvin_to_celsius(temp):
    return temp - 273.15

print &#39;absolute zero in Celsius:&#39;, kelvin_to_celsius(0.0)</code></pre>

<div class="out"><pre class='out'><code>absolute zero in Celsius: -273.15
</code></pre></div>


화씨온도에서 섭씨온도로 변환하는 것은 어떤가요? 
공식을 적을 수도 있지만, 그럴 필요가 없다. 
이미 작성한 두개의 함수를 [조합(compose)](../../gloss.html#function-composition)할 수 있다.


<pre class="in"><code>def fahr_to_celsius(temp):
    temp_k = fahr_to_kelvin(temp)
    result = kelvin_to_celsius(temp_k)
    return result

print &#39;freezing point of water in Celsius:&#39;, fahr_to_celsius(32.0)</code></pre>

<div class="out"><pre class='out'><code>freezing point of water in Celsius: 0.0
</code></pre></div>


어떻게 좀더 커다란 프로그램이 만들어지는지 첫번째 맛을 봤다. 기본 연산을 정의하고 원하는 효과를 얻기 위해서 이를 조합한다. 실제 함수는 상기 보여진 것보다 더 크다. 일반적으로 대략 6줄에서 20~30줄 정도 한다. 하지만 이보다 함수가 더 길거나 함수를 읽는 사람이 어떻게 동작하는지 이해할수 없는 것은 곤란하다.


<div class="challenges" markdown="1">
#### 도전 과제

1.  두 개의 문자열을 "더하는 것"은 사슬처럼 잇게 한다(concatenate). 즉, 
    `'a' + 'b'`는 `'ab'`이 된다.
    두 개의 매개변수 `original`과 `wrapper`를 받아 `original`의 처음과 끝에 `wrapper`를 씌워 새로운 문자열을 반환하는 함수 `fence`를 작성하세요.
    
    ~~~python
    print fence('name', '*')
    *name*
    ~~~

1.  만약 변수 `s`가 문자열이면, `s[0]`는 첫번째 문자이고, `s[-1]`은 마지막 문자가 된다.
    입력문자열의 처음과 끝 문자로만 구성된 문자열을 반환하는 함수 `outer`를 작성하세요.

    ~~~python
    print outer('helium')
    hm
    ~~~
</div>

### 콜 스택(Call Stack)


`fahr_to_celsius(32.0)`을 호출할 때 무엇이 생기는지 좀더 자세히 살펴보자. 좀더 명확하기 하기 위해서, 변수에 초기값을 32.0으로 설정하고 결과를 `final`에 저장해서 출발해봅시다.


<pre class="in"><code>original = 32.0
final = fahr_to_celsius(original)</code></pre>


다음 다이어그램은 첫번째 행이 실행된 다음에 메모리가 어떻게 되지는 보여준다.


<img src="img/python-call-stack-01.svg" alt="Call Stack (Initial State)" />


함수 `fahr_to_celsius`을 호출할 때, R은 변수 `temp`를 바로 생성하지는 *않는다*. 대신에 [스택 프레임(stack frame)](../../gloss.html#stack-frame)을
생성해서 `fahr_to_kelvin`함수가 정의한 변수를 추적한다. 초기에 스택은 `temp` 값만을 가지고 있다.


<img src="img/python-call-stack-02.svg" alt="Call Stack Immediately After First Function Call" />


`fahr_to_celsius` 함수 내부에 `fahr_to_kelvin` 함수를 호출할 때, 파이썬은 또 다른 스택 프레임을 생성해서 `fahr_to_kelvin`의 변수를 저장한다.


<img src="img/python-call-stack-03.svg" alt="Call Stack During First Nested Function Call" />


이제 temp로 불리는 동작하는 두개의 변수가 있다. 하나는 `fahr_to_celsius` 함수의 매개변수이고, 다른 하나는 `fahr_to_kelvin` 함수의 매개변수다. 프로그램의 같은 부분에 동일한 이름을 가진 변수 두개가 있는 것이 애매모호해서, 파이썬(그리고 다른 최신 프로그래밍 언어)은 각 함수 호출에 대해서 새로운 스택 프레임을 생성해서 다른 함수에서 정의된 변수와 구별되게 함수의 변수를 보관한다. 

`fahr_to_kelvin` 함수 호출이 값을 반환할 때, 파이썬은 `fahr_to_kelvin` 함수의 스택 프레임을 사용한 후 버리고 절대 온도 정보를 보관하기 위해서 `fahr_to_celsius`에 대한 스택 프레임에 새로운 변수를 생성한다.


<img src="img/python-call-stack-04.svg" alt="Call Stack After Return From First Nested Function Call" />


그리고 나서 `kelvin_to_celsius`을 호출하는데 함수의 변수를 저장할 스택 프레임을 생성한다는 의미다.


<img src="img/python-call-stack-05.svg" alt="Call Stack During Call to Second Nested Function" />


다시 한번, 파이썬은 `kelvin_to_celsius` 함수가 수행완료될 때 스택 프레임을 폐기한다. 그리고 `fahr_to_celsius` 함수를 위해 스택 프레임에 `result` 변수를 생성한다.


<img src="img/python-call-stack-06.svg" alt="Call Stack After Second Nested Function Returns" />


마지막으로, `fahr_to_celsius` 함수 수행이 완료될 때, 파이썬은 *자신의* 스택 프레임을 폐기하고 초기 시작한 스택 프레임에 있는 신규 변수 `final`에 결과값을 넣는다.


<img src="img/python-call-stack-07.svg" alt="Call Stack After All Functions Have Finished" />


이 마지막 스택 프레임은 항상 존재해서 작성한 코드 중에 함수 외부에서 정의한 변수를 간직한다. 간직하지 *않는* 것은 다양한 스택 프레임에 있었던 변수다. 만약 함수가 수행 종료된 후에 `temp` 값을 얻고자 한다면, 파이썬은 그런 것은 없다고 회답한다.


<pre class="in"><code>print &#39;final value of temp after all function calls:&#39;, temp</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
&lt;ipython-input-12-ffd9b4dbd5f1&gt; in &lt;module&gt;()
----&gt; 1 print &#39;final value of temp after all function calls:&#39;, temp

NameError: name &#39;temp&#39; is not defined</code></pre><pre class='out'><code>final value of temp after all function calls:</code></pre></div>


왜 이 모든 어려움으로 갈가요? 배열의 최대값과 최소값의 차이를 계산하는 `span`이라는 함수가 다음에 있다.


<pre class="in"><code>import numpy

def span(a):
    diff = a.max() - a.min()
    return diff

data = numpy.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)
print &#39;span of data&#39;, span(data)</code></pre>

<div class="out"><pre class='out'><code> span of data 20.0
</code></pre></div>


`span` 함수는 값을 `diff` 변수에 할당함을 주목하세요. 염증 데이터 정보를 담고 있는 동일한 이름의 변수(`diff`)를 매우 사용할 수도 있다.


<pre class="in"><code>diff = numpy.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)
print &#39;span of data:&#39;, span(diff)</code></pre>

<div class="out"><pre class='out'><code>span of data: 20.0
</code></pre></div>


함수 호출 뒤에 변수 `diff`가 값 20.0을 갖게 되는 것을 기대하지 않는다. 
그래서 `diff` 이름은 프로그램 메인 몸체부문에서 하는 것처럼 `span` 내부에 정의된 동일한 변수를 참조할 수 없다. 이 경우에 메인 프로그램에 `diff`와 다른 이름을 아마도 선택할 수 있지만, 변수의 값이 변경되는 경우마다 무슨 변수명이 사용되었는지를 살펴보기 위해 호출하는 NumPy의 모든 코드 행을 읽고 싶지는 않다.


여기서 기본적인 아이디어는 [캡슐화(encapsulation)](../../gloss.html#encapsulation)이고, 정확하고 이해하기 쉬운 프로그램을 작성하는 열쇠다. 함수가 하는 일은 몇개의 작업을 하나로 변환하는 것이어서 무언가를 하고자 할 때마다 수십개에서 수백개의 문장을 수행하는 대신에 단 하나의 함수 호출을 생각할 수 있다. 함수가 서로에게 간섭하지 않는다면 이 방식은 동작한다. 만약 서로 간섭하게 되면 다시 한번 세부사항에 주의를 기울여야 하고 급격하게 단기 기억에 과부하를 주게된


<div class="challenges" markdown="1">
#### 도전 과제

1.  이전에 `fence`와 `outer` 함수를 작성했다. 다음을 실행할 때 콜 스택(call stack)이 어떻게 변하는지 다이어그램을 그려보세요.
    
    ~~~python
    print outer(fence('carbon', '+'))
    ~~~
</div>

### 테스팅과 문서화


함수에 명령어들을 넣어서 재사용할 수 있자마자, 작성한 함수가 제대로 동작하는지를 테스트할 필요가 있다. 어떻게 수행하는지 알아보기 위해서, 특정한 값 주위에 데이터를 중앙에 위치하게 하는 함수를 작성하자.


<pre class="in"><code>def center(data, desired):
    return (data - data.mean()) + desired</code></pre>


작성한 함수를 실제 데이터에 테스트할 수도 있으나, 값이 무엇이 되어야하는지 모르기 때문에 결과가 맞는지 구분하기가 어렵다. 대신에, NumPy를 사용해서 0으로 구성된 행렬을 생성하고 3 주위가 중심이 되게 하자.


<pre class="in"><code>z = numpy.zeros((2,2))
print center(z, 3)</code></pre>

<div class="out"><pre class='out'><code>[[ 3.  3.]
 [ 3.  3.]]
</code></pre></div>


맞는 것처럼 보여서 실제 데이터에 중심(`center`)을 잡도록 하자


<pre class="in"><code>data = numpy.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)
print center(data, 0)</code></pre>

<div class="out"><pre class='out'><code>[[-6.14875 -6.14875 -5.14875 ..., -3.14875 -6.14875 -6.14875]
 [-6.14875 -5.14875 -4.14875 ..., -5.14875 -6.14875 -5.14875]
 [-6.14875 -5.14875 -5.14875 ..., -4.14875 -5.14875 -5.14875]
 ..., 
 [-6.14875 -5.14875 -5.14875 ..., -5.14875 -5.14875 -5.14875]
 [-6.14875 -6.14875 -6.14875 ..., -6.14875 -4.14875 -6.14875]
 [-6.14875 -6.14875 -5.14875 ..., -5.14875 -5.14875 -6.14875]]
</code></pre></div>


상기 출력으로부터 결과가 맞는지 구분하기 어렵다. 하지만, 확인을 할 수 있는 몇가지 테스트가 있다.


<pre class="in"><code>print &#39;original min, mean, and max are:&#39;, data.min(), data.mean(), data.max()
centered = center(data, 0)
print &#39;min, mean, and and max of centered data are:&#39;, centered.min(), centered.mean(), centered.max()</code></pre>

<div class="out"><pre class='out'><code>original min, mean, and max are: 0.0 6.14875 20.0
min, mean, and and max of centered data are: -6.14875 -3.49054118942e-15 13.85125
</code></pre></div>


거의 맞는 것처럼 보인다. 
원 평균은 약 6.1이였다. 그래서 0 에서 하한은 약 -6.1이다. 
중앙으로 위치한 데이터의 평균은 0 은 아니지만, 무척 가깝다. 도전 과제에서 왜 그렇게 되었는지 탐색할 것이다.좀더 나아가 표준편차가 바뀌었는지 확인하자.


<pre class="in"><code>print &#39;std dev before and after:&#39;, data.std(), centered.std()</code></pre>

<div class="out"><pre class='out'><code>std dev before and after: 4.61383319712 4.61383319712
</code></pre></div>


두 값이 동일하다. 하지만 6번째 소수점에서 차이가 있다면 알아채지 못할 것이다. 대신에 다음을 수행하자.


<pre class="in"><code>print &#39;difference in standard deviations before and after:&#39;, data.std() - centered.std()</code></pre>

<div class="out"><pre class='out'><code>difference in standard deviations before and after: -3.5527136788e-15
</code></pre></div>


다시 차이가 매우 작다. 여전히 함수가 잘못될 가능성은 여전히 있다. 하지만, 분석으로 되돌릴 정도는 아닐 듯 하다. 하지만, 한 가지 더 작업이 있다. 후에 작성한 함수가 무엇을 위한 것이고 어떻게 사용하는지에 대해서 우리 자신에게 되새김되도록 함수의 [문서화(documentation)](../../gloss.html#documentation)를 위해서 문서를 작성해야 한다.

소프트웨어에 문서를 넣는 일반적인 방법은 다음과 같은 [주석(comments)](../../gloss.html#comment)을 추가하는 것이다.


<pre class="in"><code># center(data, desired): return a new array containing the original data centered around the desired value.
def center(data, desired):
    return (data - data.mean()) + desired</code></pre>


하지만, 더 나은 방법이 있다. 만약 함수의 첫번째 것이 변수에 할당되지 않은 문자열이라면, 
그 문자열은 문서로서 함수에 덧붙여진다.


<pre class="in"><code>def center(data, desired):
    &#39;&#39;&#39;Return a new array containing the original data centered around the desired value.&#39;&#39;&#39;
    return (data - data.mean()) + desired</code></pre>


상기 것이 더 좋은데, 파이썬의 내장 도움말 시스템에 함수의 문서를 보여줄 수 있다.


<pre class="in"><code>help(center)</code></pre>

<div class="out"><pre class='out'><code>Help on function center in module __main__:

center(data, desired)
    Return a new array containing the original data centered around the desired value.

</code></pre></div>


이와 같은 문자열을 [docstring](../../gloss.html#docstring)이라고 한다.
주석을 작성할 때 인용부호를 3번 사용할 필요는 없다. 하지만, 만약 인용부호를 3번 사용한다면, 여러줄에 걸쳐서 도움말을 쪼개 작성할 수 있다.


<pre class="in"><code>def center(data, desired):
    &#39;&#39;&#39;Return a new array containing the original data centered around the desired value.
    Example: center([1, 2, 3], 0) =&gt; [-1, 0, 1]&#39;&#39;&#39;
    return (data - data.mean()) + desired

help(center)</code></pre>

<div class="out"><pre class='out'><code>Help on function center in module __main__:

center(data, desired)
    Return a new array containing the original data centered around the desired value.
    Example: center([1, 2, 3], 0) =&gt; [-1, 0, 1]

</code></pre></div>


<div class="challenges" markdown="1">
#### 도전 과제

1.  `analyze` 함수를 작성해서 매개변수로 파일 이름을 받아서 [앞선 학습](01-numpy.ipynb) 
    결과(시간에 따른 염증의 평균값, 최소값, 최대값)를 그래프로 화면에 출력하도록 하세요.`analyze("inflammation-01.csv`") 결과는 
    이미 보여진 그래프를 생성해야 하지만, `analyze("inflammation-02.csv")` 결과는 두번째 데이터셋에 상응하는 그래프를 생성해야 한다. 
    `docstring`으로 함수를 문서화하도록 확인하세요.    

2.  `rescale`함수를 작성해서 입력값으로 벡터를 받고, 0.0에서 1.0사이의 범위로 조정되게 해당 벡터를 반환하게 하세요. 
    만약 $L$과 $H$이 원 벡터이 하한과 상한이라면, $v$의 치환값은 $(v-L) / (H-L)$이 되어야 한다. 
    `docstring`으로 함수를 문서화하도록 확인하세요.

3.  `help(numpy.arange)`과 `help(numpy.linspace)` 도움말 명령어를 실행해서 어떻게 함수를 사용하여 균등한 간격을 가진 값을 생성하는지 살펴보세요.
    그리고 나서 이들 값을 사용하여 rescale 함수가 정상적으로 동작하는지 시험하세요.
</div>

### 초기 설정(Default) 정의


두 가지 방식으로 함수에 매개변수를 넘겨줬다. `span(data)`처럼 직접적으로, 
`numpy.loadtxt(fname='something.csv', delimiter=',')`과 같이 이름으로 넘겨줬다. 사실 매개변수를 `fname=` 없이 `loadtxt` 함수에 파일이름을 넘길 수 있다.


<pre class="in"><code>numpy.loadtxt(&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)</code></pre>

<div class="out"><pre class='out'><code>array([[ 0.,  0.,  1., ...,  3.,  0.,  0.],
       [ 0.,  1.,  2., ...,  1.,  0.,  1.],
       [ 0.,  1.,  1., ...,  2.,  1.,  1.],
       ..., 
       [ 0.,  1.,  1., ...,  1.,  1.,  1.],
       [ 0.,  0.,  0., ...,  0.,  2.,  0.],
       [ 0.,  0.,  1., ...,  1.,  1.,  0.]])</code></pre></div>


하지만, 여전히 `delimiter=`을 언급할 필요가 있다.


<pre class="in"><code>numpy.loadtxt(&#39;inflammation-01.csv&#39;, &#39;,&#39;)</code></pre>

<div class="out"><pre class='err'><code>---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
&lt;ipython-input-26-e3bc6cf4fd6a&gt; in &lt;module&gt;()
----&gt; 1 numpy.loadtxt(&#39;inflammation-01.csv&#39;, &#39;,&#39;)

/Users/gwilson/anaconda/lib/python2.7/site-packages/numpy/lib/npyio.pyc in loadtxt(fname, dtype, comments, delimiter, converters, skiprows, usecols, unpack, ndmin)
    775     try:
    776         # Make sure we&#39;re dealing with a proper dtype
--&gt; 777         dtype = np.dtype(dtype)
    778         defconv = _getconv(dtype)
    779 

TypeError: data type &#34;,&#34; not understood</code></pre></div>


무엇이 진행되는지 이해하고 작성한 함수를 사용하기 좀더 쉽게 하기 위해서, 다음과 같이 `center` 함수를 다시 정의하자.


<pre class="in"><code>def center(data, desired=0.0):
    &#39;&#39;&#39;Return a new array containing the original data centered around the desired value (0 by default).
    Example: center([1, 2, 3], 0) =&gt; [-1, 0, 1]&#39;&#39;&#39;
    return (data - data.mean()) + desired</code></pre>


중요 변경사항은 두번째 인자가 이제 `desired` 대신에 `desired = 0.0`이 되었다. 인자 두개로 가진 함수를 호출하면, 전과 동일한 방식으로 동작한다.


<pre class="in"><code>test_data = numpy.zeros((2, 2))
print center(test_data, 3)</code></pre>

<div class="out"><pre class='out'><code>[[ 3.  3.]
 [ 3.  3.]]
</code></pre></div>


하지만, 단지 하나의 인자로 함수를 호출할 수도 있다. 이 경우에 `desired`는 자동적으로 [초기 설정값(default value)](../../gloss.html#default-parameter-value) 0.0 이 할당된다.


<pre class="in"><code>more_data = 5 + numpy.zeros((2, 2))
print &#39;data before centering:&#39;, more_data
print &#39;centered data:&#39;, center(more_data)</code></pre>

<div class="out"><pre class='out'><code>data before centering: [[ 5.  5.]
 [ 5.  5.]]
centered data: [[ 0.  0.]
 [ 0.  0.]]
</code></pre></div>


매우 편리하다. 만약 한 방식으로 동작하는 함수를 원하지만 때때로 다르게 동작할 필요가 있다면, 필요할 때만 매개변수를 넘기게 하고 정상적인 경우는 좀더 쉽게 하려고 초기 설정값을 넣는다.

다음 예제는 어떻게 파이썬이 값을 인자에 매칭하는지 보여준다.


<pre class="in"><code>def display(a=1, b=2, c=3):
    print &#39;a:&#39;, a, &#39;b:&#39;, b, &#39;c:&#39;, c

print &#39;no parameters:&#39;
display()
print &#39;one parameter:&#39;
display(55)
print &#39;two parameters:&#39;
display(55, 66)</code></pre>

<div class="out"><pre class='out'><code>no parameters:
a: 1 b: 2 c: 3
one parameter:
a: 55 b: 2 c: 3
two parameters:
a: 55 b: 66 c: 3
</code></pre></div>


예제가 보여주듯이, 인자는 왼쪽에서 오른쪽으로 매칭된다. 그리고 명시적으로 값이 주어지지 않는 것은 초기 설정값을 갖는다. 인자를 넘길 때 값에 이름을 줌으로써 이런 행동을 오버라이드(override)할 수 있다.


<pre class="in"><code>print &#39;only setting the value of c&#39;
display(c=77)</code></pre>

<div class="out"><pre class='out'><code>only setting the value of c
a: 1 b: 2 c: 77
</code></pre></div>


상기 내용을 가지고, `numpy.loadtxt` 함수의 도움말을 살펴 보자.


<pre class="in"><code>help(numpy.loadtxt)</code></pre>

<div class="out"><pre class='out'><code>Help on function loadtxt in module numpy.lib.npyio:

loadtxt(fname, dtype=&lt;type &#39;float&#39;&gt;, comments=&#39;#&#39;, delimiter=None, converters=None, skiprows=0, usecols=None, unpack=False, ndmin=0)
    Load data from a text file.
    
    Each row in the text file must have the same number of values.
    
    Parameters
    ----------
    fname : file or str
        File, filename, or generator to read.  If the filename extension is
        ``.gz`` or ``.bz2``, the file is first decompressed. Note that
        generators should return byte strings for Python 3k.
    dtype : data-type, optional
        Data-type of the resulting array; default: float.  If this is a
        record data-type, the resulting array will be 1-dimensional, and
        each row will be interpreted as an element of the array.  In this
        case, the number of columns used must match the number of fields in
        the data-type.
    comments : str, optional
        The character used to indicate the start of a comment;
        default: &#39;#&#39;.
    delimiter : str, optional
        The string used to separate values.  By default, this is any
        whitespace.
    converters : dict, optional
        A dictionary mapping column number to a function that will convert
        that column to a float.  E.g., if column 0 is a date string:
        ``converters = {0: datestr2num}``.  Converters can also be used to
        provide a default value for missing data (but see also `genfromtxt`):
        ``converters = {3: lambda s: float(s.strip() or 0)}``.  Default: None.
    skiprows : int, optional
        Skip the first `skiprows` lines; default: 0.
    usecols : sequence, optional
        Which columns to read, with 0 being the first.  For example,
        ``usecols = (1,4,5)`` will extract the 2nd, 5th and 6th columns.
        The default, None, results in all columns being read.
    unpack : bool, optional
        If True, the returned array is transposed, so that arguments may be
        unpacked using ``x, y, z = loadtxt(...)``.  When used with a record
        data-type, arrays are returned for each field.  Default is False.
    ndmin : int, optional
        The returned array will have at least `ndmin` dimensions.
        Otherwise mono-dimensional axes will be squeezed.
        Legal values: 0 (default), 1 or 2.
        .. versionadded:: 1.6.0
    
    Returns
    -------
    out : ndarray
        Data read from the text file.
    
    See Also
    --------
    load, fromstring, fromregex
    genfromtxt : Load data with missing values handled as specified.
    scipy.io.loadmat : reads MATLAB data files
    
    Notes
    -----
    This function aims to be a fast reader for simply formatted files.  The
    `genfromtxt` function provides more sophisticated handling of, e.g.,
    lines with missing values.
    
    Examples
    --------
    &gt;&gt;&gt; from StringIO import StringIO   # StringIO behaves like a file object
    &gt;&gt;&gt; c = StringIO(&#34;0 1\n2 3&#34;)
    &gt;&gt;&gt; np.loadtxt(c)
    array([[ 0.,  1.],
           [ 2.,  3.]])
    
    &gt;&gt;&gt; d = StringIO(&#34;M 21 72\nF 35 58&#34;)
    &gt;&gt;&gt; np.loadtxt(d, dtype={&#39;names&#39;: (&#39;gender&#39;, &#39;age&#39;, &#39;weight&#39;),
    ...                      &#39;formats&#39;: (&#39;S1&#39;, &#39;i4&#39;, &#39;f4&#39;)})
    array([(&#39;M&#39;, 21, 72.0), (&#39;F&#39;, 35, 58.0)],
          dtype=[(&#39;gender&#39;, &#39;|S1&#39;), (&#39;age&#39;, &#39;&lt;i4&#39;), (&#39;weight&#39;, &#39;&lt;f4&#39;)])
    
    &gt;&gt;&gt; c = StringIO(&#34;1,0,2\n3,0,4&#34;)
    &gt;&gt;&gt; x, y = np.loadtxt(c, delimiter=&#39;,&#39;, usecols=(0, 2), unpack=True)
    &gt;&gt;&gt; x
    array([ 1.,  3.])
    &gt;&gt;&gt; y
    array([ 2.,  4.])

</code></pre></div>


많은 정보가 있지만, 가장 중요하는 부분은 처음 몇줄이다.

~~~python
loadtxt(fname, dtype=<type 'float'>, comments='#', delimiter=None, converters=None, skiprows=0, usecols=None,
        unpack=False, ndmin=0)
~~~

`loadtxt`는 하나의 매개변수 `fname`만 초기 설정을 갖지 않고, 다른 6개는 초기 설정을 가지는 것을 보여준다. 만약 함수를 다음과 같이 호출한다면,

~~~python
numpy.loadtxt('inflammation-01.csv', ',')
~~~

파일 이름이 사용자가 원하는 위치인 `fname`에 할당되지만, 구분자 문자열 `','`은 `delimiter` 대신에 `dtype`에 할당된다. 왜냐하면, `dtype`이 매개변수 리스트에 두번째 위치에 있기 때문이다. 이러한 이유로 파일이름에 대해서 `fname=`을 명기할 필요는 없지만, 두번째 매개변수에 대해서는 `delimiter=`를 *명기해야* 한다.


<div class="challenges" markdown="1">
#### 도전 과제

1.  `rescale` 함수를 재작성해서 초기 설정으로 데이터가 0.0 에서 1.0 사이에 놓게 하세요. 
    하지만 호출자가 원한다면, 하한과 상한을 지정할 수 있게 하세요. 
    옆 사람과 구현한 것을 비교하세요. 두 함수가 항상 같은 방식으로 동작하나요?
</div>


<div class="keypoints" markdown="1">
#### 주요점

*   `def name(...params...)`을 사용해서 함수를 작성하라.
*   함수의 몸통부분은 들여쓰기 한다.
*   `name(...values...)`을 사용해서 함수를 호출하라.
*   숫자는 정수 혹은 부동 소수점으로 저장된다.
*   정수 나눗셈은 결과값은 몫만 산출하고 실수의 소수부는 산출하지 않는다.
*   함수가 매번 호출될 때마다, 신규 스택 프레임이 [콜 스택(call stack)](../../gloss.html#call-stack)에 생성되어 인자와 로컬 변수를 가진한다.
*   파이썬은 상위 수준에서 변수를 찾기 전에 현재 스택 프레임에서 변수를 찾는다.
*   무언가의 도움말을 보기 위해서 help(thing)을 사용한다.
*   함수에 도움말을 제공하려면 함수에 docstring을 넣어라.   
*   매개변수 목록에 `name = value`을 사용해서 함수를 정의할 때, 인자에 대해서 초기 설정값을 명기하라.    
*   매개변수는 이름, 위치, 혹은 생략하고 넘길 수 있다. 생략하는 경우 초기설정값이 사용된다.
</div>


#### 다음 단계

`analyze` 함수를 가지고 하나의 데이터셋을 시각화할 수 있다. 다음과 같이 12개 데이터넷 모두를 탐색하려고 사용할 수 있다.

~~~python
analyze('inflammation-01.csv')
analyze('inflammation-02.csv')
...
analyze('inflammation-12.csv')
~~~

하지만, 12개 파일이름을 모두 정확하게 타이핑할 가능성은 크지 않다. 그리고 만약 백개 파일이 더 있다면 더 난감하게 될 것이다. 필요한 것은 파이썬이 각 파일에 대해서 한번씩 무엇을 수행하게 하는 것이다. 이것이 다음 학습주제다.


<pre class="in"><code></code></pre>
