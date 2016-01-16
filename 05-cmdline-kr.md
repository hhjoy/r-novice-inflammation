---
layout: page
title: R 프로그래밍
subtitle: 명령-라인 프로그램
minutes: 30
---

> ## 학습 목표 {.objectives}
>
> *   프로그램에 명령어-라인 인자값을 사용한다.
> *   명령어-라인 프로그램에서 플래그와 파일을 따로 처리한다.
> *   프로그램에서 표준 입력으로부터 데이터를 읽어들여, 파이프라인에서 사용되게 한다.

R콘솔이나 RStudio 같은 인터랙티브 개발 툴은 시제품 코드(Prototyping code) 개발이나 데이터 탐색에는 훌륭하다. 
하지만 조만간 파이프라인(pipeline)에 프로그램을 사용하고, 데이터 파일 수천개를 처리하는데 쉘 스크립트를 사용해서 실행하고 싶을 것이다. 
이를 위해서, 유닉스 명령어-라인 도구와 함께 동작하도록 프로그램을 작성할 필요가 있다. 
예를 들어, 데이터셋을 읽어들여, 환자마다 염증평균값을 출력하는 프로그램을 작성한다:


~~~
$ Rscript readings.R --mean data/inflammation-01.csv
5.45
5.425
6.1
...
6.4
7.05
5.9
~~~

하지만, 첫 4줄에 나온 최소값만을 보고자 한다.

~~~
$ head -4 data/inflammation-01.csv | Rscript readings.R --min
~~~

혹은 몇개 파일에서 최대 염증값을 하나씩 보고자 한다:

~~~
$ Rscript readings.R --max data/inflammation-*.csv
~~~

요구사항을 정리하면 대략 다음과 같다:

1. 파일 이름이 명령-라인에 주어지지 않는다면, 데이터를 [표준입력(standard input)](reference.html#standard-input-(stdin))에서 불러온다.
2. 파일 이름이 하나 혹은 그 이상 주어지면, 파일 이름에서 데이터를 읽고, 각 파일별로 통계량을 보고한다.
3. 어떤 통계량을 출력할지를 `--min`, `--mean`, `--max` 플래그를 사용해서 결정한다.

상기 요구사항을 만족시키려면, 프로그램에서 명령어-라인 인자를 처리하는 방법과 표준 입력을 받는 방법을 이해할 필요가 있다. 이런 질문을 차례로 아래에서 다룬다.


### 명령어-라인(Command-Line) 인자

여러분이 선호하는 텍스트 편집기를 사용해서, 
`session-info.R` 텍스트 파일에 다음 코드를 추가하고 저장한다:


~~~{.output}
sessionInfo()
~~~

`sessionInfo` 함수는 실행되는 R 버젼과 사용하고 있는 컴퓨터 사양, 그리고 적재된 패키지 버젼을 화면에 출력한다.
`sessionInfo` 출력정보에는 여러분이 작성한 R 코드에 대한 도움을 다른 사람들이 요청했을 때 포함될 매우 유용한 정보가 포함되어 있다.

이제 `Rscript`를 사용하여 유닉스 쉘에서 생성한 코드를 실행할 수 있다:

~~~{.r}
Rscript session-info.R
~~~

~~~{.output}
R version 3.2.2 (2015-08-14)
Platform: x86_64-pc-linux-gnu (64-bit)
Running under: Ubuntu 14.04.3 LTS

locale:
 [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
 [3] LC_TIME=en_US.UTF-8        LC_COLLATE=en_US.UTF-8    
 [5] LC_MONETARY=en_US.UTF-8    LC_MESSAGES=en_US.UTF-8   
 [7] LC_PAPER=en_US.UTF-8       LC_NAME=C                 
 [9] LC_ADDRESS=C               LC_TELEPHONE=C            
[11] LC_MEASUREMENT=en_US.UTF-8 LC_IDENTIFICATION=C       

attached base packages:
[1] stats     graphics  grDevices utils     datasets  base     
~~~

> ## Tip {.callout}
>
> 만약 정상적으로 동작하지 않는다면, 
> 올바른 디렉토리에 있는지 확인한다. 
> `pwd` 명령어를 사용해서, 현재 어느 디렉토리에 있는지 확인할 수 있고, 
> `cd` 명령어를 사용해서, 다른 디렉토리로 변경할 수 있다. 
> 다시 복습하려면, [쉘 학습](https://swcarpentry.github.io/shell-novice/01-filedir.html)을 참고한다.

좀더 흥미로운 작업을 수행하는 또다른 스크립트를 생성하자.
 `print-args.R`라고 작명하고, 파일에 다음 행을 작성하고 저장한다:

~~~{.output}
args <- commandArgs()
cat(args, sep = "\n")
~~~

`commandArgs` 함수는 명령어 라인에 있는 모든 인자를 뽑아내서 벡터로 반환한다. 
유닉스 쉘의 `cat`과 유사한 R 함수 `cat`은 변수 내용을 출력한다. 
출력결과를 저장할 파일명을 명기하지 않아서, `cat`은 출력결과를 [표준 출력(standard output)](reference.html#standard-output-(stdout))으로 보낸다. 
이와 같은 방식으로, 다른 유닉스 함수에 출력결과를 보낼 수 있다. 
`sep` 인자를 개행(new line) 기호인 `"\n"`을 설정했기 때문에, 
벡터의 각 요소는 새로운 행에 출력된다. 
유닉스 쉘에서 이 프로그램을 실행할 때, 무슨 일이 생기는지 살펴보자:


~~~{.r}
Rscript print-args.R
~~~

~~~{.output}
/usr/lib/R/bin/exec/R
--slave
--no-restore
--file=print-args.R
~~~

상기 출력결과로부터, 
`Rscript`는 R 스크립트를 실행하는 단지 편의상 명령어라는 것을 알 수 있다. 
벡터의 첫 인자는 `R` 실행파일에 대한 경로다. 
R 동작에 영향을 주는 모든 명령어-라인 인자는 다음과 같다. 
R 도움말 파일로부터...:

*  `--slave`: R을 가능하면 조용하게 실행하게 한다.
*  `--no-restore`: R 세션이 유지되는 동안, 생성된 어떤 것도 복원하지 않는다.
*  `--file`: 이 파일을 실행한다.
*  `--args`: 다음 인자를 실행될 파일에 전달한다.

따라서, 다음을 실행하는 것보다. Rscript로 파일을 실행하는 것이 더 쉬운 방법이다:

~~~{.r}
R --slave --no-restore --file=print-args.R --args
~~~

~~~{.output}
/usr/lib/R/bin/exec/R
--slave
--no-restore
--file=print-args.R
--args
~~~

하지만, 만약 인자 몇개로 실행하면:

~~~{.r}
Rscript print-args.R first second third
~~~

~~~{.output}
/usr/lib/R/bin/exec/R
--slave
--no-restore
--file=print-args.R
--args
first
second
third
~~~

그러면 `commandArgs`는 반환되는 벡터에 인자 각각을 추가한다. 
벡터 첫 요소는 항상 동일하기 때문에, `--args` 뒤에만 오는 인자를 반환하도록 `commandArgs`에 명령할 수 있다. 
`print-args.R`을 갱신하고 나서 `print-args-trailing.R` 명칭으로 저장한다:

~~~{.output}
args <- commandArgs(trailingOnly = TRUE)
cat(args, sep = "\n")
~~~

그리고 나서 유닉스 쉘에서 `print-args-trailing`을 실행한다:


~~~{.r}
Rscript print-args-trailing.R first second third
~~~

~~~{.output}
first
second
third
~~~

이제 `commandArgs`는  `print-args-trailing.R` 다음에 나열된 목록 인자만을 반환한다.

이것을 가지고, `readings.R` 버젼을 작성해서, 
데이터 파일 한개에 나온 환자별(행별) 평균을 항상 출력하게 한다.
첫번째 단계는 프로그램 구현 개요를 기술하는 함수와 
실제 작업을 하는 함수에 대한 자리 표시자(placeholder)를 작성하는 것이다. 
관례로, 이런 함수를 통상 `main` 메인이라고 부른다. 
하지만 원하는 임의 이름을 붙일 수도 있다. 
`readings-01.R` 파일을 생성해서, 다음과 같이 코드를 작성한다:

~~~{.output}
main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  filename <- args[1]
  dat <- read.csv(file = filename, header = FALSE)
  mean_per_patient <- apply(dat, 1, mean)
  cat(mean_per_patient, sep = "\n")
}
~~~

상기 함수는 `commandArgs`에서 반환되는 첫번째 요소를 처리할 파일명으로 받는다. 
유닉스 쉘에서 수행하는 간단한 테스트가 다음에 나와있다:

~~~{.r}
Rscript readings-01.R data/inflammation-01.csv
~~~

출력되는 것은 아무것도 없는데, 이유는 함수를 정의했지만, 실제로 호출하지는 않았기 때문이다. 
`main`에 호출을 추가하고, `readings-02.R`로 파일을 저장하자:

~~~{.output}
main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  filename <- args[1]
  dat <- read.csv(file = filename, header = FALSE)
  mean_per_patient <- apply(dat, 1, mean)
  cat(mean_per_patient, sep = "\n")
}

main()
~~~

~~~{.r}
Rscript readings-02.R data/inflammation-01.csv
~~~

~~~{.output}
5.45
5.425
6.1
5.9
5.55
6.225
5.975
6.65
6.625
6.525
6.775
5.8
6.225
5.75
5.225
6.3
6.55
5.7
5.85
6.55
5.775
5.825
6.175
6.1
5.8
6.425
6.05
6.025
6.175
6.55
6.175
6.35
6.725
6.125
7.075
5.725
5.925
6.15
6.075
5.75
5.975
5.725
6.3
5.9
6.75
5.925
7.225
6.15
5.95
6.275
5.7
6.1
6.825
5.975
6.725
5.7
6.25
6.4
7.05
5.9
~~~

> ## 도전 과제 - 단순한 명령-라인 프로그램 {.challenge}
>
>  + 덧셈과 뺄셈을 수행하는 명령어-라인 프로그램을 작성한다.
>   **힌트:** 명령어-라인에서 읽혀지는 모든 인자는 [문자열(string)](reference.html#string)로 해석된다. 
>   `as.numeric` 함수를 사용해서 문자열을 숫자로 변환한다.
> 
> ~~~{.r}
> Rscript arith.R 1 + 2
> ~~~
> 
> 
> ~~~{.output}
> 3
> ~~~
>
> ~~~{.r}
> Rscript arith.R 3 - 4
> ~~~
> 
> ~~~{.output}
> -1
> ~~~
>
>  + 만약 프로그램에 `*`을 사용해서 곱하기를 추가한다면, 어떤 것이 잘못될까요?
>
> <!-- second-answer -->
> <!-- The * is a wildcard character in the Unix Shell. -->
> <!-- Thus all the files in the current working directory are included as arguments to  arith.R. -->
>
>  + 이전 [루프 학습](03-loops-R.html)에서 소개된 `list.files` 함수를 사용해서,
>     `find-pattern.R`이라는 명령문-라인 프로그램을 작성하는데, 
>     특정한 패턴을 갖는 현재 디렉토리에서 있는 파일 모두를 목록으로 출력한다:
> 
> ~~~{.r}
> # For example, searching for the pattern "print-args" returns the two scripts we
> # wrote earlier
> Rscript find-pattern.R print-args
> ~~~
> 
> ~~~{.output}
> print-args.R
> print-args-trailing.R
> ~~~
>


###  다수 파일 처리하기

다음 단계는 프로그램에게 다수 파일을 처리하는 방법을 가르치는 것이다. 
파일당 출력결과 60줄은 페이지를 넘기며 살펴보기에는 많은 불량이여서, 일단 작은 파일 3개로 시작한다. 
작은 파일 각각은 환자 두명에 대한 3일치 데이터가 있다. 
유닉스 쉘에서 작은 파일을 살펴보자:

~~~{.r}
ls data/small-*.csv
~~~

~~~{.output}
data/small-01.csv
data/small-02.csv
data/small-03.csv
~~~

~~~{.r}
cat data/small-01.csv
~~~

~~~{.output}
0,0,1
0,1,2
~~~

~~~{.r}
Rscript readings-02.R data/small-01.csv
~~~

~~~{.output}
0.3333333
1
~~~

작은 파일을 입력값으로 사용하는 것이 좀더 결과를 쉽게 확인할 수 있게 한다: 
예를 들어, 프로그램이 각 행마다 올바르게 평균을 계산하는지 살펴볼 수도 있다. 
반면에 전체 파일을 사용하게 되면 이전과 마찬가지로 정말 잘 계산된다는 믿음으로만 가지게 된다. 
이것이 또 다른 프로그래밍 규칙이다: "간단한 것을 먼저 시험하라(test the simple things first)".

작성한 프로그램이 파일 각각을 개별적으로 처리되길 원해서, 각 파일명마다 한번씩 실행되는 반복루프가 필요하다. 
명령어 라인에 파일명을 지정한다면, 파일 이름은 `commandArgs(trailingOnly = TRUE)` 명령문에 의해서 반환될 것이다. 
작성한 프로그램이 임의 갯수 파일에 대해서 실행될 수 있기 때문에, 
파일이름 갯수를 파악할 수 없는 상황에도 동작할 필요가 있다.

해결책은 `commandArgs(trailingOnly = TRUE)`에서 반환되는 벡터에 대해 루프를 돌리는 것이다. 
변경된 프로그램이 다음에 있는데 `readings-03.R` 이름으로 저장한다:

~~~{.output}
main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  for (filename in args) {
    dat <- read.csv(file = filename, header = FALSE)
    mean_per_patient <- apply(dat, 1, mean)
    cat(mean_per_patient, sep = "\n")
  }
}

main()
~~~

그리고 실행 결과가 다음에 나와있다:

~~~{.r}
Rscript readings-03.R data/small-01.csv data/small-02.csv
~~~

~~~{.output}
0.3333333
1
13.66667
11
~~~

**주의**: 이 시점에서, 스크립트 버젼 3개(`readings-01.R`, `readings-02.R`, `readings-03.R`)를 생성했다. 실무에서 이렇게 하지는 않는다: 대신에, `readings.R` 파일만 보관하고 기능향상 작업이 완료될 때마다 버젼 제어 시스템에 커밋한다. 
하지만, 교육 목적으로 나란히 연속된 스크립트 버젼은 필요하다.


> ## 도전 과제 - 인자를 갖는 명령-라인 프로그램 {.challenge}
>
>  + `check.R` 프로그램을 작성해서, 인자로 염증 데이터 파일명을 하나이상 받아서, 모든 파일이 동일한 행과 열을 갖는지 검증한다. 
>     이렇게 작성한 프로그램을 테스트 시험하는 가장 최선의 방법은 무엇인가요?


### 명령어-라인 플래그(Command-Line Flags) 처리하기

다음 단계는 프로그램이 `--min`, `--mean`, `--max` 플래그에 관심을 갖도록 가르친다.
플래그는 항상 파일명 앞에 위치한다. 
`readings-04.R` 파일에 다음 사항을 저장하자:

~~~{.output}
main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  action <- args[1]
  filenames <- args[-1]
  
  for (f in filenames) {
    dat <- read.csv(file = f, header = FALSE)
    
    if (action == "--min") {
      values <- apply(dat, 1, min)
    } else if (action == "--mean") {
      values <- apply(dat, 1, mean)
    } else if (action == "--max") {
      values <- apply(dat, 1, max)
    }
    cat(values, sep = "\n")
  }
}

main()
~~~

유닉스 쉘에서 다음을 실행해서, 상기 작성한 것이 정상적으로 동작하는지 확인한다:

~~~{.r}
Rscript readings-04.R --max data/small-01.csv
~~~


~~~{.output}
1
2
~~~

하지만, 몇가지 잘못된 것이 있다:

1.  `main` 함수가 너무 커서, 가독성이 떨어져 편안하게 읽기가 쉽지 않다.

2.  `action` 인자가 인지된 3개의 플래그 중에 하나가 아니라면, 
     프로그램이 파일 각각을 불러 로딩(loading)하지만, 아무것도 수행하기 않는다. 
     왜냐하면, 조건을 매칭하는 곳에서 어느 분기에도 해당되지 않기 때문이다. 
     이와 같이 [침묵하는 실패(Silent failures)](reference.html#silent-failure)가 항상 디버그하기 어렵다.

새로 작성한 버젼은 파일 각가에 대한 처리를 루프에서 빼내서 별도 처리하는 함수로 만들었다. 
임의 처리를 수행하기 전에 `action`이 사전에 정의된 플래그 중 하나인지 검사하도록 한다.
그렇게 함으로써, 만약 문제가 있다면 프로그램이 조기 실패로 끝나 종료된다.
`readings-05.R` 이름으로 프로그램을 저장한다:


~~~{.output}
main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  action <- args[1]
  filenames <- args[-1]
  stopifnot(action %in% c("--min", "--mean", "--max"))
  
  for (f in filenames) {
    process(f, action)
  }
}

process <- function(filename, action) {
  dat <- read.csv(file = filename, header = FALSE)
  
  if (action == "--min") {
    values <- apply(dat, 1, min)
  } else if (action == "--mean") {
    values <- apply(dat, 1, mean)
  } else if (action == "--max") {
    values <- apply(dat, 1, max)
  }
  cat(values, sep = "\n")
}

main()
~~~

상기 프로그램은 앞서 작성한 프로그램보다 4줄 더 길다. 
하지만, 좀더 완전히 이해하기 쉬운 8줄, 12줄 프로그램 덩어리로 쪼갰다.

> ## Tip {.callout}
>
> R [argparse][argparse-r] R 패키지가 복잡한 명령어-라인 플래그를 처리하는데 도움이 된다. 
> [argparse][argparse-r] 패키지는 동일한 이름 [파이썬 모듈][argparse-py]을 활용한다. 
> 이번 학습에서는 [argparse][argparse-r] 패키지를 다루지 않을 것이다. 
> 하지만, 다수의 매개변수를 가진 프로그램을 작성할 때, 패키지 
> [소품문(비네트, vignette)][]을 읽으면 도움이 된다.

[argparse-r]: http://cran.r-project.org/web/packages/argparse/index.html
[argparse-py]: http://docs.python.org/dev/library/argparse.html
[vignette]: http://cran.r-project.org/web/packages/argparse/vignettes/argparse.pdf

> ## 도전 과제 - 더 짧은 명령-라인 인자 {.challenge}
>
>  + 상기 프로그래을 다시 작성해서 `--min`, `--mean`, `--max` 대신에 `-n`, `-m`, `-x`을 각각 사용하게 한다. 어떤 코드가 가독성이 더 좋은가요? 어떤 프로그램이 더 이해하기 좋은가요?
> 
>  + 이와는 별도로 프로그램을 변경해서, 만약 어떤 동작(action)도 명기되지 않거나, 혹은 잘못된 동작이 주어지면, 어떻게 사용되어야 하는지 설명하는 메시지를 출력하게 한다.


### 표준 입력(Standard Input) 처리하기

프로그램이 다음으로 할 작업은 파일명이 주어지지 않았다면 표준 입력에서 데이터를 읽어오는 것이다. 
파일명을 파이프라인에 넣어 입력값으로 되돌려 사용하는 것이 예이다. 
또다른 스크립트 `count-stdin.R`로 저장하고 실험을 시작해보자:

~~~{.output}
lines <- readLines(con = file("stdin"))
count <- length(lines)
cat("lines in standard input: ")
cat(count, sep = "\n")
~~~

상기 작은 프로그램은 `file("stdin")`을 사용해서 표준 입력에서 라인(행)을 읽어온다. 
이것은 일반적인 정규 파일에서 할 수 있는, 거의 모든 것을 수행할 수 있게 한다. 
상기 예제에서 인자를 `readLines` 함수에 전달하는데, 각 라인을 벡터의 요소로 저장한다. 
마치 정규 명령어-라인 프로그램인 것처럼, 유닉스 쉘에서 실행을 시도해 보자:

~~~{.r}
Rscript count-stdin.R < data/small-01.csv
~~~

~~~{.output}
lines in standard input: 2
~~~

주목할 점은 `cat`을 호출할 때, `sep = "\n"`을 명기하지 않아서, 결과가 동일한 줄에 쓰여져 출력된다.

흔한 실수는 다음과 같이 표준입력에서 읽어서 무언가 실행하려고 하는 것이다:

~~~{.r}
Rscript count-stdin.R data/small-01.csv
~~~

즉, 표준입력에서 파일로 되돌리는 문자(`<`)를 생략한 것이다. 
이런 경우, 표준 입력에는 아무 것도 없어서 프로그램은 루프 시작에서 누군가 키보드로 무엇인가를 입력하길  기다리는 상황이 발생된다. 
무언가를 타이핑하지만, R은 멈추지 않는데 이유는 표준 입력이 언제 끝날지 모르기 때문이다. 
이와 같은 상황이 발생되면, `ctrl`+`z`를 눌러 R을 잠시 멈출 수 있다. 
하지만 기술적으로 백그라운드에서 잠시 멈춰있는 것이다. 만약 프로세스를 종료시키려면, 링크된 [지시][ps-kill])를 따르세요.

[ps-kill]: http://linux.about.com/library/cmd/blcmdl_kill.htm

프로그램을 다시 작성해서, 만약 어떤 파일명도 제공된게 없다면 `file("stdin")`에서 데이터를 가져와서 로딩한다. 
운좋게도, `read.csv`는 파일명 혹은 첫번째 매개변수로 열려져 있는 파일을 처리할 수 있다. 
그래서 실질적으로 `process` 함수를 변경할 필요는 없다. 
`main`을 갱신하여 `readings-06.R` 파일로 저장한다:

~~~{.output}
main <- function() {
  args <- commandArgs(trailingOnly = TRUE)
  action <- args[1]
  filenames <- args[-1]
  stopifnot(action %in% c("--min", "--mean", "--max"))
  
  if (length(filenames) == 0) {
    process(file("stdin"), action)
  } else {  
    for (f in filenames) {
      process(f, action)
    }
  }
}

process <- function(filename, action) {
  dat <- read.csv(file = filename, header = FALSE)
  
  if (action == "--min") {
    values <- apply(dat, 1, min)
  } else if (action == "--mean") {
    values <- apply(dat, 1, mean)
  } else if (action == "--max") {
    values <- apply(dat, 1, max)
  }
  cat(values, sep = "\n")
}

main()
~~~

작성한 프로그램을 시도해 보자. 
모든 환자의 평균 염증값을 계산하는 대신에, 첫 환자(행) 10명에 대한 평균값만을 계산한다:

~~~{.r}
head data/inflammation-01.csv | Rscript readings-06.R --mean
~~~

~~~{.output}
5.45
5.425
6.1
5.9
5.55
6.225
5.975
6.65
6.625
6.525
~~~

이제 완료했다: 프로그램이 처음 기획했던 모든 것을 수행한다.

> ## 도전 과제 - R로 `wc` 구현하기 {.challenge}
>
>  + `line-count.R` 프로그램을 작성해서 유닉스 `wc` 명령어처럼 동작하게 하세요.
>    *   만약 어떤 파일명도 주어지지 않는다면, 표준 입력에 행 숫자만을 출력한다.
>    *   만약 파일이름명이 하나 이상 주어지면, 파일 각각에 대한 행 숫자와 전체 행 숫자를 출력한다.


> ## 주요점 {.callout}
>
> *   `commandArgs(trailingOnly = TRUE)`을 사용해서 프로그램 실행에 필요한 명령어-라인 인자 벡터를 얻는다.
> *   침묵하는 실패(Silent failures)를 피한다.
> *   `file("stdin")`을 사용해서 프로그램의 표준입력에 연결한다.
> *   `cat(vec, sep = "\n")`을 사용하여 `vec` 요소를 한줄에 하나씩 표준출력으로 쓴다.
