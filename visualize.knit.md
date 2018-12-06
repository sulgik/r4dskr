
# 데이터 시각화

## 들어가기

> “간단한 그래프는 다른 어떤 방법보다 데이터분석가에게 
> 더 많은 정보를 제공한다.” — 죤 튜키 (John Tukey)


이 장에서는 **ggplot2**를 이용하여 데이터를 시각화하는 법을 배울 것이다. R 은 그래프를 만드는 몇몇 시스템이 있지만 **ggplot2**는 가장 우아하고 다재다능한 시스템 중 하나이다. **ggplot2**는 그래프를 기술하고 구축하는 시스템인 **그래픽 문법**을 구현한다. **ggplot2**으로 하나의 시스템을 배우고 이를 여러 곳에서 적용하여 빠르게 진행할 수 있다. 

시작하기 전 **ggplot2**의 이론적 토대에 대해 더 자세히 알고 싶다면 [The Layered Grammar of Graphics](http://vita.had.co.nz/papers/layered-grammar.pdf)를 읽을 것을 추천한다.  

### 준비하기

이 장에서는 tidyverse의 핵심 구성원 중 하나인 **ggplot2**을 집중하여 살펴본다. 이 장에서 사용할 데이터셋, 도움말 페이지, 함수에 접근하기 위해 다음 코드를 실행하여 tidyverse를 로드하라.  


```r
library(tidyverse)
```

이 한 줄의 코드만 입력하면 tidyverse 핵심패키지들이 로드되는데, 거의 모든 데이터 분석에서 이 패키지들을 사용할 것이다. 또한 이 코드는 tidyverse의 어떤 함수가 베이스 R 함수들 (혹은 이미 로드한 다른 패키지의 함수들)과 충돌하는지도 알려준다. 

만약 이 코드를 실행하고 "there is no package called 'tidyverse’"라는 오류 메시지가 뜨면 먼저 패키지를 설치한 후 `library()`를 다시 실행해야 한다. 


```r
install.packages("tidyverse")
library(tidyverse)
```

패키지는 한 번만 인스톨하면 되지만, 새로운 세션을 시작할 때마다 다시 로드해야 한다. 

어떤 함수나 데이터셋이 어느 패키지에서 왔는지 명시할 필요가 있는 경우에는 특수 형식인 `package::function()`를 사용할 것이다. 예를 들어 `ggplot2::ggplot()`는 **ggplot2** 패키지의 `ggplot()` 함수를 사용한다는 것을 명시한다. 

## 첫 단계

다음의 질문에 답하기 위해 그래프를 이용해 보자. 엔진이 큰 차가 작은 차보다 연료를 더 많이 사용하는가? 이미 답은 알고 있겠지만, 답을 정교하게 만들어보자. 엔진 크기와 연비의 관계는 어떠한가? 양의 관계? 음의 관계? 선형? 비선형? 

### `mpg` 데이터프레임

**ggplot2** (다른 표현으로 `ggplot2::mpg`)에 있는 `mpg` 데이터프레임으로 여러분의 답을 테스트할 수 있다. 데이터프레임은 변수들(열)과 관측값들(행)의 직사각형 형태 집합체이다. `mpg`에는 미 환경보호당국이 수집한 38개의 차 모델들에 대한 관측값들이 포함되어 있다. 


```r
mpg
#> # A tibble: 234 x 11
#>   manufacturer model displ  year   cyl trans drv     cty   hwy fl    class
#>   <chr>        <chr> <dbl> <int> <int> <chr> <chr> <int> <int> <chr> <chr>
#> 1 audi         a4      1.8  1999     4 auto… f        18    29 p     comp…
#> 2 audi         a4      1.8  1999     4 manu… f        21    29 p     comp…
#> 3 audi         a4      2    2008     4 manu… f        20    31 p     comp…
#> 4 audi         a4      2    2008     4 auto… f        21    30 p     comp…
#> 5 audi         a4      2.8  1999     6 auto… f        16    26 p     comp…
#> 6 audi         a4      2.8  1999     6 manu… f        18    26 p     comp…
#> # ... with 228 more rows
```

`mpg`에는 다음과 같은 변수들이 있다. 

1. `displ`: 엔진 크기 (단위: 리터) 

1. `hwy`: 고속도로에서의 자동차 연비 (단위: 갤런당 마일, mpg). 같은 거리를 주행할 때, 연비가 낮은 차는 연비가 높은 차보다 연료를 더 많이 소비한다. 

`mpg`에 대해 더 알고자 한다면 `?mpg`를 실행하여 해당 도움말 페이지를 이용하라. 

### ggplot 생성하기

다음 코드를 실행하여 `mpg`를 플롯할 때 `displ`을 x축, `hwy`를 y축에 놓아라. 


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy))
```

<img src="visualize_files/figure-html/unnamed-chunk-4-1.png" width="70%" style="display: block; margin: auto;" />

이 플롯은 엔진 크기(`displ`)와 연비(`hwy`) 사이에 음의 관계가 있음을 보여준다. 다른 말로 하면 엔진이 큰 차들은 연료를 더 많이 사용한다. 이제 연비와 엔진 크기에 대한 여러분의 가설이 확인되거나 반증되었는가? 

 

**ggplot2**에서는 `ggplot()` 함수로 플롯을 시작한다. `ggplot()`을 하면 레이어를 추가시킬 수 있는 좌표 시스템이 생성된다. `ggplot()`의 첫 번째 인수는 그래프에서 사용할 데이터셋이다. 따라서 `ggplot(data = mpg)`를 하면 빈 그래프가 생성되지만, 그리 흥미로운 것이 아니므로 생략하겠다. 

 

그래프는 `ggplot()`에 레이어를 하나 이상 추가해서 완성된다. 함수 `geom_point()`는 플롯에 점 레이어를 추가하여 산점도를 생성한다. **ggplot2**에는 많은 geom 함수가 있는데, 각각은 플롯에 다른 유형의 레이어를 추가한다. 이 장에서 이 많은 함수를 모두 배울 것이다. 

 

**ggplot2**의 각각의 geom 함수에는 매핑 인수가 있다. 이 인수는 데이터셋의 변수들이 시각적 속성으로 어떻게 매핑될 지를 정의한다. 이 인수는 항상 `aes()`와 쌍을 이루는데, `aes()`의 `x, y` 인수는 x, y축으로 매핑될 변수를 지정한다. **ggplot2**는 매핑된 변수를 데이터 인수(우리 경우엔 `mpg`)에서 찾는다. 

### 그래프 작성 탬플릿

이제 이 코드를 **ggplot2**로 그래프를 만드는, 재사용 가능한 템플릿으로 바꿔보자. 그래프를 만들려면 다음의 코드에서 괄호 안의 부분을 해당되는 데이터셋, geom함수나 매핑모음으로 바꾸라. 


```r
ggplot(data = <DATA>) + 
  <GEOM_FUNCTION>(mapping = aes(<MAPPINGS>))
```

이 장의 나머지 부분에서는 이 템플릿을 완성하고 확장하여 다른 유형의 그래프들을 만드는 법을 살펴볼 것이다. `<MAPPINGS>` 부분부터 시작해보자. 


### 연습문제

1. `ggplot(data = mpg)`을 실행하라. 무엇이 나타나는가? 

1. `mpg`는 행이 몇 개인가? 열은 몇 개인가? 

1. `drv` 변수는 무엇을 나타내는가? `?mpg`로 도움말 페이지를 참고하여 알아보자. 

1. `hwy` 대 `cyl`의 산점도를 만들어라. 

1. `class` 대 `drv` 산점도를 만들면 어떻게 되는가? 이 플롯이 유용하지 않은 이유는 무엇인가? 

## Aesthetic 매핑
> "그래프는 전혀 예상하지 못한 것을 보여줄 때 가장 큰 가치가 있다." - 죤 튜키

다음 플롯에서 한 그룹의 포인트들은(빨간색으로 강조) 선형 추세를 벗어나는 것 처럼 보인다. 이 차들은 예상한 것보다 더 높은 연비를 가진다. 이 차들을 어떻게 설명할 수 있을까?  


<img src="visualize_files/figure-html/unnamed-chunk-6-1.png" width="70%" style="display: block; margin: auto;" />

이 차들은 하이브리드 차라고 가설을 세워보자. 이 가설을 검정하는 방법으로 각 차의 `class` 값을 살펴보는 방법이 있다. `mpg` 데이터셋의 `class` 변수는 차를 소형, 중형, SUV 같은 그룹으로 분류한다. 이상점들이 하이브리드 차들이라면 소형이나 경차로 분류되었을 것이다. (이 데이터들은 하이브리드 트럭이나 SUV가 대중화되기 전에 수집되었음을 염두에 두자.) 

`class` 같은 세 번째 변수를 **aesthetics**에 매핑하여 이차원 산점도에 추가할 수도 있다. aesthetics은 플롯에 객체들의 시각적 속성이다. aesthetics에는 포인트의 크기, 모양, 색상 같은 것들이 포함된다. aesthetics 속성 값을 변경하여 점을 (아래와 같이) 다른 방법으로 표시할 수 있다. 데이터를 설명할 때 ’값’이라는 용어를 이미 사용했으므로 aesthetics 속성을 설명할 때는 단어 ’수준(level)’이라는 용어를 사용하자. 여기에서는 크기, 모양, 색상의 수준을 변경하여 다음과 같이 점을 작게 혹은 삼각형이나 파란색으로 만들었다. 


<img src="visualize_files/figure-html/unnamed-chunk-7-1.png" width="70%" style="display: block; margin: auto;" />

플롯의 aesthetics를 데이터셋의 변수들에 매핑해서 데이터에 대한 정보를 전달할 수 있다. 예를 들어 점의 색상을 `class` 변수에 매핑하여 각 차의 차종을 나타낼 수 있다. 


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, color = class))
```

<img src="visualize_files/figure-html/unnamed-chunk-8-1.png" width="70%" style="display: block; margin: auto;" />

(해들리처럼 영국식 영어를 선호한다면 `color` 대신 `colour`를 사용할 수도 있다.) 

aesthetics을 변수에 매핑하기 위해서는 `aes()` 내부에서 aesthetics 이름을 변수 이름과 연결해야 한다. **ggplot2**는 변수의 고유한 값에 aesthetics의 고유한 수준(여기서는 고유한 색상)을 자동으로 지정하는데, 이 과정을 **스케일링 (scaling)**이라고 한다. **ggplot2**는 어떤 수준이 어떤 값에 해당하는지를 설명하는 범례도 추가한다. 

플롯의 색상들을 보면 이상점 중 다수가 2인승 차임을 보여준다. 이 차들은 하이브리드 차가 아닌 것 같고, 놀랍게도 스포츠카들이다! 스포츠카들은 SUV와 픽업트럭처럼 엔진이 크지만, 차체가 중형차나 소형차처럼 작아서 연비가 좋다. 다시 생각해보면 이 차들은 엔진 크기가 컸기 때문에 하이브리드일 가능성이 낮다. 

위의 예제에서 `class` 변수를 색상 aesthetics에 매핑했지만 이 변수를 같은 방법으로 크기 aesthetics에 매핑할 수도 있다. 이 경우, 각 포인트의 정확한 크기는 차종을 나타낼 것이다. 여기서 **경고**가 뜨는데, 비순서 변수 (`class`)를 순서형 aesthetics (`size`)로 매핑하는 것은 좋은 생각이 아니기 때문이다. 


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, size = class))
#> Warning: Using size for a discrete variable is not advised.
```

<img src="visualize_files/figure-html/unnamed-chunk-9-1.png" width="70%" style="display: block; margin: auto;" />

class`를 **alpha** aesthetic에 매핑할 수도 있었는데, 이는 포인트의 투명도 혹은 모양을 제어한다.


```r
# Left
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, alpha = class))

# Right
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, shape = class))
```

<img src="visualize_files/figure-html/unnamed-chunk-10-1.png" width="50%" /><img src="visualize_files/figure-html/unnamed-chunk-10-2.png" width="50%" />

What happened to the SUVs? ggplot2 will only use six shapes at a time. By default, additional groups will go unplotted when you use the shape aesthetic.

For each aesthetic, you use `aes()` to associate the name of the aesthetic with a variable to display. The `aes()` function gathers together each of the aesthetic mappings used by a layer and passes them to the layer's mapping argument. The syntax highlights a useful insight about `x` and `y`: the x and y locations of a point are themselves aesthetics, visual properties that you can map to variables to display information about the data. 

Once you map an aesthetic, ggplot2 takes care of the rest. It selects a reasonable scale to use with the aesthetic, and it constructs a legend that explains the mapping between levels and values. For x and y aesthetics, ggplot2 does not create a legend, but it creates an axis line with tick marks and a label. The axis line acts as a legend; it explains the mapping between locations and values.

You can also _set_ the aesthetic properties of your geom manually. For example, we can make all of the points in our plot blue:


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy), color = "blue")
```

<img src="visualize_files/figure-html/unnamed-chunk-11-1.png" width="70%" style="display: block; margin: auto;" />

Here, the color doesn't convey information about a variable, but only changes the appearance of the plot. To set an aesthetic manually, set the aesthetic by name as an argument of your geom function; i.e. it goes _outside_ of `aes()`. You'll need to pick a level that makes sense for that aesthetic:

* The name of a color as a character string.

* The size of a point in mm.

* The shape of a point as a number, as shown in Figure \@ref(fig:shapes).

<div class="figure" style="text-align: center">
<img src="visualize_files/figure-html/shapes-1.png" alt="R has 25 built in shapes that are identified by numbers. There are some seeming duplicates: for example, 0, 15, and 22 are all squares. The difference comes from the interaction of the `colour` and `fill` aesthetics. The hollow shapes (0--14) have a border determined by `colour`; the solid shapes (15--18) are filled with `colour`; the filled shapes (21--24) have a border of `colour` and are filled with `fill`." width="75%" />
<p class="caption">(\#fig:shapes)R has 25 built in shapes that are identified by numbers. There are some seeming duplicates: for example, 0, 15, and 22 are all squares. The difference comes from the interaction of the `colour` and `fill` aesthetics. The hollow shapes (0--14) have a border determined by `colour`; the solid shapes (15--18) are filled with `colour`; the filled shapes (21--24) have a border of `colour` and are filled with `fill`.</p>
</div>

### 연습문제

1.  What's gone wrong with this code? Why are the points not blue?

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy, color = "blue"))
    ```
    
    <img src="visualize_files/figure-html/unnamed-chunk-12-1.png" width="70%" style="display: block; margin: auto;" />
    
1.  Which variables in `mpg` are categorical? Which variables are continuous? 
    (Hint: type `?mpg` to read the documentation for the dataset). How
    can you see this information when you run `mpg`?

1.  Map a continuous variable to `color`, `size`, and `shape`. How do
    these aesthetics behave differently for categorical vs. continuous
    variables? 
    
1.  What happens if you map the same variable to multiple aesthetics? 

1.  What does the `stroke` aesthetic do? What shapes does it work with?
    (Hint: use `?geom_point`)
    
1.  What happens if you map an aesthetic to something other than a variable 
    name, like `aes(colour = displ < 5)`?  Note, you'll also need to specify x and y.

## Common problems

As you start to run R code, you're likely to run into problems. Don't worry --- it happens to everyone. I have been writing R code for years, and every day I still write code that doesn't work! 

Start by carefully comparing the code that you're running to the code in the book. R is extremely picky, and a misplaced character can make all the difference. Make sure that every `(` is matched with a `)` and every `"` is paired with another `"`. Sometimes you'll run the code and nothing happens. Check the left-hand of your console: if it's a `+`, it means that R doesn't think you've typed a complete expression and it's waiting for you to finish it. In this case, it's usually easy to start from scratch again by pressing ESCAPE to abort processing the current command.

One common problem when creating ggplot2 graphics is to put the `+` in the wrong place: it has to come at the end of the line, not the start. In other words, make sure you haven't accidentally written code like this:

```R
ggplot(data = mpg) 
+ geom_point(mapping = aes(x = displ, y = hwy))
```

If you're still stuck, try the help. You can get help about any R function by running `?function_name` in the console, or selecting the function name and pressing F1 in RStudio. Don't worry if the help doesn't seem that helpful - instead skip down to the examples and look for code that matches what you're trying to do.

If that doesn't help, carefully read the error message. Sometimes the answer will be buried there! But when you're new to R, the answer might be in the error message but you don't yet know how to understand it. Another great tool is Google: try googling the error message, as it's likely someone else has had the same problem, and has gotten help online.

## 면분할(facet)

변수를 추가하는 방법으로 aesthetic을 이용하는 방법을 보았다.  또다른 방법은 범주형 변수에 특히 유용한 방법인데, 플롯을 데이터 서브셋을 각각 표시하는 하위플롯인 **면분할**(facet)로 나누는 것이다. 

플롯을 하나의 변수에 대해 면분할(facet) 하기 위해서는, `facet_wrap()`을 이용하면 된다. `facet_wrap()`의 첫 번째 인수로는 `~`와 따라나오는 변수 이름으로 된 공식(formula)이어야 한다. (여기서 ’공식‘ 은 R 의 데이터 구조의 한 형태이며 ’등식(equation)’과 같은 의미가 아니다.) `facet_wrap()`에 전달하는 변수는 이산형이어야 한다. 



```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_wrap(~ class, nrow = 2)
```

<img src="visualize_files/figure-html/unnamed-chunk-13-1.png" width="70%" style="display: block; margin: auto;" />

플롯을 두 변수 조합으로 면분할하기 위해서는 `facet_grid()`를 플롯 호출에 추가하면 된다. `facet_grid()`의 첫 번째 인수도 공식이다. 이번에는 공식이 두 개의 변수가 `~`로 분리되어 있는 형태여야 한다. 




```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(drv ~ cyl)
```

<img src="visualize_files/figure-html/unnamed-chunk-14-1.png" width="70%" style="display: block; margin: auto;" />

열이나 행으로 면분할하고 싶지 않다면 변수 이름 대신 `.`를 이용하라. (예: `+ facet_grid(. ~ cyl)`)  

### 연습문제

1. 연속형 변수로 면분할하면 어떻게 되는가? 

1. `facet_grid(drv ~ cyl)`로 만든 플롯에 있는 빈 셀들은 무엇을 의미하는가? 다음의 플롯과 어떻게 연관되는가?


    
    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = drv, y = cyl))
    ```

1.  다음의 코드는 어떤 플롯을 만드는가? `.`은 어떤 역할을 하는가?  
 

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) +
      facet_grid(drv ~ .)
    
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) +
      facet_grid(. ~ cyl)
    ```

1.  이 절의 면분할된 첫 번째 플롯을 살펴보라.  

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) + 
      facet_wrap(~ class, nrow = 2)
    ```
    
    색상 aesthetic을 쓰지 않고 면분할하는 것은 어떤 이점이 있는가? 단점은 무엇인가? 데이터가 더 크다면 이 균형은 어떻게 바뀌겠는가? 
    

1.  `?facet_wrap`을 읽어라. `nrow`의 역할은 무엇인가? `ncol`은 어떤 일을 하는가? 개별 패널의 배치를 조정하는 기타 옵션들은 무엇인가? `facet_grid()`에는 `nrow, ncol`인수가 왜 없는가? 

1.  `facet_grid()`를 사용할 때, 대개의 경우 고유 수준이 더 많은 변수를 열로 두어야 한다. 왜인가?     

## 기하 객체

두 플롯은 유사한가?  

<img src="visualize_files/figure-html/unnamed-chunk-18-1.png" width="50%" /><img src="visualize_files/figure-html/unnamed-chunk-18-2.png" width="50%" />

두 플롯은 동일한 `x` 변수, 동일한 `y` 변수를 포함하고, 동일한 데이터를 나타낸다. 그러나 둘은 같지 않다. 각 플롯은 데이터를 표현하는 시각 객체가 다르다. **ggplot2** 문법으로는 두 플롯이 다른 **지옴(geom)**을 사용한다고 말한다. 

지옴은 데이터를 나타내기 위해 플롯이 사용하는 기하 객체(geometric object)이다. 사람들은 플롯이 사용하는 지옴의 유형으로 플롯을 기술한다. 예를 들어 막대 차트는 막대 지옴들을 이용하고, 라인 차트는 라인 지옴을, 박스플롯은 박스플롯 지옴을 이용하는 식이다. 산점도는 추세를 망가뜨린다. 즉, 포인트 지옴을 사용한다. 위에서 보았듯이, 같은 데이터를 플롯하기 위해 다른 지옴을 사용할 수 있다. 왼쪽의 플롯은 포인트 지옴을 사용했고, 오른쪽의 플롯은 평활(smooth) 지옴, 즉 데이터에 적합된 평활선을 이용했다. 

플롯에서 지옴을 바꾸기 위해서는 `ggplot()`에 추가하는 지옴 함수를 변경하면 된다. 예를 들어 다음의 코드를 사용하여 위 플롯들을 만들었다. 


```r
# left
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy))

# right
ggplot(data = mpg) + 
  geom_smooth(mapping = aes(x = displ, y = hwy))
```

**ggplot2**의 모든 지옴 함수는 `mapping` 인수를 가진다. 그러나 모든 aesthetic이 모든 지옴과 작동하는 것은 아니다. 포인트의 shape(모양)을 설정할 수 있지만, 선의 ’shape’을 설정할 수는 없다. 반면, 라인의 linetype(선유형)을 설정할 수 있다. `geom_smooth()`는 linetype으로 매핑된 변수의 각 고유 값마다 다른 형태의  선을 그린다. 



```r
ggplot(data = mpg) + 
  geom_smooth(mapping = aes(x = displ, y = hwy, linetype = drv))
```

<img src="visualize_files/figure-html/unnamed-chunk-20-1.png" width="70%" style="display: block; margin: auto;" />

여기서 `geom_smooth()`는 자동차의 동력전달장치를 의미하는 `drv` 값에 기초하여 차 모델들을 세 개의 선으로 분리한다. 선 하나는 `4` 값을 가진 점들 모두를 표시하고, 다른 선은 `f`을 가진 모든 점을, 또 다른 선은 `r` 값을 가진 모든 점을 표시한다. 여기서 `4`는 사륜구동, `f`는 전륜구동, `r`은 후륜구동을 나타낸다. 

이것이 이상하게 들린다면 원 데이터 위에 선들을 겹쳐 그린 후, 선과 점을 `drv`에 따라 색상을 입히면 좀 더 명료하게 만들 수 있다.  

<img src="visualize_files/figure-html/unnamed-chunk-21-1.png" width="70%" style="display: block; margin: auto;" />

이 플롯은 같은 그래프에 두 개의 지옴을 포함하고 있는 것을 주목하라! 흥미로운가? 그러면 자, 기대하시라. 다음 절에서는 같은 플롯에 다중의 지옴을 놓는 방법을 배울 것이다.  

**ggplot2**에는 30개가 넘는 지옴이 있고, 확장 패키지에는 더 많은 지옴이 있다.  (예제는 <https://www.ggplot2-exts.org>에 있다). 포괄적인 개요는 **ggplot2**  치트시트에서 가장 잘 볼 수 있는데, <http://rstudio.com/cheatsheets>에서 얻을 수 있다. 더 배우고 싶은 지옴이 있다면 `?geom_smooth` 같이 도움말을 이용하라. 

`geom_smooth()` 같이 많은 수의 지옴은 데이터의 열, 여러 개를 표시 하기 위해 하나의 기하 객체를 사용한다. 이러한 지옴들에 대해 그룹 aesthetic을 다중 객체를 그리는 범주형 변수로 설정할 수 있다. **ggplot2**는 그룹 변수의 각 고유값에 따라 별도의 객체를 그린다. 실제로는 **ggplot2**는 (`linetype` 예제에서와 같이) aesthetic을 이산형 변수에 매핑할 때마다 이 지옴들에 대한 데이터를 자동으로 그룹화한다. 그룹 aesthetic은 기본적으로 범례를 추가하거나 구별시켜주는 기능들을 추가하지 않기 때문에, 이 기능을 활용하면 편리하다. 



```r
ggplot(data = mpg) +
  geom_smooth(mapping = aes(x = displ, y = hwy))
              
ggplot(data = mpg) +
  geom_smooth(mapping = aes(x = displ, y = hwy, group = drv))
    
ggplot(data = mpg) +
  geom_smooth(
    mapping = aes(x = displ, y = hwy, color = drv),
    show.legend = FALSE
  )
```

<img src="visualize_files/figure-html/unnamed-chunk-22-1.png" width="33%" /><img src="visualize_files/figure-html/unnamed-chunk-22-2.png" width="33%" /><img src="visualize_files/figure-html/unnamed-chunk-22-3.png" width="33%" />

같은 플롯에 여러 지옴을 표시하려면 `ggplot()`에 여러 지옴 함수를 추가하라. 


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) +
  geom_smooth(mapping = aes(x = displ, y = hwy))
```

<img src="visualize_files/figure-html/unnamed-chunk-23-1.png" width="70%" style="display: block; margin: auto;" />

 
그러나 이렇게 하면 코드에 중복이 생긴다. y-축을 `hwy` 대신 `cty`을 표시하도록 변경한다고 해보자. 두 군데에서 변수를 변경해야 하는데, 하나를 업데이트하는 것을 잊어버릴 수 있다. 이러한 종류의 중복은 매핑 집합을 `ggplot()`으로 전달하여 피할 수 있다. 이렇게 하면 **ggplot2**는 이 매핑들을 전역 매핑으로 처리하여 그래프의 각 지옴에 적용한다. 다른 말로 하면 다음의 코드는 이전 코드와 동일한 플롯을 생성한다.  


```r
ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + 
  geom_point() + 
  geom_smooth()
```

지옴 함수에 매핑을 넣으면 **ggplot2**는 해당 레이어에 대한 로컬 매핑으로 처리한다. 이 매핑으로 전역 매핑을 확장하거나 덮어쓴 뒤 해당 레이어에만 적용한다. 이렇게 하면 다른 레이어마다 다른 aesthetic을 표시하는 것이 가능하다. 


```r
ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + 
  geom_point(mapping = aes(color = class)) + 
  geom_smooth()
```

<img src="visualize_files/figure-html/unnamed-chunk-25-1.png" width="70%" style="display: block; margin: auto;" />

같은 원리로 각 레이어마다 다른 데이터를 지정할 수 있다. 여기서 우리의 평활선은 `mpg** 데이터셋의 서브셋인 경차만을 표시했다. `geom_smooth()`의 로컬 데이터 인수는 해당 레이어에 한해서만 `ggplot()`의 전역 데이터 인수를 덮어쓴다.





















































