# 결측값 {#missing-values}

## 들어가기


```r
library(tidyverse)
#> ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──
#> ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
#> ✓ tibble  3.1.5     ✓ dplyr   1.0.7
#> ✓ tidyr   1.1.4     ✓ stringr 1.4.0
#> ✓ readr   1.4.0     ✓ forcats 0.5.1
#> ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()
```

Missing topics:

-   Missing values generated from matching data frames (i.e. `left_join()` and `anti_join()`

-   Last observation carried forward and `tidy::fill()`

-   `coalesce()` and `na_if()`

## 기초

### 결측값 {#missing-values-filter}

One important feature of R that can make comparison tricky is missing values, or `NA`s ("not availables").
`NA` represents an unknown value so missing values are "contagious": almost any operation involving an unknown value will also be unknown.


```r
NA > 5
#> [1] NA
10 == NA
#> [1] NA
NA + 10
#> [1] NA
NA / 2
#> [1] NA
```

The most confusing result is this one:


```r
NA == NA
#> [1] NA
```

It's easiest to understand why this is true with a bit more context:


```r
# Let x be Mary's age. We don't know how old she is.
x <- NA
# Let y be John's age. We don't know how old he is.
y <- NA
# Are John and Mary the same age?
x == y
#> [1] NA
# We don't know!
```

If you want to determine if a value is missing, use `is.na()`:


```r
is.na(x)
#> [1] TRUE
```

### Exercises

1.  How many flights have a missing `dep_time`?
    What other variables are missing?
    What might these rows represent?

2.  How could you use `arrange()` to sort all missing values to the start?
    (Hint: use `!is.na()`).

3.  Come up with another approach that will give you the same output as `not_cancelled %>% count(dest)` and `not_cancelled %>% count(tailnum, wt = distance)` (without using `count()`).

4.  Look at the number of cancelled flights per day.
    Is there a pattern?
    Is the proportion of cancelled flights related to the average delay?

## 명시적 vs 암묵적 결측값 {#missing-values-tidy}

데이터셋의 표현 방식을 변경하면 결측값에 중요하고도 미묘한 이슈가 나타난다. 놀랍게도 데이터값은 다음 두 가지 방식으로 결측될 수 있다.

* **명시적** 으로 즉, `NA` 로 표시됨.
* **암묵적** 으로, 즉 단순히 데이터에 존재하지 않음.

아주 간단한 데이터셋으로 이를 설명해보자.


```r
stocks <- tibble(
  year   = c(2015, 2015, 2015, 2015, 2016, 2016, 2016),
  qtr    = c(   1,    2,    3,    4,    2,    3,    4),
  return = c(1.88, 0.59, 0.35,   NA, 0.92, 0.17, 2.66)
)
```

이 데이터셋에는 결측값이 다음과 같이 두 개가 존재한다.

-   2015 년 4 분기 수익은 명백하게 결측되어 있다. 그 값이 있어야 할 셀에 `NA` 가 대신 있기 때문이다.

-   2016 년 1 분기 수익은 암묵적으로 결측되었다. 수익이 데이터셋에 없기 때문이다.
 
다음의 선문답으로 둘의 차이를 생각해볼 수 있다. 명시적 결측값은 결측의 존재이다. 암묵적 결측값은 존재의 결측이다.

데이터셋 표현법으로 암묵적 값을 명시적으로 만들 수 있다. 예를 들어 연도를 열로 넣어 암묵적 결측값을 명시적으로 만들 수 있다.


```r
stocks %>%
  pivot_wider(names_from = year, values_from = return)
#> # A tibble: 4 × 3
#>     qtr `2015` `2016`
#>   <dbl>  <dbl>  <dbl>
#> 1     1   1.88  NA   
#> 2     2   0.59   0.92
#> 3     3   0.35   0.17
#> 4     4  NA      2.66
```

이러한 명시적인 결측값은 어떤 데이터 표현에서는 중요하지 않을 수 있으므로 `pivot_longer()` 에서 `values_drop_na = TRUE` 를 설정하여 명시적 결측값을 암묵적으로 전환할 수 있다.


```r
stocks %>%
  pivot_wider(names_from = year, values_from = return) %>%
  pivot_longer(
    cols = c(`2015`, `2016`),
    names_to = "year",
    values_to = "return",
    values_drop_na = TRUE
  )
#> # A tibble: 6 × 3
#>     qtr year  return
#>   <dbl> <chr>  <dbl>
#> 1     1 2015    1.88
#> 2     2 2015    0.59
#> 3     2 2016    0.92
#> 4     3 2015    0.35
#> 5     3 2016    0.17
#> 6     4 2016    2.66
```

타이디 데이터에서 결측값을 명시적으로 표현하는 또 다른 중요한 도구로 `complete()` 가 있다.


```r
stocks %>%
  complete(year, qtr)
#> # A tibble: 8 × 3
#>    year   qtr return
#>   <dbl> <dbl>  <dbl>
#> 1  2015     1   1.88
#> 2  2015     2   0.59
#> 3  2015     3   0.35
#> 4  2015     4  NA   
#> 5  2016     1  NA   
#> 6  2016     2   0.92
#> # … with 2 more rows
```

`complete()` 는 열 집합을 인풋으로, 고유한 조합을 모두 찾는다. 그런 다음 원본 데이터셋에 모든 값이 포함되도록, 필요한 곳에 명시적 `NA` 를 채운다.

결측값 작업할 때 알아야 할 중요한 도구가 하나 더 있다. 데이터 소스가 주로 데이터 입력에 사용된 경우 결측값은 이전 값이 전달되어야 함을 나타낸다.


```r
treatment <- tribble(
  ~person,           ~treatment, ~response,
  "Derrick Whitmore", 1,         7,
  NA,                 2,         10,
  NA,                 3,         9,
  "Katherine Burke",  1,         4
)
```

이러한 결측값을 `fill()` 을 사용하여 채울 수 있다. 
이 함수는 결측값을 가장 최근의 비결측값으로 치환하고자 하는 열(집합)을 취한다. 이를 마지막 관측값 이월이라고도 한다.


```r
treatment %>%
  fill(person)
#> # A tibble: 4 × 3
#>   person           treatment response
#>   <chr>                <dbl>    <dbl>
#> 1 Derrick Whitmore         1        7
#> 2 Derrick Whitmore         2       10
#> 3 Derrick Whitmore         3        9
#> 4 Katherine Burke          1        4
```

### Exercises

1.  `pivot_wider()` 와 `complete()` 의 `fill` 인수를 비교 대조하라.

1.  `fill()` 의 `direction` 인수는 무엇을 하는가? 

## dplyr verbs

`filter()` only includes rows where the condition is `TRUE`; it excludes both `FALSE` and `NA` values.
If you want to preserve missing values, ask for them explicitly:


```r
df <- tibble(x = c(1, NA, 3))
filter(df, x > 1)
#> # A tibble: 1 × 1
#>       x
#>   <dbl>
#> 1     3
filter(df, is.na(x) | x > 1)
#> # A tibble: 2 × 1
#>       x
#>   <dbl>
#> 1    NA
#> 2     3
```

Missing values are always sorted at the end:


```r
df <- tibble(x = c(5, 2, NA))
arrange(df, x)
#> # A tibble: 3 × 1
#>       x
#>   <dbl>
#> 1     2
#> 2     5
#> 3    NA
arrange(df, desc(x))
#> # A tibble: 3 × 1
#>       x
#>   <dbl>
#> 1     5
#> 2     2
#> 3    NA
```

## Exercises

1.  Why is `NA ^ 0` not missing? Why is `NA | TRUE` not missing? Why is `FALSE & NA` not missing? Can you figure out the general rule? (`NA * 0` is a tricky counterexample!)
