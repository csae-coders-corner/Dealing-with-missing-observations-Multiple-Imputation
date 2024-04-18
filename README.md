![CC Graphics 2024_Multipleimputation](https://github.com/csae-coders-corner/Dealing-with-missing-observations-Multiple-Imputation/assets/148211163/c0d172c5-ac70-4e6a-86ca-3a00d9258002)

# Dealing-with-missing-observations-Multiple-Imputation
Missing observations or attrition are a common issue in empirical research. This post briefly discusses the method multiple imputation to deal with missing observations.[^1] Multiple imputation replaces every missing value of the variable with a list of simulated values.  We can then run the required specification to estimate the parameter of interest using the imputed dataset. 

> [^1]:Multiple imputation was first discussed in Rubin (1987).

To illustrate the method, I use data from a randomized controlled trial (RCT) conducted by Blattman et al. (2011, 2014). The data can be downloaded from Harvard Dataverse.[^2]

> [^2]: I am using the dataset “yop2_yop4_deid.dta”. 

## Step 1: Setting up the dataset

Suppose we are interested in investigating the impact of randomly assigned cash transfers (given at baseline) on migration (measured at endline). We can run a logistic regression since the outcome variable “migrate_e” is a binary variable that refers to whether the respondent has changed parish at endline, since baseline. However, after checking the data, we notice that the variable “migrate_e” has 161 missing observations. 

Now, suppose we wish to impute those 161 observations for the “migrate_e” variable and then fit a logistic regression model with the complete dataset. To do so, we can use multiple imputation “mi” commands, which are readily available to use on Stata.[^3] To use the commands, the dataset in memory should be set as an “mi” dataset, as shown in the second command below.

> [^3]: The Stata version used here is Stata 17.0.

<img width="437" alt="missing 1" src="https://github.com/csae-coders-corner/Dealing-with-missing-observations-Multiple-Imputation/assets/148211163/1e7740ba-be17-4d33-9de1-fb01d8ac9d22">

<img width="402" alt="missing 2" src="https://github.com/csae-coders-corner/Dealing-with-missing-observations-Multiple-Imputation/assets/148211163/55258056-13d1-47c9-856a-2c9f04a2ad0b">

The misstable summarize command is particularly useful when you have multiple variables with missing values.

## Step 2: Imputing missing values 

I impute the values for “migrate_e” using a logit regression since it is a binary variable. Refer to table 1 below for which commands to use after “mi impute” depending on the type of variable. 

Table 1: Commands used for the different types of imputation variables. 

| Type of Variable 	|Commands for the relevant imputation method |
|-------------------|--------------------------------------------|
| Continuous 	      | regress, pmm, truncreg, intreg             |
| Binary 	          | logit                                      |
| Categorical 	    | ologit, mlogit                             |
| Count 	          | Poisson, nbreg                             |

<img width="395" alt="missing 3" src="https://github.com/csae-coders-corner/Dealing-with-missing-observations-Multiple-Imputation/assets/148211163/faa7581b-1241-427d-9ee4-cd7cc4531539">

I select the number of imputations to be equal to 20.[^4] For the results to be reproducible, select the random number seed. I selected 1234. Other control variables being used are “age age_2 age_3 urban risk_aversion education wealthindex voc_training.”[^5]

>[^5]: All control variables are baseline variables. “age” is the age of the respondent, “age_2” is is age to the power 2, “age_3” is age to the power 3, “urban” is a binary variable for whether one lives in an urban or rural area, “risk_aversion” is an index that indicated the risk aversion of an individual (all indices are calculated based on a set of survey questions), “education” demonstrated the highest level of education reached at school, “wealthindex” is an index that indicated the respondent’s wealth, “voc_training” is a binary variable indicating whether the respondent has had any vocational training.

> [^4]: This is selected arbitrarily here, but this number could be given more thought. Check, for example, T. Von Hippel (2020) for more details on how many imputations you need. 

Now, we are ready to estimate our logistic regression as shown below. Remember to look at the imputed values of the “migrate_e” and make sure they make sense. Notice that the number of observations is now 5,354. 

## Step 3: Estimating the model using imputed datasets

The mi estimate command estimates the desired model with each of the 20 imputed datasets (where 20 is the number of imputations decided by the researcher) and attains 20 respective coefficients with their corresponding standard errors. Stata combines these 20 estimates to attain one coefficient, standard error, and set of inferential statistics. 

<img width="365" alt="missing 4" src="https://github.com/csae-coders-corner/Dealing-with-missing-observations-Multiple-Imputation/assets/148211163/42a1cacf-2168-43c9-b51b-b3a682bba62e">

To sum up the steps that were implemented above:
1. Select the data you want to use, load it, and set it for use with “mi”.
2. Check which variables contain missing observations and thus those that need to be imputed.
3. Select the imputation method depending on the type of the variable to be imputed. For example, use standard OLS for a continuous variable.
4. Select the number of imputations. T. Von Hippel (2020) provide details on how many imputations should be chosen.
5. Select the random number seed for the results to be reproducible.
6. Impute the missing values of the variable(s) using mi impute.
7. Estimate the desired model using mi estimate. 


