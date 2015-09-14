---
layout: lesson
root: ../..
---

## 데이터 생성과 변형(Creating and Modifying Data)


<div class="objectives" markdown="1">
#### 목표

*   테이블을 생성하는 쿼리를 작성한다.
*   레코드를 삽입, 변형, 삭제하는 쿼리를 작성한다.
</div>


지금까지 어떻게 데이터베이스에서 정보를 추출하는지만 살펴봤다.
왜냐하면, 정보를 추가하는 것보다 정보를 조회하는 것이 더 자주 있는 일이기도 하고, 다른 연산자는 쿼리가 이해되어야만 의미가 통하기 때문이다.
만약 데이터를 생성하고 변형하고자 한다면, 다른 두짝의 명령어를 공부할 필요가 있다.

첫번째 짝은 `create table`과 `drop table`이다.
두 단어로 작성되지만, 사실 하나의 단일 명령어다.
첫번째 명령어는 새로운 테이블을 생성한다. 인자는 테이블 칼럼의 이름과 형식이다.
예를 들어, 다음 문장은 survey 데이터베이스에 테이블 4개를 생성한다.

~~~
create table Person(ident text, personal text, family text);
create table Site(name text, lat real, long real);
create table Visited(ident integer, site text, dated text);
create table Survey(taken integer, person text, quant real, reading real);
~~~

다음 명령어를 사용하여 테이블 중의 하나를 제거할 수도 있다.

~~~
drop table Survey;
~~~

데이블을 제거할 때 매우 주의하라. 대부분의 데이터베이이스는 변경사항을 되돌리는 기능을 제공하지만, 이러한 기능에 의존하지 않는 것이 더 낫다.

다른 데이터베이스 시스템은 테이블 칼럼의 다른 데이터 형식도 지원하지만,
대부분은 다음을 다음을 제공한다.

<table>
  <tr> <td>integer</td> <td>부호있는 정수형</td> </tr>
  <tr> <td>real</td> <td>부동 소수점 실수</td> </tr>
  <tr> <td>text</td> <td>문자열</td> </tr>
  <tr> <td>blob</td> <td>이미지 같은 "이진 대형 개체"</td> </tr>
</table>

대부분의 데이터베이스는 불(boolean)과 날짜/시간 값도 지원한다.
SQLite는 불값을 정수 0 과 1 을 사용하고 날짜/시간은 [앞선(earlier)](#a:dates) 학습방식으로 표현한다. 
점점 더 많은 데이터베이스가 위도와 경도 같은 지리정보 데이터 형식도 지원한다.
특정 시스템이 무슨 기능을 제공하고 제공하지 않는지 그리고 어떤 이름을 다른 데이터 형식에 
부여하는지를 계속 파악하는 것은 끝없는 시스템 이식성에 대한 골치거리다.

테이블을 생성할 때, 칼럼에 몇가지 제약사항을 지정할 수 있다.
예를 들어, `Survey` 테이블에 대한 좀더 좋은 정의는 다음과 같이 될 것이다.

~~~
create table Survey(
    taken   integer not null, -- where reading taken
    person  text,             -- may not know who took it
    quant   real not null,    -- the quantity measured
    reading real not null,    -- the actual reading
    primary key(taken, quant),
    foreign key(taken) references Visited(ident),
    foreign key(person) references Person(ident)
);
~~~

다시 한번, 정확하게 무슨 제약사항이 이용가능하고 어떻게 호출되는지는 어떤 데이터베이스 관리자를 사용하는야에 달려있다.
  
테이블이 생성되자마자, 다른 명령어 짝 `insert`와 `delete`를 사용하여 레코드를 추가하고 제거할 수 있다.
`insert` 문의 가장 간단한 형식은 순서대로 값을 목록으로 나열하는 것이다.

~~~
insert into Site values('DR-1', -49.85, -128.57);
insert into Site values('DR-3', -47.15, -126.72);
insert into Site values('MSK-4', -48.87, -123.40);
~~~

또한, 다른 테이블에서 직접 값을 테이블에 삽입할 수도 있다.

~~~
create table JustLatLong(lat text, long text);
insert into JustLatLong select lat, long from site;
~~~

레코드를 삭제하는 것은 약간 난이도가 있다.
왜냐하면, 데이터베이스가 내부적으로 일관성을 보장할 필요가 있기 때문이다.
만약 하나의 단독 테이블만 관심을 둔다면, 삭제하고자 하는 레코드와 매칭되는 
`where`절과 `delete`문을 함께 사용한다.
예를 들어, Frank Danforth가 어떤 측정도 하지 않았다는 것을 인지하자마자,
다음과 같이 `Person` 테이블에서 Frank Danforth를 제거할 수 있다.

~~~
delete from Person where ident = "danforth";
~~~

하지만 대신에 Anderson Lake를 실수로 제거했다면 어떨까요?
`Survey` 테이블은 Anderson Lake이 수행한 7개의 측정 레코드를 담고 있지만,
이것은 결코 일어나지 말아야 된다.
`Survey.person`은 `Person` 테이블에 외래키이고,
모든 쿼리는  전자의 모든 값을 매칭하는 후자의 행이 있을 거라고 가정한다.

이러한 문제를 [참조 무결성(referential integrity)](../../gloss.html#referential-integrity)이라고 부른다. 테이블 사이의 모든 참조는 항상 제대로 해결될 수 있도록 확인할 필요가 있다.
참조 무결성을 보증하는 한 방법은 기본키로 사용하는 레코드를 삭제하기 전에 외래키로 
`'lake'`를 사용하는 모든 레코드를 삭제하는 것이다.
만약 데이터베이스 관리자가 이 기능을 지원한다면, 
[연쇄적인 삭제(cascading delete)](../../gloss.html#cascading-delete)를 사용해서 자동화할 수 있다.
하지만, 이 기법은 여기서 다루는 학습 영역밖이다.

> 모든 것을 데이터베이스에 저장하는 대신 많은 응용프로그램은 
> 하이브리드 저장 모델을 사용한다.
> 천체 이미지 같은 실제 데이터는 파일에 저장되는 반면에, 파일 이름, 변경된 날짜,
> 커버하는 하늘의 영역, 스펙트럼 특성, 등등 정보는 데이터베이스에 저장한다.
> 대부분의 음악 재생기(MP3 플레이어) 소프트웨어가 작성되는 방식이기도 하다.
> 응용프로그램 내부 데이터베이스는 MP3 파일을 기억하고 있지만, MP3 파일 자체는 디스크에 있다.


#### 도전 과제

1.  `Survey.person`의 `null`인 모든 사용자를 문자열 `'unknown'`으로 대체하는 SQL문을 작성하세요.

2.  동료중의 한명이 Robert Olmstead가 측정한 온도 측정치를 포함하는 다음과 같은 형식의
    [CSV](../../gloss.html#comma-separeted-values) 파일을 보내왔다.

    ~~~
    Taken,Temp
    619,-21.5
    622,-15.5
    ~~~
    
    survey 데이터베이스에 레코드로 추가하려고 CSV 파일을 읽고 SQL `insert`문을 출력하는 
    작은 파이썬 프로그램을 작성하세요.
    `Person` 테이블에 Olmstead 항목을 추가할 필요가 있을 것이다.
    반복적으로 프로그램을 테스트하려면, SQL `insert or replace` 문을 자세히 살펴볼 필요도 있다.
    
3.  SQLite는 SQL 표준이 아닌 몇개 관리 명령어가 있다.
    그중의 하나가 `.dump`로 데이터베이스를 다시 생성하는데 필요한 SQL 명령문을 출력한다.
    또다른 것은 `.load`로 `.dump`에서 생성된 파일을 읽어서 데이터베이스를 복원한다.
    여러분의 동료중의 한명이 텍스트인 dump 파일을 버젼 제어 시스템에 저장하는 것이
    데이터베이스 변경사항을 추적하고 관리하는 좋은 방법이라고 생각한다.
    이러한 접근법의 장점과 단점은 무엇일까요? (힌트: 레코드는 어느 특정한 순서로 저장되지 않는다.)


<div class="keypoints" markdown="1">
#### 주요점

*   데이터베이스 테이블은 테이블 이름과 필드의 이름과 특성을 명시하는 쿼리를 사용해서 생성된다.
*   쿼리를 사용해서 레코드는 삽입, 갱신, 삭제될 수 있다.
*   모든 레코드가 유일한 기본키를 가질 때 데이터를 변경하는 것이 더 간단하고 안전하다.
</div>


<pre class="in"><code></code></pre>
