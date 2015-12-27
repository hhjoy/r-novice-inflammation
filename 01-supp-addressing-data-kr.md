---
layout: page
title: R 프로그래밍
subtitle: 데이터 다루기
minutes: 20
---

> ## 학습 목표 {.objectives}
>
> * 데이터프레임 내부 데이터를 R이 다루는 세가지 다른 방식을 이해한다.
> * 데이터를 다루는 다양한 메쏘드와 데이터 부분집합을 갱신하는 할당 연산자를 조합한다.

데이터를 능숙하게 다루는 강력한 언어가 R이다.
R 객체 내부에 있는 데이터를 다루는데 있어 주요방식이 3가지다.

* 인덱스 (슬라이싱): Index
* 논리 벡터: Logical Vector
* 명칭 (칼럼만 해당): Name

표본 데이터를 적재해서 시작해 본다:

~~~{.r}
dat<-read.csv(file='data/sample.csv',header=TRUE, stringsAsFactors=FALSE)
~~~

> ## 조언 {.callout} 
>
> csv 파일 첫번째 행이 칼럼명칭 명칭이다.
> `read.csv` 함수에 인자로 *header=TRUE* 를 사용했다.
> 그렇게 함으로써 R이 올바르게 파일을 해석할 수 있게 된다.
> R에 대한 기본디폴트 행동을 *stringsAsFactors=FALSE* 인자로 사용해서 치환한다.
> R에서 요인(factor) 사용은 별도 학습에서 다뤄진다.

적재한 데이터를 살펴보자.

~~~{.r}
class(dat)
~~~

~~~{.output}
[1] "data.frame"
~~~

R은 `.csv` 파일 콘텐츠를 `data.frame`인 `dat`라는 변수로 적재했다.

~~~{.r}
dim(dat)
~~~

~~~{.output}
[1] 100   9
~~~

데이터는 행이 100개, 열이 9개로 구성되었다.

~~~{.r}
head(dat)
~~~

~~~{.output}
      ID Gender      Group BloodPressure  Age Aneurisms_q1 Aneurisms_q2
1 Sub001      m    Control           132 16.0          114          140
2 Sub002      m Treatment2           139 17.2          148          209
3 Sub003      m Treatment2           130 19.5          196          251
4 Sub004      f Treatment1           105 15.7          199          140
5 Sub005      m Treatment1           125 19.9          188          120
6 Sub006      M Treatment2           112 14.3          260          266
  Aneurisms_q3 Aneurisms_q4
1          202          237
2          248          248
3          122          177
4          233          220
5          222          228
6          320          294
~~~

데이터는 (실제가 아닌) 실험 결과로 3가지 다른 처방을 받은 환자의 눈에 생성된 동맥(Aneurisms) 갯수를 볼 수 있다.

### 인덱스로 다루기: Index

데이터를 인덱스(index)로 접근할 수 있다.
이미 꺾쇠 기호 `[` 를 사용해서 데이터 부분집합(슬라이스)을 뽑아내는 방법을 살펴봤다.
일반적인 형식은  `dat[행번호, 열번호]`이다.

> ## 도전 과제 - 값 선택하기 1 {.challenge}
>
> `dat[1,1]`에 대한 반환값은 무엇인가?

~~~{.r}
dat[1,1]
~~~

~~~{.output}
[1] "Sub001"
~~~

만약 차원정보를 생략하면, R은 해당 차원에 대한 모든 값을 요청하는 것으로 해석한다.

> ## 도전 과제 - 값 선택하기 2 {.challenge}
>
> `dat[,2]`에 대한 반환값은 무엇인가?

콜론 `:`을 사용해서 정수 순열을 생성할 수 있다.

~~~{.r}
6:9
~~~

~~~{.output}
[1] 6 7 8 9
~~~

6에서 9까지 숫자 벡터를 생성한다.

데이터를 다루는데 이런 기능이 매우 유용하다.

> ## 도전 과제 - 순열로 부분집합 뽑아내기 {.challenge}
>
> 콜론 연산자를 사용해서 동맥 갯수 데이터(칼럼 6에서 9까지)만 색인해서 뽑아낸다.

마지막으로 `c()` (**c**ombine) 함수를 사용해서 비순차적인 행과 열을 다룬다.

~~~{.r}
dat[c(1,5,7,9),1:5]
~~~

~~~{.output}
      ID Gender      Group BloodPressure  Age
1 Sub001      m    Control           132 16.0
5 Sub005      m Treatment1           125 19.9
7 Sub007      f    Control           173 17.7
9 Sub009      m Treatment2           131 19.4
~~~

1,5,7,9 행에 있는 환자에 대해서 첫 5 칼럼을 반환한다.

> ## 도전 과제 - 비순차적인 데이터 부분집합 뽑아내기 {.challenge}
>
> 첫 환자 5명에 대한 연령(Age)과 성별(Gender) 값을 반환하라.

### 명칭으로 다루기: Name

R 데이터프레임 칼럼은 명칭이 주어졌다.

~~~{.r}
names(dat)
~~~

~~~{.output}
[1] "ID"            "Gender"        "Group"         "BloodPressure"
[5] "Age"           "Aneurisms_q1"  "Aneurisms_q2"  "Aneurisms_q3" 
[9] "Aneurisms_q4" 
~~~

> ## 조언 {.callout} 
>
> 만약 명칭이 명세되지 않았다면, 예를 들어 `read.csv()` 함수에 `headers=FALSE`을 사용해서,
> R은 기본디폴트 명칭을 할당한다: `V1,V2,...,Vn`.

통상 `$` 연산자를 사용해서 명칭으로 칼럼을 다룬다.

~~~{.r}
dat$Gender
~~~

~~~{.output}
  [1] "m" "m" "m" "f" "m" "M" "f" "m" "m" "f" "m" "f" "f" "m" "m" "m" "f"
 [18] "m" "m" "F" "f" "m" "f" "f" "m" "M" "M" "f" "m" "f" "f" "m" "m" "m"
 [35] "m" "f" "f" "m" "M" "m" "f" "m" "m" "m" "f" "f" "M" "M" "m" "m" "m"
 [52] "f" "f" "f" "m" "f" "m" "m" "m" "f" "f" "f" "f" "M" "f" "m" "f" "f"
 [69] "M" "m" "m" "m" "F" "m" "m" "f" "M" "M" "M" "f" "m" "M" "M" "m" "m"
 [86] "f" "f" "f" "m" "m" "f" "m" "F" "f" "m" "m" "F" "m" "M" "M"
~~~

꺾쇠에 명칭으로 데이터를 다루는 것을 사용할 수도 있다.

~~~{.r}
head(dat[,c('Age','Gender')])
~~~

~~~{.output}
   Age Gender
1 16.0      m
2 17.2      m
3 19.5      m
4 15.7      f
5 19.9      m
6 14.3      M
~~~

> ## 모범 사례 {.callout} 
>
> 모범 사례는 명칭으로 칼럼을 다루는 것이다.
> 흔히 칼럼을 생성하거나 삭제하고, 칼럼 위치는 변경된다.

### 논리적 색인 : Logical Indexing

논리적 벡터는 `TRUE` 와 `FALSE` 특수값만 포함한다.

~~~{.r}
c(TRUE,TRUE,FALSE,FALSE,TRUE)
~~~

~~~{.output}
[1]  TRUE  TRUE FALSE FALSE  TRUE
~~~

> ## 조언 {.callout} 
>
> `TRUE` 와 `FALSE` 논리값은 모두 대문자로 인용부호가 없음에 유의한다.

논리적 벡터는 `관계 연산자(relational operators)`를 사용해서 생성할 수 있다.
예를 들어, `<, >, ==, !=, %in%`.

~~~{.r}
x<-c(1,2,3,11,12,13)
x < 10
~~~

~~~{.output}
[1]  TRUE  TRUE  TRUE FALSE FALSE FALSE
~~~

~~~{.r}
x %in% 1:10
~~~

~~~{.output}
[1]  TRUE  TRUE  TRUE FALSE FALSE FALSE
~~~

논리 벡터를 사용해서 데이터프레임에서 데이터를 선택해서 뽑아낼 수 있다.

~~~{.r}
index <- dat$Group == 'Control'
dat[index,]$BloodPressure
~~~

~~~{.output}
 [1] 132 173 129  77 158  81 137 111 135 108 133 139 126 125  99 122 155
[18] 133  94  98  74 116  97 104 117  90 150 116 108 102
~~~

흔히, 상기 연산을 코드 한줄로 작성한다:

~~~{.r}
plot(dat[dat$Group=='Control',]$BloodPressure)
~~~

<img src="fig/logical_vectors_indexing2-1.png" title="plot of chunk logical_vectors_indexing2" alt="plot of chunk logical_vectors_indexing2" style="display: block; margin: auto;" />

> ## 도전 과제 - 논리 인덱스 사용 {.challenge}
>
> 1. 대조군에 있지 않는 피험자에 대해서 BloodPressure 정보를 보여주는 산점도를 생성한다.
> 2. 해당 피험자 집단을 인덱스해서 뽑아내는 방식이 몇개나 있을까?

### 인덱스와 할당 조합하기

할당 연산자, `<-`, 를 인덱스와 조합할 수 있다.

~~~{.r}
x<-c(1,2,3,11,12,13)
x[x < 10] <- 0
x
~~~

~~~{.output}
[1]  0  0  0 11 12 13
~~~

> ## 도전 과제 - 부분집합 값을 갱신하기 {.challenge}
> 
> 상기 데이터셋에서, 성별(Gender)에 대한 값이 대문자  `M, F`와 소문자 `m,f`로 기록되어 있다.
> 모든 값을 소문자로 전환하도록 인덱스와 할당 연산자를 조합하라.
