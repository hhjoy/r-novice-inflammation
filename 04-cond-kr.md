---
layout: page
title: R 프로그래밍
subtitle: 조건 선택
minutes: 30
---


> ## 학습 목표 {.objectives}
>
> *   pdf 파일로 그림(plot)을 저장한다.
> *   `if`와 `else`로 구성된 조건문을 작성한다.
> *   올바르게 `&` ("and")와 `|` ("or")를 포함하는 표현식을 평가한다.

앞선 학습에서 어떻게 데이터를 다루고, 함수를 정의하고, 반복하는지 배웠다. 
하지만, 지금까지 작성한 프로그램은 어떤 데이터가 주어지든지 관계없이, 항상 동일한 작업을 수행한다. 
프로그램이 조작하는 값에 근거해서 자동으로 조건을 평가하고 선택하도록 만들고자 한다.

### 그래프를 파일로 저장하기

지금까지 `analyze` 함수를 작성해서, 염증 데이터의 요약 통계값을 도식화했다:

~~~{.r}
analyze <- function(filename) {
  # Plots the average, min, and max inflammation over time.
  # Input is character string of a csv file.
  dat <- read.csv(file = filename, header = FALSE)
  avg_day_inflammation <- apply(dat, 2, mean)
  plot(avg_day_inflammation)
  max_day_inflammation <- apply(dat, 2, max)
  plot(max_day_inflammation)
  min_day_inflammation <- apply(dat, 2, min)
  plot(min_day_inflammation)
}
~~~

또한, `analyze_all` 함수를 작성해서, 각 데이터 파일에 대한 처리를 자동화했다:

~~~{.r}
analyze_all <- function(pattern) {
  # Runs the function analyze for each file in the current working directory
  # that contains the given pattern.
  filenames <- list.files(path = "data", pattern = pattern, full.names = TRUE)
  for (f in filenames) {
    analyze(f)
  }
}
~~~

앞선 접근방법이 인터랙티브 R 세션에서는 유용하지만, 
만약 작업결과를 공동 연구자에게 보낸다면 어떨까? 
현재 데이터셋이 12개 있기 때문에, `analyze_all`을 실행하게 되면, 그래프가 36개 생성된다. 
개별적으로 그래프 각각을  저장하는 것은 지루하고 수작업이 포함되어 실수하기도 쉽다. 
그리고, 데이터 처리절차를 변경하거나 그래프 모양과 느낌을 변경이 빈번하게 발생하는 상황에서, 
동료 연구자와 갱신된 결과를 다시 공유한다면, 다시 한번 그래프를 36개 모두 저장해야만 된다.

첫 염증 데이터 통계값을 그래프 3개에 도식화한 결과를 pdf 파일에 저장하는 방법이 다음에 나와있다:

~~~{.r}
pdf("inflammation-01.pdf")
analyze("data/inflammation-01.csv")
dev.off()
~~~

`pdf` 함수는 R에서 생성된 모든 그래프를 pdf 파일로 다시 보낸다(redirect). 
상기 경우에 그래프를 일단 "inflammation-01.pdf"로 명명된 파일에 저장한다. 
pdf 파일에 저장될 그래프를 생성을 완료한 후에, 
`dev.off` 함수로 R이 더 이상 그래프를 다시 보내기(redirect) 못하게 정지한다.


> ## Tip {.callout}
>
> `dev.off` 실행하지 않고 `pdf`함수를 여러번 실행하게 되면, 
> 가장 최근에 열린 파일에 그래프가 저장된다. 
> 하지만, 연결(connection)이 닫혀지지 않아서, 이전 pdf 파일은 열 수가 없다. 
> 이 상황을 벗어나기 위해서, 
> 모든 pdf 연결(connection)을 끊을 때, `dev.off`를 실행할 필요가 있다. 
> `dev.cur` 함수를 사용해서 현재 상태를 확인할 수 있다. 
> 만일 "pdf"라고 R이 화면에 뿌려준다면, 모든 그래프는 가장 마지막에 지정된 pdf 파일에 저장된다. 
> 만약 "null device" 혹은 "RStudioGD"라면, 정상적으로 그래프가 도식화 될 것이다.

`analyze` 함수를 갱신해서, pdf 파일에 그래프를 항상 저장할 수 있다. 
하지만, 새로운 변경을 인터랙티브하게 시험하는 것은 약간 더 어렵다. 
입력값에 따라 `analyze` 함수가 그래프를 저장하거나 혹은 저장하지 않게 만드는 것이 이상적이다.


### 조건문(Conditionals)

함수를 갱신해서 그래프를 저장할지 저장하지 않을지 결정하려면, 
다중 선택옵션사항 중에서 자동으로 결정을 내리게 하는 코드를 작성할 필요가 있다. 
이런 작업을 위해서 R이 제공하는 도구를 [조건문(conditional statement)](reference.html#conditional-statement)이라고 부르고, 다음과 같다:

~~~{.r}
num <- 37
if (num > 100) {
  print("greater")
} else {
  print("not greater")
}
print("done")
~~~

~~~{.output}
[1] "not greater"
[1] "done"
~~~

코드의 두번째 줄은 `if`문을 사용하여 R에게 사용자가 선택을 원한다고 일러준다.
만약 다음 시험(test)이 참(true)이면, 
`if`문 몸통부분(아래 중괄호로 둘러싸인 행들)이 실행된다. 
만약 시험이 거짓(false)이면, `else`의 몸통부분이 대신 실행된다. 
오직 이것 아니면 저것 둘중 하나만 실행된다:

<img src="fig/python-flowchart-conditional.svg" alt="Executing a Conditional" />

상기 예제에서 `num > 100` 시험은 `FALSE` 값을 반환하는데, 
이것이 왜 `if` 문 내부 코드덩어리를 건너뛰고, 
`else` 문 내부 코드덩어리가 대신 실행된 이유다.


~~~{.r}
num > 100
~~~

~~~{.output}
[1] FALSE
~~~

그리고 유추할 수 있듯이, `FALSE`의 반대는 `TRUE`다.

~~~{.r}
num < 100
~~~


~~~{.output}
[1] TRUE
~~~

조건문이 반드시 `else`를 포함할 필요는 없다. 
만약 `else`가 없다면, R은 단순히 시험조건이 거짓이면 아무것도 실행하지 않는다:

~~~{.r}
num <- 53
if (num > 100) {
  print("num is greater than 100")
}
~~~

또한, 두가지 이상의 선택사항이 있다면, 
시험 몇개를 연쇄적으로 수행할 수 있다. 
이렇게 하면 숫자부호를 반환하는 함수 작성이 단순해진다:

~~~{.r}
sign <- function(num) {
  if (num > 0) {
    return(1)
  } else if (num == 0) {
    return(0)
  } else {
    return(-1)
  }
}

sign(-3)
~~~


~~~{.output}
[1] -1
~~~

~~~{.r}
sign(0)
~~~

~~~{.output}
[1] 0
~~~

~~~{.r}
sign(2/3)
~~~

~~~{.output}
[1] 1
~~~

동치 시험은 등호 기호(`==`)를 두개 사용한다.

> ## Tip {.callout}
>
> 다른 시험에는 크거나 같다(`>=`), 
> 작거나 같다(`<=`), 
> 같지 않다.(`!=`)가 있다.

시험(test)을 조합할 수도 있다. 
앰퍼샌드(`&`)는 "and", 수직 막대(`|`)는 "or"를 상징한다. 
`&`은 양쪽이 모두 참일 때만 참이다:

~~~{.r}
if (1 > 0 & -1 > 0) {
    print("both parts are true")
} else {
  print("at least one part is not true")
}
~~~


~~~{.output}
[1] "at least one part is not true"
~~~

반면에, `|`은 둘 중에 하나만 참이 되도 참이다.

~~~{.r}
if (1 > 0 | -1 > 0) {
    print("at least one part is true")
} else {
  print("neither part is true")
}
~~~


~~~{.output}
[1] "at least one part is true"
~~~

이런 경우, "둘 중에 하나"는 둘중 하나 혹은 둘 모두를 의미한다. 
하지만, 이쪽이나 저쪽 혹은 둘다 아니다를 의미하지는 않는다.

> ## 도전 과제 - 조건문을 사용해서 동작 변경하기 {.challenge}
>
>  + `plot_dist` 함수를 작성해서 벡터의 길이가 지정한 임계치보다 크면,
> 상자그림(boxplot)을 그리게 하고,
> 그렇지 않으면 스트립차트(stripchart)를 그리게 한다. 
> 이 작업을 수행하는 함수를 작성하는데, R함수 `boxplot`과 `stripchart`를 사용한다.
>
>~~~{.r}
> dat <- read.csv("data/inflammation-01.csv", header = FALSE)
> plot_dist(dat[, 10], threshold = 10)     # day (column) 10
>~~~
>
><img src="fig/04-cond-using-conditions-01-1.png" title="plot of chunk using-conditions-01" alt="plot of chunk using-conditions-01" style="display: block; margin: auto;" />
>
>~~~{.r}
> plot_dist(dat[1:5, 10], threshold = 10)  # samples (rows) 1-5 on day (column) 10
>~~~
>
><img src="fig/04-cond-using-conditions-01-2.png" title="plot of chunk using-conditions-01" alt="plot of chunk using-conditions-01" style="display: block; margin: auto;" />
>
>  + 동료 중 한명은 더 큰 벡터의 분포를 상자그림 대신에 히스토그램 도식화를 선호한다.
>  히스토그램과 상자그림 둘중에 하나를 선택하는데, `plot_dist` 함수를 편집해서 추가로 `use_boxplot` 인자를 넣는다.
>  기본디폴트 설정으로 `use_boxplot` 인자값을 `TRUE`로 설정해서, 벡터가 `threshold` 값보다 길 때는 자동으로 상자그램을 생성하게 한다.
>  `use_boxplot` 인자값이  `FALSE` 일 때는 대신에 히스토그램을 그리게 된다.
>  앞에서처럼, 벡터 길이가 `threshold` 보다 짧다면, `plot_dist` 함수는 스트립차트를 생성하게 된다.
>  히스토그램은 R `hist` 명령어로 생성한다.
>
> 
> ~~~{.r}
> dat <- read.csv("data/inflammation-01.csv", header = FALSE)
> plot_dist(dat[, 10], threshold = 10, use_boxplot = TRUE)   # day (column) 10 - create boxplot
> ~~~
> 
> <img src="fig/04-cond-conditional-challenge-hist-1.png" title="plot of chunk conditional-challenge-hist" alt="plot of chunk conditional-challenge-hist" style="display: block; margin: auto;" />
> 
> ~~~{.r}
> plot_dist(dat[, 10], threshold = 10, use_boxplot = FALSE)  # day (column) 10 - create histogram
> ~~~
> 
> <img src="fig/04-cond-conditional-challenge-hist-2.png" title="plot of chunk conditional-challenge-hist" alt="plot of chunk conditional-challenge-hist" style="display: block; margin: auto;" />
> 
> ~~~{.r}
> plot_dist(dat[1:5, 10], threshold = 10)                    # samples (rows) 1-5 on day (column) 10
> ~~~
> 
> <img src="fig/04-cond-conditional-challenge-hist-3.png" title="plot of chunk conditional-challenge-hist" alt="plot of chunk conditional-challenge-hist" style="display: block; margin: auto;" />

### 자동 생성된 그림 저장하기

입력값에 따라 R이 어떻게 결정을 내리는지 알게 되었다. 
함수 `analyze`를 갱신하자.

~~~{.r}
analyze <- function(filename, output = NULL) {
  # Plots the average, min, and max inflammation over time.
  # Input:
  #    filename: character string of a csv file
  #    output: character string of pdf file for saving
  if (!is.null(output)) {
    pdf(output)
  }
  dat <- read.csv(file = filename, header = FALSE)
  avg_day_inflammation <- apply(dat, 2, mean)
  plot(avg_day_inflammation)
  max_day_inflammation <- apply(dat, 2, max)
  plot(max_day_inflammation)
  min_day_inflammation <- apply(dat, 2, min)
  plot(min_day_inflammation)
  if (!is.null(output)) {
    dev.off()
  }
}
~~~

`output` 인자를 추가했고, 초기디폴트값을 `NULL`로 설정했다. 
처음에 `if`문이 `output` 인자를 확인하고, 그래프를 pdf에 저장할지 저장하지 않을지 결정한다. 
좀더 자세히 하나씩 살펴보자. 
변수가 `NULL`이면, `is.null` 함수는 `TRUE`를 반환하고, 그렇지 않은 경우는 `FALSE`를 반환한다. 
느낌표 `!`은 "반대(not)"을 나타낸다. 
그러므로, `if` 블록에 나온 행들은 `output`이 "not null"인 경우만 실행된다.


~~~{.r}
output <- NULL
is.null(output)
~~~

~~~{.output}
[1] TRUE
~~~

~~~{.r}
!is.null(output)
~~~

~~~{.output}
[1] FALSE
~~~

이제 인터랙티브하게 `analyze` 함수를 양쪽 모두 사용할 수 있다:

~~~{.r}
analyze("data/inflammation-01.csv")
~~~

<img src="fig/04-cond-inflammation-01-1.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />
<img src="fig/04-cond-inflammation-01-2.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />
<img src="fig/04-cond-inflammation-01-3.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />

그리고, 그래프를 저장하려면 다음과 같이 수행한다:

~~~{.r}
analyze("data/inflammation-01.csv", output = "inflammation-01.pdf")
~~~

한번에 하나의 데이터 파일을 처리할 때, 이제는 잘 동작한다. 
하지만, `analyze_all`함수에 출력 파일을 어떻게 지정할 수 있을까? 
파일이름 "csv" 확장자를 "pdf"로 대체할 필요가 있다. 
이를 위해서 `sub` 함수를 사용한다:


~~~{.r}
f <- "data/inflammation-01.csv"
sub("csv", "pdf", f)
~~~

~~~{.output}
[1] "data/inflammation-01.pdf"
~~~

이제 `analyze_all` 함수를 갱신하자:

~~~{.r}
analyze_all <- function(pattern) {
  # Runs the function analyze for each file in the current working directory
  # that contains the given pattern.
  filenames <- list.files(path = "data", pattern = pattern, full.names = TRUE)
  for (f in filenames) {
    pdf_name <- sub("csv", "pdf", f)
    analyze(f, output = pdf_name)
  }
}
~~~

이제는 모든 출력 결과를 코드 단 한줄로 저장할 수 있다:

~~~{.r}
analyze_all("inflammation")
~~~

이제 만약 분석에 어떤 변경을 가할 필요가 생긴다면, 
`analyze` 함수를 편집해서, 모든 그림을 `analzye_all` 함수로 재빨리 다시 생성할 수 있게 되었다.

> ## 도전 과제 - 그래프 그리는 plot 명령어 동작 변경하기 {.challenge}
>
>  + 동료 중 한명이 점 대신에 선으로 그래프를 다시 생성할 수 있는지 가능성을 타진해 왔다. 
>  `?plot` 명령어를 사용해서 도움말 문서를 읽고, `plot`함수와 관련된 인자를 찾아서, 
>  `analyze`함수를 갱신하고, 그리고 나서, `analyze_all`함수로 모든 그림을 다시 생성한다.



> ## 주요점 {.callout}
>
> *   `pdf("name.pdf")`를 사용하여 pdf 파일에 그래프를 저장하고, `dev.off()` 명령어로 더이상 pdf파일에 쓰기를 멈추게 한다.
> *   `if (condition)`를 사용해서 조건문을 시작하고, `else if (condition)`로 추가 시험조건을 달고, `else`로 기본디폴트설정을 한다.
> *   조건문의 몸통부분은 중괄호(`{ }`)로 둘러싼다.
> *   동치를 시험하기 위해서 `==`을 사용한다.
> *   `X & Y`은 X와 Y 모두가 참일때만 참이다.
> *   `X | Y`은 X 혹은 Y가 참이거나, 둘다 참인 경우 참이다.

> ## 다음 단계 {.callout}
>
> 지금 인터랙티브하게 R 코드를 작성하는 기초를 살펴봤다. 
> 마지막 학습을 통해서 파이프라인과 쉘 스크립트에서 사용해서 명령어-라인 프로그램 작성법을 학습할 것이다. 
> 그렇게 함으로써 다른 사람 작업을 통합할 수 있게 된다. 
> 이것이 다음 학습 주제이며, 마지막 수업이 된다.
