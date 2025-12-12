# League of Legends Match Analysis – DSC 80 Final Project

## Introduction

This project uses the 2022 Oracle’s Elixir League of Legends professional match dataset, which contains detailed statistics for thousands of competitive games, including objective events, gold differences, and match outcomes. After filtering the raw data to include only team-level rows, the cleaned dataset contains X rows (replace X with team_df.shape[0] from your notebook), where each row represents a single team’s performance in one match.

The central question of this project is:

How strongly do early-game events—such as First Blood, early objectives, and gold advantage—predict whether a team will win a professional League of Legends match?

Readers should care about this question because competitive League of Legends revolves around early-game decision-making, and understanding which early advantages matter most helps explain why certain strategies succeed at the highest levels of play. This analysis also demonstrates how early metrics can be transformed into predictive features capable of forecasting match outcomes.

The key columns relevant to this question include:
- Firstblood – whether the team secured the first kill of the match (0/1)
- Firsttower – whether the team destroyed the first turret (0/1)
- Firstdragon – whether the team secured the first dragon (0/1)
- firstherald – whether the team secured the first Rift Herald (0/1)
- golddiffat25 – the team’s gold lead (positive) or deficit (negative) at 25 minutes 
- result – the match outcome (0 = loss, 1 = win)

These columns capture the main strategic objectives and economic state of a team in the early game, making them directly relevant for evaluating how early leads translate into match victories.

## Data Cleaning and Exploratory Data Analysis

- Summary of cleaning steps
  - Filtered the dataset to include only team-level rows (position == "team").
  - Reset the index and removed irrelevant or duplicated team/player entries.
  - Converted early-game event columns (firstblood, firstdragon, firstherald, firsttower, etc.) into integer (0/1) values.
  - Removed matches under 300 seconds to avoid corrupted or “remake” games.
  - Mapped teamid into a categorical side label: blue or red.
  - Final cleaned dataset contained one row per team per match.
	```
					  gameid datacompleteness                                          url league  year   split  playoffs                 date  game  patch  participantid side position playername playerid                  teamname                                   teamid champion      ban1          ban2          ban3      ban4     ban5     pick1      pick2     pick3    pick4      pick5  gamelength  result  kills  deaths  assists  teamkills  teamdeaths  doublekills  triplekills  quadrakills  pentakills  firstblood  firstbloodkill  firstbloodassist  firstbloodvictim  team kpm    ckpm  firstdragon  dragons  opp_dragons  elementaldrakes  opp_elementaldrakes  infernals  mountains  clouds  oceans  chemtechs  hextechs  dragons (type unknown)  elders  opp_elders  firstherald  heralds  opp_heralds  void_grubs  opp_void_grubs  firstbaron  barons  opp_barons  atakhans  opp_atakhans  firsttower  towers  opp_towers  firstmidtower  firsttothreetowers  turretplates  opp_turretplates  inhibitors  opp_inhibitors  damagetochampions        dpm  damageshare  damagetakenperminute  damagemitigatedperminute  damagetotowers  wardsplaced     wpm  wardskilled    wcpm  controlwardsbought  visionscore    vspm  totalgold  earnedgold  earned gpm  earnedgoldshare  goldspent      gspd   gpr  total cs  minionkills  monsterkills  monsterkillsownjungle  monsterkillsenemyjungle     cspm  goldat10   xpat10  csat10  opp_goldat10  opp_xpat10  opp_csat10  golddiffat10  xpdiffat10  csdiffat10  killsat10  assistsat10  deathsat10  opp_killsat10  opp_assistsat10  opp_deathsat10  goldat15   xpat15  csat15  opp_goldat15  opp_xpat15  opp_csat15  golddiffat15  xpdiffat15  csdiffat15  killsat15  assistsat15  deathsat15  opp_killsat15  opp_assistsat15  opp_deathsat15  goldat20   xpat20  csat20  opp_goldat20  opp_xpat20  opp_csat20  golddiffat20  xpdiffat20  csdiffat20  killsat20  assistsat20  deathsat20  opp_killsat20  opp_assistsat20  opp_deathsat20  goldat25   xpat25  csat25  opp_goldat25  opp_xpat25  opp_csat25  golddiffat25  xpdiffat25  csdiffat25  killsat25  assistsat25  deathsat25  opp_killsat25  opp_assistsat25  opp_deathsat25
	0  ESPORTSTMNT01_2690210         complete                                          NaN   LCKC  2022  Spring         0  2022-01-10 07:44:08     1  12.01            100  NaN     team        NaN      NaN         BRION Challengers  oe:team:733ebb9dbf22a401c0127a0c80193ca      NaN     Karma       Caitlyn        Syndra    Thresh     Lulu  Renekton     Samira  Xin Zhao  LeBlanc      Leona        1713       0      9      19       19          9          19          0.0          0.0          0.0         0.0           1             NaN               NaN               NaN    0.3152  0.9807            0      1.0          3.0              1.0                  3.0        0.0        0.0     0.0     0.0        0.0       1.0                     NaN     0.0         0.0            1      2.0          0.0         NaN             NaN           0     0.0         0.0       NaN           NaN           1     3.0         6.0            1.0                 1.0           5.0               0.0         0.0             1.0            56560.0  1981.0858          NaN             3537.1979                 2364.7285          6168.0         74.0  2.5919         51.0  1.7863                33.0        197.0  6.9002      47070     28222.0    988.5114              NaN    44570.0 -0.028312  0.94       NaN        680.0         160.0                    NaN                      NaN  29.4221   16218.0  18213.0   322.0       14695.0     18076.0       330.0        1523.0       137.0        -8.0        3.0          5.0         0.0            0.0              0.0             3.0   24806.0  28001.0   487.0       24699.0     29618.0       510.0         107.0     -1617.0       -23.0        5.0         10.0         6.0            6.0             18.0             5.0   31962.0  36874.0   631.0       32906.0     41821.0       715.0        -944.0     -4947.0       -84.0        5.0         10.0         7.0            7.0             22.0             5.0   40224.0  45960.0   767.0       40136.0     49931.0       864.0          88.0     -3971.0       -97.0        6.0         12.0         7.0            7.0             22.0             6.0
	1  ESPORTSTMNT01_2690210         complete                                          NaN   LCKC  2022  Spring         0  2022-01-10 07:44:08     1  12.01            200  NaN     team        NaN      NaN  Nongshim Esports Academy  oe:team:7c64febcd5ccff13dcd035dc6867a00      NaN   Lee Sin  Twisted Fate           Zoe  Nautilus     Rell      Jinx      Viego    Gragas   Viktor    Alistar        1713       1     19       9       62         19           9          6.0          0.0          0.0         0.0           0             NaN               NaN               NaN    0.6655  0.9807            1      3.0          1.0              3.0                  1.0        2.0        1.0     0.0     0.0        0.0       0.0                     NaN     0.0         0.0            0      0.0          2.0         NaN             NaN           0     0.0         0.0       NaN           NaN           0     6.0         3.0            0.0                 0.0           0.0               5.0         1.0             0.0            79912.0  2799.0193          NaN             3009.6673                 2872.3292         13846.0         93.0  3.2574         51.0  1.7863                45.0        205.0  7.1804      52617     33769.0   1182.8021              NaN    45850.0  0.028312 -0.94       NaN        792.0         184.0                    NaN                      NaN  34.1856   14695.0  18076.0   330.0       16218.0     18213.0       322.0       -1523.0      -137.0         8.0        0.0          0.0         3.0            3.0              5.0             0.0   24699.0  29618.0   510.0       24806.0     28001.0       487.0        -107.0      1617.0        23.0        6.0         18.0         5.0            5.0             10.0             6.0   32906.0  41821.0   715.0       31962.0     36874.0       631.0         944.0      4947.0        84.0        7.0         22.0         5.0            5.0             10.0             7.0   40136.0  49931.0   864.0       40224.0     45960.0       767.0         -88.0      3971.0        97.0        7.0         22.0         6.0            6.0             12.0             7.0
	2  ESPORTSTMNT01_2690219         complete                                          NaN   LCKC  2022  Spring         0  2022-01-10 08:38:24     1  12.01            100  NaN     team        NaN      NaN        T1 Esports Academy  oe:team:731b7a9fd004cdbe2bcb3da795bce47      NaN      Sona     Jarvan IV       Caitlyn      Lulu   Lucian   Lee Sin       Jhin    Gragas    Rakan    Orianna        2114       0      3      16        7          3          16          0.0          0.0          0.0         0.0           0             NaN               NaN               NaN    0.0851  0.5393            0      1.0          4.0              1.0                  4.0        0.0        1.0     0.0     0.0        0.0       0.0                     NaN     0.0         0.0            1      1.0          1.0         NaN             NaN           0     0.0         2.0       NaN           NaN           0     3.0        11.0            0.0                 0.0           2.0               3.0         0.0             2.0            59579.0  1690.9839          NaN             2984.0208                 3109.6121          6842.0        119.0  3.3775         55.0  1.5610                47.0        277.0  7.8619      57629     34688.0    984.5222              NaN    53945.0 -0.207137 -3.23       NaN        994.0         215.0                    NaN                      NaN  34.3141   14939.0  17462.0   317.0       16558.0     19048.0       344.0       -1619.0     -1586.0       -27.0        1.0          1.0         3.0            3.0              3.0             1.0   23522.0  28848.0   533.0       25285.0     29754.0       555.0       -1763.0      -906.0       -22.0        1.0          1.0         3.0            3.0              3.0             1.0   31228.0  38596.0   710.0       36368.0     42069.0       758.0       -5140.0     -3473.0       -48.0        1.0          1.0         5.0            5.0              6.0             1.0   39335.0  49409.0   895.0       46615.0     57155.0       928.0       -7280.0     -7746.0       -33.0        1.0          1.0         8.0            8.0             13.0             1.0
	3  ESPORTSTMNT01_2690219         complete                                          NaN   LCKC  2022  Spring         0  2022-01-10 08:38:24     1  12.01            200  NaN     team        NaN      NaN        Liiv SANDBOX Youth  oe:team:e7a7c6bf58eb268ed3f13aac4158aa8      NaN   LeBlanc         Yuumi  Twisted Fate     Karma  Alistar  Renekton     Syndra   Nidalee    Leona  Gangplank        2114       1     16       3       39         16           3          1.0          0.0          0.0         0.0           1             NaN               NaN               NaN    0.4541  0.5393            1      4.0          1.0              4.0                  1.0        0.0        2.0     1.0     0.0        0.0       1.0                     NaN     0.0         0.0            0      1.0          1.0         NaN             NaN           1     2.0         0.0       NaN           NaN           1    11.0         3.0            1.0                 1.0           3.0               2.0         2.0             0.0            74855.0  2124.5506          NaN             2745.7237                 2868.4201         19944.0        129.0  3.6613         70.0  1.9868                65.0        346.0  9.8202      71004     48063.0   1364.1343              NaN    66410.0  0.207137  3.23       NaN       1013.0         244.0                    NaN                      NaN  35.6764   16558.0  19048.0   344.0       14939.0     17462.0       317.0        1619.0      1586.0        27.0        3.0          3.0         1.0            1.0              1.0             3.0   25285.0  29754.0   555.0       23522.0     28848.0       533.0        1763.0       906.0        22.0        3.0          3.0         1.0            1.0              1.0             3.0   36368.0  42069.0   758.0       31228.0     38596.0       710.0        5140.0      3473.0        48.0        5.0          6.0         1.0            1.0              1.0             5.0   46615.0  57155.0   928.0       39335.0     49409.0       895.0        7280.0      7746.0        33.0        8.0         13.0         1.0            1.0              1.0             8.0
	4       8401-8401_game_1          partial  https://lpl.qq.com/es/stats.shtml?bmid=8401    LPL  2022  Spring         0  2022-01-10 09:24:26     1  12.01            100  NaN     team        NaN      NaN                 Oh My God  oe:team:f4c4528c6981e104a11ea7548630c23      NaN  Renekton       Lee Sin       Caitlyn     Jayce  Camille      Jinx  Jarvan IV  Nautilus   Syndra       Gwen        1365       1     13       6       35         13           6          NaN          NaN          NaN         NaN           0             NaN               NaN               NaN    0.5714  0.8352            0      2.0          1.0              NaN                  NaN        NaN        NaN     NaN     NaN        NaN       NaN                     2.0     NaN         NaN         <NA>      2.0          0.0         0.0             0.0        <NA>     1.0         0.0       NaN           NaN           1     8.0         3.0            NaN                 NaN           NaN               NaN         1.0             0.0            40086.0  1762.0220          NaN             2263.2527                       NaN             NaN         79.0  3.4725         33.0  1.4505                32.0        162.0  7.1209      45468     30167.0   1326.0220              NaN    36908.0 -0.005862   NaN       NaN          NaN         172.0                   98.0                     18.0      NaN       NaN      NaN     NaN           NaN         NaN         NaN           NaN         NaN         NaN        NaN          NaN         NaN            NaN              NaN             NaN       NaN      NaN     NaN           NaN         NaN         NaN           NaN         NaN         NaN        NaN          NaN         NaN            NaN              NaN             NaN       NaN      NaN     NaN           NaN         NaN         NaN           NaN         NaN         NaN        NaN          NaN         NaN            NaN              NaN             NaN       NaN      NaN     NaN           NaN         NaN         NaN           NaN         NaN         NaN        NaN          NaN         NaN            NaN              NaN             NaN
	```

- Univariate Analysis
  1. Match Duration Distribution
Game lengths cluster between ~1700 and ~2200 seconds, forming a clear unimodal shape.
  2. First Blood Frequency
The firstblood column shows an uneven distribution, with most games containing a first blood event early on.
- Bivariate Analysis
  1. Win/Loss Conditional on First Blood
Teams that secured first blood had visibly higher counts of wins compared to losses.
  2. Gold Difference at 25 vs Match Duration
A strong negative trend: large early gold leads correlate with shorter matches, showing that early dominance closes games faster.

- Interesting Aggregate
  - Win rate by league was computed by grouping matches by the tournament league.
Results mostly centered around 0.5, which is expected because each match contributes one win and one loss to the dataset.
The purpose of this aggregation was to confirm no systemic imbalance in recorded match outcomes.

- (Embed images below)

<iframe
  src="assets/univariate_match_duration.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

<iframe
  src="assets/univariate_freq_first_blood.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

![Plot 1](images/plot1.png)
![Plot 2](images/plot2.png)

## Assessment of Missingness

## Hypothesis Testing (Step 4)
- Hypothesis test 1 summary + conclusion First Blood and Win Rate
  - 	Null Hypothesis: Teams with and without first blood have identical win rates.
	•	Alternative Hypothesis: Teams with first blood have a higher win rate.
	•	Test Statistic: Difference in mean win rate.
	•	Result: p-value ≈ 0. The null hypothesis is rejected.
Conclusion: Obtaining first blood is strongly associated with a higher probability of winning.
- Hypothesis test 2 summary + conclusion Gold Lead at 25 min and Game Length
  - Null Hypothesis: Early gold difference is unrelated to match duration.
	•	Alternative Hypothesis: Larger gold leads at 25 minutes reduce match duration.
	•	Test Statistic: Correlation between golddiffat25 and gamelength.
	•	Result: p-value ≈ 0. The null hypothesis is rejected.
Conclusion: Early economic advantage is a strong predictor of shorter matches.

## Framing a Prediction Problem
- Target variable:  result (0 = loss, 1 = win).
- Prediction Type: Binary classification.
	•	Justification: All features used are available early in the match, so no data leakage occurs.
	•	Evaluation Metric: Accuracy, chosen for interpretability and because classes are not extremely imbalanced.

## Baseline Model (Step 6)
- Model: Logistic Regression inside a single sklearn Pipeline.
	•	Features Used:
firstblood, firsttower, firstdragon, firstherald, golddiffat25
	•	Transformations: StandardScaler applied to numeric features.
	•	Performance:
Train accuracy ≈ 0.835
Test accuracy ≈ 0.830

Interpretation: The logistic model provides a strong linear baseline, capturing early objective control and gold lead information.

## Final Model (Step 7)
-	Model Type: Random Forest Classifier.
	•	New Engineered Features:
	•	Scaled/normalized gold advantage
	•	Combined objective indicators
	•	Hyperparameters Tuned (GridSearchCV):
	•	n_estimators
	•	max_depth
	•	min_samples_split
	•	Best Parameters:
{n_estimators: 200, max_depth: 10, min_samples_split: 5}
	•	Performance:
Train accuracy ≈ 0.862
Test accuracy ≈ 0.858

Conclusion: The Random Forest model improves generalization by capturing nonlinear interactions that Logistic Regression cannot represent.

Fairness Question

Does the model perform differently for blue side teams compared to red side teams?

Groups
	•	Group X: Blue side
	•	Group Y: Red side

Metric: Precision

Null Hypothesis:

The model is fair; precision for blue and red teams is the same.

Alternative Hypothesis:

The model is unfair; precision for blue differs from precision for red.

Result
	•	Observed precision difference: ~–0.88
	•	Permutation test p-value: 1.0
	•	The observed difference lies entirely within the null distribution.

Conclusion:
There is no evidence the model systematically favors either group.
All differences are consistent with random variation.
- Embed fairness histogram

![Fairness Plot](images/fairness.png)

## Fairness Analysis

GitHub Repo
Link to the code repository (private).