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
![image](https://github.com/ursus-maritimus-714/NYT-XWord-EDA-Global-Median-Solver/assets/90933302/f2528b84-ddde-4555-8a00-063a6c818723)
*<h5>The first 3 principal components accounted for 47.6% of total variance. All puzzles from Jan. 1, 2018- Jan. 31, 2024 were included in this analysis (N=2,222).*
###
The overlapping distributions in GMSTs in the solve time density plot in **Figure 2** show a parallel performance phenomenon to the continuum of puzzle properties seen in **Fig. 1**; namely that while solve difficulty increased as the week progressed, puzzle days of adjacent difficulty had substantially overlapping GMST distributions. Other than for the "easy" days (Monday and Tuesday), distributions of GMSTs were quite broad. Wednesday and Saturday also had somewhat multimodal solve time distributions, supporting the notion that there were "easy" and "hard" puzzle pools/constructors at the level of specific puzzle days. The broadness of each puzzle day-specific GMST distribution over the entire sample timeframe depicted here (2018-2024) was also increased by the fairly dramatic improvement in GMS performance over those 6+ years. The temporal dynamics of this improvement will be highly evident in the next section's figures.     

One additional contextual note about the GMS is worth mention upfront. Though Matt from XWStats uses the word "global", and I adopt it as well, it is highly likely that the sample from which the GMST is pulled per puzzle skews faster than the true population distributions. The reasons for this assumption are twofold; only solvers who actually complete a given puzzle are included in its sample, and each sample contains only solvers motivated enough by the prospect of improvement to track their own progress to begin with.

**<h4>Figure 2. Distributions of GMSTs by Puzzle Day for Full Sample Period**                   

![image](https://github.com/ursus-maritimus-714/NYT-XWord-EDA-Global-Median-Solver/assets/90933302/f97270a9-e4cf-425f-8a6d-97a472bf62d1)
*<h5>All puzzles from Jan. 1, 2018- Jan. 31, 2024 were included in this analysis (N=2,222).* 

### Key Outcomes from the GMS EDA
One of the most important findings from the EDA as far as implications for predictive modeling was that the GMS demonstrated marked improvement over the course of the sample period across all puzzle days (**Figure 3**). Coupled to the fact that puzzle day-specific, decay-time weighted recent past performance was highly positively correlated to performance on the next puzzle both overall (r=.86) and across puzzle days (**Figure 4**), this created an imperative to explore and include different variants of this feature type in the predictive modeling stage.    

**Figure 3. GMS Solve Time Overview by Puzzle Day: 10-Puzzle Moving Averages and Distributions of Raw Values**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/f3aca92d-df5a-4312-8c8c-a6efb3ff582c)

**<h4>Figure 4. Puzzle Day-Specific, Decay-Time Weighted Recent Performance (RPB) Correlation to GMS Performance on the Next Puzzle**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/4b449a5d-bf13-403e-80cd-bc255176fda5)
*<h5> Puzzle-day specfic, decay-time weighted GMS recent past performance (x-axis) was calculated over the 20 day-specific puzzles previous to the next solve (y-axis).* 

###
Along with the recent performance baseline (RPB) discussed above, multiple features pertaining to the puzzles themselves demonstrated moderately strong or strong correlations with GMS performance on individual puzzles (**Figure 5**). Two that stood out in particular for their correlational strength with GMS performance were 'Average Answer Length' (upper right panel) and 'Freshness Factor' (lower right panel), the latter of which is a proprietary XWord Info measure of the rareness of a given answer in the NYT puzzle. The density plots on the right hand side (below each correlation scatterplot) show that the distributions of these features that were highly correlated to GMST (for all 15x15 puzzles: r=.69 and .70, respectively) were well-separated across puzzle days. This is an important property for candidate predictive features to have since, as is shown in **Fig. 2**, distributions of solve times for individual puzzle days were themselves well-separated.   

**<h4>Figure 5. Correlations of Puzzle-Related and Past-Performance Features to GMSTs**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/fdcab939-16a7-4d03-b661-f0e30828b13c)

## Methods

### Predictive Feature Generation
The right panel of **Figure 6** summarizes predictive features generated and included in the modeling stage (N=38), by class. A few key example features from each class are mentioned below. **Supplementary Table 1** comprehensively lists out, classifies and describes all included features.  

* 'Solver Past Performance Features' (n=7) included features capturing recent (decay time-weighted mean of prior 10 day-specific puzzles) or longer-term (same, but prior 25) GMS performance prior to a given solve. The left panel of **Fig. 6** depicts schematically the time-decay weighting of these two features. A number of decay curves were tested in univariate linear models (see here)[for both short and long-term features, and smooth decay weighting (25,24,23.... or 10,9,8...) yielded the best predictions. This class also included mean prior performance against the specific constructor(s) of a given puzzle, normalized to account for both GMS form at the time of each solve and for the mix of puzzle days for past puzzles by specific constructor(s).  
* 'Puzzle: Clue or Answer Features' (n=19) included 'Freshness Factor', which measured the aggregate rarity of answers in a given puzzle across all NYT crossword puzzles before or since the issue date. This class also included other measures of answer rarity, including the number of entirely unique answers in a puzzle and the 'Scrabble Score', which assigns corresponding Scrabble tile values to each letter in an answer (rarer letters = higher tile values, hence it's different angle at assessing answer rarity). On the clue side of the ledger, this class also included a count of the frequency of wordplay in clues for a given puzzle. Later week puzzles contained more such clues, and early week puzzles often contained very, very few. 
* 'Puzzle: Grid Features' (n=11) included both the number of answers and the average answer length in a given puzzle. As puzzles get more difficult across the week, the former tends to decrease and the latter tends to increase. This class also included the number of open squares, which is a proprietary measure of XWord info capturing white squares not bordered by black squares (tends to increase as puzzles increase in difficulty across the week). This category also included features capturing other design principles of puzzles, including when puzzles deviated from standard rotational symmetry (e.g., left-right mirror or diagonal symmetry), that could have implications for their difficulty.  
* 'Puzzle Day' (n=1) was a class one one, simply assigning a number to the puzzle day of week for a given solve. 


**Figure 6. Overview of Decay-Time Weighting of Solver Past Performance Features, and Predictive Features By Class**
![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/87bd047a-ab03-49ce-beb2-3e7c07f3c339)





**<h4>Table S1. Features Included in Predictive Modeling**

![image](https://github.com/ursus-maritimus-714/NYT-XWord-Modeling-Global-Median-Solver/assets/90933302/85179d7f-b8d5-45ef-b9ba-1183c0378b7c)
