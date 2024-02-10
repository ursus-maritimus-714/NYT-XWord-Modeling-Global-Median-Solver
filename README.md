# Predictive Modeling of "Global Median Solver" (GMS) Performance on the New York Times Crossword Puzzle

## Introduction

### Project Overview and Data Sources
This summary reports on the results of predictive modeling of "Global Median Solver" (GMS) performance over 6+ years (Jan. 2018 - early Feb. 2024) of the [New York Times (NYT) crossword puzzle](https://www.nytimes.com/crosswords). Previously, I conducted a [comprehensive exploratory data analysis (EDA) of GMS performance](https://github.com/ursus-maritimus-714/NYT-XWord-EDA-Global-Median-Solver?tab=readme-ov-file#readme) over this sample period. From this EDA, numerous features pertaining to both the puzzles themselves as well as to the past performance of the GMS were identified as candidate features for predictive modeling of GMS solve times (GMSTs).    

This project relied crucially on two amazing data sources. The first, [XWord Info: New York Times Crossword Answers and Insights](https://www.xwordinfo.com/), was my source for data on the puzzles themselves. This included a number of proprietary metrics pertaining to the grids, answers, clues and constructors. XWord Info has a contract with NYT for access to the raw data underlying these metrics, but I unfortunately do not. Therefore, I will not be able to share raw or processed data that I've acquired from their site. Nonetheless, [Jupyter notebooks](https://jupyter.org/) with all of my Python code for analysis and figure generation can be found [here](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/tree/main/notebooks). The second, [XWStats](xwstats.com), was my source for historical GMS raw solve time data. From these raw solve times for each puzzle, XWStats (Matt) calculates global median solve times (GMSTs). Thus, the GMS is a composite of many different individuals who just so happened to fall at the 50th percentile on (at least) one particular puzzle's solve time distribution. Per personal communication with Matt, most puzzle dates have somewhere between 1-2K individual solver solve times recorded (with consent from each individual solver). I do *not* have the underlying raw data for solvers in the database apart from that for two experienced solvers, both of whom were also subjects of similar EDA analyses; [Individual Solver 1(IS1)](https://github.com/ursus-maritimus-714/NYT-XWord-EDA-Individual-Solver-1/blob/main/README.md) and [Individual Solver 2(IS2)](https://github.com/ursus-maritimus-714/NYT-XWord-EDA-Individual-Solver-2/blob/main/README.md) Both of these individual solvers will also be carried through predictive modeling in the near future as well. 

Please visit, explore and strongly consider financially supporting both of these wonderful sites; XWord Info via [membership purchase at one of several levels](https://www.xwordinfo.com/Pay) and XWStats via [BuyMeACoffee](https://www.buymeacoffee.com/xwstats). 

### Overview of NYT Crossword and GMS Characteristics
The NYT crossword has been published since 1942, and many consider the "modern era" to have started with the arrival of Will Shortz as (only) its 4th editor 30 years ago. A new puzzle for each day is published online at either 6 PM (Sunday and Monday puzzles) or 10 PM (Tuesday-Saturday puzzles) ET the prior evening. Difficulty for the 15x15 grids (Monday-Saturday) is intended to increase gradually across the week, with Thursday generally including a gimmick or trick of some sort (e.g., "rebuses" where the solver must enter more than one character into one or more squares). Additionally, nearly all Sunday through Thursday puzzles have themes, some of which are revealed via letters placed in circled or shaded squares. Friday and Saturday are almost always themeless puzzles, and tend to have considerably more open constructions and longer (often multiword) answers than the early week puzzles. The clue sets tend to be more wordplay heavy/punny as the week goes on, and the answers become less common in the aggregate as well. Sunday puzzles have larger grids (21x21), and almost always feature a wordplay-intensive theme to which the longest answers in the puzzle pertain. The intended difficulty of the Sunday puzzle is approximately somewhere between a tough Wednesday and an easy Thursday. 

**Figure 1** shows dimensionality reduction via Principal Component Analysis (PCA) of 23 grid, clue and answer-related features obtained from XWord Info. This analysis demonstrates that, while puzzles from a given puzzle day do indeed aggregate with each other in n-dimensional "puzzle property space", the puzzle days themselves nonetheless exist along a continuum. Sunday is well-separated from the other puzzle days in this analysis by PCA1, which undoubtedly incorporates one or more grid size-contingent features.   

**Figure 1. PCA of Select Puzzle Grid, Clue and Answer Features**                                                                  
![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/e4d69316-0aa3-4a41-9a2f-e560e8e424c2)
*<h5>The first 3 principal components accounted for 47.6% of total variance. All puzzles from Jan. 1, 2018- Feb. 10, 2024 were included in this analysis (N=2,232).*
###
The overlapping distributions in GMSTs in the solve time density plot in **Figure 2** show a parallel performance phenomenon to the continuum of puzzle properties seen in **Fig. 1**; namely that while solve difficulty increased as the week progressed, puzzle days of adjacent difficulty had substantially overlapping GMST distributions. Other than for the "easy" days (Monday and Tuesday), distributions of GMSTs were quite broad. Wednesday and Saturday also had somewhat multimodal solve time distributions, supporting the notion that there were "easy" and "hard" puzzle pools/constructors at the level of specific puzzle days. The broadness of each puzzle day-specific GMST distribution over the entire sample timeframe depicted here (2018-2024) was also increased by the fairly dramatic improvement in GMS performance over those 6+ years. The temporal dynamics of this improvement will be highly evident in the next section's figures.     

One additional contextual note about the GMS is worth mention upfront. Though Matt from XWStats uses the word "global", and I adopt it as well, it is highly likely that the sample from which the GMST is pulled per puzzle skews faster than the true population distributions. The reasons for this assumption are twofold; only solvers who actually complete a given puzzle are included in its sample, and each sample contains only solvers motivated enough by the prospect of improvement to track their own progress to begin with.

**<h4>Figure 2. Distributions of GMSTs by Puzzle Day for Full Sample Period**                   

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/5adabd60-54e9-43bf-8667-12d72cbdb67b)
*<h5>All puzzles from Jan. 1, 2018- Feb. 10, 2024 were included in this analysis (N=2,232).* 

### Key Outcomes from the GMS EDA
One of the most important findings from the EDA as far as implications for predictive modeling was that the GMS demonstrated marked improvement over the course of the sample period across all puzzle days (**Figure 3**). Coupled to the fact that puzzle day-specific, decay-time weighted recent past performance was highly positively correlated to performance on the next puzzle both overall (r=.86) and across puzzle days (**Figure 4**), this created an imperative to explore and potentially include different variants of this feature type in the predictive modeling stage.    

**Figure 3. GMS Solve Time Overview by Puzzle Day: 10-Puzzle Moving Averages and Distributions of Raw Values**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/b3f72248-99ed-463a-8ad3-547db6759d7d)
*<h5>All puzzles from Jan. 1, 2018- Feb. 10, 2024 were included in this analysis (N=2,232).* 

**<h4>Figure 4. Puzzle Day-Specific, Decay-Time Weighted Recent Performance (RPB) Correlation to GMS Performance on the Next Puzzle**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/5964b2fb-c2c1-4ce8-bbfc-459b73b1a595)
*<h5> Puzzle-day specfic, decay-time weighted GMS recent past performance (x-axis) was calculated over the 20 day-specific puzzles previous to the next solve (y-axis). All puzzles from Jan. 1, 2019- Feb. 10, 2024 were included in this analysis (N=1,867).* 

###
Along with the recent performance baseline (RPB) discussed above, multiple features pertaining to the puzzles themselves demonstrated moderately strong or strong correlations with GMS performance on individual puzzles (**Figure 5**). Two that stood out in particular for their correlational strength with GMS performance were 'Average Answer Length' (upper right panel) and 'Freshness Factor' (lower right panel), the latter of which is a proprietary XWord Info measure of the rareness of a given answer in the NYT puzzle. The density plots on the right hand side (below each correlation scatterplot) show that the distributions of these features that were highly correlated to GMST (for all 15x15 puzzles: r=.69 and .70, respectively) were well-separated across puzzle days. This is an important property for candidate predictive features to have since, as is shown in **Fig. 2**, distributions of solve times for individual puzzle days were themselves well-separated.   

**<h4>Figure 5. Correlations of Puzzle-Related and Past-Performance Features to GMSTs**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/fdcab939-16a7-4d03-b661-f0e30828b13c)
*<h5> All puzzles from Jan. 1, 2019- Feb. 10, 2024 were included in these analyses (N=1,867).*

## Methods

### Predictive Feature Generation
For predictive feature generation, all puzzles issued by NYT from Jan. 1 2018-Feb. 10, 2024 (N=2,232) were included. The right panel of **Figure 6** summarizes predictive features included at the outset of the modeling stage (N=38) by broad class. A few key example features from each class are mentioned below. **Supplementary Table 1** comprehensively lists out, classifies and describes all included features.  

* 'Solver Past Performance Features' (n=9) included a subset (3) of features capturing short- (decay time-weighted mean of the 10 puzzle day-specific puzzles immediately prior to a given puzzle being predicted on), intermediate- (same, but prior 25 puzzles) or long-term (same, but prior 40) GMS past performance. The left panel of **Fig. 6** depicts schematically the time-decay weighting of this feature subset. A number of temporal integration windows and decay curves were tested in univariate linear models for short-, intermediate- and long-term features, and smooth decay-weighting (e.g., 40,39,38,37...) yielded the best predictions. Later testing wih the full feature set revealed, however, that predictions were slightly better when including *only* long-term features from this class. Thus, the ultimate 'Best Model' included only smooth decay-weighted, long-term GMS past performance features (see Discussion for more on this subject).
 
* 'Puzzle: Clue or Answer Features' (n=19) included 'Freshness Factor', which measured the aggregate rarity of answers in a given puzzle across all NYT crossword puzzles from before or since the issue date. This class also included other measures of answer rarity, including the number of entirely unique answers in a puzzle and the 'Scrabble Score', which assigns corresponding Scrabble tile values to each letter in an answer (rarer letters = higher tile values, hence a different angle at assessing answer rarity). On the clue side of the ledger, this class also included a count of the frequency of wordplay in clues for a given puzzle ('Wordplay #). Later week puzzles contained more such clues, and early week puzzles often contained very, very few. 
* 'Puzzle: Grid Features' (n=11) included both the number of answers ('Answer #') and 'Average Answer Length' in a given puzzle. As puzzles got more difficult across the week, the former tended to decrease and the latter tended to increase. This class also included 'Open Squares #', which is a proprietary measure of XWord info capturing white squares not bordered by black squares (tended to increase as puzzles increased in difficulty across the week). This category also included features capturing other design principles of puzzles, including 'Unusual Symmetry'. This feature captured puzzles deviating from standard rotational symmetry (e.g., those with left-right mirror or diagonal symmetry), that could have had implications for their difficulty.  
* 'Puzzle Day' (n=1) was a class of one, simply assigning a number to the puzzle day of week for a given solve. 

**Figure 6. Overview of Decay-Time Weighting of Solver Past Performance Features, and Predictive Features By Class**
![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/b4ea856a-404e-4dd0-aa44-697c4edad5cd)


### Machine Learning Regression Modeling 
For the modeling phase, puzzles from the first year of the sample period were removed to minimize the potential effects of baseline performance volatility (see EDA linked in Intro and **Fig. 3**). This reduced the overall sample size to N=1,867. Importantly, as they were generated prior to this filtering, 'Solver Past Performance Features' included in modeling accrued from the beginning of 2018. Additonally, for the main model 21x21 puzzles (Sun) were also removed from the sample. This resulted in a final modeling 15x15 puzzle N=1,601. The 21x21 puzzles (N=266) were, however, included in by-puzzle-day modeling (see **Figure 8**). 

After predictive features were generated for each puzzle, the best regression model for prediction of the TF (raw GMS solve time, in minutes) was found ('Best Model'). To find 'Best Model', 4 different regression models were explored using [scikitlearn (scikit-learn 1.1.1)](https://scikit-learn.org/stable/auto_examples/release_highlights/plot_release_highlights_1_1_0.html): Linear, Random Forest, Gradient Boosting, and HistGradient Boosting. For evalaution of models including all 15x15 puzzles, a 75/25 training/test split (1,200/401 puzzles) and 5-fold training set cross-validation were used. Additionally, hyperparameter grid search optimization was used per model as warranted (for ex., for Gradient Boosting the grid search was conducted for imputation type, scaler type, learning rate, maximum depth, and maximum features). 'Best Model' (ie, lowest RMSE training error when hyperparameter-optimized) was a Linear Regression model. See the 'Model Metrics' csv files in the 'Reporting' folder for details, including how this model performed relative to the other models). Also, see **Supplementary Figure 1** for some details on the performance of 'Best Model' (ie, Data Quality Assessment, K-best features selection, and Feature Importances).

## Key Modeling Results

**1)** 'Best Model' predicted the TF (raw GMS solve time, in minutes) more accurately than did a univariate linear model with puzzle day-specific, mean Global Median Solver solve time *across the entire sample period* as the sole predictive input ('Mean PDS GMST'). 'Best Model' also outperformed a variant that simply guessed the mean of the training set TF, *across all 15x15 puzzle days*, for each individual puzzle ('Dummy').(**Fig. 7**). The 'Full Model' mean training error of 3.87 minutes, which corresponded to a 23.7% difference from the training set mean across all 15x15 puzzle days. In contrast, the 'Mean PDS GMST' and 'Dummy' benchmark models had mean training errors of 4.37 and 8.11 minutes, respectively (corresponding to 26.7% and 49.7% differences from the training set mean). 

**Figure 7. Best Model Prediction Quality vs Benchmark Models**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/ecc4bcea-3d5f-4fd3-883c-2755426158b8)
*<h5> 'Best Model' was a Linear Regression Model (K-best features = 23).* 

###
**2)** 'Best Model' was discovered on a puzzle day-specific basis (BPDM), including for the lone 21x21 puzzle day Sunday (**Figure 8**). Because GMS mean solve time per puzzle day varied considerably, training errors in **Fig. 8** were normalized to percentage difference from training set mean for that puzzle day. The 'Dummy' model in this puzzle day-specific context is analogous to the 'Mean PDS GMST' benchmark model in **Fig. 1**, as the 'Dummy' for all 15x15 puzzles guessed the *overall sample mean* for each puzzle regardless of puzzle day. 

Though the number of puzzles included in the BPDMs (N=266; +- 1) was much smaller than that in the all 15x15 puzzles model, each still outperformed its particular (not so dumb) 'Dummy'. However, high performance variability on later week puzzle days (Fri and Sat) can be seen in standard deviations that overlap with 'Dummy' model performance. Sunday (18.3% mean BPDM training error) and Monday (15.8%) stood out as the two most predictable individual puzzle days, with the other puzzle days ranging between 21-23% mean training error for BPDM. Finally, it is also worth noting that despite the much lower N's, on a % of mean solve time basis, each BPDM outperformed the 'Best Model' for all 15x15 puzzle days (27%; see **Fig. 1** and associated text). 

**Figure 8. Best Puzzle Day-Specific Model (BPDM) Prediction Quality**
![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/c943a509-9093-4296-bbaf-984afa3d746b)
*<h5> BPDM for each day was a Linear Regression Model, with hyperparameter optimization specific to that puzzle day. Due to the relatively small number of puzzles in the sample for each puzzle day, an 80/20 training/testing split was used to find each BPDM (~212/54 puzzles for each puzzle day). Data Quality Assessments for each BPDM were inconclusive regarding sufficency of number of puzzles included.* 

**<h4>Table S1. Features Included in Predictive Modeling**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/2dda8c9f-0268-4ff8-bd71-9cdcd6fa2383)


![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/acde78ac-1b63-44fe-8dc6-4b5253cd2388)
*<h5> Note: this is the complete set of features included at the outset of predictive modeling, but some were removed through the iterative process that led to 'Best Model' for all 15x15 puzzles selection. See Methods for details.*


