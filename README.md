# League of Legends Game Fairness Analysis

by Daimeng Sun (dsun@ucsd.edu)


---
## Introduction

In this project, we will investigate if there's bias in league of legends competitive games that favors one side over the other. <br><br>Specifically, we will investigate its fairness by exploring impact of getting dragon objectives on the game as claiming dragons in league of legends 2022 season provide permanent benefits that could shift the direction of the match. <br><br>Then we will investigate if both sides are equally likely to get dragon objectives to show the game's fairness.


---
## Cleaning and EDA

#### Cleaning process

Only rows that contain team stats were kept. Columns were selected for the purpose of this project. Empty values were dropped.

| gameid                |   participantid |   gamelength | side   | firstdragon   |   dragons |   result |   golddiffat15 |   totalgold |
|:----------------------|----------------:|-------------:|:-------|:--------------|----------:|---------:|---------------:|------------:|
| ESPORTSTMNT01_2690210 |             100 |         1713 | Blue   | False         |         1 |        0 |            107 |       47070 |
| ESPORTSTMNT01_2690210 |             200 |         1713 | Red    | True          |         3 |        1 |           -107 |       52617 |
| ESPORTSTMNT01_2690219 |             100 |         2114 | Blue   | False         |         1 |        0 |          -1763 |       57629 |
| ESPORTSTMNT01_2690219 |             200 |         2114 | Red    | True          |         4 |        1 |           1763 |       71004 |
| ESPORTSTMNT01_2690227 |             100 |         1972 | Blue   | True          |         4 |        1 |           1191 |       62868 |

#### EDA: Univariate Analysis

- Explore the distribution of dragon killed by one side in games (column 'dragon')

<iframe src="assets/drake_kill_one_side.html" width=800 height=600 frameBorder=0></iframe>

I expected the distribution of dragon killed by one side to be roughly normally distributed. However, it appears to be more skewed to the right than being normal. We will look at the combined dragon killed by both side next (dragon killed in one game).

- Explore the distribution of total dragon killed by both teams in games

<iframe src="assets/drake_kill_one_game.html" width=800 height=600 frameBorder=0></iframe>

The distribution of combined dragon killed by both sides looks normally distributed. Together, these two distributions suggest that in league of legends competitive games, one side tends to claim dragon objectives while the other side could not claim any. We will investigate this phenomenon using permutation testing. But first, we will explore if dragon objectives have a relationship with advantages in game.

#### EDA: Bivariate Analysis

- Explore distribution of teams gold difference at 15 by if team gets the first dragon

<iframe src="gd15_drake.html" width=800 height=600 frameBorder=0></iframe>

Conditional distribution shows that teams claiming the first dragon may correlate with them having a positive gold difference at 15 (leading). Does leads at 15 minutes have an impact on the eventual victory of the game?

- Explore relationship between gold difference at 15 and total gold

<iframe src="gd15_td.html" width=800 height=600 frameBorder=0></iframe>

Scatter plot between gold difference at 15 and team total gold does not show a positive correlation between them. Points toward the right side of the plot indicate greater leads at 15 minute result in lower team total gold, which is counter intuitive. Maybe teams with such huge gold lead will end the game early, resulting in the observed low toal gold. Therefore, we will standardize team total gold with game length, then invesigate the correlation.

- Explore relationship between gold difference at 15 and normalized total gold

<iframe src="gd15_ntd.html" width=800 height=600 frameBorder=0></iframe>

Scatter plot between gold difference at 15 and normalized team total gold does show a positive correlation between them. Combined with relationship between first dragon and gold difference at 15 minutes, teams that claim the first dragon may have a higher total gold, thus more likely to win the game. In addition to the observation that one team is likely to claim dragon objectives while the other tend to claim none, it is interesting to investigate if both sides are equally likely to claim dragon objectives.

#### EDA: Interesting Aggregates

- Aggregate dragon kills by side

Counts:
|   dragons |   Blue |   Red |       
|----------:|-------:|------:|
|         0 |   1518 |  1179 |
|         1 |   2176 |  1938 |
|         2 |   2558 |  2448 |
|         3 |   2343 |  2523 |
|         4 |   1742 |  2088 |
|         5 |    267 |   421 |
|         6 |     13 |    19 |
|         7 |      0 |     1 |

Normalized:
|   dragons |       Blue |         Red |
|----------:|-----------:|------------:|
|         0 | 0.142978   | 0.111048    |
|         1 | 0.204954   | 0.182537    |
|         2 | 0.240934   | 0.230574    |
|         3 | 0.220684   | 0.237638    |
|         4 | 0.164076   | 0.196666    |
|         5 | 0.0251483  | 0.0396534   |
|         6 | 0.00122445 | 0.00178958  |
|         7 | 0          | 9.41886e-05 |

Aggregated by blue or red side, dragon killed look different but require statistical analysis for further investigation.


---
## Assessment of Missingness

#### NMAR Analysis

I believe the column firstdragon is NMAR. By game knowledge, games that have shorter game length may end without a dragon being claimed. I will need column gamelength to help investigate the missingness of column firstdragon.

#### Missingness Dependency

<iframe src="missingness_dist.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="emp_dist.html" width=800 height=600 frameBorder=0></iframe>

Conclusion: A critical value of 0.05 is used. Permutation testing on missingness of column first dragon by game length results in a p-value smaller than the critical value 0.05. Therefore, we reject the null hypothesis that the absolute difference in mean game length observed in the sample is due to random chance. In addition, the missingness of column first dragon may have dependency on the game length.


---

#### Hypothesis Testing

Null hypothesis: In league of legends competitive games, the distribution of number of dragon kills on blue or red side is the same. The difference between the two observed samples is due to chance. <br>
Alternative hypothesis: In league of legends competitive games, the distributions of number of dragon killed between blue and red side are different.

<iframe src="dk_side.html" width=800 height=600 frameBorder=0></iframe>

Conclusion: TVD is used as test statistics because we are comparing between categorical data. A critical value of 0.05 is used. Permutation testing on dragon killed aggregated by blue or red side results in a p-value smaller than 0.05. Therefore, we reject the null hypothesis that the total variation difference observed in the sample is due to random chance. In addition, the distribution of claiming dragon objectives are difference among blue and red side. This analysis reveal potential unfairness between the two sides in league of legends competitive games.

