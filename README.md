# Study-Visualization-Storytelling (R)

__[R]__
 - I. Explore One Variable
 - II. Explore Two Variable 
 - III. Explore Multi-Variable
 - IV. Wide vs Long
 
http://hci.stanford.edu/publications/2013/invisibleaudience/invisibleaudience.pdf

__Library:__ `ggplot2`

__Data:__ 'pseudo_facebook.tsv' 

__Story:__ People's perception of their audience on FB matches up to the reality? 
 - Well, FB believes who you think is in your audience affects how you present yourself. 
 - Who's actually seeing the content they are sharing?  
   - __Survey Q1.__ "How many do you think saw the post? 
     - There is a big mismatch b/w people's perceived audience size and their actual audience size.
     - People dramatically underestimate the size of their audience - (1/4). 

> Adjust a font size?
 - with the font size set to 20 ?
```
install.packages('ggthemes', dependencies = TRUE)
library(ggthemes)
theme_set(theme_minimal(20)) 

pf <- read.csv('C:/Users/Minkun/Desktop/classes_1/NanoDeg/1.Data_AN/L7/---New R/data/pseudo_facebook.tsv', sep = '\t')
```
<img src="https://user-images.githubusercontent.com/31917400/35759700-3fd3e9f2-0873-11e8-93d7-ff5e74cbefc6.jpg" />


### I. One Variable

>Phase_01. Understand the audience
 - [1) Histogram of Users' **Birthdays**]
   - 'date of birth' on x-axis
   - Adjust x-axis' bins, using 'scale' layers - `+ scale_x_continuous(breaks)`...JUST marking ticks - ("1 to 31")
   - 'binwidth argument' in the 'geom_histogram' layer is actually change bin size while 'breaks argument' in the 'scale' layer just change the tick marks!
```
ggplot(aes(x = dob_day), data = pf) + geom_histogram(binwidth = 1) + scale_x_continuous(breaks = 1:31)
```
 - Faceting, Splitting the data over 'dob_month' variable: `+ facet_wrap( ~ variable, ncol)`
<img src="https://user-images.githubusercontent.com/31917400/35760868-2c798ad0-087b-11e8-91e5-08aad97e0341.jpg" width="300" height="160" /> 

```
ggplot(data = pf, aes(x = dob_day)) + geom_histogram(binwidth = 1) + scale_x_continuous(breaks = 1:31) +
  facet_wrap(~dob_month, ncol = 4)
```
<img src="https://user-images.githubusercontent.com/31917400/35760701-e1ce5ce6-0879-11e8-808a-e6f293bc4f09.jpg" width="600" height="160" />

 - [2) Histogram of Users' **friend size** and introducing a **categorical variable**]
   - 'friends_count' on x-axis
   - Outliers? then 'Limiting axis': `+ scale_x_continuous(limits = c(n, n), breaks)`
   - Faceting by 'gender': `+ facet_wrap( ~ gender)` or '+ facet_grid(gender ~ .)' 
   - then ommiting NA values..it sould be dealt within the 'data' level: `data=subset(pf, !is.na(gender))`
   - IF the relationship include ****categorical variable**** (such as 'gender'), then
     - Find STATISTICS ? 
       - by the categorical variable, what we want is 'total count of records' and see the distribution in each group.: `table(categorical_variable, useNA = 'ifany')`
       - by the categorical variable, what we want is 'other function' : `by(target_variable, categorical_variable, function)`
     
```
ggplot(aes(x=friend_count), data=pf) + geom_histogram() 

ggplot(aes(x=friend_count), data=pf) + geom_histogram() + scale_x_continuous(limits = c(0,1000))

ggplot(aes(x = friend_count), data = pf) + geom_histogram(binwidth = 25) + scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50))
```
<img src="https://user-images.githubusercontent.com/31917400/35767164-d029a0c6-08de-11e8-9814-07da198c3bc2.jpg" width="1200" height="160" />

```
ggplot(aes(x = friend_count), data = pf) + geom_histogram(binwidth = 25) + scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) + facet_wrap(~gender)

ggplot(aes(x = friend_count), data = subset(pf, !is.na(gender))) + geom_histogram(binwidth = 25) + scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) + facet_wrap(~gender)
```
<img src="https://user-images.githubusercontent.com/31917400/35767303-de7caf80-08e1-11e8-9bdb-9f4b269e2486.jpg" width="700" height="200" />

```
table(pf$gender) 
by(pf$friend_count, pf$gender, summary)
```
<img src="https://user-images.githubusercontent.com/31917400/35767397-73a5e5bc-08e3-11e8-800c-4b8a94318713.jpg" width="600" height="80" />

 - [3) Histogram of Users' **tenure by year** - how many days they've used facebook so far?]
   - 'tenure/365' on x-axis
   - Determines the color of the outline, area_inside in a plot: `+ geom_histogram(binwidth, color, fill)`
   - How to set up bins? Using `summary()`, find mix&max. In x-axis, the tick-marks ranged 0 to 9 (by 1), and the plot limit is 0 to 7..binwidth be 0.25 
   - How about gender difference?: `+ facet_wrap(~gender)`
```
ggplot(aes(x = tenure), data = pf) +
  geom_histogram(binwidth = 30, color = 'black', fill = '#099DD9')
  
summary(pf$tenure/365)   
  
ggplot(aes(x = tenure/365), data=subset(pf, !is.na(gender))) +
  geom_histogram(binwidth = 0.25, color = 'black', fill = '#F79420') + scale_x_continuous(breaks=seq(1,7,1), limits=c(0,7)) +
  facet_wrap(~gender) + xlab('Years using Facebook') + ylab('Number of users in sample')
```   
<img src="https://user-images.githubusercontent.com/31917400/35768532-0c5ef29c-08f5-11e8-944f-4e41967b2051.jpg" width="600" height="170" />   

 - [4) Data Transformation of Users' **friend size**]
   - To get a better look at the data... 
   - If we have 'Over-Dispersed' data (the term is always relative to some particular posited distribution..Poisson? Gamma?) 
     - **too long tailed shape**: Some have 100 times the median value...some have an order of magnitude..
     - **Shorten the tail**: to see SD, Orders of magnitude, the pattern more clearly, etc
       - Take 'log' or 'sqrt' in the variable: `ggplot(aes(x=log(), data)`, `ggplot(aes(x=sqrt(), data)`
       - Take 'log' or 'sqrt' in a scaling layer: `+ scale_x_log()`, `+ scale_x_sqrt()`
         - what's the difference?: **x-axis labeling**. A scaling layer method returns actual 'friend_count' on x-axis. 
   - If we need to use regression
     - Take 'normalization' ?

Create all three histograms on "one single plot."
```
p1 <- ggplot(aes(x=friend_count), data=pf) + geom_histogram()
p2 <- ggplot(aes(x=log(friend_count + 1)), data=pf) + geom_histogram()
p3 <- ggplot(aes(x=sqrt(friend_count)), data=pf) + geom_histogram()

install.packages('gridExtra')
library(gridExtra)
grid.arrange(p1,p2,p3, ncol=1)

p1 <- ggplot(aes(x=friend_count), data=pf) + geom_histogram()
p2 <- p1 + scale_x_log()
p3 <- p1 + scale_x_sqrt()

grid.arrange(p1,p2,p3, ncol=1)
```
<img src="https://user-images.githubusercontent.com/31917400/36116065-f1058850-102c-11e8-9e1f-4900eab941b0.jpg" /> 

 - [5) Comparison I. **Frequency Polygon**, Connecting all counts and comparing each distribution]
   - `+ geom_freqpoly(aes(color = categorical_variable), binwidth)`
   - The shape of the frequency polygon depends on how our bins are set up - the height of the lines are the same as the bars in individual histograms, but the lines are easier to make a comparison with since they are on the same axis. 

With `facet_wrap(~gender)`, Check the each 'friend_count'.
```
ggplot(aes(x = friend_count), data = subset(pf, !is.na(gender))) + 
  geom_histogram(aes(color = gender), binwidth = 10) + 
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) + 
  facet_wrap(~gender)

ggplot(aes(x = friend_count), data = subset(pf, !is.na(gender))) + 
  geom_freqpoly(aes(color = gender), binwidth = 10) + 
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) + 
  facet_wrap(~gender)

by(pf$friend_count, pf$gender, sum)
```
<img src="https://user-images.githubusercontent.com/31917400/35770970-4515ab8a-091d-11e8-9b71-2e596b39152e.jpg" width="700" height="230" />

But the plots above do not show which gender has more friends on avg. We need proportion !
 - See the **proportion**:  Which side has more friends on average?
   - `sum(..count..)` will sum across color, so the proportions displayed are based on total users. 
   - `y = ..density...` will plot these proportions within each group
 - Change y-axis to show proportion instead of raw counts: `ggplot(aes(x = variable, y = ..count../sum(..count..))` 
 - Without `facet_wrap(~gender)` which gives 'overlay' !
 - Adding xlab(), ylab()
```
ggplot(aes(x = friend_count, y = ..count../sum(..count..)), data = subset(pf, !is.na(gender))) +
  geom_freqpoly(aes(color = gender), binwidth=10) +
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) +
  xlab('Friend Count') +
  ylab('Proportion of users with that friend count')
```
<img src="https://user-images.githubusercontent.com/31917400/35770870-a88164cc-091b-11e8-8f25-ba37273f8647.jpg" width="300" height="190" />

 - [6) Comparison II. **Boxplot** and comparing each distribution]
   - `+ geom_boxplot()`
   - In the boxplot, y-axis is no longer row count, but it should be the variable normally distributed. So we pass in the **categorical** to x, and the **numerical** to y.
   - 1)So many outliers, then focus on the box only(limiting 'friend_counts'(y-axis) between 0 and 1000) 
   - 2)But they remove our data from calculation and our boxes were affected, then go with the **"coord_cartesian layer"** instead of scale_layer !
   - 3)Let's try boxes only..
<img src="https://user-images.githubusercontent.com/31917400/35805199-c4becc8a-0a72-11e8-9978-4cb3468f7097.jpg" width="700" height="300" />

```
ggplot(aes(x=gender, y=friend_count), data = subset(pf, !is.na(gender))) + 
  geom_boxplot() + 
[...] + scale_y_continuous(limits = c(1,1000))

[...] + coord_cartesian(ylim = c(1,1000))
by(pf$friend_count, pf$gender, summary) 
[...] + coord_cartesian(ylim = c(0,250))
```
<img src="https://user-images.githubusercontent.com/31917400/35804020-e14a897a-0a6d-11e8-98cc-dcc327622762.jpg" />

> Useful Tips: To get a better look at the data 
 - What if the variable holds too many '0'sss ? 'mobile_likes' shows how often each user is giving 'likes' via their mobile phones. 
<img src="https://user-images.githubusercontent.com/31917400/35808590-31a105b0-0a7e-11e8-89fc-492a6350bc05.jpg" width="600" height="50" />

 - We want to simplify it. check_in or not...
 - Then **we create a factor!** First, let's create a new variable('mobile_check_in')that we funnel NA to..
 - `ifelse(cond, 1, 0)` if the cond is true, assign 1, otherwise assign 0, and convert it to a factor variable.
```
pf$mobile_check_in <- NA  
pf$mobile_check_in <- ifelse(pf$mobile_likes>0, 1, 0) 
pf$mobile_check_in <- factor(pf$mobile_check_in)

summary(pf$mobile_check_in)
```
<img src="https://user-images.githubusercontent.com/31917400/35807437-b58fdf26-0a7a-11e8-8a0a-fd59cfd427fd.jpg" width="600" height="40" />

 - What percent of users check_in, using mobile? 
   - The `sum()` will not work since mobile_check_in is a **factor variable**. 
   - Use the `length()` to determine the number of values in a vector.
```
sum(pf$mobile_check_in==1)/length(pf$mobile_check_in)
```
[1] 0.6459097

### II. Two Variable

>Phase_02. Tackle the issue - plotting 'actual_audience_size'(x) vs 'perceived_audience_size'(y)
 - Usually it's best to use a scatter plot to examine the relationship b/w two continuous variables. 
 - Here we have these scatter plots, where we're showing people's raw guesses of their perceived audience size against their actual audience size. And you can see they all fall below the perfect accuracy line. So people underestimate. But it's still kind of hard to get the big picture from these scatter plots and so, we have some histograms to capture more summary data. Using the histogram showing how much people over/underestimate their audience size. histogram ==> the #users vs (actual% - perceived%)
<img src="https://user-images.githubusercontent.com/31917400/35930965-49d990ee-0c2b-11e8-86c7-fb9aff037977.jpg" />

 
 - [1) Scatter of **'friend_count'** vs **'age'**]
   - `+ geom_point(alpha=1/20)`
   - `+ xlim(n, n)` (but what about 'scale_x_continuous(limits, breaks)' ?????differ?????)
   - Note: OverPlotting - Some of these pt are **spread out from one another(verically)** while others are stacked right on top of each other. Some are overplotted. We can't see how many pt are in each region. so we set the transparency of pt using `geom_point(alpha)`.
```
summary(pf$age)
ggplot(aex(x=age, y=friend_count), data = pf) + geom_point() + xlim(13, 90)
ggplot(aes(x=age, y=friend_count), data = pf) + geom_point(alpha=1/20) + xlim(13, 90)
ggplot(aes(x=age, y=friend_count), data = pf) + geom_jitter(alpha=1/20) + xlim(13, 90)
```
<img src="https://user-images.githubusercontent.com/31917400/35819924-0fb29f7c-0a9c-11e8-840a-efd1e9b1a0e6.jpg" />

We want to do some **transformation on y-axis** to get a better visualization of the data. 
 - `+ scale_y_sqrt()` and `+ geom_smooth(method = 'lm')`
 - `+ coord_trans(y='sqrt')`: transforming (cf...'coord_cartesian(ylim=c(n, n))': limiting)
 - The difference between transforming the 'scales' and transforming the 'coordinate system' is that:
   - Scale transformation occurs BEFORE statistics.
   - Coordinate transformation afterwards. Coordinate transformation also changes the shape of geoms.
 - If we want 'jitter', then use `+ geom_point(position = position_jitter(h=0))` where we set the min-height of jitter as '0' because we have '0' value on y-axis, and in jitter, the value is trembling and might end up with negative value and those sqrt would be imaginary. 
```
ggplot(aes(x=age, y=friend_count), data=pf) + geom_point(alpha=1/20) + xlim(13, 90) 
  + scale_y_sqrt() + geom_smooth(method = 'lm') 

ggplot(aes(x=age, y=friend_count), data=pf) + geom_point(alpha=1/20) + xlim(13, 90) 
  + coord_trans(y='sqrt')

ggplot(aes(x=age, y=friend_count), data=pf) + geom_point(alpha=1/20, position = position_jitter(h=0)) + xlim(13, 90) 
  + coord_trans(y='sqrt')
```
<img src="https://user-images.githubusercontent.com/31917400/35880379-afd913c6-0b75-11e8-88b1-334d11681924.jpg" />

 - [2) Further thought about **'friend_count'** vs **'age'**]

Normally, it's impossible to judge important qualities from a scatter display (just plotting every single point) only..coz we need to see mean, median, variance varies with another variables.  For example, we ask..**how does the "average" friend count varies over age?** (sounds like a moving averages smoothing out?) To do this, 
# Create a new summary dataset !!
We could start by creating a table that for each age gives us the mean, median for friend count(conditional mean, median)
 - Library: `dplyr`
   - It allows us to split up the dataframe and apply a function to SOME parts of the data !!!!
   - It provides a set of tools for efficiently manipulating dataframes. 
     - `group_by()` + `summarise()`: reduces multiple, scattered values down to a grouped, single value..just like a pivot table? 
       - `summarise()` is typically used on grouped data created by `group_by()`. The output will have one row for each group. we used this when the expected groups are SO FUCKING a lot - 10years_old, 11 years_old,....100years_old,...! (this means...otherwise(few groups)..groups are considered categorical variables and `by(target, category, func)` can handle it).  
       - the `n()` is only useful in `summarise()` function and it reports how many people are in each group ^..^..discrete? categorical?
     - `mutate()`
     - `filter()`
     - `select()`
     - `arrange(data, by=desc(variable))`
```
install.packages('dplyr')
library(dplyr)
```
 - want to group our data by the values of 'age'? Create a new dataset! 
 - create new dataset, using: `summarise(grouped_data, columns we want to create, the NO.of records in each group)`
 - so...it creates a...pivot table...then sort out in an ascending order
```
age_groups <- group_by(pf, age)
pf_fcount_by_age <- summarise(age_groups, f_count_mean = mean(friend_count), f_count_median = median(friend_count), n=n())
pf_fcount_by_age <- arrange(pf_fcount_by_age, by=age)
head(pf_fcount_by_age, 50) 
```
<img src="https://user-images.githubusercontent.com/31917400/35917431-05e374b0-0c06-11e8-84bd-f0af369197aa.jpg" />

 - Plot 'mean_friend_count' vs. 'age' using a line graph.
```
ggplot(aes(x=age, y=f_count_mean), data=pf_fcount_by_age) + geom_line() 
```
<img src="https://user-images.githubusercontent.com/31917400/35918378-7f84d554-0c09-11e8-868e-43284f1f84bf.jpg" />

>well..in older ages, our estimates are highly variable for 'mean_friend_count'. But, in ages 30 to 60, the mean-count is hovering about over 100...

 - We can overlay our original scatter plot with the summary plots on its top. 
```
ggplot(aes(x=age, y=friend_count), data=pf) + 
  geom_point(alpha=1/20, position = position_jitter(h=0), color='orange') +
  xlim(13, 90) + coord_trans(y='sqrt') +
  
  geom_line(stat = 'summary', fun.y = mean) + 
#Black: this is new!! the y is mean 'friend_count' by age over my raw data. we can see how dispersed the data is around the means..
  geom_line(stat = 'summary', fun.y = quantile, fun.args=list(probs = 0.1), linetype=2, color='blue') + 
#Blue: 10% quantile: the value that cuts off the first 10 percent of the data values when it is sorted in ascending order. 
  geom_line(stat = 'summary', fun.y = quantile, fun.args=list(probs = 0.9), linetype=2, color='red') +
#Red: 90% quantile 
  geom_line(stat = 'summary', fun.y = quantile, fun.args=list(probs = 0.5), linetype=2, color='green') 
#Green: 50% quantile("median")
```
 - We can zoom in, if we use `coord_cartesian(xlim = c(13, 90))` instead of `xlim()`and `scale_x_continuous(limits=c(n,n))`. 
```
ggplot(aes(x=age, y=friend_count), data=pf) + 
  geom_point(alpha=1/20, position = position_jitter(h=0), color='orange') +
  
  coord_cartesian(xlim = c(13, 70), ylim = c(0,1000)) + 

  geom_line(stat = 'summary', fun.y = mean) + 
  geom_line(stat = 'summary', fun.y = quantile, fun.args=list(probs = 0.1), linetype=2, color='blue') + 
  geom_line(stat = 'summary', fun.y = quantile, fun.args=list(probs = 0.9), linetype=2, color='red') +  
  geom_line(stat = 'summary', fun.y = quantile, fun.args=list(probs = 0.5), linetype=2, color='green') 
```
<img src="https://user-images.githubusercontent.com/31917400/35929570-b00c38f2-0c27-11e8-81b6-cdb2ffc7d278.jpg" />

>The first plot: well..the 90% of data values are still below 1000. The second plot: well..in b/w 35 to 50, the friend count falls below 250, so 90% of users in this group have.. < 250 friends

 - Correlation: 'age' and 'friend_count'..Let's see the strength of the relationship!
   - The `with()` function applys an expression to a 'cond-dataset'.
   - The `by()` function applys a function to each level of a factor or factors(few categories).
   - the dplyr's `group_by()` + `summarise(group_by(), new-columns we want, n=n())` is for a dataset with so many categories. 
```
cor.test(pf$age, pf$friend_count, method = 'pearson')
with(pf, cor.test(pf$age, pf$friend_count, method = 'pearson'))
```
<img src="https://user-images.githubusercontent.com/31917400/35931338-46f1e7f4-0c2c-11e8-80f9-8f916a0f67a4.jpg" width="600" height="120" />

>-0.027 so... there is no significant relationship...

 - The relationship is not linear or not monotonic...i mean...not flat..and we don't want to include the older ages, since they are likely to be incorrect. Let's recalculate the corr.
```
with(subset(pf, age<=70), cor.test(age, friend_count, method = 'pearson')) #as the age increases, friend count decreases...but it's just a descriptive statistics..we don't think age is not only factor that causes the decrease of friend count...so we need inferential statistics..-0.17

with(subset(pf, age<=70), cor.test(age, friend_count, method = 'spearman')) #spearman(the rank correlation measure) gives us 'rho' instead of 'cor'...that also measures "monotonic relationship"...-0.25
```
<img src="https://user-images.githubusercontent.com/31917400/35931723-640b1cec-0c2d-11e8-9b94-c64f80ede048.jpg" width="800" height="120" />

 - [3) Scatter of **likes_received**(y) vs. **www_likes_received**(x)]
```
ggplot(aes(x=www_likes_received, y=likes_received), data = pf) + geom_point()
```
Let's ignore some funky outlier..so limit the x, y-axis..using `quantile()`
```
ggplot(aes(x=www_likes_received, y=likes_received), data = pf) + geom_point() +
  xlim(0, quantile(pf$www_likes_received, 0.95)) +
  ylim(0, quantile(pf$likes_received, 0.95)) +
  geom_smooth(method = 'lm', color='red')
```
<img src="https://user-images.githubusercontent.com/31917400/35943037-b6cffe4e-0c4f-11e8-903c-017c0434fc19.jpg" width="800" height="210" />

The correlation coefficient measures the "tightness" of linear relationship between two variables and is bounded between -1 and 1, inclusive.

The regression slope measures the "steepness" of the linear relationship between two variables and can take any value from −∞ to +∞.

The correlation gives you a bounded measurement that can be interpreted independently of the scale of the two variables. The closer the estimated correlation is to ±1, the closer the two are to a perfect linear relationship. The regression slope, in isolation, does not tell you that piece of information.
<img src="https://user-images.githubusercontent.com/31917400/35944238-aeffa6fc-0c53-11e8-8fc8-218a781716c1.jpg" />

>But the correlation coefficient is invariant under a linear transformation of either X or Y, and the **slope** of the regression line when both X and Y have been transformed to **z-scores** is the **correlation coefficient**. 

That is, the **slope** of the regression and the **correlation** coincide when SD(Y) = SD(X). They only coincide when the two variables are on the same scale, in some sense. The most common way of achieving this is through **standardization**. When you are doing multiple regression, this can be more complicated due to multicollinearity.  

 - Let's quantify this relationship! 
```
cor.test(pf$www_likes_received, pf$likes_received, method = 'pearson') 
```
This gives us a correlation of 0.948. This is a strong positive correlation, and in reality most variables are not correlated that closely. The correlation that we just found was an artifact of the nature of the variables. One of them was really a superset of the other.


### III. Multi Variable

>Phase_03. Tackle the issue - plotting 'actual_audience_size'(x) vs 'perceived_audience_size'(y)
 - Okay, so people aren't very good at guessing their audience sizes. But maybe, maybe people who are older, maybe they have a better sense, than teenagers, for example. 
 - So the next plot is another scatter plot, but this time we added a **third level**, where we added color to represent the age of the survey respondent. And you can see again we have this horizontal stripes for people who are guessing that there are 50 or 100 people in their audience. But I don't see any pattern in the color. We do reach dead ends. This is one example of kind of a failure. I can tell if younger people were more accurate than older people, there is too much over plotting. In this plot there are too many dots on top of each other..
<img src="https://user-images.githubusercontent.com/31917400/35946556-2fda77ae-0c5c-11e8-9d0c-302632b73627.jpg" width="600" height="200" /> 
 
 - [1) Third 'categorical' Variable]
   - Scatter of 'friend_count' vs 'age' vs **'gender'**
   - Previously we noted that female users have more friends on average than male users. And, we might wonder, .... "Is this just because female users have a different "age" distribution ?" Or, maybe c.o.n.d.i.t.i.o.n.a.l. on age, the differences are actually larger. (gender vs friend_count)----- age?

We want to add 'mean' on the boxplot ? `+ stat_summary(fun.y, geom, shape)`   
```
ggplot(aes(x=gender, y=age), data = subset(pf, !is.na(gender))) + 
  geom_boxplot() + stat_summary(fun.y=mean, geom='point', shape=4)
```
Since male users on avg are a bit younger, we might actually think a simple 'male-to-female comparison' doesn't capture their substantial differences in friend_count. Let's look at median_friend_count by age and gender instead. Then we can see that nearly everywhere the median friend count is larger for women than it is for men. `+ geom_line(aes(color), stat, fun.y)`
```
ggplot(aes(x=age, y=friend_count), data = subset(pf, !is.na(gender))) + 
  geom_line(aes(color=gender), stat='summary', fun.y=median)
```
<img src="https://user-images.githubusercontent.com/31917400/36158322-f713adba-10d3-11e8-8b01-cd5cf765a4fd.jpg" width="700" height="200" /> 

We write code to create a new dataframe, called **'pf.fc_by_age_gender'**, that contains information on each 'age' AND 'gender' group. The dataframe should contain the following variables:
 - mean_friend_count
 - median_friend_count
 - n (the number of users in each age and gender grouping)

then plot and shows the median friend count for each "gender" as "age" increases. Here, **the aes wrapper** deals with the variable with 'many categories', and **geom_line layer** deals with the variable with 'few categories'. It helps us understand how the difference between male and female users varies with age.
```
library(dplyr)

age_gender <- group_by(subset(pf, !is.na(gender)), age, gender)
pf.fc_by_age_gender <- summarise(age_gender, mean_friend_count=mean(friend_count), median_friend_count=median(friend_count), n=n()) 

ggplot(aes(x = age, y = median_friend_count), data = pf.fc_by_age_gender) + 
  geom_line(aes(color=gender), stat = 'summary', fun.y=median)
```
<img src="https://user-images.githubusercontent.com/31917400/36160971-72509730-10da-11e8-8c33-03f6dcd7b8d8.jpg" /> 

 - [2) Thinking in **Ratios for Categorical**]
   - It seems like the gender difference is largest for our young users. It would be to put this in relative terms though. So, let's answer a different question. "**How many times more friends**" does the average female user have than the male user? Maybe, females have twice as many friends as male users, or maybe it's ten times as many friends.....
   - We want to plot the **ratio of females to males** to determine how many times more friends the average female user has, compared to the number of friends the average male user has...Plot the ratio of the female to male median friend counts using the dataframe **'pf.fc_by_age_gender2'**.

# Create a new summary dataset !!
>Notice how the variables we grouped over -male and female-, have been repeated. We need to rearrange our data a little bit. Right now, our data is in 'long format'(pf.fc_by_age_gender). We have many rows. What we need is 'wide format' with values of the 'median_friend_count 'conditional on 'gender'(pf.fc_by_age_gender2).
 - `dcast(data, keep ~ expand, key_value)`: **Reshaping** the dataset: We specify the dataset we are going to change and modify and then we put in a formula. Now, the first part of the formula (the left of the tilde sign), will list the variables I want to keep (age). On the right side of the tilde, we use the gender variable since we want male and female users to have **their own columns** for median friend count in the data frame. And finally, we set "value.var" because it holds the k.e.y m.e.a.s.u.r.e.m.e.n.t.s. or their values in our new dataframe.
 - Note: `melt()`: Converting the wide-format(expanded for human) back to the original long-format(shrunk for machine).
```
install.packages('reshape2')
library(reshape2)

pf.fc_by_age_gender2 <- dcast(pf.fc_by_age_gender, age ~ gender, value.var = 'median_friend_count')
head(pf.fc_by_age_gender2, 10)
```
 - Adding a baseline in the ratio plot 
   - `+ geom_hline(yintercept, linetype)`: A horizontal line to the plot with a y-intercept of 1 (female = male), with the linetype of...
     - 0 = blank 
     - 1 = solid
     - 2 = dashed
     - 3 = dotted
     - 4 = dotdash
     - 5 = longdash
     - 6 = twodash
```
ggplot(aes(x=age, y=female/male), data = pf.fc_by_age_gender2) + geom_line() + 
  geom_hline(yintercept=1, linetype=2, alpha=0.3)
```
<img src="https://user-images.githubusercontent.com/31917400/36177967-59931ee0-110f-11e8-8d0e-246b5db097c7.jpg" /> 

 - [3) Third 'Quantitative' Variable]
   - Scatter of 'friend_count' vs 'age' vs 'gender' vs **'tenure'**
   - What if we looked at 'friend_count', 'age' and 'gender'(categorical) over another **numerical** variable?
   - For example, since users are likely to accumulate friends over time using FB, Facebook'tenure' is important for predicting friend_count. Tenure or how many days since registering with Facebook is associated with age as well. 
   - We create a new variable called **'year_joined'** in our dataframe. This variable is going to hold the year that our users first joined FB(using the variable 'tenure' and 2014 as the reference year). 
   - 'tenure' is measured in days, but we want to convert it to years.
     - `floor()` to round down to the nearest integer. 
     - `ceiling()` to round up to the nearest integer.
```
pf$year_joined <-  floor(2014 - pf$tenure/365)
```
<img src="https://user-images.githubusercontent.com/31917400/36208607-5a7837f0-1191-11e8-9dd4-aba4a89990eb.jpg" /> 

 - Cut a variable and make it **categorical**.
 - `cut(variable, breaks=c(n,n))` converts Numeric(or Continuous) to Factor, breaking up a continuous variable(such as age) into a categorical variable.
 - We create a new variable in the dataframe called **'year_joined.bucket'** offering the following buckets:
   - (2004, 2009]
   - (2009, 2011]
   - (2011, 2012]
   - (2012, 2014]
 - Median 
   - We have four bins of users, depending on when they joined Facebook, and it looks like two people have a value of NA. 
   - Let's use this **'year_joined.bucket'** variable to create a line graph of 'friend_count' vs 'age' so that each 'bucket' is a line tracking the **median friend_count** across 'age'(four different lines on our plot)...`+ geom_line(aes(color), stat='summary', fun.y)`
```
pf$year_joined.bucket <- cut(pf$year_joined, breaks = c(2004,2009,2011,2012,2014)) 

table(pf$year_joined.bucket, useNA = 'ifany')

ggplot(aes(x=age, y=friend_count), data = subset(pf, !is.na(year_joined.bucket))) + 
  geom_line(aes(color=year_joined.bucket), stat = 'summary', fun.y=median)
```
 - Grand-Mean
   - In this plot, we can see that our suspicion is confirmed. Users with a longer tenure(red line) tend to have higher friend counts, with the exception of our older users, say, about 80 and up. To put these **cohort-specific-medians** in perspective, we can change them to **cohort-specific-means**. And then plot the grand-mean down as well - `geom_line(stat, fun.y, linetype)`
```
ggplot(aes(x=age, y=friend_count), data = subset(pf, !is.na(year_joined.bucket))) + 
  geom_line(aes(color=year_joined.bucket), stat = 'summary', fun.y=mean) + 
  geom_line(stat = 'summary', fun.y=mean, linetype=2, alpha=0.8)
```
<img src="https://user-images.githubusercontent.com/31917400/36211466-33418d96-1199-11e8-9afb-f0b89cdc6ed4.jpg" />

>We've seen that users who have been on the site longer, typically have higher friend counts across ages. Now, this leaves me wondering if friend requests are the same or different across groups. Do 'new users' go on friending sprees? Or do 'users with more tenure' initiate more friendships? Since the general pattern continues to hold after conditioning on each of the 'buckets of year joined', we might increase our confidence that this observation isn't just an artifact of the time that users have had to accumulate friends. 
 - Need to subset the data to only consider user with at least one day of tenure.
 - we could see how many friendships does a user initiate for **each day** since they've started using the service. 
```
ggplot(aes(x=tenure, y=friendships_initiated), data = subset(pf, tenure>=1)) +
  geom_line(aes(color=year_joined.bucket), stat = 'summary', fun.y=mean)

ggplot(aes(x=tenure, y=friendships_initiated/tenure), data = subset(pf, tenure>=1)) +
  geom_line(aes(color=year_joined.bucket), stat = 'summary', fun.y=mean)
```
<img src="https://user-images.githubusercontent.com/31917400/36225178-bff17828-11c1-11e8-8371-87b534a5b989.jpg" />

 - Bias-Variance Tradeoff
   - As the bin size increases we see less noise on the plot!!!
   - Adjust the noise by bending our x-axis differently.
   - Binning values by the denominator in the round function and then transforming back to the natural scale with the constant in front.
```
ggplot(aes(x = 7 * round(tenure / 7), y = friendships_initiated/tenure), data = subset(pf, tenure > 0)) +
  geom_line(aes(color = year_joined.bucket), stat = "summary", fun.y = mean) 

ggplot(aes(x = 30 * round(tenure / 30), y = friendships_initiated/tenure), data = subset(pf, tenure > 0)) +
  geom_line(aes(color = year_joined.bucket), stat = "summary", fun.y = mean)

ggplot(aes(x = 90 * round(tenure / 90), y = friendships_initiated/tenure), data = subset(pf, tenure > 0)) +
  geom_line(aes(color = year_joined.bucket), stat = "summary", fun.y = mean) 
```
<img src="https://user-images.githubusercontent.com/31917400/36225818-ca9f528e-11c3-11e8-874c-47528562310a.jpg" />

 - Natural Smoothing w/o bin control ? `+ geom_smooth(aes(color))`  but weird...what about mean function???? What about geom_line()???
```
ggplot(aes(x = tenure, y = friendships_initiated/tenure), data = subset(pf, tenure >= 1)) +
  geom_smooth(aes(color = year_joined.bucket))
```
<img src="https://user-images.githubusercontent.com/31917400/36226064-af2334e8-11c4-11e8-804c-64725385a10e.jpg" />


### Wide vs Long
>Story: Throughout all of our analyses of the pseudo-Facebook user data set, we've come to learn a lot about our users. From their birthdays, to their friend counts, to their friendships initiated, we've really come to understand their behaviors and how they use the Facebook platform. But, now I think it's time for something completely different. In the next couple of segments, we'll look at another data set, and then **we'll return to this Facebook data set to draw some comparisons**. 

>Data: The citation for the original paper on the yogurt data set is Kim, Jaehwan, Greg M. Allenby, and Peter E. Rossi. "Modeling consumer demand for variety." Marketing Science 21.3 (2002): 229-250.

We are going to work with a dataset **describing household purchases of five flavors of Dannon yogurt in the 8 ounce size**. Their price is recorded with each purchase occasion. This yogurt dataset has a quite different structure than our pseudo-Facebook dataset. 
 - The synthetic Facebook data has one row per individual with that row giving their characteristics and counts of behaviors over a single period of time. (wide-format)
 - On the other hand, the **yogurt data** has many rows per household, one for each purchase occasion. This kind of microdata is often useful for answering different types of questions than we've looked at so far. (long-format)
```
yo <- read.csv('C:/Users/Minkun/Desktop/classes_1/NanoDeg/1.Data_AN/L7/---New R/data/yogurt.csv')
head(yo, 5)
str(yo)

yo$id <- factor(yo$id)
```
<img src="https://user-images.githubusercontent.com/31917400/36228666-b9107e2c-11cc-11e8-9bba-fe7381273805.jpg" />

 - [1) Histogram & Discreteness]
   - We can immediately notice some important discreetness to the distribution below. There appear to be prices at which there are many observations, but then no observations in adjacent prices. This makes sense if prices are set in a way that applies to many of the consumers. There are some purchases that involve much lower prices, and if we are interested in price sensitivity, we definitely want to consider what sort of variations is in these prices. Now, I also want you to note that **if we chose a different bin width**, we might obscure this discreteness.
   - Say if I chose a bandwidth equal to ten. In this histogram, we would miss the observation for some of the empty spaces for the adjacent prices. So, it's no surprise that for this very discreet data this histogram is a very biased model. 
```
ggplot(aes(x=price), data=yo) + geom_histogram(color='red')
ggplot(aes(x=price), data=yo) + geom_histogram(binwidth = 10, fill='red')
```
<img src="https://user-images.githubusercontent.com/31917400/36228973-a899bc60-11cd-11e8-8507-aa178e3cf0d0.jpg" />

 - How to detect the discreteness? 
   - `summary()`: if we just look at a five number summary of the data, we might not notice this so easily. One clue to the discreteness is that the 75th percentile is the same as the maximum.
   - `unique()`: We could also see this discreteness by looking at how many distinct prices there are in the data set.
   - `table()`: Tabling the variable we get an idea of the distribution like we saw in the histogram.

<img src="https://user-images.githubusercontent.com/31917400/36229317-bec10a60-11ce-11e8-950a-5a8ad02d8f6a.jpg" />

 - Now that we know something about the price, let's figure out on a given purchase occasion how many 'yogurts' does a household purchase. To answer this we need to combine the 'counts of the different yogurt flavors' into one variable. For example, for the one particular household, on one purchase occasion, they bought three different types of yogurt. To figure this out for all the households, we need to make use of a new function.
   - Create a new variable called **'all.purchases'**, which gives the total counts of yogurt for each observation or household.
   - One way to do this is using the `transform(data, defining new_variable)` 
   - The transform function produces a dataframe so if you use it then save the result to 'yo'!
   - yo$all.purchase <- sum(yo[, 4:8]) #....doesn't work..
```
yo <- transform(yo, all.purchase = strawberry+blueberry+pina.colada+plain+mixed.berry)
summary(yo$all.purchase)

ggplot(aes(x=all.purchase), data=yo) + geom_histogram(color='red', binwidth = 1, fill='yellow')
```
<img src="https://user-images.githubusercontent.com/31917400/36231044-8fd1c770-11d4-11e8-8835-e283bb449694.jpg" />

 - The histogram above reveals that most households buy one or two yogurts at a time. To dive deeper into our **yogurt prices** and **household behavior**, let's investigate the **price over time** in more detail.  In this dataset, we can examine changes in prices because **we have data on the same households over time**. The visualization should be a scatter plot of **price** vs **time** (time series plot). 
 - Looking at the plot, we can see that the mode or the most common prices, seem to be increasing over time. We also see some lower price points scattered about the graph. These may be due to sales or, perhaps, buyers using coupons that bring down the price of yogurt..
```
ggplot(aes(x=time, y=price), data = yo) + geom_point(alpha=0.3, color='red')
ggplot(aes(x=time, y=price), data = yo) + geom_jitter(alpha=0.2, shape=21, fill='yellow')
```
<img src="https://user-images.githubusercontent.com/31917400/36229637-d3feef22-11cf-11e8-8cff-7566f712ecf0.jpg" width="700" height="200" /> 

 - [2) Sampling Observations]
   - Can you see the **scattered time series** data? How to proceed differently with this type of a data set?
   - When familiarizing yourself with a new data set that contains **multiple observations of the same units (over time?)**, it's often useful to work with a **sample** of those units so that it's easy to display the raw data for that sample. 
   - In the case of the yogurt data set, we might want to look at a small sample of households in more detail so that we know what kind of within and between household variation we are working with. 
   - This analysis of a **sub-sample** might come before trying to use within household variation as part of a model. For example, this data set was originally used to model consumer preferences for variety. But, before doing that, we'd want to look at 
     - how often we observe households buying yogurt 
     - how often they buy multiple items
     - what prices they're buying yogurt at. 
   - One way to do this is to look at some **sub-sample** in more detail. Let's pick **16 households** at random and take a closer look.
   - 'id' is a factor, otherwise, level() doesnt work. `level()` specify each category in the variable announced as a factor.
   - "x %in% y"" returns a logical (boolean) vector the same length as x that says whether each entry in x appears in y. That is, for each entry in x, it checks to see whether it is in y. This allows us to subset the data so we get all the purchases occasions for the households in the sample. Then, we create scatterplots of price vs. time and facet by the sample id. 
   - `subset(yo, id %in% sample.ids)`: data is..with 'id'..that only present in 'sample.ids'
   - `facet_wrap(~id)`: ~ variable name' that we want to split the data over..so categorical variable
   - Use the 'pch' or 'shape' parameter to specify the symbol point...
<img src="https://user-images.githubusercontent.com/31917400/36230305-eaa85888-11d1-11e8-9550-037bd0143794.jpg" /> 

```
ggplot(aes(x=time, y=price), data = subset(yo, id %in% sample.ids)) + 
         facet_wrap(~id) + 
         geom_line() + geom_point(aes(size = all.purchase), pch=1) 
```
<img src="https://user-images.githubusercontent.com/31917400/36231168-f0a9b67a-11d4-11e8-9a95-2b10033247b4.jpg" /> 

 - From these plots above, we can see the variation and how often each household buys yogurt. It seems that some household purchases more quantities than others with these larger circles indicating. For most of the households, the price of yogurt holds steady, or tends to increase over time. Now, there are, of course, some exceptions. We might think that the household is using coupons to drive the price down. Now, we don't have the coupon data to associate with this buying data, but you could see how that information could be paired to this data to better understand the consumer behavior. 



   









