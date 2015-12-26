---
layout: page
title: R 프로그래밍
subtitle: 환자 데이터 분석
minutes: 30
---

> ## 학습목표 {.objectives}
>
> *   파일에서 표 형식의 데이터를 프로그램으로 읽어들인다.
> *   값을 변수에 할당한다.
> *   개별적인 값과 데이터에서 일부분을 선택한다.
> *   데이터에 대한 데이터프레임에 연산작업을 수행한다.
> *   간단한 그래프를 화면에 출력한다.

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

### 데이터 적재하기

염증 데이터를 적재(loading)하기 위해서, 먼저 값을 담고 있는 파일이 어디에 있는지
컴퓨터에 일러줄 필요가 있다.
파일 명칭이 `inflammation-01.csv`라고 들었다.
R에게 이것은 매우 중요하다. 만약 이 절차를 잊게되면, 파일을 읽어 올 때 오류 메시지가 나온다.
현재 작업 디렉토리를 `setwd` 함수를 사용해서 변경할 수 있다.
이번 예제에서, 방금전에 생성한 디렉토리로 경로를 변경한다.

~~~{.r}
setwd("~/Desktop/r-novice-inflammation/)
~~~

유닉스 쉘과 마찬가지로 명령어를 타이핑하고 `엔터(Enter)` (혹은 `리턴(return)`) 키를 누른다. 
다른 방식으로, RStudio GUI의 메뉴 옵션(`Session` -> `Set Working Directory` -> `Choose Directory...`)을 사용하여 작업 디렉토리를 변경할 수 있다.

작업 디렉토리 내부에 `data` 디렉토리에 데이터 파일이 위치해 있다.
이제 `read.csv`를 사용하여 데이터를 R로 적재할 수 있다:

~~~{.r}
read.csv(file = "data/inflammation-01.csv", header = FALSE)
~~~

`read.csv(...)` 표현식은 [함수 호출(function call)](reference.html#function-call)로 R에게 요청하여 `read.csv` 함수를 실행하게 한다.

`read.csv`는 두 개의 [인자(arguments)](reference.html#argument)가 있다: 하나는 읽고자 하는 파일 이름이고, 다른 하나는 파일 첫 행에 데이터 칼럼(열) 이름 포함 여부다. 
파일이름은 [문자열(string)]((reference.html#string))로 될 필요가 있어서, 인용부호안에 파일이름을 넣는다. 
두번째 인수 `header`가 `FALSE`로 지정된 것은 데이터 파일이 칼럼 헤더(column header)를 가지고 있지 않음을 나타낸다. `FALSE` 값과 반대의 경우 `TRUE`가 되는 것은 4번째 학습에서 더 논의를 진행할 것이다.

> ## 조언 {.callout}
>
> `read.csv` 함수는 실제로 데이터를 가져올 때 유용한 더 많은 인자를 갖울 수 있다.
> 보충 [학습](01-supp-read-write-csv.html)에서 다양한 선택옵션에 관해서 학습할 수 있다.

함수의 유용성은 무슨 값이 인자로 전달되든지 주어진 행동을 수행한다는 것이다. 
예를 들어, `file` 인자에 다른 파일 이름을 제공하면, `read.csv`는 대신에 해당 파일을 읽어 들일 것이다. 
다음 학습에서 함수와 인자에 관해서 좀더 자세한 내용을 배울 것이다.

함수 출력결과에 대해서 어떤 특별한 것도 지시하지 않아서, 
콘솔에서 `inflammation-01.csv` 파일 전체 내용을 화면에 출력한다. 시도해 보세요.

`read.csv`는 파일을 읽어들이지만, 데이터프레임을 변수로 할당하지 않으면, 데이터를 사용할 수 없다.
변수는 `x`, `current_temperature`, `subject_id`처럼 단순히 값에 대한 이름이다.
새로운 변수를 생성하여 `<-`을 사용해서 값을 변수에 할당할 수 있다.

~~~{.r}
weight_kg <- 55
~~~

변수가 값을 가지게 되면, 변수 이름을 타이핑하고 `엔터(Enter)` 혹은 `리턴(return)`을 쳐서 변수를 출력할 수 있다. 
일반적으로 변수에 할당하는 경우를 *제외하고* R은 함수나 연산에서 반환되는 임의의 객체를 콘솔에 출력한다.

~~~{.r}
weight_kg
~~~

~~~{.output}
[1] 55

~~~

변수로 간단한 산수를 할 수 있다:

~~~{.r}
# weight in pounds:
2.2 * weight_kg
~~~

~~~{.output}
[1] 121

~~~

> ## 조언 {.callout}
>
> `#` 문자를 사용해서 코드에 주석을 추가할 수 있다. 
> 이런 방식으로 코드를 문서화하는 것은 매우 유용하다. 
> 그렇게 함으로써 다른 사람이나 자신도 다음에 코드를 읽을 때 코드가 무엇을 하는 것인지 따라가기가 쉽다.

새로운 값을 할당함으로써 객체의 값을 변경할 수 있다:

~~~{.r}
weight_kg <- 57.5
# 체중이 이제 킬로그램 단위다.
weight_kg
~~~

~~~{.output}
[1] 57.5
~~~

만약 변수를 이름이 써진 포스트잇 같은 스티커 노트라고 가정한다면, 
할당은 특정한 값에 스티커 노트를 붙이는 것과 같다:

<img src="fig/python-sticky-note-variables-01.svg" alt="Variables as Sticky Notes" />

이것이 의미하는 바는 한 객체에 값을 할당하는 것이 다른 변수의 값을 변경시키지는 않는다. 
예를 들어, 변수에 특정 개체에 대한 무게를 파운드로 저장하자:

~~~{.r}
weight_lb <- 2.2 * weight_kg
# kg 단위 체중...
weight_kg
~~~

~~~{.output}
[1] 57.5
~~~

~~~{.r}
# ...그리고 파운드 단위 체중
weight_lb
~~~


~~~{.output}
[1] 126.5
~~~

<img src="fig/python-sticky-note-variables-02.svg" alt="Creating Another Variable" />

그리고 나서 `weight_kg`를 변경하자:

~~~{.r}
weight_kg <- 100.0
# 이제 kg 단위 체중...
weight_kg
~~~

~~~{.output}
[1] 100
~~~

~~~{.r}
# ...파운드 체중은 그대로
weight_lb
~~~

~~~{.output}
[1] 126.5
~~~

<img src="fig/python-sticky-note-variables-03.svg" alt="Updating a Variable" />

`weight_lb` 변수는 값이 어디에서 왔는지 기억하지 않기 때문에, 
자동적으로 `weight_kg`이 변경될 때 갱신되지 않는다. 
엑셀같은 스프레드쉬트가 동작하는 방식과 이런 점이 다르다.

> ## 조언 {.callout} 
>An alternative way to print the value of a variable is to use () around the assignment statement. As an example: `(total_weight <- weight_kg + weight_lb)`, adds the values of `weight_kg` and `weight_lb`, assigns the result to the `total_weight`, and finally prints the assigned value of the variable `total_weight`.


Now that we know how to assign things to variables, let's re-run `read.csv` and save its result:


~~~{.r}
dat <- read.csv(file = "data/inflammation-01.csv", header = FALSE)
~~~

This statement doesn't produce any output because assignment doesn't display anything.
If we want to check that our data has been loaded, we can print the variable's value.
However, for large data sets it is convenient to use the function `head` to display only the first few rows of data.


~~~{.r}
head(dat)
~~~



~~~{.output}
  V1 V2 V3 V4 V5 V6 V7 V8 V9 V10 V11 V12 V13 V14 V15 V16 V17 V18 V19 V20
1  0  0  1  3  1  2  4  7  8   3   3   3  10   5   7   4   7   7  12  18
2  0  1  2  1  2  1  3  2  2   6  10  11   5   9   4   4   7  16   8   6
3  0  1  1  3  3  2  6  2  5   9   5   7   4   5   4  15   5  11   9  10
4  0  0  2  0  4  2  2  1  6   7  10   7   9  13   8   8  15  10  10   7
5  0  1  1  3  3  1  3  5  2   4   4   7   6   5   3  10   8  10   6  17
6  0  0  1  2  2  4  2  1  6   4   7   6   6   9   9  15   4  16  18  12
  V21 V22 V23 V24 V25 V26 V27 V28 V29 V30 V31 V32 V33 V34 V35 V36 V37 V38
1   6  13  11  11   7   7   4   6   8   8   4   4   5   7   3   4   2   3
2  18   4  12   5  12   7  11   5  11   3   3   5   4   4   5   5   1   1
3  19  14  12  17   7  12  11   7   4   2  10   5   4   2   2   3   2   2
4  17   4   4   7   6  15   6   4   9  11   3   5   6   3   3   4   2   3
5   9  14   9   7  13   9  12   6   7   7   9   6   3   2   2   4   2   0
6  12   5  18   9   5   3  10   3  12   7   8   4   7   3   5   4   4   3
  V39 V40
1   0   0
2   0   1
3   1   1
4   2   1
5   1   1
6   2   1

~~~

> ## Challenge - Assigning values to variables {.challenge}
>
> Draw diagrams showing what variables refer to what values after each statement in the following program:
>
~~~{.r}
mass <- 47.5
age <- 122
mass <- mass * 2.0
age <- age - 20
~~~

### Manipulating Data

Now that our data is loaded in memory, we can start doing things with it.
First, let's ask what type of thing `dat` is:


~~~{.r}
class(dat)
~~~



~~~{.output}
[1] "data.frame"

~~~

The output tells us that is a data frame. Think of this structure as a spreadsheet in MS Excel that many of us are familiar with.
Data frames are very useful for storing data and you will find them elsewhere when programming in R. A typical data frame of experimental data contains individual observations in rows and variables in columns.

We can see the shape, or [dimensions](reference.html#dimensions-(of-an-array)), of the data frame with the function `dim`:


~~~{.r}
dim(dat)
~~~



~~~{.output}
[1] 60 40

~~~

This tells us that our data frame, `dat`, has 60 rows and 40 columns.

If we want to get a single value from the data frame, we can provide an [index](reference.html#index) in square brackets, just as we do in math:


~~~{.r}
# first value in dat
dat[1, 1]
~~~



~~~{.output}
[1] 0

~~~



~~~{.r}
# middle value in dat
dat[30, 20]
~~~



~~~{.output}
[1] 16

~~~

An index like `[30, 20]` selects a single element of a data frame, but we can select whole sections as well.
For example, we can select the first ten days (columns) of values for the first four patients (rows) like this:


~~~{.r}
dat[1:4, 1:10]
~~~



~~~{.output}
  V1 V2 V3 V4 V5 V6 V7 V8 V9 V10
1  0  0  1  3  1  2  4  7  8   3
2  0  1  2  1  2  1  3  2  2   6
3  0  1  1  3  3  2  6  2  5   9
4  0  0  2  0  4  2  2  1  6   7

~~~

The [slice](reference.html#slice) `1:4` means, "Start at index 1 and go to index 4."

The slice does not need to start at 1, e.g. the line below selects rows 5 through 10:


~~~{.r}
dat[5:10, 1:10]
~~~



~~~{.output}
   V1 V2 V3 V4 V5 V6 V7 V8 V9 V10
5   0  1  1  3  3  1  3  5  2   4
6   0  0  1  2  2  4  2  1  6   4
7   0  0  2  2  4  2  2  5  5   8
8   0  0  1  2  3  1  2  3  5   3
9   0  0  0  3  1  5  6  5  5   8
10  0  1  1  2  1  3  5  3  5   8

~~~
We can use the function `c`, which stands for **c**ombine, to select non-contiguous values:


~~~{.r}
dat[c(3, 8, 37, 56), c(10, 14, 29)]
~~~



~~~{.output}
   V10 V14 V29
3    9   5   4
8    3   5   6
37   6   9  10
56   7  11   9

~~~

We also don't have to provide a slice for either the rows or the columns.
If we don't include a slice for the rows, R returns all the rows; if we don't include a slice for the columns, R returns all the columns.
If we don't provide a slice for either rows or columns, e.g. `dat[, ]`, R returns the full data frame.


~~~{.r}
# All columns from row 5
dat[5, ]
~~~



~~~{.output}
  V1 V2 V3 V4 V5 V6 V7 V8 V9 V10 V11 V12 V13 V14 V15 V16 V17 V18 V19 V20
5  0  1  1  3  3  1  3  5  2   4   4   7   6   5   3  10   8  10   6  17
  V21 V22 V23 V24 V25 V26 V27 V28 V29 V30 V31 V32 V33 V34 V35 V36 V37 V38
5   9  14   9   7  13   9  12   6   7   7   9   6   3   2   2   4   2   0
  V39 V40
5   1   1

~~~



~~~{.r}
# All rows from column 16
dat[, 16]
~~~



~~~{.output}
 [1]  4  4 15  8 10 15 13  9 11  6  3  8 12  3  5 10 11  4 11 13 15  5 14
[24] 13  4  9 13  6  7  6 14  3 15  4 15 11  7 10 15  6  5  6 15 11 15  6
[47] 11 15 14  4 10 15 11  6 13  8  4 13 12  9

~~~

Now let's perform some common mathematical operations to learn about our inflammation data.
When analyzing data we often want to look at partial statistics, such as the maximum value per patient or the average value per day.
One way to do this is to select the data we want to create a new temporary data frame, and then perform the calculation on this subset:


~~~{.r}
# first row, all of the columns
patient_1 <- dat[1, ]
# max inflammation for patient 1
max(patient_1)
~~~



~~~{.output}
[1] 18

~~~

We don't actually need to store the row in a variable of its own.
Instead, we can combine the selection and the function call:


~~~{.r}
# max inflammation for patient 2
max(dat[2, ])
~~~



~~~{.output}
[1] 18

~~~

R also has functions for other common calculations, e.g. finding the minimum, mean, median, and standard deviation of the data:


~~~{.r}
# minimum inflammation on day 7
min(dat[, 7])
~~~



~~~{.output}
[1] 1

~~~



~~~{.r}
# mean inflammation on day 7
mean(dat[, 7])
~~~



~~~{.output}
[1] 3.8

~~~



~~~{.r}
# median inflammation on day 7
median(dat[, 7])
~~~



~~~{.output}
[1] 4

~~~



~~~{.r}
# standard deviation of inflammation on day 7
sd(dat[, 7])
~~~



~~~{.output}
[1] 1.725187

~~~

What if we need the maximum inflammation for all patients, or the average for each day?
As the diagram below shows, we want to perform the operation across a margin of the data frame:

<img src="fig/r-operations-across-axes.svg" alt="Operations Across Axes" />

To support this, we can use the `apply` function.

> ## Tip {.callout}
>
> To learn about a function in R, e.g. `apply`, we can read its help
> documention by running `help(apply)` or `?apply`.

`apply` allows us to repeat a function on all of the rows (`MARGIN = 1`) or columns (`MARGIN = 2`) of a data frame.

Thus, to obtain the average inflammation of each patient we will need to calculate the mean of all of the rows (`MARGIN = 1`) of the data frame.


~~~{.r}
avg_patient_inflammation <- apply(dat, 1, mean)
~~~

And to obtain the average inflammation of each day we will need to calculate the mean of all of the columns (`MARGIN = 2`) of the data frame.


~~~{.r}
avg_day_inflammation <- apply(dat, 2, mean)
~~~

Since the second argument to `apply` is `MARGIN`, the above command is equivalent to `apply(dat, MARGIN = 2, mean)`.
We'll learn why this is so in the next lesson.

> ## Tip {.callout}
>
> Some common operations have more efficient alternatives. For example, you
> can calculate the row-wise or column-wise means with `rowMeans` and
> `colMeans`, respectively.

> ## Challenge - Slicing (subsetting) data {.challenge}
>
> A subsection of a data frame is called a [slice](reference.html#slice).
> We can take slices of character vectors as well:
>
> 
> ~~~{.r}
> animal <- c("m", "o", "n", "k", "e", "y")
> # first three characters
> animal[1:3]
> ~~~
> 
> 
> 
> ~~~{.output}
> [1] "m" "o" "n"
> 
> ~~~
> 
> 
> 
> ~~~{.r}
> # last three characters
> animal[4:6]
> ~~~
> 
> 
> 
> ~~~{.output}
> [1] "k" "e" "y"
> 
> ~~~
>
> 1.  If the first four characters are selected using the slice `animal[1:4]`, how can we obtain the first four characters in reverse order?
>
> 1.  What is `animal[-1]`?
>    What is `animal[-4]`?
>    Given those answers,
>    explain what `animal[-1:-4]` does.
>
> 1.  Use a slice of `animal` to create a new character vector that spells the word "eon", i.e. `c("e", "o", "n")`.


> ## Challenge - Subsetting data 2 {.challenge}
>
> Suppose you want to determine the maximum inflamation for patient 5 across days three to seven.
> To do this you would extract the relevant slice from the data frame and calculate the maximum value.
> Which of the following lines of R code gives the correct answer?
>
> (a) `max(dat[5, ])`
> (b) `max(dat[3:7, 5])`
> (c) `max(dat[5, 3:7])`
> (d) `max(dat[5, 3, 7])`

### Plotting

The mathematician Richard Hamming once said, "The purpose of computing is insight, not numbers," and the best way to develop insight is often to visualize data.
Visualization deserves an entire lecture (or course) of its own, but we can explore a few of R's plotting features.

Let's take a look at the average inflammation over time.
Recall that we already calculated these values above using `apply(dat, 2, mean)` and saved them in the variable `avg_day_inflammation`.
Plotting the values is done with the function `plot`.


~~~{.r}
plot(avg_day_inflammation)
~~~

<img src="fig/01-starting-with-data-plot-avg-inflammation-1.png" title="plot of chunk plot-avg-inflammation" alt="plot of chunk plot-avg-inflammation" style="display: block; margin: auto;" />

Above, we gave the function `plot` a vector of numbers corresponding to the average inflammation per day across all patients.
`plot` created a scatter plot where the y-axis is the average inflammation level and the x-axis is the order, or index, of the values in the vector, which in this case correspond to the 40 days of treatment.
The result is roughly a linear rise and fall, which is suspicious: based on other studies, we expect a sharper rise and slower fall.
Let's have a look at two other statistics: the maximum and minimum inflammation per day.


~~~{.r}
max_day_inflammation <- apply(dat, 2, max)
plot(max_day_inflammation)
~~~

<img src="fig/01-starting-with-data-plot-max-inflammation-1.png" title="plot of chunk plot-max-inflammation" alt="plot of chunk plot-max-inflammation" style="display: block; margin: auto;" />


~~~{.r}
min_day_inflammation <- apply(dat, 2, min)
plot(min_day_inflammation)
~~~

<img src="fig/01-starting-with-data-plot-min-inflammation-1.png" title="plot of chunk plot-min-inflammation" alt="plot of chunk plot-min-inflammation" style="display: block; margin: auto;" />

The maximum value rises and falls perfectly smoothly, while the minimum seems to be a step function. Neither result seems particularly likely, so either there's a mistake in our calculations or something is wrong with our data.

> ## Challenge - Plotting data {.challenge}
>
> Create a plot showing the standard deviation of the inflammation data for each day across all patients.

> ## Key Points {.callout}
>
> * Use `variable <- value` to assign a value to a variable in order to record it in memory.
> * Objects are created on demand whenever a value is assigned to them.
> * The function `dim` gives the dimensions of a data frame.
> * Use `object[x, y]` to select a single element from a data frame.
> * Use `from:to` to specify a sequence that includes the indices from `from` to
>`to`. 
> * All the indexing and slicing that works on data frames also works on vectors.
> * Use `#` to add comments to programs.
> * Use `mean`, `max`, `min` and `sd` to calculate simple statistics.
> * Use `apply` to calculate statistics across the rows or columns of a data frame.
> * Use `plot` to create simple visualizations.

> ## Next Steps {.callout}
>
> Our work so far has convinced us that something's wrong with our first data file.
> We would like to check the other 11 the same way, but typing in the same commands repeatedly is tedious and error-prone.
> Since computers don't get bored (that we know of), we should create a way to do a complete analysis with a single command, and then figure out how to repeat that step once for each file.
> These operations are the subjects of the next two lessons.
