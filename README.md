# League of Legends Game Analysis


### In this project, we draw data from Oracle's Elixir's League of Legends competitive match dataset to answer the question: is the "best" player in any given game, on the winning side?


# Introduction


We are utilizing a large data set of competitive match data from the 2022 season (containing 149,232 rows!) and using a specialized formula to determine the overall highest-performing player in each game. The relevant columns we have are:


   - gameid: The specific id for each game (there are over 10,000!)
   - url: The link to the match summary data
   - gamelength: The length of a game (in seconds)
   - position: The position of a player on the team (could also be Teamm if something is considered a combined effort)
   - datacompleteness: Whether the data in the row is complete or missing
   - total cs: The total amount of non-player entities killed
   - result: The result of the match, 1 is a win, 0 is a loss
   - kills: The total amount of kills
   - deaths: The total amount of deaths
   - assists: The total amount of assisted kills
   - damagetochampions: The total amount of damage dealt to other champions (players)
   - earnedgold: The total amount of gold collected
   - visionscore: The amount of times your actions allowed your time to see otherwise unseen targets
   - damagemitigatedperminute: The amount of damage you healed per minute (to yourself or others)


It is important to note that we consider a player to be the best by reviewing their KDA, damagetochampions, earnedgold, visionscore, damagemitigated, and total_cs performance in their game. (more detail on this down below)


Answering this question is important as it highlights individual performance and excellence within the competitive circuit. It motivates players to improve as there is verifiable data that suggests that the best teams thrive off of the best individual performers.




# Cleaning and EDA
The steps we took in cleaning our data consisted of first selecting our relevant columns and proceeding to clean those.


Firstly, we selected our columns by passing in a list of the columns above. This helped us immensely in pinpointing the exact columns that we needed to answer our question. Once we selected these, we first began by querying out the position of 'team.' This is because our particular analysis focuses on individual performance rather than team effort. Additionally, we also edited the game length column by dividing it by 60 to convert it from seconds to minutes; this was particularly useful in obtaining another column called damagemitigated by multiplying the game length column by the damagemitigatedperminute column. Lastly, we replaced all 0.0s with 1s in the deaths column so that we could create a new column called KDA (the Kill, Death, and Assist ratio); KDA serves as an aggregate statistic useful for finding the "best" player on either team.


## Cleaned Dataframe (first few rows):


| gameid                |   url | datacompleteness   |   gamelength | position   |   total cs |   result |   kills |   deaths |   assists |   damagetochampions |   earnedgold |   visionscore |   damagemitigated |     KDA |
|:----------------------|------:|:-------------------|-------------:|:-----------|-----------:|---------:|--------:|---------:|----------:|--------------------:|-------------:|--------------:|------------------:|--------:|
| ESPORTSTMNT01_2690210 |   nan | complete           |        28.55 | top        |        231 |        0 |       2 |        3 |         2 |               15768 |         7164 |            26 |             22206 | 1.33333 |
| ESPORTSTMNT01_2690210 |   nan | complete           |        28.55 | jng        |        148 |        0 |       2 |        5 |         6 |               11765 |         5368 |            48 |             18562 | 1.6     |
| ESPORTSTMNT01_2690210 |   nan | complete           |        28.55 | mid        |        193 |        0 |       2 |        2 |         3 |               14258 |         5945 |            29 |              6503 | 2.5     |
| ESPORTSTMNT01_2690210 |   nan | complete           |        28.55 | bot        |        226 |        0 |       2 |        4 |         2 |               11106 |         6835 |            25 |              6249 | 1       |
| ESPORTSTMNT01_2690210 |   nan | complete           |        28.55 | sup        |         42 |        0 |       1 |        5 |         6 |                3663 |         2908 |            69 |             13993 | 1.4     |






## Univariate Analysis


### KDA analysis:
<iframe src="assets/KDA.html" width=800 height=600 frameBorder=0></iframe>


The above graph showcases the distributions of KDA in matches won (1) and matches lost (0). There is a clear difference in scale and spread of the 2 distributions. We can see that the vast majority of players had a KDA between 0-5 and of those, an overwhelming majority of those players lost. Conversely, we can see that as KDA increased (up to 30+), the players tended to be on the winning team.


### Damagetochampions analysis:
<iframe src="assets/DTC.html" width=800 height=600 frameBorder=0></iframe>


As opposed to the first graph (KDA UA), we can see here that the distribution of damagetochampions (DTC) is much more similar here. Despite it being a metric that can be skewed by player position and other factors, it looks like, regardless of being on the winning or losing team, players often deal similar amounts of damage to each other.


## Bivariate Analysis


### Damage to Champions and Earned Gold
<iframe src="assets/DandEG.html" width=800 height=600 frameBorder=0></iframe>


Looking at the DTC and Earned Gold, we can determine that there is a positive linear relationship. The above figure showcases a high correlation between DTC and Earned Gold; this is to be expected as players who do more damage tend to earn more gold which in turn allows them to upgrade items and do even more damage.


This further supports our formula for determining the best player as statistics like this seem to have relationships with one another


### Interesting Aggregates (first few rows)


|     KDA |   visionscore |   earnedgold |   damagetochampions |   total cs |   damagemitigated |
|--------:|--------------:|-------------:|--------------------:|-----------:|------------------:|
| 58.8333 |           402 |        61987 |              136472 |       1816 |            149518 |
| 58.8333 |           402 |        61987 |              136472 |       1816 |            149518 |
| 58.8333 |           402 |        61987 |              136472 |       1816 |            149518 |
| 58.8333 |           402 |        61987 |              136472 |       1816 |            149518 |
| 58.8333 |           402 |        61987 |              136472 |       1816 |            149518 |




By grouping our cleaned data frame by gameid (i.e match), we can utilize a transformation to get the sum of 'gameid','KDA', 'visionscore','earnedgold','damagetochampions', 'total cs', and 'damagemitigated' columns in each row. This helps us in obtaining our aggregate statistic  MVPscore which is a new column obtained by utilizing the grouped df (called match_sums) to turn the rows in the columns mentioned to proportions of their performance per game. Using this, we calculate MVPscore by taking summing 10% of KDA, 20% DTC, 20% Earned Gold, 35% Vision Score, 15% Damage Mitigated, and 10% Total cs.


# Assessment of Missingness


## NMAR Analysis


We do not believe that any of the columns in our cleaned dataset fall under the criteria of Not Missing at Random. This is defined as columns whose values themselves determine their missingness.
The columns that do have missingness in our set, namely url, don't have anything specific about them that allows you to determine whether they will be missing or not so we concluded that they are jnot NMAR.


## Missingness Dependency


For analysis of missingness, we will utilize the 'url' column as it has the most significant missingness




### KDA distribution when url is missing
<iframe src="assets/KDA_URL.html" width=800 height=600 frameBorder=0></iframe>


The distribution of KDA when URL is missing and the distribution of KDA when URL is not missing.


This showcases a clear difference in the distributions; specifically, we can see that the distributions look similar
however, they showcase a disparity in KDA rows that have missing urls and those that don't. We tested if the missingness of the game url is dependent on KDA using a permutation test with a 5% cut-off p-value. Our test statistic was the diff in group means since the distributions look similar.


In this case, our hypothesis would be:
- null: the distribution of KDA when url is missing is the same as the distribution of KDA when url is not missing


For this examination, we received a p-value of 0.013 meaning that we have enough evidence to reject the null, or in other words, we can say that the missingness of url is dependent on KDA. While this was initially surprising, we can see that in the figure above, the distributions had an apparent similarity but were just different in size.




We also examined if the missingness of url was dependent on the result (win or loss) where our test statistic was TVD and we also utilized a permutation test with a 5% cut-off p-value.


In this case, our hypothesis would be:
- null: the distribution of results when url is missing is the same as the distribution of results when url is not missing


### Empirical Distribution and Observed Statistic (result and url)
<iframe src="assets/empdis.html" width=800 height=600 frameBorder=0></iframe>
- Observed Test Statistic = 0.00001318771


The result of this test was also surprising as the p-value was 0.989 which means we do not have enough evidence to reject the notion that the result is independent of url missingness. This was interesting as it seems consistent for the url to match data to be missing dependent on if the result was a loss or a win (perhaps heinous characters at play).


# Hypothesis Testing


Given that we calculated the MVP score (i.e the MVPscore column), which is the score that roughly grades the performance of a player
in their match, we performed a hypothesis test to answer our original question: is the best player always on the winning team? We believe this to be an accurate path to answering our question as MVP score takes into account not only things like KDA and damage dealt to other players (DTC), but also game stats like damage mitigated and vision score. This is done in order not to favor champions that specialize in dealing damage and take into account other aspects of being a good well rounded player; this also allows other champions and positions a shot at being the best!


We utilized a hypothesis test with a 5% cut-off p-value. The proportion of times that the best player (highest MVP per game) was on the winning side was the test statistic. This is similar to a hypothesis test done on a fair coin with an unfair coin as the observed; in which case, the proportion of the bias is valid.


In this case, our hypothesis was:
- Null: The 'best' player is just as likely to be on the winning team as the losing team
- Alternative: The 'best' player is more likely to be on the winning team


### Empirical Distribution and Observed Statistic (MVP)
<iframe src="assets/Hypo.html" width=800 height=600 frameBorder=0></iframe>
- Observed Test Statistic = 0.9412947326095698


As can be ascertained from the above graph, the p-value was low; as a matter of fact, it was 0! This means that we can reject the null that the best player is likely to be on either side as our observation (the proportion of the best player being on the winning team) is not consistent with said null.




# Conclusion


We can thus conclude that the null hypothesis put forth that the best player is equally likely to be on the winning and losing sides in a competitive game of League of Legends cannot be supported by this data.



















