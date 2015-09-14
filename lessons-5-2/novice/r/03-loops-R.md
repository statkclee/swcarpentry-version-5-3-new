---
layout: lesson
root: ../..
---



## 다수의 데이터셋 분석하기

하나의 데이터셋에 대해서 일별 염증율의 최소값, 평균값, 최대값의 그래프를 생성하는 함수 `analyze`를 만들었다.

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
}

analyze("inflammation-01.csv")</code></pre>

<img src="figure/03-loops-R-inflammation-011.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />
<img src="figure/03-loops-R-inflammation-012.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />
<img src="figure/03-loops-R-inflammation-013.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />

작성한 함수를 사용해서 다른 데이터셋도 하나씩 하나씩 분석할 수 있다.

<pre class='in'><code>analyze("inflammation-02.csv")</code></pre>

<img src="figure/03-loops-R-inflammation-021.png" title="plot of chunk inflammation-02" alt="plot of chunk inflammation-02" style="display: block; margin: auto;" />
<img src="figure/03-loops-R-inflammation-022.png" title="plot of chunk inflammation-02" alt="plot of chunk inflammation-02" style="display: block; margin: auto;" />
<img src="figure/03-loops-R-inflammation-023.png" title="plot of chunk inflammation-02" alt="plot of chunk inflammation-02" style="display: block; margin: auto;" />

하지만, 지금 당장 12개의 데이터셋이 있고 앞으로 더많은 데이터가 있을 것이다. 하나의 문장으로 모든 데이터에 대해서 그래프를 생성하고자 한다. 이를 위해서 어떻게 반복하는지를 컴퓨터를 학습시켜야 한다. 

<div class="objectives" markdown="1">
#### 목표

* `for` 루프가 무엇을 수행하는지 설명한다.
* 올바르게 `for` 루프를 작성해서 간단한 계산을 반복한다.
* 루프가 실행될 때, 루프 변수의 변경을 추적한다.
* `for` 루프로 다른 변수가 갱신될 때, 다른 변수의 변경사항도 추적한다.
* 함수를 사용해서 간단한 패턴이 매칭되는 파일 목록을 얻는다.
* `for` 루프를 사용해서 다수의 파일을 처리한다.
</div>

### For 루프

문장에 각 단어를 출력하고자 한다고 가정하자. 한 방법은 6개의 `print` 문을 사용한다.

<pre class='in'><code>best_practice <- c("Let", "the", "computer", "do", "the", "work")
print_words <- function(sentence) {
  print(sentence[1])
  print(sentence[2])
  print(sentence[3])
  print(sentence[4])
  print(sentence[5])
  print(sentence[6])
}

print_words(best_practice)</code></pre>



<div class='out'><pre class='out'><code>[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
[1] "work"
</code></pre></div>

하지만, 두개의 이유로 좋지 못한 접근법이다.

 1. 확장성이 좋지 않다. 만약 100개의 요소를 가진 벡터를 화면에 출력한다면, 단순하게 타이핑하는게 더 좋을 것이다.

 2. 강건하지가 못하다. 만약 조금 더 긴 벡터를 주면 데이터의 일부분만 화면에 출력한다. 만약 조금 짧은 입력을 준다면, `NA` 값을 반환하는데 이유는 존재하지 않는 벡터의 요소값을 요청했기 때문이다.

<pre class='in'><code>best_practice[-6]</code></pre>



<div class='out'><pre class='out'><code>[1] "Let"      "the"      "computer" "do"       "the"     
</code></pre></div>



<pre class='in'><code>print_words(best_practice[-6])</code></pre>



<div class='out'><pre class='out'><code>[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
[1] NA
</code></pre></div>

> **Tip:** R은 `NA` 라는 특수한 변수가 있다. `NA`는 데이터셋의 결측치(**N**ot **A**vailable)를 지정하는 역할을 한다. 좀더 자세한 사항은 `?NA`의 도움말과 [An Introduction to R][na]을 참조바란다.

[na]: http://cran.r-project.org/doc/manuals/r-release/R-intro.html#Missing-values

좀더 좋은 접근법이 다음에 있다.

<pre class='in'><code>print_words <- function(sentence) {
  for (word in sentence) {
    print(word)
  }
}

print_words(best_practice)</code></pre>



<div class='out'><pre class='out'><code>[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
[1] "work"
</code></pre></div>

좀더 짧고 더 강건하다. 100개 문자열의 각 문자를 화면에 출력하는 것보다 확실히 짧다.

<pre class='in'><code>print_words(best_practice[-6])</code></pre>

<div class='out'><pre class='out'><code>[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
</code></pre></div>

`print_words` 함수의 개선된 버젼은 연산을 반복하기 위해서 [for 루프](../../gloss.html#for-loop)를 사용한다. 이 경우에 각각에 대해서 한번만 출력한다. 루프의 일반적인 형태는 다음과 같다.

<pre class='in'><code>for (variable in collection) {
  do things with variable
}</code></pre>

원하는 이름으로 [루프 변수(loop variable)](../../gloss.html#loop-variable)를 할 수 있으나 숫자로 변수 이름을 시작할 수 없다는 예와 같은 몇가지 [제약(restrictions)][]이 있다. `in`은 `for`문을 구성하는 일부다.
루프의 몸통 부문은 중괄호(`{ }`)로 싸여있다. 한줄 루프 몸통 부문은 중괄호가 필요하지 않지만, 루프를 코딩할 때 포함하는 것이 좋은 습관이다.

[restrictions]: http://cran.r-project.org/doc/manuals/R-intro.html#R-commands_003b-case-sensitivity-etc

반복적으로 변수를 갱신하는 또 다른 루프가 있다.

<pre class='in'><code>len <- 0
vowels <- c("a", "e", "i", "o", "u")
for (v in vowels) {
  len <- len + 1
}
# Number of vowels
len</code></pre>



<div class='out'><pre class='out'><code>[1] 5
</code></pre></div>

상기 작은 프로그램의 실행을 단계별로 추적할 가치가 있다. `vowels` 벡터에 5개 요소가 있어서, 루프 내부 문장은 5번 실행된다. 첫번째 루프에서, 첫 행에서 값이 1이 할당되어 `len`은 0이고, `v`는 `"a"`다. `len`의 초기값에 0을 더하고 새로운 값을 나타내는 `len`을 갱신한다. 다음번 루프에서 `v`는 `"e"`이고, `len`은 1이다. 그래서 `len`은 2로 갱신된다. 3번 더 갱신한 후에 `len`은 5가 된다. R이 처리할 더이상의 값이 `vowels` 벡터에 남아있지 않아서, 루프는 종료한다.

루프 변수는 루프의 상태를 기록하기 위해 사용되는 단지 변수에 불과하다. 루프가 종료된 후에도 여전히 존재한다. 또한 루프 변수와 마찬가지로 앞에서 정의한 변수를 재사용할 수 있따.

<pre class='in'><code>letter <- "z"
for (letter in c("a", "b", "c")) {
  print(letter)
}</code></pre>



<div class='out'><pre class='out'><code>[1] "a"
[1] "b"
[1] "c"
</code></pre></div>



<pre class='in'><code># after the loop, letter is
letter</code></pre>



<div class='out'><pre class='out'><code>[1] "c"
</code></pre></div>

벡터의 길이를 찾는 것은 일반적인 연산이라 R은 `length`로 불리는 내장 함수가 있음을 주목하세요.

<pre class='in'><code>length(vowels)</code></pre>



<div class='out'><pre class='out'><code>[1] 5
</code></pre></div>

`length` 함수는 여러분이 스스로 작성하는 다른 어떤 R 함수보다도 훨씬 빠르고 두줄 루프보다 훨씬 읽기 쉽다. `length`함수를 사용해서 아직 만나보지 못한 많은 다른 것의 길이도 알 수 있다. 그런 이유로 사용할 수 있다면 `length` 함수를 항상 사용해야 한다. R에 데이터를 저장하는 다른 방법에 대해서 더 많이 학습하고자 한다면 링크된 [학습(lesson)](00-first-timers.html)을 클릭하세요.

#### 도전 과제

1. R에는 숫자 리스트를 생성하는 `seq` 내장 함수가 있다. 

<pre class='in'><code>seq(3)</code></pre>



<div class='out'><pre class='out'><code>[1] 1 2 3
</code></pre></div>

`seq` 함수를 사용해서 한줄마다 하나씩 첫 **N** 개의 숫자를 화면에 출력하는 함수를 작성하세요.

<pre class='in'><code>print_N(3)</code></pre>



<div class='out'><pre class='out'><code>[1] 1
[1] 2
[1] 3
</code></pre></div>

2. 누승(Exponentiation)은 R의 내장함수다.

<pre class='in'><code>2^4</code></pre>



<div class='out'><pre class='out'><code>[1] 16
</code></pre></div>

동일한 결과를 계산하기 위해서 루프를 사용하는 `expo` 함수를 작성하세요.

<pre class='in'><code>expo(2, 4)</code></pre>



<div class='out'><pre class='out'><code>[1] 16
</code></pre></div>

3. 벡터 값의 합을 계산하는 `total` 함수를 작성하세요. R에는 내장함수 `sum`이 있어서 동일한 계산을 대신할 수 있지만, 이번 도전과제에는 사용하지 마세요.


<pre class='in'><code>ex_vec <- c(4, 8, 15, 16, 23, 42)
total(ex_vec)</code></pre>



<div class='out'><pre class='out'><code>[1] 108
</code></pre></div>

### 다수 파일 처리하기
이제 모든 데이터 파일의 처리에 필요한 거의 모든 것을 갖추었다. 빠진 단 한가지는 파일 이름을 패턴과 매칭하여 파일을 찾아내는 함수다. 이를 위해서 함수를 작성할 필요는 없는데 이미 R에는 `list.files` 함수가 존재한다.

어떤 인자도 없이 `list.files()` 함수를 실행하게 되면, 현재 작업 디렉토리의 모든 파일을 반환한다. `?list.files()` 도움말 파일을 읽어서 결과를 이해할 수 있다. 첫번째 인자, `path`는 검색되는 디렉토리의 경로이고 초기 설정값은 `"."`이다. 유닉스 쉘을 [학습](../shell/01-filedir.html)할 때, `"."`은 현재 작업 디렉토리를 짧게 나타낸다는 것을 배웠다. 두번째 인자는, `pattern`으로 검색되는 패턴을 나타내고 초기 설정값은 `NULL`이다. 어떠한 패턴도 파일을 거르도록 지정되지 않아서 모든 파일이 반환된다.

모든 csv 파일 목록을 출력하기 위해서, 다음 중 하나를 실행한다.

<pre class='in'><code>list.files(pattern = "csv")</code></pre>

<div class='out'><pre class='out'><code> [1] "inflammation-01.csv" "inflammation-02.csv" "inflammation-03.csv"
 [4] "inflammation-04.csv" "inflammation-05.csv" "inflammation-06.csv"
 [7] "inflammation-07.csv" "inflammation-08.csv" "inflammation-09.csv"
[10] "inflammation-10.csv" "inflammation-11.csv" "inflammation-12.csv"
</code></pre></div>



<pre class='in'><code>list.files(pattern = "inflammation")</code></pre>



<div class='out'><pre class='out'><code> [1] "inflammation-01.csv" "inflammation-02.csv" "inflammation-03.csv"
 [4] "inflammation-04.csv" "inflammation-05.csv" "inflammation-06.csv"
 [7] "inflammation-07.csv" "inflammation-08.csv" "inflammation-09.csv"
[10] "inflammation-10.csv" "inflammation-11.csv" "inflammation-12.csv"
</code></pre></div>

인자의 이름 `pattern`을 명기해야 되는데 첫 인자가 `path`로 초기 설정값으로 사용되고 있어서 그렇다. 함수의 초기 설정 인자에 대해서 앞에 [학습(함수)](02-func-R.html)를 참조바란다.

> **Tip:** 상기 예제는 작기 때문에, 동일한 디렉토리에 데이터와 코드가 있다. 좀더 큰 프로젝트에서는 분석의 각 부분별로 다수의 하위 디렉토리 구조를 가지도록 추전한다. 예를 들어, 원 데이테를 가지는 하위 디렉토리, 코드만 담고 있는 하위 디렉토리, 그림같은 결과를 담고 있는 디렉토리. 이 주제에 대해서 좀더 많은 조언은 William Stafford Noble이 작성한 [A quick guide to organizing computational biology projects][Noble2009]을 참조바란다.

[Noble2009]: http://www.ploscompbiol.org/article/info%3Adoi%2F10.1371%2Fjournal.pcbi.1000424

상기 예제가 보여주듯이, `list.files` 결과는 문자열 벡터로 각 파일이름에 대해서 뭔가를 하기 위해서 루프를 반복해야 한다는 것이다. 지금 사례의 경우, 작업하려는 것은 `analyze` 함수다. 벡터의 첫 3개 파일을 분석해서 시험해보자.

<pre class='in'><code>filenames <- list.files(pattern = "csv")
filenames <- filenames[1:3]
for (f in filenames) {
  print(f)
  analyze(f)
}</code></pre>



<div class='out'><pre class='out'><code>[1] "inflammation-01.csv"
</code></pre></div>

<img src="figure/03-loops-R-loop-analyze1.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="figure/03-loops-R-loop-analyze2.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="figure/03-loops-R-loop-analyze3.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" />

<div class='out'><pre class='out'><code>[1] "inflammation-02.csv"
</code></pre></div>

<img src="figure/03-loops-R-loop-analyze4.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="figure/03-loops-R-loop-analyze5.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="figure/03-loops-R-loop-analyze6.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" />

<div class='out'><pre class='out'><code>[1] "inflammation-03.csv"
</code></pre></div>

<img src="figure/03-loops-R-loop-analyze7.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="figure/03-loops-R-loop-analyze8.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="figure/03-loops-R-loop-analyze9.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" />

물론, 데이터셋의 최대값은 처음과 동일한 경사를 보여주고, 최소값은 동일한 계단구조를 보여준다.

> **Tip:** 이번 학습에서 연산을 반복하기 위해서 간단한 `for` 루프를 어떻게 사용하는지 살펴봤다. R로 진행을 하면서, 작업을 수행하는데 다양한 방법이 있다는 것을 배울 것이다. 때때로 다른 방법에 대한 한 방법을 선택하는 것은 좀더 개인 취향의 문제지만, 다른 경우에 코드의 속도 차이를 가져오는 결과가 되기도 한다. 모범 사례로, R의 연산을 어떻게 반복하는지 시범을 보여주는 [보충 학습](03-supp-loops-in-depth.html)을 참조바랍니다.

#### 도전 과제

1. 파일 이름 패턴을 인자로만 받는 `analyze_all` 함수를 작성해서 파일 이름과 패턴이 매칭되는 파일에 대해서 `analyze`를 실행하세요.

<div class="keypoints" markdown="1">
#### 주요점

* `for (variable in collection)`를 사용해서 한번에 하나씩 요소를 처리하세요.
* `for` 루프의 몸통 부분은 중괄호(`{ }`)로 감싸져 있다.
* 다른 값을 담고 있는 것의 길이를 알기 위해서 `length(thing)`을 사용하세요.
* `list.files(pattern = "pattern")`을 사용해서 파일 이름과 패턴이 매칭되는 파일 목록을 생성하세요.
</div>

#### 다음 단계

이제 원래 문제를 해결했다. 단일 명령문으로 임의 데이터 파일을 분석할 수 있다. 좀더 중요한 것은 프로그래밍에서 가장 중요한 두가지 아이디어를 경험했다.

* 함수를 사용해서 코드를 재사용하기 쉽게하고 이해하기 쉽게 했다.
* 벡터와 데이터프레임을 사용해서 관련된 값을 저장했고, 루프를 사용하여 데이터에 대한 연산을 반복했다.

한가지 더 소개할 큰 아이디가 있다.
