---
layout: page
title: R 프로그래밍
subtitle: R 팩키지 작성
minutes: 30
---

> ## 학습 목표 {.objectives}
>
> R 팩키지 작성하는 방법 (그리고 이유)에 대한 짧은 요약

왜 본인만의 R 팩키지를 작성해야 할까?

**재현가능한 연구!**

R 팩키지는 **재사용가능한 코드 기본 단위**다.
향후 코드를 재사용하고자 하거나 다른 사람이 여러분이 작성한 코드를 사용할 수 있도록 하려면,
팩키지에 코드를 담야야만 된다.

R 팩키지는 구성요소가 두개 필요하다: 
- 팩키지에 대한 메타데이터를 갖는 DESCRIPTION 파일
- 코드를 갖는 R 디렉토리

*다른 선택옵션 구성요소도 있다. 자세한 정보는 [여기](http://adv-r.had.co.nz/Package-basics.html)를 참조한다.*

DESCRIPTION 파일
----------------

    Package: Package name
    Title: Brief package description
    Description: Longer package description
    Version: Version number(major.minor.patch)
    Author: Name and email of package creator
    Maintainer: Name and email of package maintainer (who to contact with issues)
    License: Abbreviation for an open source license

팩키지 명칭은 문자로 시작해야 되고, 문자와 숫자만 포함될 수 있다.

.R 파일
--------

함수가 파일 하나에 모두 있거나, 각 파일별로 쪼개질 필요도 없다.
함수를 어떻게 구조화할지는 본인에게 달려있다.
추천: 논리적 방식으로 구조화해서, 어떤 파일이 어떤 함수를 보관할지 알기 쉽게 한다.


첫번째 R 팩키지 작성
---------------------------

온도 변환 함수를 R 팩키지로 변환하자.

~~~{.r}
fahr_to_kelvin <- function(temp) {
    #Converts Fahrenheit to Kelvin
    kelvin <- ((temp - 32) * (5/9)) + 273.15
    kelvin
}
~~~


~~~{.r}
kelvin_to_celsius <- function(temp) {
  #Converts Kelvin to Celsius
  Celsius <- temp - 273.15
  Celsius
}
~~~


~~~{.r}
fahr_to_celsius <- function(temp) {
  #Converts Fahrenheit to Celsius using fahr_to_kelvin() and kelvin_to_celsius()
  temp_k <- fahr_to_kelvin(temp)
	result <- kelvin_to_celsius(temp_k)
  result
}
~~~

`devtools` 와 `roxygen2`을 사용하는데, 이유는 상대적으로 간단하게 R 팩키지를 생성하게 한다.
먼저 `devtools` 팩키지를 설치한다. 
그러면, GitHub ([코드][])에서 `roxygen2` 팩키지를 설치할 수 있게 된다.

[코드]: https://github.com/klutometis/roxygen


~~~{.r}
install.packages("devtools")
library("devtools")
install_github("klutometis/roxygen")
library("roxygen2")
~~~

작업 디렉토리를 설정하고 나서, `create` 함수를 사용해서 팩키지 작성을 시작한다.
명칭은 단순하고 유일무이하게 한다.

- package_to_convert_temperatures_between_kelvin_fahrenheit_and_celsius (나쁨)
- tempConvert (좋음)


~~~{.r}
setwd(parentDirectory)
create("tempConvert")
~~~

R 디렉토리에 작성한 함수를 추가한다.
각 함수를 개별 R 스크립트에 위치시키고, 다음과 같이 문서를 추가한다:

~~~{.r}
#' Convert Fahrenheit to Kelvin
#'
#' This function converts input temperatures in Fahrenheit to Kelvin.
#' @param temp The input temperature.
#' @export
#' @examples
#' fahr_to_kelvin(32)

fahr_to_kelvin <- function(temp) {
  #Converts Fahrenheit to Kelvin
  kelvin <- ((temp - 32) * (5/9)) + 273.15
  kelvin
}
~~~

`roxygen2` 팩키지는 주석으로 `#'` 시작되는 행을 읽어 들이고, 
작성하는 팩키지에 대한 문서를 생성한다.
기술되는 태그는 `@`로 시작된다.
예를 들어, `@param`은 함수에 입력 매개변수에 관한 정보를 갖고 있다.
이제 `roxygen2`를 사용해서 작성한 문서를 표준 R 형식으로 변환한다.


~~~{.r}
setwd("./tempConvert")
document()
~~~

이제 팩키지 디렉토리를 살펴보자.
`/man` 디렉토리에 적절하게 서식화된 문서를 갖는 각 `.R` 파일에 대해 `.Rd` 파일이 있다.

이제, 팩키지를 적재하고 문서를 살펴보자.

~~~{.r}
setwd("..")
install("tempConvert")

?fahr_to_kelvin
~~~

이제 전역 환경에 대해 부모 환경인 tempConvert 환경이 있음에 주목한다.

~~~{.r}
search()
~~~

이제 작성한 팩키지가 적재돼었기 때문에, 함수 일부를 시도해 보자.

~~~{.r}
fahr_to_celsius(32)
~~~


~~~{.output}
[1] 0

~~~



~~~{.r}
fahr_to_kelvin(212)
~~~



~~~{.output}
[1] 373.15

~~~



~~~{.r}
kelvin_to_celsius(273.15)
~~~



~~~{.output}
[1] 0

~~~

> ## 도전과제 - 분포에 대한 팩키지 생성 {.challenge}
>
> - tempConvert 팩키지가 Kelvin 을 Fahrenheit으로 변환하거나 혹은 Celsius에서 Kelvin 혹은 Fahrenheit로 변환하도록 신규 함수를 작성하라.
> - `analyze` 함수에 대한 팩키지를 작성해서, 더 많은 데이터가 들어오면 적재하기 쉽게 만든다.
