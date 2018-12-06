
# 데이터 불러오기

## 들어가기

R 패키지가 제공하는 데이터를 이용하여 데이터 과학 도구를 익히는 것은 좋은
방법이다. 그렇지만 어느 시점에 이르러서는 학습을 중단하고 자신의 데이터로
작업해보고 싶어질 것이다. 이 장에서는 일반 텍스트 직사각형 파일을 R로 불러오는 
방법을 배운다. 데이터 불러오기 맛보기만 할 뿐이지만, 여기에서 배우는
많은 원칙을 다른 형태의 데이터에도 적용할 수 있다. 이 장의 마무리에서 다른
유형의 데이터에 유용한 패키지 몇 가지를 소개한다.

### 준비하기

이 장에서는 tidyverse의 핵심인 **readr** 패키지를 사용하여 플랫 파일을 불러오
는 방법을 학습한다.


```r
library(tidyverse)
```

## 시작하기

readr 함수 대부분은 플랫 파일을 데이터프레임으로 바꾸는 것과 연관이 있다.

* `read_csv()`는 쉼표로 구분된 파일을 읽고, `read_tsv()`는 탭-구분 파일을 읽는다. `read_delim()`은 임의의 구분자로 된 파일을 읽는다.

* `read_fwf()`는 고정 너비 파일을 읽는다. 필드 너비는 `fwf_widths()`를 이용하여,
필드 위치는 `fwf_positions()`를 이용하여 지정할 수 있다. `read_table()`은 고정
너비 파일의 일반적 변형 형태인 열이 공백으로 구분된 파일을 읽는다.

* `read_log()`는 Apache 스타일의 로그 파일을 읽는다. (하지만 `read_log()` 기반
구축되어 더 많은 유용한 도구를 제공하는 [webreadr](https://github.com/Ironholds/webreadr)도 확인하라.)

이 함수들은 문법이 모두 비슷하다. 하나를 익히면 나머지는 쉽게 사용할 수 있다. 
이 장의 나머지 부분에서는 `read_csv()`에 초점을 맞출 것이다. CSV 파일은 가장 
일반적인 형태의 데이터 저장 형태일 뿐 아니라 `read_csv()`를 이해하면 readr의 
다른 모든 함수에 쉽게 적용할 수 있다.

`read_csv()`의 첫 번째 인수가 가장 중요한데 바로 읽으려고 하는 파일의 경로다.


```r
heights <- read_csv("data/heights.csv")
#> Parsed with column specification:
#> cols(
#>   earn = col_double(),
#>   height = col_double(),
#>   sex = col_character(),
#>   ed = col_double(),
#>   age = col_double(),
#>   race = col_character()
#> )
```

`read_csv()`를 실행하면 각 열의 이름과 유형을 제공하는 열 사양이 화면 출력된
다. 이는 readr에서 중요한 부분이다. [파일 파싱하기]에서 다시 살펴보자.

인라인 CSV 파일을 넣을 수도 있다. 이것은 readr로 실험해볼 때와 다른 사람들과 
공유할 재현 가능한 예제를 만들 때 유용하다.


```r
read_csv("a,b,c
1,2,3
4,5,6")
#> # A tibble: 2 x 3
#>       a     b     c
#>   <dbl> <dbl> <dbl>
#> 1     1     2     3
#> 2     4     5     6
```

두 경우 모두 read_csv()는 데이터의 첫 번째 줄을 열 이름으로 사용한다. 이는
매우 일반적인 규칙이다. 이 동작을 조정해야 하는 경우는 두 가지이다.

1.  파일 앞 부분에 메타 데이터 몇 줄이 있는 경우가 있다. `skip = n`을 사용하여
첫 `n`줄을 건너 뛸 수 있다. 또는 `comment = "#"`을 사용하여 `#`으로 시작하는 모
든 줄을 무시할 수 있다.

    
    ```r
    read_csv("The first line of metadata
      The second line of metadata
      x,y,z
      1,2,3", skip = 2)
    #> # A tibble: 1 x 3
    #>       x     y     z
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    
    read_csv("# A comment I want to skip
      x,y,z
      1,2,3", comment = "#")
    #> # A tibble: 1 x 3
    #>       x     y     z
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    ```
    
1.  데이터에 열 이름이 없을 수 있다. `col_names = FALSE`를 사용하면 `read_csv()`가 
첫 행을 헤드로 취급하지 않고 대신 X1에서 Xn까지 순차적으로 이름을 붙인다.

    
    ```r
    read_csv("1,2,3\n4,5,6", col_names = FALSE)
    #> # A tibble: 2 x 3
    #>      X1    X2    X3
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    #> 2     4     5     6
    ```

(`"\n"`은 새 줄을 추가하는 편리한 단축키이다. [문자열 기초]에서 이
단축어와 문자열 이스케이프의 다른 유형에 대해 자세히 배운다.)
다른 방법으로는 `col_names`에 열 이름으로 사용할 문자형 벡터를 전달할 수
도 있다.

    
    ```r
    read_csv("1,2,3\n4,5,6", col_names = c("x", "y", "z"))
    #> # A tibble: 2 x 3
    #>       x     y     z
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    #> 2     4     5     6
    ```

일반적으로 조정이 필요한 또 다른 옵션은 `na`이다. 파일에서 결측값을 나타내는
데 사용되는 값(들)을 지정한다.


```r
read_csv("a,b,c\n1,2,.", na = ".")
#> # A tibble: 1 x 3
#>       a     b c    
#>   <dbl> <dbl> <lgl>
#> 1     1     2 NA
```

여기까지 배운 것들로 실제로 마주하게 될 CSV 파일의 75% 정도를 불러올 수
있다. 또한 탭으로 구분된 파일을 `read_tsv()`를 사용하여, 혹은 고정간격 파일을
`read_fwf()`를 사용하여 불러오는 데도 쉽게 적용할 수 있다. 더 복잡한 파일을 읽으려면 
readr이 각 열을 파싱하여 R 벡터로 바꾸는 방법에 대해 자세히 배워야한다.

### 베이스 R과 비교

R을 이전에 사용한 사람은 우리가 `read.csv()`를 사용하지 않는 이유가 궁금할 것
이다. 베이스 함수보다 readr 함수를 선호하는 타당한 이유가 몇 가지 있다.

* 일반적으로 베이스 함수보다 훨씬 더(~10배) 빠르다. 오래 걸리는 작업은 진
행 표시줄을 통해 상황을 알 수 있다. raw speed로 작업하려면
`data.table::fread()`를 사용해보라. 이 함수는 tidyverse에 잘 어울리지는 않지
만, 훨씬 더 빠를 수 있다.

* 티블을 생성한다. 문자 벡터를 팩터형으로 변환하지도, 행 이름을 사용하거
나 열 이름을 변경하지도 않는다. 베이스 R 함수는 변환, 변경하기 때문에 불
편하다.

* 좀 더 재현 가능하다. 베이스 R 함수는 운영체제 및 환경 변수의 일부 동작을
상속하므로 자신의 컴퓨터에서 작동하는 불러오기 코드가 다른 사람의 컴퓨터에서 
작동하지 않을 수 있다.


### 연습문제

1.  필드가 “|”로 분리된 파일을 읽으려면 어떤 함수를 사용하겠는가?

1.  `file`, `skip`, `comment` 외에 `read_csv()`와 `read_tsv()`가 공통으로 
가진 인수는 무엇인가?

1. `read_fwf()`에서 가장 중요한 인수는 무엇인가?


1.  CSV 파일의 문자열에 쉼표가 포함되는 경우가 있다. 그것들이 문제를 일으
키지 않게 하려면 `"` 혹은 `'`와 같은 인용 문자로 둘러싸일 필요가 있다. 
`read_csv()`는 인용 문자가 `"`라고 가정한다. 이를 변경하려면 `read_delim()`을 대신
사용하면 된다. 다음 텍스트를 데이터프레임으로 읽으려면 어떤 인수를 설정해야하는가?

    
    ```r
    "x,y\n1,'a,b'"
    ```
    
1.  다음 각 인라인 CSV 파일에 어떤 문제가 있는지 확인하라. 코드를 실행하면
어떻게 되는가?

    
    ```r
    read_csv("a,b\n1,2,3\n4,5,6")
    read_csv("a,b,c\n1,2\n1,2,3,4")
    read_csv("a,b\n\"1")
    read_csv("a,b\n1,2\na,b")
    read_csv("a;b\n1;3")
    ```

## 벡터 파싱하기

readr이 디스크에서 파일을 읽는 방법에 대해 깊이 알아보기 전에, 잠깐 벗어나서 
`parse_*()` 함수에 대해 살펴볼 필요가 있다. 이 함수들은 문자형 벡터를 입력으로 
하여 논리형, 정수형 또는 날짜형과 같은 좀 더 특수화된 벡터를 반환한다.


```r
str(parse_logical(c("TRUE", "FALSE", "NA")))
#>  logi [1:3] TRUE FALSE NA
str(parse_integer(c("1", "2", "3")))
#>  int [1:3] 1 2 3
str(parse_date(c("2010-01-01", "1979-10-14")))
#>  Date[1:2], format: "2010-01-01" "1979-10-14"
```

이 함수들은 독립적으로도 유용하지만, readr의 중요한 구성요소이기도 하다.
이 절에서는 개별 파서(parser)가 어떻게 동작하는지를 우선 배우고, 다음 절에
서 개별 파서들이 어떻게 구성되어 파일 전체를 파싱하는지 살펴볼 것이다

tidyverse의 모든 함수와 마찬가지로, `parse_*()` 함수는 동일한 형태이다. 즉,
첫 번째 인수는 파싱할 문자형 벡터이며 `na` 인수는 결측으로 처리되어야 하는 문자열을 
지정한다.


```r
parse_integer(c("1", "231", ".", "456"), na = ".")
#> [1]   1 231  NA 456
```

파싱에 실패하면 경고 메시지가 나타난다.


```r
x <- parse_integer(c("123", "345", "abc", "123.45"))
#> Warning: 2 parsing failures.
#> row col               expected actual
#>   3  -- an integer                abc
#>   4  -- no trailing characters    .45
```

이런 경우에는 출력에서 누락될 것이다.


```r
x
#> [1] 123 345  NA  NA
#> attr(,"problems")
#> # A tibble: 2 x 4
#>     row   col expected               actual
#>   <int> <int> <chr>                  <chr> 
#> 1     3    NA an integer             abc   
#> 2     4    NA no trailing characters .45
```

파싱에 실패한 경우가 많으면 `problems()`를 사용하여 실패 전체를 가져와야 한
다. problems()는 티블을 반환하면, dplyr로 작업할 수 있다.


```r
problems(x)
#> # A tibble: 2 x 4
#>     row   col expected               actual
#>   <int> <int> <chr>                  <chr> 
#> 1     3    NA an integer             abc   
#> 2     4    NA no trailing characters .45
```

파서를 잘 활용하려면 어떤 종류가 있는지, 각종 입력 유형을 어떻게 다루는지
를 잘 이해해야 한다. 특별히 중요한 8개의 파서가 있다.

1.  `parse_logical()` 과 `parse_integer()` 는 각각 논리형 및 정수형을 파싱한다. 기본
적으로 이 파서에 잘못될 수 있는 것은 없으므로 여기서 더 설명하지 않겠다.
    
1.  `parse_double()` 은 엄격한 수치형 파서이고, `parse_number()`는 유연한 수치형 파
서이다. 이들은 예상보다 더 복잡하다. 왜냐하면 세계 여러 지역이 각자 다른
방식으로 숫자를 쓰기 때문이다.

1.  `parse_character()` 는 너무 단순해서 필요 없을 것 같다고 생각할지도 모른다.
그러나 어떤 복잡성 때문에 이 파서가 매우 중요하다. 바로 문자 인코딩이 그것이다.

1.  `parse_factor()` 는 팩터형을 생성하는데, 팩터형은 R이 미리 정해지고 알려진
값으로 범주형 변수를 나타내기 위해 사용하는 데이터 구조이다.

1.  `parse_datetime()`, `parse_date()`, `parse_time()` 을 사용하면 다양한 날짜와 시간
데이터를 파싱할 수 있다. 날짜를 쓰는 방법은 다양하기 때문에 이 함수들이
가장 복잡하다. 다음 절들에서 더 자세히 살펴보기로 하자.

### 숫자

숫자를 파싱하는 것은 간단한 것처럼 보이지만, 까다로운 세 가지 문제가 있다.

1. 세계 여러 지역에서 사람들은 숫자를 다르게 쓴다. 예를 들어 어떤 국가에서
는 실수의 정수 부분과 소수 부분 사이에 `.`를 쓰고 다른 국가에서는 `,`를 쓴다.

1. 숫자는 ‘$1000’, ‘10%’와 같이 단위를 나타내는 다른 문자가 붙어있을 때가 많다.

1. 숫자는 ‘1,000,000’과 같이 쉽게 읽을 수 있도록 ‘그룹화’ 문자가 포함되는 경우가 
많다. 이러한 그룹화 문자는 국가마다 다르다.

첫 번째 문제를 해결하기 위해서 readr은 지역에 따라 파싱 옵션을 지정하는 객체인 ‘로캘(locale)’이라는 개념을 사용한다. 숫자를 파싱할 때 가장 중요한 옵션은 
소수점으로 사용하는 문자이다. 새로운 로캘을 생성하고 `decimal_mark` 인수를
설정하여 기본값인 `.`를 다른 값으로 재정의할 수 있다.


```r
parse_double("1.23")
#> [1] 1.23
parse_double("1,23", locale = locale(decimal_mark = ","))
#> [1] 1.23
```

readr의 기본 로캘은 미국 중심인데, 왜냐하면 일반적으로 R은 미국 중심이기 때문이다 
(즉, 베이스 R의 문서가 미국식 영어로 작성되었다). 다른 방법은 운영체제의 
기본값을 추측하여 시도하는 것이다. 이러한 방법은 잘 동작하지 않고, 더
중요한 것은 코드가 취약하게 된다. 자신의 컴퓨터에서 동작하더라도 코드를 다른 
국가의 동료에게 이메일로 보낼 때 오류가 발생할 수 있다.

두 번째 문제를 처리하는 `parse_number()`는 숫자 앞뒤의 non-numeric
character 를 무시한다. 통화 및 백분율에 특히 유용하지만, 텍스트에 포함
된 숫자를 추출하는 데도 효과적이다.


```r
parse_number("$100")
#> [1] 100
parse_number("20%")
#> [1] 20
parse_number("It cost $123.45")
#> [1] 123
```

마지막 문제는 `parse_number()`와 로캘을 조합하여 `parse_number()`가 ‘그룹화 마크’
를 무시하도록 함으로써 해결할 수 있다.


```r
# Used in America
parse_number("$123,456,789")
#> [1] 1.23e+08

# Used in many parts of Europe
parse_number("123.456.789", locale = locale(grouping_mark = "."))
#> [1] 1.23e+08

# Used in Switzerland
parse_number("123'456'789", locale = locale(grouping_mark = "'"))
#> [1] 1.23e+08
```

### 문자열 {#readr-strings}

`parse_character()`는 정말 단순할 것처럼 보인다. 입력을 단순히 반환하는 것 아닌가. 
그런데 불행하게도 삶은 그렇게 호락호락하지 않다. 같은 문자열을 나타내는 
방법은 여러 가지이다. 이게 무슨 이야기인지 이해하려면 컴퓨터가 문자열을 
표시하는 방법에 대해 깊고 상세하게 들어가야 한다. R에서는 `charToRaw()`를
사용하여 문자열의 기본 표현을 볼 수 있다.


```r
charToRaw("Hadley")
#> [1] 48 61 64 6c 65 79
```

각 16진수값은 정보 바이트를 나타낸다. 예를 들면 `48`은 H를 나타내고, `61`은 a
를 나타낸다. 16진수 수치를 문자로 매핑하는 것을 인코딩이라고 하며, 앞의
인코딩은 ASCII 라고 한다. ASCII 는 정보 교환을 위한 미국 표준 코드
(American Standard Code for Information Interchange)의 줄임말이며 따라서
영문자를 잘 표현한다.

영어가 아닌 다른 언어의 경우 더욱 복잡해진다. 컴퓨터 시대 초창기에는 비영어권 
문자 인코딩을 위한 여러 표준 규격이 있었다. 문자열을 정확하게 해석하기 위해서는 
값과 인코딩을 모두 알아야했다. 예를 들어 두 가지 일반적인 인코딩은 
Latin1(ISO-8859-1, 서유럽 언어들에서 사용)과 Latin2(ISO-8859-2, 동유럽 언어들에서 사용)이다. Latin1에서 바이트 `b1`은 ‘±’이지만, Latin2에서는 ‘ą’이다! 다행히 오늘날에는 거의 모든 곳에서 지원되는 하나의 표준인 UTF-8이 있다. UTF-8은 오늘날 인간이 사용하는 거의 모든 문자와 기타 기호들(예: 이모티콘)을 
인코딩할 수 있다.

readr은 모든 곳에서 UTF-8을 사용한다. 데이터를 읽을 때 UTF-8이라고 가정
하며, 쓸 때는 항상 사용한다. UTF-8은 좋은 기본값이지만, 이를 인식하지 못하
는 구형 시스템에서 생성된 데이터에 사용할 수 없다. 이런 상황이면 문자열을
화면 출력할 때 이상하게 보인다. 한두 개의 문자만 엉망이 될 수도 있고, 완전
히 외계어들을 볼 수도 있다. 다음의 예를 보자.



```r
x1 <- "El Ni\xf1o was particularly bad this year"
x2 <- "\x82\xb1\x82\xf1\x82\xc9\x82\xbf\x82\xcd"

x1
#> [1] "El Ni\xf1o was particularly bad this year"
x2
#> [1] "\x82\xb1\x82\xf1\x82\u0242\xbf\x82\xcd"
```

문제를 해결하려면 `parse_character()`에서 인코딩을 지정해야 한다.


```r
parse_character(x1, locale = locale(encoding = "Latin1"))
#> [1] "El Niño was particularly bad this year"
parse_character(x2, locale = locale(encoding = "Shift-JIS"))
#> [1] "こんにちは"
```

올바른 인코딩을 어떻게 찾을 수 있을까? 운이 좋다면 데이터 문서의 어딘가
에 포함되었을 것이다. 하지만 불행하게도 그런 경우는 거의 없으므로, readr는
`guess_encoding()`을 제공하여 사용자가 알아낼 수 있도록 도와준다. 이것은 완벽
하지도 않고, (앞의 사례와 달리) 텍스트가 많아야 더 잘 작동하지만, 한번 시도
해볼 만한 방법이다. 올바른 인코딩을 찾기 전에 몇 가지 다른 인코딩을 시도해
보라.


```r
guess_encoding(charToRaw(x1))
#> # A tibble: 2 x 2
#>   encoding   confidence
#>   <chr>           <dbl>
#> 1 ISO-8859-1       0.46
#> 2 ISO-8859-9       0.23
guess_encoding(charToRaw(x2))
#> # A tibble: 1 x 2
#>   encoding confidence
#>   <chr>         <dbl>
#> 1 KOI8-R         0.42
```

`guess_encoding()`의 첫 번째 인수로는 파일의 경로 혹은, 이 예제와 같이 원시 벡터 (문자열이 이미 R에 있는 경우 유용함)가 될 수 있다.

인코딩은 방대하고 복잡한 주제이며, 여기서 우리는 단지 겉핥기만 한 것이다. 더 배우고 싶으면 <http://kunststube.net/encoding>에서 자세한 설명을 읽어보길
추천한다.

### 팩터형 {#readr-factors}

R은 팩터형을 사용하여, 가질 수 있는 값을 미리 알고 있는 범주형 변수를 나타낸다. 
예상치 못한 값이 있을 때마다 경고를 생성하려면 `parse_factor()`에 가질
수 있는 레벨의 벡터를 제공하면 된다.


```r
fruit <- c("apple", "banana")
parse_factor(c("apple", "banana", "bananana"), levels = fruit)
#> Warning: 1 parsing failure.
#> row col           expected   actual
#>   3  -- value in level set bananana
#> [1] apple  banana <NA>  
#> attr(,"problems")
#> # A tibble: 1 x 4
#>     row   col expected           actual  
#>   <int> <int> <chr>              <chr>   
#> 1     3    NA value in level set bananana
#> Levels: apple banana
```

그러나 입력값에 문제가 많이 있는 경우에는, 그 입력값을 우선 문자형 벡터로
남겨두고 [문자열]과 [팩터형]에서 배울 도구를 사용하여 정리하는 것이 쉬울 때가 많다.

### Dates, date-times, 시간 {#readr-datetimes}

You pick between three parsers depending on whether you want a date (the number of days since 1970-01-01), a date-time (the number of seconds since midnight 1970-01-01), or a time (the number of seconds since midnight). When called without any additional arguments:

*   `parse_datetime()` expects an ISO8601 date-time. ISO8601 is an
    international standard in which the components of a date are
    organised from biggest to smallest: year, month, day, hour, minute, 
    second.
    
    
    ```r
    parse_datetime("2010-10-01T2010")
    #> [1] "2010-10-01 20:10:00 UTC"
    # If time is omitted, it will be set to midnight
    parse_datetime("20101010")
    #> [1] "2010-10-10 UTC"
    ```
    
    This is the most important date/time standard, and if you work with
    dates and times frequently, I recommend reading
    <https://en.wikipedia.org/wiki/ISO_8601>
    
*   `parse_date()` expects a four digit year, a `-` or `/`, the month, a `-` 
    or `/`, then the day:
    
    
    ```r
    parse_date("2010-10-01")
    #> [1] "2010-10-01"
    ```

*   `parse_time()` expects the hour, `:`, minutes, optionally `:` and seconds, 
    and an optional am/pm specifier:
  
    
    ```r
    library(hms)
    parse_time("01:10 am")
    #> 01:10:00
    parse_time("20:10:01")
    #> 20:10:01
    ```
    
    Base R doesn't have a great built in class for time data, so we use 
    the one provided in the hms package.

If these defaults don't work for your data you can supply your own date-time `format`, built up of the following pieces:

Year
: `%Y` (4 digits). 
: `%y` (2 digits); 00-69 -> 2000-2069, 70-99 -> 1970-1999.

Month
: `%m` (2 digits).
: `%b` (abbreviated name, like "Jan").
: `%B` (full name, "January").

Day
: `%d` (2 digits).
: `%e` (optional leading space).

Time
: `%H` 0-23 hour.
: `%I` 0-12, must be used with `%p`.
: `%p` AM/PM indicator.
: `%M` minutes.
: `%S` integer seconds.
: `%OS` real seconds. 
: `%Z` Time zone (as name, e.g. `America/Chicago`). Beware of abbreviations:
  if you're American, note that "EST" is a Canadian time zone that does not
  have daylight savings time. It is _not_ Eastern Standard Time! We'll
  come back to this [time zones].
: `%z` (as offset from UTC, e.g. `+0800`). 

Non-digits
: `%.` skips one non-digit character.
: `%*` skips any number of non-digits.

The best way to figure out the correct format is to create a few examples in a character vector, and test with one of the parsing functions. For example:


```r
parse_date("01/02/15", "%m/%d/%y")
#> [1] "2015-01-02"
parse_date("01/02/15", "%d/%m/%y")
#> [1] "2015-02-01"
parse_date("01/02/15", "%y/%m/%d")
#> [1] "2001-02-15"
```

If you're using `%b` or `%B` with non-English month names, you'll need to set the  `lang` argument to `locale()`. See the list of built-in languages in `date_names_langs()`, or if your language is not already included, create your own with `date_names()`.


```r
parse_date("1 janvier 2015", "%d %B %Y", locale = locale("fr"))
#> [1] "2015-01-01"
```

### Exercises

1.  What are the most important arguments to `locale()`? 

1.  What happens if you try and set `decimal_mark` and `grouping_mark` 
    to the same character? What happens to the default value of 
    `grouping_mark` when you set `decimal_mark` to ","? What happens
    to the default value of `decimal_mark` when you set the `grouping_mark`
    to "."?

1.  I didn't discuss the `date_format` and `time_format` options to
    `locale()`. What do they do? Construct an example that shows when 
    they might be useful.

1.  If you live outside the US, create a new locale object that encapsulates 
    the settings for the types of file you read most commonly.
    
1.  What's the difference between `read_csv()` and `read_csv2()`?
    
1.  What are the most common encodings used in Europe? What are the
    most common encodings used in Asia? Do some googling to find out.

1.  Generate the correct format string to parse each of the following 
    dates and times:
    
    
    ```r
    d1 <- "January 1, 2010"
    d2 <- "2015-Mar-07"
    d3 <- "06-Jun-2017"
    d4 <- c("August 19 (2015)", "July 1 (2015)")
    d5 <- "12/30/14" # Dec 30, 2014
    t1 <- "1705"
    t2 <- "11:15:10.12 PM"
    ```

## Parsing a file

Now that you've learned how to parse an individual vector, it's time to return to the beginning and explore how readr parses a file. There are two new things that you'll learn about in this section:

1. How readr automatically guesses the type of each column.
1. How to override the default specification.

### Strategy

readr uses a heuristic to figure out the type of each column: it reads the first 1000 rows and uses some (moderately conservative) heuristics to figure out the type of each column. You can emulate this process with a character vector using `guess_parser()`, which returns readr's best guess, and `parse_guess()` which uses that guess to parse the column:


```r
guess_parser("2010-10-01")
#> [1] "date"
guess_parser("15:01")
#> [1] "time"
guess_parser(c("TRUE", "FALSE"))
#> [1] "logical"
guess_parser(c("1", "5", "9"))
#> [1] "double"
guess_parser(c("12,352,561"))
#> [1] "number"

str(parse_guess("2010-10-10"))
#>  Date[1:1], format: "2010-10-10"
```

The heuristic tries each of the following types, stopping when it finds a match:

* logical: contains only "F", "T", "FALSE", or "TRUE".
* integer: contains only numeric characters (and `-`).
* double: contains only valid doubles (including numbers like `4.5e-5`).
* number: contains valid doubles with the grouping mark inside.
* time: matches the default `time_format`.
* date: matches the default `date_format`.
* date-time: any ISO8601 date.

If none of these rules apply, then the column will stay as a vector of strings.

### Problems

These defaults don't always work for larger files. There are two basic problems:

1.  The first thousand rows might be a special case, and readr guesses
    a type that is not sufficiently general. For example, you might have 
    a column of doubles that only contains integers in the first 1000 rows. 

1.  The column might contain a lot of missing values. If the first 1000
    rows contain only `NA`s, readr will guess that it's a character 
    vector, whereas you probably want to parse it as something more
    specific.

readr contains a challenging CSV that illustrates both of these problems:


```r
challenge <- read_csv(readr_example("challenge.csv"))
#> Parsed with column specification:
#> cols(
#>   x = col_double(),
#>   y = col_logical()
#> )
#> Warning: 1000 parsing failures.
#>  row col           expected     actual                                                                                         file
#> 1001   y 1/0/T/F/TRUE/FALSE 2015-01-16 '/Library/Frameworks/R.framework/Versions/3.5/Resources/library/readr/extdata/challenge.csv'
#> 1002   y 1/0/T/F/TRUE/FALSE 2018-05-18 '/Library/Frameworks/R.framework/Versions/3.5/Resources/library/readr/extdata/challenge.csv'
#> 1003   y 1/0/T/F/TRUE/FALSE 2015-09-05 '/Library/Frameworks/R.framework/Versions/3.5/Resources/library/readr/extdata/challenge.csv'
#> 1004   y 1/0/T/F/TRUE/FALSE 2012-11-28 '/Library/Frameworks/R.framework/Versions/3.5/Resources/library/readr/extdata/challenge.csv'
#> 1005   y 1/0/T/F/TRUE/FALSE 2020-01-13 '/Library/Frameworks/R.framework/Versions/3.5/Resources/library/readr/extdata/challenge.csv'
#> .... ... .................. .......... ............................................................................................
#> See problems(...) for more details.
```

(Note the use of `readr_example()` which finds the path to one of the files included with the package)

There are two printed outputs: the column specification generated by looking at the first 1000 rows, and the first five parsing failures. It's always a good idea to explicitly pull out the `problems()`, so you can explore them in more depth:


```r
problems(challenge)
#> # A tibble: 1,000 x 5
#>     row col   expected      actual   file                                 
#>   <int> <chr> <chr>         <chr>    <chr>                                
#> 1  1001 y     1/0/T/F/TRUE… 2015-01… '/Library/Frameworks/R.framework/Ver…
#> 2  1002 y     1/0/T/F/TRUE… 2018-05… '/Library/Frameworks/R.framework/Ver…
#> 3  1003 y     1/0/T/F/TRUE… 2015-09… '/Library/Frameworks/R.framework/Ver…
#> 4  1004 y     1/0/T/F/TRUE… 2012-11… '/Library/Frameworks/R.framework/Ver…
#> 5  1005 y     1/0/T/F/TRUE… 2020-01… '/Library/Frameworks/R.framework/Ver…
#> 6  1006 y     1/0/T/F/TRUE… 2016-04… '/Library/Frameworks/R.framework/Ver…
#> # ... with 994 more rows
```

A good strategy is to work column by column until there are no problems remaining. Here we can see that there are a lot of parsing problems with the `x` column - there are trailing characters after the integer value. That suggests we need to use a double parser instead.

To fix the call, start by copying and pasting the column specification into your original call:


```r
challenge <- read_csv(
  readr_example("challenge.csv"), 
  col_types = cols(
    x = col_integer(),
    y = col_character()
  )
)
```

Then you can tweak the type of the `x` column:


```r
challenge <- read_csv(
  readr_example("challenge.csv"), 
  col_types = cols(
    x = col_double(),
    y = col_character()
  )
)
```

That fixes the first problem, but if we look at the last few rows, you'll see that they're dates stored in a character vector:


```r
tail(challenge)
#> # A tibble: 6 x 2
#>       x y         
#>   <dbl> <chr>     
#> 1 0.805 2019-11-21
#> 2 0.164 2018-03-29
#> 3 0.472 2014-08-04
#> 4 0.718 2015-08-16
#> 5 0.270 2020-02-04
#> 6 0.608 2019-01-06
```

You can fix that by specifying that `y` is a date column:


```r
challenge <- read_csv(
  readr_example("challenge.csv"), 
  col_types = cols(
    x = col_double(),
    y = col_date()
  )
)
tail(challenge)
#> # A tibble: 6 x 2
#>       x y         
#>   <dbl> <date>    
#> 1 0.805 2019-11-21
#> 2 0.164 2018-03-29
#> 3 0.472 2014-08-04
#> 4 0.718 2015-08-16
#> 5 0.270 2020-02-04
#> 6 0.608 2019-01-06
```

Every `parse_xyz()` function has a corresponding `col_xyz()` function. You use `parse_xyz()` when the data is in a character vector in R already; you use `col_xyz()` when you want to tell readr how to load the data.

I highly recommend always supplying `col_types`, building up from the print-out provided by readr. This ensures that you have a consistent and reproducible data import script. If you rely on the default guesses and your data changes, readr will continue to read it in. If you want to be really strict, use `stop_for_problems()`: that will throw an error and stop your script if there are any parsing problems.

### Other strategies

There are a few other general strategies to help you parse files:

*   In the previous example, we just got unlucky: if we look at just
    one more row than the default, we can correctly parse in one shot:
   
    
    ```r
    challenge2 <- read_csv(readr_example("challenge.csv"), guess_max = 1001)
    #> Parsed with column specification:
    #> cols(
    #>   x = col_double(),
    #>   y = col_date(format = "")
    #> )
    challenge2
    #> # A tibble: 2,000 x 2
    #>       x y         
    #>   <dbl> <date>    
    #> 1   404 NA        
    #> 2  4172 NA        
    #> 3  3004 NA        
    #> 4   787 NA        
    #> 5    37 NA        
    #> 6  2332 NA        
    #> # ... with 1,994 more rows
    ```

*   Sometimes it's easier to diagnose problems if you just read in all
    the columns as character vectors:
   
    
    ```r
    challenge2 <- read_csv(readr_example("challenge.csv"), 
      col_types = cols(.default = col_character())
    )
    ```
    
    This is particularly useful in conjunction with `type_convert()`,
    which applies the parsing heuristics to the character columns in a data
    frame.

    
    ```r
    df <- tribble(
      ~x,  ~y,
      "1", "1.21",
      "2", "2.32",
      "3", "4.56"
    )
    df
    #> # A tibble: 3 x 2
    #>   x     y    
    #>   <chr> <chr>
    #> 1 1     1.21 
    #> 2 2     2.32 
    #> 3 3     4.56
    
    # Note the column types
    type_convert(df)
    #> Parsed with column specification:
    #> cols(
    #>   x = col_double(),
    #>   y = col_double()
    #> )
    #> # A tibble: 3 x 2
    #>       x     y
    #>   <dbl> <dbl>
    #> 1     1  1.21
    #> 2     2  2.32
    #> 3     3  4.56
    ```
    
*   If you're reading a very large file, you might want to set `n_max` to
    a smallish number like 10,000 or 100,000. That will accelerate your 
    iterations while you eliminate common problems.

*   If you're having major parsing problems, sometimes it's easier
    to just read into a character vector of lines with `read_lines()`,
    or even a character vector of length 1 with `read_file()`. Then you
    can use the string parsing skills you'll learn later to parse
    more exotic formats.

## Writing to a file

readr also comes with two useful functions for writing data back to disk: `write_csv()` and `write_tsv()`. Both functions increase the chances of the output file being read back in correctly by:

* Always encoding strings in UTF-8.
  
* Saving dates and date-times in ISO8601 format so they are easily
  parsed elsewhere.

If you want to export a csv file to Excel, use `write_excel_csv()` --- this writes a special character (a "byte order mark") at the start of the file which tells Excel that you're using the UTF-8 encoding.

The most important arguments are `x` (the data frame to save), and `path` (the location to save it). You can also specify how missing values are written with `na`, and if you want to `append` to an existing file.


```r
write_csv(challenge, "challenge.csv")
```

Note that the type information is lost when you save to csv:


```r
challenge
#> # A tibble: 2,000 x 2
#>       x y         
#>   <dbl> <date>    
#> 1   404 NA        
#> 2  4172 NA        
#> 3  3004 NA        
#> 4   787 NA        
#> 5    37 NA        
#> 6  2332 NA        
#> # ... with 1,994 more rows
write_csv(challenge, "challenge-2.csv")
read_csv("challenge-2.csv")
#> Parsed with column specification:
#> cols(
#>   x = col_double(),
#>   y = col_logical()
#> )
#> # A tibble: 2,000 x 2
#>       x y    
#>   <dbl> <lgl>
#> 1   404 NA   
#> 2  4172 NA   
#> 3  3004 NA   
#> 4   787 NA   
#> 5    37 NA   
#> 6  2332 NA   
#> # ... with 1,994 more rows
```

This makes CSVs a little unreliable for caching interim results---you need to recreate the column specification every time you load in. There are two alternatives:

1.  `write_rds()` and `read_rds()` are uniform wrappers around the base 
    functions `readRDS()` and `saveRDS()`. These store data in R's custom 
    binary format called RDS:
    
    
    ```r
    write_rds(challenge, "challenge.rds")
    read_rds("challenge.rds")
    #> # A tibble: 2,000 x 2
    #>       x y         
    #>   <dbl> <date>    
    #> 1   404 NA        
    #> 2  4172 NA        
    #> 3  3004 NA        
    #> 4   787 NA        
    #> 5    37 NA        
    #> 6  2332 NA        
    #> # ... with 1,994 more rows
    ```
  
1.  The feather package implements a fast binary file format that can
    be shared across programming languages:
    
    
    ```r
    library(feather)
    write_feather(challenge, "challenge.feather")
    read_feather("challenge.feather")
    #> # A tibble: 2,000 x 2
    #>       x      y
    #>   <dbl> <date>
    #> 1   404   <NA>
    #> 2  4172   <NA>
    #> 3  3004   <NA>
    #> 4   787   <NA>
    #> 5    37   <NA>
    #> 6  2332   <NA>
    #> # ... with 1,994 more rows
    ```

Feather tends to be faster than RDS and is usable outside of R. RDS supports list-columns (which you'll learn about in [many models]); feather currently does not.



## Other types of data

To get other types of data into R, we recommend starting with the tidyverse packages listed below. They're certainly not perfect, but they are a good place to start. For rectangular data:

* __haven__ reads SPSS, Stata, and SAS files.

* __readxl__ reads excel files (both `.xls` and `.xlsx`).

* __DBI__, along with a database specific backend (e.g. __RMySQL__, 
  __RSQLite__, __RPostgreSQL__ etc) allows you to run SQL queries against a 
  database and return a data frame.

For hierarchical data: use __jsonlite__ (by Jeroen Ooms) for json, and __xml2__ for XML. Jenny Bryan has some excellent worked examples at <https://jennybc.github.io/purrr-tutorial/>.

For other file types, try the [R data import/export manual](https://cran.r-project.org/doc/manuals/r-release/R-data.html) and the [__rio__](https://github.com/leeper/rio) package.
