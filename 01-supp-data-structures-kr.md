---
layout: page
title: R 프로그래밍
subtitle: 자료형과 자료구조
minutes: 45
---

> ## 학습 목표 {.objectives}
>
> * R에서 가능한 다양한 자료형을 학습자에게 노출시킨다. 
> * 다양한 자료형을 갖는 벡터 생성법을 학습한다.
> * 벡터 자료형을 검사할 수 있어야 한다.
> * 결측치를 갖는 데이터와 다른 특수값에 대해 학습한다.
> * 다양한 자료구조(리스트, 행렬, 데이터프레임)에 대해 친숙해진다.

### R에 있는 기본 자료형 이해하기

R 언어를 가장 잘 활용하려면,
기본 자료형과 자료구조, 그리고 연산작업하는 방법에 관한 정확한 이해가 요구된다.

이해가 매우 중요한 이유는, R로 매일 조작하는 객체가 자료형과 자료구조이기 때문이다.
객체 전환(object conversion) 작업이 초보자에게 있어 가장 일반적인 좌절요인 중 하나다.

R에서 **모든 것은** 객체다.

R에는 쪼갤 수 없는 원자수준 벡터 자료형이 6개 있다. (워크샵에서 raw 클래스에 대한 논의는 하지 않을 것이다.)

* 문자(character)
* 숫자(numeric, 실수 즉 십진수)
* 정수(integer)
* 논리값(logical)
* 복소수(complex)

*쪼갤 수 없는 원자수준(atomic)*이란,
벡터가 한가지 자료형 데이터만 담게 된다는 의미다.

* **문자(character)**: `"a"`, `"swc"`
* **숫자(numeric)**: `2`, `15.5`
* **정수(integer)**: `2L` (`L`부호는 정수형으로 데이터를 저장하도록 R에게 일러준다)
* **논리값(logical)**: `TRUE`, `FALSE`
* **복소수(complex)**: `1+4i` (실수부와 허수부를 갖는 복소수)

벡터와 다른 객체에 대한 특징을 조사하는데 많은 함수가 R에 제공된다. 예를 들어,

* `class()` - (고수준에서) 어떤 유형의 객체인가?
* `typeof()` - (저수준에서) 객체의 자료형은 무엇인가?
* `length()` - 길이는 얼마나 되는가? 2차원 객체는 어떨까?
* `attributes()` - 어떤 메타데이터를 갖고 있는가?

```r
# 예제
x <- "dataset"
typeof(x)
```

```
## [1] "character"
```

```r
attributes(x)
```

```
## NULL
```

```r
y <- 1:10
y
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

```r
typeof(y)
```

```
## [1] "integer"
```

```r
length(y)
```

```
## [1] 10
```

```r
z <- as.numeric(y)
z
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

```r
typeof(z)
```

```
## [1] "double"
```

R에는 __자료구조(data structure)__가 많다. 자료구조에는 다음이 포함된다:

* 원자벡터(atomic vector)
* 리스트(list)
* 행렬(matrix)
* 데이터프레임(data frame)
* 요인(factors)

### 원자벡터(Atomic Vectors)

벡터가 R에서 가장 일반적이고 기본이 되는 자료구조다.
그리고, R을 위해 열심히 일하는 중요한 존재다.
기술적으로, 벡터는 두가지 자료형 중 하나가 될 수 있다:

* 원자벡터(atomic vectors)
* 리스트(lists)

하지만, "벡터"라는 용어는 리스트가 아닌 원자벡터를 일반적으로 지칭한다.

### 다양한 벡터 방식(mode, 모드)

벡터는 가장 일반적인 방식 `character`, `logical`, `integer`, `numeric`을 요소로 갖는 집합(collection)이다.

`vector()` 함수로 빈 벡터를 생성할 수 있다.
(기본디폴트 설정으로, 방식이 `logical`이 된다. 아래 예제에 나온 것처럼 더 명식적으로 할 수도 있다.)
`character()`, `numeric()` 등 처럼, 직접적인 생성자를 사용하는 것이 더 일반적이다.

```r
vector() # 'logical' (기본디폴트) 빈 벡터
```

```
## logical(0)
```

```r
vector("character", length = 5) # 요소 5개를 갖는 'character' 방식 벡터
```

```
## [1] "" "" "" "" ""
```

```r
character(5) # 동일하지만, 직접적으로 생성자를 사용함.
```

```
## [1] "" "" "" "" ""
```

```r
numeric(5)   # 요소 5개를 갖는 숫자 벡터
```

```
## [1] 0 0 0 0 0
```

```r
logical(5)   # 요소 5개를 갖는 논리 벡터
```

```
## [1] FALSE FALSE FALSE FALSE FALSE
```

내용물을 직접적으로 명세함으로써 벡터를 생성할 수도 있다.
그러면, R이 해당 벡터에 대해서 적절한 저장 방식을 추측한다. 예를 들어:

```r
x <- c(1, 2, 3)
```

상기 명령어는 `numeric` 방식을 갖는 벡터 `x`를 생성한다.
상기 방식이 가장 일반적인 유형으로 배정밀도를 갖는 실수형으로 처리된다.
만약 명시적으로 정수를 생성하려면, 
각 요소마다 `L`을 추가한다. (혹은 `as.integer()` 함수를 사용해서, 정수형으로 *강제한다*).


```r
x1 <- c(1L, 2L, 3L)
```

`TRUE` 와 `FALSE`를 사용하면 `logical` 방식 벡터를 생성하게 된다:

```r
y <- c(TRUE, TRUE, FALSE, FALSE)
```

반면에, 인용부호를 갖는 텍스트를 사용하면, `character` 방식 벡터가 생성된다:

```r
z <- c("Sarah", "Tracy", "Jon")
```

### 벡터 조사하기

`typeof()`, `length()`, `class()` `str()` 함수는 전반적으로 R 객체와 벡터에 대한
유용한 정보를 제공하는 역할을 한다.

```r
typeof(z)
```

```
## [1] "character"
```

```r
length(z)
```

```
## [1] 3
```

```r
class(z)
```

```
## [1] "character"
```

```r
str(z)
```

```
##  chr [1:3] "Sarah" "Tracy" "Jon"
```

> ## 도전 과제 - 공통성 알아내기 {.challenge}
>
> 상기 모든 벡터에 대해 공통된 특성을 볼 수 있는가?

### 요소 추가하기

`c()` (**c**ombine) 함수를 사용해서 벡터에 요소를 추가한다.

```r
z <- c(z, "Annette")
z
```

```
## [1] "Sarah"   "Tracy"   "Jon"     "Annette"
```

```r
z <- c("Greg", z)
z
```

```
## [1] "Greg"    "Sarah"   "Tracy"   "Jon"     "Annette"
```

### 숫자 순열에서 나온 벡터

숫자 순열로 벡터를 생성할 수 있다.

```r
series <- 1:10
seq(10)
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

```r
seq(from = 1, to = 10, by = 0.1)
```

```
##  [1]  1.0  1.1  1.2  1.3  1.4  1.5  1.6  1.7  1.8  1.9  2.0  2.1  2.2  2.3
## [15]  2.4  2.5  2.6  2.7  2.8  2.9  3.0  3.1  3.2  3.3  3.4  3.5  3.6  3.7
## [29]  3.8  3.9  4.0  4.1  4.2  4.3  4.4  4.5  4.6  4.7  4.8  4.9  5.0  5.1
## [43]  5.2  5.3  5.4  5.5  5.6  5.7  5.8  5.9  6.0  6.1  6.2  6.3  6.4  6.5
## [57]  6.6  6.7  6.8  6.9  7.0  7.1  7.2  7.3  7.4  7.5  7.6  7.7  7.8  7.9
## [71]  8.0  8.1  8.2  8.3  8.4  8.5  8.6  8.7  8.8  8.9  9.0  9.1  9.2  9.3
## [85]  9.4  9.5  9.6  9.7  9.8  9.9 10.0
```

### 결측값

R은 벡터에 결측 데이터 처리 기능을 지원한다.
결측 데이터는 `NA` (Not Available, 자료없음)으로 표현되고, 이번 학습에 다뤄지는 
모든 벡터에 사용될 수 있다:

```r
x <- c(0.5, NA, 0.7)
x <- c(TRUE, FALSE, NA)
x <- c("a", NA, "c", "d", "e")
x <- c(1+5i, 2-3i, NA)
```

`is.na()` 함수는 결측 데이터를 표현하는 벡터 요소를 표기한다.
`anyNA()` 함수는 벡터에 어떤 결측값이 포함되면 `TRUE`를 반환한다:

```r
x <- c("a", NA, "c", "d", NA)
y <- c("a", "b", "c", "d", "e")
is.na(x)
```

```
## [1] FALSE  TRUE FALSE FALSE  TRUE
```

```r
is.na(y)
```

```
## [1] FALSE FALSE FALSE FALSE FALSE
```

```r
anyNA(x)
```

```
## [1] TRUE
```

```r
anyNA(y)
```

```
## [1] FALSE
```

### 기타 특수값

`Inf`는 무한이다. 양의 무한 혹은 음의 무한일 수 있다.

```r
1/0
```

```
## [1] Inf
```

`NaN` 은 숫자가 아님(Not a Number)을 의미한다. 
`NaN`은 정의되지 않는 값이다.

```r
0/0
```

```
## [1] NaN
```

### 벡터 내부에 자료형이 뒤섞일 때 어떤 일이 생기나요?

R은 기본적으로 모든 요소를 가장 쉽게 수용할 수 있는 방식(mode)을 갖는 벡터를 생성한다.
저장 방식 사이에 이러한 전환을 "강제변환(coercion)"이라고 부른다.
R이 내용물에 기반해서 저장 방식을 전환할 때, 이를 "암묵적 강제전환(implicit coercion)"이라고 한다.
예를 들어, 다음 결과가 (실행하지 않고) 어떨 것이라고 추측되나요?

```r
xx <- c(1.7, "a")
xx <- c(TRUE, 2)
xx <- c("a", TRUE)
```

`as.<class_name>()` 함수를 사용해서, 벡터를 강제변환하는 방식을 명시적으로 제어할 수 있다:

```r
as.numeric("1")
```

```
## [1] 1
```

```r
as.character(1:2)
```

```
## [1] "1" "2"
```

### 객체 속성

객체는 __속성(attributes)__을 갖는다.
속성은 객체의 일부분이다. 속성에는 다음이 포함된다:

* 명칭(names)
* 차원명칭(dimnames)
* 차원(dim)
* 클래스(class)
* 속성(attributes), 속성은 메타데이터가 담겨진다.

또한 (벡터와 리스트에 동작하는) 길이 혹은 (문자열에 대한 ) 문자 갯수같은 
속성같은 기타 정보를 얻을 수도 있다.

```r
length(1:10)
```

```
## [1] 10
```

```r
nchar("Software Carpentry")
```

```
## [1] 18
```

### 행렬(Matrix)

R에서 행렬은 숫자와 문자 벡터의 연장이다.
행렬은 별도 객체 자료형이 아니라,
차원을 갖는 원자벡터다; 차원은 행과 열의 숫자다.

```r
m <- matrix(nrow = 2, ncol = 2)
m
```

```
##      [,1] [,2]
## [1,]   NA   NA
## [2,]   NA   NA
```

```r
dim(m)
```

```
## [1] 2 2
```

R에서 행렬은 칼럼 방향으로 채워진다.

```r
m <- matrix(1:6, nrow = 2, ncol = 3)
```

행렬을 구축하는 다른 방식은 다음과 같다.

```r
m      <- 1:10
dim(m) <- c(2, 5)
```

상기 명령어는 벡터를 받아, 이를 행 2개와 열 5개를 갖는 행렬로 변환한다.

또다른 방식은 `cbind()` 와 `rbind()`를 사용해서 행과 열을 묶는 것이다.

```r
x <- 1:3
y <- 10:12
cbind(x, y)
```

```
##      x  y
## [1,] 1 10
## [2,] 2 11
## [3,] 3 12
```

```r
rbind(x, y)
```

```
##   [,1] [,2] [,3]
## x    1    2    3
## y   10   11   12
```

`byrow` 인자를 사용해서 행렬이 어떻게 채워질지 명세할 수도 있다.

```r
mdat <- matrix(c(1,2,3, 11,12,13), nrow = 2, ncol = 3, byrow = TRUE)
mdat
```

```
##      [,1] [,2] [,3]
## [1,]    1    2    3
## [2,]   11   12   13
```

### 리스트

R에서 리스트는 컨테이너 용기처럼 동작한다.
원자벡터와 달리, 리스트 내용물은 단일 방식(mode)으로 제한되지 않아서,
어떤 자료형도 뒤섞어서 망라할 수 있다.
때때로 리스트를 포괄 벡터(generic vector)라고 부르는데, 
이유는 리스트의 요소가 R 객체의 어떤 자료형도 될 수 있기 때문이다.
심지어 리스트를 담는 리스트도 가능하다.
이러한 속성이 원자벡터와 근본적으로 다른 자료형과 리스트를 차별화한다.

리스트는 특별한 벡터 자료형이다.
각 요소는 서로 다른 자료형이 될 수 있다.

`list()` 함수를 사용하거나, `as.list()`를 사용해서 다른 객체를 강제전환해서 리스트를 생성한다.
필요한 길이를 갖는 빈 리스트는 `vector()` 함수를 사용해서 생성할 수 있다.

```r
x <- list(1, "a", TRUE, 1+4i)
x
```

```
## [[1]]
## [1] 1
## 
## [[2]]
## [1] "a"
## 
## [[3]]
## [1] TRUE
## 
## [[4]]
## [1] 1+4i
```

```r
x <- vector("list", length = 5) ## 빈 리스트
length(x)
```

```
## [1] 5
```

```r
x[[1]]
```

```
## NULL
```

```r
x <- 1:10
x <- as.list(x)
length(x)
```

```
## [1] 10
```

1. `x[1]`에 대한 클래스는 무엇이 될까요?
2. `x[[1]]`에 대한 클래스는 무엇이 될까요?

```r
xlist <- list(a = "Karthik Ram", b = 1:10, data = head(iris))
xlist
```

```
## $a
## [1] "Karthik Ram"
## 
## $b
##  [1]  1  2  3  4  5  6  7  8  9 10
## 
## $data
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
```

1. 상기 객체에 대한 길이는 얼마인가? 구조(structure)는 어떨까?

리스트는 함수 내부에서 극단적으로 유용한다.
리스틀 사용해서 함수가 반환할 수 있는 객체 단 하나에 다양한 유형의 많은 결과를 한데 묶을 수 있다.

리스트는 벡터처럼 콘솔에 출력하지 않는다.
대신에, 리스트 각 요소는 개행(newline)에서 시작된다.

요소는 이중 꺾쇠로 색인된다. 단일 꺾쇠는 리스트 하나 혹은 또다른 리스트를 반환한다.

### 데이터프레임(Dataframe)

데이터프레임은 R에서 매우 중요한 자료형이다.
대부분의 테이블 형태 데이터와 통계학에서 사용되는 *사실상의 표준(de facto)* 자료구조가 데이터프레임이다.

데이터프레임은 리스트에 있는 모든 요소가 동일한 길이를 갖는 특수한 리스트 자료형이다.

데이터프레임에는 `rownames()` 같은 부가적인 속성이 있을 수 있다.
`rownames()`은 `subject_id` 혹은 `sample_id` 같이 데이터에 주석을 달 때 유용하다.
하지만, 대부분의 경우에 사용되지는 않는다.

데이터프레임에 추가적인 정보는 다음과 같다:

* `read.csv()`와 `read.table()`로 통상 생성된다.
* `data.matrix()` (선호됨) 혹은 `as.matrix()` 함수로 행렬을 전환할 수 있다.
* 강제전환을 강제로 실행할 수는 있다. 항상 기대한 것이 얻어지는 것은 아니다.
* `data.frame()` 함수로 생성할 수도 있다.
* `nrow(dat)` 와 `ncol(dat)` 으로 행과 열의 갯수를 각각 알아낸다.
* 행명칭은 통상 1,2,...,n 이 된다.

### 수작업으로 데이터프레임 생성하기

수작업으로 데이터프레임을 생성하려면:

```r
dat <- data.frame(id = letters[1:10], x = 1:10, y = 11:20)
dat
```

```
##    id  x  y
## 1   a  1 11
## 2   b  2 12
## 3   c  3 13
## 4   d  4 14
## 5   e  5 15
## 6   f  6 16
## 7   g  7 17
## 8   h  8 18
## 9   i  9 19
## 10  j 10 20
```

> ## 유용한 데이터프레임 함수 {.callout}
>
> * `head()` - 첫행 6개를 보여준다.
> * `tail()` - 마지막 행 6개를 보여준다.
> * `dim()` - 차원 정보를 반환한다.
> * `nrow()` - 행갯수
> * `ncol()` - 열갯수
> * `str()` - 각 칼럼 구조
> * `names()` - 데이터프레임에 대한 `names` 속성을 보여주는데, 칼럼명이 제시된다.

실제로 특수 리스트라는 것을 알 수 있다:

```r
is.list(iris)
```

```
## [1] TRUE
```

```r
class(iris)
```

```
## [1] "data.frame"
```

| 차원 | 동종 자료형 | 이종 자료형 |
| ------- | ---- | ---- |
| 1-D | 원자벡터 | 리스트 |
| 2-D | 행렬 | 데이터프레임 |