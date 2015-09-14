---
layout: lesson
root: ../..
---

## 필터링 (Filtering)


<div class="objectives" markdown="1">
#### 목표

*   사용자가 정의한 조건을 만족하는 레코드를 선택하는 쿼리를 작성한다.
*   쿼리 절이 실행되는 순서를 설명한다.
</div>


데이터베이스의 가장 강력한 기능중 하나는 데이터를 [필터(filter)](../../gloss.html#filter)하는 능력이다.
즉, 특정 기준에 맞는 레코드만 선택한다. 예를 들어, 특정 사이트를 언제 방문했는지 확인한다고 가정하자.
쿼리에 `where` 절을 사용해서 `Visited` 테이블로부터 레코드를 뽑아낼 수 있다.


<pre class="in"><code>%load_ext sqlitemagic</code></pre>


<pre class="in"><code>%%sqlite survey.db
select * from Visited where site=&#39;DR-1&#39;;</code></pre>

<div class="out"><table>
<tr><td>619</td><td>DR-1</td><td>1927-02-08</td></tr>
<tr><td>622</td><td>DR-1</td><td>1927-02-10</td></tr>
<tr><td>844</td><td>DR-1</td><td>1932-03-22</td></tr>
</table></div>


데이터베이스 관리자는 두 단계로 나누어 쿼리를 실행한다.
첫번째로, `where` 절을 만족하는 것이 있는지 확인하기 위해서 
`Visited` 테이블의 각 행을 점검한다.
그리고 나서 무슨 칼럼을 표시할지 결정하기 위해서 `select` 키워드 다음에 있는 칼럼 이름을 사용한다.


이러한 처리 순서가 의미하는 바는 화면에 표시되지 않는 칼럼 값에 기반해서도 `where` 절을 사용해서 레코드를 필터링할 수 있다는 것이다.


<pre class="in"><code>%%sqlite survey.db
select ident from Visited where site=&#39;DR-1&#39;;</code></pre>

<div class="out"><table>
<tr><td>619</td></tr>
<tr><td>622</td></tr>
<tr><td>844</td></tr>
</table></div>


<img src="img/sql-filter.svg" alt="SQL Filtering in Action" />


데이터를 필터링하는데 불 연산자(Boolean Operators)를 사용할 수 있다. 예를 들어, 1930년 이후로 DR-1 사이트에서 수집된 모든 정보를 요청할 수도 있다.


<pre class="in"><code>%%sqlite survey.db
select * from Visited where (site=&#39;DR-1&#39;) and (dated&gt;=&#39;1930-00-00&#39;);</code></pre>

<div class="out"><table>
<tr><td>844</td><td>DR-1</td><td>1932-03-22</td></tr>
</table></div>


(각 테스트 주위의 괄호는 엄밀히 말해 필요하지는 않지만 쿼리를 좀더 읽기 쉽게 한다.)

> 대부분의 데이터베이스 관리자는 날짜에 대한 특별한 데이터 형식을 가진다.
> 사실 많이 있지만 두가지 형식으로 볼 수 있다. 날짜 데이터 형식의 하나는 "May 31, 1971"와 같은 것이고,
> 다른 하나는 "31 days" 같은 기간에 대한 것이다.
> SQLite는 구분하지는 않는다. 대신에 SQLite는 날짜를 텍스트 (ISO-8601 표준 형식 "YYYY-MM-DD HH:MM:SS.SSSS"), 혹은 
> 실수 (November 24, 4714 BCE 이후 지나간 일수), 혹은 정수 (1970년 1월 1일 자정 이후 초)로만 저장한다.
> 만약 복잡하게 들린다면, 그럴수도 있다 하지만 [스웨덴의 역사적인 날짜(historical dates in Sweden)](http://en.wikipedia.org/wiki/Swedish_calendar)를
> 이해하는 것만큼 복잡하는지는 않다.


Lake 혹은 Roerich가 무슨 측정을 했는지 알아내고자 한다면, `or`를 사용하여 이름에 테스트를 조합할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select * from Survey where person=&#39;lake&#39; or person=&#39;roe&#39;;</code></pre>

<div class="out"><table>
<tr><td>734</td><td>lake</td><td>sal</td><td>0.05</td></tr>
<tr><td>751</td><td>lake</td><td>sal</td><td>0.1</td></tr>
<tr><td>752</td><td>lake</td><td>rad</td><td>2.19</td></tr>
<tr><td>752</td><td>lake</td><td>sal</td><td>0.09</td></tr>
<tr><td>752</td><td>lake</td><td>temp</td><td>-16.0</td></tr>
<tr><td>752</td><td>roe</td><td>sal</td><td>41.6</td></tr>
<tr><td>837</td><td>lake</td><td>rad</td><td>1.46</td></tr>
<tr><td>837</td><td>lake</td><td>sal</td><td>0.21</td></tr>
<tr><td>837</td><td>roe</td><td>sal</td><td>22.5</td></tr>
<tr><td>844</td><td>roe</td><td>rad</td><td>11.25</td></tr>
</table></div>


다른 방식으로, `in`을 사용하여 특정 집합에 값이 있는지 확인할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select * from Survey where person in (&#39;lake&#39;, &#39;roe&#39;);</code></pre>

<div class="out"><table>
<tr><td>734</td><td>lake</td><td>sal</td><td>0.05</td></tr>
<tr><td>751</td><td>lake</td><td>sal</td><td>0.1</td></tr>
<tr><td>752</td><td>lake</td><td>rad</td><td>2.19</td></tr>
<tr><td>752</td><td>lake</td><td>sal</td><td>0.09</td></tr>
<tr><td>752</td><td>lake</td><td>temp</td><td>-16.0</td></tr>
<tr><td>752</td><td>roe</td><td>sal</td><td>41.6</td></tr>
<tr><td>837</td><td>lake</td><td>rad</td><td>1.46</td></tr>
<tr><td>837</td><td>lake</td><td>sal</td><td>0.21</td></tr>
<tr><td>837</td><td>roe</td><td>sal</td><td>22.5</td></tr>
<tr><td>844</td><td>roe</td><td>rad</td><td>11.25</td></tr>
</table></div>


`and`와 `or`를 조합할 수는 있지만, 어느 연산자가 먼저 수행되는지 주의할 필요가 있다.
만약 괄호를 사용하지 *않는다면*, 다음을 얻게 된다.


<pre class="in"><code>%%sqlite survey.db
select * from Survey where quant=&#39;sal&#39; and person=&#39;lake&#39; or person=&#39;roe&#39;;</code></pre>

<div class="out"><table>
<tr><td>734</td><td>lake</td><td>sal</td><td>0.05</td></tr>
<tr><td>751</td><td>lake</td><td>sal</td><td>0.1</td></tr>
<tr><td>752</td><td>lake</td><td>sal</td><td>0.09</td></tr>
<tr><td>752</td><td>roe</td><td>sal</td><td>41.6</td></tr>
<tr><td>837</td><td>lake</td><td>sal</td><td>0.21</td></tr>
<tr><td>837</td><td>roe</td><td>sal</td><td>22.5</td></tr>
<tr><td>844</td><td>roe</td><td>rad</td><td>11.25</td></tr>
</table></div>


상기 결과는 Lake가 측정한 염분량과 Roerich가 측정한 *임의* 측정값이다.
대신에 아마도 다음과 같은 결과를 얻고자 했을 것이다.


<pre class="in"><code>%%sqlite survey.db
select * from Survey where quant=&#39;sal&#39; and (person=&#39;lake&#39; or person=&#39;roe&#39;);</code></pre>

<div class="out"><table>
<tr><td>734</td><td>lake</td><td>sal</td><td>0.05</td></tr>
<tr><td>751</td><td>lake</td><td>sal</td><td>0.1</td></tr>
<tr><td>752</td><td>lake</td><td>sal</td><td>0.09</td></tr>
<tr><td>752</td><td>roe</td><td>sal</td><td>41.6</td></tr>
<tr><td>837</td><td>lake</td><td>sal</td><td>0.21</td></tr>
<tr><td>837</td><td>roe</td><td>sal</td><td>22.5</td></tr>
</table></div>


마지막으로 `distinct`와 `where`를 사용하여  두번째 수준의 필터링을 한다.


<pre class="in"><code>%%sqlite survey.db
select distinct person, quant from Survey where person=&#39;lake&#39; or person=&#39;roe&#39;;</code></pre>

<div class="out"><table>
<tr><td>lake</td><td>sal</td></tr>
<tr><td>lake</td><td>rad</td></tr>
<tr><td>lake</td><td>temp</td></tr>
<tr><td>roe</td><td>sal</td></tr>
<tr><td>roe</td><td>rad</td></tr>
</table></div>


하지만, 기억하라. `distinct`는 처리될 때 선택된 칼럼에 표시되는 값에만 적용되고 전체 행에는 적용되지 않는다.

> 방금전까지 수행하는 것은 대부분의 사람들이 어떻게 SQL 쿼리를 *증가*시키는지 살펴봤다.
> 의도한 것의 일부를 수행하는 단순한 것에서부터 시작했다.
> 그리고 절을 하나씩 하나씩 추가하면서 효과를 테스트했다. 좋은 전략이다. 사실 복잡한 쿼리를 작성할 때, 종종 *유일한* 전략이다.
> 하지만 이 전략은 빠른 회전(turnaround)시간에 달려있고 사용자에게는 정답을 얻게되면 인지하는 것에 달려있다.
> 빠른 회전시간을 달성하는 최선의 방법은 임시 데이터베이스에 데이터의 일부를 저장하고 쿼리를 실행하거나 혹은 
> 합성된 레코드로 작은 데이터베이스를 채워놓고 작업을 하는 것이다.
> 예를 들어, 2천만 호주사람의 실제 데이터베이스에 쿼리를 작업하지 말고,
> 1만명 샘플을 뽑아 쿼리를 돌리거나 작은 프로그램을 작성해서 랜덤으로 혹은 그럴듯한 1만명 레코드를 생성해서 사용한다.


#### 도전 과제

1.  극에서 30&deg보다 고위도에 위치한 모든 사이트를 선택하고자 한다고 가정하자.
    작성한 첫번째 쿼리는 다음과 같다.

    ~~~
    select * from Site where (lat > -60) or (lat < 60);
    ~~~

    왜 이 쿼리가 잘못된 것인지 설명하세요.
    그리고 쿼리를 다시 작성해서 올바르게 동작하게 만드세요.

2.  정규화된 염분 수치는 0.0에서 1.0 사이에 있어야 한다.
    상기 범위 밖에 있는 염분수치를 가진 모든 레코드를 `Survey` 테이블에서 선택하는 쿼리를 작성하세요.

3.  만약 명명된 칼럼의 값이 주어진 패턴과 일치한다면 SQL 테스트 `*column-name* like *pattern*`은 참이다.
    "0 혹은 그 이상의 문자와 매칭"된다는 것을 의미하기 위해서 '%'문자를 패턴에 임의 숫자 횟수에 사용한다.

    <table>
      <tr> <th>표현식</th> <th>값</th> </tr>
      <tr> <td><code>'a' like 'a'</code></td> <td>True</td> </tr>
      <tr> <td><code>'a' like '%a'</code></td> <td>True</td> </tr>
      <tr> <td><code>'b' like '%a'</code></td> <td>False</td> </tr>
      <tr> <td><code>'alpha' like 'a%'</code></td> <td>True</td> </tr>
      <tr> <td><code>'alpha' like 'a%p%'</code></td> <td>True</td> </tr>
    </table>
    
    표현식 `*column-name* not like *pattern*`은 테스트를 거꾸로 한다.
    `like`를 사용하여 사이트에서 'DR-something'으로 라벨이 붙지 *않은* 모든 레코드를 `Visited`에서 찾는 쿼리를 작성하세요.


<div class="keypoints" markdown="1">
#### 주요점

*   `where`를 사용해서 불 조건(Boolean conditions)에 따라 레코드를 필터링한다.
*   필터링이 전체 레코드에 적용되어서, 조건을 실제로 표시되지 않는 필드에 사용할 수 있다.
</div>


<pre class="in"><code></code></pre>
