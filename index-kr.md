---
layout: lesson
title: R 프로그래밍
---
프로그램을 어떻게 작성하는지 배우는 가장 좋은 방법은 의미있는 무언가를 작성해보는 것이다.
그래서 이번 R 소개는 흔한 과학 작업에 맞춰있다: 자료 분석.

진정한 목적은 R을 가르치는 것이 아니라, 
모든 프로그래밍에 기반하는 기본 개념을 전달함에 있다.
R을 사용해서 학습을 진행한다. 왜냐하면,

1. 예제로 *어떤* 언어든지 사용해야만 된다.
2. 무료이고, 문서화가 잘 되어 있고, 거의 모든 곳에서 실행된다.
3. 과학자들 사이에 커다란 (그리고 점증하는) 사용자 기반이 있다.
4. 다양한 작업을 수행하는데 이용가능한 외부 패키지 라이브러리가 많다.

하지만, 두 가지 가장 중요한 것은
어떤 언어든 상관없이 동료가 사용하는 언어를 사용해서, 
작업 결과를 쉽게 공유할 수 있어야 하고, 
해당 언어를 *잘* 사용해야 된다는 점이다.

관절염에 대한 새로운 치료법이 처방된 환자의 염증에 대한 연구를 진행하고 있고, 
첫 데이터셋(Data Set) 12개를 분석할 필요가 있다. 
데이터셋은 [CSV 형식](reference.html#comma-separated-values)(comma-separated values, 구분자가 콤마 값을 가진 파일 형식)으로 저장되어 있다: 
각 행은 환자 한명에 대한 정보로 구성되고, 
열은 연속된 날짜 정보를 나타낸다. 
첫번째 파일에 대한 처음 행 몇줄 정보는 다음과 같다:

~~~
0,0,1,3,1,2,4,7,8,3,3,3,10,5,7,4,7,7,12,18,6,13,11,11,7,7,4,6,8,8,4,4,5,7,3,4,2,3,0,0
0,1,2,1,2,1,3,2,2,6,10,11,5,9,4,4,7,16,8,6,18,4,12,5,12,7,11,5,11,3,3,5,4,4,5,5,1,1,0,1
0,1,1,3,3,2,6,2,5,9,5,7,4,5,4,15,5,11,9,10,19,14,12,17,7,12,11,7,4,2,10,5,4,2,2,3,2,2,1,1
0,0,2,0,4,2,2,1,6,7,10,7,9,13,8,8,15,10,10,7,17,4,4,7,6,15,6,4,9,11,3,5,6,3,3,4,2,3,2,1
0,1,1,3,3,1,3,5,2,4,4,7,6,5,3,10,8,10,6,17,9,14,9,7,13,9,12,6,7,7,9,6,3,2,2,4,2,0,1,1
~~~

다음을 수행해야 된다.

*  CSV 형식 데이터 파일을 주기억장치에 적재(loading)한다.
*  모든 환자에 대해서 각 날짜별로 평균 염증을 계산한다.
*  결과값을 도식화한다.

상기 작업을 수행하기 위해서, 프로그래밍에 관해 약간 학습할 필요가 있다.

> ## 전제조건 {.prereq}
>
> 학습자는 (작업 디렉토리를 포함) 파일과 디렉토리에 관한 개념을 이해할 필요가 있다.
> 흔히 RStudio를 사용해서 학습을 진행하지만, 필수로 요구되는 것은 아니다.

> ## 사전 준비 {.getready}
>
> 학습을 진행하는데 일부 파일을 미리 다운로드할 필요가 있다:
>
> 1. 바탕화면에 `r-novice-inflammation`로 새로운 폴더를 생성한다.
> 2. [r-novice-inflammation-data.zip](./r-novice-inflammation-data.zip) 파일을 다운로드 하고,
상기 폴더에 이동한다.
> 3. 만약 아직 압축을 풀지 않았다면, 두번 클릭해서 압축을 푼다. 최종 결과작업은 `data` 폴더가 새로 생겨야 된다.
> 4. 다음 명령어로 유닉스 쉘에서 상기 폴더로 접근할 수 있다:
>```
> $ cd && cd Desktop/r-novice-inflammation/data
>```

## 학습주제
|   한국어(Korean)      |    영어(English)            |
|--------------------------------|-----------------------------------|
|1.  [환자 데이터 분석](01-starting-with-data-kr.html)   |1.  [Analyzing patient data](01-starting-with-data.html) |
|2.  [함수 생성](02-func-kr.html)                                 |2.  [Creating functions](02-func-R.html) |
|3.  [데이터셋 다수 분석](03-loops-R-kr.html)                |3.  [Analyzing multiple data sets](03-loops-R.html) |
|4.  [조건 선택](04-cond-R-kr.html)                                    |4.  [Making choices](04-cond.html) |
|5.  [명령-라인 프로그램](05-cmdline-R-kr.html)               |5.  [Command-Line Programs](05-cmdline.html) |
|6.  [R을 사용해서 프로그램을 설계하는 모범사례](06-best-practices-R-kr.html)                              |6.  [Best practices for using R and designing programs](06-best-practices-R.html) |
|7.  [knitr로 동적 보고서 작성](07-knitr-R-kr.html)          |7.  [Dynamic reports with knitr](07-knitr-R.html) |
|8.  [R 팩키지 작성](08-making-packages-R-kr.html)  |8.  [Making packages in R](08-making-packages-R.html) |

## 추가 학습교재       

*   [참고문헌](reference.html)
*   [토론](discussion.html)
*   [강사 안내서](instructors.html)

## 보충 학습
|   한국어(Korean)      |    영어(English)            |
|--------------------------------|-----------------------------------|
|*  [RStudio 소개](01-supp-intro-rstudio-kr.html)         |*  [Introduction to RStudio](01-supp-intro-rstudio.html)|
|*  [데이터 다루기](01-supp-addressing-data-kr.html)    |*  [Addressing data](01-supp-addressing-data.html)|
|*  [요인 이해하기](01-supp-factors-kr.html)                  |*  [Understanding factors](01-supp-factors.html)|
|*  [자료형과 자료구조](01-supp-data-structures-kr.html)|*  [Data types and structures](01-supp-data-structures.html)|









