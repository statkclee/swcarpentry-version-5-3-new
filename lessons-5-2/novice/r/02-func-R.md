---
layout: lesson
root: ../..
---



## 함수 생성하기

만약 분석할 데이터셋이 하나라면, 파일을 스프레드쉬트에 올려서 간단한 통계치를 구하고 그래프를 그리는 것이 아마도 휠씬 빠르다. 하지만, 확인할 파일이 12개이고 앞으로 더 늘어난다면 얘기는 달라진다. 이번 학습에서 함수를 어떻게 작성하는지 배워서 하나의 명령어로 몇개 작업을 반복할 수 있다.

#### 목표

* 인자(argument)를 받는 함수를 작성한다.
* 함수에서 값을 반환한다.
* 함수를 테스트한다.
* 콜 스택(call stack)이 무엇인지 설명하고 함수가 호출될때 콜 스택에 변경사항을 추적한다.
* 함수 인자로 디폴트 값을 설정한다.
* 왜 프로그램을 작게 단일 목적 함수로 잘게 쪼개는지 설명한다.

### 함수 정의하기

화씨(Fahrenheit)에서 절대온도(Kelvin)로 온도를 변환하는 `fahr_to_kelvin` 함수를 정의하는 것부터 시작하자.

<pre class='in'><code>fahr_to_kelvin <- function(temp) {
  kelvin <- ((temp - 32) * (5 / 9)) + 273.15
  return(kelvin)
}</code></pre>

함수 `function` 출력을 할당해서 `fahr_to_kelvin` 함수를 정의한다.
인자이름 목록은 괄호에 포함된다. 다음에 함수 [몸통부문(body)](../../gloss.html#function-body)은 함수가 수행될 때 실행되는 문장(스테이트먼트, statement)으로 중괄호(`{}`) 내부에 포함된다. 몸통부문의 문장은 공백 2개로 들여쓰기 된다. 들여쓰기는 코드를 일기 쉽게하지만, 코드가 어떻게 동작하는지에는 영향을 주지 않는다.

함수를 호출할 때, 함수에 전달하는 값은 변수에 할당되어서 함수 내부에서 사용할 수 있다. 함수 내부에 [리턴 문장(return statement)](../../gloss.html#return-statement)을 사용해서 요청하는 곳에 결과를 되돌린다.

> **Tip:** R에만 있는 독특한 기능 중의 하나는 반환문장(return statement)가 반드시 필요한 아니다. R은 자동적으로 함수 몸통부문 마지막 행에 있는 임의의 변수를 반환한다. 지금은 학습 단계여서 명시적으로 반환문장을 정의한다.

상기 함수를 실행하자. 본인이 작성한 함수를 호출하는 것은 다른 함수를 호출하는 것과 차이가 없다.

<pre class='in'><code># 물의 어는 점
fahr_to_kelvin(32)</code></pre>



<div class='out'><pre class='out'><code>[1] 273.1
</code></pre></div>



<pre class='in'><code># 물의 끓는 점
fahr_to_kelvin(212)</code></pre>



<div class='out'><pre class='out'><code>[1] 373.1
</code></pre></div>

정의한 함수를 성공적으로 호출해서 반환한 값에 접근할 수 있다.

### 함수 조합하기

화씨온도를 절대온도로 어떻게 변환하는지 봤기 때문에 절대온도를 섭씨온도로 바꾸는 것은 쉽다.

<pre class='in'><code>kelvin_to_celsius <- function(temp) {
  celsius <- temp - 273.15
  return(celsius)
}

# 섭씨에서 영도
kelvin_to_celsius(0)</code></pre>


<div class='out'><pre class='out'><code>[1] -273.1
</code></pre></div>

화씨온도에서 섭씨온도로 변환하는 것은 어떤가요? 공식을 적을 수도 있지만, 그럴 필요가 없다. 이미 작성한 두개의 함수를 [조합(compose)](../../gloss.html#function-composition)할 수 있다.

<pre class='in'><code>fahr_to_celsius <- function(temp) {
  temp_k <- fahr_to_kelvin(temp)
  result <- kelvin_to_celsius(temp_k)
  return(result)
}

# freezing point of water in Celsius
fahr_to_celsius(32.0)</code></pre>



<div class='out'><pre class='out'><code>[1] 0
</code></pre></div>

어떻게 좀더 커다란 프로그램이 만들어지는지 첫번째 맛을 봤다. 기본 연산을 정의하고 원하는 효과를 얻기 위해서 이를 조합한다. 실제 함수는 상기 보여진 것보다 더 크다. 일반적으로 대략 6줄에서 20~30줄 정도 한다. 하지만 이보다 함수가 더 길거나 함수를 읽는 사람이 어떻게 동작하는지 이해할수 없는 것은 곤란하다.

#### 도전 

  + 마지막 학습에서 `c` 함수를 사용해서 요소(element)를 결합(**c**oncatenate)하는지 배웠다. 예로,`x <- c("A", "B", "C")` 문장은 3 요소를 가진 벡터 `x`를 생성한다. 좀더 나아가, `c`를 사용해서 상기 벡터를 확장할 수 있다. 예로, `y <- c(x, "D")` 문장은 4개 요소를 가진 벡터 `y`를 생성한다.
  인자로 `original`과 `wrapper` 벡터 두개를 받는 `fence` 함수를 작성하세요. `fence` 함수는 `original` 앞과 뒤를 감싸는 새로운 벡터를 반환한다.
  
<pre class='in'><code>best_practice <- c("Write", "programs", "for", "people", "not", "computers")
asterisk <- "***"  # R interprets a variable with a single value as a vector
                   # with one element.
fence(best_practice, asterisk)</code></pre>



<div class='out'><pre class='out'><code>[1] "***"       "Write"     "programs"  "for"       "people"    "not"      
[7] "computers" "***"      
</code></pre></div>

  + 변수 `v`가 벡터를 참조한다면, `v[1]`은 벡터의 첫번째 요소이고 `v[length(v)]`은 벡터의 마지막 요소가 된다. 함수 `length`는 벡터의 요소 갯수를 반환한다.
  입력값의 첫번째와 마지막 요소로만 구성된 벡터를 반환하는 `outer` 함수를 작성하세요.

<pre class='in'><code>dry_principle <- c("Don't", "repeat", "yourself", "or", "others")
outer(dry_principle)</code></pre>



<div class='out'><pre class='out'><code>[1] "Don't"  "others"
</code></pre></div>

### 콜 스택(Call Stack)

`fahr_to_celsius(32)`을 호출할 때 무엇이 생기는지 좀더 자세히 살펴보자. 좀더 명확하기 하기 위해서, 변수에 초기값을 32로 설정하고 결과를 `final`에 저장해서 출발해봅시다.

<pre class='in'><code>original <- 32
final <- fahr_to_celsius(original)</code></pre>

다음 다이어그램은 첫번째 행이 실행된 다음에 메모리가 어떻게 되지는 보여준다.

<img src="../python/img/python-call-stack-01.svg" alt="Call Stack (Initial State)" />

함수 `fahr_to_celsius`을 호출할 때, R은 변수 `temp`를 바로 생성하지는 *않는다.* 대신에 [스택 프레임(stack frame)](../../gloss.html#stack-frame)을 생성해서 `fahr_to_kelvin`함수가 정의한 변수를 추적한다. 초기에 스택은 `temp` 값만을 가지고 있다.

<img src="../python/img/python-call-stack-02.svg" alt="Call Stack Immediately After First Function Call" />

`fahr_to_celsius` 함수 내부에 `fahr_to_kelvin` 함수를 호출할 때, R은 또 다른 스택 프레임을 생성해서 `fahr_to_kelvin`의 변수를 저장한다.

<img src="../python/img/python-call-stack-03.svg" alt="Call Stack During First Nested Function Call" />

이제 `temp`로 불리는 동작하는 두개의 변수가 있다. 하나는 `fahr_to_celsius` 함수의 인수이고, 다른 하나는 `fahr_to_kelvin` 함수의 인수다. 프로그램의 같은 부분에 동일한 이름을 가진 변수 두개가 있는 것이 애매모호해서, R(그리고 다른 최신 프로그래밍 언어)은 각 함수 호출에 대해서 새로운 스택 프레임을 생성해서 다른 함수에서 정의된 변수와 구별되게 함수의 변수를 보관한다. 

`fahr_to_kelvin` 함수 호출이 값을 반환할 때, R은 `fahr_to_kelvin` 함수의 스택 프레임을 사용한 후 버리고 절대 온도 정보를 보관하기 위해서 `fahr_to_celsius`에 대한 스택 프레임에 새로운 변수를 생성한다. 

<img src="../python/img/python-call-stack-04.svg" alt="Call Stack After Return From First Nested Function Call" />

그리고 나서 `kelvin_to_celsius`을 호출하는데 함수의 변수를 저장할 스택 프레임을 생성한다는 의미다.

<img src="../python/img/python-call-stack-05.svg" alt="Call Stack During Call to Second Nested Function" />

다시 한번, R은 `kelvin_to_celsius` 함수가 수행완료될 때 스택 프레임을 폐기한다. 그리고 `fahr_to_celsius` 함수를 위해 스택 프레임에 `result` 변수를 생성한다.

<img src="../python/img/python-call-stack-06.svg" alt="Call Stack After Second Nested Function Returns" />

마지막으로, `fahr_to_celsius` 함수 수행이 완료될 때, R은 *자신의* 스택 프레임을 폐기하고 초기 시작한 스택 프레임에 있는 신규 변수 `final`에 결과값을 넣는다.

<img src="../python/img/python-call-stack-07.svg" alt="Call Stack After All Functions Have Finished" />

이 마지막 스택 프레임은 항상 존재해서 작성한 코드 중에 함수 외부에서 정의한 변수를 간직한다. 간직하지 않는 것은 다양한 스택 프레임에 있었던 변수다. 만약 함수가 수행 종료된 후에 `temp` 값을 얻고자 한다면, R은 그런 것은 없다고 회답한다.

<pre class='in'><code>temp</code></pre>



<div class='out'><pre class='out'><code>Error: object 'temp' not found
</code></pre></div>

> **Tip:** 상기 스택 프레임(stack frame)의 설명은 매우 일반적이다. 기본 개념이 여러분이 프로그램하는 대부분의 언어를 이해하는데 도움을 줄 것이다. 하지만, 좀더 복잡한 연산을 수행할 때, R은 활용할 몇가지 독특한 점이 있다. 좀더 고급 개념의 지식이 필요한 어떠한 것도 작성하지는 않을 것이다. 향후에 R로 함수를 작성하는 것이 편해질 때 [R Language Manual][man] 혹은 Hadley Wickham의 [Advanced R Programming][adv-r]에서 환경 [장(chapter)][]에서 좀더 많을 것을 배울 수 있다. 프레임(frame) 대신에 "환경(environment)" 용어를 R에서 사용한다.

[man]: http://cran.r-project.org/doc/manuals/r-release/R-lang.html#Environment-objects
[chapter]: http://adv-r.had.co.nz/Environments.html
[adv-r]: http://adv-r.had.co.nz/

왜 이 모든 어려움으로 갈가요? 배열의 최대값과 최소값의 차이를 계산하는 `span`이라는 함수가 다음에 있다.

<pre class='in'><code>span <- function(a) {
  diff <- max(a) - min(a)
  return(diff)
}

dat <- read.csv(file = "inflammation-01.csv", header = FALSE)
# span of inflammation data
span(dat)</code></pre>



<div class='out'><pre class='out'><code>[1] 20
</code></pre></div>

`span` 함수는 값을 `diff` 변수에 할당함을 주목하세요. 염증 데이터 정보를 담고 있는 동일한 이름의 변수(`diff`)를 매우 사용할 수도 있다.

<pre class='in'><code>diff <- read.csv(file = "inflammation-01.csv", header = FALSE)
# span of inflammation data
span(diff)</code></pre>



<div class='out'><pre class='out'><code>[1] 20
</code></pre></div>

함수 호출 뒤에 변수 `diff`가 값 20을 갖게 되는 것을 기대하지 않는다. 그래서 R이 전역 환경(global environment)으로 부르는 프로그램 메인에서 하는 것처럼, `diff` 이름이 `span` 내부에 정의된 동일한 변수를 참조할 수 없다. 이 경우에 변수에 `diff`와 다른 이름을 아마도 선택할 수 있지만, 변수의 값이 변경되는 경우마다 무슨 변수명이 사용되었는지를 살펴보기 위해 호출하는 R함수의 모든 코드 행을 읽고 싶지는 않다.

여기서 기본적인 아이디어는 [캡슐화(encapsulation)](../../gloss.html#encapsulation)이고, 정확하고 이해하기 쉬운 프로그램을 작성하는 열쇠다. 함수가 하는 일은 몇개의 작업을 하나로 변환하는 것이어서 무언가를 하고자 할 때마다 수십개에서 수백개의 문장을 수행하는 대신에 단 하나의 함수 호출을 생각할 수 있다. 함수가 서로에게 간섭하지 않는다면 이 방식은 동작한다. 만약 서로 간섭하게 되면 다시 한번 세부사항에 주의를 기울여야 하고 급격하게 단기 기억에 과부하를 주게된다.   

#### 도전 과제

  + 이전에 `fence`와 `outer` 함수를 작성했다. 다음을 실행할 때 콜 스택(call stack)이 어떻게 변하는지 다이어그램을 그려보세요.

<pre class='in'><code>inside <- "carbon"
outside <- "+"
result <- outer(fence(inside, outside))</code></pre>

### 테스팅과 문서화

함수에 명령어들을 넣어서 재사용할 수 있자마자, 작성한 함수가 제대로 동작하는지를 테스트할 필요가 있다. 어떻게 수행하는지 알아보기 위해서, 특정한 값 주위에 데이터를 중앙에 위치하게 하는 함수를 작성하자.

<pre class='in'><code>center <- function(data, desired) {
  new_data <- (data - mean(data)) + desired
  return(new_data)
}</code></pre>

작성한 함수를 실제 데이터에 테스트할 수도 있으나, 값이 무엇이 되어야하는지 모르기 때문에 결과가 맞는지 구분하기가 어렵다. 대신에, 0으로 구성된 벡터를 생성하고 3 주위가 중심이 되게 하자. 매우 간단하게 만들어서 함수가 기대한 대로 동작하는지 살펴보자.

<pre class='in'><code>z <- c(0, 0, 0, 0)
z</code></pre>



<div class='out'><pre class='out'><code>[1] 0 0 0 0
</code></pre></div>



<pre class='in'><code>center(z, 3)</code></pre>



<div class='out'><pre class='out'><code>[1] 3 3 3 3
</code></pre></div>

맞는 것처럼 보여서 실제 데이터에 중심을 잡도록 하자. 염증 데이터의 4번째 날을 0 주위에 중심을 잡게 한다.


<pre class='in'><code>dat <- read.csv(file = "inflammation-01.csv", header = FALSE)
centered <- center(dat[, 4], 0)
head(centered)</code></pre>



<div class='out'><pre class='out'><code>[1]  1.25 -0.75  1.25 -1.75  1.25  0.25
</code></pre></div>

상기 출력으로부터 결과가 맞는지 구분하기 어렵다. 하지만, 확인을 할 수 있는 몇가지 테스트가 있다.

<pre class='in'><code># original min
min(dat[, 4])</code></pre>



<div class='out'><pre class='out'><code>[1] 0
</code></pre></div>



<pre class='in'><code># original mean
mean(dat[, 4])</code></pre>



<div class='out'><pre class='out'><code>[1] 1.75
</code></pre></div>



<pre class='in'><code># original max
max(dat[, 4])</code></pre>



<div class='out'><pre class='out'><code>[1] 3
</code></pre></div>



<pre class='in'><code># centered min
min(centered)</code></pre>



<div class='out'><pre class='out'><code>[1] -1.75
</code></pre></div>



<pre class='in'><code># centered mean
mean(centered)</code></pre>



<div class='out'><pre class='out'><code>[1] 0
</code></pre></div>



<pre class='in'><code># centered max
max(centered)</code></pre>



<div class='out'><pre class='out'><code>[1] 1.25
</code></pre></div>

거의 맞는 것처럼 보인다. 원 평균은 약 1.75였다. 그래서 0에서 하한은 약 -1.75이다. 중앙값이 바뀐 데이터의 평균은 0이다. 좀더 나아가 표준편차가 바뀌었는지 확인하자.

<pre class='in'><code># original standard deviation
sd(dat[, 4])</code></pre>



<div class='out'><pre class='out'><code>[1] 1.068
</code></pre></div>



<pre class='in'><code># centerted standard deviation
sd(centered)</code></pre>



<div class='out'><pre class='out'><code>[1] 1.068
</code></pre></div>

두 값이 동일하다. 하지만 6번째 소수점에서 차이가 있다면 알아채지 못할 것이다. 대신에 다음을 수행하자.

<pre class='in'><code># difference in standard deviations before and after
sd(dat[, 4]) - sd(centered)</code></pre>



<div class='out'><pre class='out'><code>[1] 0
</code></pre></div>

때때로, 매우 작은 차이를 소수점 아래에서 반올림때문에 탐지할 수 있다. R에는 반올림 오류에 대해서 두 객체를 비교하는 유용한 함수(`all.equal`)가 있다. 

<pre class='in'><code>all.equal(sd(dat[, 4]), sd(centered))</code></pre>



<div class='out'><pre class='out'><code>[1] TRUE
</code></pre></div>

함수가 잘못될 가능성은 여전히 있다. 하지만, 분석으로 되돌릴 정도는 아닐 듯 하다. 하지만, 한 가지 더 작업이 있다. 후에 작성한 함수가 무엇을 위한 것이고 어떻게 사용하는지에 대해서 우리 자신에게 되새김되도록 함수의 [문서화(documentation)](../../gloss.html#documentation)를 위해서 문서를 작성해야 한다.

소프트웨어에 문서를 넣는 일반적인 방법은 다음과 같은 [주석(comments)](../../gloss.html#comment)을 추가하는 것이다.

<pre class='in'><code>center <- function(data, desired) {
  # return a new vector containing the original data centered around the
  # desired value.
  # Example: center(c(1, 2, 3), 0) => c(-1, 0, 1)
  new_data <- (data - mean(data)) + desired
  return(new_data)
}</code></pre>

> **Tip:** R함수의 공식 문서는 [LaTeX][]과 유사한 마크업 언어를 사용해서 별도의 `.Rd` 파일로 작성된다. `?read.csv` 처럼 주어진 함수의 도움말을 살펴볼 때, 문서화 결과를 보게 된다. [roxygen2][] 패키지는 R 코드 작성자가 함수와 문서를 작성할 수 있게 해서 적당한 `.Rd` 파일를 생성한다. 좀더 복잡한 R 프로젝트를 작성할 때, 좀더 공식적인 문서 작성법으로 전환하고 싶을지도 모른다.

[LaTeX]: http://www.latex-project.org/
[roxygen2]: http://cran.r-project.org/web/packages/roxygen2/vignettes/rd.html

#### 도전 과제

  + `analyze` 함수를 작성해서 인자로 파일 이름을 받아서 [앞선 학습][01] 결과(시간에 따른 염증의 평균값, 최소값, 최대값)를 그래프로 화면에 출력하도록 하세요. analyze("inflammation-01.csv") 결과는 이미 보여진 그래프를 생성해야 하지만, `analyze("inflammation-02.csv")` 결과는 두번째 데이터셋에 상응하는 그래프를 생성해야 한다. 주석으로 함수를 문서화하도록 확인하세요.

[01]: 01-starting-with-data.html

  + `rescale`함수를 작성해서 입력값으로 벡터를 받고, 0에서 1사이의 범위로 조정되게 해당 벡터를 반환하게 하세요. 만약 $L$과 $H$이 원 벡터이 하한과 상한이라면, $v$의 치환값은 $(v-L) / (H-L)$이 되어야 한다. 주석으로 함수를 문서화하도록 확인하세요.

  + `min`, `max`, `plot` 함수를 사용해서 `rescale` 함수가 정상적으로 동작하는지 시험하세요.

### 초기 설정(Default) 정의

두가지 방식으로 함수에 인자를 넘겨줬다. `dim(dat)`처럼 직접적으로, `read.csv(file = "inflammation-01.csv", header = FALSE)`과 같이 이름으로 넘겨줬다. 사실 인자를 이름없이 `read.csv` 함수에 넘길 수 있다.

<pre class='in'><code>dat <- read.csv("inflammation-01.csv", FALSE)</code></pre>

하지만, 인자가 이름이 매칭되지 않는다면 인자의 위치가 문제가 된다.

<pre class='in'><code>dat <- read.csv(header = FALSE, file = "inflammation-01.csv")
dat <- read.csv(FALSE, "inflammation-01.csv")</code></pre>



<div class='out'><pre class='out'><code>Error: 'file' must be a character string or connection
</code></pre></div>

무엇이 진행되는지 이해하고 작성한 함수를 사용하기 좀더 쉽게 하기 위해서, 다음과 같이 `center` 함수를 다시 정의하자.

<pre class='in'><code>center <- function(data, desired = 0) {
  # return a new vector containing the original data centered around the
  # desired value (0 by default).
  # Example: center(c(1, 2, 3), 0) => c(-1, 0, 1)
  new_data <- (data - mean(data)) + desired
  return(new_data)
}</code></pre>

중요 변경사항은 두번째 인자가 이제 `desired` 대신에 `desired = 0`이 되었다. 인자 두개로 가진 함수를 호출하면, 전과 동일한 방식으로 동작한다.

<pre class='in'><code>test_data <- c(0, 0, 0, 0)
center(test_data, 3)</code></pre>



<div class='out'><pre class='out'><code>[1] 3 3 3 3
</code></pre></div>

하지만, 단지 하나의 인자로 `center()` 함수를 호출할 수도 있다. 이 경우에 `desired`는 자동적으로 초기 설정값 `0`이 할당된다.

<pre class='in'><code>more_data <- 5 + test_data
more_data</code></pre>



<div class='out'><pre class='out'><code>[1] 5 5 5 5
</code></pre></div>



<pre class='in'><code>center(more_data)</code></pre>



<div class='out'><pre class='out'><code>[1] 0 0 0 0
</code></pre></div>

매우 편리하다. 만약 한 방식으로 동작하는 함수를 원하지만 때때로 다르게 동작할 필요가 있다면, 필요할 때만 인자를 넘기게 하고 정상적인 경우는 좀더 쉽게 하려고 초기 설정값을 넣는다.

다음 예제는 어떻게 R이 값을 인자에 매칭하는지 보여준다.

<pre class='in'><code>display <- function(a = 1, b = 2, c = 3) {
  result <- c(a, b, c)
  names(result) <- c("a", "b", "c")  # This names each element of the vector
  return(result)
}

# no arguments
display()</code></pre>



<div class='out'><pre class='out'><code>a b c 
1 2 3 
</code></pre></div>



<pre class='in'><code># one argument
display(55)</code></pre>



<div class='out'><pre class='out'><code> a  b  c 
55  2  3 
</code></pre></div>



<pre class='in'><code># two arguments
display(55, 66)</code></pre>



<div class='out'><pre class='out'><code> a  b  c 
55 66  3 
</code></pre></div>



<pre class='in'><code># three arguments
display (55, 66, 77)</code></pre>



<div class='out'><pre class='out'><code> a  b  c 
55 66 77 
</code></pre></div>

예제가 보여주듯이, 인자는 왼쪽에서 오른쪽으로 매칭된다. 그리고 명시적으로 값이 주어지지 않는 것은 초기 설정값을 갖는다. 인자를 넘길 때 값에 이름을 줌으로써 이런 행동을 오버라이드(override)할 수 있다.

<pre class='in'><code># only setting the value of c
display(c = 77)</code></pre>



<div class='out'><pre class='out'><code> a  b  c 
 1  2 77 
</code></pre></div>

> **Tip:** 좀더 정확하게 R은 세가지 방식으로 사용자가 넘긴 인자를 함수 정의에 있는 공식 인자와 매칭한다.
>
> 1. 완전한 이름으로 매칭
> 2. 부분 이름으로 매칭 (인자 이름의 첫 *n*개 문자)
> 3. 위치로 매칭
>
> 상기 언급된 *순서*의 방식으로 인자가 매칭된다. 완전한 이름으로 매칭하고, 부분 이름으로 매칭하고, 마지막으로 위치로 매칭한다.

상기 내용을 가지고, `read.csv()` 함수의 도움말을 살펴 보자.

<pre class='in'><code>?read.csv</code></pre>

많은 정보가 있지만, 가장 중요하는 부분은 처음 몇줄이다.

<pre class='in'><code>read.csv(file, header = TRUE, sep = ",", quote = "\"",
         dec = ".", fill = TRUE, comment.char = "", ...)</code></pre>

`read.csv()`는 하나의 인자 `file`만 초기 설정을 갖지 않고, 다른 6개는 초기 설정을 가지는 것을 보여준다. 이제 왜 다음 명령문이 오류를 생성하는지 이해한다.

<pre class='in'><code>dat <- read.csv(FALSE, "inflammation-01.csv")</code></pre>



<div class='out'><pre class='out'><code>Error: 'file' must be a character string or connection
</code></pre></div>

`FALSE`가 `file`에 할당되고, 파일 이름이 `header` 인자에 할당되어 실패한다.

#### 도전 과제

  + `rescale` 함수를 재작성해서 초기 설정으로 벡터를 0에서 1 사이에 놓게 하세요. 하지만 호출자가 원한다면, 하한과 상한을 지정할 수 있게 하세요. 옆 사람과 구현한 것을 비교하세요. 두 함수가 항상 같은 방식으로 동작하나요?

#### 주요점

* `name <- function(...args...)`을 사용해서 함수를 작성하라.
* 함수의 몸통부분은 중괄호(`{}`)로 싸여진다.
* `name(...values...)`을 사용해서 함수를 호출하라.
* 함수가 매번 호출될 때마다, 신규 스택 프레임이 [콜 스택(call stack)](../../gloss.html#call-stack)에 생성되어 인자와 로컬 변수를 가진한다.
* R은 상위 수준에서 변수를 찾기 전에 현재 스택 프레임에서 변수를 찾는다.
* 무언가의 도움말을 보기 위해서 `help(thing)`을 사용한다.
* 함수에 도움말을 제공하려면 함수 시작에 주석을 달아라.
* 코드에 주석을 달아라.
* 인자 목록에 `name = value`을 사용해서 함수를 정의할 때, 인자에 대해서 초기 설정값을 명기하라.
* 인자는 이름, 위치, 혹은 생략하고 넘길 수 있다. 생략하는 경우 초기설정값이 사용된다. 

#### 다음 단계

`analyze` 함수를 가지고 하나의 데이터셋을 시각화할 수 있다. 다음과 같이 12개 데이터넷 모두를 탐색하려고 사용할 수 있다.

<pre class='in'><code>analyze("inflammation-01.csv")
analyze("inflammation-02.csv")
#...
analyze("inflammation-12.csv")</code></pre>

하지만, 12개 파일이름을 모두 정확하게 타이핑할 가능성은 크지 않다. 그리고 만약 백개 파일이 더 있다면 더 난감하게 될 것이다. 필요한 것은 R이 각 파일에 대해서 한번씩 무엇을 수행하게 하는 것이다. 이것이 다음 학습주제다.
