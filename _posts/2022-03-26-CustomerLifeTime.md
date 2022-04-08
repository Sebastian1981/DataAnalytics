---
title:  "Customer Lifetime Value Approximation Using Machine Learning"
mathjax: true
layout: post
categories: media
---


## Project Overview
>In marketing, customer lifetime value (CLV or often CLTV), lifetime customer value (LCV), or life-time value (LTV) is a prognostication of the net profit contributed to the whole future relationship with a customer. The prediction model can have varying levels of sophistication and accuracy, ranging from a crude heuristic to the use of complex predictive analytics techniques. One of the major uses of CLV is customer segmentation, which starts with the understanding that not all customers are equally important. CLV-based segmentation model allows the company to predict the most profitable group of customers, understand those customers' common characteristics, and focus more on them rather than on less profitable customers. CLV-based segmentation can be combined with a Share of Wallet (SOW) model to identify "high CLV but low SOW" customers with the assumption that the company's profit could be maximized by investing marketing resources in those customers. 
[**Ref:** https://en.wikipedia.org/wiki/Customer_lifetime_value]

In this project two seperate approaches were chosen to model the CLV. In the [CLV-regression approach](https://github.com/Sebastian1981/CustomerAnalytics_CLV/blob/main/CustomerLifetimeValue_Regression.ipynb), a regression model was fit to estimate the individual CLV of each customer. In the [CLV-classification approach](https://github.com/Sebastian1981/CustomerAnalytics_CLV/blob/main/CustomerLifetimeValue_Multiclass.ipynb), a binary classifier was fit to assign each customer to either the segment "Low-CLV" or "High-CLV". The machine learning models are build on historical customer data with the CLVs been pre-computed for each customers as shown below. Besides pure building and evaluating machine learnign models the focus was also set on model explainability and model fairness.

![datatable](https://user-images.githubusercontent.com/21213464/162038886-6a9c3d54-3a86-4b75-97a8-5086bfa248e6.jpg)



## Regression Modeling Results
### Exploratory Data Analysis (EDA)
The pearson correlation analysis between numeric features and the target (i.e. CLV) reveals moderate correlations for the features "Monthly Premium Auto" and "Total Claim Amount".

![PearsonCorrelation](https://user-images.githubusercontent.com/21213464/162038940-d67733dd-cb81-433e-a84e-283a8a5bd251.jpg)

The boxplots show a clear effect of the feature "Vehicle Class" on CLV.

![CLV_vehicle_class](https://user-images.githubusercontent.com/21213464/162039012-34c0ecdc-28c8-431a-ba5a-f5f4ad6d6488.jpg)

With respect to gender, no apparent effect on CLV can be observed which is an important precondition for model fairness.

![CLV_gender](https://user-images.githubusercontent.com/21213464/162006161-2ecb957d-bc22-45e4-8802-aef6b1db0d71.jpg)


### Model Evaluation
For modeling the CLV, a "Light Gradient Boosting Machine" algorithm (LightGBM) was used explaining 68% (=R2) of the variance on unseen customer data.

![CLV_regression_model](https://user-images.githubusercontent.com/21213464/162039110-b39b3007-e4c1-45cf-b9c4-64b4a9d43af2.jpg)

### Model Explanation 
#### Global Explanation
In consistence with the EDA-findings the SHAP values reveal that overall the features "Number of Policies" and "Monthly Premium Auto" have a significant impact on CLV. The feature "Vehicle Class" in return has only moderate impact. 

![regression_model_meanshap](https://user-images.githubusercontent.com/21213464/162039215-5eccbc03-4207-4cab-ae6a-e559bee784c7.jpg)

#### Local Explanation
With focus on local model explanation, the plot below shows the effects of the different features on the CLV of a single customer. We can see that the expected CLV is located around 8400 (base-value). The features "Monthly Premium Auto" and "Number of Policies" push the CLV to lower values of around 5100 whereas other features like "Income" have a positive effect increasing the CLV to the final value of around 5500.  

![regression_model_shaplocal](https://user-images.githubusercontent.com/21213464/162039242-0fa0f761-ae20-46d3-88a2-47e51f68adc3.jpg)


### Model Fairness
#### Unmitigated Model
With respect to model fairness, the figure below shows the gender-biased model performance in terms of R2 towards the female subgroup. This indicates that the model can predict the CLV for women clearly better than for men. 

![R2_gender_biased](https://user-images.githubusercontent.com/21213464/162387921-a631a8f2-e605-4f48-ab32-078867b98fb9.png)

To mitigate this effect, several models were trained to find the best trade-off between model performance and model fairness, as depicted below.

![model_fairness](https://user-images.githubusercontent.com/21213464/162388089-5449eebe-1ca1-4291-a78d-c9b43035491e.png)

As we can the, the average model performance of the unmitigated model was between r2=0.65 and r2=0.66 while the r2 group difference was between 0.08-0.10. However, to mitigate model unfairness, we could e.g. choose "model_mitigated #4" in order to clearly reduce the r2 group differnce to the range between 0.06-0.08 while preserving a strong model performance of r2 between r2=0.64 and r2=0.65.

