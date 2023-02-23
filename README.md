# League-of-Legends-Project
Project 3 for DSC 80

### Here we are trying to answer the question: is the "best" player in any given game, on the winning side?


# Introduction

We are utilizing a large data set of competitive match data from the 2022 season (containing 149,232 rows!) and using a specialized formula to determine the overall highest performing player in each game. The relevent columns we have are:

    - gameid: The specific id for each game (there are over 10,000!)
    - url: The link to the match summery data
    - gamelength: The length of a game (in seconds)
    - position: The position of a player on the team (could also be Teamm if something is considered a combined effort)
    - datacompleteness: Whether the data in the row is complete or missing
    - total cs: The total amount of non-player entities killed
    - result: The result of the match, 1 is win, 0 is loss
    - kills: The total amount of kills
    - deaths: The total amount of deaths
    - assists: The total amount of assisted kills
    - damagetochampions: The total amount of damage dealt to other champions (players)
    - earnedgold: The total amount of gold collected
    - visionscore: The amount of times your actions allowed your time to see otherwise unseen targets
    - damagemitigatedperminute: The amount of damage you healed per minute (to yourself or others)

It is important to note that we consider a player to be the best by reviewing their KDA, damagetochampions, earnedgold, visionscore, damagemitigated, and total_cs performance in their game. (more detail on this down below)

Answering this question is important as it highlights indivual performance and excellence within the competitive circuit. It motivates players to improve as there is verifyable data that suggests that the best teams thrive off of the best indivudual performers.


# Cleaning and EDA
The steps we took in cleaning our data consisted of first selecting our relevent columns and proceeding to clean those. 

Firstly, we selected our columns by passing in a list of the columns above. This helped us immensly in pinpointing the exact columns that we needed to answer our question. Once we selected these, we first began by querying out the position of 'team.' This is due to the fact that our particular analysis focuses on individidual performance rather than team effort. Additionally, we also edited the gamelength column by dividing it by 60 to  convert it from seconds to minutes; this was particularly useful in obtaining another column called damagemitigated by multiplying the gamelength column by the damagemitigatedperminute column. Lastly, we replaced all 0.0s with 1s in the deaths column so that we could create a new column called KDA (the Kill, Death, and Assist ratio); KDA serves as an aggregate statistic useful for finding the "best" player on either team. 

### Cleaned Dataframe (first few rows):
`print(relavent.head().to_markdown(index=False))`


## Univariate Analysis

### KDA analysis:
<iframe src="assets/KDA.html" width=800 height=600 frameBorder=0></iframe>

The above graph showcases the distributions of KDA in matches won (1) and matches lost (0). An apparent difference as the KDA is seen as, in the matches lost, we can see that most KDA for players are bunched up at the star (roughly 1 to 5), however, in the matches won, we can see that KDA can vary greatly going up to even 30+

### Damagetochampions analysis:
<iframe src="assets/DTC.html" width=800 height=600 frameBorder=0></iframe>

As opposed to the first graph (KDA UA), we can see here that the distribution of damagetochampions (DTC) is much more similar here. Despite being another factor in being the best player in a given match, it looks like, regardless of being on the winnning or losing team, players often deal similar amounts of damage to players.

## Bivariate Analysis

### Damage to Champions and Earned Gold
<iframe src="assets/DandEG.html" width=800 height=600 frameBorder=0></iframe>

Looking at the DTC and Earned Gold, we can determine that there is indeed a relationship. The above figure showcase a high correlation between DTC and Earned Gold; this is to be expected as players who do more damage are also more likely to have high amounts of gold. 

This further supports our manner for selecting the best player as statistics like this seem to have relationships with one another

### Intersting Aggregates

`print(match_sums.to_markdown(index=False))`

By grouping our cleaned dataframe by gameid (i.e match), we can utilize a transformation in order to get the sum of 'gameid','KDA', 'visionscore','earnedgold','damagetochampions', 'total cs', and 'damagemitigated' columns in each row. This helps us in optaining our aggregate statistic  MVPscore which is a new column optained by utilizing the grouped df (called match_sums) in order to turn the rows in the columns mentioned to proportions of theior performance per game. Using this, we calculate MVPscore by taking summing 10% of KDA, 20% DTC, 20% Earned Gold, 35% Vision Score, 15% Damage Mitigated, and 10% Total cs.

# Assessment of Missingness

## NMAR Analysis

We do not believe that any of the columns in our cleaned dataset fall under the criteria of Not Missing at Random. When a column has data that is NMAR, it means that the missingness of a value depends on the value itself; however, in the columns that do have missingness, this is not the case. MAYBE MORE

## Missingness Dependency

For analysis on missingness, we will utilize the 'url' column as it has the most significant missingness


### KDA distribution when url is missing
<iframe src="assets/KDA_URL.html" width=800 height=600 frameBorder=0></iframe>

The distribution of KDA when URL is missing and the distribution of KDA when URL is not missing.

This showcases a clear difference in the distributions; specifically, we can see that the distributions look similar
however, they showcase a disparity in KDA rows that have missing urls and those that don't. We tested if the missingness of game url is dependent on KDA using a permutation test with a 5% cut off p-value. Our test statistic was the diff in group means since the distributions look similar.

In this case, our hypothesis would be: 
- null: the distribution of KDA when url is missing is the same as the distribution of KDA when url is not missing

For this examination, we recieved a p-value of 0.013 meaning that we have enough evidence to reject the null or in other words, we can say that the missingness of url is dependent on KDA. While this was initially surprising, we can see that in the figure above, the distrobutions had an apparent similarity but were just different in size. 


We also examined if missingness of url was dependent on result (win or loss) where our test stastistic was TVD where we also utilized a permutation test with a 5% cut off p-value. 

In this case, our hypothesis would be: 
- null: the distribution of result when url is missing is the same as the distribution of result when url is not missing

### Empirical Distribution and Observed Statistic (result and url)
<iframe src="assets/empdis.html" width=800 height=600 frameBorder=0></iframe>
- Observed Test Statistic = 0.00001318771

The result of this test was also surprising as the p-value was 0.989 which means we do not have enough evidence to reject the notion that result is independent of url missingness. This was interesting as it seems consistent for the url to match data to be missing dependent on if the result was a loss or a win (perhaps heinous characters at play).

# Hypothesis Testing

Given that we calculated the MVP score (i.e the MVPscore column), which is the score that roughly grades the performance of a player
in their match, we performed a hypothesis test to answer our original question: is the best player always on the winning team? We believe this to be an accurate path to answering our question as MVP score takes into account not only things like KDA and damage dealt to other players (DTC), but also game stats like damage mitigated and vision score. This is done in order not to favor champions that specialize in dealing damage and take into account other aspects of being a good well rounded player; this also allows other champions and positions a shot at being the best!

We utilized a hypothesis test  with a 5% cut off p-value. The proportion of times that the best player (highest MVP per game) was on the winning side was the test statistic. This is similar to a hypothesis test done on a fair coin with an unfair coin as the observed; in which case, the proportion of the bias is valid.

In this case, our hypothesis were:
- Null: The 'best' player is just as likely to be on the winning team as the losing team
- Alternative: The 'best' player is more likely to be on the winning team

### Empirical Distribution and Observed Statistic (MVP)
<iframe src="assets/Hypo.html" width=800 height=600 frameBorder=0></iframe>
- Observed Test Statistic = 0.9412947326095698

As can be ascertained from the above graph, the p-value was low; as a matter of fact, it was 0! This means that we can reject the null that the best player is likely to be on either side as our observation (the proportion of best player being on the winning team) is not consistent with said null.


# Conclusion










