---
layout: page
title: R 프로그래밍
subtitle: 요인 이해하기
minutes: 20
---

> ## 학습 목표 {.objectives}
>
> *  R에서 범주형 데이터를 표현하는 방법을 이해한다.
> *  순위가 있는 요인과 순위가 없는 요인 사이 차이점을 알게 된다.
> *  요인을 사용할 때 마주치는 문제점을 인지한다.

이번 학습은 [데이터 카펜트리 교재](http://datacarpentry.org)를 모형으로 따랐다.

요인(factor)은 범주형 데이터를 표현하는데 사용된다.
요인은 순위를 가질 수도, 순위를 갖지 않을수도 있다.
요인은 통계적 분석과 도식화에 대한 중요한 클래스다.

요인은 정수로 저장되고, 유일무이한 정수와 연관된 표식을 갖는다.
요인은 문자벡터처럼 보이지만(흔히 행동한다), 실제로 내부를 보면 정수다.
문자열처럼 요인을 다룰 때, 주의를 기울일 필요가 있다.

요인이 생성되면, 요인은 *수준(level)*으로 알려진 사전에 정의된 집합값만 담을 수 있다.
기본 디폴트 설정으로, R은 항상 *수준*을 알파벳 순으로 정렬한다.
예를 들어, 만약 수준 2을 갖는 요인이 있다면:

> ## 조언 {.callout}
>
> `factor()` 명령어를 사용해서 R에 요인을 생성하고 변경한다.

~~~{.r}
sex <- factor(c("male", "female", "female", "male"))
~~~

R은 `1`을 `"female"` 수준에, `2`를 `"male"` 수준에 할당한다.
왜냐하면 `f`가 `m`보다 앞서기 때문이다.
`levels()` 함수를 사용해서 이점을 확인할 수 있다.
`nlevels()` 함수를 사용해서 수준의 갯수도 확인할 수 있다:

~~~{.r}
levels(sex)
~~~

~~~{.output}
[1] "female" "male"  
~~~

~~~{.r}
nlevels(sex)
~~~

~~~{.output}
[1] 2
~~~

때때로, 요인 순위가 문제가 되지 않지만,
다른 때는 순위를 명세하는 것이 필요한데, 
이유는 순위가 유의미(예를 들어, "low", "medium", "high")하기 때문이다.
혹은 특정 유형의 자료분석에서 필요하기 때문이다.
부가적으로, 수준에 대한 순위를 명세하면 수준을 비교할 수 있게 된다:

~~~{.r}
food <- factor(c("low", "high", "medium", "high", "low", "medium", "high"))
levels(food)
~~~

~~~{.output}
[1] "high"   "low"    "medium"
~~~

~~~{.r}
food <- factor(food, levels=c("low", "medium", "high"))
levels(food)
~~~

~~~{.output}
[1] "low"    "medium" "high"  
~~~

~~~{.r}
min(food) ## 돌아가지 않는다.
~~~



~~~{.error}
Error in Summary.factor(structure(c(1L, 3L, 2L, 3L, 1L, 2L, 3L), .Label = c("low", : 'min' not meaningful for factors
~~~


~~~{.r}
food <- factor(food, levels=c("low", "medium", "high"), ordered=TRUE)
levels(food)
~~~

~~~{.output}
[1] "low"    "medium" "high"  
~~~

~~~{.r}
min(food) ## 정상 동작한다!
~~~

~~~{.output}
[1] low
Levels: low < medium < high
~~~

R 메모리에서, 상기 요인은 숫자 (1, 2, 3)으로 표현된다.
간단한 정수 표식을 사용하는 것보다 요인을 사용하는 것이 더 낫다.
왜냐하면 요인은 자기 기술을 하기 때문이다: 
`"low"`, `"medium"`, `"high"`"와 같은 표기법이 
`1`, `2`, `3` 보다 더 기술을 잘하고 있다.
"low"는 어떤 것인가?
정수형 데이터로는 분간할 수 없다.
요인은 이러한 정보가 붙박이로 내장되어 있다.
(예제 데이터셋에 나온 피험자처럼) 특히 수준이 많은 경우 도움이 된다.

> ## 도전 과제 - R로 데이터 표현하기 {.challenge}
>
> 피험자 5명이 받은 운동 수준을 나타내는 벡터가 있다; 
> **"l","n","n","i","l"** ; n=none, l=light, i=intense
>
> R로 테이터를 나타내는 가장 좋은 방식은 어떤 것일까?
>
> a) exercise<-c("l","n","n","i","l")
>
> b) exercise<-factor(c("l","n","n","i","l"), ordered=TRUE)
>
> c) exercise<-factor(c("l","n","n","i","l"), levels=c("n","l","i"), ordered=FALSE)
>
> d) exercise<-factor(c("l","n","n","i","l"), levels=c("n","l","i"), ordered=TRUE)

###  요인을 변환하기

요인을 숫자로 변환하면 문제가 발생될 수 있다:

~~~{.r}
f<-factor(c(3.4, 1.2, 5))
as.numeric(f)
~~~

~~~{.output}
[1] 2 1 3
~~~

예상한대로 행동하지 않는다(그리고 경고도 없다).

추천하는 방식은 요인 수준을 인덱스하는데 정수형 벡터를 사용하는 것이다:

~~~{.r}
levels(f)[f]
~~~

~~~{.output}
[1] "3.4" "1.2" "5"  
~~~

상기 실행결과는 문자벡터를 반환한다.
`as.numeric()` 함수가 여전히 필요하다.
값을 적절한 자료형(숫자형, numeric)으로 전환하는 역할을 수행한다.

~~~{.r}
f<-levels(f)[f]
f<-as.numeric(f)
~~~

### 요인 사용하기

예제 데이터를 적재해서 요인에 대한 사용법을 살펴보자:

~~~{.r}
dat<-read.csv(file='data/sample.csv', stringsAsFactors=TRUE)
~~~

> ## 조언 {.callout}
>
> `stringsAsFactors=TRUE` 인자는 R에서 기본 디폴트로 설정된 사항이다.
> 이 인자를 내버려둘 수도 있다.
> 여기서는 명확성을 위해 포함했다.

~~~{.r}
str(dat)
~~~

~~~{.output}
'data.frame':	100 obs. of  9 variables:
 $ ID           : Factor w/ 100 levels "Sub001","Sub002",..: 1 2 3 4 5 6 7 8 9 10 ...
 $ Gender       : Factor w/ 4 levels "f","F","m","M": 3 3 3 1 3 4 1 3 3 1 ...
 $ Group        : Factor w/ 3 levels "Control","Treatment1",..: 1 3 3 2 2 3 1 3 3 1 ...
 $ BloodPressure: int  132 139 130 105 125 112 173 108 131 129 ...
 $ Age          : num  16 17.2 19.5 15.7 19.9 14.3 17.7 19.8 19.4 18.8 ...
 $ Aneurisms_q1 : int  114 148 196 199 188 260 135 216 117 188 ...
 $ Aneurisms_q2 : int  140 209 251 140 120 266 98 238 215 144 ...
 $ Aneurisms_q3 : int  202 248 122 233 222 320 154 279 181 192 ...
 $ Aneurisms_q4 : int  237 248 177 220 228 294 245 251 272 185 ...
~~~

첫 3 칼럼이 요인으로 전환되었다.
이들 값이 데이터 파일에서 텍스트로,
R은 자동적으로 이를 범주형 변수로 해석한다.

~~~{.r}
summary(dat)
~~~

~~~{.output}
       ID     Gender        Group    BloodPressure        Age       
 Sub001 : 1   f:35   Control   :30   Min.   : 62.0   Min.   :12.10  
 Sub002 : 1   F: 4   Treatment1:35   1st Qu.:107.5   1st Qu.:14.78  
 Sub003 : 1   m:46   Treatment2:35   Median :117.5   Median :16.65  
 Sub004 : 1   M:15                   Mean   :118.6   Mean   :16.42  
 Sub005 : 1                          3rd Qu.:133.0   3rd Qu.:18.30  
 Sub006 : 1                          Max.   :173.0   Max.   :20.00  
 (Other):94                                                         
  Aneurisms_q1    Aneurisms_q2    Aneurisms_q3    Aneurisms_q4  
 Min.   : 65.0   Min.   : 80.0   Min.   :105.0   Min.   :116.0  
 1st Qu.:118.0   1st Qu.:131.5   1st Qu.:182.5   1st Qu.:186.8  
 Median :158.0   Median :162.5   Median :217.0   Median :219.0  
 Mean   :158.8   Mean   :168.0   Mean   :219.8   Mean   :217.9  
 3rd Qu.:188.0   3rd Qu.:196.8   3rd Qu.:248.2   3rd Qu.:244.2  
 Max.   :260.0   Max.   :283.0   Max.   :323.0   Max.   :315.0  
~~~

`summary()` 함수가 요인을 숫자에 대해서(문자열에 대해서) 다르게 처리하고 있음에 주목한다.
각 값에 대한 출현 횟수가 더 유용한 정보다.

> ## 조언 {.callout}
> 
> `summary()` 함수가 데이터에 존재하는 오류를 찾아내는 매우 훌륭한 방법이 된다.
> *dat$Gender* 칼럼을 보라.
> 결측 데이터를 찾아내는 매우 훌륭한 방법이기도 하다.

> ## 도전 과제 - 요인 순위 바꾸기 {.challenge}
> 
> `table()` 함수는 관측점을 표로 만들어서, 이를 사용해서 재빨리 막대그래프를 생성한다. 예를 들어:
> 
> ~~~{.r}
> table(dat$Group)
> ~~~
> 
> ~~~{.output}
> 
>    Control Treatment1 Treatment2 
>         30         35         35 
> ~~~
> 
> ~~~{.r}
> barplot(table(dat$Group))
> ~~~
> 
> <img src="fig/01-supp-factors-reordering-factors-1.png" title="plot of chunk reordering-factors" alt="plot of chunk reordering-factors" style="display: block; margin: auto;" />
> `factor()` 명령어를 사용해서 `dat$Group` 칼럼을 변경해서 *Control* 집단이 마지막에 도식화되도록 한다.

### 요인에서 수준 제거하기

상기 데이터셋에서 성(Gender) 값이 일부 잘못 코드화되었다.
해당 요인을 제거하자.

~~~{.r}
barplot(table(dat$Gender))
~~~

<img src="fig/01-supp-factors-gender-counts-1.png" title="plot of chunk gender-counts" alt="plot of chunk gender-counts" style="display: block; margin: auto;" />

남녀 성별에 대한 값은 소분자 'm' 와 'f' 으로 기록되어야 된다.
다음과 같이 이 문제를 고쳐야 된다.

~~~{.r}
dat$Gender[dat$Gender=='M']<-'m'
~~~

> ## 도전 과제 - 요인 갱신하기 {.challenge}
>
> ~~~{.r}
> plot(x=dat$Gender,y=dat$BloodPressure)
> ~~~
> 
> <img src="fig/01-supp-factors-updating-factors-1.png" title="plot of chunk updating-factors" alt="plot of chunk updating-factors" style="display: block; margin: auto;" />
>
> 상기 그림은 왜 수준이 4개를 보여줄까요?
>
> *힌트* dat$Gender 변수는 얼마나 많은 수준을 가졌을까요?

"M" 수준이 해당 dat$Gender 칼럼에서 더이상 유요한 값이 아니라고 R에게 전달할 필요가 있다.
`droplevels()` 함수를 사용해서 여분이 된 수준을 제거한다.

~~~{.r}
dat$Gender<-droplevels(dat$Gender)
plot(x=dat$Gender,y=dat$BloodPressure)
~~~

<img src="fig/01-supp-factors-dropping-levels-1.png" title="plot of chunk dropping-levels" alt="plot of chunk dropping-levels" style="display: block; margin: auto;" />

> ## 조언 {.callout}
>
> 이번 경우에, 요인 `levels()` 수준을 조정하는 것이 값을 재할당하는데 유용한 지름길이 된다.
> 
> ~~~{.r}
> levels(dat$Gender)[2] <- 'f'
> plot(x = dat$Gender, y = dat$BloodPressure)
> ~~~
> 
> <img src="fig/01-supp-factors-adjusting-levels-1.png" title="plot of chunk adjusting-levels" alt="plot of chunk adjusting-levels" style="display: block; margin: auto;" />

> ## 주요점 {.callout}
>
> * 요인을 사용해서 범주형 데이터를 표현한다.
> * 요인은 *순위가 있을(ordered)* 수도, *순위가 없을(unordered)* 수도 있다.
> * 일부 R 함수는 요인을 처리하는 특수한 메쏘드가 있다.
