# Counterpick-Analysis-LoL2022
Created as a final project for DSC80 at UCSD.
---
### Introduction
League of Legends (LoL) is a multiplayer online battle arena released in 2009 and devloped by Riot Games. It is one of the most played video games, and also features the largest competative esports scenes. The website [Oracle's Elixer](https://oracleselixir.com/) has compiled a dataset consisting of information from 2022 esport matches. The dataset includes information about the teams, as well as in game statistics at several timepoints.

In LoL, players can choose from 1 of 5 roles. This analysis will focus on the top lane role and the concept of counterpicking. Before playing each game, there is a draft phase where each side takes turn to ban champions, then pick champions. Depending on the bans and picks, teams can adjust their own champion picks to better match up with the enemy team's composition. Particularly, if you know who your lane opponent is going to play, it can be advantageous to pick a champion who has a favorable matchup against them, or **"counterpick"** them.

Top lane is generally considered the most isolated of the 5 roles. The jungle and midlane roles can roam around the map due to their central location, and the bottom and support roles lane together as one 'botlane'. The dragon objectives, which botlane is located near, are also considered more valuable than the early game objective top lane is located near, the Rift Herald. As such, top laners generally interact much more with their opponent laner than any other role.

**"Laning phase"** is a term referring to the period of the game before teams group up, and where most interactions between two players of the same role occur. During a game of LoL, there are turrets in each lanes that are difficult to destroy and prevent enemy players from advancing further into controlled territory. At 14 minutes, turrets lose some protection and become easier to destroy. Once one is destroyed, teams generally group up and focus more on obtaining objectives, which signals the end of the laning phase and beginning of a more team-oriented, group playstyle. For the purpose of this analysis, statistics from **ten minutes** into the game will be considered from the laning phase.

I am interested in how **being able to counterpick an opponents effects how well a player performs during laning phase.** I will test how counterpicking effects early game stats, and if a player was counterpicked or not is able to be obtained from these stats. This prediction tool can help teams or LoL players draft their teams, particularly if they play top lane.
## Dataset Information
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

###
<iframe
  src="assets/EDA1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
