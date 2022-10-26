# Question 1
Linear regression, RANSAC, and regression error bounds


## (a) [To be solved by hand. Your answers may be handwritten or typed.]  
Suppose we set the following parameters for RANSAC.  
• 𝒔 : number of samples per draw  
• 𝒆 : probability of a point being an outlier  
• 𝒑 : desired probability that we get a good draw  
• N : number of draws needed to reach the above probability.

### i. What is the probability that all the samples in the draw are inliers?  

* P(a point in an inlier) = 1 - 𝒆  
* P(all samples are inliers) = (1 - 𝒆)^𝒔 


### ii. What is the probability for a draw to be a bad one? i.e. the draw contains at least one outlier.

* P(the draw contains at least one outlier) = 1 - P(no outliers)
* P(no outliers) = P(all samples are inliers) = (1 - 𝒆)^𝒔 
* P(the draw contains at least one outlier) = 1 - (1 - 𝒆)^𝒔 

### iii. What is the probability that we can get at least one good draw? 
Report your result in terms of 𝑒, 𝑠 and 𝑁. (Hint: this equals to 𝒑)  

* P(at least one good draw) = 1 - P(all bad draws in N draws)
* P(all bad draws in N draws) = P(bad draw) ^ N
* P(bad draw) = 1 - P(all samples are inliers)
* P(all samples are inliers) = (1 - 𝒆)^𝒔

* **Combine all**:   
    P(at least one good draw) = 1 - (1 - (1-𝒆)^𝒔)^N


### iv. If 𝑠 = 8, 𝑒 = 0.5, what is minimum number of draws we need to have 𝑝 = 0.99?

* 0.99 = 1 - (1 - (1 - 0.5) ^ 8) ^ N  
* N = 1177 (round up to integer)

Please answer the following parts below:
## (b) [By hand; computer use is OK but optional] 
In this regression problem, it is recommended that you center the y data for all 3 datasets, based on statistics of the training-set data; and for 𝑙_1 and 𝑙_2 regression using sklearn.linear_model.Ridge and
Lasso, set fit_intercept = ‘false’.  

* Why center y and set fit_intercept parameter as stated?
    * Center y:   
        By centering the data, we don't need to worry about the bias term. Correspondingly, setting the fit_intercept as False has the same effect.
    * fit_intercept:   
        Since our dataset is centered , we should set the fit_intercept to false. We use the augmented feature space in this problem, so the intercept is inherited in the weights (parameters) (w_0)

* Why use statistics of only training-set data to calculate parameters?  
    We are predicting from given training dataset, so we should only use training set data and information only. 

**Tip**: if you’re not sure why, you can proceed to solve the rest of this problem as described above, and then return to answer this “why” question later.

## (c) [Computer] Try linear regression
Try linear regression with no regularization, 𝑙_1 regularization and 𝑙_2
regularization. You can choose the best regularization coefficient 𝜆 within range
𝑙𝑜𝑔_2_𝜆 = −10 to 𝑙𝑜𝑔_2_𝜆 = 10, step size Δ𝑙𝑜𝑔_2_𝜆 = 1 based on validation set.   

Report the 𝜆 (if applicable) and val_MSE of your best model from each of the 3 regression methods.

* No regularization = 23538.38

* LASSO
    * The best log lambda for LASSO 0
    * The corresponding MSE:  23531.90

* Ridge 
    * The best log lambda for RIDGE 9
    * The corresponding MSE:  23522.57

## (d) [Computer] Try RANSAC. 

You can use sklearn.linear_model.RANSACRegressor.
Please try multiple parameters in RANSAC and select the best model with smallest
val_MSE. Report the val_MSE of your best model. Compare with results from best
models from (c) and discuss the possible reasons behind it.

I tried RANSAC with Ridge regressor, and search for a good parameter combination using the GridSearch. 

Final result: 
* max_trials = 6000
* min_samples = 4000
* **MSE** = 23526.39

The validation set MSE is as same as the Linear Regression Ridge result, without using RANSAC. The reason is:
1. I used the Linear Regression Ridge as the model for RANSAC
2. When training the dataset, we can only choose number of min_samples that smaller than the validation dataset, so that we can predict the validation set using the same model. In that way, the model lost moany information from one training. 

Thus the result is about the same as pure Linear Regression Ridge model. 


## (e) [By hand]   
### (i) 
For each of the 3 best models of (c), give an algebraic expression of its generalization-error bound e/B_L based on the model’s val_MSE.   
Evaluate each generalization-error bound numerically for δ = 0.1 .
Hint: You might find Lecture 11 and 13 notes useful.

e/B_L = (log(M/δ) / 2N) ^ (1/2)

For three validation set  
* M = 1 (Different in lasso and ridge)
* δ = 0.1
* N = number of data in validation set = 4172

1. No reg:   
    0.0109
2. Lasso: (M = 20) `log_lambda_2 = np.arange(-10, 25, 1)`  
    0.0166
3. Ridge: (M = 35) `log_lambda = np.arange(-10, 10, 1)`  
    0.0175

### (ii) 
For best model of (d), give an algebraic expression of its generalization-error
bound e/B_L based on the model’s val_MSE.    
Evaluate the generalization-error bound numerically for δ = 0.1 .

For three validation set  
* M = 100 (10 * 10)
* δ = 0.1
* N = number of data in validation set = 4172  

4. RANSAC: (M = 35) `log_lambda = np.arange(-10, 10, 1)`   
    0.0190

### (iii) 
1. Which of these 4 models looks best, based on val_MSE only?   
    Ridge regression. 

2. Which of the 4 models have the largest, and smallest, generalization-error bounds?
* Largest: RANSAC
* Smallest: Linear regression without regularization


## (f) [By hand and computer]  
We are now going to use the test set to compare the final 4 models. This is not the standard use of a test set, but we can do it if we take into account the generalization-error bounds.

### (i) 
Compute the test_MSE of each of the 4 models you evaluated in (e) above.
Give the generalization-error bound e/B_L for each of these test_MSE results, in terms of an algebraic expression, and its numeric value for for δ = 0.1.

* Final MLS No regularization: 25313.90
* Final MLS Lasso: 25317.08  
* Final MLS Ridge: 25300.78  
* Final MLS RANSAC: 25344.53  

e/B_L = e/B_L = (log(M/δ) / 2N) ^ (1/2)  
* M = 1
* δ = 0.1
* N = number of data in test set = 2780

**e/B_L = 0.0134** same for all 4 models  

### (ii) 
Pick the best model from (f)(i) based on their test_MSE results. Now what is the generalization-error bound e/B_L for this final chosen best model? Now what
is the generalization-error bound e/B_L for this final chosen best model?, evaluate numerically for δ = 0.1 , and briefly justify your generalization-error bound answer.

Based on the test MSE result, the Ridge regression perform the best.  
e/B_L = (log(M/δ) / 2N) ^ (1/2)  
* M = 4
* δ = 0.1
* N = number of data in test set = 2780

**e/B_L = 0.0169**  
Since we are choosing the best model from 4 models, the M is equal to 4.  

**Hint**: the generalization-error bound is different here than in (f)(i). The
reason is subtle. Try to figure out why.



# Question 2
CART, feature types, cost metrics  

