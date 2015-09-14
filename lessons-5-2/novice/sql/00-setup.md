---
layout: lesson
root: ../..
---

# SQLite 설치


이번 학습은 다음 장에서 사용되는 예제 데이터베이스를 어떻게 설치하는지 설명한다.
다음의 지도사항을 따르기 위해서는 명령-라인을 사용하여 어떻게 디렉토리를 여기저기 이동하는지와 명령-라인에서 명령문을 어떻게 실행하는지 숙지할 필요가 있다.
이런 주제와 친숙하지 않다면, [유닉스 쉘(Unix Shell)](http://software-carpentry.org/v5/novice/shell/index.html) 학습을 참조하세요.
이후의 장에서 데이터베이스를 어떻게 생성하고 데이터를 채우는지 배울 것이지만, 먼저 SQLite 데이터베이스가 어떻게 동작하는지 설명을 할 필요가 있어서
데이터베이스를 선행하여 제공한다.


#### 목표

*   다음 장에서 사용될 예제 데이터베이스를 구축한다.
*   데이터베이스가 이용가능한지를 점검하고 어느 테이블이 있는지도 확인한다.


## 설치


인터랙티브하게 다음 학습을 수행하기 위해서는 [설치 방법](http://software-carpentry.org/v5/setup.html)에 언급된 SQLite 를 참조하여 설치하세요.

그리고 , 여러분이 선택한 위치에 "software_carpentry_sql" 디렉토리를 생성하세요.예를 들어


1) 명령 라인 터미널 윈도우를 여세요.

2) 다음과 같이 타이핑한다.


<pre class="in"><code>mkdir ~/swc/sql</code></pre>


3) 생성한 디렉토리로 현재 작업 디렉토리를 변경한다.


<pre class="in"><code>cd ~/swc/sql</code></pre>


### github에서 "gen-survey-database.sql" 파일을 어떻게 다운로드 받을까요?


"~/swc/sql" 디렉토리로 이동한 후에 그 디렉토리에서  github 사이트 (https://github.com/swcarpentry/bc/blob/master/novice/sql/gen-survey-database.sqlSQL) 에 위치한 SQL 파일("gen-survey-database.sql")을 다운로드한다.

파일이 github 저장소 내에 위치하고 있어서, 전체 git 저장소(git repo)를 복제(cloning)하지 않고 단일 파일만 로컬로 가져온다. 이 목적을 달성하기 위해서,
HTTP, HTTPS, FTP 프로토콜을 지원하는 명령-라인 웹크롤러(web-crawler) 소프트웨어 [GNU Wget](http://en.wikipedia.org/wiki/Wget) 혹은,
다양한 프로토콜을 사용하여 데이터를 전송하는데 사용되는 라이브러리이며 명령-라인 도구인 [cURL](http://en.wikipedia.org/wiki/CURL)을 사용한다.
두가지 도구 모두 크로스 플랫폼(cross platform)으로 다양한 운영체제를 지원한다.

Wget 혹은 cURL을 로컬에 설치한 후에, 터미널에서 다음 명령어를 실행한다.

<i>[Tip: 만약 cURL을 선호한다면, 다음 명령문에서 "wget"을 "curl -O"로 대체하세요.]</i>


<pre class="in"><code>mom@durga:~/swc/sql$ wget https://raw.githubusercontent.com/swcarpentry/bc/master/novice/sql/gen-survey-database.sql</code></pre>


상기 명령문으로 Wget은 HTTP 요청을 생성해서 github 저장소의 "gen-survey-database.sql" 원 파일만 현재 작업 디렉토리로 가져온다.
성공적으로 완료되면 터미널은 다음 출력결과를 화면에 표시한다.


<pre class="in"><code>--2014-09-02 18:31:43--  https://raw.githubusercontent.com/swcarpentry/bc/master/novice/sql/gen-survey-database.sql
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 103.245.222.133
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|103.245.222.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3297 (3.2K) [text/plain]
Saving to: ‘gen-survey-database.sql’

100%[=========================================================================================================================&gt;] 3,297       --.-K/s   in 0.01s   

2014-09-02 18:31:45 (264 KB/s) - ‘gen-survey-database.sql’ saved [3297/3297]</code></pre>


이제 성공적으로 단일 SQL 파일을 가져와서, "survey.db" 데이터베이스를 생성하고 "gen-survey-database.sql" 에 저장된 지시방법에 따라서 데이터를 채워넣는다.
명령-라인 터미널에서 SQLite3 프로그램을 호출하기 위해서, 다음 명령문을 실행한다.


<pre class="in"><code>sqlite3 survey.db &lt; gen-survey-database.sql</code></pre>


### SQLite DB 연결 및 설치 테스트


생성된 데이터베이스에 연결하기 위해서, 데이터베이스를 생성한 디렉토리 안에서 SQLite를 시작한다. 그래서 "~/swc/sql" 디렉토리에서 다음과 같이 타이핑한다.


<pre class="in"><code>sqlite3 survey.db</code></pre>


"sqlite3 survey.db" 명령문이 데이터베이스를 열고 데이터베이스 명령-라인 프롬프트로 안내한다. SQLite에서 데이터베이스는 플랫 파일(flat file)로 명시적으로 열 필요가 있다. 그리고 나서 SQLite 시작되고 "sqlite"로 명령-라인 프롬프트로 다음과 같이 변경되어 표시된다.


<pre class="in"><code>/novice/sql$ sqlite3 survey.db 
SQLite version 3.7.15.2 2013-01-09 11:53:05
Enter &#34;.help&#34; for instructions
Enter SQL statements terminated with a &#34;;&#34;
sqlite&gt;  </code></pre>


다음 출력결과가 보여주듯이 ".databases" 명령문으로 소속된 데이터베이스 이름과 파일 목록을 확인한다.


<pre class="in"><code>sqlite&gt; .databases
seq  name             file                                                      
---  ---------------  ----------------------------------------------------------
0    main             ~/novice/sql/survey.db </code></pre>


다음과 같이 타이핑해서 필요한 "Person", "Survey", "Site" "Visited" 테이블이 존재하는 것을 확인한다.


<pre class="in"><code>.tables</code></pre>


그리고 ".table"의 출력결과는 다음과 같다.


<pre class="in"><code>sqlite&gt; .tables
Person   Site     Survey   Visited</code></pre>


이제, 설치를 완료해서 다음 학습으로 진행할 수 있다. 
현재 명령-라인 SQLite 세션에서 다음 연습을 수행할 수 있다.
IPython의 마술같은 방법 (%로 시작하는 각 명령문의 첫 행)은 IPython notebook에서만 동작하기 때문에,
터미널에서 SQLite를 사용하는 동안 생략할 수 있다.
만약 IPython notebook 사용을 선호하면 SQLite를 끝낼 수 있다.


### SQLite3 DB 명령-라인 인터페이스(CLI)를 어떻게 빠져나올까요?


SQLite3를 빠져나오기 위해서, 다음과 같이 타이핑한다.


<pre class="in"><code>sqlite&gt; .quit</code></pre>


### SQLite3 CLI 대신에 IPython notebook을 어떻게 사용할까요?


만약 예제를 따라가는데 IPython notebook 사용을 선호한다면, IPython이 로컬 컴퓨터에 설치되었는지 점검하라. 만약 설치되어 있지 않다면, 
[설치 방법](http://ipython.org/install.html)을 따르세요.
만약 IPython이 이미 로컬 컴퓨터에 설치되어 있다면 notebook을 열기 위해서 작업 폴더 "~/swc/sql" 내부에서 "ipython notebook"을 타이핑하세요.


<pre class="in"><code>~/swc/sql$ ipython notebook</code></pre>


상기 명령어가 IPython 커널을 구동해서 디폴트 브라우져에 인터랙티브 노트북을 화면에 표시해서 학습하면서 편집할 수 있게 한다.
다음 학습에 보여지는 명령문을 IPython notebook에서 수행할 수 있다. 훈련이 종료되면 변경사항을 간직하기 위해서 노트북 저장을 기억하세요.


<pre class="in"><code></code></pre>
