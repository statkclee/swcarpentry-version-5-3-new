---
layout: lesson
root: ../..
---

## 데이터 선택하기


1920년 후반, 1930년 초반 William Dyer, Frank Pabodie,Valentina Roerich는 남태평양 [도달불가능한 극(Pole of Inaccessibility)](http://en.wikipedia.org/wiki/Pole_of_inaccessibility)과 이어서 남극 대륙을 탐험했다.
2년 전에 이들의 탐험 기록이 Miskatonic 대학 창고 사물함에서 발견됐다. 
기록을 스캔해서 OCR로 저장했고, 이제는 검색가능하고 분석이 용히한 방식으로 정보를 저장하고자 한다.

기본적으로 3가지 선택 옵션(텍스트 파일, 스프레드쉬트, 데이터베이스)이 있다. 
텍스트 파일은 생성하기 가장 쉽고 버젼 제어와 궁합이 맞지만, 검색과 분석 도구를 별도로 구축해야한다.
스프레드쉬트는 단순한 분석에는 적합하지만, 크고 복잡한 데이터셋을 매우 잘 다루지는 못한다.
그래서 데이터를 데이터베이스에 넣어서 어떻게 검색과 분석을 하는지 이번 학습에서 배울 것이다.


<div class="objectives" markdown="1">
#### 목표

*   테이블, 레코드, 필드의 차이점을 설명한다.
*   데이터베이스와 데이터베이스 관리자의 차이를 설명한다.
*   단독 테이블에서 특정 필드에 있는 모든 값을 선택하는 질의문(쿼리, query)를 작성한다.
</div>

### 정의 몇가지


[관계형 데이터베이스(relational database)](../../gloss.html#relational-database)는 
[테이블(tables)](../../gloss.html#table)로 정렬된 정보를 저장하고 다루는 방식이다.
각 테이블은 데이터를 기술하는 [필드(fields)](../../gloss.html#field)로도 알려진 열(column)과
데이터를 담고 있는 [레코드(records)](../../gloss.html#record)로 알려진 행(row)으로 구성된다.

스프레드쉬트를 사용할 때, 이전 값에 기초하여 새로운 값을 계산할 때 공식을 셀(cell)에 넣어서 구한다.
데이터베이스를 사용할 때는 [쿼리(queries, 질의)](../../gloss.html#query)로 불리는 명령문을 
[데이터베이스 관리자(database manager)](../../gloss.html#database-manager)에게 보낸다.
데이터베이스 관리자는 사용자를 대신해서 데이터베이스를 다루는 프로그램이다.
데이터베이스 관리자는 쿼리가 명기하는 임의의 조회와 계산을 수행하고 다음 쿼리의 시작점으로 사용될 수 있는
테이블 형식으로 결과값을 반환한다.

> 모든 데이터베이스 관리자(IBM DB2, PostgreSQL, MySQL, Microsoft Access, SQLite)는
> 서로 다른 고유한 방식으로 데이터를 저장해서 한곳에서 생성된 데이터베이스는 다른 곳의 데이터베이스에서
> 직접적으로 사용될 수 없다. 하지만, 모든 데이터베이스 관리자는 데이터를 다양한 형식으로 가져오기(import)와 내보내기(export)를 지원한다.
> 그래서 한 곳에서 다른 곳으로 정보를 이동하는 것이 *가능하다*.

쿼리는 [SQL](../../gloss.html#sql)로 불리는 언어로 작성된다.
SQL 은 "Structured Query Language"(구조적 질의 언어)의 약자다.
SQL은 데이터를 분석하고 다시 조합할 수 있는 수백개의 다른 방식을 제공한다.
학습에서 일부를 살펴볼 것이지만, 이 일부가 과학자가 수행하는 일의 대부분을 처리할 것이다.

다음 테이블은 예제로 사용할 데이터베이스를 보여준다.


<table>
<tr>
<td valign="top">
<strong>Person</strong>: 판독한 사람.

<table>
  <tr> <th>ident</th> <th>personal</th> <th>family</th> </tr>
  <tr> <td>dyer</td> <td>William</td> <td>Dyer</td> </tr>
  <tr> <td>pb</td> <td>Frank</td> <td>Pabodie</td> </tr>
  <tr> <td>lake</td> <td>Anderson</td> <td>Lake</td> </tr>
  <tr> <td>roe</td> <td>Valentina</td> <td>Roerich</td> </tr>
  <tr> <td>danforth</td> <td>Frank</td> <td>Danforth</td> </tr>
</table>

<strong>Site</strong>: 판독한 장소.

<table>
  <tr> <th>name</th> <th>lat</th> <th>long</th> </tr>
  <tr> <td>DR-1</td> <td>-49.85</td> <td>-128.57</td> </tr>
  <tr> <td>DR-3</td> <td>-47.15</td> <td>-126.72</td> </tr>
  <tr> <td>MSK-4</td> <td>-48.87</td> <td>-123.4</td> </tr>
</table>

<strong>Visited</strong>: 특정 사이트에서 판독한 시점.

<table>
  <tr> <th>ident</th> <th>site</th> <th>dated</th> </tr>
  <tr> <td>619</td> <td>DR-1</td> <td>1927-02-08</td> </tr>
  <tr> <td>622</td> <td>DR-1</td> <td>1927-02-10</td> </tr>
  <tr> <td>734</td> <td>DR-3</td> <td>1939-01-07</td> </tr>
  <tr> <td>735</td> <td>DR-3</td> <td>1930-01-12</td> </tr>
  <tr> <td>751</td> <td>DR-3</td> <td>1930-02-26</td> </tr>
  <tr> <td>752</td> <td>DR-3</td> <td bgcolor="red">&nbsp;</td> </tr>
  <tr> <td>837</td> <td>MSK-4</td> <td>1932-01-14</td> </tr>
  <tr> <td>844</td> <td>DR-1</td> <td>1932-03-22</td> </tr>
</table>
</td>
<td valign="top">
<strong>Survey</strong>: 실제 판독.

<table>
  <tr> <th>taken</th> <th>person</th> <th>quant</th> <th>reading</th> </tr>
  <tr> <td>619</td> <td>dyer</td> <td>rad</td> <td>9.82</td> </tr>
  <tr> <td>619</td> <td>dyer</td> <td>sal</td> <td>0.13</td> </tr>
  <tr> <td>622</td> <td>dyer</td> <td>rad</td> <td>7.8</td> </tr>
  <tr> <td>622</td> <td>dyer</td> <td>sal</td> <td>0.09</td> </tr>
  <tr> <td>734</td> <td>pb</td> <td>rad</td> <td>8.41</td> </tr>
  <tr> <td>734</td> <td>lake</td> <td>sal</td> <td>0.05</td> </tr>
  <tr> <td>734</td> <td>pb</td> <td>temp</td> <td>-21.5</td> </tr>
  <tr> <td>735</td> <td>pb</td> <td>rad</td> <td>7.22</td> </tr>
  <tr> <td>735</td> <td bgcolor="red">&nbsp;</td> <td>sal</td> <td>0.06</td> </tr>
  <tr> <td>735</td> <td bgcolor="red">&nbsp;</td> <td>temp</td> <td>-26.0</td> </tr>
  <tr> <td>751</td> <td>pb</td> <td>rad</td> <td>4.35</td> </tr>
  <tr> <td>751</td> <td>pb</td> <td>temp</td> <td>-18.5</td> </tr>
  <tr> <td>751</td> <td>lake</td> <td>sal</td> <td>0.1</td> </tr>
  <tr> <td>752</td> <td>lake</td> <td>rad</td> <td>2.19</td> </tr>
  <tr> <td>752</td> <td>lake</td> <td>sal</td> <td>0.09</td> </tr>
  <tr> <td>752</td> <td>lake</td> <td>temp</td> <td>-16.0</td> </tr>
  <tr> <td>752</td> <td>roe</td> <td>sal</td> <td>41.6</td> </tr>
  <tr> <td>837</td> <td>lake</td> <td>rad</td> <td>1.46</td> </tr>
  <tr> <td>837</td> <td>lake</td> <td>sal</td> <td>0.21</td> </tr>
  <tr> <td>837</td> <td>roe</td> <td>sal</td> <td>22.5</td> </tr>
  <tr> <td>844</td> <td>roe</td> <td>rad</td> <td>11.25</td> </tr>
</table>
</td>
</tr>
</table>


3개 항목 (`Visited` 테이블에서 1개, `Survey` 테이블에서 2개) 은 붉은색으로 표기한 것을 주목하라.
왜냐하면 어떠한 값도 담고 있지 않아서 그렇다. [뒤에](#s:null) 결측값(missing)을 다시 다룰 것이다.
지금으로서는 과학자의 이름을 화면에 표시하는 SQL을 작성하자. 
SQL `select` 문을 사용해서 원하는 칼럼이름과 원하는 테이블이름을 준다.
쿼리와 결과는 다음과 같다.


<pre class="in"><code>%load_ext sqlitemagic</code></pre>


<pre class="in"><code>%%sqlite survey.db
select family, personal from Person;</code></pre>

<div class="out"><table>
<tr><td>Dyer</td><td>William</td></tr>
<tr><td>Pabodie</td><td>Frank</td></tr>
<tr><td>Lake</td><td>Anderson</td></tr>
<tr><td>Roerich</td><td>Valentina</td></tr>
<tr><td>Danforth</td><td>Frank</td></tr>
</table></div>


쿼리 끝에 세미콜론(`;`)은 쿼리가 완료되어 실행준비 되었다고 데이터베이스 관리자에게 알려준다.
명령문과 칼럼 이름을 모두 소문자로 작성했고, 테이블 이름은 타이틀 케이스(Title Case, 단어의 첫 문자를 대문자로 표기)로 작성했다.
하지만 그렇게 반듯이 할 필요는 없다. 아래 예제가 보여주듯이, SQL은 [대소문자 구분하지 않는다. (case insensitive)](../../gloss.html#case-insensitive)


<pre class="in"><code>%%sqlite survey.db
SeLeCt FaMiLy, PeRsOnAl FrOm PeRsOn;</code></pre>

<div class="out"><table>
<tr><td>Dyer</td><td>William</td></tr>
<tr><td>Pabodie</td><td>Frank</td></tr>
<tr><td>Lake</td><td>Anderson</td></tr>
<tr><td>Roerich</td><td>Valentina</td></tr>
<tr><td>Danforth</td><td>Frank</td></tr>
</table></div>


모두 소문자, 타이틀 케이스, 소문자 낙타 대문자(Lower Camel Case)를 선택하든지 관계없이 일관성을 가져라.
랜덤 대문자를 추가적으로 인지하지 않더라고 복잡한 쿼리는 충분히 그 자체로 이해하기 어렵다.


쿼리로 돌아가서, 데이터베이스 테이블의 행과 열이 특정한 순서로 저장되지 않는다는 것을 이해하는 것이 중요하다.
어떤 순서로 항상 *표시되지만*, 다양한 방식으로 제어할 수 있다.
예를 들어, 쿼리를 다음과 같이 작성해서 칼럼을 교환할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select personal, family from Person;</code></pre>

<div class="out"><table>
<tr><td>William</td><td>Dyer</td></tr>
<tr><td>Frank</td><td>Pabodie</td></tr>
<tr><td>Anderson</td><td>Lake</td></tr>
<tr><td>Valentina</td><td>Roerich</td></tr>
<tr><td>Frank</td><td>Danforth</td></tr>
</table></div>


혹은 심지어 칼럼을 반복할 수도 있다.


<pre class="in"><code>%%sqlite survey.db
select ident, ident, ident from Person;</code></pre>

<div class="out"><table>
<tr><td>dyer</td><td>dyer</td><td>dyer</td></tr>
<tr><td>pb</td><td>pb</td><td>pb</td></tr>
<tr><td>lake</td><td>lake</td><td>lake</td></tr>
<tr><td>roe</td><td>roe</td><td>roe</td></tr>
<tr><td>danforth</td><td>danforth</td><td>danforth</td></tr>
</table></div>


손쉬운 방법으로, `*`을 사용해서 테이블의 모든 칼럼을 선택할 수도 있다.


<pre class="in"><code>%%sqlite survey.db
select * from Person;</code></pre>

<div class="out"><table>
<tr><td>dyer</td><td>William</td><td>Dyer</td></tr>
<tr><td>pb</td><td>Frank</td><td>Pabodie</td></tr>
<tr><td>lake</td><td>Anderson</td><td>Lake</td></tr>
<tr><td>roe</td><td>Valentina</td><td>Roerich</td></tr>
<tr><td>danforth</td><td>Frank</td><td>Danforth</td></tr>
</table></div>


#### 도전 과제

1.  `Site` 테이블에서 사이트 이름만 선택하는 쿼리를 작성하세요.

2.  많은 사람들이 쿼리를 다음과 같은 형식으로 작성한다.

    ~~~
    SELECT personal, family FROM person;
    ~~~

    혹은 다음과 같이도 작성한다.

    ~~~
    select Personal, Family from PERSON;
    ~~~

    읽기 쉽기 쉬운 스타일은 어느 것인가요? 이유는 무엇일까요?


<div class="keypoints" markdown="1">
#### 주요점

*   관계형 데이터베이스는 정보를 테이블로 저장한다. 고정된 숫자의 칼럼과 변하기 쉬운 숫자의 레코드로 구성된다.    
*   데이터베이스 관리자는 데이터베이스에 저장된 정보를 다루는 프로그램이다.
*   데이터베이스에서 정보를 추출하는데 SQL이라고 불리는 특화된 언어로 쿼리를 작성한다.
*   SQL은 대소문자를 구별하지 않는다.
</div>


<pre class="in"><code></code></pre>
