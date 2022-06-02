# Unified data preparation and model training with Amazon SageMaker Data Wrangler and Amazon SageMaker Autopilot

Data fuels machine learning (ML); the quality of data has a direct impact on the quality of ML models. Therefore, improving data quality and employing the right feature engineering techniques are critical to creating accurate ML models. ML practitioners often tediously iterate on feature engineering, choice of algorithms, and other aspects of ML in search of optimal models that generalize well on real-world data and deliver the desired results. Because speed in doing business disproportionately matters, this extremely tedious and iterative process may lead to runtime delays and lost business opportunities. 

Amazon SageMaker Data Wrangler reduces the time to aggregate and prepare data for ML from weeks to minutes, and Amazon SageMaker Autopilot automatically builds, trains, and tunes the best ML models based on your data. With Autopilot, you still maintain full control and visibility of your data and model. Both services are purpose-built to make ML practitioners more productive and accelerate time to value.

Data Wrangler now provides a unified experience enabling you to prepare data and seamlessly train a ML model in Autopilot. With this newly launched feature, you can now prepare your data in Data Wrangler and easily launch Autopilot experiments directly from the Data Wrangler user interface (UI). With just a few clicks, you can automatically build, train, and tune ML models, making it easier to employ state-of-the-art feature engineering techniques, train high-quality ML models, and gain insights from your data faster.

In this post, we discuss how you can use this new integrated experience in Data Wrangler to analyze datasets and easily build high-quality ML models in Autopilot.

## Dataset overview

Pima Indians are an Indigenous group that live in Mexico and Arizona, US. Studies show Pima Indians as a high-risk population group for diabetes mellitus. Predicting the probability of an individual's risk and susceptibility to a chronic illness like diabetes is an important task in improving the health and well-being of this often underrepresented minority group.

We use the Pima Indian Diabetes public dataset to predict the susceptibility of an individual to diabetes. We focus on the new integration between Data Wrangler and Autopilot to prepare data and automatically create an ML model without writing a single line of code.

The dataset contains information about Pima Indian females 21 years or older and includes several medical predictor (independent) variables and one target (dependent) variable, Outcome. 

The dataset contains 1,036 records, with 768 labeled as having diabetes (1) and 268 labeled as not (0). We store this dataset in Amazon Simple Storage Bucket (Amazon S3) as a CSV file and then import the CSV directly into a Data Wrangler flow from Amazon S3.

## Solution Overview
The following diagram summarizes what we accomplish in this post.

Data scientists, doctors, and other medical domain experts provide patient data with information on glucose levels, blood pressure, body mass index, and other features used to predict the likelihood of having diabetes. With the dataset in Amazon S3, we import the dataset into Data Wrangler to perform exploratory data analysis (EDA), data profiling, feature engineering, and splitting the dataset into train and test for model building and evaluation.

We then use Autopilot’s new feature integration to quickly build a model directly from the Data Wrangler interface. We choose Autopilot’s best model based on the model with the highest F-beta score. After Autopilot deploys the model, we run a batch transform on the test set to evaluate our model.

Medical experts can provide new data to the validated model to obtain a prediction to see if a patient will likely have diabetes. With these insights, medical experts can start treatment early to improve the health and well-being of vulnerable populations. Medical experts can also explain a model’s prediction by referencing the model’s detail in Autopilot because they have full visibility into the model’s explainability, performance, and artifacts. This visibility in addition to validation of the model from the test set gives medical experts greater confidence in the model’s predictive ability. 

We walk you through the following high-level steps.

1.	Import the dataset from Amazon S3.
2.	Perform EDA and data profiling with Data Wrangler.
3.	Perform feature engineering to handle outliers and missing values.
4.	Split data into train and test sets.
5.	Train and build a model with Autopilot.
6.	Test the model on a holdout sample with a SageMaker notebook.
7.	Analyze validation and test set performance.

## Prerequisites
Complete the following prerequisite steps:
1.	Upload the dataset to an S3 bucket of your choice.
2.	Make sure you have the necessary permissions. For more information, refer to Get Started with Data Wrangler.
3.	Set up a SageMaker domain configured to use Data Wrangler. For instructions, refer to Onboard to Amazon SageMaker Domain.

## Import your dataset with Data Wrangler 
You can integrate a Data Wrangler data flow into your ML workflows to simplify and streamline data preprocessing and feature engineering using little to no coding. Complete the following steps: 

1.	Create a new Data Wrangler flow.

If this is your first time opening Data Wrangler, you may have to wait a few minutes for it to be ready.

2.	Choose the dataset stored in Amazon S3 and import it into Data Wrangler.

