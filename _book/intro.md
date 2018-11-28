

# 서문

데이터 과학은 원데이터로부터 이해와 통찰과 지식을 얻는 흥미로운 분야이다.
이 책의 목표는 R에 있는 주요 도구들을 배워 데이터 과학 작업을 할 수 있도록
도와주는 것이다. 이 책을 읽은 후에는 다양한 데이터 과학 문제를 해결할 수 있는 도구를 갖게 될 것이다.

## 무엇을 배우게 될까

데이터 과학은 광대한 분야이기 때문에, 단 한 권의 책을 읽어서 통달할 수는 없다.
이 책의 목표는 주요 도구를 사용할 수 있게 기초를 튼튼히 다지는 것이다.
일반적인 데이터 과학 프로젝트에 필요한 도구를 도식화해 보면 대략 다음과
같다.

<img src="diagrams/data-science.png" width="75%" style="display: block; margin: auto;" />

먼저 데이터를 R로 **불러와야(import)** 한다. 이는 일반적으로 파일, 데이터베이스
또는 웹 API에 저장된 데이터를 가져 와서 R의 데이터프레임으로 로드하는
것을 말한다. R로 가져올 수 없는 데이터가 있다면 그 데이터로는 데이터 과학
작업을 할 수 없다!

데이터를 가져온 후에는 **타이디하게(tidy)** 만드는 것이 좋다. 타이디하게 한다는 것은, 
데이터셋이 의미하는 바와 저장된 방식이 일치하도록 일관된
형식으로 저장한다는 의미이다. 간단히 말해, 데이터가 타이디하다면 각 열은
변수이고 각 행은 관측값이 된다. 타이디한 데이터는 중요하다. 왜냐하면 데이터 구조가 
이렇게 일관되면 여러 함수에 적합한 형태로 만드느라 애쓸 필요 없이, 
데이터에 대한 문제에 노력을 쏟을 수 있기 때문이다.

타이디한 데이터를 얻었다면 첫 번째 단계는 데이터를 **변환(transform)**하는 것이다.
관심 있는 관측값의 범위를 좁히고(예, 어떤 도시의 모든 사람, 또는 작년부터 모든 데이터), 
기존 변수의 함수꼴인 새로운 변수를 작성하고(예, 거리와 시간으로부터 속도를 계산), 
요약 통계량들(예, 빈도나 평균)을 계산하는 것 등이 데이터
변환에 속한다. 데이터 정리와 데이터 변형을 묶어서 데이터를 **길들인다(wrangling)**고
부르는데, 작업하기에 자연스러운 형태로 데이터를 만드는 것이 종종
싸움처럼 느껴지기 때문이다!

필요한 변수들이 있는 타이디 데이터가 되었다면 이제 시각화와 모델링이라
는 두 가지 주요 지식 생성 엔진이 있다. 이 둘에는 상호보완적인 강점과 약점이
있기 때문에, 실제 분석에서는 여러 번 반복된다.

**시각화**는 근본적으로 인간의 활동이다. 좋은 시각화는 예상치 못한 것을 보여주거나,
주어진 데이터에 대해 새로운 문제를 제기한다. 또한 좋은 시각화는
현재 탐구하고 있는 문제가 잘못되었다거나, 다른 데이터를 수집할 필요가 있다는 것을 알려주기도 한다. 
시각화는 여러분을 놀라게 할 수 있지만, 인간이 해석해야 하기 때문에 확장 적용(scale)이 아주 잘 되지는 않는다.

**모델**은 시각화를 보완하는 도구이다. 문제가 충분히 정교하다면 모델을 사용하여 그 문제에 답을 확인할 수 있다. 모델은 근본적으로 수학 또는 전산 도구이므로 일반적으로 잘 확장된다. 확장되지 않는 경우에도 두뇌를 더 확보하는 것보다 컴퓨터를 더 확보하는 것이 일반적으로 더 저렴하다! 한편 모든 모델은 가정을 하고 있으며 본성적으로 자신의 가정에 의문을 제기할 수 없다. 이는 모델이 근본적으로 여러분이 예상하지 못한 엉뚱한 결과를 낼 수 없다는 말이다.

데이터 과학의 마지막 단계는 데이터 분석 프로젝트에서 절대적으로 중요한
부분인 **의사소통**이다. 결과 내용을 다른 사람들에게 전달할 수 없다면 여러분이
모델과 시각화를 통해 데이터를 얼마나 잘 이해하게 되었는지는 중요하지 않다.

이 모든 도구를 둘러싸고 있는 것이 **프로그래밍**이다. 프로그래밍은 프로젝트의 모든 부분에서 사용되는 다용도 도구이다. 데이터 과학자가 되기 위해 전문
프로그래머가 될 필요는 없지만, 더 나은 프로그래머가 되면 반복 작업을 자동화하고 새로운 문제를 좀 더 쉽게 해결할 수 있다는 점에서 프로그래밍에 대해
배우면 언제나 도움이 된다.

You'll use these tools in every data science project, but for most projects they're not enough. There's a rough 80-20 rule at play; you can tackle about 80% of every project using the tools that you'll learn in this book, but you'll need other tools to tackle the remaining 20%. Throughout this book we'll point you to resources where you can learn more.

## How this book is organised

The previous description of the tools of data science is organised roughly according to the order in which you use them in an analysis (although of course you'll iterate through them multiple times). In our experience, however, this is not the best way to learn them:

* Starting with data ingest and tidying is sub-optimal because 80% of the time 
  it's routine and boring, and the other 20% of the time it's weird and
  frustrating. That's a bad place to start learning a new subject! Instead, 
  we'll start with visualisation and transformation of data that's already been
  imported and tidied. That way, when you ingest and tidy your own data, your
  motivation will stay high because you know the pain is worth it.
  
* Some topics are best explained with other tools. For example, we believe that
  it's easier to understand how models work if you already know about 
  visualisation, tidy data, and programming.
  
* Programming tools are not necessarily interesting in their own right, 
  but do allow you to tackle considerably more challenging problems. We'll
  give you a selection of programming tools in the middle of the book, and 
  then you'll see how they can combine with the data science tools to tackle 
  interesting modelling problems.

Within each chapter, we try and stick to a similar pattern: start with some motivating examples so you can see the bigger picture, and then dive into the details. Each section of the book is paired with exercises to help you practice what you've learned. While it's tempting to skip the exercises, there's no better way to learn than practicing on real problems.

## What you won't learn

There are some important topics that this book doesn't cover. We believe it's important to stay ruthlessly focused on the essentials so you can get up and running as quickly as possible. That means this book can't cover every important topic.

### Big data

This book proudly focuses on small, in-memory datasets. This is the right place to start because you can't tackle big data unless you have experience with small data. The tools you learn in this book will easily handle hundreds of megabytes of data, and with a little care you can typically use them to work with 1-2 Gb of data. If you're routinely working with larger data (10-100 Gb, say), you should learn more about [data.table](https://github.com/Rdatatable/data.table). This book doesn't teach data.table because it has a very concise interface which makes it harder to learn since it offers fewer linguistic cues. But if you're working with large data, the performance payoff is worth the extra effort required to learn it.

If your data is bigger than this, carefully consider if your big data problem might actually be a small data problem in disguise. While the complete data might be big, often the data needed to answer a specific question is small. You might be able to find a subset, subsample, or summary that fits in memory and still allows you to answer the question that you're interested in. The challenge here is finding the right small data, which often requires a lot of iteration.

Another possibility is that your big data problem is actually a large number of small data problems. Each individual problem might fit in memory, but you have millions of them. For example, you might want to fit a model to each person in your dataset. That would be trivial if you had just 10 or 100 people, but instead you have a million. Fortunately each problem is independent of the others (a setup that is sometimes called embarrassingly parallel), so you just need a system (like Hadoop or Spark) that allows you to send different datasets to different computers for processing. Once you've figured out how to answer the question for a single subset using the tools described in this book, you learn new tools like sparklyr, rhipe, and ddr to solve it for the full dataset.

### Python, Julia, and friends

In this book, you won't learn anything about Python, Julia, or any other programming language useful for data science. This isn't because we think these tools are bad. They're not! And in practice, most data science teams use a mix of languages, often at least R and Python.

However, we strongly believe that it's best to master one tool at a time. You will get better faster if you dive deep, rather than spreading yourself thinly over many topics. This doesn't mean you should only know one thing, just that you'll generally learn faster if you stick to one thing at a time. You should strive to learn new things throughout your career, but make sure your understanding is solid before you move on to the next interesting thing.

We think R is a great place to start your data science journey because it is an environment designed from the ground up to support data science. R is not just a programming language, but it is also an interactive environment for doing data science. To support interaction, R is a much more flexible language than many of its peers. This flexibility comes with its downsides, but the big upside is how easy it is to evolve tailored grammars for specific parts of the data science process. These mini languages help you think about problems as a data scientist, while supporting fluent interaction between your brain and the computer.

### Non-rectangular data

This book focuses exclusively on rectangular data: collections of values that are each associated with a variable and an observation. There are lots of datasets that do not naturally fit in this paradigm: including images, sounds, trees, and text. But rectangular data frames are extremely common in science and industry, and we believe that they are a great place to start your data science journey.

### Hypothesis confirmation

It's possible to divide data analysis into two camps: hypothesis generation and hypothesis confirmation (sometimes called confirmatory analysis). The focus of this book is unabashedly on hypothesis generation, or data exploration. Here you'll look deeply at the data and, in combination with your subject knowledge, generate many interesting hypotheses to help explain why the data behaves the way it does. You evaluate the hypotheses informally, using your scepticism to challenge the data in multiple ways.

The complement of hypothesis generation is hypothesis confirmation. Hypothesis confirmation is hard for two reasons:

1.  You need a precise mathematical model in order to generate falsifiable
    predictions. This often requires considerable statistical sophistication.

1.  You can only use an observation once to confirm a hypothesis. As soon as
    you use it more than once you're back to doing exploratory analysis. 
    This means to do hypothesis confirmation you need to "preregister" 
    (write out in advance) your analysis plan, and not deviate from it
    even when you have seen the data. We'll talk a little about some 
    strategies you can use to make this easier in.

It's common to think about modelling as a tool for hypothesis confirmation, and visualisation as a tool for hypothesis generation. But that's a false dichotomy: models are often used for exploration, and with a little care you can use visualisation for confirmation. The key difference is how often do you look at each observation: if you look only once, it's confirmation; if you look more than once, it's exploration.

## 준비하기
이 책에서는 여러분이 이미 몇 가지를 알고 있다고 가정을 했다.
독자는 일반적으로 수학을 알고 있을 것이다. 이미 프로그래밍 경험이 있다면
도움이 된다. 이전에 프로그래밍을 해본 적이 없다면 개럿의 [Hands on Programming with R](http://amzn.com/1449359019) 책이
유용한 보조책이 될 것이다.

이 책에 나오는 코드를 실행하는 데는 네 가지가 필요하다. R, RStudio, **tidyverse**라
불리는 R 패키지 모음, 그리고 다른 패키지들이다. 패키지는 재현 가능한 
R 코드의 기초 단위이다. 재사용 가능한 함수, 사용법을 설명하는 문서, 그리고 샘플 데이터가 패키지에 포함된다.

### R 
R을 다운로드하려면 CRAN (**c**omprehensive **R** **a**rchive **n**etwork) 로 가면 된다. CRAN은 전세계에 분산되어 있는 미러 서버의 집합으로 구성되어 있으며, R 및 R 패키지를 배포하는 데 사용된다. 가장 가까운 미러를 선택하지 말고 자동으로 미러를 찾아주는 클라우드 미러<https://cloud.r-project.org>를 사용하라. 

새로운 메이저 버전의 R은 일 년에 한 번 나오고, 매년 2-3 번의 마이너 릴리스가 있다. 정기적으로 업데이트하는 것이 좋다. 업그레이드는 약간 번거롭다. 메이저 버전의 경우 특히 더 그러한데 모든 패키지를 재설치해야 하기 때문이다. 하지만 그렇다고 미루면 업그레이드가 더 어려워질 뿐이다. 


### RStudio
RStudio는 R 프로그래밍을 위한, 통합개발환경(integrated development environment), 줄여서 IDE이다. <http://www.rstudio.com/download>에서 다운로드하여 설치하라. RStudio는 1 년에 두어 번 업데이트 된다. 새 버전이 있으면 RStudio가 알려준다. 최신의 강력한 기능을 활용할 수 있도록 정기적으로 업그레이드하는 것이 좋다. 이 책에서는 RStudio 1.0.0 이 필요하다. 

RStudio를 시작하면 인터페이스에 두 개의 주요 영역이 보일 것이다. 

<img src="diagrams/rstudio-console.png" width="75%" style="display: block; margin: auto;" />

지금은 콘솔 창에 R 코드를 입력하고 엔터 키를 눌러 실행시키는 것만 알면 된다. 앞으로 더 배우게 될 것이다!

### Tidyverse

R 패키지 몇 개도 설치해야 한다. R **패키지**란 베이스 R의 기능을 확장시키는 함수, 데이터, 문서의 집합체를 말한다. R을 성공적으로 사용하려면 패키지 사용이 핵심이다. 이 책에서 배우게 될 패키지의 대부분은 소위 tidyverse라고 하는 패키지집합에 들어있다. tidyverse의 패키지들은 데이터 및 R 프로그래밍에 관해 같은 철학에 기반하고 있으며, 자연스럽게 함께 작동되도록 설계되었다. 

다음 한 줄의 코드로 tidyverse를 설치할 수 있다. 


```r
install.packages("tidyverse")
```

자신의 컴퓨터에서 콘솔에 해당 코드 줄을 입력 한 다음 엔터 키를 눌러 실행하라. R이 CRAN에서 패키지들을 다운로드해서 여러분의 컴퓨터에 설치한다. 설치에 문제가 있으면 인터넷에 연결되어 있는지, 방화벽이나 프록시에 의해 <https://cloud.r-project.org/>가 차단되어 있지 않은지 확인하라.

`library()`를 사용하여 패키지를 로드하기 전에는, 해당 패키지의 함수, 객체 및 도움말 파일을 사용할 수 없다. 패키지를 설치하면 `library()` 함수를 사용하여 로드할 수 있다.


```r
library(tidyverse)
#> ── Attaching packages ────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
#> ✔ ggplot2 3.1.0     ✔ purrr   0.2.5
#> ✔ tibble  1.4.2     ✔ dplyr   0.7.8
#> ✔ tidyr   0.8.2     ✔ stringr 1.3.1
#> ✔ readr   1.2.1     ✔ forcats 0.3.0
#> ── Conflicts ───────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
#> ✖ dplyr::filter() masks stats::filter()
#> ✖ dplyr::lag()    masks stats::lag()
```

이것은 tidyverse가 ggplot2, tibble, tidyr, readr, purrr, dplyr 패키지를 로드하고 있음을 나타낸다. 이 패키지들은 거의 모든 분석에서 사용될 것이기 때문에 tidyverse의 **코어**라 할 수 있다. 

tidyverse의 패키지는 상당히 자주 변경된다. `tidyverse_update()`를 실행하여 업데이트가 가능한지 확인할 수 있다.

### 기타 패키지들

많은 훌륭한 패키지가 도메인이 다르거나 설계된 원칙이 달라서 tidyverse에 포함되지 않았다. 그 패키지들은 더 좋거나 나쁜 것이 아니라 단순히 다른 것뿐이다. 다른 말로 표현하자면 tidyverse가 아닌 패키지는 messyverse(지저분한 패키지)가 아니라 엄청나게 많은 서로 연관된 패키지들이다. R로 더 많은 데이터과학 프로젝트를 수행하면 새로운 패키지와 데이터에 대한 새로운 사고 방식을 배우게 될 것이다. 
이 책에서 우리는 tidyverse에 없는 다음의 데이터 패키지 세 개를 사용할 것이다. 


```r
install.packages(c("nycflights13", "gapminder", "Lahman"))
```

이 패키지들은 각각 항공기, 세계개발, 야구에 대한 데이터를 제공한다. 이를 이용하여 데이터과학 아이디어를 설명하겠다.

## R 코드 실행하기

이전 절에서 R 코드를 실행하는 몇 가지 예를 보았다. 책의 코드는 다음과 같다. 


```r
1 + 2
#> [1] 3
```

로컬 콘솔에서 동일한 코드를 실행하면 다음과 같이 표시된다. 

```
> 1 + 2
[1] 3
```

There are two main differences. In your console, you type after the `>`, called the __prompt__; we don't show the prompt in the book. In the book, output is commented out with `#>`; in your console it appears directly after your code. These two differences mean that if you're working with an electronic version of the book, you can easily copy code out of the book and into the console.

Throughout the book we use a consistent set of conventions to refer to code:

* Functions are in a code font and followed by parentheses, like `sum()`, 
  or `mean()`.

* Other R objects (like data or function arguments) are in a code font,
  without parentheses, like `flights` or `x`.
  
* If we want to make it clear what package an object comes from, we'll use
  the package name followed by two colons, like `dplyr::mutate()`, or   
  `nycflights13::flights`. This is also valid R code.

## 도움받기, 더 배우기

이 책은 외딴 섬이 아니다. 단 하나의 자료로 R을 완전히 습득할 수는 없다. 이 책에서 설명한 기술을 자신의 데이터에 적용하기 시작할 때, 답을 찾을 수 없는 문제가 곧 나타날 것이다. 이 절에서는 도움을 얻고 학습을 돕는 방법에 대한 몇 가지 팁을 설명한다. 

문제가 발생하면 구글 검색부터 시작하라. 일반적으로 검색어에 ‘R’을 추가하면 관련된 결과로 한정할 수 있다. 검색이 유용하지 않은 경우 R에 해당하는 결과가 없다는 의미일 경우가 많다. 구글은 오류 메시지에 특히 유용하다. 오류 메시지가 나타나서 그것이 무슨 뜻인지 잘 모를 경우 구글 검색해 보라! 과거에 이미 궁금해했을 누군가가 있어서, 웹 어딘가에 도움글이 있을 가능성이 있다. (오류 메시지가 영어가 아니라면 `>Sys.setenv(LANGUAGE = "en")`를 실행하고 코드를 다시 실행하라. 영어 오류 메시지에 대한 도움말을 찾을 가능성이 더 높다.) 

구글 검색이 도움이 되지 않으면 [stackoverflow](http://stackoverflow.com)에 가 보라. 기존 답변을 검색하는 데 시간을 조금 투자해 보라. `[R]`을 포함하면 R 사용과 관련된 질문과 답변으로 검색을 제한한다. 유용한 정보가 없으면 최소한의 재현 가능한 예 (reproducible example), 줄임말로 **reprex**를 준비하라. 좋은 reprex는 다른 사람들이 여러분을 도울 수 있게 해 주며, 이를 만드는 과정에서 문제의 답을 스스로 찾는 경우가 많다. 

예제를 재현 가능하게 만들기 위해 포함해야 할 세 가지는 필요한 패키지, 데이터, 코드이다. 

1. **패키지**는 스크립트 맨 위에 로드해야 어떤 패키지가 예제에 필요한지 쉽게 볼 수 있다. 각 패키지의 최신 버전을 사용하고 있는지 확인해 보자. 즉, 발견한 버그가 패키지 설치 이후 이미 해결되었을 가능성이 있다. tidyverse에 있는 패키지의 경우 `tidyverse_update()`를 실행하는 것이 가장 쉬운 방법이다.  

1.  질문에 **데이터**를 포함시키는 가장 쉬운 방법은 `dput()`을 사용하여 재현되는 R 코드를 생성하는 것이다. 예를 들어, R에서 `mtcars` 데이터셋을 다시 만들려면 다음 단계로 수행하라.  

    1. R 에서 `dput(mtcars)`를 실행한다.  
    1. 출력을 복사한다.
    1. 재현가능한 스크립트에서, `mtcars <-` 를 입력하고 붙여 넣는다.  

    동일한 문제가 나타나는 데이터의 가장 작은 서브셋을 찾아보라.    


1.  다른 사람들이 **코드**를 쉽게 읽을 수 있도록 시간을 조금 투자하라.  

    1. 공백을 사용했는지 변수 이름은 간결하지만 정보성이 있는지 확인하라.  

    1. 주석을 사용하여 문제점이 있는 곳을 표시하라. 

    1. 문제와 관련 없는 것은 모두 제거하라. 코드가 짧을수록 이해하기 쉽고 수정하기가 쉽다. 

새로운 R 세션을 시작하고 스크립트를 복사-붙여넣기하여 실제로 재현할 수 있는 예제를 만들었는지 확인하는 것으로 마무리하라. 

문제가 발생하기 전에 문제를 해결할 수 있게 스스로 준비시키는데 시간을 써야 한다. 매일 R을 배우는 데 약간의 시간을 투자하면 결국에는 멋지게 보상받을 것이다. 한 가지 방법은 RStudio의 해들리, 개럿 및 다른 모든 사람들이 [RStudio 블로그](https://blog.rstudio.org)에서 수행하는 작업을 팔로우하는 것이다. RStudio 블로그는 우리가 새로운 패키지, 새로운 IDE 기능 및 직접 진행하는 수업에 대한 공지 사항을 게시하는 곳이다. 트위터에서 해들리 [\@hadleywickham](https://twitter.com/hadleywickham) 또는 개럿 [\@statgarrett](https://twitter.com/statgarrett)을 팔로우하거나 [\@rstudiotips](https://twitter.com/rstudiotips)를 팔로우해서 IDE의 새로운 기능을 계속 전달받을 수 있다. 

R 커뮤니티를 보다 폭넓게 소통하기 위해서는 <http://www.r-bloggers.com>을 읽는 것이 좋은데 여기에는 전세계로부터 R에 대한 500개 이상의 블로그가 모여있다. 트위터 유저인 경우 `#rstats` 해시 태그를 팔로우하라. 트위터는 해들리가 커뮤니티의 새로운 발전을 따라가기 위해 사용하는 핵심 도구이다.

## Acknowledgements

This book isn't just the product of Hadley and Garrett, but is the result of many conversations (in person and online) that we've had with the many people in the R community. There are a few people we'd like to thank in particular, because they have spent many hours answering our dumb questions and helping us to better think about data science:

* Jenny Bryan and Lionel Henry for many helpful discussions around working
  with lists and list-columns.
  
* The three chapters on workflow were adapted (with permission), from
  <http://stat545.com/block002_hello-r-workspace-wd-project.html> by 
  Jenny Bryan.

* Genevera Allen for discussions about models, modelling, the statistical
  learning perspective, and the difference between hypothesis generation and 
  hypothesis confirmation.

* Yihui Xie for his work on the 
  package, and for tirelessly responding to my feature requests.

* Bill Behrman for his thoughtful reading of the entire book, and for trying 
  it out with his data science class at Stanford.

* The \#rstats twitter community who reviewed all of the draft chapters
  and provided tons of useful feedback.

* Tal Galili for augmenting his dendextend package to support a section on clustering that did not make it into the final draft.


## Colophon

An online version of this book is available at <http://r4ds.had.co.nz>. It will continue to evolve in between reprints of the physical book. The source of the book is available at <https://github.com/hadley/r4ds>. The book is powered by <https://bookdown.org> which makes it easy to turn R markdown files into HTML, PDF, and EPUB.

This book was built with:


```r
devtools::session_info(c("tidyverse"))
#> ─ Session info ──────────────────────────────────────────────────────────
#>  setting  value                       
#>  version  R version 3.5.1 (2018-07-02)
#>  os       macOS  10.14.1              
#>  system   x86_64, darwin15.6.0        
#>  ui       X11                         
#>  language (EN)                        
#>  collate  en_US.UTF-8                 
#>  ctype    en_US.UTF-8                 
#>  tz       Asia/Seoul                  
#>  date     2018-11-28                  
#> 
#> ─ Packages ──────────────────────────────────────────────────────────────
#>  package      * version  date       lib source        
#>  assertthat     0.2.0    2017-04-11 [1] CRAN (R 3.5.0)
#>  backports      1.1.2    2017-12-13 [1] CRAN (R 3.5.0)
#>  base64enc      0.1-3    2015-07-28 [1] CRAN (R 3.5.0)
#>  BH             1.66.0-1 2018-02-13 [1] CRAN (R 3.5.0)
#>  bindr          0.1.1    2018-03-13 [1] CRAN (R 3.5.0)
#>  bindrcpp       0.2.2    2018-03-29 [1] CRAN (R 3.5.0)
#>  broom          0.5.0    2018-07-17 [1] CRAN (R 3.5.0)
#>  callr          3.0.0    2018-08-24 [1] CRAN (R 3.5.0)
#>  cellranger     1.1.0    2016-07-27 [1] CRAN (R 3.5.0)
#>  cli            1.0.1    2018-09-25 [1] CRAN (R 3.5.0)
#>  clipr          0.4.1    2018-06-23 [1] CRAN (R 3.5.0)
#>  colorspace     1.3-2    2016-12-14 [1] CRAN (R 3.5.0)
#>  crayon         1.3.4    2017-09-16 [1] CRAN (R 3.5.0)
#>  curl           3.2      2018-03-28 [1] CRAN (R 3.5.0)
#>  DBI            1.0.0    2018-05-02 [1] CRAN (R 3.5.0)
#>  dbplyr         1.2.2    2018-07-25 [1] CRAN (R 3.5.0)
#>  digest         0.6.18   2018-10-10 [1] CRAN (R 3.5.0)
#>  dplyr        * 0.7.8    2018-11-10 [1] CRAN (R 3.5.0)
#>  evaluate       0.12     2018-10-09 [1] CRAN (R 3.5.0)
#>  fansi          0.4.0    2018-10-05 [1] CRAN (R 3.5.0)
#>  forcats      * 0.3.0    2018-02-19 [1] CRAN (R 3.5.0)
#>  fs             1.2.6    2018-08-23 [1] CRAN (R 3.5.0)
#>  ggplot2      * 3.1.0    2018-10-25 [1] CRAN (R 3.5.0)
#>  glue           1.3.0    2018-07-17 [1] CRAN (R 3.5.0)
#>  gtable         0.2.0    2016-02-26 [1] CRAN (R 3.5.0)
#>  haven          2.0.0    2018-11-22 [1] CRAN (R 3.5.0)
#>  highr          0.7      2018-06-09 [1] CRAN (R 3.5.0)
#>  hms            0.4.2    2018-03-10 [1] CRAN (R 3.5.0)
#>  htmltools      0.3.6    2017-04-28 [1] CRAN (R 3.5.0)
#>  httr           1.3.1    2017-08-20 [1] CRAN (R 3.5.0)
#>  jsonlite       1.5      2017-06-01 [1] CRAN (R 3.5.0)
#>  knitr          1.20     2018-02-20 [1] CRAN (R 3.5.0)
#>  labeling       0.3      2014-08-23 [1] CRAN (R 3.5.0)
#>  lattice        0.20-35  2017-03-25 [1] CRAN (R 3.5.1)
#>  lazyeval       0.2.1    2017-10-29 [1] CRAN (R 3.5.0)
#>  lubridate      1.7.4    2018-04-11 [1] CRAN (R 3.5.0)
#>  magrittr       1.5      2014-11-22 [1] CRAN (R 3.5.0)
#>  markdown       0.8      2017-04-20 [1] CRAN (R 3.5.0)
#>  MASS           7.3-50   2018-04-30 [1] CRAN (R 3.5.1)
#>  Matrix         1.2-14   2018-04-13 [1] CRAN (R 3.5.1)
#>  mgcv           1.8-24   2018-06-23 [1] CRAN (R 3.5.1)
#>  mime           0.6      2018-10-05 [1] CRAN (R 3.5.0)
#>  modelr         0.1.2    2018-05-11 [1] CRAN (R 3.5.0)
#>  munsell        0.5.0    2018-06-12 [1] CRAN (R 3.5.0)
#>  nlme           3.1-137  2018-04-07 [1] CRAN (R 3.5.1)
#>  openssl        1.1      2018-11-15 [1] CRAN (R 3.5.0)
#>  pillar         1.3.0    2018-07-14 [1] CRAN (R 3.5.0)
#>  pkgconfig      2.0.2    2018-08-16 [1] CRAN (R 3.5.0)
#>  plogr          0.2.0    2018-03-25 [1] CRAN (R 3.5.0)
#>  plyr           1.8.4    2016-06-08 [1] CRAN (R 3.5.0)
#>  processx       3.2.0    2018-08-16 [1] CRAN (R 3.5.0)
#>  ps             1.2.1    2018-11-06 [1] CRAN (R 3.5.0)
#>  purrr        * 0.2.5    2018-05-29 [1] CRAN (R 3.5.0)
#>  R6             2.3.0    2018-10-04 [1] CRAN (R 3.5.0)
#>  RColorBrewer   1.1-2    2014-12-07 [1] CRAN (R 3.5.0)
#>  Rcpp           1.0.0    2018-11-07 [1] CRAN (R 3.5.0)
#>  readr        * 1.2.1    2018-11-22 [1] CRAN (R 3.5.0)
#>  readxl         1.1.0    2018-04-20 [1] CRAN (R 3.5.0)
#>  rematch        1.0.1    2016-04-21 [1] CRAN (R 3.5.0)
#>  reprex         0.2.1    2018-09-16 [1] CRAN (R 3.5.0)
#>  reshape2       1.4.3    2017-12-11 [1] CRAN (R 3.5.0)
#>  rlang          0.3.0.1  2018-10-25 [1] CRAN (R 3.5.0)
#>  rmarkdown      1.10     2018-06-11 [1] CRAN (R 3.5.0)
#>  rprojroot      1.3-2    2018-01-03 [1] CRAN (R 3.5.0)
#>  rstudioapi     0.8      2018-10-02 [1] CRAN (R 3.5.0)
#>  rvest          0.3.2    2016-06-17 [1] CRAN (R 3.5.0)
#>  scales         1.0.0    2018-08-09 [1] CRAN (R 3.5.0)
#>  selectr        0.4-1    2018-04-06 [1] CRAN (R 3.5.0)
#>  stringi        1.2.4    2018-07-20 [1] CRAN (R 3.5.0)
#>  stringr      * 1.3.1    2018-05-10 [1] CRAN (R 3.5.0)
#>  tibble       * 1.4.2    2018-01-22 [1] CRAN (R 3.5.0)
#>  tidyr        * 0.8.2    2018-10-28 [1] CRAN (R 3.5.0)
#>  tidyselect     0.2.5    2018-10-11 [1] CRAN (R 3.5.0)
#>  tidyverse    * 1.2.1    2017-11-14 [1] CRAN (R 3.5.0)
#>  tinytex        0.9      2018-10-23 [1] CRAN (R 3.5.0)
#>  utf8           1.1.4    2018-05-24 [1] CRAN (R 3.5.0)
#>  viridisLite    0.3.0    2018-02-01 [1] CRAN (R 3.5.0)
#>  whisker        0.3-2    2013-04-28 [1] CRAN (R 3.5.0)
#>  withr          2.1.2    2018-03-15 [1] CRAN (R 3.5.0)
#>  xfun           0.4      2018-10-23 [1] CRAN (R 3.5.0)
#>  xml2           1.2.0    2018-01-24 [1] CRAN (R 3.5.0)
#>  yaml           2.2.0    2018-07-25 [1] CRAN (R 3.5.0)
#> 
#> [1] /Library/Frameworks/R.framework/Versions/3.5/Resources/library
```
