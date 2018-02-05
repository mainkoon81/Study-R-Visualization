# Study-Visualization-Storytelling (R)

__[R]__
 - I. Explore One Variable
 - II. Explore Two Variable 
 - III. Explore Multi-Variable

### I. One Variable
http://hci.stanford.edu/publications/2013/invisibleaudience/invisibleaudience.pdf

__Story:__ People's perception of their audience on FB matches up to the reality? 
 - Well, FB believes who you think is in your audience affects how you present yourself. 
 - Who's actually seeing the content they are sharing?  
   - __Survey Q1.__ "How many do you think saw the post? 
     - There is a big mismatch b/w people's perceived audience size and their actual audience size.
     - People dramatically underestimate the size of their audience - (1/4). 
   
__Library:__ `ggplot2`

__Data:__ 'pseudo_facebook.tsv' 

#### Adjust a font size?
 - with the font size set to 20 ?
```
install.packages('ggthemes', dependencies = TRUE)
library(ggthemes)
theme_set(theme_minimal(20)) 

pf <- read.csv('C:/Users/Minkun/Desktop/classes_1/NanoDeg/1.Data_AN/L7/---New R/data/pseudo_facebook.tsv', sep = '\t')
```
<img src="https://user-images.githubusercontent.com/31917400/35759700-3fd3e9f2-0873-11e8-93d7-ff5e74cbefc6.jpg" />

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

 - [2) Histogram of Users' **friend size**]
   - 'friends_count' on x-axis
   - Outliers? then 'Limiting axis': `+ scale_x_continuous(limits = c(n, n), breaks)`
   - Faceting by 'gender': `+ facet_wrap( ~ variable)` or '+ facet_grid(gender ~ .)' 
   - then ommiting NA values..it sould be dealt within the 'data' level: `data=subset(pf, !is.na(gender))`
   - IF the relationship include categorical variable (such as 'gender'), 
     - STATISTICS - which side has more friend on average?
     - To find average 'friend_count_by_gender', we use `by(target variable, categorical variable, function)`
     
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
   - How to set up bins? Using `summary`, find mix&max. In x-axis, the tick-marks ranged 0 to 9 (by 1), and the plot limit is 0 to 7..binwidth be 0.25 
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

 - [4) Histogram of Users' **age**]
   - 'age' on x-axis
   - In x-axis, the tick-marks ranged 0 to 113 (by 5), and..binwidth would be 1.
```
summary(pf$age) 

ggplot(aes(x = age), data = pf) +
  geom_histogram(binwidth = 1, color='black', fill = '#5760AB') + scale_x_continuous(breaks = seq(0, 113, 5)) + 
  xlab('Ages using FB') + ylab('NO.users in sample')
```
<img src="https://user-images.githubusercontent.com/31917400/35768286-4383a3e8-08f1-11e8-9164-94dbea6dfa95.jpg" width="300" height="170" /> 
 
 - [5) Data Transformation of Users' **friend size**]
   - If we have 'Over-Dispersed' data (the term is always relative to some particular posited distribution..Poisson? Gamma?) 
     - **too long tailed shape**: Some have 100 times the median value...some have an order of magnitude..
     - **Shorten the tail**: to see SD, Orders of magnitude, the pattern more clearly, etc
       - Take 'log' or 'sqrt' in the variable
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
<img src="https://user-images.githubusercontent.com/31917400/35769867-c082c712-0909-11e8-964c-7b9d82821158.jpg" /> 

 - [6) Comparison I. **Frequency Polygon**, Connecting all counts and comparing each distribution]
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
 - Without `facet_wrap(~gender)`
 - Adding xlab(), ylab()
```
ggplot(aes(x = friend_count, y = ..count../sum(..count..)), data = subset(pf, !is.na(gender))) +
  geom_freqpoly(aes(color = gender), binwidth=10) +
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) +
  xlab('Friend Count') +
  ylab('Proportion of users with that friend count')
```
<img src="https://user-images.githubusercontent.com/31917400/35770870-a88164cc-091b-11e8-8f25-ba37273f8647.jpg" width="300" height="190" />

 - [7) Comparison II. **Boxplot** and comparing each distribution]
   - `+ geom_boxplot()`
   - In the boxplot, y-axis is no longer row count, but it should be the variable normally distributed. So we pass in the **categorical** to x, and the **numerical** to y.
   - 1)So many outliers, then focus on the box only(limiting 'friend_counts'(y-axis) between 0 and 1000) 
   - 2)But they remove our data from calculation and our boxes were affected, then go with the **"coord_cartesian layer"** instead of scale_layer !
   - 3)Let's try boxes only..
<img src="https://user-images.githubusercontent.com/31917400/35803594-3d23e450-0a6c-11e8-95c4-b23757f52055.jpg" width="400" height="70" />

```
ggplot(aes(x=gender, y=friend_count), data = subset(pf, !is.na(gender))) + 
  geom_boxplot() + 
[...] + scale_y_continuous(limits = c(1,1000))

[...] + coord_cartesian(ylim = c(1,1000))
by(pf$friend_count, pf$gender, summary) 
[...] + coord_cartesian(ylim = c(0,250))
```
<img src="https://user-images.githubusercontent.com/31917400/35804020-e14a897a-0a6d-11e8-98cc-dcc327622762.jpg" />







































