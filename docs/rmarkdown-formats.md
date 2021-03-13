# R 마크다운 유형 {#rmarkdown-formats}

## 들어가기

지금까지 본 R마크다운은 HTML 문서를 생성하는 것이었다. 이 장에서는 R마크다운으로 생성할 수 있는 그 외의 출력 유형들에 대해 간략하게 설명한다. 출력 문서를 설정하는 방법에는 두 가지가 있다.  

1. 영구적 방법은 YAML 헤더를 수정하는 것이다. 

    ```yaml
    title: "Viridis Demo"
    output: html_document
    ```
    
1.  일시적 방법은 직접 `rmarkdown::render()` 를 호출하는 것이다.
    
    
    ```r
    rmarkdown::render("diamond-sizes.Rmd", output_format = "word_document")
    ```
    
    여러 출력 유형을 생성하는 것을 프로그래밍하고자 할 때 유용한 방법이다.

RStudio의 knit 버튼을 클릭하면 `output`  필드의 첫 번째 형식으로 파일을 렌더링한다. knit 버튼 옆의 드롭다운 메뉴를 클릭하여 다른 형식으로 렌더링할 수 있다.

<img src="screenshots/rmarkdown-knit.png" width="206" style="display: block; margin: auto;" />

## 출력 옵션

각 출력 형식은 R 함수와 연관되어 있다. `foo`  혹은 `pkg::foo` 로 작성할 수 있다. `pkg` 를 생략하면 기본값으로 rmarkdown로 간주한다. 출력을 만드는 함수 이름을 아는 것은 중요하다. 도움말을 얻을 수 있는 곳이기 때문이다. 예를 들어 `html_document` 과 함께 설정할 수 있는 파라미터가 무엇인지를 알아보기 위해서는 `?rmarkdown::html_document` 를 하면 된다.

기본 파라미터 값을 무시하려면 확장된 출력 필드를 이용해야 한다. 예를 들어 떠다니는 목차가 있는 `html_document` 로 렌더링하고자 하면 다음 같이 하면 된다.

```yaml
output:
  html_document:
    toc: true
    toc_float: true
```

포맷 리스트를 제공하여 다중 출력으로 렌더링할 수도 있다.

```yaml
output:
  html_document:
    toc: true
    toc_float: true
  pdf_document: default
```

기본 옵션을 무시하지 않으려면 특수 구문을 주의하라.

## Document

이전 장에서는 기본 `html_document`  출력에 중점을 두었었다. 이 테마에는 여러 가지 변종이 있는데, 이들은 다양한 유형의 문서를 생성한다.

* `pdf_document` 는 LaTeX (오픈 소스 문서 레이아웃 시스템)이 있는 PDF를 생성한다. LaTeX 설치가 필요한데, 하지 않았다면 RStudio가 안내할 것이다.

* `word_document` 는 마이크로소프트 워드 문서(.docx)를 생성한다.

* `odt_document` 는 오픈 문서 텍스트 문서(.odt)를 생성한다.

* `rtf_document` 는 서식 있는 포맷(.rtf) 문서에 해당된다.

* `md_document` 은 마크다운 문서에 해당된다. 일반적으로 유용하지 않은 유형이지만, 예를 들어 회사의 CMS 나 랩 위키가 마크다운을 사용한다면 이용해 볼 수 있다.

* `github_document` : 깃허브에서 공유할 수 있도록 설계된 `md_document` 의 맞춤형 버전이다.

의사결정권자에게 공유할 문서를 생성할 경우, 설정 청크에서 전역 옵션을 설정하여 코드를 표시하지 않을 수 있음을 기억하라.


```r
knitr::opts_chunk$set(echo = FALSE)
```

`html_document` 의 기타 옵션으로는 코드 청크를 기본적으로 표시하지 않다가, 클릭해야 표시하는 것이 있다.

```yaml
output:
  html_document:
    code_folding: hide
```

## Notebook

`html_notebook` (노트북)은 `html_document` 의 변형이다. 렌더링된 출력은 서로 매우 유사하지만 목적이 다르다. `html_document` 는 의사결정권자와 의사소통하는 데 맞춰져 있지만, 노트북은 다른 데이터 과학자들과의 공동작업에 초점을 맞춘다. 목적이 이렇게 다르기 때문에 HTML 출력이 다른 방식으로 사용된다. 두 HTML 출력물 모두 완전히 렌더링된 출력을 포함하지만 노트북은 소스 코드 전체도 포함한다. 이로 인해 노트북이 생성한 `.nb.html` 을 다음의 두 가지 방식으로 사용할 수 있다.

1.  웹 브라우저에서 이 파일을 보고 렌더링된 출력을 볼 수 있다. 렌더링을 생성한 소스 코드의 임베디드 복사본이 포함되는 것이 `html_document` 과 다르다. 

1.  RStudio 에서 이 파일을 편집할 수 있다. `.nb.html`  파일을 열 때, RStudio 는 이를 생성한 _.Rmd_ 파일을 자동으로 다시 생성한다. 앞으로는 지원하는 파일들(예: _.csv_ 데이터 파일)도 포함할 수 있을 것인데, 이렇게 되면 필요할 때마다 이 파일들을 자동으로 불러올 수 있다.  

`.nb.html`  파일들을 이메일로 보내는 것은 동료들과 분석 내용을 공유할 수 있는 간단한 방법이다. 그러나 동료들이 수정을 원하면 일이 매우 힘들어 진다. 이런 일이 발생하기 시작한다면 이 때가 깃과 깃허브를 배우기 좋은 순간이다. 깃과 깃허브를 배우는 것은 처음에는 정말 힘들지만 공동작업에서 얻는 활용도는 엄청나다. 앞서 이야기했듯이 깃과 깃허브는 이 책의 범위를 벗어나지만, 이를 사용하고 있다면 유용한 팁이 하나 있다. 다음과 같이 `html_notebook` 과 `github_document`  출력을 모두 사용하라.

```yaml
output:
  html_notebook: default
  github_document: default
```


`html_notebook` 은 로컬 미리보기가 가능하고 이메일을 통해 공유할 수 있는 파일을 제공한다. `github_document` 는 깃에서 확인할 수 있는 최소한의 md 파일을 생성한다. (코드뿐만 아니라) 분석 결과가 시간에 따라 어떻게 변하는지를 쉽게 볼 수 있다. 깃허브는 이를 온라인으로 멋지게 렌더링한다.

## Presentation

R마크다운으로 프리젠테이션을 생성할 수도 있다. 키노트, 파워포인트와 같은 도구를 사용하는 것보다는 시각적인 제어에 제약이 있지만, R코드 결과를 자동으로 프리젠테이션에 삽입하여 시간을 훨씬 절약할 수 있다. 내용이 슬라이드로 나누어지는데, 첫 번째(`#`) 혹은 두 번째(`##`)레벨 헤더에서 새 슬라이드가 시작된다. 수평선(`***`)을 삽입하여 헤더없는 새로운 슬라이드를 생성할 수도 있다. 

R마크다운에는 세 가지 프리젠테이션 형식이 내장되어 있다.
1.  `ioslides_presentation` 
    ioslices 를 이용한 HTML 프리젠테이션

1.  `slidy_presentation` 
    W2C Slidy 를 이용한 HTML 프리젠테이션

1.  `beamer_presentation` 
    LaTeX Beamer를 이용한 PDF 프리젠테이션.

이 밖에 인기 있는 다음의 두 가지 형식은 패키지에 의해 제공된다.

1.  `revealjs::revealjs_presentation` 
    reveal.js를 이용한 HTML 프리젠테이션. revealjs패키지 필요.

1.  `rmdshower`  (https://github.com/MangoTheCat/rmdshower)는 shower(https://github.com/shower/shower) 프리젠테이션 엔진의 래퍼를 제공한다.

## Dashboard

대시보드는 많은 양의 정보를 시각적으로 신속하게 전달할 수 있는 유용한 도구이다. flexdashboard를 사용하면 R마크다운과, 헤더의 레이아웃 표시 규칙을 이용하여 대시보드를 쉽게 생성할 수 있다.

* 레벨 1 헤더(`#`)에서 대시보드의 새로운 페이지가 시작된다.
* 레벨 2 헤더(`##`)에서 새로운 열이 시작된다.
* 레벨 3 헤더(`###`)에서 새로운 행이 시작된다.

예를 들어 아래와 같은 대시보드는, 

<img src="screenshots/rmarkdown-flexdashboard.png" width="75%" style="display: block; margin: auto;" />

다음의 코드로 만든다.


````
---
title: "Diamonds distribution dashboard"
output: flexdashboard::flex_dashboard
---

```{r setup, include = FALSE}
library(ggplot2)
library(dplyr)
knitr::opts_chunk$set(fig.width = 5, fig.asp = 1/3)
```

## Column 1

### Carat

```{r}
ggplot(diamonds, aes(carat)) + geom_histogram(binwidth = 0.1)
```

### Cut

```{r}
ggplot(diamonds, aes(cut)) + geom_bar()
```

### Colour

```{r}
ggplot(diamonds, aes(color)) + geom_bar()
```

## Column 2

### The largest diamonds

```{r}
diamonds %>% 
  arrange(desc(carat)) %>% 
  head(100) %>% 
  select(carat, cut, color, price) %>% 
  DT::datatable()
```
````

flexdashboard 에는 사이드바, 탭모음, 값상자, 게이지를 생성하는 간단한 도구들이 있다. flexdashboard 에 대한 자세한 내용은 다음을 참고하라. <http://rmarkdown.rstudio.com/flexdashboard/>

## 대화형 동작

HTML 유형(문서, 노트북, 프리젠테이션, 대시보드)에는 대화형 구성요소가 포함될 수 있다.

### htmlwidgets

HTML 은 대화형 포맷이므로 html위젯(대화형 HTML 시각화를 생성하는 R 함수들)으로 이러한 대화형 동작을 이용할 수 있다. 예를 들어 다음의 leaflet 지도를 보자. 웹에서 이 페이지를 보고 있다면 지도를 이리저리 드래그하거나, 확대∙축소 등을 할 수 있다. 책에서는 물론 그렇게 할 수 없으므로 rmarkdown이 스크린샷을 자동으로 삽입한다.


```r
library(leaflet)
leaflet() %>%
  setView(174.764, -36.877, zoom = 16) %>% 
  addTiles() %>%
  addMarkers(174.764, -36.877, popup = "Maungawhau") 
```

```{=html}
<div id="htmlwidget-ac96cb3ee4656e2e9ec3" style="width:70%;height:415.296px;" class="leaflet html-widget"></div>
<script type="application/json" data-for="htmlwidget-ac96cb3ee4656e2e9ec3">{"x":{"options":{"crs":{"crsClass":"L.CRS.EPSG3857","code":null,"proj4def":null,"projectedBounds":null,"options":{}}},"setView":[[-36.877,174.764],16,[]],"calls":[{"method":"addTiles","args":["//{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",null,null,{"minZoom":0,"maxZoom":18,"tileSize":256,"subdomains":"abc","errorTileUrl":"","tms":false,"noWrap":false,"zoomOffset":0,"zoomReverse":false,"opacity":1,"zIndex":1,"detectRetina":false,"attribution":"&copy; <a href=\"http://openstreetmap.org\">OpenStreetMap<\/a> contributors, <a href=\"http://creativecommons.org/licenses/by-sa/2.0/\">CC-BY-SA<\/a>"}]},{"method":"addMarkers","args":[-36.877,174.764,null,null,null,{"interactive":true,"draggable":false,"keyboard":true,"title":"","alt":"","zIndexOffset":0,"opacity":1,"riseOnHover":false,"riseOffset":250},"Maungawhau",null,null,null,null,{"interactive":false,"permanent":false,"direction":"auto","opacity":1,"offset":[0,0],"textsize":"10px","textOnly":false,"className":"","sticky":true},null]}],"limits":{"lat":[-36.877,-36.877],"lng":[174.764,174.764]}},"evals":[],"jsHooks":[]}</script>
```

html위젯은 HTML 이나 자바스크립트를 몰라도 사용할 수 있다는 점에서 매우 좋다. 세부사항이 전부 패키지 안에 랩되어 있어서 이에 대해 신경쓸 필요가 없다. 

html위젯을 제공하는 패키지들은 이 외에도 많으며, 일부는 다음과 같다.

* dygraphs (http://rstudio.github.io/dygraphs/): 대화형 시계열 시각화 패키지.

* DT (http://rstudio.github.io/DT/): 대화형 테이블 패키지.

* threejs (https://github.com/bwlewis/rthreejs): 대화형 3d 플롯 패키지.

* DiagrammeR (http://rich-iannone.github.io/DiagrammeR/): (플로 차트와 단순한 노트링크 다이아그램과 같은) 다이어그램 패키지.

html위젯에 대해 더 알아보고, 제공하는 패키지의 전체 목록을 보려면 다음을 방문하라. <http://www.htmlwidgets.org/>

### Shiny

html위젯은 _클라이언트측_ 대화형 기능을 제공한다. 즉, 모든 대화형 기능은 R과는 독립적으로 브라우저에서 일어난다. R에 연결하지 않고 HTML 파일을 배포할 수 있기 때문에 한편으로는 편리하다. 그러나 HTML 과 자바스크립트로 구현된 것들에 할 수 있는 것이 많지 않다. 대신 Shiny를 사용할 수 있는데, 이는 자바스크립트가 아닌 R 코드를 사용하여 대화형 기능을 생성할 수 있게 하는 패키지이다.

R마크다운 문서에서 Shiny 코드를 호출하려면 헤더에 `runtime: shiny` 를 추가하라.

```yaml
title: "Shiny Web App"
output: html_document
runtime: shiny
```

그런 다음 ’입력‘ 함수들을 이용하여 문서에 대화형 구성요소를 추가할 수 있다.


```r
library(shiny)

textInput("name", "What is your name?")
numericInput("age", "How old are you?", NA, min = 0, max = 150)
```
<img src="screenshots/rmarkdown-shiny.png" width="357" style="display: block; margin: auto;" />
이제 `input$name` 과 `input$age` 으로 값들을 참조할 수 있고, 이 값들을 사용하는 코드는 값들이 변경될 때 마다 자동으로 다시 실행된다.

Shiny 앱을 여기서는 보여줄 수 없는데, Shiny 대화형 기능은 _서버측_ 에서 작동하기 때문이다. 따라서 자바스크립트를 몰라도 대화형 앱을 작성할 수 있지만, 실행하기 위해서는 서버가 필요하다. 수송 문제가 생기는 것이다. 즉, Shiny앱은 이를 온라인으로 실행되기 위해서 Shiny 서버가 필요하다. 본인의 컴퓨터에서 Shiny 앱을 실행하면 Shiny는 직접 Shiny 서버를 자동으로 설정하지만, 온라인으로 이러한 대화형 기능을 공개하려면 퍼블릭 Shiny 서버가 필요하다. 

Shiny에 대해서 다음에서 더 알아보라. <http://shiny.rstudio.com/>

## Website

추가 인프라를 조금 더 사용하면 R마크다운을 이용하여 완전한 웹사이트를 생성할 수 있다.

* 단일 디렉터리에 `.Rmd` 파일들을 넣어라. `index.Rmd` 가 홈페이지가 된다.

* `_site.yml` 이라는 이름의 YAML 파일을 추가하면 사이트 탐색기능이 제공된다. 예를 들면

    
    ```
    name: "my-website"
    navbar:
      title: "My Website"
      left:
        - text: "Home"
          href: index.html
        - text: "Viridis Colors"
          href: 1-example.html
        - text: "Terrain Colors"
          href: 3-inline.html
    ```

독립형 정적 웹사이트로 배포할 준비가 된 파일들의 디렉터리인 _site 가 빌드하기 위해, 혹은 RStudio 프로젝트를 사용하고 있다면 `rmarkdown::render_site()` 를 실행하라. RStudio가 IDE에 Build 탭을 추가시키는데, 이 탭은 사이트를 빌드하고 미리보는 데 사용할 수 있다.

다음에서 더 알아보라. <http://rmarkdown.rstudio.com/rmarkdown_websites.html>

## Other formats

다른 패키지들은 더 많은 출력 유형을 제공한다. 

* [bookdown 패키지](https://github.com/rstudio/bookdown) 을 사용하면 책을 저술하기가 쉬운데, 이 책도 이 패키지를 사용했다. 더 알아보려면 이후이 시에(Yihui Xie)의  [_Authoring Books with R Markdown_](https://bookdown.org/yihui/bookdown/)(당연히 bookdown으로 작성됨)을 읽어보라. <http://www.bookdown.org> 을 방문하여 R 커뮤니티에서 저술한 다른 bookdown 책을 보라.

* [prettydoc 패키지](https://github.com/yixuan/prettydoc/) 는 약식 문서 포맷들을 다양한 매력적인 테마와 함께 제공한다.

* [rticles 패키지](https://github.com/rstudio/rticles) 는 선별된 과학 학술지들에 유형이 맞도록 컴파일한다.

더 많은 유형의 목록을 다음에서 확인하라 <http://rmarkdown.rstudio.com/formats.html>. 다음 사이트에 있는 설명서를 따라하여 본인의 유형을 생성할 수도 있다. <http://rmarkdown.rstudio.com/developer_custom_formats.html>.

## 더 배우기

이러한 다양한 유형으로 효과적인 의사소통을 하는 법을 더 배우고 싶다면 다음의 자료들을 추천한다.

* 프리젠테이션 기술들을 개선하고 싶으면 닐 포드(Neal Ford), 매튜 맥컬러프(Matthew McCollough), 너쌔니얼 슈타(Nathaniel Schutta)의 [_Presentation Patterns_](https://amzn.com/0321820800) 를 추천한다. 프리젠테이션을 개선하는 데 적용할 수 있는 효과적인 패턴들(낮은 수준과 높은 수준 모두)을 제공한다.

* 학술 발표를 한다면 [_Leek group guide
  to giving talks_](https://github.com/jtleek/talkguide) 를 읽어보라.

* 직접 수강하지는 않았지만, 맷 맥개러티(Matt McGarrity)의 [대중 연설 온라인 수업](<https://www.coursera.org/learn/public-speaking>) 이 좋다는 이야기를 들었다.

* 대시보드를 많이 생성한다면 스티븐 퓨(Stephen Few)의 [Information Dashboard Design: The Effective Visual Communication 
  of Data](https://amzn.com/0596100167) 를 읽어라. 보기에만 이쁜 것이 아니라 정말 유용한 대시보드들을 생성할 수 있게 도와줄 것이다.

* 그래픽 디자인 지식이 있으면 아이디어를 효과적으로 소통하는 데 도움이 되는 경우가 많다. [_The Non-Designer's Design
  Book_](http://amzn.com/0133966151) 부터 시작하면 좋다.

