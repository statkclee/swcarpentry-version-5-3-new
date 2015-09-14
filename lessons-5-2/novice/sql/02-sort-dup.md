---
layout: lesson
root: ../..
---

## 정렬하고 중복 제거하기


<div class="objectives" markdown="1">
#### 목표

*   특정 순서로 결과를 표시하는 쿼리를 작성한다.
*   데이터에서 중복값을 제거하는 쿼리를 작성한다.
</div>


데이터는 종종 잉여가 있어서, 쿼리도 종종 과잉 정보를 반환한다.
예를 들어, `survey` 테이블에서 측정된 수량 정보를 선택하면, 다음을 얻게된다.


<pre class="in"><code>%load_ext sqlitemagic</code></pre>


<pre class="in"><code>%%sqlite survey.db
select quant from Survey;</code></pre>

<div class="out"><table>
<tr><td>rad</td></tr>
<tr><td>sal</td></tr>
<tr><td>rad</td></tr>
<tr><td>sal</td></tr>
<tr><td>rad</td></tr>
<tr><td>sal</td></tr>
<tr><td>temp</td></tr>
<tr><td>rad</td></tr>
<tr><td>sal</td></tr>
<tr><td>temp</td></tr>
<tr><td>rad</td></tr>
<tr><td>temp</td></tr>
<tr><td>sal</td></tr>
<tr><td>rad</td></tr>
<tr><td>sal</td></tr>
<tr><td>temp</td></tr>
<tr><td>sal</td></tr>
<tr><td>rad</td></tr>
<tr><td>sal</td></tr>
<tr><td>sal</td></tr>
<tr><td>rad</td></tr>
</table></div>


결과를 좀더 읽을 수 있게 만들기 위해서 쿼리에 `distinct` 키워드를 추가해서 중복된 출력을 제거한다.


<pre class="in"><code>%%sqlite survey.db
select distinct quant from Survey;</code></pre>

<div class="out"><table>
<tr><td>rad</td></tr>
<tr><td>sal</td></tr>
<tr><td>temp</td></tr>
</table></div>


하나 이상의 칼럼(예를 들어 survey 사이트 ID와 측정된 수량)을 선택한다면, 별개로 구별된 값의 쌍이 반환된다.


<pre class="in"><code>%%sqlite survey.db
select distinct taken, quant from Survey;</code></pre>

<div class="out"><table>
<tr><td>619</td><td>rad</td></tr>
<tr><td>619</td><td>sal</td></tr>
<tr><td>622</td><td>rad</td></tr>
<tr><td>622</td><td>sal</td></tr>
<tr><td>734</td><td>rad</td></tr>
<tr><td>734</td><td>sal</td></tr>
<tr><td>734</td><td>temp</td></tr>
<tr><td>735</td><td>rad</td></tr>
<tr><td>735</td><td>sal</td></tr>
<tr><td>735</td><td>temp</td></tr>
<tr><td>751</td><td>rad</td></tr>
<tr><td>751</td><td>temp</td></tr>
<tr><td>751</td><td>sal</td></tr>
<tr><td>752</td><td>rad</td></tr>
<tr><td>752</td><td>sal</td></tr>
<tr><td>752</td><td>temp</td></tr>
<tr><td>837</td><td>rad</td></tr>
<tr><td>837</td><td>sal</td></tr>
<tr><td>844</td><td>rad</td></tr>
</table></div>


양쪽 경우에 설사 데이터베이스 내에서 서로 인접하지 않더라도 모두 중복이 제거된 것을 주목하세요.
다시 한번, 행은 실제로 정렬되지는 않았다는 것을 기억하세요. 단지 정렬된 것으로 화면에 출력된다.


#### 도전 과제

1.  `Site` 테이블에서 별개로 구별되는 날짜를 선택하는 쿼리를 작성하세요.


앞서 언급했듯이, 데이터베이스 레코드는 특별한 순서로 저장되지 않는다. 이것이 의미하는 바는 쿼리 결과가 반드시 정렬되어 있지 않다는 것이다.
설사 정렬이 되어 있더라도, 종종 다른 방식으로 정렬하고 싶을 것이다.
예를 들어 과학자의 이름 대신에 프로젝트 이름으로 정렬할 수도 있다. SQL에서 쿼리에 `order by` 절을 추가해서 간단하게 구현할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select * from Person order by ident;</code></pre>

<div class="out"><table>
<tr><td>danforth</td><td>Frank</td><td>Danforth</td></tr>
<tr><td>dyer</td><td>William</td><td>Dyer</td></tr>
<tr><td>lake</td><td>Anderson</td><td>Lake</td></tr>
<tr><td>pb</td><td>Frank</td><td>Pabodie</td></tr>
<tr><td>roe</td><td>Valentina</td><td>Roerich</td></tr>
</table></div>


디폴트로, 결과는 오름차순으로 정렬되어야 한다. (즉, 가장 적은 값에서 가장 큰 값 순으로 정렬된다.) 
`desc` ("descending")를 사용해서 역순으로도 정렬할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select * from person order by ident desc;</code></pre>

<div class="out"><table>
<tr><td>roe</td><td>Valentina</td><td>Roerich</td></tr>
<tr><td>pb</td><td>Frank</td><td>Pabodie</td></tr>
<tr><td>lake</td><td>Anderson</td><td>Lake</td></tr>
<tr><td>dyer</td><td>William</td><td>Dyer</td></tr>
<tr><td>danforth</td><td>Frank</td><td>Danforth</td></tr>
</table></div>


(그리고, `desc` 대신에 `asc`를 사용해서 오름차순으로 정렬하고 있다는 것을 명시적으로 표현할 수도 있다.)

한번에 여러 필드를 정렬할 수도 있다. 예를 들어, 다음 쿼리는 `taken` 필드를 오름차순으로 그리고 동일 그룹의 `taken` 값 내에서는 
`person`으로 내림차순으로 결과를 정렬한다.


<pre class="in"><code>%%sqlite survey.db
select taken, person from Survey order by taken asc, person desc;</code></pre>

<div class="out"><table>
<tr><td>619</td><td>dyer</td></tr>
<tr><td>619</td><td>dyer</td></tr>
<tr><td>622</td><td>dyer</td></tr>
<tr><td>622</td><td>dyer</td></tr>
<tr><td>734</td><td>pb</td></tr>
<tr><td>734</td><td>pb</td></tr>
<tr><td>734</td><td>lake</td></tr>
<tr><td>735</td><td>pb</td></tr>
<tr><td>735</td><td>None</td></tr>
<tr><td>735</td><td>None</td></tr>
<tr><td>751</td><td>pb</td></tr>
<tr><td>751</td><td>pb</td></tr>
<tr><td>751</td><td>lake</td></tr>
<tr><td>752</td><td>roe</td></tr>
<tr><td>752</td><td>lake</td></tr>
<tr><td>752</td><td>lake</td></tr>
<tr><td>752</td><td>lake</td></tr>
<tr><td>837</td><td>roe</td></tr>
<tr><td>837</td><td>lake</td></tr>
<tr><td>837</td><td>lake</td></tr>
<tr><td>844</td><td>roe</td></tr>
</table></div>


만약 중복을 제거한다면 이해하기가 더 쉽다.


<pre class="in"><code>%%sqlite survey.db
select distinct taken, person from Survey order by taken asc, person desc;</code></pre>

<div class="out"><table>
<tr><td>619</td><td>dyer</td></tr>
<tr><td>622</td><td>dyer</td></tr>
<tr><td>734</td><td>pb</td></tr>
<tr><td>734</td><td>lake</td></tr>
<tr><td>735</td><td>pb</td></tr>
<tr><td>735</td><td>None</td></tr>
<tr><td>751</td><td>pb</td></tr>
<tr><td>751</td><td>lake</td></tr>
<tr><td>752</td><td>roe</td></tr>
<tr><td>752</td><td>lake</td></tr>
<tr><td>837</td><td>roe</td></tr>
<tr><td>837</td><td>lake</td></tr>
<tr><td>844</td><td>roe</td></tr>
</table></div>


#### 도전 과제

1.  `Visited` 테이블에서 별개로 구별되는 날짜를 반환하는 쿼리를 작성하세요.

2.  성(family name)으로 정렬된 `Person` 테이블에 과학자의 성명 전부를 화면에 출력하는 쿼리를 작성하세요.


<div class="keypoints" markdown="1">
#### 주요점

*   데이터베이스 테이블의 레코드는 본질적으로 정렬되지 않는다.
    만약 특정 순서로 정렬하여 표시하려면, 명시적으로 정렬을 명기하여야 한다.
*   데이터베이스의 값이 유일(unique)함을 보장하지는 않는다. 
    만약 중복을 제거하고자 한다면, 명시적으로 유일함을 명기하여야 한다.
</div>


<pre class="in"><code></code></pre>
