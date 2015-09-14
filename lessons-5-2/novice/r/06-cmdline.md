---
layout: lesson
root: ../..
---



## 명령어-라인(Command-Line) 프로그램

R콘솔이나 RStudio 같은 인터랙티브 개발 툴은 코드 프로토타이핑(Prototyping)이나 데이터 탐색에는 훌륭하다. 하지만 조만간 파이프라인(pipeline)에 프로그램을 사용하고 수천개의 데이터 파일을 처리하려고 쉘 스크립트를 실행하고 싶을 때가 있다. 이를 위해서 유닉스 명령어-라인 도구와 함께 작업하도록 프로그램을 만들 필요가 있다. 예를 들어, 데이터셋을 읽어들이고 환자마다 평균 염증을 출력하는 프로그램을 작성할 필요가 있다.

~~~
$ Rscript readings.R --mean inflammation-01.csv
5.45
5.425
6.1
...
6.4
7.05
5.9
~~~

하지만, 첫 4개 줄의 최소값만을 보고 싶을지도 모른다.

~~~
$ head -4 inflammation-01.csv | Rscript readings.R --min
~~~

혹은 몇개 파일에서 하나씩 최대 염증값을 보고 싶다.

~~~
$ Rscript readings.R --max inflammation-*.csv
~~~

요구사항은 대략 다음과 같다.

1. 명령-라인에 파일 이름이 주어지지 않는다면, 데이터를 [표준입력(standard input)](../../gloss.html#standard-input)에서 불러온다.
2. 하나 혹은 그 이상의 파일 이름이 주어진다면, 파일 이름에서 데이터를 읽고, 각 파일별로 통계량을 보고한다.
3. 무슨 통계량을 출력할지를 `--min`, `--mean`, `--max` 플래그를 사용해서 결정한다.

상기 요구사항을 만족시키기 위해서, 프로그램에서 어떻게 명령어-라인 인자를 처리하는지와 표준 입력에서 어떻게 받아들이지 알 필요가 있다. 이런 질문을 아래에서 차례로 다룬다.

<div class="objectives" markdown="1">
#### 목표

*   프로그램의 명령어-라인 인자값을 사용한다.
*   명령어-라인 프로그램에서 별도로 플래그와 파일을 처리한다.
*   프로그램의 표준 입력에서 데이터를 읽어들여 파이프라인에서 사용될 수 있게 한다.
</div>

### 명령어-라인(Command-Line) 인자

여러분이 선택한 텍스트 편집기를 사용하여 `session-info.R` 텍스트 파일에 다음 코드를 저장하세요.

<div class='out'><pre class='out'><code>sessionInfo()
</code></pre></div>

`sessionInfo` 함수는 실행되는 R 버젼과 사용하고 있는 컴퓨터 형식, 그리고 적재된 패키지 버젼을 화면에 출력한다.
`sessionInfo` 출력정보는 다른 사람들에게 여러분이 작성한 R 코드에 대한 도움을 요청할 때 매우 유용한 정보를 포함하고 있다.

이제 `Rscript`를 사용하여 유닉스 쉘에서 생성한 파일 코드를 실행할 수 있다.

<pre class='in'><code>Rscript session-info.R</code></pre>




<div class='out'><pre class='out'><code>R version 3.1.1 (2014-07-10)
Platform: x86_64-pc-linux-gnu (64-bit)

locale:
 [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
 [3] LC_TIME=en_US.UTF-8        LC_COLLATE=en_US.UTF-8    
 [5] LC_MONETARY=en_US.UTF-8    LC_MESSAGES=en_US.UTF-8   
 [7] LC_PAPER=en_US.UTF-8       LC_NAME=C                 
 [9] LC_ADDRESS=C               LC_TELEPHONE=C            
[11] LC_MEASUREMENT=en_US.UTF-8 LC_IDENTIFICATION=C       

attached base packages:
[1] stats     graphics  grDevices utils     datasets  base     
</code></pre></div>

> **Tip:** 만약 정상적으로 동작하지 않는다면, 옳은 디렉토리에 있어야된다는 것을 기억하라. `pwd` 명령어를 사용하여 현재 어느 디렉토리에 있는지 확인할 수 있고, `cd` 명령어를 사용하여 다른 디렉토리로 변경할 수 있다. 다시 회고하기 위해서 [쉘 학습](../shell/01-filedir.html) 혹은 [Unix Shell Reference](../ref/01-shell.html)를 참고바란다.

좀더 흥리로운 작업을 수행하는 또다른 스크립트를 생성하자. `print-args.R` 파일에 다음 행을 작성하고 저장한다.

<div class='out'><pre class='out'><code>args <- commandArgs()
cat(args, sep = "\n")
</code></pre></div>

`commandArgs` 함수는 모든 명령어 라인 인자를 뽑아내서 벡터로 반환한다. 유닉스 쉘의 `cat`와 유사한 함수 `cat`는 변수의 내용을 출력한다. 출력결과를 쓸 파일이름을 명기하지 않아서 `cat`는 출력결과를 [표준 출력(standard output)](../../gloss.html#standard-output)으로 보낸다. 그렇게 해서 다른 유닉스 함수에 출력결과를 보낼 수 있다. `sep` 인자를 개행(new line) 기호인 `"\n"`을 설정했기 때문에, 벡터의 각 요소는 새로운 행에 출력된다. 유닉스 쉘에서 이 프로그램을 실행할 때, 무슨 일이 생기는지 살펴보자.

<pre class='in'><code>Rscript print-args.R</code></pre>




<div class='out'><pre class='out'><code>/usr/lib/R/bin/exec/R
--slave
--no-restore
--file=print-args.R
--args
</code></pre></div>

상기 출력결과로부터 `Rscript`는 R 스크립트를 실행하는 단지 편의 명령문이라는 것을 배웠다. 벡터의 첫 인자는 `R` 실행파일의 경로다. R 행동에 영향을 주는 모든 명령어-라인 인자는 다음과 같다. R 도움말 파일로부터...

*  `--slave`: R을 가능하면 조용하게 실행하게 한다.
*  `--no-restore`: R 세션에서 생성된 어떤 것도 복원하지 않는다.
*  `--file`: 이 파일을 실행한다.
*  `--args`: 다음 인자를 실행될 파일에 전달한다.

Rscript로 파일을 실행하는 것이 다음을 실행하는 좀더 쉬운 방법이다.

<pre class='in'><code>R --slave --no-restore --file=print-args.R --args</code></pre>




<div class='out'><pre class='out'><code>/usr/lib/R/bin/exec/R
--slave
--no-restore
--file=print-args.R
--args
</code></pre></div>

하지만, 만약 몇개 인자로 실행한다면,

<pre class='in'><code>Rscript print-args.R first second third</code></pre>




<div class='out'><pre class='out'><code>/usr/lib/R/bin/exec/R
--slave
--no-restore
--file=print-args.R
--args
first
second
third
</code></pre></div>

`commandArgs`는 반환하는 벡터에 각각의 인자를 추가한다. 벡터의 첫 요소는 항상 동일하기 때문에, `--args` 뒤에만 오는 인자를 반환하도록 `commandArgs`에 분부할 수 있다. `print-args.R`을 갱신하고 `print-args-trailing.R`으로 저장하자.

<div class='out'><pre class='out'><code>args <- commandArgs(trailingOnly = TRUE)
cat(args, sep = "\n")
</code></pre></div>

그리고 나서 유닉스 쉘에서 `print-args-trailing`을 실행하자.

<pre class='in'><code>Rscript print-args-trailing.R first second third</code></pre>




<div class='out'><pre class='out'><code>first
second
third
</code></pre></div>

이제 `commandArgs`는  `print-args-trailing.R` 다음에 목록으로 나열한 인자만을 반환한다.

이것을 가지고, `readings.R` 버젼을 작성해서 하나의 데이터 파일의 환자별(행별) 평균을 항상 출력하게 하자. 첫번째 단계는 구현의 개요를 잡는 함수와 실제 작업을 하는 함수에 대한 자리 표시자(placeholder)를 작성하는 것이다. 관례로 함수를 통상 `main`으로 부른다. 하지만 원하는 임의의 이름지을 수도 있다. 신규로 readings-01.R` 파일을 생성하여 다음 코드를 작성하세요.

<div class='out'><pre class='out'><code>main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  filename <- args[1]
  dat <- read.csv(file = filename, header = FALSE)
  mean_per_patient <- apply(dat, 1, mean)
  cat(mean_per_patient, sep = "\n")
}
</code></pre></div>

상기 함수는 처리할 파일의 이름을 `commandArgs`에서 반환되는 첫 요소로 받는다. 유닉스 쉘에서 수행하는 간단한 시험이 다음에 있다.

<pre class='in'><code>Rscript readings-01.R inflammation-01.csv</code></pre>

출력되는 것은 아무것도 없는데 이유는 함수를 정의했지만, 실제로 호출하지는 않았다. `main`에 호출을 추가하고 `readings-02.R`로 파일을 저장하자.

<div class='out'><pre class='out'><code>main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  filename <- args[1]
  dat <- read.csv(file = filename, header = FALSE)
  mean_per_patient <- apply(dat, 1, mean)
  cat(mean_per_patient, sep = "\n")
}

main()
</code></pre></div>


<pre class='in'><code>Rscript readings-02.R inflammation-01.csv</code></pre>




<div class='out'><pre class='out'><code>5.45
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

#### 도전 과제

  + 덧셈과 뺄셈을 수행하는 명령어-라인 프로그램을 작성하세요. **힌트:** 명령어-라인에서 읽혀지는 모든 인자는 [문자열(string)](../../gloss.html#string)로 해석된다. `as.numeric` 함수를 사용해서 문자열을 숫자로 변환한다.

<pre class='in'><code>Rscript arith.R 1 + 2</code></pre>




<div class='out'><pre class='out'><code>3
</code></pre></div>


<pre class='in'><code>Rscript arith.R 3 - 4</code></pre>




<div class='out'><pre class='out'><code>-1
</code></pre></div>



  + 만약 프로그램에 `*`을 사용해서 곱하기를 추가한다면 무엇이 잘못될까요?
  


  + 이전 [루프 학습](03-loops-R.html)에서 소개된 `list.files` 함수를 사용해서 특정한 패턴을 가진 모든 파일을 현재 디렉토리에서 목록으로 나열하는 명령문-라인 프로그램을 작성하세요.

<pre class='in'><code>Rscript find-pattern.R inflammation</code></pre>




<div class='out'><pre class='out'><code>inflammation-01.csv
inflammation-01.pdf
inflammation-02.csv
inflammation-02.pdf
inflammation-03.csv
inflammation-03.pdf
inflammation-04.csv
inflammation-04.pdf
inflammation-05.csv
inflammation-05.pdf
inflammation-06.csv
inflammation-06.pdf
inflammation-07.csv
inflammation-07.pdf
inflammation-08.csv
inflammation-08.pdf
inflammation-09.csv
inflammation-09.pdf
inflammation-10.csv
inflammation-10.pdf
inflammation-11.csv
inflammation-11.pdf
inflammation-12.csv
inflammation-12.pdf
</code></pre></div>



### 다수 파일 처리하기

다음 단계는 프로그램에게 파일 다수를 어떻게 처리하는지 가르치는 것이다. 파일당 60줄의 출력결과는 페이지를 넘기며 살펴보기에는 많은 불량이여서 3개의 작은 파일로 시작한다. 작은 파일 각각은 두 환자에 대한 3일치 데이터가 있다. 유닉스 쉘에서 작은 파일을 살펴보자.

<pre class='in'><code>ls small-*.csv</code></pre>




<div class='out'><pre class='out'><code>small-01.csv
small-02.csv
small-03.csv
</code></pre></div>


<pre class='in'><code>cat small-01.csv</code></pre>




<div class='out'><pre class='out'><code>0,0,1
0,1,2
</code></pre></div>


<pre class='in'><code>Rscript readings-02.R small-01.csv</code></pre>




<div class='out'><pre class='out'><code>0.3333333
1
</code></pre></div>

작은 파일을 입력값으로 사용하는 것은 좀더 쉽게 결과를 확인할 수 있게 한다. 예를 들어, 프로그램이 각 행마다 올바르게 평균을 계산하는지 살펴볼 수 있다. 반면에 전에는 정말 믿음으로만 가지고 있었다. 이것은 또 다른 프로그래밍 규칙이다. ("[간단한 것을 먼저 시험하라(test the simple things first)](../../rules.html#test-simple-first)")

작성한 프로그램이 각각의 파일을 개별로 처리하길 원해서 각 파일 이름마다 한번씩 실행되는 루프가 필요하다. 명령어 라인에 파일 이름을 지정한다면, 파일 이름은 `commandArgs(trailingOnly = TRUE)` 명령문으로 반환될 것이다. 작성한 프로그램이 임의 갯수의 파일에 대해서 실행될 수 있기 때문에 알수 없는 갯수의 파일이름을 처리할 필요가 있다.

해결책은 `commandArgs(trailingOnly = TRUE)`에서 반환되는 벡터에 루프를 돌리는 것이다. 변경된 프로그램이 다음에 있는데 `readings-03.R` 이름으로 저장한다.

<div class='out'><pre class='out'><code>main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  for (filename in args) {
    dat <- read.csv(file = filename, header = FALSE)
    mean_per_patient <- apply(dat, 1, mean)
    cat(mean_per_patient, sep = "\n")
  }
}

main()
</code></pre></div>

그리고 실행 결과가 다음에 있다.

<pre class='in'><code>Rscript readings-03.R small-01.csv small-02.csv</code></pre>




<div class='out'><pre class='out'><code>0.3333333
1
13.66667
11
</code></pre></div>

**Note**: 이 지점에서, 스크립트 버젼 3개(`readings-01.R`, `readings-02.R`, `readings-03.R`)를 생성했다. 실무에서는 이렇게 하지는 않을 것이다. 대신에 `readings.R` 파일만 보관하고 기능향상 작업을 할 때마다 버젼 관리 시스템에 커밋한다. 하지만, 교육 목적으로 나란히 연속된 버젼이 필요하다.

#### 도전 과제

  + `check.R` 프로그램을 작성해서 인자로 하나 혹은 그 이상의 염증 데이터 파일 이름을 가지고 모든 파일이 동일한 행과 열을 가지는지 검증하게 하세요. 프로그램을 시험하는 가장 최선의 방법은 무엇인가요?


### 명령어-라인 플래그(Command-Line Flags) 처리하기

다음 단계는 프로그램이 `--min`, `--mean`, `--max` 플래그에 관심을 두게 한다. 플래그는 항상 파일 이름 앞에 위치한다. `readings-04.R` 파일에 다음 사항을 저장하자.

<div class='out'><pre class='out'><code>main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  action <- args[1]
  filenames <- args[-1]
  
  for (f in filenames) {
    dat <- read.csv(file = f, header = FALSE)
    
    if (action == "--min") {
      values <- apply(dat, 1, min)
    } else if (action == "--mean") {
      values <- apply(dat, 1, mean)
    } else if (action == "--max") {
      values <- apply(dat, 1, max)
    }
    cat(values, sep = "\n")
  }
}

main()
</code></pre></div>

유닉스 쉘에서 다음을 실행해서 상기 작성한 것이 정상적으로 동작하는지 확인한다.

<pre class='in'><code>Rscript readings-04.R --max small-01.csv</code></pre>




<div class='out'><pre class='out'><code>1
2
</code></pre></div>

하지만, 몇가지 잘못된 것이 있다.

1.  `main` 함수가 너무 커서 편안하게 읽기가 쉽지 않다.

2.  `action` 인자가 인지된 3개의 플래그 중에 하나가 아니라면, 프로그램을 각각의 파일 로딩(loading)하지만 아무것도 수행하기 않는다. 왜냐하면, 조건을 매칭하는 곳에서 어느 분기에도 해당되지 않기 때문이다. 이와 같이 [침묵하는 실패(Silent failures)](../../gloss.html#silent-failure)가 항상 디버그하기가 어렵다.

새로 작성한 버젼은 각 파일의 처리를 루프에서 빼내서 자신만의 처리 함수를 만들었다. 처리를 수행하기 전에 `action`이 사전에 정의된 플래그중의 하나인지를 검사해서 프로그램이 빨리 종료한다. `readings-05.R` 이름으로 프로그램을 저장한다.

<div class='out'><pre class='out'><code>main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  action <- args[1]
  filenames <- args[-1]
  stopifnot(action %in% c("--min", "--mean", "--max"))
  
  for (f in filenames) {
    process(f, action)
  }
}

process <- function(filename, action) {
  dat <- read.csv(file = filename, header = FALSE)
  
  if (action == "--min") {
    values <- apply(dat, 1, min)
  } else if (action == "--mean") {
    values <- apply(dat, 1, mean)
  } else if (action == "--max") {
    values <- apply(dat, 1, max)
  }
  cat(values, sep = "\n")
}

main()
</code></pre></div>

상기 프로그램은 앞서 작성한 프로그램보다 4줄 더 길다. 하지만, 좀더 완전히 이해하기 쉬운 8줄과 12줄 프로그램 덩어리로 쪼갰다.

> **Tip:** [argparse][argparse-r] R 패키지가 복잡한 명령어-라인 플래그를 처리하는데 도움이 된다. [argparse][argparse-r] 패키지는 동일한 이름의 [파이썬 모듈][argparse-py]을 활용한다. 이번 학습에서는 [argparse][argparse-r] 패키지를 다루지 않을 것이다. 하지만, 다수의 매개변수를 가진 프로그램을 작성할 때, 패키지 [소품문(비네트, vignette)][]을 읽으면 도움이 된다.

[argparse-r]: http://cran.r-project.org/web/packages/argparse/index.html
[argparse-py]: http://docs.python.org/dev/library/argparse.html
[vignette]: http://cran.r-project.org/web/packages/argparse/vignettes/argparse.pdf

#### 도전 과제

  + 상기 프로그래을 다시 작성해서 `--min`, `--mean`, `--max` 대신에 `-n`, `-m`, `-x`을 각각 사용하게 하세요. 코드가 가독성이 좋습니까? 프로그램이 더 이해하기 좋습니까?

  + 이와는 별도로 프로그램을 변경해서 만약 어떤 행동(action)이 명기되지 않거나 혹은 잘못된 동작이 주어지면, 어떻게 사용되어야 하는지 설명하는 메시지를 출력하게 하세요.

### 표준 입력(Standard Input) 처리하기

프로그램이 다음으로 할 작업은 파일 이름이 주어지지 않았다면 표준 입력에서 데이터를 읽는 것이다. 파일이름을 파이프라인에 넣고 입력값으로 되돌려 사용하는 것이 예이다. 또 다른 스크립트 `count-stdin.R`로 저장하고 실험을 해보자.

<div class='out'><pre class='out'><code>lines <- readLines(con = file("stdin"))
count <- length(lines)
cat("lines in standard input: ")
cat(count, sep = "\n")
</code></pre></div>

상기 작은 프로그램은 `file("stdin")`을 사용해서 표준 입력에서 라인(행)을 읽어온다. 이것은 정규 파일에서 할 수 있었던 거의 모든 것을 할 수 있게 한다. 상기 예제에서 인자를 `readLines` 함수에 전달하는데 각 라인을 벡터의 요소로 저장한다. 마치 정규 명령어-라인 프로그램인 것처럼 유닉스 쉘에서 실행을 시도해 보자.

<pre class='in'><code>Rscript count-stdin.R < small-01.csv</code></pre>

<div class='out'><pre class='out'><code>lines in standard input: 2
</code></pre></div>

주목할 점은 `cat`을 호출할 때, `sep = "\n"`을 명기하지 않아서 출력이 동일한 줄에 쓰여진다.

흔한 실수는 다음과 같이 표준입력에서 읽어서 무언가 실행하려고 하는 것이다.

<pre class='in'><code>Rscript count-stdin.R small-01.csv</code></pre>

즉, 표준입력에서 파일로 되돌리는 문자(`<`)를 생략한 것이다. 이 경우에 표준 입력에는 아무 것도 없어서 프로그램은 누군가 키보드로 무엇인가를 입력하는 루프 시작에서 기다리기만 한다. 무언가를 타이핑하지만 R은 멈추지 않는데 이유는 언제 표준 입력이 끝나는지 모르기 때문이다. 이와 같은 상황이라면, `ctrl`+`z`를 눌러 R을 잠시 멈출 수 있다. 하지만 기술적으로 백그라운드에서 잠시 멈춰있는 것이다. 만약 프로세스를 죽이려면, 링크된 [지시][ps-kill])를 따르세요.

[ps-kill]: http://linux.about.com/library/cmd/blcmdl_kill.htm

프로그램을 다시 작성해서 만약 어떤 파일이름도 제공된게 없다면 `file("stdin")`에서 데이터를 로딩한다. 운좋게도, `read.csv`는 파일이름 혹은 첫번째 매개변수로 열린 파일을 처리할 수 있다. 그래서 실질적으로 `process`를 변경할 필요는 없다. `main`을 갱신하여 `readings-06.R` 파일로 저장한다.

<div class='out'><pre class='out'><code>main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  action <- args[1]
  filenames <- args[-1]
  stopifnot(action %in% c("--min", "--mean", "--max"))
  
  if (length(filenames) == 0) {
    process(file("stdin"), action)
  } else {  
    for (f in filenames) {
      process(f, action)
    }
  }
}

process <- function(filename, action) {
  dat <- read.csv(file = filename, header = FALSE)
  
  if (action == "--min") {
    values <- apply(dat, 1, min)
  } else if (action == "--mean") {
    values <- apply(dat, 1, mean)
  } else if (action == "--max") {
    values <- apply(dat, 1, max)
  }
  cat(values, sep = "\n")
}

main()
</code></pre></div>

작성한 프로그램을 시도해 보자. 모든 환자의 평균 염증값을 계산하는 대신에, 첫 10명의 환자(행)에 대한 평균값만을 계산할 수 있다.

<pre class='in'><code>head inflammation-01.csv | Rscript readings-06.R --mean</code></pre>

<div class='out'><pre class='out'><code>5.45
5.425
6.1
5.9
5.55
6.225
5.975
6.65
6.625
6.525
</code></pre></div>

이제 완료했다. 프로그램이 처음 기획했던 모든 것을 수행한다.

#### 도전 과제

  + `line-count.R` 프로그램을 작성해서 유닉스 `wc` 명령어처럼 동작하게 하세요.
    *   만약 어떤 파일이름도 주어지지 않는다면, 표준 입력에 행 숫자만을 보고한다.
    *   만약 하나 혹은 그 이상의 파일이름이 주어지면, 각 파일의 행 숫자와 전체 행 숫자를 보고한다.

<div class="keypoints" markdown="1">
#### 주요점

*   `commandArgs(trailingOnly = TRUE)`을 사용해서 프로그램이 실행시에 필요한 명령어-라인 인자 벡터를 얻는다.
*   침묵하는 실패(Silent failures)를 피한다.
*   `file("stdin")`을 사용해서 프로그램의 표준 입력에 연결한다.
*   `cat(vec, sep = "\n")`을 사용하여 `vec` 요소를 한줄에 하나씩 표준 출력으로 쓴다.
</div>
