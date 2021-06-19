# NBA injury Analysis 2013-2019

## Introduction
Recently, NBA personalities have complained that the players in the league are 'overworked' and that that results in preventable injuries. In this report, I sought out to investigate whether or not there is any truth to that general sentiment. Additionally, I also explored whether or not the number of games played in the NBA regular season is necessary for determining the playoff picture, and if reducing the number of games would be a feasible solution to players feeling 'overworked'. 

## Methodology

Prior to gathering the data, I had to decide exactly how I would classify a player's 'workload' and whether or not they were overworked. Upon reading [these](https://pubmed.ncbi.nlm.nih.gov/32125672/) [two](https://www.scienceforsport.com/acutechronic-workload-ratio/) articles, I decided that the best method would be to track what a player's acute-chronic workload ratio (ACWR for short) was at the time of the injury. The ACWR ratio monitors how much effort the athlete has recently exerted vs how much effort the athlete is _used_ to exerting. Generally, sports scientists use the average workload the athlete has exerted over the past 7 days  and 28 days as the 'recent effort' and 'normal effort' respectively. Classifying exactly what an NBA player's workload is was tricky. Since I have no access to the training data of individual datas, I was forced to resort to statistics that are readily available to the public on NBA.com. I decided that using the in-game distances a player has traveled would be the most appropriate metric to define as a player's 'workload'. 

Next, I had to decide which timeframes I had to use to conduct this analysis. The NBA only started tracking the distance traveled by players during the 2013-14 season, so I was restricted between the years of 2013-2021. Considering that the 2019-20 and 2020-21 seasons were heavily affected by the onset of COVID-19, I decided to only conduct my analysis on the seasons that occured between 2013-2019.

While my analysis was only on data from 2013-2019, I also had to consider that a player's injury history may play a large role in how susceptible they are to injuries. Thus, I gathered injury data from 1992-2019 to ensure that every player in the NBA would have their true injury history recorded. 

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
The player injury history would specify which how many times the player has injured each body part as well as the cumulative number of days a player was out due to an injury on said body part. 

Once I was finished compiling the player injury history data, I was ready to merge it with the cleaned 2013-2019 injury data. The player injury history dataset was continuously updated to consider the new injuries players incurred as well as the new players that entered the league after 2013.


### Gathering the workload data

Once I incordporated the injury history of the players' into my 2013-2019 data, I was almost ready to analyze it. The last thing I had to do was gather the in-game distances traveled by the players over the past 7 and 28 days. I used the [NBA API](https://github.com/swar/nba_api) library to help me with extract the necessary data. Once I finished scraping the necessary data, the data was finally ready for analysis. Below is a screencap of the data right before I started the analysis:

**Fig. 2**:
![alt text](https://github.com/danimaaz/NBA-Injury-Analysis-2013-2019/blob/main/Images%20and%20Graphs/ready_for_analysis.PNG "Figure 1")

## Analysis

## Conclusion

## Limitations
