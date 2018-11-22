
# Model building

## Introduction

In the previous chapter you learned how linear models work, and learned some basic tools for understanding what a model is telling you about your data. The previous chapter focussed on simulated datasets. This chapter will focus on real data, showing you how you can progressively build up a model to aid your understanding of the data.

We will take advantage of the fact that you can think about a model partitioning your data into pattern and residuals. We'll find patterns with visualisation, then make them concrete and precise with a model. We'll then repeat the process, but replace the old response variable with the residuals from the model. The goal is to transition from implicit knowledge in the data and your head to explicit knowledge in a quantitative model. This makes it easier to apply to new domains, and easier for others to use. 

For very large and complex datasets this will be a lot of work. There are certainly alternative approaches - a more machine learning approach is simply to focus on the predictive ability of the model. These approaches tend to produce black boxes: the model does a really good job at generating predictions, but you don't know why. This is a totally reasonable approach, but it does make it hard to apply your real world knowledge to the model. That, in turn, makes it difficult to assess whether or not the model will continue to work in the long-term, as fundamentals change. For most real models, I'd expect you to use some combination of this approach and a more classic automated approach.

It's a challenge to know when to stop. You need to figure out when your model is good enough, and when additional investment is unlikely to pay off. I particularly like this quote from reddit user Broseidon241: 

> A long time ago in art class, my teacher told me "An artist needs to know 
> when a piece is done. You can't tweak something into perfection - wrap it up. 
> If you don't like it, do it over again. Otherwise begin something new". Later
> in life, I heard "A poor seamstress makes many mistakes. A good seamstress 
> works hard to correct those mistakes. A great seamstress isn't afraid to 
> throw out the garment and start over."
> 
> -- Broseidon241, <https://www.reddit.com/r/datascience/comments/4irajq>

### Prerequisites

We'll use the same tools as in the previous chapter, but add in some real datasets: `diamonds` from ggplot2, and `flights` from nycflights13.  We'll also need lubridate in order to work with the date/times in `flights`.


```r
library(tidyverse)
library(modelr)
options(na.action = na.warn)

library(nycflights13)
library(lubridate)
```

## Why are low quality diamonds more expensive? {#diamond-prices}

In previous chapters we've seen a surprising relationship between the quality of diamonds and their price: low quality diamonds (poor cuts, bad colours, and inferior clarity) have higher prices.


```r
ggplot(diamonds, aes(cut, price)) + geom_boxplot()
ggplot(diamonds, aes(color, price)) + geom_boxplot()
ggplot(diamonds, aes(clarity, price)) + geom_boxplot()
```

<img src="model-building_files/figure-html/unnamed-chunk-2-1.png" width="70%" style="display: block; margin: auto;" /><img src="model-building_files/figure-html/unnamed-chunk-2-2.png" width="70%" style="display: block; margin: auto;" /><img src="model-building_files/figure-html/unnamed-chunk-2-3.png" width="70%" style="display: block; margin: auto;" />

Note that the worst diamond color is J (slightly yellow), and the worst clarity is I1 (inclusions visible to the naked eye).

### Price and carat

It looks like lower quality diamonds have higher prices because there is an important confounding variable: the weight (`carat`) of the diamond. The weight of the diamond is the single most important factor for determining the price of the diamond, and lower quality diamonds tend to be larger.


```r
ggplot(diamonds, aes(carat, price)) + 
  geom_hex(bins = 50)
```

<img src="model-building_files/figure-html/unnamed-chunk-3-1.png" width="70%" style="display: block; margin: auto;" />

We can make it easier to see how the other attributes of a diamond affect its relative `price` by fitting a model to separate out the effect of `carat`. But first, lets make a couple of tweaks to the diamonds dataset to make it easier to work with:

1. Focus on diamonds smaller than 2.5 carats (99.7% of the data)
1. Log-transform the carat and price variables.


```r
diamonds2 <- diamonds %>% 
  filter(carat <= 2.5) %>% 
  mutate(lprice = log2(price), lcarat = log2(carat))
```

Together, these changes make it easier to see the relationship between `carat` and `price`:


```r
ggplot(diamonds2, aes(lcarat, lprice)) + 
  geom_hex(bins = 50)
```

<img src="model-building_files/figure-html/unnamed-chunk-5-1.png" width="70%" style="display: block; margin: auto;" />

The log-transformation is particularly useful here because it makes the pattern linear, and linear patterns are the easiest to work with. Let's take the next step and remove that strong linear pattern. We first make the pattern explicit by fitting a model:


```r
mod_diamond <- lm(lprice ~ lcarat, data = diamonds2)
```

Then we look at what the model tells us about the data. Note that I back transform the predictions, undoing the log transformation, so I can overlay the predictions on the raw data:


```r
grid <- diamonds2 %>% 
  data_grid(carat = seq_range(carat, 20)) %>% 
  mutate(lcarat = log2(carat)) %>% 
  add_predictions(mod_diamond, "lprice") %>% 
  mutate(price = 2 ^ lprice)

ggplot(diamonds2, aes(carat, price)) + 
  geom_hex(bins = 50) + 
  geom_line(data = grid, colour = "red", size = 1)
```

<img src="model-building_files/figure-html/unnamed-chunk-7-1.png" width="70%" style="display: block; margin: auto;" />

That tells us something interesting about our data. If we believe our model, then the large diamonds are much cheaper than expected. This is probably because no diamond in this dataset costs more than $19,000.

Now we can look at the residuals, which verifies that we've successfully removed the strong linear pattern:


```r
diamonds2 <- diamonds2 %>% 
  add_residuals(mod_diamond, "lresid")

ggplot(diamonds2, aes(lcarat, lresid)) + 
  geom_hex(bins = 50)
```

<img src="model-building_files/figure-html/unnamed-chunk-8-1.png" width="70%" style="display: block; margin: auto;" />

Importantly, we can now re-do our motivating plots using those residuals instead of `price`. 


```r
ggplot(diamonds2, aes(cut, lresid)) + geom_boxplot()
ggplot(diamonds2, aes(color, lresid)) + geom_boxplot()
ggplot(diamonds2, aes(clarity, lresid)) + geom_boxplot()
```

<img src="model-building_files/figure-html/unnamed-chunk-9-1.png" width="70%" style="display: block; margin: auto;" /><img src="model-building_files/figure-html/unnamed-chunk-9-2.png" width="70%" style="display: block; margin: auto;" /><img src="model-building_files/figure-html/unnamed-chunk-9-3.png" width="70%" style="display: block; margin: auto;" />

Now we see the relationship we expect: as the quality of the diamond increases, so too does its relative price. To interpret the `y` axis, we need to think about what the residuals are telling us, and what scale they are on. A residual of -1 indicates that `lprice` was 1 unit lower than a prediction based solely on its weight. $2^{-1}$ is 1/2, points with a value of -1 are half the expected price, and residuals with value 1 are twice the predicted price.

### A more complicated model

If we wanted to, we could continue to build up our model, moving the effects we've observed into the model to make them explicit. For example, we could include `color`, `cut`, and `clarity` into the model so that we also make explicit the effect of these three categorical variables:


```r
mod_diamond2 <- lm(lprice ~ lcarat + color + cut + clarity, data = diamonds2)
```

This model now includes four predictors, so it's getting harder to visualise. Fortunately, they're currently all independent which means that we can plot them individually in four plots. To make the process a little easier, we're going to use the `.model` argument to `data_grid`:









































