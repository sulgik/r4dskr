# 워크플로우: 스크립트

지금까지는 코드를 실행하기 위해 콘솔을 사용해왔다. 처음 시작하기에는 콘솔에서 작업하는 것이 좋지만, 복잡한 ggplot2 그래프나 dplyr 파이프를 생성하면
콘솔 창이 금세 비좁아진다는 사실을 확인할 수 있다. 따라서 더 많은 작업 공간을 확보하기 위해서는 스크립트 에디터를 사용하는 것이 좋다. 파일 메뉴를 클릭하고 새 파일에서 R 스크립트를 선택하거나 키보드 단축키 Cmd/Ctrl + Shift + N 을 사용하여 스크립트 파일을 열어보자. 그러면 다음과 같이 네 개의 영역을
볼 수 있을 것이다.

<img src="diagrams/rstudio-editor.png" width="75%" style="display: block; margin: auto;" />

스크립트 에디터에는 작업 중인 코드를 두기에 좋다. 콘솔에서 실험하다가 원하는 작업을 오류 없이 수행하는 코드를 작성하게 되면 스크립트 에디터에 넣는다. RStudio 는 종료 시 에디터의 내용을 자동으로 저장하고, RStudio 를 다시 열 때 자동으로 불러온다. 하지만 그렇다 해도 스크립트는 정기적으로 저장하고 백업하는 것이 좋다.

## 코드 실행하기

스크립트 에디터는 복잡한 ggplot2 플롯이나 dplyr 작업의 긴 시퀀스를 생성하기에 좋다. 스크립트 에디터를 효율적으로 사용하기 위한 열쇠는 가장 중요한
단축키 중 하나인 Cmd/Ctrl + Enter 를 기억하는 것이다. 이 단축키는 콘솔에서
커서가 위치한 줄의 R 코드를 실행한다. 예를 들어 다음의 코드를 실행한다고
하자. 커서가 █ 에 있을 때 Cmd/Ctrl + Enter 를 누르면 `not_cancelled` 로 시작하는
완전한 명령문이 실행되며 커서가 (`not_cancelled %>%` 으로 시작하는) 다음 문장으로 이동한다. 이렇게 Cmd/Ctrl + Enter 를 반복적으로 입력하면 전체 스크립트를 쉽게 실행할 수 있다.


```r
library(dplyr)
library(nycflights13)
not_cancelled <- flights %>%
  filter(!is.na(dep_delay)█, !is.na(arr_delay))
not_cancelled %>%
  group_by(year, month, day) %>%
  summarise(mean = mean(dep_delay))
```

코드를 순차적으로 실행하는 대신 Cmd/Ctrl + Shift + S를 사용하여 전체 스크립트를 한 번에 실행할 수도 있다. 이 작업을 규칙적으로 수행하면 코드의 중요한
부분을 모두 입력하였는지 스크립트 내에서 확인할 수 있다.

스크립트에는 항상 필요한 패키지들을 먼저 작성하는 것을 추천한다. 그렇게 하면 다른 사람들과 코드를 공유할 때, 어떤 패키지를 설치해야 하는지 쉽게
알 수 있다. 그렇지만 공유하려는 스크립트에 `install.packages()` 또는 `setwd()` 를
절대 포함해서는 안 된다. 다른 사람의 컴퓨터 설정을 변경하게 하는 것은 옳지
않다.

이 책의 다음 장들을 실행할 때는 에디터에서 작업을 시작하고, 키보드 단축키 연습을 적극 권장한다. 시간이 지남에 따라 콘솔로 코드를 보내는 것에 익숙해져서 이전의 방식은 전혀 생각나지 않을 것이다.


## RStudio 진단

스크립트 에디터에서는 구불구불한 빨간색 선과 사이드바의 X자 표시로 구문
오류를 강조 표시한다:

<img src="screenshots/rstudio-diagnostic.png" width="129" style="display: block; margin: auto;" />

X자 위로 마우스를 가져가면 문제가 무엇인지 알 수 있다:

<img src="screenshots/rstudio-diagnostic-tip.png" width="202" style="display: block; margin: auto;" />

또한, RStudio 는 잠재적인 문제에 대해서도 알려준다.

<img src="screenshots/rstudio-diagnostic-warn.png" width="439" style="display: block; margin: auto;" />

## Exercises

1.  Go to the RStudio Tips Twitter account, <https://twitter.com/rstudiotips> and find one tip that looks interesting.
Practice using it!

2.  What other common mistakes will RStudio diagnostics report?
Read <https://support.rstudio.com/hc/en-us/articles/205753617-Code-Diagnostics> to find out.
