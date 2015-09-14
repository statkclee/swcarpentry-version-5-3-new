---
layout: lesson
root: ../..
---



## 조건 선택

앞선 학습에서 어떻게 데이터를 다루고, 함수를 정의하고, 반복하는지를 배웠다. 하지만, 지금까지 작성한 프로그램은 무슨 데이터가 주어지든지 관계없이 항상 동일한 것을 수행한다. 프로그램이 다루는 값에 기반하여 선택하도록 만들고 싶다.

<div class="objectives" markdown="1">
#### 목표

*   pdf 파일로 플롯(plot)을 저장한다.
*   `if`와 `else`로 구성된 조건문을 작성한다.
*   `&` ("and")와 `|` ("or")를 포함하는 표현식을 올바르게 평가한다.
</div>

### 플롯을 파일로 저장하기

지금까지 `analyze` 함수를 작성해서 염증 데이터의 요약 통계를 플롯했다. 

<pre class='in'><code>analyze <- function(filename) {
  # Plots the average, min, and max inflammation over time.
  # Input is character string of a csv file.
  dat <- read.csv(file = filename, header = FALSE)
  avg_day_inflammation <- apply(dat, 2, mean)
  plot(avg_day_inflammation)
  max_day_inflammation <- apply(dat, 2, max)
  plot(max_day_inflammation)
  min_day_inflammation <- apply(dat, 2, min)
  plot(min_day_inflammation)
}</code></pre>

또한 `analyze_all` 함수를 작성해서 각 파일에 대한 처리를 자동화했다.

<pre class='in'><code>analyze_all <- function(pattern) {
  # Runs the function analyze for each file in the current working directory
  # that contains the given pattern.
  filenames <- list.files(pattern = pattern)
  for (f in filenames) {
    analyze(f)
  }
}</code></pre>

앞에서 접근방법이 인터랙티브 R 세션에서 유용하지만, 만약 결과를 공동 연구자에게 보낸다면 어떨까요? 현재 12개 데이터셋이 있기 때문에, `analyze_all`을 실행하면 36개의 플롯이 생성된다. 개별적으로 각각의 플롯을 저장하는 것은 지루하고 실수하기 싶다. 그리고, 데이터 처리절차를 변경하거나 플롯의 모양과 느낌을 변경하는 상황에서는 동료 연구자와 갱신된 결과를 공유하기 전에 다시 한번 36개 플롯 모두를 저장해야한다.

pdf 파일에 첫 염증 데이터의 플롯 3개를 어떻게 저장하는지 다음에 보여준다.

<pre class='in'><code>pdf("inflammation-01.pdf")
analyze("inflammation-01.csv")
dev.off()</code></pre>

`pdf` 함수는 R에서 생성된 모든 플롯을 pdf 파일로 다시 보낸다(redirect). 상기 경우에 플롯을 "inflammation-01.pdf"로 명명된 파일에 저장한다. pdf 파일에 저장될 플롯 생성을 완료한 후에, `dev.off` 함수로 R이 더 이상 플롯을 다시 보내기(redirect) 못하게 정지한다.

> **Tip:** `dev.off` 실행하지 않고 `pdf`함수를 여러번 실행한다면, 가장 최근에 열린 파일에 플롯을 저장할 것이다. 하지만, 연결(connection)이 닫혀지지 않아서 이전 pdf 파일을 열 수는 없을 것이다. 이 상황에서 벗어나기 위해서, 모든 pdf 연결(connection)을 닫을 때, `dev.off`를 실행할 필요가 있다. `dev.cur` 함수를 사용해서 현재 상태를 확인할 수 있다. 만일 "pdf"라고 R이 화면에 뿌려준다면, 모든 플롯은 마지막에 지정된 pdf 파일에 저장된다. 만약 "null device" 혹은 "RStudioGD"라면, 정상적으로 플롯이 그려질 것이다.

`analyze` 함수를 갱신해서 pdf 파일에 플롯을 항상 저장할 수 있다. 하지만, 새로운 변경을 인터랙티브하게 시험하는 것은 좀더 어렵다. 입력에 따라 `analyze`가 플롯을 저장하거나 혹은 저장하지 않게 만드는 것이 이상적이다.

### 조건문(Conditionals)

저장할지 저장하지 않을지 결정하는 함수를 갱신하기 위해서 다중 선택사항 중에서 자동으로 결정을 하는 코드를 작성할 필요가 있다. R이 이를 위해서 제공하는 도구는 [조건문(conditional statement)](../../gloss.html#conditional-statement)이라고 불리고, 다음과 같다.

<pre class='in'><code>num <- 37
if (num > 100) {
  print("greater")
} else {
  print("not greater")
}
print("done")</code></pre>



<div class='out'><pre class='out'><code>[1] "not greater"
</code></pre></div>



<div class='out'><pre class='out'><code>[1] "done"
</code></pre></div>

코드이 두번째 줄이 `if`문을 사용하여 R에게 사용자가 선택을 하고 싶다고 전한다. 만약 다음 시험(test)가 참(true)이면, `if`문의 몸통부분(아래 중괄호로 둘러싸인 행들)이 실행된다. 만약 시험이 거짓(false)이면, `else`의 몸통부분이 대신 실행된다. 오직 하나와 다른 것만이 실행된다.

<img src="../python/img/python-flowchart-conditional.svg" alt="Executing a Conditional" />

상기 예제에서 `num > 100` 시험은 `FALSE` 값을 반환하는데 이것이 왜 `if` 블록 내부 코드를 건너뛰고, `else` 문 내부 코드가 대신 실행된 이유다.

<pre class='in'><code>num > 100</code></pre>



<div class='out'><pre class='out'><code>[1] FALSE
</code></pre></div>

그리고 유추할 수 있듯이, `FALSE`의 반대는 `TRUE`다.

<pre class='in'><code>num < 100</code></pre>



<div class='out'><pre class='out'><code>[1] TRUE
</code></pre></div>

조건문이 반드시 `else`를 포함할 필요는 없다. 만약 `else`가 없다면, R은 단순하게 시험조건이 거짓이면 아무것도 실행하지 않는다.

<pre class='in'><code>num <- 53
if (num > 100) {
  print("num is greater than 100")
}</code></pre>

또한, 두가지 이상의 선택사항이 있다면, 몇개의 시험을 연쇄적으로 할 수 있다. 이렇게 하는 것이 숫자부호를 반환하는 함수 작성을 단순하게 한다.

<pre class='in'><code>sign <- function(num) {
  if (num > 0) {
    return(1)
  } else if (num == 0) {
    return(0)
  } else {
    return(-1)
  }
}

sign(-3)</code></pre>



<div class='out'><pre class='out'><code>[1] -1
</code></pre></div>



<pre class='in'><code>sign(0)</code></pre>



<div class='out'><pre class='out'><code>[1] 0
</code></pre></div>



<pre class='in'><code>sign(2/3)</code></pre>



<div class='out'><pre class='out'><code>[1] 1
</code></pre></div>

동치 시험은 두 개의 등호 기호(`==`)를 사용한다.

> **Tip:** 다른 시험은 크거나 같다(`>=`), 작거나 같다(`<=`), 같지 않다.(`!=`)가 있다.

시험(test)를 조합할 수도 있다. 앰퍼샌드(`&`)는 "and", 수직 막대(`|`)는 "or"를 상징한다. `&`은 양쪽이 모두 참일 때만 참이다.

<pre class='in'><code>if (1 > 0 & -1 > 0) {
    print("both parts are true")
} else {
  print("at least one part is not true")
}</code></pre>



<div class='out'><pre class='out'><code>[1] "at least one part is not true"
</code></pre></div>

반면에 `|`은 둘 중에 하나만 참이면 참이다.

<pre class='in'><code>if (1 > 0 | -1 > 0) {
    print("at least one part is true")
} else {
  print("neither part is true")
}</code></pre>



<div class='out'><pre class='out'><code>[1] "at least one part is true"
</code></pre></div>

이 경우, 둘 중에 하나는 둘중 하나 혹은 둘다를 의미한다. 하지만, 이쪽이나 저쪽 혹은 둘다 아니다를 의미하지는 않는다.

<div class="challenges" markdown="1">
#### 도전 과제

  + `plot_dist` 함수를 작성해서 벡터의 길이가 지정한 임계치보다 크다면 박스플롯(boxplot)을 그리고 그렇지 않으면 스트립차트(stripchart)를 그리게 하세요. 함수를 작성하기 위해서 R함수 `boxplot`과 `stripchart`을 사용하세요.

<pre class='in'><code>dat <- read.csv("inflammation-01.csv", header = FALSE)
plot_dist(dat[, 10], threshold = 10)  # day (column) 10</code></pre>

<img src="figure/04-cond-conditional-challenge1.png" title="plot of chunk conditional-challenge" alt="plot of chunk conditional-challenge" style="display: block; margin: auto;" />

<pre class='in'><code>plot_dist(dat[1:5, 10], threshold = 10)  # samples (rows) 1-5 on day (column) 10</code></pre>

<img src="figure/04-cond-conditional-challenge2.png" title="plot of chunk conditional-challenge" alt="plot of chunk conditional-challenge" style="display: block; margin: auto;" />
</div>

### 자동 생성된 그림 저장하기

입력값에 R이 어떻게 결정을 내리는지를 알기 때문에, 함수 `analyze`를 갱신하자.

<pre class='in'><code>analyze <- function(filename, output = NULL) {
  # Plots the average, min, and max inflammation over time.
  # Input:
  #    filename: character string of a csv file
  #    output: character string of pdf file for saving
  if (!is.null(output)) {
    pdf(output)
  }
  dat <- read.csv(file = filename, header = FALSE)
  avg_day_inflammation <- apply(dat, 2, mean)
  plot(avg_day_inflammation)
  max_day_inflammation <- apply(dat, 2, max)
  plot(max_day_inflammation)
  min_day_inflammation <- apply(dat, 2, min)
  plot(min_day_inflammation)
  if (!is.null(output)) {
    dev.off()
  }
}</code></pre>

인자 `output`을 추가해서 초기값을 `NULL`로 설정했다. 처음에 `if`문이 `output` 인자를 확인하고 플롯을 pdf에 저장할지 하지 않을지를 결정한다. 좀더 자세히 하나씩 살펴보자. 변수가 `NULL`이면, `is.null` 함수는 `TRUE`를 반환하고 그렇지 않으면 `FALSE`를 반환한다. 느낌표 `!`은 "반대(not)"을 나타낸다. 그러므로, `if` 블록에 행은 `output`이 "not null"인 경우만 실행된다.

<pre class='in'><code>output <- NULL
is.null(output)</code></pre>



<div class='out'><pre class='out'><code>[1] TRUE
</code></pre></div>



<pre class='in'><code>!is.null(output)</code></pre>



<div class='out'><pre class='out'><code>[1] FALSE
</code></pre></div>

이제 인터랙티브하게 `analyze` 함수를 양쪽 다 사용할 수 있다.

<pre class='in'><code>analyze("inflammation-01.csv")</code></pre>

<img src="figure/04-cond-inflammation-011.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" /><img src="figure/04-cond-inflammation-012.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" /><img src="figure/04-cond-inflammation-013.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />

플롯을 저장할 수도 있다.

<pre class='in'><code>analyze("inflammation-01.csv", output = "inflammation-01.pdf")</code></pre>

한번에 하나의 데이터 파일을 처리할 때 이제는 잘 동작한다. 하지만, 함수 `analyze_all`의 출력 파일을 어떻게 지정할 수 있을까? 파일이름 "csv" 확장자를 "pdf"로 대체할 필요가 있다. 이를 위해서 `sub` 함수를 사용한다.

<pre class='in'><code>f <- "inflammation-01.csv"
sub("csv", "pdf", f)</code></pre>



<div class='out'><pre class='out'><code>[1] "inflammation-01.pdf"
</code></pre></div>

이제 `analyze_all` 함수를 갱신하자.

<pre class='in'><code>analyze_all <- function(pattern) {
  # Runs the function analyze for each file in the current working directory
  # that contains the given pattern.
  filenames <- list.files(pattern = pattern)
  for (f in filenames) {
    pdf_name <- sub("csv", "pdf", f)
    analyze(f, output = pdf_name)
  }
}</code></pre>

이제는 모든 출력 결과를 단 한줄의 코드로 저장할 수 있다.

<pre class='in'><code>analyze_all("csv")</code></pre>

이제 만약 분석에 어떤 변경을 할 필요가 있다면, `analyze` 함수를 편집해서, 재빨리 모든 그림을 `analzye_all` 함수로 다시 생성할 수 있다.

<div class="challenges" markdown="1">
#### 도전 과제

  + 동료중의 한명이 점 대신에 선으로 그림을 다시 생성할 수 있는지 요청한다. `?plot`을 사용해서 도움말을 문서를 읽고 `plot`함수의 관련된 인자를 찾아서, `analyze`함수를 갱신하고, `analyze_all`함수로 모든 그림을 다시 생성하세요.


</div>

<div class="keypoints" markdown="1">
#### 주요점

*   `pdf("name.pdf")`를 사용하여 pdf 파일에 플롯에 저장하고 `dev.off()` 명령어로 pdf파일에 더이상 쓰기를 멈추게 하세요.
*   `if (condition)`를 사용해서 조건문을 시작하고, `else if (condition)`로 추가 시험조건을 달고, `else`로 디폴트 초기 설정을 하세요.
*   조건문의 몸통부분은 중괄호(`{ }`)로 둘러싼다.
*   동치를 시험하기 위해서 `==`을 사용한다.
*   `X & Y`은 X와 Y 모두가 참일때만 참이다.
*   `X | Y`은 X 혹은 Y가 참이거나, 둘다 참인 경우 참이다.
</div>

#### 다음 단계

지금 인터랙티브하게 R 코드를 작성하는 기초를 봤다. 마지막 학습을 통해서 파이프라인과 쉘 스크립트에서 사용한 명령어-라인 프로그램을 어떻게 작성하지 배울 것이다. 그렇게 함으로써 다른 사람의 작업과 통합할 수 있다. 이것이 다음 학습의 주제이고 마지막 수업이 된다.