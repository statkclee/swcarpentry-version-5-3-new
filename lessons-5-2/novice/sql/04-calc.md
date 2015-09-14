---
layout: lesson
root: ../..
---

## 새로운 값 계산하기


<div class="objectives" markdown="1">
#### 목표

*   각 선택된 레코드에 대해 새로운 값을 계산하는 쿼리를 작성한다.
</div>


주의깊이 탐험 기록을 다시 정독한 뒤에, 탐험대가 보고한 방사선 측정치가 5%만큼 상향되어 수정될 필요가 있다는 것을 깨달았다.
저장된 데이터를 변형하기 보다는 쿼리의 일부분으로서 즉석에서 계산을 수행할 수 있다.


<pre class="in"><code>%load_ext sqlitemagic</code></pre>


<pre class="in"><code>%%sqlite survey.db
select 1.05 * reading from Survey where quant=&#39;rad&#39;;</code></pre>

<div class="out"><table>
<tr><td>10.311</td></tr>
<tr><td>8.19</td></tr>
<tr><td>8.8305</td></tr>
<tr><td>7.581</td></tr>
<tr><td>4.5675</td></tr>
<tr><td>2.2995</td></tr>
<tr><td>1.533</td></tr>
<tr><td>11.8125</td></tr>
</table></div>


쿼리를 실행하면, 표현식 `1.05 * reading`이 각 행마다 평가된다.
표현식에는 임의의 필드, 통상 많이 사용되는 연산자, 그리고 다양한 함수를 사용한다.
(정확하게는 어느 데이터베이스 관리자를 사용되느냐에 따라 의존성을 띄게된다.)
예를 들어, 온도 측정치를 화씨에서 섭씨로 소수점 아래 두자리에서 반올림하여 변환할 수 있다.


<pre class="in"><code>%%sqlite survey.db
select taken, round(5*(reading-32)/9, 2) from Survey where quant=&#39;temp&#39;;</code></pre>

<div class="out"><table>
<tr><td>734</td><td>-29.72</td></tr>
<tr><td>735</td><td>-32.22</td></tr>
<tr><td>751</td><td>-28.06</td></tr>
<tr><td>752</td><td>-26.67</td></tr>
</table></div>


다른 필드의 값을 조합할 수도 있다. 예를 들어, 문자열 접합 연산자 (string concatenation operator, `||`)를 사용한다. 


<pre class="in"><code>%%sqlite survey.db
select personal || &#39; &#39; || family from Person;</code></pre>

<div class="out"><table>
<tr><td>William Dyer</td></tr>
<tr><td>Frank Pabodie</td></tr>
<tr><td>Anderson Lake</td></tr>
<tr><td>Valentina Roerich</td></tr>
<tr><td>Frank Danforth</td></tr>
</table></div>


> `first`와 `last` 대신에 필드 이름으로 `personal`과 `family`를 사용하는 것이 이상해 보일지 모른다.
> 하지만, 문화적 차이를 다루기 위한 필요한 첫번째 단계다. 예를 들어, 다음 규칙을 고려해보자.

<table>
  <tr> <th>성명 전부(Full Name)</th> <th>알파벳 순서</th> <th>이유</th> </tr>
  <tr> <td>Liu Xiaobo</td> <td>Liu</td> <td>중국 성이 이름보다 먼저 온다.</td> </tr>
  <tr> <td> Leonardo da Vinci</td> <td>Leonardo</td> <td>"da Vinci" 는 "from Vinci"를 뜻한다.</td> </tr>
  <tr> <td> Catherine de Medici</td> <td>Medici</td> <td>성(family name)</td> </tr>
  <tr> <td> Jean de La Fontaine</td> <td>La Fontaine</td> <td>성(family name)이 "La Fontaine"이다.</td> </tr>
  <tr> <td> Juan Ponce de Leon</td> <td>Ponce de Leon</td> <td>전체 성(full family name)이 "Ponce de Leon"이다.</td> </tr>
  <tr> <td> Gabriel Garcia Marquez</td> <td>Garcia Marquez</td> <td>이중으로 된 스페인 성(surnames)</td> </tr>
  <tr> <td> Wernher von Braun</td> <td>von <em>or</em> Braun</td> <td>독일 혹은 미국에 있는냐에 따라 달라짐</td> </tr>
  <tr> <td> Elizabeth Alexandra May Windsor</td> <td>Elizabeth</td> <td>군주가 통치하는 이름에 따라 알파벳순으로 정렬</td> </tr>
  <tr> <td> Thomas a Beckett</td> <td>Thomas</td> <td>시성된(canonized) 이름에 따라 성인이름 사용</td> </tr>
</table>

> 분명하게, 심지어 두부분 "personal"과 "family"으로 나누는 것도 충분하지 않다.


#### 도전 과제

1.  좀더 조사한 뒤에, Valentina Roerich는 염도를 퍼센티지(%)로 작성한 것을 알게되었다.
    `Survey` 테이블에서 값을 100으로 나누어서 모든 염도 측정치를 반환하는 쿼리를 작성하세요.

2.  `union` 연산자는 두 쿼리의 결과를 조합한다.


<pre class="in"><code>%%sqlite survey.db
select * from Person where ident=&#39;dyer&#39; union select * from Person where ident=&#39;roe&#39;;</code></pre>

<div class="out"><table>
<tr><td>dyer</td><td>William</td><td>Dyer</td></tr>
<tr><td>roe</td><td>Valentina</td><td>Roerich</td></tr>
</table></div>


`union`을 사용하여 앞선 도전과제에서 기술되어 수정된 Roerich가 측정한, Roerich만 측정한 염도 측정치의 통합 리스트를 생성하세요.
출력결과는 다음과 같아야 한다.

<table>
  <tr> <td>619</td> <td>0.13</td> </tr>
  <tr> <td>622</td> <td>0.09</td> </tr>
  <tr> <td>734</td> <td>0.05</td> </tr>
  <tr> <td>751</td> <td>0.1</td> </tr>
  <tr> <td>752</td> <td>0.09</td> </tr>
  <tr> <td>752</td> <td>0.416</td> </tr>
  <tr> <td>837</td> <td>0.21</td> </tr>
  <tr> <td>837</td> <td>0.225</td> </tr>
</table>


3.  `Visited` 테이블에 사이트 식별자는 '-'으로 구분되는 두 부분으로 구성되어 있다.


<pre class="in"><code>%%sqlite survey.db
select distinct site from Visited;</code></pre>

<div class="out"><table>
<tr><td>DR-1</td></tr>
<tr><td>DR-3</td></tr>
<tr><td>MSK-4</td></tr>
</table></div>


몇몇 주요 사이트 식별자는 두 문자길이를 가지고 몇몇은 3문자길이를 가진다.
"in string" 함수 `instr(X, Y)`은 X 문자열에 문자열 Y가 첫번째 출현의 1-기반 인덱스를 반환하거나 
Y가 X에 존재하지 않으면 0 을 반환한다.
부분 문자열 함수 `substr(X, I)`은 인덱스 I에서 시작하는 문자열 X의 부분문자열을 반환한다.
상기 두 함수를 사용해서 유일한 주요 사이트 식별자를 생성하세요. (이 데이터에 대해서 작업된 리스트는 
"DR"과 "MSK"만 포함해야 한다.)


<div class="keypoints" markdown="1">
#### 주요점

*   SQL은 쿼리의 일부로서 레코드의 값을 사용한 계산을 수행한다.
</div>


<pre class="in"><code></code></pre>
