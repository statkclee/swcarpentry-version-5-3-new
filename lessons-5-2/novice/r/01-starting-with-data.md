---
layout: lesson
root: ../..
---



## 환자 데이터(Patient Data) 분석

관절염에 새로운 치료법이 처방된 환자의 염증에 대한 연구를 진행하고 있고, 첫 12 데이터셋(Data Set)을 분석할 필요가 있다.
데이터셋은 [CSV 형식(comma-separated values, 구분자가 콤마 값을 가진 파일 형식)](../../gloss.html#comma-separeted-values)으로 저장되어 있다. 각 행은 환자 한명의 정보로 구성되어 있고, 열은 일련의 날짜 정보를 나타낸다. 첫번째 파일의 첫 몇 행의 정보는 다음과 같다.

<div class='out'><pre class='out'><code>0,0,1,3,1,2,4,7,8,3,3,3,10,5,7,4,7,7,12,18,6,13,11,11,7,7,4,6,8,8,4,4,5,7,3,4,2,3,0,0
0,1,2,1,2,1,3,2,2,6,10,11,5,9,4,4,7,16,8,6,18,4,12,5,12,7,11,5,11,3,3,5,4,4,5,5,1,1,0,1
0,1,1,3,3,2,6,2,5,9,5,7,4,5,4,15,5,11,9,10,19,14,12,17,7,12,11,7,4,2,10,5,4,2,2,3,2,2,1,1
0,0,2,0,4,2,2,1,6,7,10,7,9,13,8,8,15,10,10,7,17,4,4,7,6,15,6,4,9,11,3,5,6,3,3,4,2,3,2,1
0,1,1,3,3,1,3,5,2,4,4,7,6,5,3,10,8,10,6,17,9,14,9,7,13,9,12,6,7,7,9,6,3,2,2,4,2,0,1,1
</code></pre></div>

다음을 수행해야 된다.

* CSV 형식 데이터 파일을 주기억장치에 로딩(loading)한다.
* 모든 환자에 대해서 각 날짜별로 평균 염증을 계산한다.
* 결과값을 플롯(plot)한다.

상기 모든 것을 수행하기 위해서, 프로그래밍에 관해 약간 학습할 필요가 있다.

#### 목표

* 파일에서 테이블 형식의 데이터를 읽는다.
* 값을 변수에 할당한다.
* 데이터에서 개별적인 값과 일부분을 선택한다.
* 데이터의 데이터프레임에 연산을 수행한다.
* 간단한 그래프를 화면에 출력한다.

### 데이터 로딩하기(Loading Data)

염증 데이터를 로딩(loading)하기 위해서, 먼저 데이터의 위치를 지정할 필요가 있다. 현재 작업 디렉토리를 
`setwd` 함수를 사용해서 CSV 파일이 있는 지점으로 변경한다.
예를 들어, CSV 파일이 홈디렉토리 `swc`로 명명된 디렉토리에 자리하고 있다면, 다음 명령어를 사용하여 작업 디렉토리를 변경한다.

<pre class='in'><code>setwd("~/swc")</code></pre>

유닉스 쉘과 마찬가지로 명령어를 타이핑하고 '엔터(Enter)' (혹은 '리턴(return)') 키를 누른다. RStudio GUI의 메뉴 옵션(`Session` -> `Set Working Directory` -> `Choose Directory...`)을 사용하여 작업 디렉토리를 변경할 수 있다.

이제 `read.csv`를 사용하여 R에 데이터를 로딩할 수 있다.

<pre class='in'><code>read.csv(file = "inflammation-01.csv", header = FALSE)</code></pre>

`read.csv(...)` 표현식은 [함수 호출(function call)](../../gloss.html#function-call)로 R에게 요청하여 `read.csv`을 실행하게 한다.

`read.csv`는 두 개의 [인수(arguments)](../../gloss.html#argument)가 있다. 하나는 읽고자 하는 파일 이름이고, 다른 하나는 파일의 첫 행이 데이터의 칼럼(열) 이름 포함 여부다. 파일이름은 [문자열(string)](../../gloss.html#string)이 될 필요가 있어서 인용부호안에 파일이름을 넣는다. 두번째 인수 `header`가 `FALSE`로 할당된 것은 데이터 파일이 칼럼 헤더(column header)를 가지고 있지 않음을 나타낸다. `FALSE` 값과 반대의 경우 `TRUE`가 되는 것은 4번째 학습에서 더 얘기를 나눌 것이다. 

함수의 유용성은 무슨 값이 인수에 전달되든지 주어진 행동을 수행한다는 것이다. 예를 들어, 인수 `file`에 다른 파일의 이름을 제공한다면, `read.csv`는 대신에 그 파일을 읽을 것이다. 다음 학습에서 함수와 인수에 관한 좀더 자세한 내용을 배울 것이다.

함수의 출력결과에 대해서 어떤 특별한 것을 지시하지 않아서, 콘솔에서 `inflammation-01.csv` 파일 전체 내용을 화면에 출력한다. 시도해 보세요.

`read.csv`는 파일을 읽어들이지만, 데이터를 주기억장치(memory)에 저장하지 않는다.
저장을 하기고자 한다면, 데이터프레임을 변소에 할당할 필요가 있다. 변수는 단순히 값에 대한 이름으로 `x`, `current_temperature`, `subject_id`과 같다. 새로운 변수를 생성하여 `<-`을 사용해서 값을 변수에 할당할 수 있다.

<pre class='in'><code>weight_kg <- 55</code></pre>

변수가 값을 가지게 되면, 변수 이름을 타이핑하고 `엔터(Enter)` 혹은 `리턴(return)`을 쳐서 변수를 출력할 수 있다. 일반적으로 변수에 할당하는 경우를 *제외하고* R은 함수나 연산에서 반환되는 임의의 개체를 콘솔에 출력한다.

<pre class='in'><code>weight_kg</code></pre>

<div class='out'><pre class='out'><code>[1] 55
</code></pre></div>

변수로 산수를 할 수 있다.

<pre class='in'><code># weight in pounds:
2.2 * weight_kg</code></pre>

<div class='out'><pre class='out'><code>[1] 121
</code></pre></div>

> **Tip:** `#`문자를 사용해서 코드에 주석을 추가할 수 있다. 이런 방식으로 코드를 문서화하는 것은 매우 유용하다. 그렇게 함으로써 다른 사람이나 자신도 다음에 코드를 읽을 때 코드가 무엇을 하는 것인지 따라가기가 쉽다.

새로운 값을 할당함으로써 개체의 값을 변경할 수 있다.

<pre class='in'><code>weight_kg <- 57.5
# weight in kilograms is now
weight_kg</code></pre>

<div class='out'><pre class='out'><code>[1] 57.5
</code></pre></div>

만약 변수를 이름이 써진 포스트잇 같은 스티커 노트라고 가정한다면, 할당은 특정한 값에 스티커 노트를 붙이는 것과 같다.

<img src="../python/img/python-sticky-note-variables-01.svg" alt="Variables as Sticky Notes" />

이것이 의미하는 바는 한 개체에 값을 할당하는 것이 다른 변수의 값을 변경시키지는 않는다. 예를 들어, 변수에 개체의 무게를 파운드로 저장하자.

<pre class='in'><code>weight_lb <- 2.2 * weight_kg
# weight in kg...
weight_kg</code></pre>

<div class='out'><pre class='out'><code>[1] 57.5
</code></pre></div>

<pre class='in'><code># ...and in pounds
weight_lb</code></pre>

<div class='out'><pre class='out'><code>[1] 126.5
</code></pre></div>

<img src="../python/img/python-sticky-note-variables-02.svg" alt="Creating Another Variable" />

그리고 나서 `weight_kg`를 변경하자.

<pre class='in'><code>weight_kg <- 100.0
# weight in kg now...
weight_kg</code></pre>

<div class='out'><pre class='out'><code>[1] 100
</code></pre></div>

<pre class='in'><code># ...and in weight pounds still
weight_lb</code></pre>

<div class='out'><pre class='out'><code>[1] 126.5
</code></pre></div>

<img src="../python/img/python-sticky-note-variables-03.svg" alt="Updating a Variable" />

`weight_lb` 변수는 값이 어디에서 왔는지 기억하지 않기 때문에, 자동적으로 `weight_kg`이 변경될 때 갱신되지 않는다. 엑셀같은 스프레드쉬트가 동작하는 방식과 이런 점이 다르다.

변수에 어떻게 값을 할당하는지 알기 때문에, `read.csv`을 다시 실행하고 결과를 저장하자.

<pre class='in'><code>dat <- read.csv(file = "inflammation-01.csv", header = FALSE)</code></pre>

상기 문장은 결과를 출력하지 않는데 할당은 어떤 것도 화면에 출력하지 않기 때문이다.
데이터가 주기억장치에 로딩되었는지 확인하고자 한다면, 변수의 값을 출력할 수 있다.
하지만, 큰 데이터셋에 대해서는 `head` 함수를 사용해서 데이터의 처음 몇 줄만 화면에 출력하는 것이 편리하다.

<pre class='in'><code>head(dat)</code></pre>

<div class='out'><pre class='out'><code>  V1 V2 V3 V4 V5 V6 V7 V8 V9 V10 V11 V12 V13 V14 V15 V16 V17 V18 V19 V20
1  0  0  1  3  1  2  4  7  8   3   3   3  10   5   7   4   7   7  12  18
2  0  1  2  1  2  1  3  2  2   6  10  11   5   9   4   4   7  16   8   6
3  0  1  1  3  3  2  6  2  5   9   5   7   4   5   4  15   5  11   9  10
4  0  0  2  0  4  2  2  1  6   7  10   7   9  13   8   8  15  10  10   7
5  0  1  1  3  3  1  3  5  2   4   4   7   6   5   3  10   8  10   6  17
6  0  0  1  2  2  4  2  1  6   4   7   6   6   9   9  15   4  16  18  12
  V21 V22 V23 V24 V25 V26 V27 V28 V29 V30 V31 V32 V33 V34 V35 V36 V37 V38
1   6  13  11  11   7   7   4   6   8   8   4   4   5   7   3   4   2   3
2  18   4  12   5  12   7  11   5  11   3   3   5   4   4   5   5   1   1
3  19  14  12  17   7  12  11   7   4   2  10   5   4   2   2   3   2   2
4  17   4   4   7   6  15   6   4   9  11   3   5   6   3   3   4   2   3
5   9  14   9   7  13   9  12   6   7   7   9   6   3   2   2   4   2   0
6  12   5  18   9   5   3  10   3  12   7   8   4   7   3   5   4   4   3
  V39 V40
1   0   0
2   0   1
3   1   1
4   2   1
5   1   1
6   2   1
</code></pre></div>

#### 도전과제

도표를 그려서 다음 프로그램의 각 문장이 실행된 후에 무슨 변수가 무슨 값을 참조하는지 보이세요.

	mass <- 47.5
	age <- 122
	mass <- mass * 2.0
	age <- age - 20

### 데이터 능숙하게 다루기

데이터가 주기억장치에 올라갔기 때문에, 데이터를 가지고 무언가를 시작할 수 있다.
먼저 `dat`가 무슨 형식인지 확인해보자.

<pre class='in'><code>class(dat)</code></pre>

<div class='out'><pre class='out'><code>[1] "data.frame"
</code></pre></div>

출력결과를 통해서 현재 데이터가 데이터프레임(data frame)으로 알 수 있다.
많은 사람들이 사용해서 익숙한 마이크로소프트 엑셀 스프레드쉬트와 유사하다.
데이터프레임이 데이터를 저장하는데 매우 유용한데 왜냐하면 강우량 같은 연속형 변수나 성별 같은 범주형 변수를 함께 담을 수 있기 때문이다.

다음과 같이 데이터프레임의 [형태(shape)](../../gloss.html#shape)를 확인할 수 있다.

<pre class='in'><code>dim(dat)</code></pre>

<div class='out'><pre class='out'><code>[1] 60 40
</code></pre></div>

출력결과를 통해서 `dat` 데이터프레임이 60 행과 40 열로 구성된 것을 알 수 있다.

데이터프레임에서 값 하나를 얻으려고 한다면, 수학에서 하는 것과 같은 방식으로 꺾쇄 괄호내부에 [인덱스(index)](../../gloss.html#index)를 넣을 수 있다.

<pre class='in'><code># first value in dat
dat[1, 1]</code></pre>

<div class='out'><pre class='out'><code>[1] 0
</code></pre></div>

<pre class='in'><code># middle value in dat
dat[30, 20]</code></pre>

<div class='out'><pre class='out'><code>[1] 16
</code></pre></div>

`[30, 20]` 같이 인텍스를 넣어서 데이터프레임의 요소값을 하나 선택할 수 있지만, 전체도 선택할 수 있다.
예를 들어, 다음과 같이 첫 환자 네명(행)에 대한 첫 10일치(열) 값을 선택할 수 있다.

<pre class='in'><code>dat[1:4, 1:10]</code></pre>

<div class='out'><pre class='out'><code>  V1 V2 V3 V4 V5 V6 V7 V8 V9 V10
1  0  0  1  3  1  2  4  7  8   3
2  0  1  2  1  2  1  3  2  2   6
3  0  1  1  3  3  2  6  2  5   9
4  0  0  2  0  4  2  2  1  6   7
</code></pre></div>

[슬라이스(slice)](../../gloss.html#slice) `1:4`가 뜻하는 것은 "1번 인덱스에서 시작해서 4번 인덱스까지다." 슬라이스가 반드시 1번에서 시작할 필요는 없다. 즉, 아래 행은 5~10번 행을 선택하는 사례다.

<pre class='in'><code>dat[5:10, 1:10]</code></pre>

<div class='out'><pre class='out'><code>   V1 V2 V3 V4 V5 V6 V7 V8 V9 V10
5   0  1  1  3  3  1  3  5  2   4
6   0  0  1  2  2  4  2  1  6   4
7   0  0  2  2  4  2  2  5  5   8
8   0  0  1  2  3  1  2  3  5   3
9   0  0  0  3  1  5  6  5  5   8
10  0  1  1  2  1  3  5  3  5   8
</code></pre></div>

`c` 함수(**c**ombine을 뜻함)를 사용해서 불연속적인 값도 선택할 수 있다.

<pre class='in'><code>dat[c(3, 8, 37, 56), c(10, 14, 29)]</code></pre>

<div class='out'><pre class='out'><code>   V10 V14 V29
3    9   5   4
8    3   5   6
37   6   9  10
56   7  11   9
</code></pre></div>

또한, 행과 열에 슬라이스를 줄 필요도 없다. 만약 행에 슬라이스가 없다면, R은 모든 행을 반환한다. 만약 열에 슬라이스가 없다면, R은 모든 열을 반환한다. `dat[, ]`처럼 행과 열 모두에 슬라이가 없다면 R은 전체 데이터프레임을 반환한다.

<pre class='in'><code># All columns from row 5
dat[5, ]</code></pre>

<div class='out'><pre class='out'><code>  V1 V2 V3 V4 V5 V6 V7 V8 V9 V10 V11 V12 V13 V14 V15 V16 V17 V18 V19 V20
5  0  1  1  3  3  1  3  5  2   4   4   7   6   5   3  10   8  10   6  17
  V21 V22 V23 V24 V25 V26 V27 V28 V29 V30 V31 V32 V33 V34 V35 V36 V37 V38
5   9  14   9   7  13   9  12   6   7   7   9   6   3   2   2   4   2   0
  V39 V40
5   1   1
</code></pre></div>

<pre class='in'><code># All rows from column 16
dat[, 16]</code></pre>

<div class='out'><pre class='out'><code> [1]  4  4 15  8 10 15 13  9 11  6  3  8 12  3  5 10 11  4 11 13 15  5 14
[24] 13  4  9 13  6  7  6 14  3 15  4 15 11  7 10 15  6  5  6 15 11 15  6
[47] 11 15 14  4 10 15 11  6 13  8  4 13 12  9
</code></pre></div>

이제 염증데이터에 대해서 좀더 살펴보기 위해서 수학 연산을 수행해보자.
데이터를 분석할 때, 환자마다 최대값 혹은 날마다 평균값 같은 통계량을 종종 살펴보고자 한다.
이것을 수행하는 방법은 신규로 임시 데이터 프레임을 생성해서 데이터를 선택하고 계산을 수행하는 것이다.

<pre class='in'><code># first row, all of the columns
patient_1 <- dat[1, ]
# max inflammation for patient 1
max(patient_1)</code></pre>

<div class='out'><pre class='out'><code>[1] 18
</code></pre></div>

사실 변수에 행을 저장할 필요는 없다. 대신에 선택과 함수 호출을 조합할 수 있다.

<pre class='in'><code># max inflammation for patient 2
max(dat[2, ])</code></pre>

<div class='out'><pre class='out'><code>[1] 18
</code></pre></div>

R은 또한 데이터의 최소값, 평균, 중위값, 표준편자 같은 표준 연산에 대한 함수를 제공한다.

<pre class='in'><code># minimum inflammation on day 7
min(dat[, 7])</code></pre>

<div class='out'><pre class='out'><code>[1] 1
</code></pre></div>

<pre class='in'><code># mean inflammation on day 7
mean(dat[, 7])</code></pre>

<div class='out'><pre class='out'><code>[1] 3.8
</code></pre></div>

<pre class='in'><code># median inflammation on day 7
median(dat[, 7])</code></pre>

<div class='out'><pre class='out'><code>[1] 4
</code></pre></div>

<pre class='in'><code># standard deviation of inflammation on day 7
sd(dat[, 7])</code></pre>

<div class='out'><pre class='out'><code>[1] 1.725
</code></pre></div>

만약 모든 환자에 대한 최대 염증값 혹은 각 날짜별로 평균값이 필요하다면 어떨까?
다음 도표처럼, 데이터프레임의 가장자리를 따라서 연산을 수행하고자 한다.

<img src="figure/r-operations-across-axes.svg" alt="Operations Across Axes" />

상기 연산을 수행하기 위해서 `apply` 함수를 사용한다.

> **Tip:** `apply` 같은 R 함수에 대해 좀더 학습하기 위해서, `help(apply)` 혹은 `?apply` 명령어를 실행해서 도움말 문서를 참조한다.

`apply` 함수를 사용해서 데이터 프레임의 모든 행(`MARGIN = 1`) 혹은 열(`MARGIN = 2`)에 동일한 함수연산을 수행한다.

그래서, 각 환자의 평균 염증값을 얻기 위해서 데이터프레임의 모든 행(`MARGIN = 1`)에 대해서 평균값을 계산할 필요가 있을 것이다.

<pre class='in'><code>avg_patient_inflammation <- apply(dat, 1, mean)</code></pre>

각 날짜별 평균 염증값을 얻기 위해서 데이터프레임의 모든 열(`MARGIN = 2`)에 대해서 평균값을 계산할 필요가 있을 것이다.

<pre class='in'><code>avg_day_inflammation <- apply(dat, 2, mean)</code></pre>

`apply` 함수의 두번째 인자는 `MARGIN`이여서, 상기 명령어는 `apply(dat, MARGIN = 2, mean)과 동일하다.
다음 학습에서 이유를 배울 것이다.

> **Tip:** 몇몇 많이 사용되는 연산에 대해서 좀더 효율적인 대안이 있다. 예를 들어, `rowMeans`와 `colMeans`를 사용해서 열방향과 행방향 평균을 계산할 수 있다.

#### 도전 과제

데이터프레임의 일부를 [슬라이스(slice)](../../gloss.html#slice)라고 한다. 문자 벡터에도 슬라이스를 적용할 수 있다.

<pre class='in'><code>element <- c("o", "x", "y", "g", "e", "n")
# first three characters
element[1:3]</code></pre>



<div class='out'><pre class='out'><code>[1] "o" "x" "y"
</code></pre></div>



<pre class='in'><code># last three characters
element[4:6]</code></pre>



<div class='out'><pre class='out'><code>[1] "g" "e" "n"
</code></pre></div>

1.  `element[1:4]` 슬라이스를 사용해서 첫 네개 문자를 선택했다면, 역순으로 첫 네개 문자를 어떻게 얻을 수 있을까?

2.  `element[-1]`의 값은 무엇일까? `element[-4]`의 값은 무엇일까? 상기 해답이 주어졌을 때, `element[-1:-4]`은 무엇이될지 설명해 보세요.

3. `element` 슬라이스를 사용해서 단어 "eon"을 예를 들어 `c("e", "o", "n")` 처럼 작성하는 새로운 문자 벡터를 생성하세요.

### 그래프 그리기 (Plotting)

수학자 Richard Hamming은 "컴퓨팅의 목적은 숫자가 아니라 직관(insight)다." 그래서 직관을 키우는 가장 좋은 방법은 종종 데이터를 시각화하는 것이다. 시각화에 대한 온전한 강의를 펼칠만 하지만, 여기서는 R의 그래프 기능 몇가지만 살펴본다.

시간에 따른 평균 염증값을 살펴보자. `apply(dat, 2, mean)`을 사용해서 이미 값을 계산해서 변수 `avg_day_inflammation`에 저장했다. 이를 그래프로 그리는 것은 `plot` 함수로 할 수 있다.

<pre class='in'><code>plot(avg_day_inflammation)</code></pre>

<img src="figure/01-starting-with-data-plot-avg-inflammation.png" title="plot of chunk plot-avg-inflammation" alt="plot of chunk plot-avg-inflammation" style="display: block; margin: auto;" />

위에서 `plot` 함수에 모든 환자에 대한 날마다 평균 염증에 해당하는 숫자 벡터를 전달했다. `plot` 함수는 Y축에 평균 염증 수준과 X축에 이 경우 40일 처방에 해당하는 벡터 값의 순서 즉 인덱스를 두고 산점도(scatter plot)를 생성했다. 결과는 대략 선형적으로 올라가고 내려가지만 의심스럽다. 다른 연구 결과에 기초하여 좀더 빠른 급격한 상승과 좀더 완만한 하강이 예측됐다. 또 다른 두개의 통계량 (일자별로 최대 그리고 최소 염증값)을 살펴보자.

<pre class='in'><code>max_day_inflammation <- apply(dat, 2, max)
plot(max_day_inflammation)</code></pre>

<img src="figure/01-starting-with-data-plot-max-inflammation.png" title="plot of chunk plot-max-inflammation" alt="plot of chunk plot-max-inflammation" style="display: block; margin: auto;" />


<pre class='in'><code>min_day_inflammation <- apply(dat, 2, min)
plot(min_day_inflammation)</code></pre>

<img src="figure/01-starting-with-data-plot-min-inflammation.png" title="plot of chunk plot-min-inflammation" alt="plot of chunk plot-min-inflammation" style="display: block; margin: auto;" />

최대값은 완벽하게 매끄럽게 상승하고 하강하지만, 최소값은 계단 함수(Step function) 모양으로 보인다.
어느쪽의 결과도 그럴듯하지 못해서 계산에서 실수가 있거나 데이터에 뭔가 잘못되었다..

#### 도전과제

모든 환자에 대해서 각 일자별로 염증 데이터의 표준편차를 보이는 그래프를 생성하세요.

#### 주요점

* 주기억장소에 저장하기 위해서 변수에 값을 할당할 때 `variable <- value`을 사용하라.
* 객체는 값이 변수에 할당될 때마다 주문형으로 생성된다.
* `dim` 함수는 데이터프레임의 크기 정보를 준다.
* 데이터프레임에서 요소를 하나 끄집어 낼때 `object[x, y]`을 사용한다.
* `from:to`를 사용해서 시작(`from`)과 끝(`to`) 인덱스를 포함하는 시퀀스(sequence)를 표기한다.
* 데이터프레임에서 동작하는 인덱스(index)와 슬라이스(slice)는 벡터(vector)에도 적용된다.
* `#`을 프로그램 주석으로 사용한다.
* 간단한 통계치를 계산하기 위해서 `mean`, `max`, `min`, `sd` 함수를 사용한다.
* `apply` 함수를 사용해서 데이터프레임의 행 혹은 열의 통계치를 계산한다.
* `plot`를 사용해서 간단한 시각화 그래프를 생성한다.

#### 다음 단계

지금까지 작업 결과를 통해 데이터 파일에 뭔가 잘못된 것이 있다는 것을 확인했다. 다른 11개 파일도 같은 방식으로 확인하고자 하지만, 동일한 명령어를 반복적으로 타이핑하는 것은 지루하고 오류에 쉽게 노출된다. 컴퓨터는 우리가 알고 있는 한 지루해 하지 않기 때문에, 단일 명령어로 전체 분석을 수행할 수 있는 방법을 생성하고 나서 각 파일에 대해서 각 단계를 어떻게 반복하는지를 해결해야 한다. 이런 작업이 다음 두 학습 주제다.
