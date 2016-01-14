---
layout: page
title: R 프로그래밍
subtitle: 데이터셋 다수 분석
minutes: 30
---



> ## 학습 목표 {.objectives}
>
> * `for` 루프가 무슨 작업을 수행하는지 설명한다.
> * 올바르게 `for` 루프를 작성해서 간단한 계산을 반복한다.
> * 루프가 실행될 때, 루프 변수의 변경내역을 추적한다.
> * `for` 루프로 다른 변수가 갱신될 때, 다른 변수의 변경사항도 추적한다.
> * 함수를 사용해서 간단한 패턴을 매칭하는 파일 목록을 얻어 본다.
> * `for` 루프를 사용해서 다수의 파일을 처리한다.

단일 데이터셋에 대한, 
일별 염증율의 최소값, 평균값, 최대값 그래프를 생성하는 함수 `analyze`를 만들었다:


~~~{.r}
analyze <- function(filename) {
  # 시간에 따른 염증값 평균, 최소값, 최대값을 도식화한다.
  # 입력은 csv 파일 문자열이다.
  dat <- read.csv(file = filename, header = FALSE)
  avg_day_inflammation <- apply(dat, 2, mean)
  plot(avg_day_inflammation)
  max_day_inflammation <- apply(dat, 2, max)
  plot(max_day_inflammation)
  min_day_inflammation <- apply(dat, 2, min)
  plot(min_day_inflammation)
}

analyze("data/inflammation-01.csv")
~~~

<img src="fig/03-loops-R-inflammation-01-1.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />
<img src="fig/03-loops-R-inflammation-01-2.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />
<img src="fig/03-loops-R-inflammation-01-3.png" title="plot of chunk inflammation-01" alt="plot of chunk inflammation-01" style="display: block; margin: auto;" />

작성한 함수를 사용해서 다른 데이터셋도 하나씩 하나씩 분석할 수 있다:

~~~{.r}
analyze("data/inflammation-02.csv")
~~~

<img src="fig/03-loops-R-inflammation-02-1.png" title="plot of chunk inflammation-02" alt="plot of chunk inflammation-02" style="display: block; margin: auto;" />
<img src="fig/03-loops-R-inflammation-02-2.png" title="plot of chunk inflammation-02" alt="plot of chunk inflammation-02" style="display: block; margin: auto;" />
<img src="fig/03-loops-R-inflammation-02-3.png" title="plot of chunk inflammation-02" alt="plot of chunk inflammation-02" style="display: block; margin: auto;" />

하지만, 지금 당장 데이터셋이 12개 있고, 앞으로 더많은 데이터가 있을 것이다. 
문장 하나로 모든 데이터셋에 대한 그래프를 생성하고자 한다. 
이런 작업을 수행하기 위해서, 컴퓨터에게 어떻게 반복하는지를 학습시켜야 한다. 

### For 루프

한 문장에 나온 각 단어를 출력한다고 가정하자. 
한가지 방법은 `print` 문을 6개 사용한다:

~~~{.r}
best_practice <- c("Let", "the", "computer", "do", "the", "work")
print_words <- function(sentence) {
  print(sentence[1])
  print(sentence[2])
  print(sentence[3])
  print(sentence[4])
  print(sentence[5])
  print(sentence[6])
}

print_words(best_practice)
~~~


~~~{.output}
[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
[1] "work"
~~~

하지만, 다음 두가지 사유로 좋지 못한 접근법이다:

 1. 확장성이 없다: 만약 100개 요소를 갖는 벡터를 화면에 출력한다면, 단순하게 타이핑하기만 하면 된다.

 2. 강건하지가 못하다: 만약 조금 더 긴 벡터를 주면, 데이터의 일부분만 화면에 출력된다. 
 만약 조금 짧은 입력을 준다면, `NA` 값을 반환하는데, 이유는 존재하지 않는 벡터 요소값을 요청했기 때문이다!


~~~{.r}
best_practice[-6]
~~~


~~~{.output}
[1] "Let"      "the"      "computer" "do"       "the"     
~~~

~~~{.r}
print_words(best_practice[-6])
~~~

~~~{.output}
[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
[1] NA
~~~

> ## Tip {.callout}
>
> R은 `NA` 라는 특수한 변수가 있다. 
> `NA`는 데이터셋 결측치(**N**ot **A**vailable)를 지정하는 역할을 한다. 
> 좀더 자세한 사항은 `?NA`의 도움말과 [An Introduction to R][na]을 참조바란다.

[na]: http://cran.r-project.org/doc/manuals/r-release/R-intro.html#Missing-values


좀더 좋은 접근법이 다음에 있다:

~~~{.r}
print_words <- function(sentence) {
  for (word in sentence) {
    print(word)
  }
}

print_words(best_practice)
~~~

~~~{.output}
[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
[1] "work"
~~~

좀더 짧고 더 강건하다 --- 문자열 100개 각 문자를 화면에 출력하는 것보다 확실히 짧다:

~~~{.r}
print_words(best_practice[-6])
~~~

~~~{.output}
[1] "Let"
[1] "the"
[1] "computer"
[1] "do"
[1] "the"
~~~

`print_words` 함수의 개선된 버젼은 연산을 반복하는데, [for 루프](reference.html#for-loop)를 사용한다. 이 경우에 각각에 대해서 한번만 출력한다. 루프의 일반적인 형태는 다음과 같다.

~~~{.r}
for (변수 in 컬렉션) {
  변수를 갖고 작업을 수행한다.
}
~~~

원하는 이름으로 [루프 변수(loop variable)](reference.html#loop-variable)를 작명할 수 있으나, '변수명을 숫자로 시작할 수 없다'는 사례처럼, 몇가지 [제약(restrictions)][]이 있다. `in`은 `for`문을 구성하는 일부다.
루프 몸통부문은 중괄호(`{ }`)로 싸여있다. 루프 몸통부문이 한줄인 경우 중괄호가 필요하지 않지만, 
루프를 코딩할 때 중괄호를 포함시키는 것은 좋은 습관이다.

[restrictions]: http://cran.r-project.org/doc/manuals/R-intro.html#R-commands_003b-case-sensitivity-etc

반복적으로 변수를 갱신하는 또 다른 루프가 있다:

~~~{.r}
len <- 0
vowels <- c("a", "e", "i", "o", "u")
for (v in vowels) {
  len <- len + 1
}
# 모음 갯수
len
~~~


~~~{.output}
[1] 5
~~~

상기 작은 프로그램을 실행시켜, 각 단계별로 추적할 가치는 있다. 
`vowels` 벡터에 5개 요소가 있어서, 루프 내부 문장은 5번 실행된다. 
첫번째 루프에서, (첫 행에서 값이 할당되어) `len`은 0이고, `v`는 `"a"`가 된다. 
`len`의 초기값 0에 1을 더해, 1이 되고, 새로운 값을 나타내는 `len` 변수를 갱신한다. 
다음번 루프에서 `v`는 `"e"`이고, `len`은 1이다. 그래서 `len`은 2로 갱신된다. 
3번 더 갱신한 후에 `len` 변수값은 5가 된다; 
더이상 R이 처리할 값이 `vowels` 벡터에 남아있지 않아, 루프는 자동 종료된다.

루프 변수는 루프 상태를 기록하기 위해 사용되는 단지 변수에 불과하다. 
루프가 종료된 후에도 여전히 존재한다. 
또한, 앞에서 루프 변수로 정의된 변수를 재사용할 수 있다:

~~~{.r}
letter <- "z"
for (letter in c("a", "b", "c")) {
  print(letter)
}
~~~

~~~{.output}
[1] "a"
[1] "b"
[1] "c"
~~~

~~~{.r}
# 루프를 돌린 후에, letter 변수값은 다음과 같다.
letter
~~~

~~~{.output}
[1] "c"
~~~

벡터 길이를 알아내는 것이 너무 일반적인 작업이라, 
R에는 실제로 `length`로 불리는 내장 함수가 있음에 주목한다:

~~~{.r}
length(vowels)
~~~

~~~{.output}
[1] 5
~~~

`length` 함수는 여러분이 스스로 작성하는 다른 어떤 R 함수보다도 훨씬 빠르고 두줄 루프보다 훨씬 읽기 쉽다;
`length`함수를 사용해서 아직 만나보지 못한 많은 다른 것에 대한 길이도 알아낼 수 있다. 
그런 이유로 사용할 수 있다면 `length` 함수를 항상 사용해야 한다
(R에 데이터를 저장하는 다른 방법에 대해, 더 많은 학습하고자 한다면 링크된 [학습(lesson)](01-supp-data-structures.html)을 클릭한다).


> ## 도전 과제 - 루프 사용하기 {.challenge}
>
> 1. R에는 숫자 리스트를 생성하는 `seq`라는 내장 함수가 있다:
>
> 
> ~~~{.r}
> seq(3)
> ~~~
> 
> 
> ~~~{.output}
> [1] 1 2 3
> ~~~
>
> `seq` 함수를 사용해서, 한줄마다 하나씩 첫 **N** 개의 숫자를 화면에 출력하는 함수를 작성하세요:
>
> 
> ~~~{.r}
> print_N(3)
> ~~~
> 
> 
> ~~~{.output}
> [1] 1
> [1] 2
> [1] 3
> ~~~
>
> 2. 벡터 값의 합을 계산하는 `total` 함수를 작성하세요. 
> R에는 내장함수 `sum`이 있어서 동일한 계산을 대신할 수 있지만, 
> 이번 도전과제에는 사용하지 마세요.
>
> 
> ~~~{.r}
> ex_vec <- c(4, 8, 15, 16, 23, 42)
> total(ex_vec)
> ~~~
> 
> 
> ~~~{.output}
> [1] 108
> ~~~
>
> 3. 누승(Exponentiation)은 R의 내장함수다:
>
> 
> ~~~{.r}
> 2^4
> ~~~
> 
> 
> ~~~{.output}
> [1] 16
> 
> ~~~
>
> 동일한 결과를 계산하기 위해, 루프를 사용하는 `expo` 함수를 작성하세요.
> 
> ~~~{.r}
> expo(2, 4)
> ~~~
> 
> 
> ~~~{.output}
> [1] 16
> ~~~
>

### 다수 파일 처리하기

We now have almost everything we need to process all our data files.
The only thing that's missing is a function that finds files whose names match a pattern.
We do not need to write it ourselves because R already has a function to do this called `list.files`.

If we run the function without any arguments, `list.files()`, it returns every file in the current working directory.
We can understand this result by reading the help file (`?list.files`).
The first argument, `path`, is the path to the directory to be searched, and it has the default value of `"."` (recall from the [lesson](http://swcarpentry.github.io/shell-novice/01-filedir.html) on the Unix Shell that `"."` is shorthand for the current working directory).
The second argument, `pattern`, is the pattern being searched, and it has the default value of `NULL`.
Since no pattern is specified to filter the files, all files are returned.

So to list all the csv files, we could run either of the following:


~~~{.r}
list.files(path = "data", pattern = "csv")
~~~



~~~{.output}
 [1] "car-speeds-cleaned.csv" "car-speeds.csv"        
 [3] "inflammation-01.csv"    "inflammation-02.csv"   
 [5] "inflammation-03.csv"    "inflammation-04.csv"   
 [7] "inflammation-05.csv"    "inflammation-06.csv"   
 [9] "inflammation-07.csv"    "inflammation-08.csv"   
[11] "inflammation-09.csv"    "inflammation-10.csv"   
[13] "inflammation-11.csv"    "inflammation-12.csv"   
[15] "sample.csv"             "small-01.csv"          
[17] "small-02.csv"           "small-03.csv"          

~~~



~~~{.r}
list.files(path = "data", pattern = "inflammation")
~~~



~~~{.output}
 [1] "inflammation-01.csv" "inflammation-02.csv" "inflammation-03.csv"
 [4] "inflammation-04.csv" "inflammation-05.csv" "inflammation-06.csv"
 [7] "inflammation-07.csv" "inflammation-08.csv" "inflammation-09.csv"
[10] "inflammation-10.csv" "inflammation-11.csv" "inflammation-12.csv"

~~~


> ## Tip {.callout}
>
> For larger projects, it is recommended to organize separate parts of the
> analysis into multiple subdirectories, e.g. one subdirectory for the raw data,
> one for the code, and one for the results like figures. We have done that here
> to some extent, putting all of our data files into the subdirectory "data".
> For more advice on this topic, you can read [A quick guide to organizing
> computational biology projects][Noble2009] by William Stafford Noble.

[Noble2009]: http://www.ploscompbiol.org/article/info%3Adoi%2F10.1371%2Fjournal.pcbi.1000424


As these examples show, `list.files` result is a vector of strings, which means we can loop over it to do something with each filename in turn.
In our case, the "something" we want is our `analyze` function.

Because we have put our data in separate subdirectory, if we want to access these files
using the output of `list.files` we also need to include the "path" portion of the file name.
We can do that by using the argument `full.names = TRUE`.


~~~{.r}
list.files(path = "data", pattern = "csv", full.names = TRUE)
~~~



~~~{.output}
 [1] "data/car-speeds-cleaned.csv" "data/car-speeds.csv"        
 [3] "data/inflammation-01.csv"    "data/inflammation-02.csv"   
 [5] "data/inflammation-03.csv"    "data/inflammation-04.csv"   
 [7] "data/inflammation-05.csv"    "data/inflammation-06.csv"   
 [9] "data/inflammation-07.csv"    "data/inflammation-08.csv"   
[11] "data/inflammation-09.csv"    "data/inflammation-10.csv"   
[13] "data/inflammation-11.csv"    "data/inflammation-12.csv"   
[15] "data/sample.csv"             "data/small-01.csv"          
[17] "data/small-02.csv"           "data/small-03.csv"          

~~~



~~~{.r}
list.files(path = "data", pattern = "inflammation", full.names = TRUE)
~~~



~~~{.output}
 [1] "data/inflammation-01.csv" "data/inflammation-02.csv"
 [3] "data/inflammation-03.csv" "data/inflammation-04.csv"
 [5] "data/inflammation-05.csv" "data/inflammation-06.csv"
 [7] "data/inflammation-07.csv" "data/inflammation-08.csv"
 [9] "data/inflammation-09.csv" "data/inflammation-10.csv"
[11] "data/inflammation-11.csv" "data/inflammation-12.csv"

~~~


Let's test out running our `analyze` function by using it on the first three files in the vector returned by `list.files`:



~~~{.r}
filenames <- list.files(path = "data", pattern = "inflammation", full.names = TRUE)
filenames <- filenames[1:3]
for (f in filenames) {
  print(f)
  analyze(f)
}
~~~



~~~{.output}
[1] "data/inflammation-01.csv"

~~~

<img src="fig/03-loops-R-loop-analyze-1.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="fig/03-loops-R-loop-analyze-2.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="fig/03-loops-R-loop-analyze-3.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" />

~~~{.output}
[1] "data/inflammation-02.csv"

~~~

<img src="fig/03-loops-R-loop-analyze-4.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="fig/03-loops-R-loop-analyze-5.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="fig/03-loops-R-loop-analyze-6.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" />

~~~{.output}
[1] "data/inflammation-03.csv"

~~~

<img src="fig/03-loops-R-loop-analyze-7.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="fig/03-loops-R-loop-analyze-8.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" /><img src="fig/03-loops-R-loop-analyze-9.png" title="plot of chunk loop-analyze" alt="plot of chunk loop-analyze" style="display: block; margin: auto;" />

Sure enough, the maxima of these data sets show exactly the same ramp as the first, and their minima show the same staircase structure.

> ## Tip {.callout}
>
> In this lesson we saw how to use a simple `for` loop to repeat an operation.
> As you progress with R, you will learn that there are multiple ways to
> accomplish this. Sometimes the choice of one method over another is more a
> matter of personal style, but other times it can have consequences for the
> speed of your code. For instruction on best practices, see this supplementary
> [lesson](03-supp-loops-in-depth.html) that demonstrates how to properly repeat
> operations in R.

> ## Challenge - Using loops to analyze multiple files {.challenge}
>
> 1. Write a function called `analyze_all` that takes a filename pattern as its sole argument and runs `analyze` for each file whose name matches the pattern.



> ## Key Points {.callout}
>
> * Use `for (variable in collection)` to process the elements of a collection one at a time.
> * The body of a `for` loop is surrounded by curly braces (`{ }`).
> * Use `length(thing)` to determine the length of something that contains other values.
> * Use `list.files(path = "path", pattern = "pattern", full.names = TRUE)` to create a list of files whose names match a pattern.

> ## Next Steps {.callout}
>
> We have now solved our original problem: we can analyze any number of data files with a single command.
> More importantly, we have met two of the most important ideas in programming:
>
> * Use functions to make code easier to re-use and easier to understand.
> * Use vectors and data frames to store related values, and loops to repeat operations on them.
>
> We have one more big idea to introduce...
