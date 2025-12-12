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

The plot below shows the distribution of match duration (in seconds) across all competitive 2022 League of Legends matches. Most games last between 1500 and 2200 seconds (25–36 minutes), forming a clear peak in the middle of the histogram. The long tail on the right shows that a smaller number of games extend much longer, while extremely short games are rare due to remakes being filtered out.

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



- Bivariate Analysis
  1. Win/Loss Conditional on First Blood
Teams that secured first blood had visibly higher counts of wins compared to losses.
  2. Gold Difference at 25 vs Match Duration
A strong negative trend: large early gold leads correlate with shorter matches, showing that early dominance closes games faster.

The plot below shows the relationship between **first blood** and **match outcome**. Teams that secure first blood win more often than they lose, while teams that do not secure first blood tend to lose more frequently. The difference is noticeable but not overwhelming, suggesting that first blood provides an early advantage but does not guarantee victory.

<iframe
  src="assets/bivariate_loss_dstr_first_blood.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

- Interesting Aggregate
  - Win rate by league was computed by grouping matches by the tournament league.
Results mostly centered around 0.5, which is expected because each match contributes one win and one loss to the dataset.
The purpose of this aggregation was to confirm no systemic imbalance in recorded match outcomes.

	```
				 league  result
	0              ASCI     0.5
	41              PCS     0.5
	30              LJL     0.5
	31             LJLA     0.5
	32              LLA     0.5
	33              LMF     0.5
	34              LPL     0.5
	35            LPLOL     0.5
	36           LVP SL     0.5
	37              MSI     0.5
	38             NEXO     0.5
	39              NLC     0.5
	40  NLC Aurora Open     0.5
	42              PGC     0.5
	28             LFL2     0.5
	43              PGN     0.5
	44              PRM     0.5
	45             PRMP     0.5
	46       SL (LATAM)     0.5
	47              TAL     0.5
	48              TCL     0.5
	49               UL     0.5
	50              UPL     0.5
	51              USP     0.5
	52              VCS     0.5
	53               VL     0.5
	29              LHE     0.5
	27              LFL     0.5
	1             CBLOL     0.5
	13              GLL     0.5
	2            CBLOLA     0.5
	3               CDF     0.5
	4                CT     0.5
	5              DCup     0.5
	6               DDH     0.5
	7               EBL     0.5
	8             EBLPA     0.5
	9                EL     0.5
	10            ESLOL     0.5
	11              EUM     0.5
	12               GL     0.5
	14            GLLPA     0.5
	26              LEC     0.5
	15               HC     0.5
	16               HM     0.5
	17               IC     0.5
	18              LAS     0.5
	19              LCK     0.5
	20             LCKC     0.5
	21              LCL     0.5
	22              LCO     0.5
	23              LCS     0.5
	24             LCSA     0.5
	25              LDL     0.5
	54             WLDs     0.5
	```

## Assessment of Missingness
### NMAR Analysis
One column in this dataset with meaningful missingness is golddiffat25, which records the gold difference between the two teams at the 25-minute mark. If this value is missing, the most likely explanation is that the match did not last long enough for a 25-minute timestamp to exist. This is external information about game duration, not the gold difference itself, so the missingness is likely MAR, not NMAR.

To conclude a variable is NMAR, the probability of missingness must depend on the unobserved value itself (e.g., extremely high gold leads to being hidden). There is no reason to believe teams strategically hide gold difference values based on large or small magnitudes. Instead, we would need additional match-timeline data to confirm whether short games explain all missing values.

Therefore, I do not believe any column in this analysis is NMAR.

### Missingness Dependency Analysis
I investigated whether the missingness of golddiffat25 depends on other variables in the dataset.

Test 1: Does missingness of golddiffat25 depend on game duration?
	•	Column X (missingness column): golddiffat25
	•	Column Y (tested dependency): gamelength
	•	Test statistic: Difference in mean gamelength between non-missing and missing groups
	•	Observed statistic: 218.95 seconds
	•	p-value: 0.0
	•	Conclusion: We reject the null.
Missingness of golddiffat25 does depend on gamelength.
Games that are missing golddiffat25 are significantly shorter, consistent with the idea that short games never reached 25 minutes.

Test 2: Does missingness of golddiffat25 depend on match result?
	•	Column X: golddiffat25
	•	Column Y: result
	•	Test statistic: Difference in mean win rate between non-missing and missing groups
	•	Observed statistic: 0.0
	•	p-value: 0.522
	•	Conclusion: We fail to reject the null.
Missingness of golddiffat25 does NOT depend on whether a team won or lost.
This means both winning and losing teams are equally likely to have missing values, implying no bias between results and missing gold data.

<iframe
  src="assets/missingness_gamelength.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

## Hypothesis Testing
**Hypothesis Test 1**: Does getting First Blood increase a team’s chance of winning?

Null Hypothesis (H₀):
Teams that get First Blood and teams that do not get First Blood have the same average win rate. Any observed difference is due to random chance.

Alternative Hypothesis (H₁):
Teams that get First Blood have a higher average win rate than teams that do not.

Test Statistic:
Difference in mean win rate between:
• teams with firstblood = 1
• teams with firstblood = 0

Significance Level:
α = 0.05

Observed Statistic:
0.2198 — teams with First Blood win about 22 percentage points more often.

p-value:
0.0

Conclusion:
Since the p-value is effectively 0, we reject the null hypothesis. There is strong statistical evidence that teams securing First Blood tend to win more often, though this does not prove causation.



**Hypothesis Test 2**: Is gold difference at 25 minutes correlated with total game length?

Null Hypothesis (H₀):
There is no correlation between golddiffat25 and gamelength. Any observed correlation is due to chance.

Alternative Hypothesis (H₁):
There is a correlation between golddiffat25 and gamelength.

Test Statistic:
Pearson correlation between the two columns.

Observed Statistic:
2.06 × 10⁻¹⁹ (essentially 0 — almost no linear relationship).

p-value:
0.0

Conclusion:
Even though the observed correlation is extremely close to zero, the permutation distribution is even smaller, so we still reject the null because the test statistic is significantly different from the shuffled distribution. This tells us that the observed (near-zero) correlation is statistically different from random noise—however, practically speaking, the correlation is negligible. Gold difference at 25 minutes does not predict how long a match lasts in a meaningful way.

## Framing a Prediction Problem
Prediction Problem

The goal of my prediction task is to predict whether a team wins a match, using only information that would be available by the 25-minute mark of the game. This is a binary classification problem, since the response variable result takes values 1 (win) or 0 (loss).

Response Variable
- result (0 = loss, 1 = win)

This variable directly reflects match outcome, making it the most natural choice for a prediction problem in competitive games.

Features Used at Prediction Time

All features come from events that occur before or by minute 25, meaning they are valid for prediction:
- firstblood – whether the team secured the first kill
- firsttower – whether the team destroyed the first tower
- firstdragon – whether the team secured the first dragon
- firstherald – whether the team secured the first Rift Herald
- golddiffat25 – gold lead or deficit at minute 25

These features capture early objective control and economic advantage, both widely known to influence match outcomes.

Evaluation Metric

I use accuracy as my evaluation metric.

Why accuracy?
- The dataset is roughly balanced in terms of wins and losses. 
- The goal is to correctly classify outcomes, not specifically optimize for minority-class performance.
- Accuracy is easy to interpret and appropriate for a first baseline model.

## Baseline Model
For our baseline model, we built a binary classification model that predicts whether a team wins (result = 1) or loses (result = 0) using only early-game objective information. The features used were:
* 	firstblood – binary
* 	firsttower – binary
* 	firstdragon – binary
* 	firstherald – binary
* 	golddiffat25 – quantitative

All five features are measurable before the match ends, so they are valid at “time of prediction.” The four objective-related columns are nominal binary variables, while golddiffat25 is a quantitative numeric feature.

### Encoding & Preprocessing

Since all predictors are numeric (0/1 or real-valued), the only transformation required was scaling. We used an sklearn ColumnTransformer with StandardScaler applied to all features.

The entire workflow—including preprocessing and model training—was implemented in a single Pipeline, as required.

### Model Choice

We used Logistic Regression as the baseline classifier because:
* 	it is simple and interpretable
* 	it is commonly used as a baseline for binary problems

### Performance

Using an 80/20 train–test split:
* 	Accuracy: 0.835
* 	F1 Score: 0.830

### Interpretation

The baseline model performs reasonably well: early objectives and gold leads contain strong predictive power. However, we believe the model can be improved by:
* 	adding additional engineered features
* 	using a more flexible model (e.g., Random Forest)
* 	tuning hyperparameters to better capture non-linear relationships

The logistic model provides a strong linear baseline, capturing early objective control and gold lead information.

## Final Model
For the final model, I aimed to improve upon the logistic-regression baseline by allowing the model to capture non-linear relationships in the game features. To do this, I added a QuantileTransformer to reshape all numeric variables (firstblood, firsttower, firstdragon, firstherald, golddiffat25) into a normal-like distribution. This transformation helps tree-based models separate values more effectively, especially when the raw game statistics are extremely skewed (for example, gold differences at 25 minutes have a long right tail).
Because competitive League of Legends outcomes are influenced by complex, non-linear interactions between objectives, a tree-based model is a more appropriate choice than a linear model.

I used a Random Forest classifier for the final model. To choose the best version of this model, I performed a hyperparameter search using GridSearchCV. I tuned:
* 	number of trees (n_estimators)
* 	maximum tree depth (max_depth)
* 	minimum samples required to split (min_samples_split)

GridSearchCV evaluated multiple combinations of these values and selected the model that generalized best across folds.

The best hyperparameters were:
```commandline
{'clf__max_depth': 10,
 'clf__min_samples_split': 5,
 'clf__n_estimators': 100}
```
The final model achieved:
	•	Accuracy: 0.858
	•	F1-score: 0.854

This represents a clear improvement over the baseline logistic regression model (Accuracy ≈ 0.835, F1 ≈ 0.830). The improvement is expected because Random Forests capture non-linear interactions between objectives, which better reflects how League of Legends matches actually unfold (e.g., securing first dragon matters more when combined with an early tower, gold leads interact with objective control, etc.).

Overall, the final model performs better because it aligns more closely with the underlying data-generating process of competitive games: outcomes are decided by combinations of game events rather than simple linear effects.


## Fairness Analysis
In this section, I examined whether my final Random Forest model treats blue-side teams and red-side teams differently. Since the model predicts match outcomes, I focused on precision, which measures how often a positive prediction (win) is correct.

Groups Compared
* 	Group X: Blue-side teams
* 	Group Y: Red-side teams

Evaluation Metric 
- Precision

I chose precision because false positives are especially important in game prediction: incorrectly predicting a team will win can be more misleading than incorrectly predicting a loss.

Null and Alternative Hypotheses
* 	Null Hypothesis (H₀): The model is fair. The precision for blue-side teams and red-side teams is roughly equal, and any observed difference is due to random chance. 
* Alternative Hypothesis (H₁): The model is unfair. The precision for blue-side teams is lower than the precision for red-side teams.

Test Statistic
* 	The difference in precision: precision_blue − precision_red

A negative value suggests the model is worse for blue-side teams.

Results
* 	Observed precision difference: −0.8865
* 	p-value: 1.0

Interpretation

Although the observed precision difference is large and negative, the p-value of 1.0 means differences this extreme (or more extreme) appear very frequently under random permutations of the group labels. Therefore, we fail to reject the null hypothesis at any reasonable significance level.

Conclusion

The permutation test shows a p-value of 1.0, meaning that the observed precision difference between blue-side and red-side teams is completely consistent with random variation. I do not have statistical evidence that the model systematically favors or disadvantages either group. Therefore, I cannot conclude that the model is unfair toward blue-side or red-side teams.
