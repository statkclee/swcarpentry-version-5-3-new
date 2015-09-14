---
layout: lesson
root: ../..
---

## 데이터베이스로 프로그래밍


<div class="objectives" markdown="1">
#### 목표

*   SQL 쿼리를 실행하는 짧은 프로그램을 작성한다.
*   SQL 쿼리를 포함하는 프로그램의 실행을 추적한다.
*   왜 대부분의 데이터베이스 응용프로그램이 SQL 보다 다른 범용 언어로 작성되는지 설명한다.
</div>


마무리 하면서, 파이썬 같은 범용 프로그래밍 언어에서 데이터베이스를 어떻게 접근하는지 삺펴보자.
다른 언어도 거의 같은 모델을 사용한다. 라이브러리와 함수 이름이 다를지 모르지만,
개념은 동일한다.

`survey.db`라는 이름의 파일에 저장된 SQLite 데이터베이스에서 위도와 경도를 선택하는
짧은 파이썬 프로그램이 다음에 있다.


<pre class="in"><code>import sqlite3
connection = sqlite3.connect(&#34;survey.db&#34;)
cursor = connection.cursor()
cursor.execute(&#34;select site.lat, site.long from site;&#34;)
results = cursor.fetchall()
for r in results:
    print r
cursor.close()
connection.close()</code></pre>

<div class="out"><pre class='out'><code>(-49.85, -128.57)
(-47.15, -126.72)
(-48.87, -123.4)
</code></pre></div>


`sqlite3` 라이브러리를 가져오는 것부터 프로그램이 시작한다.
만약 MySQL, DB2 혹은 다른 데이터베이스에 접속한다면,
다른 라이브러리를 가져올 것이지만, 동일한 기능을 제공한다.
그래서 만약 다른 이 데이터베이스에서 저 데이터베이스로 바꾼다면 프로그램의 나머지 
부분은 변경할 필요(적어도 그렇게 많지는 않다.)가 었다.

2번째 행이 데이터베이스에 연결을 설정한다.
SQLite를 사용하기 때문에, 명시하는데 필요한 전부는 데이터베이스 파일 이름이다.
다른 데이터베이스 시스템은 사용자명과 비밀번호를 또한 제공하도록 요구할지도 모른다.
3번째 행은 연결을 이용하여 [커서(cursor)](../../gloss.html#cursor)를 생성한다.
편집기의 커서처럼, 커서의 역할은 데이터베이스에 어느 위치에 있는지 추적하는 것이다.

4번째 행에서 커서를 사용해서 사용자를 대신해서 데이터베이스에 쿼리 실행 요청을 한다.
쿼리는 SQL로 작성되고 문자열로 `cursor.execute`에 전달된다.
SQL이 제대로 작성되어 있는지 확실히 하는 것이 사용자의 몫이다.
만약 제대로 작성이 되어 있지 않거나 실행될 때 뭔가 잘못되었다면,
데이터베이스는 오류를 보고한다.

5번째 행에 `cursor.fetchall` 호출에 응답하여 데이터베이스가 쿼리 결과를 반환한다.
결과는 결과집합에 각 레코드마다 하나의 항목을 가진 리스트다. 만약 리스트(6번째 행)를 
루프 반복을 돌려서 리스트 항목(7번째 행)을 출력하면, 
각각은 각 필드에 하나의 요소를 가진 튜플(tuple)인 것을 알 수 있다.

마지막으로, 8번째와 9번째 행은 커서와 데이터베이스 연결을 종료한다. 왜냐하면 데이터베이스는
한번에 열수 있는 제한된 숫자의 연결만 유지할 수 있기 때문이다.
하지만, 연결을 설정하는 것은 시간이 소요되어서,
단지  백만분의 수초 후에 다시 연결을 하고 또 다른 작업을 하려는 
연결을 하고, 작업을 하고 나서 연결을 종료하는 것은 하지 말아야 한다.

실제 응용프로그램에서 쿼리는 사용자가 제공하는 값에 달려있다.
예를 들어, 다음 함수는 사용자의 ID를 매개변수로 받아서 이름을 반환한다.


Queries in real applications will often depend on values provided by users.
For example,
this function takes a user's ID as a parameter and returns their name:


<pre class="in"><code>def get_name(database_file, person_ident):
    query = &#34;select personal || &#39; &#39; || family from Person where ident=&#39;&#34; + person_ident + &#34;&#39;;&#34;

    connection = sqlite3.connect(database_file)
    cursor = connection.cursor()
    cursor.execute(query)
    results = cursor.fetchall()
    cursor.close()
    connection.close()

    return results[0][0]

print &#34;full name for dyer:&#34;, get_name(&#39;survey.db&#39;, &#39;dyer&#39;)</code></pre>

<div class="out"><pre class='out'><code>full name for dyer: William Dyer
</code></pre></div>


함수의 첫번째 행에 문자열 결함을 사용해서 사용자가 넘겨준 사용자 ID를 포함하는 쿼리를 
완성한다.
단순하게 보일지 모르지만, 만약 누군가 다음 문자열을 입력값으로 준다면 무슨일이 일어날까?

~~~
dyer'; drop table Survey; select '
~~~

프로젝트 이름 뒤에는 쓰레기(garbage)처럼 보이지만, 매우 주의깊게 고른 쓰레기다.
만약 이 문자열을 쿼리에 삽입하면, 결과는 다음과 같다.

~~~
select personal || ' ' || family from Person where ident='dyer'; drop table Survey; select '';
~~~

만약 쿼리를 실행하게 된다면, 데이터베이스에 있는 테이블 중의 하나를 삭제한다.

이것을 [SQL 주입 공격(SQL injection attack)](../../gloss.html#sql-injection-attack)이라고 부른다.
SQL 주입공격은 수년에 걸쳐서 수천개의 프로그램을 공격하는데 사용되었다.
특히, 많은 웹사이트가 먼저 사려깊게 입력값을 점검하지 않고 사용자에게서 데이터를 입력받는 값을 쿼리로 바로 입력한다.

악의를 가진 사용자가 다양한 많은 방식으로 쿼리에 명령어를 몰래 밀어넣으려고 한다.
이러한 위협을 다루는 가장 안전한 방식은 인용부호 같은 문자를 대체 상응값으로 대체하는 것이다.
그렇게 해서 안전하게 문자열 내부에 사용자가 입력한 무엇이든지 넣을 수 있다.
문자열로 문장을 작성하는 대신에 [준비된 문장(prepared statement)](../../gloss.html#prepared-statement)를 사용해서 작업할 수 있다.
만약에 준비된 문장을 사용한다면, 예제 프로그램은 다음과 같다.


<pre class="in"><code>def get_name(database_file, person_ident):
    query = &#34;select personal || &#39; &#39; || family from Person where ident=?;&#34;

    connection = sqlite3.connect(database_file)
    cursor = connection.cursor()
    cursor.execute(query, [person_ident])
    results = cursor.fetchall()
    cursor.close()
    connection.close()

    return results[0][0]

print &#34;full name for dyer:&#34;, get_name(&#39;survey.db&#39;, &#39;dyer&#39;)</code></pre>

<div class="out"><pre class='out'><code>full name for dyer: William Dyer
</code></pre></div>


주요 변경사항은 쿼리 문자열과 `execute` 호출에 있다.
쿼리 자체 형식을 만드는 대신에 쿼리 템플릿에 값을 삽입하고자 하는 곳에 물음표를 넣는다.
`execute`를 호출할 때, 쿼리의 물음표 숫자만큼의 값을 담고 있는 리스트를 제공한다.
라이브러리는 입력값을 순서대로 물음표와 매칭하고 특수 문자를 별도 상응값으로 번역해서
안전하게 사용하게 된다.


#### 도전 과제

1.  10.0 에서 25.0 사이의 100,000개 난수를 가지는 레코드를 가지고, 
    `reading`으로 불리는 단일 필드를 가지고, `Pressure`라는 단일 테이블을 가지고, 
    `original.db`이라는 이름을 가지는 신규 데이터베이스를 파일에 생성하는 
    파이썬 프로그램을 작성하세요. 

2.  `original.db`과 동일한 구조를 가지는 `backup.db`으로 불리는 새로운 데이터베이스를
    생성하는 파이썬 프로그램을 작성하세요.
    `backup.db`는 `original.db`에서 `backup.db`로 20.0보다 큰 모든 값을 
    복사한 값을 담고 있다. 어느 것이 더 빠른가요?
    쿼리의 값을 필터링하는 것 혹은 주기억장치에 모든 것을 읽어드리고 
    파이썬에서 필터링하는 것 중에서 선택하세요. 


<div class="keypoints" markdown="1">
#### 주요점

*   일반적으로 범용 언어로 데이터베이스 응용프로그램을 작성하고 SQL 쿼리를 프로그램에 내장한다.
*   데이터베이스에 접속하기 위해서 프로그램은 접속하려는 데이터베이스 관리자에 특정된 라이브러리를 사용해야 한다.
*   프로그램은 하나 혹은 그 이상의 연결을 단일 데이터베이스에 열고, 각각에 대해서 활성화된 하나 혹은 그 이상의 커서를 가진다.
*   프로그램은 쿼리 결과를 배치모드로 혹은 한번에 모두 읽어들인다. 
</div>


<pre class="in"><code></code></pre>
