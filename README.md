# NBA Injury Analysis 2013-2019

## Introduction

Recently, NBA personalities have complained that the players in the league are 'overworked' and that that is the cause of more serious injuries. In this report, I sought out to investigate whether or not there is any truth to that general sentiment. Do overworked players tend to have more severe injuries? Additionally, I also explored different potential scheduling changes the NBA can make to prevent players from feeling overworked.



## Methodology

Prior to gathering the data, I had to decide exactly how I would classify a player's 'workload' and whether or not they were overworked. Upon reading [these](https://pubmed.ncbi.nlm.nih.gov/32125672/) [two](https://www.scienceforsport.com/acutechronic-workload-ratio/) articles, I decided that the best method would be to track what a player's acute-chronic workload ratio (ACWR for short) was at the time of the injury. The ACWR ratio monitors how much effort the athlete has recently exerted vs how much effort the athlete is _used_ to exerting. Generally, sports scientists use the average workload the athlete has exerted over the past 7 days  and 28 days as the 'recent effort' and 'normal effort' respectively. An ACWR above 1 indicates that an athlete has exerted more effort on average over the past 7 days than over the past 28 days. Similarly,  ACWR below 1 indicates that an athlete has exerted less effort on average over the past 7 days than over the past 28 days. And finally, an ACWR of 1 indicates that an athlete has exerted the exact same amount of effort on average the past 7 days as he has over the past 28 days. 

Classifying exactly what an NBA player's workload is was tricky. Since I have no access to the training data of individual players, I was forced to resort to statistics that are readily available to the public on NBA.com. I decided that using the in-game distances a player has traveled would be the most appropriate metric to define as a player's 'workload'. 

Next, I had to decide which timeframes I had to use to conduct this analysis. The NBA only started tracking the distance traveled by players during the 2013-14 season, so I was restricted between the years of 2013-2021. Considering that the 2019-20 and 2020-21 seasons were heavily affected by the onset of COVID-19, I decided to only conduct my analysis on the seasons that occured between 2013-2019.

While my analysis was only on data from 2013-2019, I also had to consider that a player's injury history may play a large role in how susceptible they are to injuries. Thus, I gathered injury data from 1992-2019 to ensure that every player in the NBA would have their true injury history recorded. 



## Hypothesis

Being an avid NBA fan over the past 10 years (Go Knicks!), the eye test tells me that injuries are indeed on the rise. To me, it feels like an important player is getting injured every other week. When I was younger, important players seemed to rarely get injured and injuries were viewed more as like 'freak accident'. Additionally, the league pace of the league has also increased significantly over the past 10 years due to the rise of 'small ball' strategies (teams opting to play the smaller and quicker players). Since teams are playing a lot faster than they have before, I do suspect that many injuries can be attributed to the tough 82 game schedule. Also, the eye test tells me that the most severe injuries seem to happen when players are in the middle of a rough patch in the team's schedule. Since more players are expected to run more each game, a week with 4-5 games can be incredibly taxing on a player. Thus I do expect that we will see a strong correlation between the average ACWR of players and severity of injuries.



### Gathering the injury data
Using Python's Beautiful Soup package, I was able to extract the necessary injury data from [ProSportsTransactions.com](prosportstransactions.com). I only gathered data from the 'Movement to/from injured/inactive list (IL)' portion of the transactions.

Below is a screenshot of how the data looked like once I scraped it. 

**Fig. 1**:

![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019/blob/main/Images%20and%20Graphs/example%20sc.PNG "Figure 1")

As you can see, there are only 5 columns: Date, Team, Acquired, Relinquished and Notes. 
The Acquired column indicated which player was being _removed_ from the injury list on that date, whereas the Relinquished column indicated which player was being _added_ to the injury list on that date. The Notes column indicated why a player was being placed on the injury list. 

Scrolling through the data, there were quite a few problems with it. Firstly, many players were 'Placed on the Injury List' (or 'Relinquished') but they were never removed from it. Additionally, a lot of the data did not specify why a player was being placed on the injury list. I suspect that a lot of teams would use the injury list to have their players 'rest' but not specify it.

Another major problem was that players who were listed as 'out for the season' were often never activated from the injury list again, however they did continue playing NBA basketball the following season. I had to figure out a way to solve that since the most severe injuries are often season-ending. 

### Cleaning the Injury Data
The data-cleaning process for both the 1992-2013 and  2013-2019 datasets consisted of:

**•** Determine which season an injury took place
**•** Ensuring that the names in the 'Relinquished' and 'Acquired' columns were consistent of those used on the NBA website
**•** Adding 'Acquired' rows for players who were listed as 'out for the season'
**•** Dropping rows with irrelevant data (i.e. games missed due to rest) or missing data (ex: instances where the player 'activated' from the injury list)
**•** Determining the duration of each injury
**•** Categorizing each injury on which part of the body was affected
**•** Dropping all columns with unnecessary information

Once I was done the above steps for the 1992-2013 data, I was ready to create a new ' Player Injury History' dataframe that would be used in the final analysis. 
The player injury history would specify which how many times the player has injured each body part (their 'injury count') as well as the cumulative number of days a player was out due to an injury on said body part (their cumulative injury durations). 

Once I was finished compiling the player injury history data, I was ready to merge it with the cleaned 2013-2019 injury data. The player injury history dataset was continuously updated to consider the new injuries players incurred as well as the new players that entered the league after 2013.


### Gathering the workload data

Once I incordporated the injury history of the players' into my 2013-2019 data, I was almost ready to analyze it. The last thing I had to do was gather the in-game distances traveled by the players over the past 7 and 28 days. I used the [NBA API](https://github.com/swar/nba_api) library to help me with extract the necessary data. Once I finished scraping the necessary data, the data was finally ready for analysis. Below is a screencap of the data right before I started the analysis:

**Fig. 2**:

![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019/blob/main/Images%20and%20Graphs/ready_for_analysis.PNG "Figure 2")

## Exploring the Data
Before Jumping into the analysis, let's take a quick glance at the data to see if we can notice any trends. Firstly, let's see if injuries truly _are_ on the rise in the NBA.

**Fig. 3**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Basic%20EDA%20-%20Images%20%26%20Graphs/Number%20of%20Injuries%20per%20Szn.png "Figure 3")

From this graph here, we can see that injuries have been increasing since 2013. The 2013-14 season only had a grand total of 245 injuries, whereas the 2018-19 season had a total of 547 injuries. That is a significant increase that deserves to be investigated. Let's look at the severity of injuries per season as well. Below is how I classified the severity of each injury (based on the total number of days out): 

**Table 1**:

| Severity           | Injury Duration |
| -------------------|:---------------:|
| Mild               | 0-28            |
| Moderate           | 28-90           |
| Moderately Serious | 90-180          |
| Serious            | 180+            |



**Fig. 4**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Basic%20EDA%20-%20Images%20%26%20Graphs/Category%20of%20Injuries%20per%20szn.png "Figure 4")

As we can see from the above graph, it appears that most of the injuries that occur in the NBA are mild. Additionally, the increase in the number of mild injuries appears to be the main cause of the drastic difference in injuries between the 2013-14 and 2018-19 seasons. However, it is worth noting that there was also a large increase in the amount of serious injuries between 2013-14 and 2018-19 seasons as well.

As this project is trying to see if players are getting 'overworked', let's also compare the average ACWR for each injury across each season.

**Fig. 5**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Basic%20EDA%20-%20Images%20%26%20Graphs/Uncombined%20Images/Average%20AWCN%20of%20injured%20players%20by%20season.png "Figure 5")

It appears that the ACWR of injuries has remained relatively consistent across the years, each year appears to have an average ACWR that is close to 1. To understand the consistency of the ACWR over the seasons better, let's also take a look at both the 7 and 28 day averages of the distance traveled by players in game. My initial hypothesis mentioned that it seems like NBA players are working more than they did in previous years.

**Fig. 6**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Basic%20EDA%20-%20Images%20%26%20Graphs/Uncombined%20Images/Frequency%20of%20Injuries%20by%20body%20part.png "Figure 6")

The average distances traveled by NBA players (immediately prior to injuries) have remained relatively consistent since 2013-14. This contradicts my initial hypothesis that the sharp increase in injuries can be attributed by the higher demands that player have today.  


Let's also take a look at which body parts tend to be injured the most in the NBA. 


**Fig. 7**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Basic%20EDA%20-%20Images%20%26%20Graphs/Uncombined%20Images/Frequency%20of%20Injuries%20by%20body%20part.png "Figure 7")

It appears that foot, knee and upper leg injuries are by far the most common injuries in the NBA. Let's see which body part tends to incur the most severe injuries. 

**Fig. 8**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Basic%20EDA%20-%20Images%20%26%20Graphs/Average%20Injury%20duration%20per%20body%20part.png "Figure 8")

This graph shows that a total of 6 body parts have an average injury duration that is over 30 days. Knee injuries tend to be the most severe injuries on average, with an average knee injury lasting ~49 days. In order to explore this further, let's break down the injuries by their severity and body parts alike. 

**Fig. 9**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Basic%20EDA%20-%20Images%20%26%20Graphs/Body%20Parts%20affected%20by%20Injuries%20-%20all%20categories.png "Figure 9")

It appears that foot and knee injuries are prominent in all the different categories of injuries. Figure 9 shows us that the average injury duration for knee and foot injuries (shown in Figure 8) are dragged down by the fact that there is a huge amount mild foot and knee injuries. 

## Analysis

From my hypothesis, I thought that there would be a strong correlation between the number of injuries per season and the average ACWR of the injured players. However, the graphs from Figure 3 and Figure 5 made me pessimistic that I would find a strong correlation. When calculating the correlation (link to the code [here](post the link to the EDA code)), the correlation coefficient was only  ~ -0.114. Not only was the correlation value close to 0 (signifying that it is a poor predictior), it was also negative! This indicates that I should probably take a deeper dive into what may be causing this increase injuries.

### Taking a deeper look at the ACWR

While the ACWR doesn't appear to have a strong correlation with the number of injuries, it is interesting to look at whether or not the ACWR plays a role in how serious an injury is. Below is a graph showing the average ACWRs of different injury severities. 


**Fig. 10**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/inj%20severity%20vs%20average%20acwr.png "Figure 10")

Surprisingly, there doesn't appear to be a large disparity between the average ACWRs of each injury severity category. I would imagine that players with higher ACWRs would tend to have more 'severe' injuries. To investigate this further, I also plotted a scatterplot of the ACWRs of players and their respective injury durations. 

**Fig. 11**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/inj%20severity%20vs%20average%20acwr.png "Figure 11")

When I calculated the correlation coefficient of a player's ACWR vs their injury duration, it came up to a very low 0.046. In other words, there doesn't appear to be a strong correlation between a player's ACWR and their injury duration. For good measure, I decided to run a linear regression to see if AWCR is a good predictor in how severe an injury is. This regression is detailed in Figures 12 and 13, Figure 12 is the plot of the linear regression and Figure 13 is its summary. The legend details the Pearson Correlation Coefficient between the two variables as well as the regression equation. 

**Fig. 12**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/AWCR%20vs%20Injury%20Duration%20-%20Linear%20Regression%20Plot.png "Figure 12")


**Fig. 13**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/AWCR%20vs%20Injury%20Duration%20-%20Linear%20Regression%20Summary.PNG "Figure 13")


At first glance, the model doesn't appear to be significant at all. The adjusted R-squared value is incredibly low at .2%. Similarly, the Pearson correlation coefficient is very low at only 0.046. That being said, the model's p-value is surprisingly low at 5.3%. While this is above the maximum threshold of 5%, it is important to note down how close the p-value was to being accepted. Overall, the model does not appear to be good, and the ACWR ratio does not appear to be a significant variable in predicting injury duration. However, since the ACWR was barely above the maximum accepted threshold, it may be useful to investigate it further as this project progresses. 


To further investigate the impact of ACWR on player injuries, I decided to see whether or not different body part's were more prone to getting injured if they had higher ACWRs. Figure 14 shows the average ACWR for the different injured body parts.

**Fig. 14**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Average%20AWCRs%20of%20each%20body%20part.png
 "Figure 14")

The average ACWR appears to be quite consistent across each body part. This may be due to the high amount of injuries 

I then decided to see whether or not high ACWRs caused particular body parts to be injury prone. I split the ACWRs into 3 groups illustrated in Table 2. Figure 15 shows the number of injuries in each 'ACWR' range, while Figure 16 illustrates the number of injuries for each body part in each ACWR range. 

**Table 2**:

| Range              | ACWR            |
| -------------------|:---------------:|
| Low                |  0 - 0.5        |
| Average            |  0.5 - 1.5      |
| High               |  1.5 +          |


**Fig. 15**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Number%20of%20injuries%20based%20on%20ACWR%20Range.png "Figure 15")




**Fig. 16**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Injury%20Parts%20for%20players%20w%20COMBINED%20ACWRs.png "Figure 16")

From above, the ACWR graphs look extremely similar for each 'range' of the ACWR. In Figure 17, I explore what happens if we also look at the average injury duration per body part depending on the ACWR.

**Fig. 17**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Injury%20Duration%20by%20body%20part%20-%20COMBINED%20ACWR.png "Figure 17")

In this graph, we see that high ACWRs appear to give more severe knee, lower leg and upper body injuries. Let's look at what happens if we _only_ consider knee, lower leg and upper body injuries. Will there be a trend? I ran a linear regression model to find out. 


**Fig. 18**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/only%20kneeupperbodylowerleg%20inj%20-%20AWCR%20vs%20Injury%20Duration%20-%20Linear%20Regression%20Plot.PNG "Figure 18")

**Fig. 19**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/only%20kneeupperbodylowerleg%20inj%20-%20AWCR%20vs%20Injury%20Duration%20-%20Linear%20Regression%20Summary.PNG "Figure 19")


From the linear regression model. the ACWR does not appear to have an effect on the injury durations of knee, upper body and lower leg injuries. The correlation coefficient between the ACWR and injury duration when we only considered these specific injuries was only 0.073. While it was better that the 0.046 correlation coefficient we found when considering _all_ injuries, the p-value was significantly worse at 9.4% (which is well above the maximum 5% threshold), and the adjusted R-Squared value was only 0.3%. As a result, I can conlude that this linear regression model does not appear to do a good job at predicting the injury durations for knee, lower leg and upper body injuries. Additionally, the AWCR does does appear to be a significant variable for predicting the durations of the aforementioned injuries.


I was also curious to see if I could find any patterns between a player's injury history and their AWCR. I would assume that player's who incurred injuries with lower ACWRs tend to have more serious injury histories. Whereas players with high ACWRs would have less serious injury histories (as their injury was primarily caused by being overworked). Figures 20 and 21 show the different types of injury histories that player's had for the low, average and high AWCR groups. 




**Fig. 20**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/number%20of%20injuries%20vs%20body%20part's%20injury%20history%20(duration)%20-%20COMBINED%20acwr.png "Figure 20")



**Fig. 21**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/number%20of%20injuries%20vs%20body%20part's%20injury%20count%20-%20COMBINED%20acwr.png "Figure 21")



It appears as if the ACWR range does not appear to make players more or less injury prone based on their injury history. That being said, Figure 21 seems to be showing an interesting trend. The vast majority of injuries tend to occur when a player has incurred injuries in the past to that affected body part. Thus, I think it is time to move on and explore the significance of injury history on players.


### Exploring significance of the significance of a player's injury history


Every sports fan knows how worrisome a player's injury history can be. Considering that AWCR doesn't appear to have a big impact on injuries, it is worth studying how important a player's injury history is. As previously mentioned, I considered two different types of injury histories: the total amount of time a player was out (their cumulative injury duration) and the total number of times a player was injured (their injury counts).

To be even more precise, I will be considering a player's injury history to their entire body, and a player's injury history to just the affected body part. The injury histories were split into different 'ranges' that are explained in Tables 3 and 4.

**Table 3**:                                                                              

| Injury History Severity  | Injury History (days) |
| -------------------------|:---------------------:|
| Little-No                 | 0-28                 |
| Mild                     | 28-90                 |
| Moderate                 | 90-180                |
| Serious                  | 180+                  |


**Table 4**:

| Injury History Severity  | Injury History (count) |
| -------------------------|:----------------------:|
| No                       | 0                      |
| Mild                     | 1-3                    |
| Moderate                 | 3-5                    |
| Serious                  | 5+                     |


In Figures 22 and 23, I broke down the number of injuries in each category of injury history severity. Figure 22 focuses only on the player's injury history to that specific body part. Whereas Figure 23 considers the player's _total_ injury history (all body parts).  

**Fig. 22**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/num%20of%20inj%20based%20on%20body%20part%20COMBINED.png "Figure 22")

**Fig. 23**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/num%20of%20injuries%20based%20on%20total%20COMBINED.png "Figure 23")


In Figures 24 and 25, I break down the average injury duration depending on the category of the player's injury history severity. Figure 20 focuses only on the player's injury history to that specific body part. Whereas Figure 21 considers the player's _total_ injury history (all body parts).  


**Fig. 24**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Avg%20Duration%20based%20on%20body%20part%20COMBINED.png "Figure 24")

**Fig. 25**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Avg%20Duration%20based%20on%20total%20COMBINED.png "Figure 25")

As you can see from the above graphs, there are pretty notable differences between the 'types' of injury histories we use. More specifically, the 'Mild' categories in both the 'count' and 'duration' versions of the injury histories are defined differently. The 'Mild' category of injury histories is defined as when a player has incurred between 1-3 injuries in the 'count' version. Whereas in the 'duration' version, a 'Mild' injury history is when a player has incurred between 28-90 days of an injury. This is likely what explains the large discrepancy between the two graphs. 

Unsurprisingly, when considering only the injury history to specific body parts (Figure 24), we yield higher average injury durations  in each category of injury history severity than when we consider the total injury history of a player (Figure 25). The only exception to this are the 'Little-No' and 'No' Injury history categories which yield approximately the same average injury durations. 

The figure which displays the most interesting trend to me thus far is Figure 24. There appears to be a visible relationship between how many injuries (injury count) a player has previously incurred to the body part they injured and the duration of the new injury. This is worth investigating further. Figure 25 also displays a similar trend with the total injury counts, but it is not as evident as the one found in Figure 24. However, it is still worth investigating as well. 

In order to investigate the relationship between a player's injury count and the injury duration they incurred, I plotted a linear regression to see if there truly is a correlation between the two variables. Figure 26 is a scatter plot of the body part's injury count (x-axis) and the duration of the injury with a linear regression line plotted over it. The 'p' value in the legend of the graph is the Pearson correlation coefficient, and the equation just below it is the calculated regression equation (where x is body part's injury count). Figure 27 is the summary report of the aforementioned linear regression.


**Fig. 26**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Body%20Inj.%20Count%20vs%20Injury%20Duration%20-%20regression%20plot.png "Figure 26")


**Fig. 27**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Body%20Inj.%20Count%20vs%20Injury%20Duration%20-%20regression%20results.PNG "Figure 27")


The linear regression above yielded some interesting results. While the Pearson correlation coefficient was low at 0.077, the regression model had an incredibly low p-value at 0.1%. The adjusted R-squared value was also incredibly low at 0.5%. Since the P-value was low, we can conclude that the injury count of the body part is _significant_. However, the low adjusted R-squared value indicates that our model does not explain a lot of the variation in the data. That means that when constructing other models, we should consider including the body part's injury count as a variable since it has proven to be significant. However, a model _only_ considering the body part's injury count does not appear to be enough to predict a player's injury duration. 



I also investigated the relationship between the player's total injury count and the duration of their injury. Figure 28 is a scatter plot of the player's total injury count (x-axis) and the duration of the injury with a linear regression line plotted over it. Figure 29 is the summary report of the linear regression in Figure 28. 


**Fig. 28**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Total%20Num.%20of%20Injuries%20vs%20Injury%20duration%20-%20regression%20plot.png "Figure 28")


**Fig. 29**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Total%20Num.%20of%20Injuries%20vs%20Injury%20duration%20-%20regression%20Results.PNG "Figure 29")

The graph indicates that there is no correlation between the player's total injury count and the injury duration. The Pearson correlation coefficient is only 0.008. Furthermore, the linear model's p-value is 74.6%, which is well above the 5% threshold. Finally, the adjusted R-Score is at -.001, which further emphasizes the point that the linear model is _not_ a good fit. So I can conclude that there appears to be no linear relationship between a player's total injury count and their injury duration.

Overall, the only 'significant' predictor we have found so far in predicting injury durations was the injury count of the player. While the ACWR ratio doesn't appear to be particularly significant in predicting injury durations, perhaps the average distances travelled by players may prove useful in predicting injury durations. 


### Investigating the impacts of distance traveled by players in-game

Finally, I decided to investigate the impact of the distance traveled by players on the severity of their injuries. I quickly wanted to see how much the average distance traveled by players has changed over the past few seasons. Figure 30 details the average distance traveled by all NBA players immediately prior to injury (in miles). 


**Fig. 30**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Average%20Distance%20Traveled%20Graphs/avg%20distance%20traveled%20per%20season%20-%20combined.png
 "Figure 30")

From the above graph, you can see that there does not appear to be a _large_ difference between the average distance traveled between the seasons (for both the 7 and 28 day averages). However, the 2018-19 season does appear to have the largest average distance traveled for both the 7 and 28 day average distances. Coincidentally, this is also the year that the NBA viewed the most injuries in the regular season from our sample size (see Figure 30). 

This marginal increase in distance traveled can be explained by the NBA's pace of play increasing drastically over the past few years. It is possible that the acute-chronic workload ratio has remained the same over the past few years simply because players are expected to run more _all_ the time. So, I want to see if the players who incurred more severe injuries are running more on average. Figure 31 contains the scatter plots of the average distance traveled by a player immediately prior to an injury (both the 7 day and 28 day averages) compared to their injury duration. 

**Fig. 31**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Average%20Distance%20Traveled%20Graphs/Scatter%20plot%20-%20COMBINED%20dayavg%20distance%20traveled%20vs%20injury%20duration.png "Figure 31")

From the above scatter plot, there does not seem to be a visible relationship between the average distance traveled and the injury durations. In order to investigate this further, I plotted the average distance traveled for each category of injury severity (the categories are broken down in Table 1).


**Fig. 32**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Average%20Distance%20Traveled%20Graphs/injury%20severity%20vs%20avg%20distance%20traveled%20-%20bar%20-%20combined.png "Figure 32")

From the above bar graphs, there does not appear to be a significant difference in the averages between each injury severity category. However, mild injuries appear to have larger average distances traveled over the past 28 days than the other injury categories. To check whether or not there was a correlation between injury severity and average distance traveled, I ran a linear regression to see how well the average distance traveled can predict injury severity. Figures 32 and 33 show the plot and the summary of the linear regression ran when comparing the 7 day average of the distance traveled and the injury duration. 


**Fig. 33**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Average%20Distance%20Traveled%20Graphs/Distance%20Traveled%20(7%20day%20Average)%20vs%20Injury%20Duration%20-%20regression%20plot.png "Figure 33")



**Fig. 33**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Average%20Distance%20Traveled%20Graphs/Distance%20Traveled%20(7%20day%20Average)%20vs%20Injury%20Duration%20-%20regression%20summary.PNG "Figure 34")


From the above figures, you can see that there is little to no correlation between the two variables. The Pearson correlation coefficient was low at -0.042. Additionally, the p-value came out to be 7.8%, which is well above the maximum threshold of 5%. And finally, the adjusted R-squared value came in at only 0.001. As a result, I can conclude that this is not a good linear regression model and that the average distance traveled over the past 7 days is not a significant variable when it comes to predicting the total injury duration.

I ran the same linear regression with the average distance traveled over 28 days to see whether or not there is more of a relationship between the two variables.

**Fig. 34**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Average%20Distance%20Traveled%20Graphs/Distance%20Traveled%20(28%20day%20Average)%20vs%20Injury%20Duration%20-%20regression%20plot.png "Figure 34")



**Fig. 35**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Average%20Distance%20Traveled%20Graphs/Distance%20Traveled%20(28%20day%20Average)%20vs%20Injury%20Duration%20-%20regression%20summary.PNG "Figure 35")


From the above graphs, you can see that the results we yielded with the 28 day average are similar to the ones we got with the 7 day average. The Pearson correlation coefficient is low at -0.031. The p-value was 18.7%, which is much higher than the maximum threshold of 5%. Finally, the adjusted R-Squared value rounded out to be 0.00. Overall, I can come to the same conclusion for this model as I did for the model using the 7 day average. This is not a suitable model to predict the injury duration of the player and the average distance traveled over the past 28 days is not a significant variable when predicting a palyer's injury duration. 


### Wrapping it all up

From our analysis so far, the only variable that proved to be significant in predicting a player's overall injury duration was the amount of injuries the player has previous incurred to that body part (their body part's injury count). That being said, the ACWR ratio was almost considered significant since its' p-value came in at only 5.3%. Before reaching a conclusion in my experiment, I decided to run a multiple linear regression to see how well a model considering both the player's ACWR _and_ body part injury count can predict a player's injury duration. Figure 36 is the summary of this model. 


**Fig. 36**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/wrapping%20it%20all%20up/MultipleLinearRegression.PNG
 "Figure 36")

From above, this linear model still does not appear to be a good one. While the p-value of the body part's injury count is still incredibly low at 0.1%, the p-value of the AWCR still remains slightly above the 5% threshold at 5.7%. Additionally, the adjusted R-squared value is still incredibly low at 0.008. Overall, the model does not appear to predict the duration of injuries particularly well. However, I can conclude that the body part's injury count appears to be a significant factor in predicting how severe an injury is.  



While I did not succeed in finding a successful model to build injury duration, I still wanted to see how feasible alternations to the NBA schedule would be. Currently, the NBA has an 82 game schedule (the 2019-20 and 2020-21 seasons were shortened due to Covid). Each NBA conference has 15 teams, and each NBA division has 5 teams, all teams within a division share the same conference as well. In the 82 game schedule, a team faces teams within their conference 4 times a year (amounting to a total of 16 games played within their division). They then play the remaining teams  teams within their conference 3-4 times a year (amounting to a total of 36 games played within their conference, excluding their division). Finally, they play the opposing conference 2 times a year during the regular season (amounting to a total of 30 games). 

The alternatives that I think may be viable for the NBA are the following:

1. The 58 game season

⋅⋅⋅ In the 58 game season, teams would play each team in the league (29 opposing teams) twice, once at home, and once away.


2. The 62 game season

⋅⋅⋅ In the 62 game season, teams would play teams within their division 3 times a year (with at least one game at home, and one game away). They would play the remaining 25 teams twice a year, with one home and one away game. 


2. The 72 game season

⋅⋅⋅ In the 72 game season is what the NBA implemented for the 2020-21 Regular Season. Each team would play teams within their conference 3 times a year (with at least one game at home and one game away). They would play the opposing conference only twice a year, with one home game and one away game.


To test how feasible these schedule alternations are, I calculated what would the NBA Regular Season standings would be for each model for the seasons from 2013 until 2019. I then calculated what % of the playoff teams that made the playoffs in my alternate schedule made the playoffs under the regular 82 game season. The % of teams that made the playoffs in both the alternate schedule and the regular 82 game schedule will be considered how 'accurate' the model was. I did not consider how accurate the models predicted the team's placement on the standings. I only considered whether the model was good at predicting which teams are / aren't playoff teams. To determine tie-breakers, I used the NBA rules for tie-breakers that can be found [here](https://ak-static-int.nba.com/wp-content/uploads/sites/2/2017/06/NBA_Tiebreaker_Procedures.pdf).

Below is a graph depicting how accurate the model was for each season from 2013. Table 5 details the average accuracy of each model.


**Fig. 37**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/alternate%20regular%20season%20models%20accuracy.png
 "Figure 37")




**Table 5**:                                                                              

| Alternate Regular Season Model  | Average Accuracy (%) |
| --------------------------------|:--------------------:|
| 58 Game Model                   | 93.75%               |
| 62 Game Model                   | 90.625%              |
| 72 Game Model                   | 96.875%              |



Overall, all the models appear to be fairly accurate. Personally, I think the model that would make the most sense to implement would be the 62 game model. That way, conferences and divisions are still relevant, and the players play 20 fewer games per year (which is approximately 25% less as opposed to the regular season). While my model did not find a direct link between workload and injury severity, it is still very plausible that increased workload could increase the _number_ of injuries per year, so these alternatives may be useful for the NBA to consider in order to prevent injuries. 

## Limitations

My study had many limitations. Firstly, the 'average distance traveled' data I gathered was not entirely accurate as I have no data as to how much a player trains in practice/outside of the NBA. It is very possible that players exert a lot of effort off the court which causes them to incur serious injuries. My calculations of the ACWR assumed that players only exerted effort on the court, which is simply unrealistic. Additionally, only considering the distance traveled by a player isn't the most accurate representation of how hard a player works. Certain players may partake in other effort exerting activities on the court (example: dunking a ball) which is not considered. Given that I only used publicly available data, the experiment was limited in its accuracy.

Another major limitation I had was the fact that the NBA only started to record the distances traveled by players in 2013. It would have been more useful if I conducted an experiment determining whether or not the acute-chronic workload ratio or the average distance traveled by players were somehow correlated with the _number_ of injuries per season. Since I only had data since 2013, this would not have been feasible since I was limited to less than 10 seasons worth of data. Figure 30 did show that NBA players the average distance traveled by players immediately before incurring an injury has increased over the past few years. Similarly, we have also seen an increase in the number of injuries that players have incurred during that same timeframe (Figure 3). Thus, this is a topic worth investigating in the future as we get a better sample size.

Finally, another error that my model had was that I did not compare the ACWRs/average distances traveled of the injured players to the players who were not injured during that same timeframe. It would have been useful to see how much players who _do not_ get injured work when compared to the players that _do_ get injured. It is possible that players who do not get injured get limited playing time.

Finally, I did not consider other potentially important factors when predicting the severity of injuries. In the future, it would be useful to also see how a player's height and age correlate to the severity of the injuries. Also, it may have been useful to also include the injuries players have incurred prior to entering the NBA (ex: NCAA, high school basketball, Euroleague, etc). The project was constructed on the assumption that players entered the NBA with no prior injury history, which is a strong and unrealistic assumption. 



## Conclusion

Overall, the only significant variable I found that had an effect on injury duration was the number of previous injuries a player has incurred to that body part. In my hypothesis, I believed that the acute-chronic workload ratio would have a significant effect on injury severity. However, it appears that that is not the case. One reason for this may be that teams already keep track of a player's ACWR and make sure that a player does not play so much as to become 'overworked'. 

The study had a lot of limitations, and in future updates to the project I will try to investigate other causes that might increase the severity of injuries (ex: height). Additionally, I think it is also important to investigate what causes an _increased_ number of injuries. A majority of the injuries in the NBA knocks that put a player out for 1-2 weeks. It's very likely that those knocks may be a result of the increasing physical demands an NBA player has to meet. 





