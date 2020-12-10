# Examining Effects of Home Field Advantage in European Soccer

![jpg](Home_Field.jpg)

## Introduction

### Research Question
For most of my life I have enjoyed both playing and watching soccer. Whenever an upset occurs, a highly ranked team losing to a much lower ranked team, there are many factors that are investigated to see why the match had that outcome. One of these factors is home field advantage. Teams often perform better when playing on a field they are comfortable surrounded by supportive fans.

In order to understand this phenonemnon of why playing seems to improve the likelihood of a win, this report will seek to answer the following questions:  
 - What factors of the match most positively affect win likelihood when playing at home?
 - What factors of the match most negatively affect win likelihood when playing at home?

## Data

The data contains information for the 2018-2019 season from five different professional European leagues totaling 1,862 matches worth of information with 62 different aspects of the matches having been recorded. The data can be found on datahub at the following link: https://datahub.io/collections/football.

### Preparing the Data
All five datasets were merged in order to obtain a large enough sample. Descriptions of the most relevant features for this merged dataset can be found below under "Variable Descriptions". Overall, factors of goals scored, shots on target, fouls committed, corner kicks taken, and yellow and red cards received for both the home and away teams were included for predicting whether or not a team would win at home.

Missing values were first replaced with na and later, during model preparation, were imputed with the mean for that variable. Several features also needed to be created for proper model performance. These included Home_Half_Win and Home_Win which represent a binary outcome for whether or not the home team was winning at half and/or won the match (1 being a win, 0 being a loss or draw).


### Variable Descrpitions

 - Div = League Division
 - Date = Match Date (dd/mm/yy)
 - HomeTeam = Home Team
 - AwayTeam = Away Team
 - FTHG = Full Time Home Team Goals
 - FTAG = Full Time Away Team Goals
 - FTR = Full Time Result (H=Home Win, D=Draw, A=Away Win)
 - HTHG = Half Time Home Team Goals
 - HTAG = Half Time Away Team Goals
 - HTR = Half Time Result (H=Home Win, D=Draw, A=Away Win)
 - Referee = Match Referee
 - HS = Home Team Shots
 - AS = Away Team Shots
 - HST = Home Team Shots on Target
 - AST = Away Team Shots on Target
 - HC = Home Team Corners
 - AC = Away Team Corners
 - HF = Home Team Fouls Committed
 - AF = Away Team Fouls Committed
 - HY = Home Team Yellow Cards
 - AY = Away Team Yellow Cards
 - HR = Home Team Red Cards
 - AR = Away Team Red Cards


### Exploring the Data
Based on summary statistics, it appears not only that the home team won more often than the away team (60% of the time), but that the home team also seems to perform better on average in nearly every metric, getting more shots with greater accuracy as well as committing fewer fouls leading to fewer bookings (yellow and red cards). This leads us to believe initially that most factors improve while playing at home. The question we must answer however is which of these factors are most important in helping a team win at home.

The pattern of performing better at home also seems to be true accross each division in the dataset as seen below, making it more appropriate to collapse the dataset accross the division factor. It does appear that the German and Italian leagues slightly outperform the other divisions in some aspects of the match, but these differences don't seem to be significant and therefore will not be considered in modeling.


    % Wins by Home Team:  60.0
    
    Mean Differences (Home - Away) Per Game:  
     Goals Scored:       0.339 
     Shots on Target:    0.964 
     Corner Kicks Taken: 1.066 
     Fouls Committed:   -0.274 
     Yellow Cards:      -0.273 
     Red Cards:         -0.032

    Percent Differences (Home vs. Away) Per Game:  
     Goals Scored:       28 % Increase 
     Shots on Target:    24 % Increase 
     Corner Kicks Taken: 24 % Increase 
     Fouls Committed:     2 % Decrease 
     Yellow Cards:       13 % Decrease 
     Red Cards:          28 % Decrease
    



![png](output_18_1.png)


![png](output_19_1.png)


![png](output_20_1.png)


## Methods & Results

### Modeling Home Field Advantage

In order to answer our questions of interest, several machine learning models were considered for predicting home wins, and the data was divided using a 30-70 split into a training and test dataset with missing values being imputed with the mean for that variable.

Because our model will contain 14 explanatory variables, regression models like ridge, lasso, and k nearest neighbors were used to help determine which factors of the model were of most importance while penalizing less signficant factors in the model. For comparison, decision tree, multinomial naive bayes, and support vector regression models were also considered. In order to assess model performance, the mean squared error will be assessed for both the training and testing datasets to uncover any under or over fitting of the data. This was coupled with the f1 and accuracy score for each model.

### Fitting Initial Models

![png](First_mse_comp.png)

![png](First_score_comp.png)

### Model Improvement & Optimization

Based on the mean squared error, accuracy score, and f1 score for each model, several models seemed to have performed well while others appear to have overfit the data. Because the mse both the support vector and decision tree regression models was quite a bit higher for the testing than the training data, it appears these models overfit the data and will no longer be considered.

The Lasso, Ridge, and K Nearest Neighbors models all seemed to perform the best without overfitting, based on their f1 and accuracy scores as well as the mean squared errors. Using grid searching techniques, optimization was performed for each of the model's hyperparameters. For lasso, 100 different alpha tuning parameters were assessed and the optimal parameter can be seen below. A similar process was done for ridge, with 10 different tuning parameters. Lastly, k nearest neighbor tuning parameters with a range of 30 different groupings were performed. Each model was then fit once again using these optimized tuning parameters.

![png](output_42_1.png)

![png](Model_comp.png)

## Model Selection & Results

With optimized hyperparameters, both the Lasso and Ridge regression models performed the best in predicting home wins based on the explanatory variables, with f1 and accuracy scores of about 0.98. However, the lasso regression model had a lower mean squared error than the ridge model and will thus be considered in examining feature extraction and interpretation/

Based on an evaluation of feature importance from this optimized model, it appears that the most important factors in predicting home wins were goals scored, shots on target, and number of red cards each home and away team had. However, because goals scored are inseperable from the result of the game, a model was also considered that elminated this factor and is expounded on below. After accounting for this factor, the next most important factor in improving a win at home for the home team is shots on target followed by red cards received.

    Feature: 0, Score: 0.23030
    Feature: 1, Score: -0.18864
    Feature: 2, Score: -0.00685
    Feature: 3, Score: 0.00236
    Feature: 4, Score: 0.00791
    Feature: 5, Score: -0.00571
    Feature: 6, Score: -0.00498
    Feature: 7, Score: 0.00004
    Feature: 8, Score: -0.00313
    Feature: 9, Score: 0.00037
    Feature: 10, Score: 0.00136
    Feature: 11, Score: -0.00477
    Feature: 12, Score: -0.04249
    Feature: 13, Score: 0.04125
    


![png](output_39_1.png)


### Examining a Model without Goals as an Explanatory Variable
Lasso Model without goals scored as an explanatory variable:
  - MSE: 0.42079149568745594
  - Accuracy: 0.7317518248175182
  - F1 Score: 0.7302682346959217
    
    
Feature Importance:
    - Feature: 0, Score: -0.01398
    - Feature: 1, Score: 0.00757
    - Feature: 2, Score: 0.08074
    - Feature: 3, Score: -0.07494
    - Feature: 4, Score: -0.00690
    - Feature: 5, Score: -0.00272
    - Feature: 6, Score: -0.01955
    - Feature: 7, Score: 0.00802
    - Feature: 8, Score: -0.02004
    - Feature: 9, Score: -0.00036
    - Feature: 10, Score: -0.13964
    - Feature: 11, Score: 0.09090
    


![png](output_43_1.png)


## Conclusions
Based on the results of the regression modeling, it appears that the most important factor for improving a win at home was goals. This is no surprise since it is the single most determining factor for which a result is determined. The next most important feature for improving win likelihood at home was shots on target, with the away team's number of shots on target having a nearly equal effect but in the negative.

This means that if a team focuses on accuracy rather than quantity of shots, their overall probability of winning may increase. It may also mean that great defending that keeps an away team from getting their own shots on target would also increase win probability.

Lastly, the third most important factor for increasing win likelihood at home for the home team is red cards. When a player receives a red card in any match, they must immediately leave the pitch and they cannot be replaced. This results in the player's team having to play with 10 instead of 11 players on the field for the remainder of the match. Thus, the model shows that a home team receiving a red card will more negatively impact their likelihood of winning than any other factor. Likewise, an away team receiving a red card will improve win likelihood more than any other factor.

While controlling the amount of red cards an away team recevies is not in the power of the home team to change, they can exhibit extreme caution for their own team, ensuring that their players remain level-headed and don't commit any offense that would result in them having to leave the pitch due to a red card. Perhaps coaches could do so by encouraging good sportsmanship among their players and pulling players that start to show signs of losing their temper or getting unnecessarily aggressive towards an opposing player.


### Questions & Improvements for Future Study
One question that arises from the results of this analysis is what other factors might be affected by playing at home, negatively or positively? For example, perhaps pass accuracy or number of tackles also increase when a team plays on their home field, how might these affect the likelihood of winning?

It should be noted as well that a model that does not include red cards received would perhaps highlight more clearly the differences in variables of somewhat less significance. Obtaining a larger sample size over more years of data would also be an interesting way to see if these factors of red cards and shots on target would still remain significant or if others would overtake them.
