# 워크플로우: 프로젝트

어느날 여러분은 R 을 종료하고 다른 것을 하러 간 다음, 다음날 다시 분석하러 돌아올 것입니다.
어느날 여러분은 R 을 사용하는 여러 분석업무를 동시에 하고는 각기 저장하고 싶을 것입니다. 
어느날 당신은 R 을 바깥 세상에서 데이터를 불러와서 R 에서 수치결과와 그림을 다시 그 세상에 보낼 필요가 있을 것입니다. 
이러한 실제 상황들을 다루기 위해 당신은 두가지 결정을 할 필요가 있습니다.

1.  여러분 분석 중 어떤 것이 "리얼" 인가, 즉, what will you save as your lasting record of what happened?

2.  당신 분석이 "살고" 있는 곳은 어디인가?

## 무엇이 리얼인가?

처음 시작하는 R 우저는 환경(즉, environment 창에 열거된 객체들) 을 "리얼"이라고 고려해도 괜찮다.
하지만, 장기적으로는 R 스크립트를 "리얼"이라고 고려하면 훨씬 나을 것이다.

R 스크립트 (그리고 데이터 파일) 가 있으면 여러분은 환경을 다시 생성할 수 있다.
환경에서 R 스크립트를 다시 생성하는 것이 훨씬 어렵다!
메모리에 있는 다량의 코드를  (계속 실수를 해가면서) 다시 입력해야 하거나 R 히스토리를 조심히 마이닝 해야할 것이다.

To foster this behaviour, I highly recommend that you instruct RStudio not to preserve your workspace between sessions:

<img src="screenshots/rstudio-workspace.png" width="75%" style="display: block; margin: auto;" />

This will cause you some short-term pain, because now when you restart RStudio it will not remember the results of the code that you ran last time.
But this short-term pain will save you long-term agony because it forces you to capture all important interactions in your code.
There's nothing worse than discovering three months after the fact that you've only stored the results of an important calculation in your workspace, not the calculation itself in your code.

There is a great pair of keyboard shortcuts that will work together to make sure you've captured the important parts of your code in the editor:

1.  Press Cmd/Ctrl + Shift + F10 to restart RStudio.
2.  Press Cmd/Ctrl + Shift + S to rerun the current script.

I use this pattern hundreds of times a week.

## Where does your analysis live?

R has a powerful notion of the **working directory**.
This is where R looks for files that you ask it to load, and where it will put any files that you ask it to save.
RStudio shows your current working directory at the top of the console:

<img src="screenshots/rstudio-wd.png" width="50%" style="display: block; margin: auto;" />

And you can print this out in R code by running `getwd()`:


```r
getwd()
#> [1] "/Users/hadley/Documents/r4ds/r4ds"
```

As a beginning R user, it's OK to let your home directory, documents directory, or any other weird directory on your computer be R's working directory.
But you're six chapters into this book, and you're no longer a rank beginner.
Very soon now you should evolve to organising your analytical projects into directories and, when working on a project, setting R's working directory to the associated directory.

**I do not recommend it**, but you can also set the working directory from within R:


```r
setwd("/path/to/my/CoolProject")
```

But you should never do this because there's a better way; a way that also puts you on the path to managing your R work like an expert.

## Paths and directories

Paths and directories are a little complicated because there are two basic styles of paths: Mac/Linux and Windows.
There are three chief ways in which they differ:

1.  The most important difference is how you separate the components of the path.
    Mac and Linux uses slashes (e.g. `plots/diamonds.pdf`) and Windows uses backslashes (e.g. `plots\diamonds.pdf`).
    R can work with either type (no matter what platform you're currently using), but unfortunately, backslashes mean something special to R, and to get a single backslash in the path, you need to type two backslashes!
    That makes life frustrating, so I recommend always using the Linux/Mac style with forward slashes.

2.  Absolute paths (i.e. paths that point to the same place regardless of your working directory) look different.
    In Windows they start with a drive letter (e.g. `C:`) or two backslashes (e.g. `\\servername`) and in Mac/Linux they start with a slash "/" (e.g. `/users/hadley`).
    You should **never** use absolute paths in your scripts, because they hinder sharing: no one else will have exactly the same directory configuration as you.

3.  The last minor difference is the place that `~` points to.
    `~` is a convenient shortcut to your home directory.
    Windows doesn't really have the notion of a home directory, so it instead points to your documents directory.

## RStudio 프로젝트

R experts keep all the files associated with a project together --- input data, R scripts, analytical results, figures.
This is such a wise and common practice that RStudio has built-in support for this via **projects**.

Let's make a project for you to use while you're working through the rest of this book.
Click File \> New Project, then:

<img src="screenshots/rstudio-project-1.png" width="50%" style="display: block; margin: auto;" /><img src="screenshots/rstudio-project-2.png" width="50%" style="display: block; margin: auto;" /><img src="screenshots/rstudio-project-3.png" width="50%" style="display: block; margin: auto;" />

Call your project `r4ds` and think carefully about which *subdirectory* you put the project in.
If you don't store it somewhere sensible, it will be hard to find it in the future!

Once this process is complete, you'll get a new RStudio project just for this book.
Check that the "home" directory of your project is the current working directory:


```r
getwd()
#> [1] /Users/hadley/Documents/r4ds/r4ds
```

Whenever you refer to a file with a relative path it will look for it here.

Now enter the following commands in the script editor, and save the file, calling it "diamonds.R".
Next, run the complete script which will save a PDF and CSV file into your project directory.
Don't worry about the details, you'll learn them later in the book.


```r
library(tidyverse)

ggplot(diamonds, aes(carat, price)) + 
  geom_hex()
ggsave("diamonds.pdf")

write_csv(diamonds, "diamonds.csv")
```

Quit RStudio.
Inspect the folder associated with your project --- notice the `.Rproj` file.
Double-click that file to re-open the project.
Notice you get back to where you left off: it's the same working directory and command history, and all the files you were working on are still open.
Because you followed my instructions above, you will, however, have a completely fresh environment, guaranteeing that you're starting with a clean slate.

In your favorite OS-specific way, search your computer for `diamonds.pdf` and you will find the PDF (no surprise) but *also the script that created it* (`diamonds.R`).
This is a huge win!
One day you will want to remake a figure or just understand where it came from.
If you rigorously save figures to files **with R code** and never with the mouse or the clipboard, you will be able to reproduce old work with ease!

## Summary

In summary, RStudio projects give you a solid workflow that will serve you well in the future:

-   Create an RStudio project for each data analysis project.

-   Keep data files there; we'll talk about loading them into R in [data import].

-   Keep scripts there; edit them, run them in bits or as a whole.

-   Save your outputs (plots and cleaned data) there.

-   Only ever use relative paths, not absolute paths.

Everything you need is in one place, and cleanly separated from all the other projects that you are working on.
