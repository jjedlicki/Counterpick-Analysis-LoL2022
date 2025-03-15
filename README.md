# Counterpicking in the Top Lane
Created as a final project for DSC80 at UCSD.
---
## Introduction
League of Legends (LoL) is a multiplayer online battle arena released in 2009 and developed by Riot Games. It is one of the most played video games worldwide, and also features the largest competitive esports scene. The website [Oracle's Elixer](https://oracleselixir.com/) has compiled a dataset consisting of information from 2022 esport matches. The dataset includes information about the teams, as well as in game statistics at several timepoints.

In LoL, players can choose from 1 of 5 roles. This analysis will focus on the top lane role and the concept of counterpicking. Before playing each game, there is a draft phase where each side takes turn to ban champions, then pick champions. Depending on the bans and picks, teams can adjust their own champion picks to better match up with the enemy team's composition. Particularly, if you know who your lane opponent is going to play, it can be advantageous to pick a champion who has a favorable matchup against them, or **"counterpick"** them.

Top lane is generally considered the most isolated of the 5 roles. The jungle and midlane roles can roam around the map due to their central location, and the bottom and support roles lane together as one 'botlane'. The dragon objectives, which botlane is located near, are also considered more valuable than the early game objective top lane is located near, the Rift Herald. As such, top laners generally interact much more with their opponent laner than any other role.

**"Laning phase"** is a term referring to the period of the game before teams group up, and where most interactions between two players of the same role occur. During a game of LoL, there are turrets in each lanes that are difficult to destroy and prevent enemy players from advancing further into controlled territory. At 14 minutes, turrets lose some protection and become easier to destroy. Once one is destroyed, teams generally group up and focus more on obtaining objectives, which signals the end of the laning phase and beginning of a more team-oriented, group playstyle. For the purpose of this analysis, statistics from **ten minutes** into the game will be considered from the laning phase.

I am interested in how **being able to counterpick an opponent affects how well a player performs during laning phase.** I will test how counterpicking effects early game stats, and if a player was counterpicked or not is able to be obtained from these stats. This prediction tool can help teams or LoL players draft their teams, particularly if they play top lane.
### Dataset Information
There are 150588 rows in the dataset, with each 12 rows representing data from 1 game of professional LoL, for a total of 12549 games. While there are many columns in the dataset, I will only desribe the columns used in my analysis.

| Column | Description |
| ----------- | ----------- |
| 'gameid' | A unique identifier per game of LoL that data was collected from. |
| 'datacompleteness' | If all data was obtained, or if parts are unknown. | 
| 'champion' | What champion a player picked during the game. | 
| 'position' | Signifies if the player was on the "Red" or "Blue" side, which affects the order in which champions are picked and the physical side of the game map teams play on. | 
| 'position' | What lane a player plays, or if the data is from a team instead of an individual. | 
| 'pick1' | As part of team data, what champion was picked first on the team. | 
| 'pick2' | As part of team data, what champion was picked second on the team. | 
| 'pick3' | As part of team data, what champion was picked third on the team. | 
| 'pick4' | As part of team data, what champion was picked fourth on the team. | 
| 'pick5' | As part of team data, what champion was picked fifth on the team. | 
| 'earnedgold' | Total amount of gold earned by a player. Gold is earned by defeated enemy players, minions, or objectives. | 
| 'golddiffat10' | Difference in gold earned at 10 minutes between a player and their enemy lane opponent. | 
| 'xpdiffat10' | Difference in experience (XP) earned at 10 minutes between a player and their enemy lane opponent. Experience is earned by being near minion deaths, or from defeating players and objectives. | 
| 'csdiffat10' | Difference in creep score (CS) earned at 10 minutes between a player and their enemy lane opponent. CS is obtained by doing the last hit of damage to a minion, and grants the player gold. This is the primary way to obtain gold in the game. | 
| 'league' | What regional division the game was played as a part of. | 

The more gold and experience a player has, the better stats they have, so the metrics of gold, experience, and CS will be used to determined performance.

## Data Cleaning and Exploratory Data Analysis
The original dataset does not contain data about what a players's opponent champion is, nor does it indicate if they picked their champion before or after their opponent. Using the team data, I added a column with the champion name of the opponent, and a column describing if the player was able to counterpick or not. 

| Column | Description |
| ----------- | ----------- |
| 'lane_opp' | The champion the opponent picked. |
| 'counterpick' | True if the player picked their character after their opponent, False otherwise. | 

A significant portion of data from the columns like pick1-5 were missing. This was because only the team data rows had this data associated with it, and the players had data missing by design. After adding the new columns, I no longer needed the team related rows, so they were dropped.

An example of two games from the cleaned dataframe is shown below. 

| gameid                | datacompleteness   | position   | champion   | side   |   pick1 |   pick2 |   pick3 |   pick4 |   pick5 |   earnedgold |   goldat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 | league   | lane_opp   | counterpick   |
|:----------------------|:-------------------|:-----------|:-----------|:-------|--------:|--------:|--------:|--------:|--------:|-------------:|-----------:|---------------:|-------------:|-------------:|:---------|:-----------|:--------------|
| ESPORTSTMNT01_2690210 | complete           | top        | Renekton   | Blue   |     nan |     nan |     nan |     nan |     nan |         7164 |       3228 |             52 |          -44 |            8 | LCKC     | Gragas     | True          |
| ESPORTSTMNT01_2690210 | complete           | top        | Gragas     | Red    |     nan |     nan |     nan |     nan |     nan |         6231 |       3176 |            -52 |           44 |           -8 | LCKC     | Renekton   | False         |
| ESPORTSTMNT01_2690219 | complete           | top        | Gragas     | Blue   |     nan |     nan |     nan |     nan |     nan |         6488 |       2880 |           -894 |        -1129 |          -30 | LCKC     | Gangplank  | True          |
| ESPORTSTMNT01_2690219 | complete           | top        | Gangplank  | Red    |     nan |     nan |     nan |     nan |     nan |        13289 |       3774 |            894 |         1129 |           30 | LCKC     | Gragas     | False         |

There were two rows missing champion data and total earned gold. As these make up a very small fraction of the overall data, these were dropped. 

### Univariate analysis
The following is a histogram of the gold difference at 10 minutes for both players with and without counterpick.

<iframe
  src="assets/EDAREAL.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The data is normally distributed in both cases. While there is a lot of overlap in the data, the curve for counterpick data is slightly more to the right.

### Bivariate analysis
Does counterpicking improve overall performance, or just laning phase peformance? To answer that question, I graphed a scatter plot comparing early game performance vs late game performance, using the gold earned at ten minutes versus the gold earned total.

<iframe
  src="assets/EDA2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

While there does appear to be a slight correlation between early and total gold, it does not appear to depend on if the player was able to counterpic, as the groups appear very similar on the graph.

### Interesting Aggregates
I aggregated the total earned gold and 10 minute data depending on counterpick status, and took the mean of the data.

| counterpick   |   earnedgold |   golddiffat10 |   xpdiffat10 |   csdiffat10 |
|:--------------|-------------:|---------------:|-------------:|-------------:|
| False         |      8111.06 |       -33.1406 |      -17.929 |    -0.540497 |
| True          |      8195.3  |        33.1406 |       17.929 |     0.540497 |

I used a groupby along with the mean function to get an idea of what each quantative value looks like on average. All values were higher when counterpicked. The values in the difference columns are the same because all data is mirrored, as each game has one top laner who counterpicked and one who did not.


## Assessment of Missingness
The only remaining missing values in the dataset come from the ten minute columns. If data is missing, it is missing in all 3. For my hypothesis test, I will be using the datacompleteness column, as it says missing in all cases where the ten minute data is missing.

I believe these games are not missing at random, and instead are missing based on the league they come from. The data is not missing completely at random, because the amount of data loss is too high to assume some values were accidentally not entered, or something similar. I do not think the data is missing at random, as the only possibility I could think of would be if a team had gotten very good stats and ended the game before ten minutes. However, there have only been 4 professional LoL games that have lasted less than 10 minutes, and none of them happened during 2022, so it cannot be the case here.


I will also test if they are missing based on the counterpick column, as that would affect my analysis. I am using a significance level of 0.05, and use Total Variation Distance for both tests.

**Null hypothesis:** Regardless of counterpick status, the distribution of missing data is the same.
**Alternative hypothesis:** Different counterpick statuses have different distributions of missing data.
**Test Statistic** TVDs.
**Significance level** 0.05

<iframe
  src="assets/NMAR1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The graph above shows the distribution of TVDs if counterpick was randomly assigned in green, and the actual TVD in yellow. As the yellow line is right in the middle of the distribution, I cannot reject my null.


**Null hypothesis:** Regardless of which league the data comes from, the distribution of missing data is the same.
**Alternative hypothesis:** Different leagues have different distributions of missing data.
**Test Statistic** TVDs.
**Significance level** 0.05


<iframe
  src="assets/NMAR2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As my actual TVD is far higher than any of the values from my permutated data, I can reject my null and say that my missing data does depend on which league it came from.

## Hypothesis Testing
I want to see the effect counterpicking has on early performance. As gaining both CS and experience also generates gold, I will use the gold difference at ten minutes and see if counterpicking leads to higher values. I want to use differences in means, and see if players who counterpicked have a significantly higher average gold difference.

**Null hypothesis:** The difference in gold at ten minutes is not different depending on counterpick status.
**Alternative hypothesis:** The difference in gold at ten minutes is higher in players who counterpicked.
**Test Statistic** Absolute difference in means of gold at ten minutes.
**Significance level** 0.05

My resulting P-value is 0.0, which is lower than 0.05, so I can reject my null and conclude that **in my dataset, players who counterpicked earned more gold at ten minutes on average.**

<iframe
  src="assets/HYP1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

My figure shows the distibution of the differences in means, with my actual difference in mean in yellow. It is very different from the distribution, and supports my conclusion.

## Framing a Prediction Problem
Now that I know counterpicking seems to affect early-game performance, I want to know if I can look at post-game data, like my original dataframe, and predict if the players counterpicked or not. My known data will be gold, experience, and CS at ten minutes, as well as total earned gold, what champion was picked, what side the player is on, and what champion they were opposing. 

In order to train and test my data, it was split into 75% training and 25% test data. Because I want to know if a set of stats are from a player who counterpicked or not, it is a binary classifier problem.

To test my classifier's accuracy, I will use the F1-score. This is because my data is evenly split, with each game resulting in one counterpicked toplaner and one not each game, so I do not need to worry about unevenly distributed data, and I want to make sure my accuracy and recall are both high.

## Baseline Model
My initial model was a Decision Tree Classifier using the total earned gold and the gold earned at ten minutes. These were both quantative inputs, but because it was a Decision Tree they did not need to be scaled. There were 2 features total.

To choose the hyperparameters, I used Grid Search to try out different combinations until I came up with the best max depth and minimum sample splitting. After finding the best Classifier, I recieved a F1-score of **0.518** for training data and **0.509** for test data. This not very accurate. If I flipped a coin for each player and used that to decide, I would probably get a score of around 0.5. Therefore, my model is barely better than randomly guessing.

## Final Model
To improve my model, I am switching to a Random Forest Classifier, and use one-hot encoding to change my categorical features into numerical ones. In total I am using **236 features** of which four are quantitative (total gold and gold, xp, and cs at ten minutes) and the rest are nominal. There are two columns for red or blue side, which are redundant but it doesn't matter for a Random Forest Classifier, and the rest are for what champion was picked, and what champion they are against. This will allow my model to have more nuance around the values. Even though counterpicking leads to a higher average stats, individual matchups may be less favorable early. Another concern is that some champions may earn less gold and XP than others, because they may be more team-oriented and leave the lane to assist teammates more.

Each champion collumn is one-hot encoded, meaning it is a column of 0's unless that champion was picked, in which case the value would be a 1. This allows for non-numerical data to be used for my classifier. 

Finally, I used grid search again to select the best parameters. The best parameters were as follows
{'max_depth': 30, 'min_samples_split': 80, 'n_estimators': 50}

My new classifier's test accuracy is now **0.998** which is a large improvement. My classifier is no longer randomly guessing, but is instead pretty accurate at predicting if a player counterpicked or not. Below is my confusion matrix to see how well my classifier works on the data. The actual value is represented by the columns, and the predicted value is represented by the rows.

| counterpick   |   False |   True|
|:--------------|-------------:|---------------:|
| False         | 3109 | 7 |    
| True          | 4  | 3154 |  

While there were technically almost double the amount of false negatives in the upper right than false positives in the bottom left, both make up a very small portion of the data, so I think this is a good model.

## Fairness Analysis
The red side gets to have the last pick of the game, and thus can always have a counterpick. I want to test that my model is not biased towards players who are the red side compared to the blue side.
**Null Hypothesis:** My model is fair. The model's F-1 score is the same on red and blue side, with differences due to random chance.
**Alternative Hypothesis:** My model is not fair. The model's F-1 score is higher on the red side.
**Test stat:** Difference in f1 score
**Significance Level:** 0.05

After running my permutation test, I got a p-value of **0.112** so I cannot reject my null, which suggests my model is fair to both sides. The results of my test are plotted below.

<iframe
  src="assets/FAIR1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As seen by my results, my observed statistic is located within the distribution of permutated training data.
<div style="text-align: center;">
  <img src="https://media1.tenor.com/m/1d7xExbbh80AAAAd/ornn-dance.gif" style="width: 300px; height: auto;">
</div>


