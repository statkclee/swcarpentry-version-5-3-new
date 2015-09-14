---
layout: lesson
root: ../..
---

## 집합(Aggregation)


<div class="objectives" markdown="1">
#### 목표

*   "집합(aggregation)을 정의하고 사용예를 제시한다.
*   집합하는 값을 계산하는 쿼리를 작성한다.
*   집합을 수행하는 쿼리의 실행을 추적한다.
*   결측 데이터가 어떻게 집합되는 동안에 다뤄지는지 설명한다.
</div>


이제 데이터의 평균과 범위를 계산하고자 한다. `Visited` 테이블에서 모든 날짜 정보를 어떻게 선택하는지 알고 있다.


<pre class="in"><code>%load_ext sqlitemagic</code></pre>


<pre class="in"><code>%%sqlite survey.db
select dated from Visited;</code></pre>

<div class="out"><table>
<tr><td>1927-02-08</td></tr>
<tr><td>1927-02-10</td></tr>
<tr><td>1939-01-07</td></tr>
<tr><td>1930-01-12</td></tr>
<tr><td>1930-02-26</td></tr>
<tr><td>None</td></tr>
<tr><td>1932-01-14</td></tr>
<tr><td>1932-03-22</td></tr>
</table></div>


하지만 조합하기 위해서는 `min` 혹은 `max` 같은 [집합 함수(aggregation function)](../../gloss.html#aggregation-function)를 사용해야만 한다.
각 함수는 입력으로 레코드 집합을 받고 출력으로 단일 레코드를 만든다.


<pre class="in"><code>%%sqlite survey.db
select min(dated) from Visited;</code></pre>

<div class="out"><table>
<tr><td>1927-02-08</td></tr>
</table></div>


<img src="img/sql-aggregation.svg" alt="SQL Aggregation" />


<pre class="in"><code>%%sqlite survey.db
select max(dated) from Visited;</code></pre>

<div class="out"><table>
<tr><td>1939-01-07</td></tr>
</table></div>


`min`과 `max`는 SQL에 내장된 단지 두개의 집합 함수다.
다른 세개는 `avg`, `count`, `sum`이 있다.


<pre class="in"><code>%%sqlite survey.db
select avg(reading) from Survey where quant=&#39;sal&#39;;</code></pre>

<div class="out"><table>
<tr><td>7.20333333333</td></tr>
</table></div>


<pre class="in"><code>%%sqlite survey.db
select count(reading) from Survey where quant=&#39;sal&#39;;</code></pre>

<div class="out"><table>
<tr><td>9</td></tr>
</table></div>


<pre class="in"><code>%%sqlite survey.db
select sum(reading) from Survey where quant=&#39;sal&#39;;</code></pre>

<div class="out"><table>
<tr><td>64.83</td></tr>
</table></div>


여기서 `count(reading)`을 사용했다. 하지만 `quant`를 단순히 쉽게 세거나 테이블의 다른 어떤 필드도 셀 수 있고 심지어 `count(*)`을 사용하기도 한다.
왜냐하면 `count()`함수가 값 자체보다는 얼마나 많은 값이 있는지에만 관심을 두기 때문이다.

SQL이 여러개의 집합연산도 한번에 수행한다. 예를 들어, 염분측정치의 범위도 알 수 있다.


<pre class="in"><code>%%sqlite survey.db
select min(reading), max(reading) from Survey where quant=&#39;sal&#39; and reading&lt;=1.0;</code></pre>

<div class="out"><table>
<tr><td>0.05</td><td>0.21</td></tr>
</table></div>


출력결과가 놀라움을 줄 수도 있지만, 원 결과값과 집합 결과를 조합할 수도 있다.


<pre class="in"><code>%%sqlite survey.db
select person, count(*) from Survey where quant=&#39;sal&#39; and reading&lt;=1.0;</code></pre>

<div class="out"><table>
<tr><td>lake</td><td>7</td></tr>
</table></div>


왜 Roerich 혹은 Dyer가 아닌 Lake의 이름이 나타날까요?
답은 필드를 집합하지만 어떻게 집합하는지 말을 하지 않기 때문에 데이터베이스 관리자가 입력에서 실제 값을 고른다.
처음 처리된 것, 마지막에 처리된 것, 혹은 완전히 다른 무언가를 사용할 수도 있다.

또다른 중요한 사실은 집합할 어떠한 값도 없을 때, 집합 결과는 0 혹은 다른 임의의 값 보다 "알지 못한다(don't know)"가 된다.


<pre class="in"><code>%%sqlite survey.db
select person, max(reading), sum(reading) from Survey where quant=&#39;missing&#39;;</code></pre>

<div class="out"><table>
<tr><td>None</td><td>None</td><td>None</td></tr>
</table></div>


집합 함수의 마지막 중요한 한가지 기능은 매우 유용한 방식으로 나머지 SQL과는 일관되지 않다는 것이다.
만약 두 값을 더하는데 그중 하나가 `null`이면 결과는 `null`이다.
확장해서, 만약 한 집합의 모든 값을 더하기 위해서 `sum`을 사용하고 이들 중 임의의 값이 `null`이면,
결과도 또한 `null`이어야 한다. 하지만 집합함수가 `null` 값을 무시하고 단지 `non-null` 값만을 조합한다면 훨씬 더 유용하다.
명시적으로 항상 필터해야하는 대신에 이것의 결과 쿼리를 다음과 같이 작성할 수 있게 한다.


<pre class="in"><code>%%sqlite survey.db
select min(dated) from Visited;</code></pre>

<div class="out"><table>
<tr><td>1927-02-08</td></tr>
</table></div>


명시적으로 항상 다음과 같이 필터하는 쿼리를 작성할 필요가 없다.


<pre class="in"><code>%%sqlite survey.db
select min(dated) from Visited where dated is not null;</code></pre>

<div class="out"><table>
<tr><td>1927-02-08</td></tr>
</table></div>


한번에 모든 레코드를 집합하는 것이 항상 타당하지는 않다.
예를 들어, Gina가 데이터에 체계적인 편의(bias)가 있어서 다른 과학자의 방사선 측정치가 다른 사람의 것과 비교하여 높다고 의심한다고 가정하자.
다음 쿼리가 의도를 반영하여 동작하지 않는다는 것은 알고 있다.


<pre class="in"><code>%%sqlite survey.db
select person, count(reading), round(avg(reading), 2)
from  Survey
where quant=&#39;rad&#39;;</code></pre>

<div class="out"><table>
<tr><td>roe</td><td>8</td><td>6.56</td></tr>
</table></div>


왜냐하면 데이터베이스 관리자가 각 과학자별로 구분된 집합하기 보다는 임의의 한명의 과학자 이름만 선택하기 때문이다.
단지 5명의 과학자만 있기 때문에, 다음과 같은 형식의 5개 쿼리를 작성할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select person, count(reading), round(avg(reading), 2)
from  Survey
where quant=&#39;rad&#39;
and   person=&#39;dyer&#39;;</code></pre>

<div class="out"><table>
<tr><td>dyer</td><td>2</td><td>8.81</td></tr>
</table></div>


하지만, 이러한 접근법은 성가시고, 만약 50명 혹은 500명의 과학자를 가진 데이터셋을 분석한다면,
모든 쿼리를 올바르게 작성할 가능성은 작다.

필요한 것은 데이터베이스 관리자가 `group by`절을 사용해서 각 과학자별로 시간을 집합하도록 지시하는 것이다.


<pre class="in"><code>%%sqlite survey.db
select   person, count(reading), round(avg(reading), 2)
from     Survey
where    quant=&#39;rad&#39;
group by person;</code></pre>

<div class="out"><table>
<tr><td>dyer</td><td>2</td><td>8.81</td></tr>
<tr><td>lake</td><td>2</td><td>1.82</td></tr>
<tr><td>pb</td><td>3</td><td>6.66</td></tr>
<tr><td>roe</td><td>1</td><td>11.25</td></tr>
</table></div>


`group by`는 이름이 의미하는 것과 동일한 것을 정확하게 수행한다.
지정된 필드에 동일한 값을 가진 모든 레코드를 그룹으로 묶어서 집합을 각 배치별로 처리한다.
각 배치에 모든 레코드는 `person`에 동일한 값을 가지고 있기 때문에, 
데이터베이스 관리자가 임의의 값을 잡아서 집합된 `reading` 값과 함께 표시하는지는 더 이상 문제가 되지 않는다.


한번에 다중 기준으로 정렬하듯이 다중 기준으로 묶어 그룹화할 수 있다. 
예를 들어 과학자와 측정 수량에 따라 평균 측정값을 얻기 위해서, 
`group by` 절에 또다른 필드만 추가한다.


<pre class="in"><code>%%sqlite survey.db
select   person, quant, count(reading), round(avg(reading), 2)
from     Survey
group by person, quant;</code></pre>

<div class="out"><table>
<tr><td>None</td><td>sal</td><td>1</td><td>0.06</td></tr>
<tr><td>None</td><td>temp</td><td>1</td><td>-26.0</td></tr>
<tr><td>dyer</td><td>rad</td><td>2</td><td>8.81</td></tr>
<tr><td>dyer</td><td>sal</td><td>2</td><td>0.11</td></tr>
<tr><td>lake</td><td>rad</td><td>2</td><td>1.82</td></tr>
<tr><td>lake</td><td>sal</td><td>4</td><td>0.11</td></tr>
<tr><td>lake</td><td>temp</td><td>1</td><td>-16.0</td></tr>
<tr><td>pb</td><td>rad</td><td>3</td><td>6.66</td></tr>
<tr><td>pb</td><td>temp</td><td>2</td><td>-20.0</td></tr>
<tr><td>roe</td><td>rad</td><td>1</td><td>11.25</td></tr>
<tr><td>roe</td><td>sal</td><td>2</td><td>32.05</td></tr>
</table></div>


그렇지 않으면 결과가 의미가 없기 때문에, `person`을 표시되는 필드 리스트에 추가한 것을 주목하라.

한단계 더나아가 누가 측정을 했는지 알지 못하는 모든 항목을 제거하자.


<pre class="in"><code>%%sqlite survey.db
select   person, quant, count(reading), round(avg(reading), 2)
from     Survey
where    person is not null
group by person, quant
order by person, quant;</code></pre>

<div class="out"><table>
<tr><td>dyer</td><td>rad</td><td>2</td><td>8.81</td></tr>
<tr><td>dyer</td><td>sal</td><td>2</td><td>0.11</td></tr>
<tr><td>lake</td><td>rad</td><td>2</td><td>1.82</td></tr>
<tr><td>lake</td><td>sal</td><td>4</td><td>0.11</td></tr>
<tr><td>lake</td><td>temp</td><td>1</td><td>-16.0</td></tr>
<tr><td>pb</td><td>rad</td><td>3</td><td>6.66</td></tr>
<tr><td>pb</td><td>temp</td><td>2</td><td>-20.0</td></tr>
<tr><td>roe</td><td>rad</td><td>1</td><td>11.25</td></tr>
<tr><td>roe</td><td>sal</td><td>2</td><td>32.05</td></tr>
</table></div>


좀더 면밀하게 살펴보면, 이 쿼리는,

1.  `Survey`테이블에서 `person` 필드가 `null`이 아닌 레코드를 선택한다.

2.  상기 레코드를 부분집합으로 그룹지어서 각 부분집합의 `person`과 `quant`의 값은 같다.
    
3.  먼저 `person`으로 부분집합을 정렬하고나서 `quant`로 각 하위 그룹내에서도 정렬한다.

4.  각 부분집합의 레코드 숫자를 세고, 각각 `reading` 평균을 계산하고, 각각 `person`과 `quant` 값을 선택한다.
    (모두 동등하기 때문에 어느 것인지는 문제가 되지 않는다.)


#### 도전 과제

1.  Frank Pabodie는 얼마 많이 온도 측정치를 기록했고 평균 값은 얼마인가요?

2.  집합 값의 평균은 값을 합한 것을 값의 갯수로 나눈 것이다.
    값이 1.0, `null`, 5.0 으로 주어졌을 때, `avg` 함수는 2.0 혹은 3.0을 반환하는 것을 의미하나요?

3.  각 개별 방사선 측정값과 평균값 사이의 차이를 계산하고자 한다. 쿼리를 다음과 같이 작성한다.

    ~~~
    select reading - avg(reading) from Survey where quant='rad';
    ~~~

    상기 쿼리가 무엇을 만드나요? 그리고 왜 그런가요?

4.  `group_concat(field, separator)` 함수는 지정된 구분 문자(혹은 만약 구분자가 지정되지 않는다면 ',')를 사용하여 필드의 모든 값을 결합한다.
    이 함수르 사용해서 과학자의 이름을 한줄 리스트로 다음과 같이 만드세요.

    ~~~
    William Dyer, Frank Pabodie, Anderson Lake, Valentina Roerich, Frank Danforth
    ~~~

    성씨(surname)으로 리스트를 정렬하는 방법을 제시할 수 있나요?


<div class="keypoints" markdown="1">
#### 주요점

*   집합 함수는 많은 값을 조합해서 하나의 새로운 값을 만든다.
*   집합 함수는 `null` 값을 무시한다.
*   필터링 다음에 집합이 일어난다.
</div>


<pre class="in"><code></code></pre>
