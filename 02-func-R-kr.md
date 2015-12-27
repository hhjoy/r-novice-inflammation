---
layout: page
title: R 프로그래밍
subtitle: 함수 생성하기
minutes: 30
---

> ## 학습 목표 {.objectives}
>
> *   인자(argument)를 받는 함수를 작성한다.
> *   함수에서 값을 반환한다.
> *   함수를 테스트한다.
> *   콜 스택(call stack)이 무엇인지 설명하고 함수가 호출될때 콜 스택에 변경사항을 추적한다.
> *   함수 인자에 대한 디폴트 값을 설정한다.
> *   왜 프로그램을 작게, 단일 목적 함수로 잘게 쪼개는지 설명한다.

만약 분석할 데이터셋이 하나라면, 
파일을 스프레드쉬트에 올려서 간단한 통계치를 구하고, 그래프를 그리는 것이 아마도 휠씬 빠르다. 
하지만, 확인할 파일이 12개나 되고, 앞으로 더 늘어난다면 얘기는 달라진다. 
이번 학습에서 함수를 어떻게 작성하는지 배워서 하나의 명령어로 작업 몇개를 반복처리할 수 있다.

### 함수 정의 하기

화씨(Fahrenheit)에서 절대온도(Kelvin)로 온도를 변환하는 `fahr_to_kelvin` 함수를 정의하는 것부터 시작하자:

~~~{.r}
fahr_to_kelvin <- function(temp) {
  kelvin <- ((temp - 32) * (5 / 9)) + 273.15
  return(kelvin)
}
~~~

`fahr_to_kelvin` 함수를 정의하고, `function` 출력결과를 함수에 할당한다. 
인자이름 목록은 괄호에 포함된다. 
다음에 함수 [몸통부문(body)](reference.html#function-body)은 함수가 수행될 때
실행되는 문장(스테이트먼트, statement)으로 중괄호(`{}`) 내부에 포함된다. 
몸통부문의 문장은 공백 2개로 들여쓰기 된다. 
들여쓰기는 코드를 읽기 쉽게 하지만, 
코드가 어떻게 동작하는지에는 영향을 주지 않는다.

함수를 호출할 때, 함수에 전달하는 값은 변수에 할당되어서 함수 내부에서 사용할 수 있다. 
함수 내부에, [반환 문장(return statement)](reference.html#return-statement)을 사용해서 요청하는 곳에 결과를 반환한다.

> ## 조언 {.callout}
>
>  R에서, 반환문장(return statement)을 포함하는 것이 반드시 필요하지는 않다.
> R은 자동적으로 함수 몸통부문 마지막 행에 있는 임의 변수를 반환한다. 
> 지금은 학습 단계로, 명시적으로 반환문장을 정의한다.

상기 함수를 실행하자. 
본인이 작성한 함수를 호출하는 것은 다른 어떤 함수를 호출하는 것과 별반 차이가 없다:

~~~{.r}
# 물에 대한 냉각점
fahr_to_kelvin(32)
~~~

~~~{.output}
[1] 273.15
~~~

~~~{.r}
# 물에 대한 끓는점
fahr_to_kelvin(212)
~~~

~~~{.output}
[1] 373.15
~~~

정의한 함수를 성공적으로 호출해서, 반환한 값에 접근할 수 있다.

### 함수 조합하기

화씨온도를 절대온도로 어떻게 변환하는지 봤기 때문에, 절대온도를 섭씨온도로 바꾸는 것은 쉽다:

~~~{.r}
kelvin_to_celsius <- function(temp) {
  celsius <- temp - 273.15
  return(celsius)
}

# 섭씨에서 영도
kelvin_to_celsius(0)
~~~

~~~{.output}
[1] -273.15
~~~

화씨온도에서 섭씨온도로 변환하는 것은 어떤가요? 
공식을 적을 수도 있지만, 그럴 필요가 없다. 
이미 작성한 두개의 함수를 [조합(compose)](reference.html#function-composition) 할 수 있다:

~~~{.r}
fahr_to_celsius <- function(temp) {
  temp_k <- fahr_to_kelvin(temp)
  result <- kelvin_to_celsius(temp_k)
  return(result)
}

# 섭씨온도로 물에 대한 냉각점
fahr_to_celsius(32.0)
~~~

~~~{.output}
[1] 0
~~~

어떻게 더 커다란 프로그램이 만들어지는지 첫번째로 맛을 봤다: 
기본 연산을 정의하고, 함수를 조합해서 더큰 덩어리로 만들어 원하는 효과를 얻었다.
실제 함수는 상기 보여진 것보다 더 크다 -- 일반적으로 대략 6줄에서 20~30줄 정도 한다. 
하지만 이보다 함수가 더 길거나 하면, 함수를 읽는 사람이 어떻게 동작하는지 이해할수 없게 되면 곤란하다.

> ## 도전 과제 - 함수 생성하기 {.challenge}
>
>  + 마지막 학습에서 `c` 함수를 사용해서 벡터에 요소(element)를 결합(concatenate)하는 것을 배웠다. 
>     예를 들어, `x <- c("A", "B", "C")` 문장은 요소 3개를 갖는 벡터 `x`를 생성한다. 
>     좀더 나아가, `c`를 사용해서 상기 벡터를 확장할 수 있다. 
>     예를 들어, `y <- c(x, "D")` 문장은 요소 4개를 갖는 벡터 `y`를 생성한다. 
>     `original`과 `wrapper` 벡터 두개를 인자로 받는 `fence` 함수를 작성하세요. 
>     `fence` 함수는 `original` 앞과 뒤를 감싸는 새로운 벡터를 반환한다:
>
> ~~~{.r}
> best_practice <- c("Write", "programs", "for", "people", "not", "computers")
> asterisk <- "***"  # R은 단일 값을 갖는 변수를 요소 하나를 갖는 벡터로 해석한다.
> fence(best_practice, asterisk)
> ~~~
> 
> ~~~{.output}
> [1] "***"       "Write"     "programs"  "for"       "people"    "not"      
> [7] "computers" "***"      
> ~~~
>
>  + 변수 `v`가 벡터를 참조한다면, `v[1]`은 벡터의 첫번째 요소이고, `v[length(v)]`은 벡터의 마지막 요소가 된다. 
       함수 `length`는 벡터의 요소 갯수를 반환한다. 
       입력값의 첫번째와 마지막 요소로만 구성된 벡터를 반환하는 `outer` 함수를 작성하세요.
> 
> ~~~{.r}
> dry_principle <- c("Don't", "repeat", "yourself", "or", "others")
> outside(dry_principle)
> ~~~
> 
> ~~~{.output}
> [1] "Don't"  "others"
> ~~~


### 콜 스택(Call Stack)

`fahr_to_celsius(32)`을 호출할 때 무슨 일이 생기는지 좀더 자세히 살펴보자. 
좀더 명확하기 하기 위해서, 변수에 초기값을 32로 설정하고 결과를 `final`에 저장해서 출발해 봅시다:

~~~{.r}
original <- 32
final <- fahr_to_celsius(original)
~~~

다음 다이어그램은 첫번째 행이 실행된 다음에 메모리가 어떻게 되지는 보여준다:

<img src="fig/python-call-stack-01.svg" alt="Call Stack (Initial State)" />

함수 `fahr_to_celsius`을 호출할 때, R은 변수 `temp`를 바로 생성하지는 *않는다*. 
대신에 [스택 프레임(stack frame)](reference.html#stack-frame)에 무언가를 생성해서, 
`fahr_to_kelvin`함수가 정의한 변수를 추적한다. 
초기에 스택 프레임은 `temp` 값만을 가지고 있다:

<img src="fig/python-call-stack-02.svg" alt="Call Stack Immediately After First Function Call" />

`fahr_to_celsius` 함수 내부에 `fahr_to_kelvin` 함수를 호출할 때, 
R은 또 다른 스택 프레임을 생성해서 `fahr_to_kelvin`의 변수를 저장한다:

<img src="fig/python-call-stack-03.svg" alt="Call Stack During First Nested Function Call" />

이제 `temp`로 불리는 살아있는 변수가 두개 있다: 
하나는 `fahr_to_celsius` 함수에 대한 인수이고, 
다른 하나는 `fahr_to_kelvin` 함수에 대한 인수다. 
프로그램의 같은 부분에 동일한 이름을 가진 변수 두개가 있는 것이 애매모호해서, 
R(그리고 다른 최신 프로그래밍 언어)은 각 함수 호출에 대해, 
새로운 스택 프레임을 생성해서, 
다른 함수에서 정의된 변수와 구별되게 함수의 변수를 보관한다.

`fahr_to_kelvin` 함수에 대한 호출이 값을 반환할 때, 
R은 `fahr_to_kelvin` 함수 스택 프레임을 사용한 후 버리고, 
절대 온도 정보를 보관하기 위해서 `fahr_to_celsius`에 대한 스택 프레임에 새로운 변수를 생성한다:

<img src="fig/python-call-stack-04.svg" alt="Call Stack After Return From First Nested Function Call" />

그리고 나서 `kelvin_to_celsius`을 호출하는데, 
함수의 변수를 저장할 스택 프레임을 생성한다는 의미다:

<img src="fig/python-call-stack-05.svg" alt="Call Stack During Call to Second Nested Function" />

다시 한번, R은 `kelvin_to_celsius` 함수가 수행완료될 때 스택 프레임을 폐기한다. 
그리고 `fahr_to_celsius` 함수를 위해 스택 프레임에 `result` 변수를 생성한다:

<img src="fig/python-call-stack-06.svg" alt="Call Stack After Second Nested Function Returns" />

마지막으로, `fahr_to_celsius` 함수 수행이 완료될 때, 
R은 *자신의* 스택 프레임을 폐기하고 초기 시작한 스택 프레임에 있는 신규 변수 `final`에 결과값을 넣는다:

<img src="fig/python-call-stack-07.svg" alt="Call Stack After All Functions Have Finished" />

이 마지막 스택 프레임은 항상 존재한다; 
작성한 코드 중에 함수 외부에서 정의한 변수를 간직한다. 
간직하지 *않는* 것은 다양한 스택 프레임에 있었던 변수다. 
만약 함수가 수행 종료된 후에 `temp` 값을 얻고자 한다면, 
R은 그런 것은 없다고 회답한다:

~~~{.r}
temp
~~~

~~~{.error}
Error in eval(expr, envir, enclos): object 'temp' not found
~~~

> ## 조언 {.callout}
>
> 상기 스택 프레임(stack frame)의 설명은 매우 일반적이다. 
> 기본 개념은 여러분이 프로그래밍하는 대부분의 언어를 이해하는데 도움을 줄 것이다. 
> 하지만, 좀더 복잡한 연산을 수행할 때, R에는 활용할 몇가지 독특한 점이 있다. 
> 좀더 고급 개념의 지식이 필요한 어떠한 것도 여기서 작성하지는 않을 것이다. 
> 향후에 R로 함수를 작성하는 것이 편해질 때, 
> [R Language Manual][man] 혹은 Hadley Wickham의 
> [Advanced R Programming][adv-r]에서 [환경장][chapter](chapter)에서 좀더 많을 것을 배울 수 있다. 
> 프레임(frame) 대신에 "환경(environment)" 용어를 R에서 사용한다.

[man]: http://cran.r-project.org/doc/manuals/r-release/R-lang.html#Environment-objects
[chapter]: http://adv-r.had.co.nz/Environments.html
[adv-r]: http://adv-r.had.co.nz/

왜 이 모든 어려움으로 갈가요? 
배열의 최대값과 최소값의 차이를 계산하는 `span`이라는 함수가 다음에 있다:

~~~{.r}
span <- function(a) {
  diff <- max(a) - min(a)
  return(diff)
}

dat <- read.csv(file = "data/inflammation-01.csv", header = FALSE)
# 염증 데이터에 대한 범위
span(dat)
~~~

~~~{.output}
[1] 20
~~~

`span` 함수는 값을 `diff` 변수에 할당함에 주목한다. 
동일한 이름의 변수(`diff`)를 매우 잘 사용해서 염증 데이터 정보를 담을 수도 있다:

~~~{.r}
diff <- read.csv(file = "data/inflammation-01.csv", header = FALSE)
# 염증 데이터에 대한 범위
span(diff)
~~~

~~~{.output}
[1] 20
~~~

함수를 호출한 뒤에 변수 `diff`가 값 20을 갖게 되는 것이 예상되지 않는다. 
그래서 (R이 전역 환경(global environment)으로 부르는) 프로그램 메인 몸통부문에서 하는 것처럼, 
변수명 `diff`는 `span` 내부에 정의된 동일한 변수를 참조할 수 없다. 
이 경우에 변수에 `diff`와 다른 이름을 아마도 선택할 수 있지만, 
변수의 값이 변경되는 경우마다, 무슨 변수명이 사용되었는지를 살펴보기 위해 호출하는 R함수의 모든 코드 행을 읽고 싶지는 않다.

기본적인 아이디어는 [캡슐화(encapsulation)](reference.html#encapsulation)이고, 
정확하고 이해하기 쉬운 프로그램을 작성하는 열쇠다. 
함수가 하는 일은 몇개의 작업을 하나로 변환하는 것이어서, 
무언가를 하고자 할 때마다 수십개에서 수백개의 문장을 수행하는 대신에, 
함수 호출 단 하나를 생각한다. 
함수가 서로에게 간섭하지 않는다면, 이 방식은 동작한다; 
만약 서로 간섭하게 되면, 다시 한번 세부사항에 주의를 기울여야 하고, 이는 급격하게 단기 기억에 과부하를 주게된다.

> ## 도전 과제 - 콜 스택 따라가기 {.challenge}
>
>  + 이전에 `fence`와 `outer` 함수를 작성했다. 
>     다음을 실행할 때 콜 스택(call stack)이 어떻게 변하는지 다이어그램을 그려보세요:
> 
> ~~~{.r}
> inner_vec <- "carbon"
> outer_vec <- "+"
> result <- outside(fence(inner_vec, outer_vec))
> ~~~

### Testing and Documenting

Once we start putting things in functions so that we can re-use them, we need to start testing that those functions are working correctly.
To see how to do this, let's write a function to center a dataset around a particular value:


~~~{.r}
center <- function(data, desired) {
  new_data <- (data - mean(data)) + desired
  return(new_data)
}
~~~

We could test this on our actual data, but since we don't know what the values ought to be, it will be hard to tell if the result was correct.
Instead, let's create a vector of 0s and then center that around 3.
This will make it simple to see if our function is working as expected:


~~~{.r}
z <- c(0, 0, 0, 0)
z
~~~



~~~{.output}
[1] 0 0 0 0

~~~



~~~{.r}
center(z, 3)
~~~



~~~{.output}
[1] 3 3 3 3

~~~

That looks right, so let's try center on our real data. We'll center the inflammation data from day 4 around 0:


~~~{.r}
dat <- read.csv(file = "data/inflammation-01.csv", header = FALSE)
centered <- center(dat[, 4], 0)
head(centered)
~~~



~~~{.output}
[1]  1.25 -0.75  1.25 -1.75  1.25  0.25

~~~

It's hard to tell from the default output whether the result is correct, but there are a few simple tests that will reassure us:


~~~{.r}
# original min
min(dat[, 4])
~~~



~~~{.output}
[1] 0

~~~



~~~{.r}
# original mean
mean(dat[, 4])
~~~



~~~{.output}
[1] 1.75

~~~



~~~{.r}
# original max
max(dat[, 4])
~~~



~~~{.output}
[1] 3

~~~



~~~{.r}
# centered min
min(centered)
~~~



~~~{.output}
[1] -1.75

~~~



~~~{.r}
# centered mean
mean(centered)
~~~



~~~{.output}
[1] 0

~~~



~~~{.r}
# centered max
max(centered)
~~~



~~~{.output}
[1] 1.25

~~~

That seems almost right: the original mean was about 1.75, so the lower bound from zero is now about -1.75.
The mean of the centered data is 0.
We can even go further and check that the standard deviation hasn't changed:


~~~{.r}
# original standard deviation
sd(dat[, 4])
~~~



~~~{.output}
[1] 1.067628

~~~



~~~{.r}
# centerted standard deviation
sd(centered)
~~~



~~~{.output}
[1] 1.067628

~~~

Those values look the same, but we probably wouldn't notice if they were different in the sixth decimal place.
Let's do this instead:


~~~{.r}
# difference in standard deviations before and after
sd(dat[, 4]) - sd(centered)
~~~



~~~{.output}
[1] 0

~~~

Sometimes, a very small difference can be detected due to rounding at very low decimal places.
R has a useful function for comparing two objects allowing for rounding errors, `all.equal`:


~~~{.r}
all.equal(sd(dat[, 4]), sd(centered))
~~~



~~~{.output}
[1] TRUE

~~~

It's still possible that our function is wrong, but it seems unlikely enough that we should probably get back to doing our analysis.
We have one more task first, though: we should write some [documentation](reference.html#documentation) for our function to remind ourselves later what it's for and how to use it.

A common way to put documentation in software is to add [comments](reference.html#comment) like this:


~~~{.r}
center <- function(data, desired) {
  # return a new vector containing the original data centered around the
  # desired value.
  # Example: center(c(1, 2, 3), 0) => c(-1, 0, 1)
  new_data <- (data - mean(data)) + desired
  return(new_data)
}
~~~

> ## Tip {.callout}
>
> Formal documentation for R functions is written in separate `.Rd` using a
> markup language similar to [LaTeX][]. You see the result of this documentation
> when you look at the help file for a given function, e.g. `?read.csv`.
> The [roxygen2][] package allows R coders to write documentation alongside
> the function code and then process it into the appropriate `.Rd` files.
> You will want to switch to this more formal method of writing documentation
> when you start writing more complicated R projects.

[LaTeX]: http://www.latex-project.org/
[roxygen2]: http://cran.r-project.org/web/packages/roxygen2/vignettes/rd.html





> ## Challenge - A more advanced function {.challenge}
>
>  + Write a function called `analyze` that takes a filename as a argument and displays the three graphs produced in the [previous lesson][01] (average, min and max inflammation over time).
>  `analyze("data/inflammation-01.csv")` should produce the graphs already shown, while `analyze("data/inflammation-02.csv")` should produce corresponding graphs for the second data set. Be sure to document your function with comments.
>  + Write a function `rescale` that takes a vector as input and returns a corresponding vector of values scaled to lie in the range 0 to 1.
>  (If $L$ and $H$ are the lowest and highest values in the original vector, then the replacement for a value $v$ should be $(v-L) / (H-L)$.)
>  Be sure to document your function with comments.
>  + Test that your `rescale` function is working properly using `min`, `max`, and `plot`.

[01]: 01-starting-with-data.html



### Defining Defaults

We have passed arguments to functions in two ways: directly, as in `dim(dat)`, and by name, as in `read.csv(file = "data/inflammation-01.csv", header = FALSE)`.
In fact, we can pass the arguments to `read.csv` without naming them:


~~~{.r}
dat <- read.csv("data/inflammation-01.csv", FALSE)
~~~

However, the position of the arguments matters if they are not named.


~~~{.r}
dat <- read.csv(header = FALSE, file = "data/inflammation-01.csv")
dat <- read.csv(FALSE, "data/inflammation-01.csv")
~~~



~~~{.error}
Error in read.table(file = file, header = header, sep = sep, quote = quote, : 'file' must be a character string or connection

~~~

To understand what's going on, and make our own functions easier to use, let's re-define our `center` function like this:


~~~{.r}
center <- function(data, desired = 0) {
  # return a new vector containing the original data centered around the
  # desired value (0 by default).
  # Example: center(c(1, 2, 3), 0) => c(-1, 0, 1)
  new_data <- (data - mean(data)) + desired
  return(new_data)
}
~~~

The key change is that the second argument is now written `desired = 0` instead of just `desired`.
If we call the function with two arguments, it works as it did before:


~~~{.r}
test_data <- c(0, 0, 0, 0)
center(test_data, 3)
~~~



~~~{.output}
[1] 3 3 3 3

~~~

But we can also now call `center()` with just one argument, in which case `desired` is automatically assigned the default value of `0`:


~~~{.r}
more_data <- 5 + test_data
more_data
~~~



~~~{.output}
[1] 5 5 5 5

~~~



~~~{.r}
center(more_data)
~~~



~~~{.output}
[1] 0 0 0 0

~~~

This is handy: if we usually want a function to work one way, but occasionally need it to do something else, we can allow people to pass an argument when they need to but provide a default to make the normal case easier.

The example below shows how R matches values to arguments


~~~{.r}
display <- function(a = 1, b = 2, c = 3) {
  result <- c(a, b, c)
  names(result) <- c("a", "b", "c")  # This names each element of the vector
  return(result)
}

# no arguments
display()
~~~



~~~{.output}
a b c 
1 2 3 

~~~



~~~{.r}
# one argument
display(55)
~~~



~~~{.output}
 a  b  c 
55  2  3 

~~~



~~~{.r}
# two arguments
display(55, 66)
~~~



~~~{.output}
 a  b  c 
55 66  3 

~~~



~~~{.r}
# three arguments
display (55, 66, 77)
~~~



~~~{.output}
 a  b  c 
55 66 77 

~~~

As this example shows, arguments are matched from left to right, and any that haven't been given a value explicitly get their default value.
We can override this behavior by naming the value as we pass it in:


~~~{.r}
# only setting the value of c
display(c = 77)
~~~



~~~{.output}
 a  b  c 
 1  2 77 

~~~

> ## Tip {.callout}
>
> To be precise, R has three ways that arguments supplied
>
> by you are matched to the *formal arguments* of the function definition
>
> 1. by complete name,
> 2. by partial name (matching on initial *n* characters of the argument name), and
> 3. by position.
>
> Arguments are matched in the manner outlined above in *that order*: by
> complete name, then by partial matching of names, and finally by position.

With that in hand, let's look at the help for `read.csv()`:


~~~{.r}
?read.csv
~~~

There's a lot of information there, but the most important part is the first couple of lines:


~~~{.r}
read.csv(file, header = TRUE, sep = ",", quote = "\"",
         dec = ".", fill = TRUE, comment.char = "", ...)
~~~

This tells us that `read.csv()` has one argument, `file`, that doesn't have a default value, and six others that do.
Now we understand why the following gives an error:


~~~{.r}
dat <- read.csv(FALSE, "data/inflammation-01.csv")
~~~



~~~{.error}
Error in read.table(file = file, header = header, sep = sep, quote = quote, : 'file' must be a character string or connection

~~~

It fails because `FALSE` is assigned to `file` and the filename is assigned to the argument `header`.

> ## Challenge - A function with default argument values {.challenge}
>
>  + Rewrite the `rescale` function so that it scales a vector to lie between 0 and 1 by default, but will allow the caller to specify lower and upper bounds if they want.
>  Compare your implementation to your neighbor's: do the two functions always behave the same way?



> ## Key Points {.callout}
>
> * Define a function using `name <- function(...args...) {...body...}`.
> * Call a function using `name(...values...)`.
> * Each time a function is called, a new stack frame is created on the [call stack](reference.html#call-stack) to hold its arguments and local variables.
> * R looks for variables in the current stack frame before looking for them at the top level.
> * Use `help(thing)` to view help for something.
> * Put comments at the beginning of functions to provide help for that function.
> * Annotate your code!
> * Specify default values for arguments when defining a function using `name = value` in the argument list.
> * Arguments can be passed by matching based on name, by position, or by omitting them (in which case the default value is used).

> ## Next Steps {.callout}
>
> We now have a function called analyze to visualize a single data set.
> We could use it to explore all 12 of our current data sets like this:
> 
> 
> ~~~{.r}
> analyze("data/inflammation-01.csv")
> analyze("data/inflammation-02.csv")
> #...
> analyze("data/inflammation-12.csv")
> ~~~
>
> but the chances of us typing all 12 filenames correctly aren't great, and we'll be even worse off if we get another hundred files.
> What we need is a way to tell R to do something once for each file, and that will be the subject of the next lesson.
