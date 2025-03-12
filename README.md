### Introduction
---
This is an ML initiative aimed at developing a model that forecasts the outcome of matchups in the NCAAM tournament. The project began by filtering a Kaggle dataset to include only the top 64 teams based on ESPN’s BPI and scraping ESPN for game scores and outcomes from the 2024–25 season. The target variable was defined by whether team_1 won or lost, and 26 features were created by computing the differences between the statistics of the two competing teams. The data was then split into training/validation and test sets, with a StandardScaler applied to maintain uniformity, and class imbalance was addressed using class weight adjustments.

After evaluating several models—including Decision Tree, Logistic Regression, Gradient Boosting, and Random Forest—the Random Forest model emerged as the most effective, achieving consistent accuracy around 68% on both validation and test sets, with superior precision and competitive recall. Cross-validation further confirmed its robustness, while a baseline Dummy classifier underscored the improvements made. Feature importance analysis highlighted that BPI was the most critical predictor, followed by Net Rating and Adjusted Defensive Efficiency, validating the overall approach and reinforcing the Random Forest model as the optimal choice for forecasting NCAAM tournament outcomes.

### Data Preparation

1) The top 64 teams from ESPN were extracted based on the Basketball Power Index Rank (BPI RK).
   
3) The Kaggle dataset was filtered to include only these 64 teams for the 2025 season.
   
5) Above BeautifulSoup script was utilized to scrape the ESPN website for the scoreboard of the 2024–25 NCAA Men’s Basketball games, capturing each matchup (team_1 vs team_2) along with the game scores.
   
7) The 'winner' column, derived from the scraping process, served as the target variable, indicating whether team_1 won (class 1) or lost (class 0).

8) Features were engineered by calculating, for each matchup, the difference between the statistics of team_1 and team_2, resulting in a total of 26 features per game.

9) Features underwent selection and aggregation across two primary categories: ***Team Stats*** and ***General Info***. The Team Stats category is divided into three subcategories: (A) Efficiency & Shooting, (B) Game Dynamics & Possession, and (C) Overall Team Performance & Experience. The General Info category comprises contextual columns that do not directly reflect in-game performance but remain significant for modeling or identification. Below is an overall description of all features and the applied aggregation methods:

#### Team Stats
---
A. Efficiency & Shooting Metrics

---

*Adjusted Offensive Efficiency:*
    Points scored per 100 possessions, adjusted for opponent strength. Higher values indicate a more efficient offense.

*Adjusted Defensive Efficiency:*
    Points allowed per 100 possessions, adjusted for opponent strength. Lower values indicate a stronger defense.
    
*eFGPct (Effective Field Goal Percentage):* Adjusts field goal percentage to account for the extra value of three-point shots.

*FTRate (Free Throw Rate):* Ratio of free throw attempts to field goal attempts, indicating how frequently the team gets to the line.

*FG2Pct (2-Point Field Goal Percentage):* Success rate on two-point shots inside the arc.

*FG3Pct (3-Point Field Goal Percentage):* Success rate on shots taken from beyond the arc.

*FTPct (Free Throw Percentage):* Conversion rate on free throws.

*BlockPct (Block Percentage):* Percentage of opponent two-point shots blocked by the team.

*OppFG2Pct (Opponent 2-Point Field Goal Percentage):* Opponents’ success rate on shots inside the arc, reflecting the team’s ability to defend close-range attempts.

*OppFG3Pct (Opponent 3-Point Field Goal Percentage):* Opponents’ success rate on shots from beyond the arc, reflecting the effectiveness of the perimeter defense.

*OppFTPct (Opponent Free Throw Percentage):* Conversion rate on opponents’ free throws.

*OppBlockPct (Opponent Block Percentage):* Percentage of the team’s own two-point attempts that are blocked by opponents.

---

B. Game Dynamics & Possession Metrics

---

*Adjusted Tempo:* Estimated number of possessions per 40 minutes, adjusted for opponent, indicating the pace of play.

*TOPct (Turnover Percentage):* Percentage of possessions that end in turnovers; lower values are preferable.

*ORPct (Offensive Rebound Percentage):* Percentage of available offensive rebounds secured by the team.

*ARate (Assist Rate):* Percentage of made field goals that are assisted, reflecting team ball movement.

*StlRate (Steal Rate):* Percentage of defensive possessions that result in a steal by the team.

*OppStlRate (Opponent Steal Rate):* Percentage of the team’s offensive possessions that end in a steal by the opponent.

---

C. Overall Team Performance & Experience

--- 
**Net Rating:** Difference between offensive and defensive ratings (points scored minus points allowed per 100 possessions).

**Experience:** A measure of the team’s overall experience, often weighted by minutes played.

**W-L:** Overall win-loss record, serving as a straightforward indicator of team success.

**BPI (Basketball Power Index):** ESPN’s comprehensive power metric for the team, combining aspects of offense, defense, and pace.

---


#### General Info

---
**Active Coaching Length:** Indicates the duration of the current coach’s tenure.

**SOS RK (Strength of Schedule Rank):** Rank that reflects the overall difficulty of the team’s schedule; lower rankings denote more challenging schedules.

**NC SOS (Non-Conference Strength of Schedule):** Reflects the difficulty of non-conference games, which can differ significantly from conference play.

---

### Data Sets

The DataSet is prepared from Espn and Kaggle based on the above plan and is compiled in the files:
-  NCAA_mens_data - Feature Engineered Data
      - 509 rows * 34 columns
      - Data wrangling and aggregation done through Microsoft Excel
-  raw_ncaa_mens_data - Raw data scrubbed from the websites

The Data wrangling and aggreation were done through Excel Spreadsheet. 
### Exploratory Data Analysis:

Exploratory Data Analysis helps to provide the key insights and data spread and distributions of the data visually. EDA on data spread across the team match ups, correlation heat maps and some insights on the net ratings.

In first picture below it shows the heat map with the imported dataset of matchups with Team1 and Team2 stats. The second picture shows the heatmap after team1 and team2 stats are aggregated by taking difference of each of respective stats and are introduced as new features instead of just the team specific stats.

| Heat Map on All Imported Features | Heat Map after Feature Engineering |
| --- | --- |
|![image](https://github.com/user-attachments/assets/2f39d369-7ba7-445e-b1b7-9acd9590b0ec)|![image](https://github.com/user-attachments/assets/47d55853-a75a-4aaf-a05b-34131a058e29)

<i> Note: Click the pictures to zoom in for detailed view </i>

#### Observations:
   - The heatmap on the imported dataset clearly shows no feature is correlated strongly to the 
   - BPI, Net Rating and Wins are positively correlated to the winner
   - Loss, Adjusted Defensive efficiency, Opponent Field Goal Percents are negatively correlated to the winner
   - The heatmap clearly shows weaker opponent teams with lower defensive efficiency and low field goal percents contribute to the win.

The Net Rating differences are calculated for each matched up team pair and the Top 20 matched up pairs with high net ratings are plotted to see the team performances in the match ups. The first picture below shows the plot. The Next picture represents how many records per team are available in the historical data we pulled. There are some teams that got lot of match ups with different teams which would clearly provide lot of information about the team performance. There are some teams with very few matchups and hence have less records in the data set. Determining their performances would not be accurate as there is not much history.

| Top 20 Match up Net Ratings | Match up Data Spread among Teams |
| --- | --- |
| <img width="500" alt="image" src="https://github.com/user-attachments/assets/531b86ef-ad25-4a14-a76e-450b6cda8392" /> | <img width="500" alt="image" src="https://github.com/user-attachments/assets/511067b9-5362-4a6a-8266-d0a5403137d4" /> |

#### Observations:
- Out 64 Teams, the above 20 teams represent top performing teams based on Net Ratings
- Duke Blue Devils is topping the list with highest Net Ratings across other teams in the top20 it played with
- Houston Cougers and Auburn Tigers comes next to Duke Blue Devils
- Missouri Tigers is the poorly performing team with negative net ratings with all the teams it played with

**Model Training and Validation:**

The dataset was first divided into training, validation, and test sets, with the "Winner" column serving as the target variable to differentiate between wins and losses. Given that the features were on different scales, feature scaling was applied to standardize the data.

Subsequently, multiple models—Decision Tree, Random Forest, Logistic Regression, and Gradient Boosting—were trained and evaluated. A loop was used to iterate over various hyperparameters, optimizing each model based on accuracy. In addition, performance metrics such as F1 score, precision, and recall were considered, and class imbalance was handled using a class weight adjustment. The Random Forest model and Gradient Boosting model got tied as the top performer, achieving an accuracy of 68.6% on the validation and test data set. Logistic Regression at 59.8%, and Decision Tree at 53.9%. Random Forest also delivered the highest precision and the second-highest recall, resulting in the second-best F1 score.

To further verify its performance, Random Forest was subjected to cross-validation, which confirmed its robustness by yielding an accuracy of 69% (with an F1 score of 52%), closely matching the 68% accuracy of the best-tuned model. When the optimal hyperparameters were applied to the test set, the results remained consistent at 68%.

A Dummy model was also used as a baseline comparison. Since it did not account for class imbalance, its predictions were skewed towards the majority class, resulting in an accuracy of only 54% and an F1 score of 37%. This underscored the superior performance of the Random Forest model.

| Model Peformances | Feature Importance |
| --- | --- |
|<img width="500" alt="image" src="https://github.com/user-attachments/assets/5decc0e0-7e49-46af-9eca-7d7cb653afae" />|<img width="500" alt="image" src="https://github.com/user-attachments/assets/801adea7-826b-4bc6-9e17-64b8627b6bdd" />|

### Bracket Prediction

With the best model being Random Forest is chosen and matchups are randomized between the 64 teams and bracket is built based on the model prediction. With the winners of all the matchups that occured on the randomized bracket the NCAA championship winner is predicted.

### Conclusion

In conclusion, after evaluating multiple models, Random Forest and Gradient Boosting outperformed the other models. The Random Forest Model was quicker compared to Gradient Boosting model, making it the most suitable model for predicting the winner of matchups in NCAAM tournament. With this model, the NCAA champship winner is predicted by randomized matchup and building the bracket.

The Variables Net Rating, BPI that highly correlated to Target Variable Winner during the EDA also took the top spots in Variable importance of the Random Forent Model.

### How to Run:

Download NCAAM_final_without_names.ipynb notebook and run all the cells.



