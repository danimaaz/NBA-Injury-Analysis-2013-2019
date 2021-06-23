# NBA Injury Analysis 2013-2019

## Introduction
Recently, NBA personalities have complained that the players in the league are 'overworked' and that that results in preventable injuries. In this report, I sought out to investigate whether or not there is any truth to that general sentiment. Additionally, I also explored whether or not the number of games played in the NBA regular season is necessary for determining the playoff picture, and if reducing the number of games would be a feasible solution to players feeling 'overworked'. 



## Methodology

Prior to gathering the data, I had to decide exactly how I would classify a player's 'workload' and whether or not they were overworked. Upon reading [these](https://pubmed.ncbi.nlm.nih.gov/32125672/) [two](https://www.scienceforsport.com/acutechronic-workload-ratio/) articles, I decided that the best method would be to track what a player's acute-chronic workload ratio (ACWR for short) was at the time of the injury. The ACWR ratio monitors how much effort the athlete has recently exerted vs how much effort the athlete is _used_ to exerting. Generally, sports scientists use the average workload the athlete has exerted over the past 7 days  and 28 days as the 'recent effort' and 'normal effort' respectively. An ACWR above 1 indicates that an athlete has exerted more effort on average over the past 7 days than over the past 28 days. Similarly,  ACWR below 1 indicates that an athlete has exerted less effort on average over the past 7 days than over the past 28 days. And finally, an ACWR of 1 indicates that an athlete has exerted the exact same amount of effort on average the past 7 days as he has over the past 28 days. 

Classifying exactly what an NBA player's workload is was tricky. Since I have no access to the training data of individual players, I was forced to resort to statistics that are readily available to the public on NBA.com. I decided that using the in-game distances a player has traveled would be the most appropriate metric to define as a player's 'workload'. 

Next, I had to decide which timeframes I had to use to conduct this analysis. The NBA only started tracking the distance traveled by players during the 2013-14 season, so I was restricted between the years of 2013-2021. Considering that the 2019-20 and 2020-21 seasons were heavily affected by the onset of COVID-19, I decided to only conduct my analysis on the seasons that occured between 2013-2019.

While my analysis was only on data from 2013-2019, I also had to consider that a player's injury history may play a large role in how susceptible they are to injuries. Thus, I gathered injury data from 1992-2019 to ensure that every player in the NBA would have their true injury history recorded. 



## Hypothesis

Being an avid NBA fan over the past 10 years (Go Knicks!), the eye test tells me that injuries are indeed on the rise. To me, it feels like an important player is getting injured every other week. When I was younger, important players seemed to rarely get injured and injuries were viewed more as like 'freak accident'. Additionally, the league pace of the league has also increased significantly over the past 10 years due to the rise of 'small ball' strategies (teams opting to play the smaller and quicker players). Since teams are playing a lot faster than they have before, I do suspect that many injuries can be attributed to the tough 82 game schedule. Also, the eye test tells me that the most notable injuries seem to happen when players are in the middle of a rough patch in the team's schedule. Since more players are expected to run more each game, a week with 4-5 games can be incredibly taxing on a player. Thus I do expect that we will see a strong correlation between the average ACWR of players and number of injuries.



### Gathering the injury data
Using Python's Beautiful Soup package, I was able to extract the necessary injury data from [ProSportsTransactions.com](prosportstransactions.com). I only gathered data from the 'Movement to/from injured/inactive list (IL)' portion of the transactions.

Below is a screenshot of how the data looked like once I scraped it. 

**Fig. 1**:

![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019/blob/main/Images%20and%20Graphs/example%20sc.PNG "Figure 1")

As you can see, there are only 5 columns: Date, Team, Acquired, Relinquished and Notes. 
The Acquired column indicated which player was being _removed_ from the injury list on that date, whereas the Relinquished column indicated which player was being _added_ to the injury list on that date. The Notes column indicated why a player was being placed on the injury list. 

Scrolling through the data, there were quite a problems with it. Firstly, many players were 'Placed on the Injury List' (or 'Relinquished') but they were never removed from it. Additionally, a lot of the data did not specify why a player was being placed on the injury list. I suspect that a lot of teams would use the injury list to have their players 'rest' but not specify it.

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

When I calculated the correlation coefficient of a player's ACWR vs their injury duration, it came up to an abysmal 0.046. In other words, there doesn't appear to be a strong correlation between a player's ACWR and their injury duration. 

Since there doesn't appear to be any visible relationship between a player's ACWR and their injury duration, I decided to investigate whether or not different body part's were more prone to getting injured if they had higher ACWRs. Figure 12 shows the average ACWR for the different injured body parts.

**Fig. 12**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Average%20AWCRs%20of%20each%20body%20part.png
png "Figure 12")

The average ACWR appears to be quite consistent across each body part. This may be due to the high amount of injuries 

I then decided to see whether or not high ACWRs caused particular body parts to be injury prone. I split the ACWRs into 3 groups illustrated in Table 2. Figure 13 shows the number of injuries in each 'ACWR' range Figure 14 illustrates the number of injuries for each body part in each ACWR range. 

**Table 2**:

| Range              | ACWR            |
| -------------------|:---------------:|
| Low                |  0 - 0.5        |
| Average            |  0.5 - 1.5      |
| High               |  1.5 +          |


**Fig. 13**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Number%20of%20injuries%20based%20on%20ACWR%20Range.png "Figure 13")




**Fig. 14**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Injury%20Parts%20for%20players%20w%20COMBINED%20ACWRs.png "Figure 14")

From above, the ACWR graphs look extremely similar for each 'range' of the ACWR. In Figure 13, I explore what happens if we also look at the average injury duration per body part depending on the ACWR.

**Fig. 15**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Injury%20Duration%20by%20body%20part%20-%20COMBINED%20ACWR.png "Figure 15")

In this graph, we see that high ACWRs appear to give more severe knee, lower leg and upper body injuries. Let's look at what happens if we _only_ consider knee, lower leg and upper body injuries. Will there be a trend? 


**Fig. 16**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/Injury%20durations%20vs%20ACWR%20-%20knee%20lower%20leg%20and%20upper%20body.png "Figure 14")

From the scatter plot above, there doesn't seem to be any visible correlation. The correlation coefficient between the ACWR and injury duration when we only considered these specific injuries was only 0.073. While it is still better than 0.046 correlation coefficient that I calculated when consideirng *all* injuries, it is still far too small to draw any conclusions from it. 

I was also curious to see if I could find any patterns between a player's injury history and their AWCR. I would assume that player's who incurred injuries with lower ACWRs tend to have more serious injury histories. Whereas players with high ACWRs would have less serious injury histories (as their injury was primarily caused by being overworked). Figures 17 and 18 show the different types of injury histories that player's had for the low, average and high AWCR groups. 




**Fig. 16**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/number%20of%20injuries%20vs%20body%20part's%20injury%20history%20(duration)%20-%20COMBINED%20acwr.png "Figure 16")



**Fig. 17**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/ACWR-graphs/number%20of%20injuries%20vs%20body%20part's%20injury%20count%20-%20COMBINED%20acwr.png "Figure 17")



It appears as if the ACWR range does not appear to make players more or less injury prone based on their injury history. That being said, Figure 17 seems to be showing an interesting trend. The vast majority of injuries tend to occur when a player has incurred injuries in the past to that affected body part. Thus, I think it is time to move on and explore the significance of injury history on players.


### Taking a deeper look at the significance of an injury history


Every sports fan knows how worrisome a player's injury history can be. Considering that AWCR doesn't appear to have a big impact on injuries, it is worth studying how important a player's injury history is. As previously mentioned, I considered two different types of injury histories: the total amount of time a player was out (their cumulative injury duration) and the total number of times a player was injured (their injury counts).

To be even more precise, I will be considering a player's injury history to their entire body, and a player's injury history to just the affected body part. The injury histories were split into different 'ranges' that are explained in Tables 3 and 4.

**Table 3**:                                                                              

| Injury History Severity  | Injury History (days) |
| -------------------------|:---------------------:|
| Little-No                 | 0-28                  |
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


In Figures 18 and 19, I broke down the number of injuries in each category of injury history severity. Figure 18 focuses only on the player's injury history to that specific body part. Whereas Figure 19 considers the player's _total_ injury history (all body parts).  

**Fig. 18**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/num%20of%20inj%20based%20on%20body%20part%20COMBINED.png "Figure 18")

**Fig. 19**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/num%20of%20injuries%20based%20on%20total%20COMBINED.png "Figure 19")


In Figures 20 and 21, I break down the average injury duration depending on the category of the player's injury history severity. Figure 20 focuses only on the player's injury history to that specific body part. Whereas Figure 21 considers the player's _total_ injury history (all body parts).  


**Fig. 20**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Avg%20Duration%20based%20on%20body%20part%20COMBINED.png "Figure 20")

**Fig. 21**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Avg%20Duration%20based%20on%20total%20COMBINED.png "Figure 21")

As you can see from the above graphs, there are pretty notable differences between the 'types' of injury histories we use. More specifically, the 'Mild' categories in both the 'count' and 'duration' versions of the injury histories are defined differently. The 'Mild' category of injury histories is defined as when a player has incurred between 1-3 injuries in the 'count' version. Whereas in the 'duration' version, a 'Mild' injury history is when a player has incurred between 28-90 days of an injury. This is likely what explains the large discrepancy between the two graphs. 

Unsurprisingly, when considering only the injury history to specific body parts, we yield higher average injury durations (Figures 20 and 21) in each category of injury history severity than when we consider the total injury history of a player. The only exception to this are the 'Little-No' and 'No' Injury history categories which yield approximately the same average injury durations. 

The figure which displays the most interesting trend to me thus far is Figure 20. There appears to be a visible relationship between how many injuries (injury count) a player has previously incurred to the body part they injured and the duration of the new injury. This is worth investigating further. Figure 21 also displays a similar trend with the total injury counts, but it is not as evident as the one found in Figure 20. However, it is still worth investigating as well. 

In order to investigate the relationship between a player's injury count and the injury duration they incurred, I plotted a linear regression to see if there truly is a correlation between the two variables. Figure 22 is a scatter plot of the body part's injury count (x-axis) and the duration of the injury with a linear regression line plotted over it. The 'p' value in the legend of the graph is the Pearson correlation coefficient, and the equation just below it is the calculated regression equation (where x is body part's injury count). Figure 23 is the summary report of the aforementioned linear regression.


**Fig. 22**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Body%20Inj.%20Count%20vs%20Injury%20Duration%20-%20regression%20plot.png "Figure 22")


**Fig. 23**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Body%20Inj.%20Count%20vs%20Injury%20Duration%20-%20regression%20results.PNG "Figure 23")


The linear regression above yielded some interesting results. While the Pearson correlation coefficient was low at 0.077, the regression model had an incredibly low p-value at 0.1%. The adjusted R-squared value was also incredibly low at 0.5%. Since the P-value was low, we can conclude that the injury count of the body part is _significant_. However, the low adjusted R-squared value indicates that our model does not explain a lot of the variation in the data. That means that when constructing other models, we should consider including the body part's injury count as a variable since it has proven to be significant. However, a model _only_ considering the body part's injury count does not appear to be enough to predict a player's injury duration. 



I also investigated the relationship between the player's total injury count and the duration of their injury. Figure 24 is a scatter plot of the player's total injury count (x-axis) and the duration of the injury with a linear regression line plotted over it. Figure 24 is the summary report of the linear regression in Figure 25. 


**Fig. 24**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Total%20Num.%20of%20Injuries%20vs%20Injury%20duration%20-%20regression%20plot.png "Figure 24")


**Fig. 25**:


![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019-/blob/main/Images%20and%20Graphs/Injury%20History%20Graphs/Total%20Num.%20of%20Injuries%20vs%20Injury%20duration%20-%20regression%20Results.PNG "Figure 25")

The graph indicates that there is no correlation between the player's total injury count and the injury duration. The Pearson correlation coefficient is only 0.008. Furthermore, the linear model's p-value is 74.6%, which is well above the 5% threshold. Finally, the adjusted R-Score is at -.001, which further emphasizes the point that the linear model is _not_ a good fit. So I can conclude that there appears to be no linear relationship between a player's total injury count and their injury duration.  

## Conclusion

## Limitations
