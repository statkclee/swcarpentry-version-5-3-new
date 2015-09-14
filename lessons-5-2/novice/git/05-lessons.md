---
layout: lesson
root: ../..
title: git 설치
---
<div class="objectives" markdown="1">

#### 목표
*   Git 설치한다.
*   신규 소프트웨어 카펜트리 교육과정 작성
*   의존성(dependencies) 설치

</div>

##### Git 설치 및 Git 저장소 가져오기

Git가 설치되어 있지 않다면 리눅스 우분투 기준으로 다음 명령어를 사용하여 설치한다.  

~~~
$ sudo apt-get update
$ sudo apt-get install git
~~~
{:class="in"}

GitHub 저장소에서 사용자 컴퓨터로 클론하여 가져온다.  

~~~
$ git clone https://github.com/swcarpentry/slideshows.git
~~~
{:class="in"}

#### 소프트웨어 카펜트리 학습과목 생성

1. `data-cleanup` 저장소를 GitHub에 생성한다.  

2. 템플릿 저장소를 학습과목과 동일한 이름으로 클론한다.  

~~~
$ git clone -b gh-pages -o upstream https://github.com/swcarpentry/lesson-template.git data-cleanup
~~~
{:class="in"}

3. 다음 명령어를 이용하여 다운로드 받은 디렉토리로 이동한다.  

~~~
$ cd data-cleanup
~~~
{:class="in"}

4. 다음 명령어를 사용하여 `origin`이라는 이름으로 GitHub 저장소를 추가한다.  

~~~
$ git remote add origin https://github.com/statkclee/data-cleanup
~~~
{:class="in"}

5. 작업을 진행한다.  

6. 학습과목에 대한 HTML 페이지를 빌드한다.  
`pandoc`을 설치해야 한다. 기존 GitHub가 HTML 페이지를 빌드하는 것이 아니고 학습과목을 로컬 컴퓨터에서 빌드해서 GitHub에 푸쉬해한다.   

~~~
$ make preview
~~~
{:class="in"}

7. 작업하여 빌드한 HTML 페이지를 GitHub 저장소 `gh-pages` 브랜치로 푸쉬한다.  

~~~
$ cd data-cleanup
$ git add changed-files.md *.html
$ git commit -m "Explanatory message"
$ git push origin gh-pages
~~~
{:class="in"}


##### 의존성(dependencies)

처음 리눅스를 설치했다면 아무것도 없기 때문에 가장 기본적인 `make`부터 설치한다.
`pandoc`설치를 위해서 파이썬 pip 도 설치한다. 마지막으로 소프트웨어 카펜트리 학습과정 작성을 위해서 `pandocfilters`도 설치한다.  

1. make  

~~~
$ sudo apt-get install make
~~~
{:class="in"}

2. Python pip  

~~~
$ sudo apt-get install python-pip
~~~
{:class="in"}

3. pandoc   

~~~
sudo apt-get install pandoc
~~~
{:class="in"}

4. pandocfilters  
Pandoc에 필터 작성을 도와주는 파이썬 모듈  

~~~
sudo pip install pandocfilters
~~~
{:class="in"}

