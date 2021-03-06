# (PART) 변환하기 {.unnumbered}

# 들어가기 {#data-types-intro}

이번 파트에서는 데이터프레임의 열이 포함할 수 있는 데이터유형들을 배우고 어떻게 변환하는지를 배웁니다. 
한 열에 적용하고 싶은 변환은 작업하고 있는 데이터 유형에 따라 달라지는데, 예를 들어, 문자열이 있다면 특정 부분을 추출하거나 제거하고 싶을 것이고, 수치형 데이터가 있다면 리스케일 하고 싶을 것입니다. 
앞 파트에서 이미 데이터 전처리하기에 대해 조금 배웠스빈다. 이제 현장에서 자주 만날 특정 데이터 우ㅠㅕㅇ에 대한 새로운 기술을 집중하여 알아봅니다. 

이 파트는 다음과 같이 진행됩니다:

-   \@ref(tibbles) 장에서는, 이 책에서 사용하는 데이터프레임의 변형인 **티블(tibble)** 에 대해 학습한다. 일반 데이터프레임과 어떻게 다른지를 배우고 "직접" 생성하는 법을 배울 것이다.
-   \@ref(relational-data) 장에서는 상호 연관된 데이터셋들로 작업하기 위한 도구를 배운다.

-   Chapter \@ref(missing-values)...

-   \@ref(strings) 장에서는 문자열 조작을 할 수 있는 강력한 도구인 정규표현식을 알아본다.

-   \@ref(factors) 장에서는 R 이 범주형 데이터를 저장하는 방법인 팩터형에 대해 소개한다. 이 방법은 변수가 가질 수 있는 값이 고정된 집합일 때, 또는 문자열에 대해 알파벳순이 아닌 순서를 사용하려는 경우에 사용할 수 있다.

-   \@ref(dates-and-times) 장에서는 데이트형과 데이터-타임형을 다루는 핵심 도구를 배울 것이다.

