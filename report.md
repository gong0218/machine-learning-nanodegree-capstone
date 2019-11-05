# Machine Learning Engineer Nanodegree
## Capstone Project
Takayuki Sato  
November 28th, 2019

## I. Definition

### Project Overview
Sales forecasting is very important for retail business. An accurate sales forecast helps companies to
* Understand companys' performance / customer demand
* Change strategy for the companys' growth
* Conduct beneficial Marketing promotion
* Conduct better management of Inventory
* Adjust price adequately

Many factors, such as, Past sales result / Seasonality / Marketing promotion effect / Consumer Behavior / Price / Competitors' situation / weather / Economic indicators may affect sales forecasting result. There are also many methodologies for time series forecast [1, 2]. Companies need to identify important factors and select an adequate methodology for an accurate sales forecasting.

In this project, I created machine learning models for time series sales forecast. I used the histrical sales data for 45 Walmart stores located in different regions.

### Problem Statement
Walmart Inc. is an American multinational retail corporation that operates a chain of hypermarkets, discount department stores, and grocery stores. It is the world's largest company by revenue and the largest private employer in the world.

In their kaggle competition[(Walmart Store Sales Forecasting)](https://www.kaggle.com/c/walmart-recruiting-store-sales-forecasting/data), they provide historical sales data for 45 Walmart stores located in different regions. Each store contains many departments. The objective of this competition is to project the sales for each department in each store. To add to the challenge, selected holiday markdown events are included in the dataset. These markdowns are known to affect sales, but it is challenging to predict which departments are affected and the extent of the impact.

This is a typical time series problem. General time series modeling framework(e.g. ETS, ARIMA), Gradient Boosting framework(e.g. xgboost), and Neural Network framework(e.g. LSTM) are expected to be potential solutions.

### Metrics
I used the weighted mean absolute error (WMAE) as evaluation metric.

![WMAE](path/WMAE.png "WMAE") 

where

n is the number of rows
ŷ i is the predicted sales
yi is the actual sales
wi are weights. w = 5 if the week is a holiday week, 1 otherwise

By using the WMAE metric, I can select an accurate model for holiday seasons' sales that is especially important for Walmart.

## II. Analysis

### Data Exploration
The dataset provided by the kaggle competition.
[(Walmart Store Sales Forecasting)](https://www.kaggle.com/c/walmart-recruiting-store-sales-forecasting/data)
includes historical sales data for 45 Walmart stores located in different regions.

**stores.csv**

This file contains anonymized information about the 45 stores, indicating the type and size of store.

Below is top 5 rows of stores.csv and descriptive statistics;  

![store.csv](path/store_head.png "store.csv")  

![store.describe](path/store_describe.png "store.describe")

**train.csv / test.csv**

Train data is the historical weekly data, which covers to 2010-02-05 to 2012-11-01. Within this file you will find the following fields:

* Store - the store number
* Dept - the department number
* Date - the week
* Weekly_Sales -  sales for the given department in the given store
* IsHoliday - whether the week is a special holiday week  

Below is top 5 rows of train.csv and descriptive statistics;  

![train.csv](path/train_head.png "train.csv")    

![train.describe](path/train_describe.png "train.describe")  

Test data is the historical weekly data, which covers to 2012-11-02 to 2013-08-02, with the same fields except for sales.

**features.csv**

This file contains additional data related to the store, department, and regional activity for the given dates. It contains the following fields:

* Store - the store number
* Date - the week
* Temperature - average temperature in the region
* Fuel_Price - cost of fuel in the region
* MarkDown1-5 - anonymized data related to promotional markdowns that Walmart is running. MarkDown data is only available after Nov 2011, and is not available for all stores all the time. Any missing value is marked with an NA.
* CPI - the consumer price index
* Unemployment - the unemployment rate
* IsHoliday - whether the week is a special holiday week

Below is top 5 rows of features.csv and descriptive statistics;   

![feature.csv](path/feature_head.png "feature.csv")　　

![feature.describe](path/feature_describe.png "feature.describe")  

### Exploratory Visualization
The plot below shows aggregated sales by each day.
![time_series](path/time_series.png "time_series")  

Below chart is the decomposition of Total sales. We can see there is an upward trend in the series. There is also seasonality, huge spikes around every December.

![time_series_decomp](path/time_series_decomp.png "time_series_decomp")      

We can see almost the same tendency by each store/department, but some stores/departments seem to have small trend/seasonality.  
![time_series_byStore](path/time_series_byStore.png "time_series_byStore")  

![time_series_byDept](path/time_series_byDept.png "time_series_byDept")

Walmart has three types of store. big sized A type store has big sales, whereas small sized C type has small sales.
![store_pi_chart](path/pie_chart_update.png "store_pi_chart")  

![scatter_size_sales_byStore](path/scatter_size_sales_byStore.png "scatter_size_sales_byStore")

### Algorithms and Techniques
In this section, you will need to discuss the algorithms and techniques you intend to use for solving the problem. You should justify the use of each one based on the characteristics of the problem and the problem domain. Questions to ask yourself when writing this section:
- _Are the algorithms you will use, including any default variables/parameters in the project clearly defined?_
- _Are the techniques to be used thoroughly discussed and justified?_
- _Is it made clear how the input data or datasets will be handled by the algorithms and techniques chosen?_


In this project, I used Random Forest, XGBoost, and Long short-term memory for solving the problem. Weekly sales is the target.



The following parameters are mainly tuned to optimize the model(and a default parameter I tried at first.).

- Random Forest
  - number of estimators()(default: )
  - minimum sample leaf size()(default: )
  
- XGBoost
  - The fraction of columns to be randomly samples for each tree
  - Learning rate()(default: )
  - The maximum depth of a tree()(default: )
  - L1 regularization term on weight()(default: )
  - number of estimators()(default: )
  
- LSTM
  - number of layers()(default: )
  - number of nodes()(default: )
  - the number of hidden units()(default: )
  - epochs(The number times that the learning algorithm will work through the entire training dataset. One epoch consists of one full training cycle on the training set.)(default: 100)

### Benchmark
I used Decision Tree Regressor as a bench mark model and made comparison between all models performance and the benchmark. Decision Tree Regressor is a tree-like model that is simple to understand and interpret, but often relatively unstable.
On the test dataset, 

## III. Methodology

### Data Preprocessing
I conducted some feature engineering to make the given dataset to fit machine learning models.

- isHoliday data is bool type, so I casted that to integer[0,1].
- Markdown data has some NaN / negative values, so I convert that to zero.
- Converted Date(yyyy-mm-dd format) to "year", "month", "week", and "day".
- Store data is categorical, so I used One-Hot encoding.


For only LSTM, I used only 
I used Min-Max scaler to scale the input in the range of 0 to 1.


split 20 80 no shuffle

When building your first LSTM, you will quickly realize that your input data must be in the form of a 3-dimensional array. The three dimensions are:
Samples
Time Steps (or window size)
Features


### Implementation

- Random Forest
  - number of estimators:
  - minimum sample leaf size:
  
- XGBoost
  - The fraction of columns to be randomly samples for each tree:
  - Learning rate:
  - The maximum depth of a tree:
  - L2 regularization term: L2 regularization term on weight
  - number of estimators:
  
- LSTM implemented by using Keras module
  - number of layers:
  - number of nodes:
  - the number of hidden units:
  - epochs:

The LSTM model is implemented by using Keras module. The network has 

The model's performance is evaluated by its WMAE score on the test data.

### Refinement
In this section, you will need to discuss the process of improvement you made upon the algorithms and techniques you used in your implementation. For example, adjusting parameters for certain models to acquire improved solutions would fall under the refinement category. Your initial and final solutions should be reported, as well as any significant intermediate results as necessary. Questions to ask yourself when writing this section:
- _Has an initial solution been found and clearly reported?_
- _Is the process of improvement clearly documented, such as what techniques were used?_
- _Are intermediate and final solutions clearly reported as the process is improved?_

I conducted improvement of the algorithms' performance by Hyper parameter tuning.
Following parameters were tuned by using **GridSearchCV**.

XGBoost  

**alpha**: 
  - Initial value:  
  - Search values: [5,10,30,50,100]  
  - Final value: 10
  
**n_estimators**: 
  - Initial value:  
  - Search values: [5,10,30,50,100]  
  - Final value: 500
  
**max_depth**: 
  - Initial value:  
  - Search values: [5,10,14,30,50,100]  
  - Final value: 14

**learning_rate)**:  
  - Initial value:  
  - Search values: [0.1,0.3,0.5,0.7,1]  
  - Final value: 0.1
  
**min_child_weight**: 
  - Initial value:  
  - Search values: [0,0.2,0.4,0.6,0.8,1]  
  - Final value: 1

**colsample_bytree**:  
  - Initial value:  
  - Search values: [0.1,0.3,0.5,0.7,0.85 1]  
  - Final value: 0.85

**colsample_bylevel**: 
  - Initial value:  
  - Search values: [0.5,0.6,0.8,1]  
  - Final value: 1

**reg_lambda**: 
  - Initial value: 1
  - Search values: [0,1,2,4,6,8,10]  
  - Final value: 2

**subsample**: 
  - Initial value:  
  - Search values: [0.5,0.6,0.8,1]  
  - Final value: 0.8



 For LSTM, I tried to tune below hyper parameters.
 
**sequence length**: 
  - Initial value: 1 
  - Final value: 4 (that means 4 weeks/1 month.) 

**number of layers(and number of hidden units)**: 
  - Initial value: 2 with hidden units of 50-50
  - Final value: 3 with hidden units of 200-200-200 

**number of epochs**: 
  - Initial value: --
  - Final value: ---

## IV. Results

### Model Evaluation and Validation
In this section, the final model and any supporting qualities should be evaluated in detail. It should be clear how the final model was derived and why this model was chosen. In addition, some type of analysis should be used to validate the robustness of this model and its solution, such as manipulating the input data or environment to see how the model’s solution is affected (this is called sensitivity analysis). Questions to ask yourself when writing this section:
- _Is the final model reasonable and aligning with solution expectations? Are the final parameters of the model appropriate?_
- _Has the final model been tested with various inputs to evaluate whether the model generalizes well to unseen data?_
- _Is the model robust enough for the problem? Do small perturbations (changes) in training data or the input space greatly affect the results?_
- _Can results found from the model be trusted?_

### Justification
Below table shows the model performance(WMAE) of all models. Random Forest, XGBoost, and LSTM perform better than the benchmark Decision Tree. Although LSTM model uses only lagged sales data, the model  ---------. 

---- achieved WMAE ---- on the testing data, that is significantly better thant Decision Tree as the benchmark.

|  Model  |  WMAE  |
| ------  | ------ |
|  Decision Tree(BM)  |  6,875  |
|  Random Forest  |  4,425  |
|  XGBoost  |  4,395  |
|  LSTM  |  3,267  |

## V. Conclusion

### Free-Form Visualization
In this section, you will need to provide some form of visualization that emphasizes an important quality about the project. It is much more free-form, but should reasonably support a significant result or characteristic about the problem that you want to discuss. Questions to ask yourself when writing this section:
- _Have you visualized a relevant or important quality about the problem, dataset, input data, or results?_
- _Is the visualization thoroughly analyzed and discussed?_
- _If a plot is provided, are the axes, title, and datum clearly defined?_

### Reflection
In this section, you will summarize the entire end-to-end problem solution and discuss one or two particular aspects of the project you found interesting or difficult. You are expected to reflect on the project as a whole to show that you have a firm understanding of the entire process employed in your work. Questions to ask yourself when writing this section:
- _Have you thoroughly summarized the entire process you used for this project?_
- _Were there any interesting aspects of the project?_
- _Were there any difficult aspects of the project?_
- _Does the final model and solution fit your expectations for the problem, and should it be used in a general setting to solve these types of problems?_

### Improvement
In this project, Basically, I only used original features and forecast methodlogies since I had to take a lot of time for hyper parameter tuning. If I were to continue with this project, adding features, such as, lagged variables and other economic indicators(GDP growth, consumer confidence index, etc.) is one of improvements I should explore.   
Also, using a state space model could help improve the accuracy since this sales data obviously has seasonality and trend as I mentioned in Data Exploration section.



-----------

**Before submitting, ask yourself. . .**

- Does the project report you’ve written follow a well-organized structure similar to that of the project template?
- Is each section (particularly **Analysis** and **Methodology**) written in a clear, concise and specific fashion? Are there any ambiguous terms or phrases that need clarification?
- Would the intended audience of your project be able to understand your analysis, methods, and results?
- Have you properly proof-read your project report to assure there are minimal grammatical and spelling mistakes?
- Are all the resources used for this project correctly cited and referenced?
- Is the code that implements your solution easily readable and properly commented?
- Does the code execute without error and produce results similar to those reported?
