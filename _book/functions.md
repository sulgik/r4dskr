# 함수 {#functions}

## 들어가기

데이터 과학자로서의 역량을 향상시키는 좋은 방법은 함수를 작성하는 것이다. 함수를 사용하면 복사-붙여넣기를 사용하는 것보다 강력하고 범용적인 방법으로 공통 작업을 자동화할 수 있다. 함수 작성은 복사-붙여넣기에 비해 세 가지 큰 장점이 있다. 

1. 연상시키는 이름을 함수에 부여하여 코드를 이해하기 쉽게 만들 수 있다. 

1. 요구사항이 변경되면 여러 곳이 아닌 한 곳의 코드만 업데이트하면 된다. 

1. 복사하여 붙여넣을 때 실수가 발생할 가능성이 차단된다. (예, 변수 이름을 한 위치에만 업데이트하고 다른 위치에는 하지 않음) 

 

함수를 잘 작성하는 일은 평생의 여정이다. R을 수년 동안 사용해본 나도 새로운 기술과 오래된 문제에 접근하는 더 좋은 방법을 여전히 배우고 있다. 이 장의 목표는 함수의 숨겨진 세부사항을 독자에게 속속들이 가르쳐주는 것이 아니라, 즉시 적용할 수 있는 실용적인 조언을 통해 바로 시작할 수 있게 하는 것이다. 

이 장에서는 함수 작성을 위한 실무적 조언뿐만 아니라 코드 스타일을 정하는 법에 대해서도 제안한다. 좋은 코드 스타일은 문장부호가 정확한 것과 같다. 그것 없이도 어떻게든 되지만, 갖춰지면 코드 읽는 것이 훨씬 더 쉽다! 문장부호 스타일에서와 마찬가지로 다양한 스타일이 가능하다. 여기서는 우리 코드에서 사용하는 스타일을 제시하지만 일관성을 유지하는 것이 가장 중요하다. 

### 준비하기

이 장의 초점은 베이스R로 함수를 작성하는 것이므로 추가 패키지는 필요하지 않다. 

## 함수를 언제 작성해야 하나?

코드 블록을 복사하여 붙여넣는 횟수가 두 번 이상이 될 때면 언제나, 함수를 작성하는 것을 고려해야 한다. (즉, 동일한 코드의 사본이 세 개인 경우이다.) 예를 들어 이 코드를 살펴보라. 무엇을 하는 코드인가? 


```r
df <- tibble::tibble(
  a = rnorm(10),
  b = rnorm(10),
  c = rnorm(10),
  d = rnorm(10)
)

df$a <- (df$a - min(df$a, na.rm = TRUE)) / 
  (max(df$a, na.rm = TRUE) - min(df$a, na.rm = TRUE))
df$b <- (df$b - min(df$b, na.rm = TRUE)) / 
  (max(df$b, na.rm = TRUE) - min(df$a, na.rm = TRUE))
df$c <- (df$c - min(df$c, na.rm = TRUE)) / 
  (max(df$c, na.rm = TRUE) - min(df$c, na.rm = TRUE))
df$d <- (df$d - min(df$d, na.rm = TRUE)) / 
  (max(df$d, na.rm = TRUE) - min(df$d, na.rm = TRUE))
```

각 열이 0 에서 1까지 값을 갖도록 리스케일(rescale)하는 코드라는 것을 알아냈을 것이다. 그런데 실수를 발견했는가? `df$b` 에 대한 코드를 복사하여 붙여넣을 때 오타가 발생했다. 즉, `a` 를 `b` 로 변경하는 것을 까먹었다. 반복 코드를 함수로 추출하면 이와 같은 실수를 막아준다.  

함수를 작성하려면 먼저 코드를 분석해야 한다. 입력은 몇 개인가? 


```r
(df$a - min(df$a, na.rm = TRUE)) /
  (max(df$a, na.rm = TRUE) - min(df$a, na.rm = TRUE))
```

이 코드는 입력이 `df$a`  하나이다. (`TRUE` 가 입력이 아니라는 것을 몰랐다면 연습문제에서 그 이유를 알아볼 수 있다.) 입력을 더 명확하게 지정하려면 일반 이름을 가진 임시 변수를 사용하여 코드를 다시 작성하는 것이 좋다. 여기 우리의 코드는 숫자 벡터 하나만 필요하므로, 이를 `x` 라고 부르자. 


```r
x <- df$a
(x - min(x, na.rm = TRUE)) / (max(x, na.rm = TRUE) - min(x, na.rm = TRUE))
#>  [1] 0.289 0.751 0.000 0.678 0.853 1.000 0.172 0.611 0.612 0.601
```

이 코드에는 중복이 몇 번 있다. 데이터의 범위(range) 계산을 세 번하고 있는데, 한 단계로 수행하는 것이 더 낫다. 


```r
rng <- range(x, na.rm = TRUE)
(x - rng[1]) / (rng[2] - rng[1])
#>  [1] 0.289 0.751 0.000 0.678 0.853 1.000 0.172 0.611 0.612 0.601
```

중간 계산을 명명된 변수로 빼면 코드가 하는 일을 명확하게 할 수 있다. 코드를 단순화했고 작동하는지 확인했으므로 이제 함수로 변환시킬 수 있다. 


```r
rescale01 <- function(x) {
  rng <- range(x, na.rm = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}
rescale01(c(0, 5, 10))
#> [1] 0.0 0.5 1.0
```

함수 생성에는 세 가지 주요 단계가 있다. 

1. **함수 이름** 을 지어야 한다. 여기에서는 `rescale01` 이라고 했는데, 함수가 0과 1 사이에 놓이도록 벡터를 다시 스케일하기 때문이다. 

1. `function`  내부에 함수 입력값, 즉 **인수** 를 나열한다. 여기에서는 인수가 한 개 있다. 여러 개가 있었다면 호출은 `function(x, y, z)` 와 같을 것이다.  

1. 개발한 코드를 함수의 **본문** (body), 즉 `function(...)`  다음에 오는 `{`  블록에 넣는다.  

전체 과정에 주목하라. 앞에서 우리는 간단한 입력값으로 작동시키는 방법을 먼저 찾은 후에 함수를 작성했다. 작동되는 코드를 우선 만들고, 이를 함수로 변환하는 것이 더 쉽다. 함수를 생성한 후 작동되도록 노력하는 것은 더 어렵다. 

이 시점에서 다른 입력값으로 함수를 확인하는 것이 좋다. 


```r
rescale01(c(-10, 0, 10))
#> [1] 0.0 0.5 1.0
rescale01(c(1, 2, 3, NA, 5))
#> [1] 0.00 0.25 0.50   NA 1.00
```

함수 작성을 계속하다 보면, 결국 이러한 비공식적, 대화식 테스트를 공식적, 자동화 테스트로 바꾸고 싶어진다. 이 프로세스를 단위 테스트(unit testing)라고 한다. 이는 불행하게도 이 책의 범위를 벗어나지만 다음 사이트에서 알아볼 수 있다. <http://r-pkgs.had.co.nz/tests.html> 

이제 함수가 있기 때문에 원 예제를 단순화할 수 있다. 


```r
df$a <- rescale01(df$a)
df$b <- rescale01(df$b)
df$c <- rescale01(df$c)
df$d <- rescale01(df$d)
```

원본과 비교하면 이 코드는 이해하기 쉽고, 한 종류의 복사 및 붙여넣기 오류도 제거했다. 한편 여기에도 여전히 중복이 꽤 많이 있는데 여러 열에 똑같은 작업을 하고 있기 때문이다. [반복](#iteration) 에서 R의 데이터 구조에 대해 배운 후, [벡터](#vectors) 에서 이 중복을 제거하는 방법을 배울 것이다. 

함수의 다른 장점은 요구사항이 변경되면, 한 곳에서만 변경 작업을 하면 된다는 것이다. 예를 들어 일부 변수가 무한값을 포함하면 `rescale01()` 은 작동하지 않는다는 것을 알았다고 하자. 


```r
x <- c(1:10, Inf)
rescale01(x)
#>  [1]   0   0   0   0   0   0   0   0   0   0 NaN
```

코드를 함수로 추출했기 때문에 한 곳만 수정하면 된다. 


```r
rescale01 <- function(x) {
  rng <- range(x, na.rm = TRUE, finite = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}
rescale01(x)
#>  [1] 0.000 0.111 0.222 0.333 0.444 0.556 0.667 0.778 0.889 1.000   Inf
```

이는 ’수동으로 반복하지 말 것(Do not repeat yourself, DRY)’ 원칙의 중요한 부분이다. 코드에서 반복을 많이 할수록 상황이 바뀔 때(상황은 항상 바뀜!) 업데이트해야 할 장소가 많아지고, 시간이 지나면서 버그를 만들 가능성이 커진다.

### 연습문제

1.  `TRUE` 가 `rescale01()` 의 매개변수가 아닌 이유는 무엇인가? `x` 가 결측값 하나를 포함하고, `na.rm` 이 `FALSE` 면 어떻게 되는가? 

1. `rescale01()` 의 두 번째 버전에서 무한값들은 변경되지 않았다. `–Inf` 는 0 으로, `Inf` 는 1로 매핑되도록 `rescale01()` 을 다시 작성하라.  

1. 다음의 짧은 코드를 함수로 변환하는 연습을 하라. 각 함수가 무엇을 하는지 생각해보라. 뭐라고 부르겠는가? 인수 몇 개가 필요한가? 좀 더 표현력이 있거나 덜 중복되도록 다시 작성할 수 있는가?

    
    ```r
    mean(is.na(x))
    
    x / sum(x, na.rm = TRUE)
    
    sd(x, na.rm = TRUE) / mean(x, na.rm = TRUE)
    ```

1.  다음 사이트(<http://nicercode.github.io/intro/writing-functions.html>)를 따라 수치형 벡터의 분산과 왜도(skew)를 계산하는 함수를 작성하라. 

1. 같은 길이의 두 벡터를 입력으로 하여, 두 벡터 모두 `NA` 인 위치를 반환하는 함수, `both_na()` 를 작성하라. 

1. 다음 두 함수는 무슨 작업을 하는가? 이 함수들이 짧은 데도 유용한 이유는 무엇인가?       

    
    ```r
    is_directory <- function(x) file.info(x)$isdir
    is_readable <- function(x) file.access(x, 4) == 0
    ```

1. ’작은 토끼 Foo Foo‘의 [전체 가사](https://en.wikipedia.org/wiki/Little_Bunny_Foo_Foo) 를 읽어라. 이 노래에는 중복이 많다. 초기 파이핑 예제를 확장하여 전체 노래를 다시 만들고 함수를 사용하여 중복을 줄여보라.

## 함수는 사람과 컴퓨터를 위한 것

함수는 컴퓨터뿐만 아니라 사람이 사용하는 것이라는 점을 명심해야 한다. 어떤 함수가 호출되는지, 이 함수에 어떤 주석이 포함되어 있는지는 상관없는 문제이지만, 코드를 읽는 사람들에게는 중요하다. 이 절에서는 사람이 이해할 수 있도록 함수를 작성할 때 유의해야 할 몇 가지 사항에 대해 설명한다. 

함수 이름은 중요하다. 이상적으로 함수의 이름은 짧고, 하는 일은 명확하게 나타내야 한다. 결코 쉽지 않다! 그러나 RStudio의 자동 완성을 이용하면 긴 이름을 쉽게 입력할 수 있으므로, 짧은 것보다 명확한 것이 낫다.  

일반적으로 함수 이름은 동사, 인수는 명사이어야 한다. 몇 가지 예외가 있다. 함수가 잘 알려진 명사를 계산하거나 (예: `mean()` 이 `compute_mean()` 보다 나음), 객체의 일부 속성에 접근하는 경우이다(예: `coef()` 가 `get_coefficients()` 보다 나음). ’get‘, ’compute‘, ’calculate‘ 또는 ’determine‘과 같이 매우 일반적인 동사를 사용하고 있다면 이는 명사가 더 좋을 수 있다는 신호이다. 최선의 선택을 해야 한다. 나중에 더 나은 이름이 생각났다면 함수의 이름을 변경하는 것을 두려워하지 말라. 


```r
# 너무 짧음
f()

# 동사가 아니거나 설명하고 있지 않음
my_awesome_function()

# 길지만 명확함
impute_missing()
collapse_years()
```

함수 이름이 여러 단어로 구성된 경우에는 각 소문자 단어를 언더스코어로 구분하는 ’스네이크 표기법(snake\_case)’을 사용하는 것이 좋다. 카멜 표기법(camelCase)은 널리 쓰이는 대안이다. 어떤 것을 선택하느냐는 중요하지 않다. 중요한 것은 일관성을 유지하는 것이다. 즉, 어떤 것을 고르든지 그걸 고수하라. R 자체는 그다지 일관성이 없지만, 우리가 어떻게 할 수 있는 방법은 없다. 가능한 한 코드를 일관되게 만들어 같은 함정에 빠지지 않도록 하라. 


```r
# 절대 이렇게 하지 말것!
col_mins <- function(x, y) {}
rowMaxes <- function(y, x) {}
```

비슷한 일을 하는 함수 계열을 가지고 있다면 이름과 인수가 일관되도록 하라. 공통 접두사를 사용해서 이 함수들이 연결되어 있음을 표시하라. 자동 완성 기능으로 접두사를 입력한 후 한 계열의 구성원 모두를 볼 수 있기 때문에 접미사를 쓰는 것보다 좋다.


```r
# 좋음
input_select()
input_checkbox()
input_text()

# 좋지 않음
select_input()
checkbox_input()
text_input()
```

이러한 설계의 좋은 예가 **stringr**  패키지이다. 필요한 함수를 정확히 기억하지 못하면 `str_` 을 입력하여 기억을 되살려 볼 수 있다. 

가능하면 기존 함수 및 변수를 덮어쓰지 마라. 좋은 이름들은 이미 다른 패키지가 차지했기 때문에 이를 지키는 것은 일반적으로 불가능하다. 그러나 베이스R의 일반적인 이름을 피하면 혼란을 피할 수 있다. 


```r
# 이렇게 하지 말것!
T <- FALSE
c <- 10
mean <- function(x) sum(x)
```

주석, 즉 `#` 으로 시작하는 라인을 사용하여 코드의 ’왜’를 설명하라. 일반적으로 ’무엇‘ 또는 ’어떻게’를 설명하는 주석은 사용하지 않아야 한다. 코드를 읽고 내용을 이해할 수 없으면 코드를 더 명확하게 다시 작성하는 방법을 생각해봐야 한다. 유용한 이름을 가진 중간 변수를 추가해야 할까? 큰 함수를 하위 구성요소로 분해하여 이름 지을 필요가 있을까? 그러나 코드는 의사결정 이면의 논리를 결코 담아낼 수 없다. 다른 방법 대신 이 방법을 선택한 이유? 그 밖에 무엇을 시도했는가? 이런 생각들을 주석에 남기는 것이 좋다. 

주석의 또 다른 용도는 파일을 쉽게 읽을 수 있는 덩어리로 분해하는 것이다. `-`  또는 `=` 의 긴 라인을 사용하여 쉽게 구분 지울 수 있다. 


```r
# 데이터 로드 --------------------------------------

# 데이터 플롯 --------------------------------------
```

RStudio는 이러한 헤더를 생성하는 키보드 단축키 (Cmd/Ctrl + Shift + R)를 제공하며 편집기의 왼쪽 하단 코드 탐색 드롭 다운에 이를 표시한다. 

<img src="screenshots/rstudio-nav.png" width="125" style="display: block; margin: auto;" />

### 연습문제

1.  다음 세 개의 함수 각각에 대한 소스 코드를 읽고, 이 함수들이 하는 일을 알아낸 다음, 더 나은 이름에 대해 브레인스토밍 하라. 
    
    
    ```r
    f1 <- function(string, prefix) {
      substr(string, 1, nchar(prefix)) == prefix
    }
    f2 <- function(x) {
      if (length(x) <= 1) return(NULL)
      x[-length(x)]
    }
    f3 <- function(x, y) {
      rep(y, length.out = length(x))
    }
    ```
    
1.  최근에 작성한 함수에 대해 5분 동안 더 나은 함수 이름과 인수에 대해 브레인스토밍 해보라. 

1. `rnorm()` 과 `MASS::mvrnorm()` 을 비교·대조하라. 어떻게 더 일관되게 만들겠는가? 

1. `norm_r(), norm_d()`  등이 `rnorm(), dnorm()` 보다 나은 이유를 설명하는 사례를 만들어라. 반대의 사례도 만들어라. 

## 조건부 실행

`if`  문을 사용하면 코드를 조건부로 실행할 수 있다. 다음과 같은 형태이다. 


```r
if (condition) {
  # 조건문이 TRUE 일 때 수행되는 코드  
} else {
  # 조건문이 FALSE 일 때 수행되는 코드 
}
```

`if` 에 대한 도움말을 얻으려면 다음과 같이 역따옴표로 둘러싸면 된다. `` ?`if` ``. 경험 많은 프로그래머가 아니면 딱히 도움이 되지는 않지만, 적어도 어떻게 시작하는지는 알 수 있다. 

다음은 `if` 문을 사용하는 간단한 함수이다. 이 함수의 목적은 벡터의 각 요소가 명명되었는지를 나타내는 논리형 벡터를 반환하는 것이다. 


```r
has_name <- function(x) {
  nms <- names(x)
  if (is.null(nms)) {
    rep(FALSE, length(x))
  } else {
    !is.na(nms) & nms != ""
  }
}
```

이 함수는 표준 반환 규칙을 이용한다. 즉 함수는 마지막으로 계산한 값을 반환한다. 여기에서는 `if`  문의 두 갈래 중 하나이다.

### 조건문

조건문은 `TRUE`  또는 `FALSE` 로 평가되어야 한다. 벡터인 경우 경고 메시지가 표시된다. `NA` 인 경우에는 오류가 발생된다. 자신의 코드에서 다음과 같은 메시지를 주의하라. 


```r
if (c(TRUE, FALSE)) {}
#> Warning in if (c(TRUE, FALSE)) {: the condition has length > 1 and only the
#> first element will be used
#> NULL

if (NA) {}
#> Error in if (NA) {: missing value where TRUE/FALSE needed
```

`||` (or)와 `&&` (and)를 사용하여 논리 표현식을 조합할 수 있다. 이 연산자들은 ’단락 (short-circuiting)’한다. 즉, `||` 가 첫 `TRUE` 를 보는 즉시 다른 것 계산없이 `TRUE` 를 반환한다. 마찬가지로 `&&` 가 `FALSE` 를 처음으로 보게 되면 `FALSE` 를 반환한다. `|`  또는 `&` 는 다중값에 적용하는 벡터화 연산이기 때문에(`filter()` 에서 사용하는 이유임) `if`  문에서 절대로 사용해서는 안 된다. 논리형 벡터인 경우 `any()`  또는 `all()` 을 사용하여 단일 값으로 축소할 수 있다. 

두 객체가 같은지(equality)를 테스트할 때는 주의해야 한다. `==` 는 벡터화되므로, 출력이 하나 이상이 되기 쉽다. 길이가 1인지 확인하고 `all()` 이나 `any()` 로 축소시키거나, 벡터화되지 않는 `identical()` 을 사용하라. `identical()` 은 매우 엄격하다. 항상 단일 `TRUE`  또는 `FALSE` 를 반환하며 유형을 강제 변환하지 않는다. 따라서, 정수형과 더블형을 비교할 때 주의해야 한다. 


```r
identical(0L, 0)
#> [1] FALSE
```

부동 소수점 수치에도 주의해야 한다. 


```r
x <- sqrt(2) ^ 2
x
#> [1] 2
x == 2
#> [1] FALSE
x - 2
#> [1] 4.44e-16
```

이를 비교하기 위해서는 [비교](#comparisons) 에 설명된 대로 `dplyr::near()` 를 사용하면 된다. 

또한 `x == NA` 는 아무 쓸모 없다는 것을 기억하라! 

### 다중 조건

여러 if 문을 함께 연결할 수 있다. 


```r
if (this) {
  # do that
} else if (that) {
  # do something else
} else {
  # 
}
```

그런데 `if` 문들이 길게 연속적으로 나타나게 되었다면 다시 작성하는 것을 고려해야 한다. 유용한 방법은 `switch()`  함수이다. 위치 또는 이름을 기반으로 선택한 코드를 평가할 수 있다. 


```
#> function(x, y, op) {
#>   switch(op,
#>     plus = x + y,
#>     minus = x - y,
#>     times = x * y,
#>     divide = x / y,
#>     stop("Unknown op!")
#>   )
#> }
#> <environment: 0x7fd82c6d20b0>
```

긴 `if` 문 체인을 제거할 수 있는 다른 유용한 함수는 `cut()` 이다. 연속 변수를 이산화하는 데 사용된다.

### 코드 스타일

`if` 와 `function`  모두 항상 중괄호(`{}` )가 뒤따라 나와야 하며 본문은 두 칸 들여쓰기를 해야 한다. 이렇게 하면 왼쪽 여백만 보고도 코드의 계층구조를 쉽게 알 수 있다. 

여는 중괄호는 절대로 단독으로 라인을 차지하면 안 되며 새로운 줄바꿈이 따라 나와야 한다. 닫는 중괄호는 `else` 가 뒤따르지 않는 한 항상 단독으로 라인을 차지해야 한다. 중괄호 안에서는 항상 코드를 들여써야 한다. 


```r
# 좋음
if (y < 0 && debug) {
  message("Y is negative")
}

if (y == 0) {
  log(x)
} else {
  y ^ x
}

# 나쁨
if (y < 0 && debug)
message("Y is negative")

if (y == 0) {
  log(x)
} 
else {
  y ^ x
}
```

`if` 문이 한 라인에 들어갈 정도로 짧다면 중괄호를 쓰지 않아도 된다. 


```r
y <- 10
x <- if (y < 20) "Too low" else "Too high"
```

매우 간단한 `if`  문에 대해서만 이를 권장한다. 그런 경우가 아니라면 전체 양식이 더 읽기 쉽다. 


```r
if (y < 20) {
  x <- "Too low" 
} else {
  x <- "Too high"
}
```

### 연습문제

1.  `if` 와 `ifelse()` 는 어떻게 다른가? 도움말을 주의 깊게 읽고 주요 차이점을 보여주는 세 가지 예를 만들어라. 

1. 시간에 따라 ’good morning‘, ’good afternoon‘ 또는 ’good everning‘이라고 말하는 인사말 함수를 작성하라. (힌트: 기본값으로 `lubridate::now()` 를 사용하는 `time`  인수를 사용하라. 이렇게 하면 함수를 더 쉽게 테스트할 수 있다.) 

1. `fizzbuzz`  함수를 구현하라. 단일 숫자를 입력으로 한다. 숫자가 3으로 나누어지면 ’fizz’를 반환한다. 5로 나누어지면 ’buzz’를 반환한다. 3과 5로 나누어지면 ’fizzbuzz’를 반환한다. 그렇지 않으면 숫자를 반환한다. 함수를 작성하기 전에 작동하는 코드를 우선 작성해보라. 

1. 다음의 이중첩된(nested) if-else 문을 단순화하기 위해 `cut()` 을 어떻게 사용하겠는가? 

    
    ```r
    if (temp <= 0) {
      "freezing"
    } else if (temp <= 10) {
      "cold"
    } else if (temp <= 20) {
      "cool"
    } else if (temp <= 30) {
      "warm"
    } else {
      "hot"
    }
    ```
    
    `< ` 대신 `<=` 를 사용하면 어떻게 `cut()`  호출을 변경하겠는가? 이 문제에서 `cut()` 의 다른 장점은 무엇인가? (힌트: `temp` 에 값이 많다면 어떻게 될까?) 

1. `switch()` 를 수치형과 함께 사용하면 어떻게 되나? 

1. 다음의 `switch()`  호출은 어떤 일을 하는가? `x` 가 "e" 이면 어떻게 되는가? 

    
    ```r
    switch(x, 
      a = ,
      b = "ab",
      c = ,
      d = "cd"
    )
    ```
    
    실험해 본 다음 도움말 문서를 주의 깊게 읽어라. 

## 함수 인수

함수 인수는 일반적으로 두 가지 종류로 크게 나뉜다. 한 종류는 데이터를 제공하고 다른 종류는 계산의 세부사항을 제어하는 인수를 제공한다. 예를 들면 

• `log()` 에서 데이터는 `x`, 세부사항은 로그의 `base`(밑)이다. 

• `mean()` 에서 데이터는 `x`, 세부사항은 양 끝에서부터 얼마나 잘라낼 것인지(`trim`)와 결측값을 어떻게 처리할 것인지(`na.rm` )이다. 

• `t.test()` 에서 데이터는 `x` 와 `y` , 세부사항은 `alternative` (대립가설), `mu, paired, var.equal, conf.level` 이다.  

• `str_c()` 에서 문자열 아무 개수나 `...` 에 넣을 수 있고, 병합의 세부사항은 `sep` 과 `collapse` 로 제어한다. 

일반적으로 데이터 인수가 먼저 나와야 한다. 세부사항 인수는 마지막에 있어야 하며 일반적으로 기본값을 가져야 한다. 명명된 인수를 사용하여 함수를 호출하는 것과 같은 방법으로 기본값을 지정한다. 


```r
# 정규분포 근사를 이용한 평균 주위의 신뢰구간 계산 
mean_ci <- function(x, conf = 0.95) {
  se <- sd(x) / sqrt(length(x))
  alpha <- 1 - conf
  mean(x) + se * qnorm(c(alpha / 2, 1 - alpha / 2))
}

x <- runif(100)
mean_ci(x)
#> [1] 0.498 0.610
mean_ci(x, conf = 0.99)
#> [1] 0.480 0.628
```

기본값은 대개의 경우 가장 일반적인 값이어야 한다. 이 법칙에 몇 가지 예외는 안전과 관련이 있다. 예를 들어 결측값은 중요하기 때문에 `na.rm`  기본값을 `FALSE` 로 설정하는 것이 좋다. 대부분 코드에 `na.rm = TRUE` 를 넣는다고 하더라도, 기본값으로 결측값을 조용히 무시하는 것은 좋지 않다. 

함수를 호출할 때 데이터 인수는 자주 사용되기 때문에, 일반적으로 인수 이름을 생략한다. 세부사항 인수의 기본값을 대체하려면 전체 인수 이름을 사용해야 한다. 


```r
# 좋음
mean(1:10, na.rm = TRUE)

# 나쁨
mean(x = 1:10, , FALSE)
mean(, TRUE, x = c(1:10, NA))
```

고유한 접두어(예: `mean(x, n = TRUE)`)로 인수를 지정할 수 있지만, 혼란을 가져올 수 있기 때문에 최대한 피하는 것이 좋다. 

함수를 호출할 때는 호출에서 `=`  주위에 공백을 두어야 하며 쉼표 앞이 아닌 뒤에(일반 영문법에서처럼) 공백을 항상 넣어야 한다. 공백 문자를 사용하면 중요한 함수 구성요소를 쉽게 찾을 수 있다.  


```r
# 좋음
average <- mean(feet / 12 + inches, na.rm = TRUE)

# 나쁨
average<-mean(feet/12+inches,na.rm=TRUE)
```

### 이름 정하기

인수의 이름도 중요하다. R 입장에서는 관계가 없지만 코드의 독자(미래의 여러분 포함!)에게는 중요하다. 일반적으로 길고 설명적인 이름을 선택해야 하지만 매우 일반적이고 짧은 이름이 몇 가지 있다. 다음을 기억하면 좋다.  

• `x, y, z` : 벡터. 
• `w` : 가중치 벡터.
• `df` : 데이터프레임.
• `i, j` : 수치형 인덱스 (일반적으로 행과 열).  
• `n` : 길이 혹은 행 개수
• `p` : 열 개수  

혹은, 기존 R 함수의 인수 이름과 일치시키는 것을 고려하라. 예를 들어 결측값을 제거해야 하는지 결정할 때 `na.rm` 을 사용하라. 

### 값 확인하기

여러 함수를 작성하다 보면 함수가 정확하게 어떻게 작동하는지 기억하지 못할 수도 있다. 이 시점에서 유효하지 않은 입력으로 함수를 호출하기 쉽다. 이 문제를 피하려면 제약조건을 명시적으로 나타내는 것이 좋다. 예를 들어 가중 요약 통계량을 계산하는 함수들을 작성했다고 가정해보자.


```r
wt_mean <- function(x, w) {
  sum(x * w) / sum(w)
}
wt_var <- function(x, w) {
  mu <- wt_mean(x, w)
  sum(w * (x - mu) ^ 2) / sum(w)
}
wt_sd <- function(x, w) {
  sqrt(wt_var(x, w))
}
```

`x` 와 `w` 가 같은 길이가 아니라면 어떻게 되는가?  


```r
wt_mean(1:6, 1:3)
#> [1] 7.67
```

이 경우 R 의 벡터 재활용 규칙 때문에 오류가 발생하지 않는다. 

중요한 전제조건을 확인하고, 그것이 참이 아니라면 (`stop()` 을 사용하여) 오류를 발생시키는 것이 좋다. 


```r
wt_mean <- function(x, w) {
  if (length(x) != lngth(w)) {
    stop("`x` and `w` must be the same length", call. = FALSE)
  }
  sum(w * x) / sum(w)
}
```

이 원칙을 과하게 적용하지 않도록 주의하라. 함수를 튼튼하게 만드는 데 걸리는 시간과 작성하는 데 걸리는 시간 사이에 타협해야 한다. 예를 들어 `na.rm`  인수를 추가할 때, 나는 다음과 같이 신중하게 확인하지는 않을 것이다.


```r
wt_mean <- function(x, w, na.rm = FALSE) {
  if (!is.logical(na.rm)) {
    stop("`na.rm` must be logical")
  }
  if (length(na.rm) != 1) {
    stop("`na.rm` must be length 1")
  }
  if (length(x) != length(w)) {
    stop("`x` and `w` must be the same length", call. = FALSE)
  }
  
  if (na.rm) {
    miss <- is.na(x) | is.na(w)
    x <- x[!miss]
    w <- w[!miss]
  }
  sum(w * x) / sum(w)
}
```

작은 이득에 비해 추가 작업이 너무 많다. 내장 함수 `stopifnot()` 를 쓰는 것이 적당한 타협점이다. 이 함수는 각 인수가 `TRUE` 인지 확인하고 그렇지 않으면 일반 오류 메시지를 생성한다.


```r
wt_mean <- function(x, w, na.rm = FALSE) {
  stopifnot(is.logical(na.rm), length(na.rm) == 1)
  stopifnot(length(x) == length(w))
  
  if (na.rm) {
    miss <- is.na(x) | is.na(w)
    x <- x[!miss]
    w <- w[!miss]
  }
  sum(w * x) / sum(w)
}
wt_mean(1:6, 6:1, na.rm = "foo")
#> Error in wt_mean(1:6, 6:1, na.rm = "foo"): is.logical(na.rm) is not TRUE
```

주의할 점은 `stopifnot()` 를 통해 무엇이 잘못 되었는지 검사한다기 보다, 어떤 것이 참이 돼야 하는지를 확인하는 것이다.

### 점-점-점 (...)

R 함수 중에는 임의의 개수의 입력을 받는 함수가 많다.


```r
sum(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
#> [1] 55
stringr::str_c("a", "b", "c", "d", "e", "f")
#> [1] "abcdef"
```

이 함수들은 어떻게 작동하는가? 이들은 특수 인수인 `...` 에 의존한다. 이 특수 인수는 매치되지 않는 임의 개수의 인수를 가져온다.   

이 `...` 을 다른 함수로 보낼 수 있기 있기 때문에 유용하다. 함수가 다른 함수를 둘러싸는 경우 편리하게 모두 담을 수 있다. 예를 들어 나는 일반적으로 `str_c()` 를 둘러싸는 도우미 함수를 만들곤 한다. 


```r
commas <- function(...) stringr::str_c(..., collapse = ", ")
commas(letters[1:10])
#> [1] "a, b, c, d, e, f, g, h, i, j"

rule <- function(..., pad = "-") {
  title <- paste0(...)
  width <- getOption("width") - nchar(title) - 5
  cat(title, " ", stringr::str_dup(pad, width), "\n", sep = "")
}
rule("주요 출력")
#> 주요 출력 ----------------------------------------------------------------------
```

여기에서 `...` 를 썼기 때문에, 다루고 싶지 않은 어떤 인수도 `str_c()` 에 전달할 수 있었다. 매우 편리한 기술이다. 그러나 치뤄야 할 대가가 있다. 인수 철자가 틀려도 오류를 발생시키지 않는다. 이렇게 되면 오타가 쉽게 눈에 띄지 않게 된다. 


```r
x <- c(1, 2)
sum(x, na.mr = TRUE)
#> [1] 4
```

`...`  값을 캡쳐만 하려면 `list(...)` 을 사용하면 된다. 

### 지연 평가

R의 인수는 지연 평가(lazy evaluation)된다. 즉, 필요할 때까지 계산되지 않는다. 따라서, 사용되지 않는 인수는 호출되지 않는다. 이것은 프로그래밍 언어로서 R의 중요한 속성이지만, 데이터 분석 중 함수를 작성할 때는 일반적으로 중요하지 않다. 지연 평가에 대한 자세한 내용은 다음 사이트에서 확인할 수 있다. (<http://adv-r.had.co.nz/Functions.html#lazy-evaluation>)

### 연습문제

1.  `commas(letters, collapse = "-")`  결과는 무엇인가? 이유는? 

1. `pad`  인수에 여러 문자를 제공할 수 있다면 좋을 것이다(예: `rule("Title", pad = "-+"))` . 왜 작동하지 않는가? 어떻게 고치겠는가? 

1. `mean()` 의 `trim`  인수는 어떤 일을 하는가? 언제 사용하겠는가? 

1. `cor()` 의 `method`  인수 기본값은 `c("pearson", "kendall", "spearman")` 이다. 어떤 의미인가? 기본값으로 어떤 값이 사용되는가? 

## 반환값

 

함수가 무엇을 반환해야 하는지 결정하는 것은 대개 직관적이다. 처음에 함수를 작성한 이유이다! 값을 반환할 때 고려해야 하는 것이 두 가지가 있다. 

1. 반환을 일찍하면 함수 읽기가 쉬워지는가?  

1. 함수를 파이핑할 수 있게 만들 수 있는가? 

### 명시적 반환문

함수가 반환하는 값은 일반적으로 함수가 평가하는 마지막 명령문이지만 `return()` 을 사용하여 일찍 반환하도록 선택할 수 있다. 개인적인 의견으로는 간단한 해결책으로 값을 일찍 반환할 수 있다고 알리는 목적에만 `return()` 을 사용하는 것이 좋다고 생각한다. 이렇게 하는 일반적인 이유는 입력이 빈 경우를 처리하기 위함이다. 


```r
complicated_function <- function(x, y, z) {
  if (length(x) == 0 || length(y) == 0) {
    return(0)
  }
    
  # 복잡한 코드 구역
}
```

또 다른 이유는 하나의 복잡한 블록과 단순한 블록으로 구성된 `if` 문이 있는 경우이다. 예를 들어 다음과 같이 if 문을 작성할 수 있다. 


```r
f <- function() {
  if (x) {
    # 표현하는데  
    # 많은 
    # 라인이 
    # 필요한 
    # 것을 
    # 하는 
    # 구역 
  } else {
    # 짧은 것 반환
  }
}
```

그러나 첫 번째 블록이 너무 길면 `else` 에 도착했을 때는 조건문을 잊어버린다. 다른 구현 방법은 간단한 경우에 대해 반환을 일찍 하는 것이다. 


```r

f <- function() {
  if (!x) {
    return(something_short)
  }

  # 긴 
  # 라인 
  # 으로 
  # 표현 
  # 하는 
  # 구역
}
```

이렇게 하면 코드를 이해하기 위해 문맥을 깊이 볼 필요가 없어서, 코드 읽기가 더 쉬워진다. 

### 파이핑 가능한 함수 작성하기

파이핑 가능한 함수를 작성하려면 반환값에 대해 생각하는 것이 중요하다. 반환값 객체 유형을 아는 것은 파이프라인이 ’작동’할 것이라는 것을 의미한다. 예를 들어 **dplyr** 과 **tidyr** 에서 객체 유형은 데이터프레임이다. 

파이핑 가능한 함수에는 변환과 부수효과라는 두 가지 기본 유형이 있다. **변환함수** 는 객체가 함수의 첫 번째 인수로 전달되고 수정된 객체가 반환된다. **부수효과함수** 에서는 전달된 객체가 변환되지 않는다. 대신, 함수가 플롯을 그리거나 파일을 저장하는 것과 같은 동작을 주어진 객체에 수행한다. 부수효과 함수는 첫 번째 인수가 출력되지 않지만 파이프라인에서 사용될 수 있게 ’보이지 않게‘ 반환해야 한다. 예를 들어‘ 다음의 간단한 함수는 데이터프레임의 결측값 개수를 출력한다.  


```r
show_missings <- function(df) {
  n <- sum(is.na(df))
  cat("Missing values: ", n, "\n", sep = "")
  
  invisible(df)
}
```

대화식에서 호출하면 `invisible()` 은 입력된 `df` 를 출력하지 않음을 의미한다. 


```r
show_missings(mtcars)
#> Missing values: 0
```

그러나 반환값은 여전히 존재한다. 기본값으로 출력되지만 않는다. 


```r
x <- show_missings(mtcars) 
#> Missing values: 0
class(x)
#> [1] "data.frame"
dim(x)
#> [1] 32 11
```

그리고 파이프에서 사용할 수도 있다. 



```r
mtcars %>% 
  show_missings() %>% 
  mutate(mpg = ifelse(mpg < 20, NA, mpg)) %>% 
  show_missings() 
#> Missing values: 0
#> Missing values: 18
```

## 환경

함수의 마지막 구성요소는 환경이다. 이것은 함수를 작성하기 시작할 때부터 깊게 이해해야 할 사항은 아니다. 그러나 함수가 작동하는 방식의 중요한 요소이기 때문에 환경에 대해 조금 알아야 한다. 함수의 환경은 R이 이름과 연관된 값을 찾는 방법을 제어한다. 예를 들어 다음 함수를 보자. 


```r
f <- function(x) {
  x + y
} 
```

함수 내부에서 `y` 가 정의되지 않았기 때문에 많은 프로그래밍 언어에서 위 함수는 오류가 된다. R은 **어휘 스코핑** (lexical scoping)이라는 규칙을 사용하여 이름과 관련된 값을 찾기 때문에 위 함수는 유효한 코드가 된다. 함수 내부에서 `y` 가 정의되지 않았으므로 R은 함수가 정의된 **환경** 에서 찾아본다. 


```r
y <- 100
f(10)
#> [1] 110

y <- 1000
f(10)
#> [1] 1010
```

이 동작은 버그를 만드는 법처럼 보인다. 실제로 이와 같은 함수를 의도적으로 만들지는 않아야 한다. 하지만 대개의 경우 문제를 많이 일으키지 않는다. (정기적으로 R 을 다시 시작하여 슬레이트를 깨끗하게 하면 더욱 문제가 생기지 않는다.) 

이 동작때문에 언어 관점에서 R에 일관성이 생긴다. 모든 이름은 같은 규칙 세트를 사용하여 조회된다. 이 원칙은 `f()` 로는 예상하지 못한 `{` 와 `+` 의 동작까지도 포함한다. 이를 활용하면 다음과 같은 비뚤어진 짓을 할 수 있다.


```r
`+` <- function(x, y) {
  if (runif(1) < 0.1) {
    sum(x, y)
  } else {
    sum(x, y) * 1.1
  }
}
table(replicate(1000, 1 + 2))
#> 
#>   3 3.3 
#> 100 900
rm(`+`)
```

이것은 R 에서 마주할 수 있는 흔한 현상이다. R 은 여러분의 능력에 거의 제한을 두지 않는다. 다른 프로그래밍 언어로 할 수 없는 많은 일을 할 수 있는데, 이 중 99%가 덧셈을 덮어쓰는 것과 같이 극도로 무분별한 일이다. 그러나 이러한 힘과 유연성 때문에 **ggplot2**  및 **dplyr** 과 같은 도구가 가능하다. 이 유연성을 최대한으로 활용하는 법은 이 책의 범위를 벗어나며 해들리 위컴의  [_Advanced R_](http://adv-r.had.co.nz) 에서 알아볼 수 있다.
