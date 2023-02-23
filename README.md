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
    - damagetochampions: The total amount of damage dealt to other champions
    - earnedgold: The total amount of gold collected
    - visionscore: The amount of times your actions allowed your time to see otherwise unseen targets
    - damagemitigatedperminute: The amount of damage you healed per minute (to yourself or others)

Answering this question is important as it highlights indivual performance and excellence within the competitive circuit. It motivates players to improve as there is verifyable data that suggests that the best teams thrive off of the best indivudual performers.


# Cleaning and EDA
The steps we took in cleaning our data consisted of first selecting our relevent columns and proceeding to clean those. 

Firstly, we selected our columns by passing in a list of the columns above. This helped us immensly in pinpointing the exact columns that we needed to answer our question. Once we selected these, we first began by querying out the position of 'team.' This is due to the fact that our particular analysis focuses on individidual performance rather than team effort. Additionally, we also edited the gamelength column by dividing it by 60 to  convert it from seconds to minutes; this was particularly useful in obtaining another column called damagemitigated by multiplying the gamelength column by the damagemitigatedperminute column. Lastly, we replaced all 0.0s with 1s in the deaths column so that we could create a new column called KDA (the Kill, Death, and Assist ratio); KDA serves as an aggregate statistic useful for finding the "best" player on either team. 

### Cleaned Dataframe (first few rows):
    print(relavent.head().to_markdown(index=False))


## Univariate Analysis

KDA UA:
<iframe src="assets/DSC80.html" width=800 height=600 frameBorder=0></iframe>









# Assessment of Missingness


# Hypothesis Testing



