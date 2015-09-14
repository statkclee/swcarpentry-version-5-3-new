---
layout: lesson
root: ../..
---

## 데이터 조합하기 (Combining Data)


<div class="objectives" markdown="1">
#### 목표

*   두 테이블을 조인(join)하는 쿼리 연산을 설명한다.
*   의미있는 값의 조합만 포함하기 위해서 조인문을 포함하는 쿼리 결과를 어떻게 제한하는지 설명한다.
*   동일한 키를 갖는 테이블을  조인하는 쿼리를 작성한다.
*   기본키(primary key)와 외래키(foreign key)가 무엇인지 그리고 왜 유용한지 설명한다.
*   원자값(atomic value)이 무엇이고, 왜 데이터베이스 필드는 원자값만 포함해야하는지 설명한다.
</div>


과거 기상 자료를 집계하는 웹사이트에 데이터를 제출해야 되어서, 
Gina는 위도, 경도, 날짜, 수량, 측정값 형식으로 자료를 체계적으로 만들 필요가 있다.
하지만, 위도와 경도 정보는 `Site` 테이블에 있는 반면에 측정 날짜 정보는 `Visited` 테이블에 있고,
측정값 자체는 `Survey` 테이블에 있다.
어떤 방식이든지 상기 테이블을 조합할 필요가 있다.

이러한 작업을 하는 SQL 명령어가 `join`이다. 어떻게 동작하는지 확인하기 위해서,
`Site`와 `Visited` 테이블을 조인하면서 출발해보자.


<pre class="in"><code>%load_ext sqlitemagic</code></pre>


<pre class="in"><code>%%sqlite survey.db
select * from Site join Visited;</code></pre>

<div class="out"><table>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>619</td><td>DR-1</td><td>1927-02-08</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>622</td><td>DR-1</td><td>1927-02-10</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>734</td><td>DR-3</td><td>1939-01-07</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>735</td><td>DR-3</td><td>1930-01-12</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>751</td><td>DR-3</td><td>1930-02-26</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>752</td><td>DR-3</td><td>None</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>837</td><td>MSK-4</td><td>1932-01-14</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>844</td><td>DR-1</td><td>1932-03-22</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>619</td><td>DR-1</td><td>1927-02-08</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>622</td><td>DR-1</td><td>1927-02-10</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>734</td><td>DR-3</td><td>1939-01-07</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>735</td><td>DR-3</td><td>1930-01-12</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>751</td><td>DR-3</td><td>1930-02-26</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>752</td><td>DR-3</td><td>None</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>837</td><td>MSK-4</td><td>1932-01-14</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>844</td><td>DR-1</td><td>1932-03-22</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>619</td><td>DR-1</td><td>1927-02-08</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>622</td><td>DR-1</td><td>1927-02-10</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>734</td><td>DR-3</td><td>1939-01-07</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>735</td><td>DR-3</td><td>1930-01-12</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>751</td><td>DR-3</td><td>1930-02-26</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>752</td><td>DR-3</td><td>None</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>837</td><td>MSK-4</td><td>1932-01-14</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>844</td><td>DR-1</td><td>1932-03-22</td></tr>
</table></div>


`join`은 두 테이블을 [외적(cross product)](../../gloss.html#cross-product)한다.
즉, 모든 가능한 조합을 표현하려고 한 테이블의 레코드 각각마다 다른 테이블의 각 레코드와 조인한다.
`Site` 테이블에 3개 레코드가 있고, `Visited` 테이블에 8개 레코드가 있어서,
조인된 결과는 24개 레코드가 된다. 그리고, 각 테이블이 3개 필드가 있어서 출력은 6개의 필드가 된다.

조인이 수행하지 *않은* 것은 조인되는 레코드가 서로 관계가 있는지를 파악하는 것이다.
어떻게 조인할지 명시할 때까지 레코드가 서로 관계가 있는지 없는지 알 수 있는 방법은 없다.
이를 위해서 동일한 사이트 이름을 가진 조합에만 관심있다는 것을 명시하는 절(clause)을 추가한다.


<pre class="in"><code>%%sqlite survey.db
select * from Site join Visited on Site.name=Visited.site;</code></pre>

<div class="out"><table>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>619</td><td>DR-1</td><td>1927-02-08</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>622</td><td>DR-1</td><td>1927-02-10</td></tr>
<tr><td>DR-1</td><td>-49.85</td><td>-128.57</td><td>844</td><td>DR-1</td><td>1932-03-22</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>734</td><td>DR-3</td><td>1939-01-07</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>735</td><td>DR-3</td><td>1930-01-12</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>751</td><td>DR-3</td><td>1930-02-26</td></tr>
<tr><td>DR-3</td><td>-47.15</td><td>-126.72</td><td>752</td><td>DR-3</td><td>None</td></tr>
<tr><td>MSK-4</td><td>-48.87</td><td>-123.4</td><td>837</td><td>MSK-4</td><td>1932-01-14</td></tr>
</table></div>


`on` 은  `where`와 같은 역할을 한다. 특정 테스트를 통과한 레코드만 간직한다.
(`on`과 `where`의 차이점은 `on`은 레코드가 생성될 때 레코드를 필터링하는 반면에, `where`는 조인작업이 완료될 때까지 기다리고 난 뒤에 필터링을 한다.)
쿼리에 레코드를 추가하자 마자 데이터베이스 관리자는 두 다른 사이트에 관한 조합된 정보는 사용한 뒤에 버려버리고, 원하는 레코드만 남겨둔다.

조인 결과에 필드이름을 명기하기 위해서 `table.field`를 사용한 것에 주목하세요.
이렇게 하는 이유는 테이블이 동일한 이름을 가질 수 있고 어느 필드를 언급하는지 좀더 구체성을 띌 필요가 있다.
예를 들어, `person`과 `visited` 테이블을 조인한다면, 결과는 각각의 원래 테이블에서 `ident`로 불리는 필드를 상속한다.

이제는 조인에서 원하는 3개의 칼럼을 선택하려고 점 표기법(dotted notation)을 사용할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select Site.lat, Site.long, Visited.dated
from   Site join Visited
on     Site.name=Visited.site;</code></pre>

<div class="out"><table>
<tr><td>-49.85</td><td>-128.57</td><td>1927-02-08</td></tr>
<tr><td>-49.85</td><td>-128.57</td><td>1927-02-10</td></tr>
<tr><td>-49.85</td><td>-128.57</td><td>1932-03-22</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>None</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-01-12</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-02-26</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1939-01-07</td></tr>
<tr><td>-48.87</td><td>-123.4</td><td>1932-01-14</td></tr>
</table></div>


만약 두개의 테이블을 조인하는 것이 좋은 경우에, 많은 데이블을 조인하는 것은 더 좋아야한다.
더 많은 `join` 절과 의미없는 레코드 조합을 필터링해서 제거하는 더 많은 `on` 테스트를 단순히 추가해서 사실 쿼리에 임의 갯수의 테이블을 조인할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select Site.lat, Site.long, Visited.dated, Survey.quant, Survey.reading
from   Site join Visited join Survey
on     Site.name=Visited.site
and    Visited.ident=Survey.taken
and    Visited.dated is not null;</code></pre>

<div class="out"><table>
<tr><td>-49.85</td><td>-128.57</td><td>1927-02-08</td><td>rad</td><td>9.82</td></tr>
<tr><td>-49.85</td><td>-128.57</td><td>1927-02-08</td><td>sal</td><td>0.13</td></tr>
<tr><td>-49.85</td><td>-128.57</td><td>1927-02-10</td><td>rad</td><td>7.8</td></tr>
<tr><td>-49.85</td><td>-128.57</td><td>1927-02-10</td><td>sal</td><td>0.09</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1939-01-07</td><td>rad</td><td>8.41</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1939-01-07</td><td>sal</td><td>0.05</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1939-01-07</td><td>temp</td><td>-21.5</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-01-12</td><td>rad</td><td>7.22</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-01-12</td><td>sal</td><td>0.06</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-01-12</td><td>temp</td><td>-26.0</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-02-26</td><td>rad</td><td>4.35</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-02-26</td><td>sal</td><td>0.1</td></tr>
<tr><td>-47.15</td><td>-126.72</td><td>1930-02-26</td><td>temp</td><td>-18.5</td></tr>
<tr><td>-48.87</td><td>-123.4</td><td>1932-01-14</td><td>rad</td><td>1.46</td></tr>
<tr><td>-48.87</td><td>-123.4</td><td>1932-01-14</td><td>sal</td><td>0.21</td></tr>
<tr><td>-48.87</td><td>-123.4</td><td>1932-01-14</td><td>sal</td><td>22.5</td></tr>
<tr><td>-49.85</td><td>-128.57</td><td>1932-03-22</td><td>rad</td><td>11.25</td></tr>
</table></div>


`Site`, `Visited`, `Survey` 테이블의 어느 레코드가 서로 대응되지는 분간할 수 있는데 이유는 각 테이블이 
[기본키(primary keys)](../../gloss.html#primary-key)와 [외래키(foreign keys)](../../gloss.html#foreign-key)를 가지고 있기 때문이다..
기본키는 하나의 값 혹은 여러 값의 조합으로 테이블의 각 레코드를 유일하게 식별한다.
외래키는 또 다른 테이블에 있는 유일하게 레코드를 식별하는 하나의 값(혹은 여러 값의 조합)이다.
다르게 표현하면, 외래캐는 다른 테이블에 존재하는 테이블의 기본키다.
예제 데이터베이스에서 `Person.ident`는 `Person` 테이블의 기본키인 반면에,
`Survey.person`은 외래키로 `Survey` 테이블의 항목과 `Person` 테이블의 항목을 연결한다.

대부분의 데이터베이스 디자이너는 모든 테이블은 잘 정의된 기본키가 있어야된다고 믿는다.
또한 이 키는 데이터와 떨어져서 만약 데이터를 변경할 필요가 있다면, 한 곳의 변경이 한 곳에만 변경을 만들어야만 한다.
이를 위한 쉬운 방법은 데이터베이스에 레코드를 추가할 때 임의의 유일한 ID를 각 레코드마다 추가하는 것이다.
실제로 이방법은 매우 흔하게 사용된다. "student numbers", "patient numbers" 같은 이름을 ID로 사용하고,
몇몇 데이터베이스 시스템 혹은 다른 곳에서 원래 고유 레코드 식별자로 거의 항상 판명된다.
다음 쿼리가 시범으로 보여주듯이, 테이블에 레코드가 추가됨에 따라 SQLite는 자동으로 레코드에 숫자를 붙이고, 쿼리에서 이렇게 붙여진 레코드 숫자를 사용한다.


<pre class="in"><code>%%sqlite survey.db
select rowid, * from Person;</code></pre>

<div class="out"><table>
<tr><td>1</td><td>dyer</td><td>William</td><td>Dyer</td></tr>
<tr><td>2</td><td>pb</td><td>Frank</td><td>Pabodie</td></tr>
<tr><td>3</td><td>lake</td><td>Anderson</td><td>Lake</td></tr>
<tr><td>4</td><td>roe</td><td>Valentina</td><td>Roerich</td></tr>
<tr><td>5</td><td>danforth</td><td>Frank</td><td>Danforth</td></tr>
</table></div>

### 데이터 위생 (Data Hygiene)


지금까지 조인이 어떻게 동작하는지 살펴봤으니, 왜 관계형 모델이 그렇게 유용한지 그리고 어떻게 가장 잘 사용할 수 있는지 살펴보자.
첫번째 규칙은 모든 값은 독립 요소로 분해될 수 없는 [원자(atomic)](../../gloss.html#atomic-value)적 속성을 지녀야 한다.
즉, 구별해서 작업하고자 하는 부분을 포함해서는 안된다. 하나의 칼럼에 전체 이름을 넣는 대신에
별도로 구별되는 칼럼에 이름과 성을 저장해서 이름 컴포넌트를 뽑아내는 부분 문자열 연산(substring operation)을 사용할 필요가 없다.
좀더 중요하게는, 별도로 이름을 두 부분으로 저장한다. 왜냐하면, 공백으로 쪼개는 것은 신뢰성이 약하다.
"Eloise St. Cyr" 혹은 "Jan Mikkel Steubart" 같은 이름을 생각하면 쉽게 알 수 있다.

두번째 규칙은 모든 레코드는 유일한 기본키를 가져야한다. 내재적인 의미가 전혀없는 일련번호가 될 수 있고, 레코드의 값중의 하나 
(`Person` 테이블의 `ident` 필드), 혹은 `Survey` 테이블에서 심지어 모든 측정값을 유일하게 식별하는 `(taken, person, quant)` 삼중값의 조합도 될 수 있다. 

세번째 규칙은 불필요한 정보가 없어야 한다. 예를 들어, `Site`테이블을 제거하고 다음과 같이 `Visited` 테이블을 다시 작성할 수 있다.

<table>
  <tr> <td>619</td> <td>-49.85</td> <td>-128.57</td> <td>1927-02-08</td> </tr>
  <tr> <td>622</td> <td>-49.85</td> <td>-128.57</td> <td>1927-02-10</td> </tr>
  <tr> <td>734</td> <td>-47.15</td> <td>-126.72</td> <td>1939-01-07</td> </tr>
  <tr> <td>735</td> <td>-47.15</td> <td>-126.72</td> <td>1930-01-12</td> </tr>
  <tr> <td>751</td> <td>-47.15</td> <td>-126.72</td> <td>1930-02-26</td> </tr>
  <tr> <td>752</td> <td>-47.15</td> <td>-126.72</td> <td>null</td> </tr>
  <tr> <td>837</td> <td>-48.87</td> <td>-123.40</td> <td>1932-01-14</td> </tr>
  <tr> <td>844</td> <td>-49.85</td> <td>-128.57</td> <td>1932-03-22</td> </tr>
</table>

사실, 스프레드쉬트와 마찬가지로 각 행에 각 측정값에 관한 모든 정보를 기록하는 하나의 테이블을 사용할 수도 있다.
문제는 이와 같은 방식으로 조직된 데이터를 일관성있게 관리하는 것은 매우 어렵다.
만약 특정한 사이트의 특정한 방문 날짜가 잘못된다면, 데이터베이스에 다수의 레코드를 변경해야한다.
더 안좋은 것은 다른 사이트도 그 날짜에 방문되었기 때문에 어느 레코드를 변경할지 추정해야하는 것이다.

네번째 규칙은 모든 값의 단위는 명시적으로 저장되어야한다. 예제 데이터베이스는 그렇지 못해서 문제다.

Roerich의 염분치는 다른 사람의 측정치보다 수천배 크다. 하지만, 천단위 대신에 백만 단위를 사용하고 있는지 혹은 1932년
그 사이트에 염분에 이상 실제로 있었는지 알지못한다.

한걸음 물러나서 생각하자, 데이터와 저장하는데 사용되는 도구는 공생관계다. 테이블과 조인은 데이터가 특정 방식으로 잘 조직되었다면 매우 효과적이다.
하지만, 만약 특정 형태로 되어 있다면 효과적으로 다룰 수 있는 도구가 있기 때문에 데이터를 그와 같은 방식으로 조직하기도 한다.
인류학자가 말했듯이, 도구는 도구를 만드는 손을 만든다. (the tool shapes the hand that shapes the tool)


#### 도전 과제

1.  DR-1 사이트의 모든 방사선 측정치를 출력하는 쿼리를 작성하세요.

2.  "Frank" 가 방문한 모든 사이트를 출력하는 쿼리를 작성하세요.

3.  다음 쿼리가 무슨 결과를 산출하는지 말로 기술하세요.

    ~~~
    select Site.name from Site join Visited
    on Site.lat<-49.0 and Site.name=Visited.site and Visited.dated>='1932-00-00';
    ~~~


<div class="keypoints" markdown="1">
#### 주요점

*   모든 사실은 데이터베이스에서 정확하게 한번만 표현되어야 한다.
*   조인은 한 테이블의 레코드와 다른 테이블의 레코드를 모두 조합한 결과를 출력한다.
*   기본키는 테이블의 레코드를 유일하게 식별하는 필드값(혹은 필드의 집합)이다.
*   외래키는 또 다른 테이블의 기본키가되는 필드값(혹은 필드의 집합)이다.
*   테이블사이에 기본키와 외래키를 매칭해서 의미없는 레코드의 조합을 제거할 수 있다.
*   조인을 좀더 단순하고 효율적으로 만들기 위해서 키(key)는 원자값(atomic value)이 되어야 한다.
</div>


<pre class="in"><code></code></pre>
