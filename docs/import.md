# 데이터 불러오기 {#data-import}

## 들어가기

R 패키지가 제공하는 데이터를 이용하여 데이터 과학 도구를 익히는 것은 좋은
방법이다. 그렇지만 어느 시점에 이르러서는 학습을 중단하고 자신의 데이터로
작업해보고 싶어질 것이다. 이 장에서는 일반 텍스트 직사각형 파일을 R로 불러오는 
방법을 배운다. 데이터 불러오기 맛보기만 할 뿐이지만, 여기에서 배우는
많은 원칙을 다른 형태의 데이터에도 적용할 수 있다. 이 장의 마무리에서 다른
유형의 데이터에 유용한 패키지 몇 가지를 소개한다.

### 준비하기

이 장에서는 tidyverse 의 핵심 구성요소인 **readr** 패키지를 사용하여 플랫 파일을 불러오는 방법을 학습한다.


```r
library(tidyverse)
```

## 시작하기

readr 함수 대부분은 플랫 파일을 데이터프레임으로 바꾸는 것과 연관이 있다.

* `read_csv()` 는 쉼표로 구분된 파일을 읽고, `read_tsv()` 는 탭-구분 파일을 읽는다. `read_delim()` 은 임의의 구분자로 된 파일을 읽는다.

* `read_fwf()` 는 고정 너비 파일을 읽는다. 필드 너비는 `fwf_widths()` 를 이용하여,
필드 위치는 `fwf_positions()` 를 이용하여 지정할 수 있다. `read_table()` 은 고정
너비 파일의 일반적 변형 형태인 열이 공백으로 구분된 파일을 읽는다.

* `read_log()` 는 Apache 스타일의 로그 파일을 읽는다. (하지만 `read_log()` 기반
구축되어 더 많은 유용한 도구를 제공하는 [webreadr](https://github.com/Ironholds/webreadr) 도 확인하라.)

이 함수들은 문법이 모두 비슷하다. 하나를 익히면 나머지는 쉽게 사용할 수 있다. 
이 장의 나머지 부분에서는 `read_csv()` 에 초점을 맞출 것이다. CSV 파일은 가장 
일반적인 형태의 데이터 저장 형태일 뿐 아니라 `read_csv()` 를 이해하면 readr 의 
다른 모든 함수에 쉽게 적용할 수 있다.

`read_csv()` 의 첫 번째 인수가 가장 중요한데 바로 읽으려고 하는 파일의 경로다.


```r
heights <- read_csv("data/heights.csv")
#> Rows: 1192 Columns: 6
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> chr (2): sex, race
#> dbl (4): earn, height, ed, age
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

`read_csv()` 를 실행하면 각 열의 이름과 유형을 제공하는 열 사양이 화면 출력된다. 이는 readr 에서 중요한 부분이다. [파일 파싱하기]에서 다시 살펴보겠다.

인라인 CSV 파일을 넣을 수도 있다. 이것은 readr 로 실험해볼 때와 다른 사람들과 
공유할 재현 가능한 예제를 만들 때 유용하다.


```r
read_csv("a,b,c
1,2,3
4,5,6")
#> Rows: 2 Columns: 3
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> dbl (3): a, b, c
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
#> # A tibble: 2 x 3
#>       a     b     c
#>   <dbl> <dbl> <dbl>
#> 1     1     2     3
#> 2     4     5     6
```

두 경우 모두 `read_csv()` 는 데이터의 첫 번째 줄을 열 이름으로 사용한다. 이는 매우 일반적인 규칙이다. 이 동작을 조정해야 하는 경우는 두 가지이다.

1.  파일 앞 부분에 메타 데이터 몇 줄이 있는 경우가 있다. `skip = n` 을 사용하여
첫 `n` 줄을 건너 뛸 수 있다. 또는 `comment = "#"` 을 사용하여 `#` 으로 시작하는
모든 줄을 무시할 수 있다.

    
    ```r
    read_csv("메타 데이터 첫번째 행
      메타 데이터 두번째 행
      x,y,z
      1,2,3", skip = 2)
    #> Rows: 1 Columns: 3
    #> -- Column specification --------------------------------------------------------
    #> Delimiter: ","
    #> dbl (3): x, y, z
    #> 
    #> i Use `spec()` to retrieve the full column specification for this data.
    #> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
    #> # A tibble: 1 x 3
    #>       x     y     z
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    
    read_csv("# 건너뛰고 싶은 주석
      x,y,z
      1,2,3", comment = "#")
    #> Rows: 1 Columns: 3
    #> -- Column specification --------------------------------------------------------
    #> Delimiter: ","
    #> dbl (3): x, y, z
    #> 
    #> i Use `spec()` to retrieve the full column specification for this data.
    #> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
    #> # A tibble: 1 x 3
    #>       x     y     z
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    ```
    
1.  데이터에 열 이름이 없을 수 있다. `col_names = FALSE` 를 사용하면 `read_csv()` 가 
첫 행을 헤드로 취급하지 않고 대신 X1에서 Xn까지 순차적으로 이름을 붙인다.

    
    ```r
    read_csv("1,2,3\n4,5,6", col_names = FALSE)
    #> Rows: 2 Columns: 3
    #> -- Column specification --------------------------------------------------------
    #> Delimiter: ","
    #> dbl (3): X1, X2, X3
    #> 
    #> i Use `spec()` to retrieve the full column specification for this data.
    #> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
    #> # A tibble: 2 x 3
    #>      X1    X2    X3
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    #> 2     4     5     6
    ```

(`"\n"` 은 새 줄을 추가하는 편리한 단축키이다. [문자열 기초](#strings-basics)에서 이
단축어와 문자열 이스케이프의 다른 유형에 대해 자세히 배운다.)
다른 방법으로는 `col_names` 에 열 이름으로 사용할 문자형 벡터를 전달할
수도 있다.

    
    ```r
    read_csv("1,2,3\n4,5,6", col_names = c("x", "y", "z"))
    #> Rows: 2 Columns: 3
    #> -- Column specification --------------------------------------------------------
    #> Delimiter: ","
    #> dbl (3): x, y, z
    #> 
    #> i Use `spec()` to retrieve the full column specification for this data.
    #> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
    #> # A tibble: 2 x 3
    #>       x     y     z
    #>   <dbl> <dbl> <dbl>
    #> 1     1     2     3
    #> 2     4     5     6
    ```

일반적으로 조정이 필요한 또 다른 옵션은 `na` 이다. 파일에서 결측값을 나타내는데 사용되는 값(들)을 지정한다.


```r
read_csv("a,b,c\n1,2,.", na = ".")
#> Rows: 1 Columns: 3
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> dbl (2): a, b
#> lgl (1): c
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
#> # A tibble: 1 x 3
#>       a     b c    
#>   <dbl> <dbl> <lgl>
#> 1     1     2 NA
```

여기까지 배운 것들로 실제로 마주하게 될 CSV 파일의 75% 정도를 불러올 수
있다. 또한 탭으로 구분된 파일을 `read_tsv()` 를 사용하여, 혹은 고정간격 파일을
`read_fwf()` 를 사용하여 불러오는 데도 쉽게 적용할 수 있다. 더 복잡한 파일을 읽으려면 
readr 이 각 열을 파싱하여 R 벡터로 바꾸는 방법에 대해 자세히 배워야한다.

### 베이스 R과 비교

R을 이전에 사용한 사람은 우리가 `read.csv()` 를 사용하지 않는 이유가 궁금할 것이다. 베이스 함수보다 readr 함수가 좋은 이유는 다음과 같다.

* 일반적으로 베이스 함수보다 훨씬 더(~10배) 빠르다. 오래 걸리는 작업은 진
행 표시줄을 통해 상황을 알 수 있다. raw speed로 작업하려면
`data.table::fread()` 를 사용해보라. 이 함수는 tidyverse 에 잘 어울리지는 않지만, 훨씬 더 빠를 수 있다.

* 티블을 생성한다. 문자 벡터를 팩터형으로 변환하지도, 행 이름을 사용하거
나 열 이름을 변경하지도 않는다. 베이스 R 함수는 변환, 변경하기 때문에 불
편하다.

* 좀 더 재현 가능하다. 베이스 R 함수는 운영체제 및 환경 변수의 일부 동작을
상속하므로 자신의 컴퓨터에서 작동하는 불러오기 코드가 다른 사람의 컴퓨터에서 
작동하지 않을 수 있다.


### 연습문제

1.  필드가 “|” 로 분리된 파일을 읽으려면 어떤 함수를 사용하겠는가?

1.  `read_csv()` 와 `read_tsv()` 가 공통으로 가진 인수는 `file`, `skip`, `comment` 외에 또 무엇이 있는가?

1. `read_fwf()` 에서 가장 중요한 인수는 무엇인가?


1.  CSV 파일의 문자열에 쉼표가 포함되는 경우가 있다. 그것들이 문제를 일으
키지 않게 하려면 `"` 혹은 `'`와 같은 인용 문자로 둘러싸일 필요가 있다. 
`read_csv()` 는 인용 문자가 `"`라고 가정한다. 이를 변경하려면 `read_delim()` 을 대신
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

readr 이 디스크에서 파일을 읽는 방법에 대해 깊이 알아보기 전에, 잠깐 벗어나서 
`parse_*()` 함수에 대해 살펴볼 필요가 있다. 이 함수들은 문자형 벡터를 입력으로 
하여 논리형, 정수형 또는 데이트형과 같은 좀 더 특수화된 벡터를 반환한다.


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
#>   3  -- no trailing characters abc   
#>   4  -- no trailing characters 123.45
```

이런 경우에는 출력에서 누락될 것이다.


```r
x
#> [1] 123 345  NA  NA
#> attr(,"problems")
#> # A tibble: 2 x 4
#>     row   col expected               actual
#>   <int> <int> <chr>                  <chr> 
#> 1     3    NA no trailing characters abc   
#> 2     4    NA no trailing characters 123.45
```

파싱에 실패한 경우가 많으면 `problems()` 를 사용하여 실패 전체를 가져와야 한다. `problems()` 이 반환한 티블을 dplyr로 작업할 수 있다.


```r
problems(x)
#> # A tibble: 2 x 4
#>     row   col expected               actual
#>   <int> <int> <chr>                  <chr> 
#> 1     3    NA no trailing characters abc   
#> 2     4    NA no trailing characters 123.45
```

파서를 잘 활용하려면 어떤 종류가 있는지, 각종 입력 유형을 어떻게 다루는지
를 잘 이해해야 한다. 특별히 중요한 8개의 파서가 있다.

1.  `parse_logical()` 과 `parse_integer()` 는 각각 논리형 및 정수형을 파싱한다. 기본적으로 이 파서에 잘못될 수 있는 것은 없으므로 여기서 더 설명하지 않겠다.
    
1.  `parse_double()` 은 엄격한 수치형 파서이고, `parse_number()` 는 유연한 수치형 파서이다. 이들은 예상보다 더 복잡하다. 왜냐하면 세계 여러 지역이 각자 다른 방식으로 숫자를 쓰기 때문이다.

1.  `parse_character()` 는 너무 단순해서 필요 없을 것 같다고 생각할지도 모른다.
그러나 어떤 복잡성 때문에 이 파서가 매우 중요하다. 바로 문자 인코딩이 그것이다.

1.  `parse_factor()` 는 팩터형을 생성하는데, 팩터형은 R이 미리 정해지고 알려진
값으로 범주형 변수를 나타내기 위해 사용하는 데이터 구조이다.

1.  `parse_datetime()`, `parse_date()`, `parse_time()` 을 사용하면 다양한 날짜와 시간
데이터를 파싱할 수 있다. 날짜를 쓰는 방법은 다양하기 때문에 이 함수들이
가장 복잡하다. 다음 절들에서 더 자세히 살펴보기로 하자.

### 숫자

숫자 파싱하는 것은 간단한 것처럼 보이지만, 까다로운 세 가지 문제가 있다.

1. 세계 여러 지역에서 사람들은 숫자를 다르게 쓴다. 예를 들어 어떤 국가에서는 실수의 정수 부분과 소수 부분 사이에 `.` 를 쓰고 다른 국가에서는 `,` 를 쓴다.

1. 숫자는 ‘$1000’, ‘10%’ 와 같이 단위를 나타내는 다른 문자가 붙어있을 때가 많다.

1. 숫자는 ‘1,000,000’ 과 같이 쉽게 읽을 수 있도록 ‘그룹화’ 문자가 포함되는 경우가 
많다. 이러한 그룹화 문자는 국가마다 다르다.

첫 번째 문제를 해결하기 위해서 readr 은 지역에 따라 파싱 옵션을 지정하는 객체인 ‘로캘(locale)’이라는 개념을 사용한다. 숫자를 파싱할 때 가장 중요한 옵션은 
소수점으로 사용하는 문자이다. 새로운 로캘을 생성하고 `decimal_mark` 인수를
설정하여 기본값인 `.` 를 다른 값으로 재정의할 수 있다.


```r
parse_double("1.23")
#> [1] 1.23
parse_double("1,23", locale = locale(decimal_mark = ","))
#> [1] 1.23
```

readr 의 기본 로캘은 미국 중심인데, 왜냐하면 일반적으로 R 은 미국 중심이기 때문이다 
(즉, 베이스 R의 문서가 미국식 영어로 작성되었다). 다른 방법은 운영체제의 
기본값을 추측하여 시도하는 것이다. 이러한 방법은 잘 동작하지 않고, 더
중요한 것은 코드가 취약하게 된다. 자신의 컴퓨터에서 동작하더라도 코드를 다른 
국가의 동료에게 이메일로 보낼 때 오류가 발생할 수 있다.

두 번째 문제를 처리하는 `parse_number()` 는 숫자 앞뒤의 숫자가 아닌 문자 (non-numeric
character) 를 무시한다. 통화 및 백분율에 특히 유용하지만, 텍스트에 포함
된 숫자를 추출하는 데도 효과적이다.


```r
parse_number("$100")
#> [1] 100
parse_number("20%")
#> [1] 20
parse_number("It cost $123.45")
#> [1] 123
```

마지막 문제는 `parse_number()` 와 로캘을 조합하여 `parse_number()` 가 ‘그룹화 마크’를 무시하도록 함으로써 해결할 수 있다.


```r
# 미주 방식
parse_number("$123,456,789")
#> [1] 1.23e+08

# 유럽의 많은 국가 방식
parse_number("123.456.789", locale = locale(grouping_mark = "."))
#> [1] 1.23e+08

# 스위스 방식
parse_number("123'456'789", locale = locale(grouping_mark = "'"))
#> [1] 1.23e+08
```

### 문자열 {#readr-strings}

`parse_character()` 는 정말 단순해 보인다. 입력을 단순히 반환하는 것 아닌가. 
그런데 불행하게도 삶은 그렇게 호락호락하지 않다. 같은 문자열을 나타내는 
방법은 여러 가지이다. 이게 무슨 이야기인지 이해하려면 컴퓨터가 문자열을 
표시하는 방법에 대해 깊고 상세하게 들어가야 한다. R에서는 `charToRaw()` 를
사용하여 문자열의 기본 표현을 볼 수 있다.


```r
charToRaw("Hadley")
#> [1] 48 61 64 6c 65 79
```

각 16진수값은 정보 바이트를 나타낸다. 예를 들면 `48` 은 H를 나타내고, `61` 은 a
를 나타낸다. 16진수 수치를 문자로 매핑하는 것을 인코딩이라고 하며, 앞의
인코딩은 ASCII 라고 한다. ASCII 는 정보 교환을 위한 미국 표준 코드
(American Standard Code for Information Interchange)의 줄임말이며 따라서
영문자를 잘 표현한다.

영어가 아닌 다른 언어의 경우 더욱 복잡해진다. 컴퓨터 시대 초창기에는 비영어권 
문자 인코딩을 위한 여러 표준 규격이 있었다. 문자열을 정확하게 해석하기 위해서는 
값과 인코딩을 모두 알아야했다. 예를 들어 두 가지 일반적인 인코딩은 
Latin1 (ISO-8859-1, 서유럽 언어들에서 사용)과 Latin2 (ISO-8859-2, 동유럽 언어들에서 사용)이다. 
Latin1에서 바이트 `b1` 은 ‘±’이지만, Latin2 에서는 ‘ą’이다! 
다행히 오늘날에는 거의 모든 곳에서 지원되는 하나의 표준인 UTF-8 이 있다. 
UTF-8 은 오늘날 인간이 사용하는 거의 모든 문자와 기타 기호들(예: 이모티콘)을 
인코딩할 수 있다.

**readr** 은 모든 곳에서 UTF-8 을 사용한다. 데이터를 읽을 때 UTF-8 이라고 가정하며, 
쓸 때는 항상 사용한다. UTF-8 은 좋은 기본값이지만, 이를 인식하지 못하는 
구형 시스템에서 생성된 데이터에 사용할 수 없다. 이런 상황이면 문자열을
화면 출력할 때 이상하게 보인다. 한두 개의 문자만 엉망이 될 수도 있고, 
완전히 외계어들을 볼 수도 있다. 다음의 예를 보자.



```r
x1 <- "El Ni\xf1o was particularly bad this year"
x2 <- "\x82\xb1\x82\xf1\x82\xc9\x82\xbf\x82\xcd"

x1
#> [1] "El Ni\xf1o was particularly bad this year"
x2
#> [1] "궞귪궸궭궼"
```

문제를 해결하려면 `parse_character()` 에서 인코딩을 지정해야 한다.


```r
parse_character(x1, locale = locale(encoding = "Latin1"))
#> [1] "El Nino was particularly bad this year"
parse_character(x2, locale = locale(encoding = "Shift-JIS"))
#> [1] "こんにちは"
```

올바른 인코딩을 어떻게 찾을 수 있을까? 운이 좋다면 데이터 문서의 어딘가
에 포함되었을 것이다. 하지만 불행하게도 그런 경우는 거의 없으므로, readr 는
`guess_encoding()` 을 제공하여 사용자가 알아낼 수 있도록 도와준다. 이것은 완벽하지도 않고, (앞의 사례와 달리) 텍스트가 많아야 더 잘 작동하지만, 한번 시도해볼 만한 방법이다. 올바른 인코딩을 찾기 전에 몇 가지 다른 인코딩을 시도해보라.


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

`guess_encoding()` 의 첫 번째 인수로는 파일의 경로 혹은, 이 예제와 같이 원시 벡터 (문자열이 이미 R에 있는 경우 유용함)가 될 수 있다.

인코딩은 방대하고 복잡한 주제이며, 여기서 우리는 단지 겉핥기만 한 것이다. 더 배우고 싶으면 <http://kunststube.net/encoding> 에서 자세한 설명을 읽어보길
추천한다.

### 팩터형 {#readr-factors}

R 은 팩터형을 사용하여, 가질 수 있는 값을 미리 알고 있는 범주형 변수를 나타낸다. 
예상치 못한 값이 있을 때마다 경고를 생성하려면 `parse_factor()` 에 가질
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
남겨두고 [문자열](#strings)과 [팩터형](#factors)에서 배울 도구를 사용하여 정리하는 것이 쉬울 때가 많다.

### 데이트형, 데이트-타임형, 타임형 {#readr-datetimes}

원하는 것이 date (1970-01-01 이후의 일 수), date-time (1970-01-01 자정 이후의
초 수), time (자정 이후의 초 수)인지에 따라 세 가지 파서 중에서 선택하면 된다. 추가 인수 없는 각 파서의 동작은 다음과 같다.

*   `parse_datetime()` 은 ISO 8601 date-time 을 입력으로 한다. ISO
    8601 은 국제 표준인데 날짜가 가장 큰 것부터 가장 작은 것(즉, 년, 월,     일, 시, 분, 초)으로 구성된다.

    
    ```r
    parse_datetime("2010-10-01T2010")
    #> [1] "2010-10-01 20:10:00 UTC"
    # 시간이 생략된 경우엔 자정으로 설정됨
    parse_datetime("20101010")
    #> [1] "2010-10-10 UTC"
    ```
    
    ISO 8601은 가장 중요한 날짜/시간 표준이며, 날짜와 시간을 자주 다루는     경우 <https://en.wikipedia.org/wiki/ISO_8601> 을 읽어볼 것을             추천한다.

*   `parse_date()` 는 네 자리 연도, `-` 또는 `/`, 월, `-` 또는 `/`,          날짜를 입력으로 한다.
    
    
    ```r
    parse_date("2010-10-01")
    #> [1] "2010-10-01"
    ```

*   `parse_time()` 은 시, `:`, 분 그리고 선택적으로 `:`, 초, 선택적          a.m./p.m 표시를 입력으로 한다.

    
    ```r
    library(hms)
    parse_time("01:10 am")
    #> 01:10:00
    parse_time("20:10:01")
    #> 20:10:01
    ```
    
    베이스 R에는 시간 데이터를 위한 좋은 내장 클래스가 없기 때문에,          우리는 hms 패키지에서 제공되는 클래스를 사용한다.

이러한 기본 설정으로 주어진 데이터를 처리하지 못한다면 다음의 요소들로 이루어진 자신만의 날짜-시간 형식(`format`)을 만들어 쓸 수 있다.

Year
: `%Y` (4 자리). 
: `%y` (2 자리); 00-69 -> 2000-2069, 70-99 -> 1970-1999.

Month
: `%m` (2 자리).
: `%b` ("Jan"과 같이 축약된 명칭).
: `%B` (전체 명칭, "January").

Day
: `%d` (2 자리).
: `%e` (선택적 선행 공백).

Time
: `%H` 0-23 시간.
: `%I` 0-12, `%p`와 함께 사용해야 함.
: `%p` AM/PM 구분자.
: `%M` 분.
: `%S` 정수 초.
: `%OS` 실수 초. 
: `%Z` 시간대 (이름, 예 `America/Chicago`). 참고: 줄임말에 주의하라. ‘EST’는 일광 절약 시간제가 없는 
캐나다 표준 시간대임을 주의하라. 그것은 동부 표준시가 아니다! [시간대](#time-zones)에서 이를 다시 살펴보겠다.
: `%z` (UTC 와의 오프셋, 예: `+0800`). 

숫자가 아닌 문자
: `%.` 숫자가 아닌 문자 하나를 건너뛴다.
: `%*` 숫자가 아닌 문자 모두를 건너뛴다.

올바른 포맷을 파악하는 가장 좋은 방법은 문자형 벡터로 몇 가지 예제를 만들고, 
파싱함수 중 하나로 테스트하는 것이다.


```r
parse_date("01/02/15", "%m/%d/%y")
#> [1] "2015-01-02"
parse_date("01/02/15", "%d/%m/%y")
#> [1] "2015-02-01"
parse_date("01/02/15", "%y/%m/%d")
#> [1] "2001-02-15"
```

비영어권의 월 이름에 `%b` 또는 `%B` 를 사용하는 경우, `locale()`의 `lang` 인수를 설정해야 한다. 
`date_names_langs()`에 내장된 언어 목록을 보라. 자신의 언어가 아직
포함되어 있지 않았으면 `date_names()` 를 사용하여 생성하라.


```r
parse_date("1 janvier 2015", "%d %B %Y", locale = locale("fr"))
#> [1] "2015-01-01"
```

### 연습문제

1.  `locale()` 에서 가장 중요한 인수들은 무엇인가? 

1.  `decimal_mark` 와 `grouping_mark` 를 동일 문자로 설정하려고 하면 어떻게 되는가? 
`decimal_mark` 를 ‘,’로 설정하면 `grouping_mark` 의 기본값은 어떻게 되는가?

1.  `locale()`의 `date_format` 및 `time_format` 옵션에 대해서는 논의하지 않았다. 이
들이 하는 일은 무엇인가? 이들이 유용할 수 있는 경우를 보여주는 예제를
작성해보라.

1.  가장 많이 읽는 파일 형식에 대한 설정을 압축한 새로운 로캘 객체를 만들어
보라.

1.  `read_csv()` 와 `read_csv2()` 은 무엇이 다른가?
    
1.  유럽에서 사용되는 가장 일반적인 인코딩은 무엇인가? 아시아에서 가장 많이 사용되는 인코딩은 무엇인가? 구글 검색해서 알아보라.

1.  올바른 형식 문자열을 생성하여 다음 날짜와 시간을 파싱하라.
    
    
    ```r
    d1 <- "January 1, 2010"
    d2 <- "2015-Mar-07"
    d3 <- "06-Jun-2017"
    d4 <- c("August 19 (2015)", "July 1 (2015)")
    d5 <- "12/30/14" # Dec 30, 2014
    t1 <- "1705"
    t2 <- "11:15:10.12 PM"
    ```

## 파일 파싱하기

이제까지 개별 벡터를 파싱하는 방법을 배웠으므로, 처음으로 돌아가서 readr이
파일을 파싱하는 방법을 알아볼 차례이다. 이 절에서는 다음의 두 방법을 배운다.

1. readr이 각 열의 유형을 자동으로 추측하는 방법.
1. 기본 사양을 재정의하는 방법.

### 전략

readr 은 휴리스틱 방법을 사용하여 각 열의 유형을 파악한다. 첫 번째 1000행을
읽고 (적절히 보수적인) 휴리스틱 방법을 사용하여 각 열의 유형을 찾는다.
`guess_parser()`(readr의 추정을 반환)와 `parse_guess()` (앞의 추정을 사용하여
열을 파싱)를 사용하여 문자형 벡터에 이 과정을 재현해볼 수 있다.


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

이 휴리스틱 방법은 다음 유형들을 각각 시도하여 일치하는 항목을 찾으면 멈춘다.

* 논리형: "F", "T", "FALSE", "TRUE"만 포함.
* 정수형: 수치형 문자(와 `-`)만 포함.
* 더블형: (`4.5e-5`와 같은 숫자를 포함하는) 유효한 더블형만 포함.
* 수치형: 내부에 그룹화 마크가 있는 유효한 더블형을 포함.
* 타임형: `time_format`의 기본형식과 일치.
* 데이트형: `date_format`의 기본형식과 일치.
* 데이트-시간형: ISO8601 날짜.

이러한 규칙 중 어느 것도 적용되지 않으면 해당 열은 문자열 벡터로 그대로 남는다.

### 문제점

큰 파일의 경우 이러한 기본값이 항상 잘 작동하지는 않는다. 두 가지 문제가
있다.

1. 처음 1,000 행이 특수한 경우이어서 readr 이 충분히 일반적이지 않은 유형으로 
추측할 수 있다. 예를 들어 첫 번째 1,000개의 행에 정수만 있는 더블형 열이 있을 수 있다.

1. 열에 결측값이 많이 있을 수 있다. 첫 번째 1,000 개의 행에 `NA` 만 있는 경우
readr 이 문자형 벡터로 추측했지만, 여러분은 좀 더 구체적으로 파싱하고 싶을 수 있다.

readr 에는 이러한 두 가지 문제를 모두 보여주는 까다로운 CSV 가 포함되어 있다.



```r
challenge <- read_csv(readr_example("challenge.csv"))
#> Rows: 2000 Columns: 2
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> dbl  (1): x
#> date (1): y
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

(패키지에 포함된 파일의 경로를 찾아 주는 `readr_example()` 을 사용한 것에 주목하라.)
두 가지가 출력되었다. 첫 번째 1,000 개의 행을 보고 생성된 열 상세 내용과
첫 다섯 개의 파싱 오류가 그것이다. 발생한 문제들을 'problems()' 로 명시적으로 추출하여 더
깊이 탐색하는 것은 좋은 방법이다.


```r
problems(challenge)
#> # A tibble: 0 x 5
#> # ... with 5 variables: row <int>, col <int>, expected <chr>, actual <chr>,
#> #   file <chr>
```

문제가 남아있지 않을 때까지 열 단위로 작업하는 것은 좋은 전략이다. `x` 열에
파싱 문제가 많다는 것을 알 수 있다. 정수값 다음에 따라오는 문자가 있었던 것이다. 이는 더블형 파서를 사용해야 함을 암시한다.

이 호출을 수정하기 위해 먼저 열 사양을 복사하여 원래 호출에 붙여 넣어보라.



```r
challenge <- read_csv(
  readr_example("challenge.csv"), 
  col_types = cols(
    x = col_integer(),
    y = col_character()
  )
)
```

그런 다음 `x` 열의 유형을 조정할 수 있다.


```r
challenge <- read_csv(
  readr_example("challenge.csv"), 
  col_types = cols(
    x = col_double(),
    y = col_character()
  )
)
```

첫 번째 문제는 해결되었지만, 마지막 몇 행을 보면 날짜가 문자형 벡터로 저장되었다.


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

`y` 열을 데이트형으로 설정하여 이를 수정할 수 있다.


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

모든 `parse_xyz()` 함수는 해당하는 `col_xyz()` 함수를 가지고 있다. 데이터가 이미
R 의 문자형 벡터인 경우에는 `parse_xyz()` 를 사용하면 되고, readr 이 데이터를 불러오는 방법을 설정할 경우에는 `col_xyz()` 를 사용하면 된다.

`col_types` 를 항상 설정하여 readr 이 생성하는 출력물로부터 만들어 나가는 것을 강력히 추천한다. 이렇게 하면 일관되고 재현할 수 있는 데이터 불러오기 스크립트를 갖게 된다. 기본값으로 추측하여 데이터를 읽는다면 데이터 변경 시 readr 은 과거 설정으로 읽게 될 것이다. 정말로 엄격하게 하고 싶다면 `stop_for_problems()` 를 사용하라. 파싱 문제가 생기면 오류를 내며 스크립트를 중단할 것이다.

### 기타 전략

파일을 파싱하는 데 도움이 되는 몇 가지 다른 일반적인 전략이 있다.

*   앞의 예제에서 우리는 단지 운이 없었다. 즉, 기본값보다 한 행만 더 살펴보면 한 번에 정확하게 파싱할 수 있다.

    
    ```r
    challenge2 <- read_csv(readr_example("challenge.csv"), guess_max = 1001)
    #> Rows: 2000 Columns: 2
    #> -- Column specification --------------------------------------------------------
    #> Delimiter: ","
    #> dbl  (1): x
    #> date (1): y
    #> 
    #> i Use `spec()` to retrieve the full column specification for this data.
    #> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
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

*   모든 열을 문자형 벡터로 읽으면 문제를 쉽게 진단할 수 있는 경우가 많다.

    
    ```r
    challenge2 <- read_csv(readr_example("challenge.csv"), 
      col_types = cols(.default = col_character())
    )
    ```
    
    이 방법은 `type_convert()` 와 함께 사용하면 특히 유용한데, 이 함수는 휴리스틱한 파싱 방법을 데이터프레임의 문자형 열에 적용한다.

    
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
    
    # 열 유형을 주의
    type_convert(df)
    #> 
    #> -- Column specification --------------------------------------------------------
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
    
*   매우 큰 파일을 읽는 경우, `n_max` 를 10,000 또는 100,000 과 같이 작은 숫자로 설정할 수 있다. 이렇게 하면 일반적인 문제를 해결하는 동시에 반복작업을
가속화할 수 있다.

*  파싱에 중대한 문제가 있는 경우에는 `read_lines()` 을 이용하여 라인으로 이루어진 문자형 벡터로 읽거나 `read_file()` 을 이용하여 길이가 1인 문자형 벡터로 읽는 것이 더 쉬울 수 있다. 그런 다음 나중에 배울 문자열 파싱 방법을 사용하여 좀 더 특이한 포맷을 파싱하면 된다.

## 파일에 쓰기

readr에는 디스크에 데이터를 다시 기록하는 데 유용한 함수, `write_csv()` 와 `write_tsv()` 가 있다. 두 함수 모두 다음 동작을 통해 출력 파일이 올바르게 다시 읽힐 수 있게 한다.

*   항상 UTF-8로 문자열을 인코딩한다.
*   날짜와 날짜-시간을 ISO 8601 형식으로 저장하여 어디에서든 쉽게 파싱될 수 있게 한다.

CSV 파일을 엑셀로 내보내려면 `write_excel_csv()` 를 사용하라. 이는 파일의 시작 부분에 특수 문자(‘byte order mark’)를 작성하여, UTF-8 인코딩을 사용하고 있음을 엑셀에 전달한다.

가장 중요한 인수는 `x` (저장할 데이터프레임)와 `path` (그 데이터프레임을 저장할 위치)이다. 결측값을 지정하는 인수, `na` 와 기존 파일에 첨부할지를 지정하는 인수 `append` 도 있다.


```r
write_csv(challenge, "challenge.csv")
```

CSV 로 저장하면 유형 정보가 없어진다는 것에 유의하라.


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
#> Rows: 2000 Columns: 2
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> dbl  (1): x
#> date (1): y
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
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

이런 이유로 중간 결과를 캐싱하기에 CSV 를 아주 신뢰할 수 없다. 불러올 때마다 열 사양을 다시 만들어야 한다. 두 가지 대안이 있다.

1.  `write_rds()` 과 `read_rds()` 는 베이스 함수인 `readRDS()` 와 `saveRDS()` 의 래퍼 함수들이다. 이들은 RDS 라는 R 의 커스텀 바이너리 형식으로 데이터를 저장한다.

    
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
  
1.  feather 패키지는 다른 프로그래밍 언어와 공유할 수 있는 빠른 바이너리 파일 형식을 구현한다.
    
    
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

    feather 는 RDS 보다 대체적으로 빠르며 R 외부에서도 사용할 수 있다. RDS 는 리스트-열([many models]에서 배울 것이다)을 지원하지만 feather 는 현재 지원하지 않는다.



## 기타 데이터 유형

다른 유형의 데이터를 R로 가져오려면 다음에 나열된 tidyverse 패키지로 시작
하는 것이 좋다. 이 패키지들은 완벽하지는 않지만 이들부터 시작하면 좋다. 직사각형 데이터에 대해 다음 패키지들이 있다.

* haven 은 SPSS, Stata, SAS 파일을 읽을 수 있다.

* readxl 은 엑셀 파일(`.xls` 와 `.xlsx`)을 읽을 수 있다.

* DBI 를 데이터베이스 특화 백엔드(예: **RMySQL**, **RSQLite**, **RPostgreSQL** 등)와 함께 사용하면 데이터베이스에 대해 SQL 쿼리를 실행하고 데이터프레임을
반환할 수 있다.

계층적 데이터의 경우, JSON 에는 Jeroen Ooms 가 개발한 **jsonlite**를
사용하고 XML에는 **xml2** 를 사용하면 된다. 이에 관한 좋은 예제는 Jenny Bryan 의 <https://jennybc.github.io/purrr-tutorial> 에서 볼 수 있다.

다른 파일 유형의 경우, [R 데이터 가져오기/내보내기 매뉴얼](https://cran.r-project.org/doc/manuals/r-release/R-data.html) 과
[__rio__](https://github.com/leeper/rio) 를 참고해보라.

