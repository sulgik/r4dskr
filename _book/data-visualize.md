# 데이터 시각화 {#data-visualisation}

## 들어가기

> "간단한 그래프는 데이터 분석가에게 다른 어떤 것보다도 많은 정보를 제공한다." 
--- 존 튜키 (John Tukey)

이 장에서는 ggplot2 를 이용하여 데이터를 시각화하는 법을 배울 것이다.
R 에서 그래프를 만드는 시스템이 몇명 있지만 이 중 가장 우아하고 다재다능한 
시스템 중 하나는 ggplot2 이다.
ggplot2 는 그래프를 설명하고 작성하는 시스템인 **그래픽 문법** 으로 그래프를 
구현한다.
ggplot2 로 하나의 시스템을 배우고 이를 여러 곳에 적용할 수 있다.

### 준비하기

이 장에서는 tidyverse 의 핵심 요소 중 하나인 ggplot2 를 집중적으로 살펴본다.
이 장에서 사용할 데이터셋, 도움말 페이지, 함수에 접근하기 위해 다음의 코드를 
실행하여 tidyverse 를 로드하라:


```r
library(tidyverse)
#> -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
#> v ggplot2 3.3.5     v purrr   0.3.4
#> v tibble  3.1.3     v dplyr   1.0.7
#> v tidyr   1.1.3     v stringr 1.4.0
#> v readr   2.0.0     v forcats 0.5.1
#> -- Conflicts ------------------------------------------ tidyverse_conflicts() --
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()
```

이 한 줄의 코드만 입력하면 tidyverse 핵심 패키지들이 로드되는데, 거의 모든
데이터 분석에서 이 패키지들을 사용할 것이다. 또한 이 코드는 tidyverse 의 어떤 
함수가 베이스 R 함수들(혹은 이미 로드한 다른 패키지의 함수들)과 충돌하
는지도 알려준다.

실행한 뒤 "there is no package called 'tidyverse'" 라는 오류 메시지가 
뜨면 먼저 아래와 같이 패키지를 설치한 후 `library()` 를 다시 실행해야 한다.


```r
install.packages("tidyverse")
library(tidyverse)
```

패키지는 한 번만 설치하면 되지만, 새로운 세션을 시작할 때마다 다시 로드해야 한다.

어떤 함수나 데이터셋이 어느 패키지에서 왔는지 명시해야 할 경우에는 특수형식인 
`package::function()` 을 사용할 것이다.
예를 들어 `ggplot2::ggplot()` 은 ggplot2 패키지의 `ggplot()` 함수를 사용한다는 
것을 명시한다.

## 첫 단계

다음의 질문에 답하기 위해 그래프를 이용해 보자. 엔진이 큰 차가 작은 차보다
연료를 더 많이 소비하는가? 이미 답은 알고 있겠지만, 답을 정교하게 만들어보
자. 엔진 크기와 연비의 관계는 어떠한가? 양의 관계? 음의 관계? 선형? 비선형?

### `mpg` 데이터프레임

ggplot2 에 있는 `mpg` 데이터프레임(다른 표현으로 `ggplot2::mpg`)으로 여러분의 답을 
확인할 수 있다. 데이터프레임은 변수들(열)과 관측값들(행)의 직사각형 형태 
모음이다. `mpg` 에는 미 환경보호당국이 수집한 38개의 차 모델들에 대한 관측
값들이 포함되어 있다.


```r
mpg
#> # A tibble: 234 x 11
#>   manufacturer model displ  year   cyl trans      drv     cty   hwy fl    class 
#>   <chr>        <chr> <dbl> <int> <int> <chr>      <chr> <int> <int> <chr> <chr> 
#> 1 audi         a4      1.8  1999     4 auto(l5)   f        18    29 p     compa~
#> 2 audi         a4      1.8  1999     4 manual(m5) f        21    29 p     compa~
#> 3 audi         a4      2    2008     4 manual(m6) f        20    31 p     compa~
#> 4 audi         a4      2    2008     4 auto(av)   f        21    30 p     compa~
#> 5 audi         a4      2.8  1999     6 auto(l5)   f        16    26 p     compa~
#> 6 audi         a4      2.8  1999     6 manual(m5) f        18    26 p     compa~
#> # ... with 228 more rows
```

`mpg` 에는 다음과 같은 변수들이 있다:

1.  `displ`: 엔진크기 (단위: 리터)

2.  `hwy`:  고속도로에서의 자동차 연비 (단위: 갤런당 마일, mpg)
    같은 거리를 주행할 때, 연비가 낮은 차는 연비가 높은 차보다 연료를 더 많이 소비한다.

`mpg` 에 대해 더 알고자 한다면 `?mpg` 를 실행하여 해당 도움말 페이지를 이용하라.

### ggplot 생성하기

다음의 코드를 실행하여 `mpg` 데이터 플롯을 그려라. `displ` 를 x축, `hwy` 을 y축에 놓아라.


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy))
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-3-1} \end{center}

이 플롯은 엔진 크기(`displ`)와 연비(`hwy`) 사이에 음의 관계가 있음을 보여준다.
다른 말로 하면 엔진이 큰 차들은 연료를 더 많이 소비한다. 
이제 연비와 엔진크기에 대한 여러분의 가설이 확인되거나 반증되었는가?

ggplot2 에서는, `ggplot()` 함수로 플롯을 시작한다.
`ggplot()` 을 하면 좌표시스템이 생성되고 레이어를 추가할 수 있다. 
`ggplot()` 의 첫 번째 인수는 그래프에서 사용할 데이터셋이다. 
따라서 `ggplot(data = mpg)` 를 하면 빈 그래프가 생성되지만,
그리 흥미로운 것이 아니므로 생략하겠다.
그래프는 `ggplot()` 에 하나 이상의 레이어를 추가해서 완성된다. 
함수 `geom_point()` 는 플롯에 점 레이어를 추가하여 산점도를 생성한다. 
ggplot2에는 여러 지옴(geom) 함수가 있는데, 각각은 플롯에 다른 유형의 레이어를 추가한다. 
이 장에서 다양한 함수를 배울 것이다.
ggplot2의 지옴 함수 각각에는 mapping 인수가 있다. 
이 인수는 데이터셋의 변수들이 시각적 속성으로 어떻게 매핑될지를 정의한다. 
이 인수는 항상 aes()와 쌍을 이루는데 `aes()` 의 `x`, `y` 인수는 x, y축으로 매핑될 변수를 지정한다. 
ggplot2 는 매핑된 변수를 `data` 인수(우리 경우엔 `mpg`)에서 찾는다.


### 그래프 작성 템플릿

이제 코드를 ggplot2로 그래프를 만드는, 재사용 가능한 템플릿으로 바꿔보자.
그래프를 만들려면 다음의 코드에서 괄호 안의 <>로 둘러쌓인 부분을, 해당되는
데이터셋, 지옴 함수, 매핑모음으로 바꾸어라.


```r
ggplot(data = <DATA>) + 
  <GEOM_FUNCTION>(mapping = aes(<MAPPINGS>))
```

이 장의 나머지 부분에서는 이 템플릿을 완성하고 확장하여 다른 유형의 그래프
들을 만드는 법을 살펴볼 것이다.
`<MAPPINGS>` 부분부터 시작해보자.

### 연습문제

1.  Run `ggplot(data = mpg)`.
    What do you see?

2.  How many rows are in `mpg`?
    How many columns?

3.  What does the `drv` variable describe?
    Read the help for `?mpg` to find out.

4.  Make a scatterplot of `hwy` vs `cyl`.

5.  What happens if you make a scatterplot of `class` vs `drv`?
    Why is the plot not useful?

## 심미성 매핑

> "그래프는 전혀 예상하지 못한 것을 보여줄 때 가장 큰 가치를 가진다." --- 존 튜키

다음의 그래프에서 한 그룹의 점들(빨간색으로 강조)은 선형 추세를 벗어나는
것처럼 보인다. 이 차들은 예상한 것보다 연비가 높다. 이 차들을 어떻게 설명할
수 있을까?


\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-5-1} \end{center}

Let's hypothesize that the cars are hybrids.
One way to test this hypothesis is to look at the `class` value for each car.
The `class` variable of the `mpg` dataset classifies cars into groups such as compact, midsize, and SUV.
If the outlying points are hybrids, they should be classified as compact cars or, perhaps, subcompact cars (keep in mind that this data was collected before hybrid trucks and SUVs became popular).

You can add a third variable, like `class`, to a two dimensional scatterplot by mapping it to an **aesthetic**.
An aesthetic is a visual property of the objects in your plot.
Aesthetics include things like the size, the shape, or the color of your points.
You can display a point (like the one below) in different ways by changing the values of its aesthetic properties.
Since we already use the word "value" to describe data, let's use the word "level" to describe aesthetic properties.
Here we change the levels of a point's size, shape, and color to make the point small, triangular, or blue:


\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-6-1} \end{center}

You can convey information about your data by mapping the aesthetics in your plot to the variables in your dataset.
For example, you can map the colours of your points to the `class` variable to reveal the class of each car.


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, color = class))
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-7-1} \end{center}

(If you prefer British English, like Hadley, you can use `colour` instead of `color`.)

To map an aesthetic to a variable, associate the name of the aesthetic to the name of the variable inside `aes()`.
ggplot2 will automatically assign a unique level of the aesthetic (here a unique color) to each unique value of the variable, a process known as **scaling**.
ggplot2 will also add a legend that explains which levels correspond to which values.

The colours reveal that many of the unusual points (with engine size greater than 5 litres and highway fuel efficiency greater than 20 miles per gallon) are two-seater cars.
These cars don't seem like hybrids, and are, in fact, sports cars!
Sports cars have large engines like SUVs and pickup trucks, but small bodies like midsize and compact cars, which improves their gas mileage.
In hindsight, these cars were unlikely to be hybrids since they have large engines.

In the above example, we mapped `class` to the color aesthetic, but we could have mapped `class` to the size aesthetic in the same way.
In this case, the exact size of each point would reveal its class affiliation.
We get a *warning* here, because mapping an unordered variable (`class`) to an ordered aesthetic (`size`) is not a good idea.


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, size = class))
#> Warning: Using size for a discrete variable is not advised.
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-8-1} \end{center}

Or we could have mapped `class` to the *alpha* aesthetic, which controls the transparency of the points, or to the shape aesthetic, which controls the shape of the points.


```r
# Left
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, alpha = class))
# Right
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, shape = class))
```

\begin{figure}
\includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-9-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-9-2} \end{figure}

What happened to the SUVs?
ggplot2 will only use six shapes at a time.
By default, additional groups will go unplotted when you use the shape aesthetic.

For each aesthetic, you use `aes()` to associate the name of the aesthetic with a variable to display.
The `aes()` function gathers together each of the aesthetic mappings used by a layer and passes them to the layer's mapping argument.
The syntax highlights a useful insight about `x` and `y`: the x and y locations of a point are themselves aesthetics, visual properties that you can map to variables to display information about the data.

Once you map an aesthetic, ggplot2 takes care of the rest.
It selects a reasonable scale to use with the aesthetic, and it constructs a legend that explains the mapping between levels and values.
For x and y aesthetics, ggplot2 does not create a legend, but it creates an axis line with tick marks and a label.
The axis line acts as a legend; it explains the mapping between locations and values.

You can also *set* the aesthetic properties of your geom manually.
For example, we can make all of the points in our plot blue:


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy), color = "blue")
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-10-1} \end{center}

Here, the color doesn't convey information about a variable, but only changes the appearance of the plot.
To set an aesthetic manually, set the aesthetic by name as an argument of your geom function; i.e. it goes *outside* of `aes()`.
You'll need to pick a level that makes sense for that aesthetic:

-   The name of a color as a character string.

-   The size of a point in mm.

-   The shape of a point as a number, as shown in Figure \@ref(fig:shapes).

\begin{figure}

{\centering \includegraphics[width=0.75\linewidth]{data-visualize_files/figure-latex/shapes-1} 

}

\caption{R has 25 built in shapes that are identified by numbers. There are some seeming duplicates: for example, 0, 15, and 22 are all squares. The difference comes from the interaction of the `colour` and `fill` aesthetics. The hollow shapes (0--14) have a border determined by `colour`; the solid shapes (15--20) are filled with `colour`; the filled shapes (21--24) have a border of `colour` and are filled with `fill`.}(\#fig:shapes)
\end{figure}
### Exercises
1.  What's gone wrong with this code?
    Why are the points not blue?
    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy, color = "blue"))
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-11-1} \end{center}

2.  Which variables in `mpg` are categorical?
    Which variables are continuous?
    (Hint: type `?mpg` to read the documentation for the dataset).
    How can you see this information when you run `mpg`?

3.  Map a continuous variable to `color`, `size`, and `shape`.
    How do these aesthetics behave differently for categorical vs. continuous variables?

4.  What happens if you map the same variable to multiple aesthetics?

5.  What does the `stroke` aesthetic do?
    What shapes does it work with?
    (Hint: use `?geom_point`)

6.  What happens if you map an aesthetic to something other than a variable name, like `aes(colour = displ < 5)`?
    Note, you'll also need to specify x and y.

## Common problems

As you start to run R code, you're likely to run into problems.
Don't worry --- it happens to everyone.
I have been writing R code for years, and every day I still write code that doesn't work!

Start by carefully comparing the code that you're running to the code in the book.
R is extremely picky, and a misplaced character can make all the difference.
Make sure that every `(` is matched with a `)` and every `"` is paired with another `"`.
Sometimes you'll run the code and nothing happens.
Check the left-hand of your console: if it's a `+`, it means that R doesn't think you've typed a complete expression and it's waiting for you to finish it.
In this case, it's usually easy to start from scratch again by pressing ESCAPE to abort processing the current command.

One common problem when creating ggplot2 graphics is to put the `+` in the wrong place: it has to come at the end of the line, not the start.
In other words, make sure you haven't accidentally written code like this:

``` {.r}
ggplot(data = mpg) 
+ geom_point(mapping = aes(x = displ, y = hwy))
```

If you're still stuck, try the help.
You can get help about any R function by running `?function_name` in the console, or selecting the function name and pressing F1 in RStudio.
Don't worry if the help doesn't seem that helpful - instead skip down to the examples and look for code that matches what you're trying to do.

If that doesn't help, carefully read the error message.
Sometimes the answer will be buried there!
But when you're new to R, the answer might be in the error message but you don't yet know how to understand it.
Another great tool is Google: try googling the error message, as it's likely someone else has had the same problem, and has gotten help online.

## Facets

One way to add additional variables is with aesthetics.
Another way, particularly useful for categorical variables, is to split your plot into **facets**, subplots that each display one subset of the data.

To facet your plot by a single variable, use `facet_wrap()`.
The first argument of `facet_wrap()` is a formula, which you create with `~` followed by a variable name (here, "formula" is the bane if a data structure in R, not a synonym for "equation").
The variable that you pass to `facet_wrap()` should be discrete.


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(drv ~ cyl)
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-12-1} \end{center}

To facet your plot on the combination of two variables, add `facet_grid()` to your plot call.
The first argument of `facet_grid()` is also a formula.
This time the formula should contain two variable names separated by a `~`.


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(drv ~ cyl)
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-13-1} \end{center}

If you prefer to not facet in the rows or columns dimension, use a `.` instead of a variable name, e.g. `+ facet_grid(. ~ cyl)`.

### Exercises

1.  What happens if you facet on a continuous variable?

2.  What do the empty cells in plot with `facet_grid(drv ~ cyl)` mean?
    How do they relate to this plot?

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = drv, y = cyl))
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-14-1} \end{center}

3.  What plots does the following code make?
    What does `.` do?

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) +
      facet_grid(drv ~ .)
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) +
      facet_grid(. ~ cyl)
    ```

4.  Take the first faceted plot in this section:

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) + 
      facet_wrap(~ class, nrow = 2)
    ```

    What are the advantages to using faceting instead of the colour aesthetic?
    What are the disadvantages?
    How might the balance change if you had a larger dataset?

5.  Read `?facet_wrap`.
    What does `nrow` do?
    What does `ncol` do?
    What other options control the layout of the individual panels?
    Why doesn't `facet_grid()` have `nrow` and `ncol` arguments?

6.  Which of the following two plots makes it easier to compare engine size (`displ`) across cars with different drive trains?
    What does this say about when to place a faceting variable across rows or columns?

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) + 
      facet_grid(drv ~ .)
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) + 
      facet_grid(. ~ drv)
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-17-1} \includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-17-2} \end{center}

7.  Recreate this plot using `facet_wrap()` instead of `facet_grid()`.
    How do the positions of the facet labels change?

    
    ```r
    ggplot(data = mpg) + 
      geom_point(mapping = aes(x = displ, y = hwy)) +
      facet_grid(drv ~ .)
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-18-1} \end{center}

## Geometric objects

How are these two plots similar?


\includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-19-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-19-2} 

Both plots contain the same x variable, the same y variable, and both describe the same data.
But the plots are not identical.
Each plot uses a different visual object to represent the data.
In ggplot2 syntax, we say that they use different **geoms**.

A **geom** is the geometrical object that a plot uses to represent data.
People often describe plots by the type of geom that the plot uses.
For example, bar charts use bar geoms, line charts use line geoms, boxplots use boxplot geoms, and so on.
Scatterplots break the trend; they use the point geom.
As we see above, you can use different geoms to plot the same data.
The plot on the left uses the point geom, and the plot on the right uses the smooth geom, a smooth line fitted to the data.

To change the geom in your plot, change the geom function that you add to `ggplot()`.
For instance, to make the plots above, you can use this code:


```r
# left
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy))
# right
ggplot(data = mpg) + 
  geom_smooth(mapping = aes(x = displ, y = hwy))
```

Every geom function in ggplot2 takes a `mapping` argument.
However, not every aesthetic works with every geom.
You could set the shape of a point, but you couldn't set the "shape" of a line.
On the other hand, you *could* set the linetype of a line.
`geom_smooth()` will draw a different line, with a different linetype, for each unique value of the variable that you map to linetype.


```r
ggplot(data = mpg) + 
  geom_smooth(mapping = aes(x = displ, y = hwy, linetype = drv))
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-21-1} \end{center}

Here `geom_smooth()` separates the cars into three lines based on their `drv` value, which describes a car's drive train.
One line describes all of the points with a `4` value, one line describes all of the points with an `f` value, and one line describes all of the points with an `r` value.
Here, `4` stands for four-wheel drive, `f` for front-wheel drive, and `r` for rear-wheel drive.

If this sounds strange, we can make it more clear by overlaying the lines on top of the raw data and then colouring everything according to `drv`.


\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-22-1} \end{center}

Notice that this plot contains two geoms in the same graph!
If this makes you excited, buckle up.
You will learn how to place multiple geoms in the same plot very soon.

ggplot2 provides over 40 geoms, and extension packages provide even more (see <https://exts.ggplot2.tidyverse.org/gallery/> for a sampling).
The best way to get a comprehensive overview is the ggplot2 cheatsheet, which you can find at <http://rstudio.com/resources/cheatsheets>.
To learn more about any single geom, use help, e.g. `?geom_smooth`.

Many geoms, like `geom_smooth()`, use a single geometric object to display multiple rows of data.
For these geoms, you can set the `group` aesthetic to a categorical variable to draw multiple objects.
ggplot2 will draw a separate object for each unique value of the grouping variable.
In practice, ggplot2 will automatically group the data for these geoms whenever you map an aesthetic to a discrete variable (as in the `linetype` example).
It is convenient to rely on this feature because the group aesthetic by itself does not add a legend or distinguishing features to the geoms.


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


\includegraphics[width=0.33\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-23-1} \includegraphics[width=0.33\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-23-2} \includegraphics[width=0.33\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-23-3} 

To display multiple geoms in the same plot, add multiple geom functions to `ggplot()`:


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) +
  geom_smooth(mapping = aes(x = displ, y = hwy))
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-24-1} \end{center}

This, however, introduces some duplication in our code.
Imagine if you wanted to change the y-axis to display `cty` instead of `hwy`.
You'd need to change the variable in two places, and you might forget to update one.
You can avoid this type of repetition by passing a set of mappings to `ggplot()`.
ggplot2 will treat these mappings as global mappings that apply to each geom in the graph.
In other words, this code will produce the same plot as the previous code:


```r
ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + 
  geom_point() + 
  geom_smooth()
```

If you place mappings in a geom function, ggplot2 will treat them as local mappings for the layer.
It will use these mappings to extend or overwrite the global mappings *for that layer only*.
This makes it possible to display different aesthetics in different layers.


```r
ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + 
  geom_point(mapping = aes(color = class)) + 
  geom_smooth()
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-26-1} \end{center}

You can use the same idea to specify different `data` for each layer.
Here, our smooth line displays just a subset of the `mpg` dataset, the subcompact cars.
The local data argument in `geom_smooth()` overrides the global data argument in `ggplot()` for that layer only.


```r
ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + 
  geom_point(mapping = aes(color = class)) + 
  geom_smooth(data = filter(mpg, class == "subcompact"), se = FALSE)
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-27-1} \end{center}

(You'll learn how `filter()` works in the chapter on data transformations: for now, just know that this command selects only the subcompact cars.)

### Exercises

1.  What geom would you use to draw a line chart?
    A boxplot?
    A histogram?
    An area chart?

2.  Run this code in your head and predict what the output will look like.
    Then, run the code in R and check your predictions.

    
    ```r
    ggplot(data = mpg, mapping = aes(x = displ, y = hwy, color = drv)) + 
      geom_point() + 
      geom_smooth(se = FALSE)
    ```

3.  What does `show.legend = FALSE` do?
    What happens if you remove it?\
    Why do you think I used it earlier in the chapter?

4.  What does the `se` argument to `geom_smooth()` do?

5.  Will these two graphs look different?
    Why/why not?

    
    ```r
    ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + 
      geom_point() + 
      geom_smooth()
    ggplot() + 
      geom_point(data = mpg, mapping = aes(x = displ, y = hwy)) + 
      geom_smooth(data = mpg, mapping = aes(x = displ, y = hwy))
    ```

6.  Recreate the R code necessary to generate the following graphs.
    Note that wherever a categorical variable is used in the plot, it's `drv`.

    
    \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-30-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-30-2} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-30-3} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-30-4} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-30-5} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-30-6} 
## Statistical transformations
Next, let's take a look at a bar chart.
Bar charts seem simple, but they are interesting because they reveal something subtle about plots.
Consider a basic bar chart, as drawn with `geom_bar()`.
The following chart displays the total number of diamonds in the `diamonds` dataset, grouped by `cut`.
The `diamonds` dataset is in the ggplot2 package and contains information on \~54,000 diamonds, including the `price`, `carat`, `color`, `clarity`, and `cut` of each diamond.
The chart shows that more diamonds are available with high quality cuts than with low quality cuts.

```r
ggplot(data = diamonds) + 
  geom_bar(mapping = aes(x = cut))
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-31-1} \end{center}

On the x-axis, the chart displays `cut`, a variable from `diamonds`.
On the y-axis, it displays count, but count is not a variable in `diamonds`!
Where does count come from?
Many graphs, like scatterplots, plot the raw values of your dataset.
Other graphs, like bar charts, calculate new values to plot:

-   bar charts, histograms, and frequency polygons bin your data and then plot bin counts, the number of points that fall in each bin.

-   smoothers fit a model to your data and then plot predictions from the model.

-   boxplots compute a robust summary of the distribution and then display a specially formatted box.

The algorithm used to calculate new values for a graph is called a **stat**, short for statistical transformation.
The figure below describes how this process works with `geom_bar()`.


\begin{center}\includegraphics[width=1\linewidth]{images/visualization-stat-bar} \end{center}

You can learn which stat a geom uses by inspecting the default value for the `stat` argument.
For example, `?geom_bar` shows that the default value for `stat` is "count", which means that `geom_bar()` uses `stat_count()`.
`stat_count()` is documented on the same page as `geom_bar()`, and if you scroll down you can find a section called "Computed variables".
That describes how it computes two new variables: `count` and `prop`.

You can generally use geoms and stats interchangeably.
For example, you can recreate the previous plot using `stat_count()` instead of `geom_bar()`:


```r
ggplot(data = diamonds) + 
  stat_count(mapping = aes(x = cut))
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-33-1} \end{center}

This works because every geom has a default stat; and every stat has a default geom.
This means that you can typically use geoms without worrying about the underlying statistical transformation.
There are three reasons you might need to use a stat explicitly:

1.  You might want to override the default stat.
    In the code below, I change the stat of `geom_bar()` from count (the default) to identity.
    This lets me map the height of the bars to the raw values of a $y$ variable.
    Unfortunately when people talk about bar charts casually, they might be referring to this type of bar chart, where the height of the bar is already present in the data, or the previous bar chart where the height of the bar is generated by counting rows.

    
    ```r
    demo <- tribble(
      ~cut,         ~freq,
      "Fair",       1610,
      "Good",       4906,
      "Very Good",  12082,
      "Premium",    13791,
      "Ideal",      21551
    )
    ggplot(data = demo) +
      geom_bar(mapping = aes(x = cut, y = freq), stat = "identity")
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-34-1} \end{center}

    (Don't worry that you haven't seen `<-` or `tribble()` before.
    You might be able to guess at their meaning from the context, and you'll learn exactly what they do soon!)

2.  You might want to override the default mapping from transformed variables to aesthetics.
    For example, you might want to display a bar chart of proportions, rather than counts:

    
    ```r
    ggplot(data = diamonds) + 
      geom_bar(mapping = aes(x = cut, y = after_stat(prop), group = 1))
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-35-1} \end{center}

    To find the variables computed by the stat, look for the section titled "computed variables" in the help for `geom_bar()`.

3.  You might want to draw greater attention to the statistical transformation in your code.
    For example, you might use `stat_summary()`, which summarises the y values for each unique x value, to draw attention to the summary that you're computing:

    
    ```r
    ggplot(data = diamonds) + 
      stat_summary(
        mapping = aes(x = cut, y = depth),
        fun.min = min,
        fun.max = max,
        fun = median
      )
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-36-1} \end{center}

ggplot2 provides over 20 stats for you to use.
Each stat is a function, so you can get help in the usual way, e.g. `?stat_bin`.
To see a complete list of stats, try the ggplot2 cheatsheet.

### Exercises

1.  What is the default geom associated with `stat_summary()`?
    How could you rewrite the previous plot to use that geom function instead of the stat function?

2.  What does `geom_col()` do?
    How is it different to `geom_bar()`?

3.  Most geoms and stats come in pairs that are almost always used in concert.
    Read through the documentation and make a list of all the pairs.
    What do they have in common?

4.  What variables does `stat_smooth()` compute?
    What parameters control its behaviour?

5.  In our proportion bar chart, we need to set `group = 1`.
    Why?
    In other words what is the problem with these two graphs?

    
    ```r
    ggplot(data = diamonds) + 
      geom_bar(mapping = aes(x = cut, y = after_stat(prop)))
    ggplot(data = diamonds) + 
      geom_bar(mapping = aes(x = cut, fill = color, y = after_stat(prop)))
    ```

## Position adjustments

There's one more piece of magic associated with bar charts.
You can colour a bar chart using either the `colour` aesthetic, or, more usefully, `fill`:


```r
ggplot(data = diamonds) + 
  geom_bar(mapping = aes(x = cut, colour = cut))
ggplot(data = diamonds) + 
  geom_bar(mapping = aes(x = cut, fill = cut))
```


\includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-38-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-38-2} 

Note what happens if you map the fill aesthetic to another variable, like `clarity`: the bars are automatically stacked.
Each coloured rectangle represents a combination of `cut` and `clarity`.


```r
ggplot(data = diamonds) + 
  geom_bar(mapping = aes(x = cut, fill = clarity))
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-39-1} \end{center}

The stacking is performed automatically by the **position adjustment** specified by the `position` argument.
If you don't want a stacked bar chart, you can use one of three other options: `"identity"`, `"dodge"` or `"fill"`.

-   `position = "identity"` will place each object exactly where it falls in the context of the graph.
    This is not very useful for bars, because it overlaps them.
    To see that overlapping we either need to make the bars slightly transparent by setting `alpha` to a small value, or completely transparent by setting `fill = NA`.

    
    ```r
    ggplot(data = diamonds, mapping = aes(x = cut, fill = clarity)) + 
      geom_bar(alpha = 1/5, position = "identity")
    ggplot(data = diamonds, mapping = aes(x = cut, colour = clarity)) + 
      geom_bar(fill = NA, position = "identity")
    ```
    
    
    \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-40-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-40-2} 

    The identity position adjustment is more useful for 2d geoms, like points, where it is the default.

-   `position = "fill"` works like stacking, but makes each set of stacked bars the same height.
    This makes it easier to compare proportions across groups.

    
    ```r
    ggplot(data = diamonds) + 
      geom_bar(mapping = aes(x = cut, fill = clarity), position = "fill")
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-41-1} \end{center}

-   `position = "dodge"` places overlapping objects directly *beside* one another.
    This makes it easier to compare individual values.

    
    ```r
    ggplot(data = diamonds) + 
      geom_bar(mapping = aes(x = cut, fill = clarity), position = "dodge")
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-42-1} \end{center}

There's one other type of adjustment that's not useful for bar charts, but it can be very useful for scatterplots.
Recall our first scatterplot.
Did you notice that the plot displays only 126 points, even though there are 234 observations in the dataset?


\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-43-1} \end{center}

The underlying values of `hwy` and `displ` are rounded so the points appear on a grid and many points overlap each other.
This problem is known as **overplotting**.
This arrangement makes it hard to see where the mass of the data is.
Are the data points spread equally throughout the graph, or is there one special combination of `hwy` and `displ` that contains 109 values?

You can avoid this gridding by setting the position adjustment to "jitter".
`position = "jitter"` adds a small amount of random noise to each point.
This spreads the points out because no two points are likely to receive the same amount of random noise.


```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy), position = "jitter")
```



\begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-44-1} \end{center}

Adding randomness seems like a strange way to improve your plot, but while it makes your graph less accurate at small scales, it makes your graph *more* revealing at large scales.
Because this is such a useful operation, ggplot2 comes with a shorthand for `geom_point(position = "jitter")`: `geom_jitter()`.

To learn more about a position adjustment, look up the help page associated with each adjustment: `?position_dodge`, `?position_fill`, `?position_identity`, `?position_jitter`, and `?position_stack`.

### Exercises

1.  What is the problem with this plot?
    How could you improve it?

    
    ```r
    ggplot(data = mpg, mapping = aes(x = cty, y = hwy)) + 
      geom_point()
    ```
    
    
    
    \begin{center}\includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-45-1} \end{center}

2.  What parameters to `geom_jitter()` control the amount of jittering?

3.  Compare and contrast `geom_jitter()` with `geom_count()`.

4.  What's the default position adjustment for `geom_boxplot()`?
    Create a visualisation of the `mpg` dataset that demonstrates it.

## Coordinate systems

Coordinate systems are probably the most complicated part of ggplot2.
The default coordinate system is the Cartesian coordinate system where the x and y positions act independently to determine the location of each point.
There are a number of other coordinate systems that are occasionally helpful.

-   `coord_flip()` switches the x and y axes.
    This is useful (for example), if you want horizontal boxplots.
    It's also useful for long labels: it's hard to get them to fit without overlapping on the x-axis.

    
    ```r
    ggplot(data = mpg, mapping = aes(x = class, y = hwy)) + 
      geom_boxplot()
    ggplot(data = mpg, mapping = aes(x = class, y = hwy)) + 
      geom_boxplot() +
      coord_flip()
    ```
    
    
    \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-46-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-46-2} 

    However, note that you can achieve the same result by flipping the aesthetic mappings of the two variables.

    
    ```r
    ggplot(data = mpg, mapping = aes(y = class, x = hwy)) + 
      geom_boxplot()
    ```
    
    
    \includegraphics[width=0.7\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-47-1} 

-   `coord_quickmap()` sets the aspect ratio correctly for maps.
    This is very important if you're plotting spatial data with ggplot2 (which unfortunately we don't have the space to cover in this book).

    
    ```r
    nz <- map_data("nz")
    ggplot(nz, aes(long, lat, group = group)) +
      geom_polygon(fill = "white", colour = "black")
    ggplot(nz, aes(long, lat, group = group)) +
      geom_polygon(fill = "white", colour = "black") +
      coord_quickmap()
    ```
    
    
    \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-48-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-48-2} 

-   `coord_polar()` uses polar coordinates.
    Polar coordinates reveal an interesting connection between a bar chart and a Coxcomb chart.

    
    ```r
    bar <- ggplot(data = diamonds) + 
      geom_bar(
        mapping = aes(x = cut, fill = cut), 
        show.legend = FALSE,
        width = 1
      ) + 
      theme(aspect.ratio = 1) +
      labs(x = NULL, y = NULL)
    bar + coord_flip()
    bar + coord_polar()
    ```
    
    
    \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-49-1} \includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-49-2} 

### Exercises

1.  Turn a stacked bar chart into a pie chart using `coord_polar()`.

2.  What does `labs()` do?
    Read the documentation.

3.  What's the difference between `coord_quickmap()` and `coord_map()`?

4.  What does the plot below tell you about the relationship between city and highway mpg?
    Why is `coord_fixed()` important?
    What does `geom_abline()` do?

    
    ```r
    ggplot(data = mpg, mapping = aes(x = cty, y = hwy)) +
      geom_point() + 
      geom_abline() +
      coord_fixed()
    ```
    
    
    
    \begin{center}\includegraphics[width=0.5\linewidth]{data-visualize_files/figure-latex/unnamed-chunk-50-1} \end{center}

## The layered grammar of graphics

In the previous sections, you learned much more than how to make scatterplots, bar charts, and boxplots.
You learned a foundation that you can use to make *any* type of plot with ggplot2.
To see this, let's add position adjustments, stats, coordinate systems, and faceting to our code template:

    ggplot(data = <DATA>) + 
      <GEOM_FUNCTION>(
         mapping = aes(<MAPPINGS>),
         stat = <STAT>, 
         position = <POSITION>
      ) +
      <COORDINATE_FUNCTION> +
      <FACET_FUNCTION>

Our new template takes seven parameters, the bracketed words that appear in the template.
In practice, you rarely need to supply all seven parameters to make a graph because ggplot2 will provide useful defaults for everything except the data, the mappings, and the geom function.

The seven parameters in the template compose the grammar of graphics, a formal system for building plots.
The grammar of graphics is based on the insight that you can uniquely describe *any* plot as a combination of a dataset, a geom, a set of mappings, a stat, a position adjustment, a coordinate system, and a faceting scheme.

To see how this works, consider how you could build a basic plot from scratch: you could start with a dataset and then transform it into the information that you want to display (with a stat).


\begin{center}\includegraphics[width=1\linewidth]{images/visualization-grammar-1} \end{center}

Next, you could choose a geometric object to represent each observation in the transformed data.
You could then use the aesthetic properties of the geoms to represent variables in the data.
You would map the values of each variable to the levels of an aesthetic.


\begin{center}\includegraphics[width=1\linewidth]{images/visualization-grammar-2} \end{center}

You'd then select a coordinate system to place the geoms into.
You'd use the location of the objects (which is itself an aesthetic property) to display the values of the x and y variables.
At that point, you would have a complete graph, but you could further adjust the positions of the geoms within the coordinate system (a position adjustment) or split the graph into subplots (faceting).
You could also extend the plot by adding one or more additional layers, where each additional layer uses a dataset, a geom, a set of mappings, a stat, and a position adjustment.


\begin{center}\includegraphics[width=1\linewidth]{images/visualization-grammar-3} \end{center}

You could use this method to build *any* plot that you imagine.
In other words, you can use the code template that you've learned in this chapter to build hundreds of thousands of unique plots.
