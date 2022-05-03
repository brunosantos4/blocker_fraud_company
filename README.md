# Blocker Fraud Company

> First development cycle completed

[![N|Solid](https://dsacpainc.com/wp-content/uploads/2020/11/Logo-of-Fraud-clipart-1024x768-1160x665.jpg)](https://nodesource.com/products/nsolid)

## 1.0 The Problem

> The company and the context are fictitious.

This fraud classification project uses data from a synthetic dataset generated by the PaySim mobile money simulator. The dataset is publicly available in [Kaggle](https://www.kaggle.com/ntnu-testimon/paysim1).

Blocker Fraud Company is a company specialized in detecting fraud in financial transactions made through mobile devices. The company has a service called “Blocker Fraud” in which it guarantees the blocking of fraudulent transactions.

The company's business model is of the service type with the monetization made by the performance of the service provided, that is, the user pays a fixed fee on the success in detecting fraud in the client's transactions.

However, the Blocker Fraud Company is expanding in Brazil and to acquire customers more quickly, it has adopted a very aggressive strategy. The strategy works as follows:

1 - The company receives 25% of each transaction value truly detected as fraud.

2 - The company gives back 5% of each transaction value detected as fraud, however the transaction is legitimate.

3 - The company gives back 100% of the value for the customer in each transaction detected as legitimate, however the transaction is actually a fraud.

Questions:

- What is the model's metrics?
- How reliable is the model in classifying transactions as legitimate or fraudulent?
- What is the expected billing by the company if we classify 100% of transactions with the model?


## 2.0 The solution

Create a machine learning model that maximizes the number of correct classifications.

- To increase earnings, it is necessary to obtain a large amount of values truly predicted as fraud
- To reduce losses, it is necessary to obtain the minimum of (in order of relevance):
    - False negative (transaction detected as legitimate, however the transaction is a fraud)
    - False positive (transaction value detected as fraud, however the transaction is legitimate)

The accuracy is not a good metric for this problem because the base is very unbalanced, naturally the accuracy of the model will be high as it has a lot of non-fraud cases. The best metric to evaluate the model is Recall, since it is desired to decrease the number of false negatives. It is much worse not to consider fraud and to be really a fraud.

## 3.0 Solution Steps


The notebook is structured as:

- 1. Data description
- 2. Feature Engineering
- 3. Variable filtering
- 4. Exploratory data analysis
- 5. Data preparation
- 6. Feature Selection
- 7. Balancing Data
- 8. Machine Learning Modelling
- 9. Hyperparameter fine tuning
- 10. Translation and interpretation of the error

Below are some points of the project.

### 3.1 Data description

#### Numerical

![alt text](img/data_description_numerical.png "Title")

Key points:

- The median of the attributes "oldbalance_org", "newbalance_org", "oldbalance_dest", "newbalance_dest" and "amount" has a very different value in relation to the range, which indicates a large number of outliers (which can also be seen in boxplots).

![alt text](img/data_description_boxplot.png "Title")

- There are a lot of outliers in the dataset. We have two alternatives, or we leave outliers, since they can be just fraudulent transactions, or we can treat them and use a cleaner dataset. For this first cycle of the project I will make the first alternative.

![alt text](img/data_description_boxplot_2.png "Title")

#### Categorical

![alt text](img/data_description_categorical.png "Title")

- It is possible to see that data is highly unbalanced. Only 8213 out of 6362620 are frauds (0.1291 %) 

### 3.2 Hypothesis Map

This map helped to decide wich one of the variables were necessary to validate the hypothesis.

![alt text](img/MindMapHypotesis.png "Title")


### 3.3 Hypotesis validation - bivariate analysis

#### H2. Transfer transaction is more likely to be a fraud.

![alt text](img/h2.png "Title")

> *False.* Only transfer and cash-out transactions are fraudulent. cash-out has 4116 frauds, slightly higher than transfer (4097)

#### H3. The higher the value of the transaction the greater the chance of being fraud.

![alt text](img/h3_1.png "Title")

![alt text](img/h3_2.png "Title")

> *True**. The hypothesis is true, but only in amounts below 10,000,000. Above that amount there aren't cases of fraud

#### H9. Transactions made outside normal hours are more likely to be fraud. (normal: 7h - 0h, not normal: 0h - 7h)

![alt text](img/h9.png "Title")

> *True*.

#### Hypothesis summary

| Hypotesis | Conclusion | Relevance | |
| ------ | ------ | ------ | ------ |
| H1 | True | High | Transaction that has Fraud flagged is always a fraud. |
| H2 | False | High | Transfer transaction is more likely to be a fraud. |
| H3 | True | Medium | The higher the value of the transaction the greater the chance of being fraud. |
| H4 | True | High | Zero-value transactions are more likely to be fraud. |
| H5 | False | Medium | Transactions whose initial balance is zero is never a fraud. (balance that started the transaction) |
| H6 | True | High | Transactions whose final balance is zero are more likely to be a fraud. (balance that started the transaction) |
| H7 | False | Low | Transactions that differ between the old balance and the new balance are more likely to be fraudulent. |
| H8 | True | Low | Transactions made and received by non-merchants are more likely to be a fraud. |
| H9 | True | Low | Transactions made outside normal hours are more likely to be fraud. |
| H10 | False | Low | Transactions made on the first 15 days of the month are more likely to be a fraud. |

### 3.4 Multivariate Analysis

![alt text](img/multivariate.png "Title")

These correlations can be useful for the business team to have a quick view of the relationships of the business variables. Both numerical and categorical multivariate analysis can be used later in the project.

![alt text](img/multivariate_2.png "Title")

### 3.5 Machine Learning

![alt text](img/ml.png "Title")

The Random Forest had the best result. Doing the cross validation, for each model, we obtain:

![alt text](img/ml_2.png "Title")

Therefore, the model we will use will be the Random Forest

### 3.6 Error Translation and Interpretation.

#### Overall performance

For every 1000 transactions made, according to Random Forest Cross Validation, the results are:

- 999,997 +/- 0,00169 correct classifications (true positives + true negatives). &emsp; &emsp; -> accuracy

- 99,9994% +/- 0,000337% of all frauds are detected. &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; -> Recall

- 99,9999% of transactions classified as fraud, are really fraud. &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; -> Precision

#### Performance in an example

Using a sample of 1908786 transactions:

![alt text](img/7.4.im.png "Title")

1 - Blocker Fraud Company will receive $856,775,411.85 due to transactions truly detected as fraud

2 - Blocker Fraud Company will lose $12,591.51 due to transactions detected as fraud, however the transaction is legitimate.

3 - Blocker Fraud Company will lose $3,253,801.45 due to transactions detected as legitimate, however the transaction is a fraud


## 4 Next Steps

- Use other machine learning models, such as Isolation Forest, an unsupervised model that is very good in the presence of outliers.
- Treat outliers and use models that benefit from this.
- Add more parameters in random search cross validation, or use GridSearch.

