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

### Examining Vectors

The functions `typeof()`, `length()`, `class()` and `str()` provide useful
information about your vectors and R objects in general.


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

> ## Challenge - Finding commonalities {.challenge}
>
> Do you see a property that's common to all these vectors above?

### Adding Elements

The function `c()` (for combine) can also be used to add elements to a vector.


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

### Vectors from a Sequence of Numbers

You can create vectors as a sequence of numbers.


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

### Missing Data

R supports missing data in vectors. They are represented as `NA` (Not Available)
and can be used for all the vector types covered in this lesson:


```r
x <- c(0.5, NA, 0.7)
x <- c(TRUE, FALSE, NA)
x <- c("a", NA, "c", "d", "e")
x <- c(1+5i, 2-3i, NA)
```

The function `is.na()` indicates the elements of the vectors that represent
missing data, and the function `anyNA()` returns `TRUE` if the vector contains
any missing values:


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

### Other Special Values

`Inf` is infinity. You can have either positive or negative infinity.


```r
1/0
```

```
## [1] Inf
```

`NaN` means Not a Number. It's an undefined value.


```r
0/0
```

```
## [1] NaN
```

### What Happens When You Mix Types Inside a Vector?

R will create a resulting vector with a mode that can most easily accommodate
all the elements it contains. This conversion between modes of storage is called
"coercion". When R converts the mode of storage based on its content, it is
referred to as "implicit coercion". For instance, can you guess what the
following do (without running them first)?


```r
xx <- c(1.7, "a")
xx <- c(TRUE, 2)
xx <- c("a", TRUE)
```

You can also control how vectors are coerced explicitly using the
`as.<class_name>()` functions:


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

### Objects Attributes

Objects can have __attributes__. Attributes are part of the object. These include:

* names
* dimnames
* dim
* class
* attributes (contain metadata)

You can also glean other attribute-like information such as length (works on
vectors and lists) or number of characters (for character strings).


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

### Matrix

In R matrices are an extension of the numeric or character vectors. They are not
a separate type of object but simply an atomic vector with dimensions; the
number of rows and columns.


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

Matrices in R are filled column-wise.


```r
m <- matrix(1:6, nrow = 2, ncol = 3)
```

Other ways to construct a matrix


```r
m      <- 1:10
dim(m) <- c(2, 5)
```

This takes a vector and transforms it into a matrix with 2 rows and 5 columns.

Another way is to bind columns or rows using `cbind()` and `rbind()`.


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

You can also use the `byrow` argument to specify how the matrix is filled. From R's own documentation:


```r
mdat <- matrix(c(1,2,3, 11,12,13), nrow = 2, ncol = 3, byrow = TRUE)
mdat
```

```
##      [,1] [,2] [,3]
## [1,]    1    2    3
## [2,]   11   12   13
```

### List

In R lists act as containers. Unlike atomic vectors, the contents of a list are
not restricted to a single mode and can encompass any mixture of data
types. Lists are sometimes called generic vectors, because the elements of a
list can by of any type of R object, even lists containing further lists. This
property makes them fundamentally different from atomic vectors.

A list is a special type of vector. Each element can be a different type.

Create lists using `list()` or coerce other objects using `as.list()`. An empty
list of the required length can be created using `vector()`


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
x <- vector("list", length = 5) ## empty list
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

1. What is the class of `x[1]`?
2. What about `x[[1]]`?


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

1. What is the length of this object? What about its structure?

Lists can be extremely useful inside functions. You can “staple” together lots
of different kinds of results into a single object that a function can return.

A list does not print to the console like a vector. Instead, each element of the
list starts on a new line.

Elements are indexed by double brackets. Single brackets will still return
a(nother) list.


### Data Frame

A data frame is a very important data type in R. It's pretty much the *de facto*
data structure for most tabular data and what we use for statistics.

A data frame is a special type of list where every element of the list has same length.

Data frames can have additional attributes such as `rownames()`, which can be
useful for annotating data, like `subject_id` or `sample_id`. But most of the
time they are not used.

Some additional information on data frames:

* Usually created by `read.csv()` and `read.table()`.
* Can convert to matrix with `data.matrix()` (preferred) or `as.matrix()`
* Coercion will be forced and not always what you expect.
* Can also create with `data.frame()` function.
* Find the number of rows and columns with `nrow(dat)` and `ncol(dat)`, respectively.
* Rownames are usually 1, 2, ..., n.

### Creating Data Frames by Hand

To create data frames by hand:


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

> ## Useful data frame functions {.callout}
>
> * `head()` - shown first 6 rows
> * `tail()` - show last 6 rows
> * `dim()` - returns the dimensions
> * `nrow()` - number of rows
> * `ncol()` - number of columns
> * `str()` - structure of each column
> * `names()` - shows the `names` attribute for a data frame, which gives the
>column names.

See that it is actually a special list:


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

| Dimensions | Homogenous | Heterogeneous |
| ------- | ---- | ---- |
| 1-D | atomic vector | list |
| 2-D | matrix | data frame |
