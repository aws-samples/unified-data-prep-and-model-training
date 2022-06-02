# Unified data preparation and model training with Amazon SageMaker Data Wrangler and Amazon SageMaker Autopilot

Data fuels machine learning (ML); the quality of data has a direct impact on the quality of ML models. Therefore, improving data quality and employing the right feature engineering techniques are critical to creating accurate ML models. ML practitioners often tediously iterate on feature engineering, choice of algorithms, and other aspects of ML in search of optimal models that generalize well on real-world data and deliver the desired results. Because speed in doing business disproportionately matters, this extremely tedious and iterative process may lead to runtime delays and lost business opportunities. 

[Amazon SageMaker Data Wrangler](https://aws.amazon.com/sagemaker/data-wrangler/) reduces the time to aggregate and prepare data for ML from weeks to minutes, and [Amazon SageMaker Autopilot](https://aws.amazon.com/sagemaker/autopilot/) automatically builds, trains, and tunes the best ML models based on your data. With Autopilot, you still maintain full control and visibility of your data and model. Both services are purpose-built to make ML practitioners more productive and accelerate time to value.

Data Wrangler now provides a unified experience enabling you to prepare data and seamlessly train a ML model in Autopilot. With this newly launched feature, you can now prepare your data in Data Wrangler and easily launch Autopilot experiments directly from the Data Wrangler user interface (UI). With just a few clicks, you can automatically build, train, and tune ML models, making it easier to employ state-of-the-art feature engineering techniques, train high-quality ML models, and gain insights from your data faster.

In this post, we discuss how you can use this new integrated experience in Data Wrangler to analyze datasets and easily build high-quality ML models in Autopilot.

## Dataset overview

Pima Indians are an Indigenous group that live in Mexico and Arizona, US. [Studies](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4418458/) show Pima Indians as a high-risk population group for diabetes mellitus. Predicting the probability of an individual's risk and susceptibility to a chronic illness like diabetes is an important task in improving the health and well-being of this often underrepresented minority group.

We use the [Pima Indian Diabetes public dataset](https://www.kaggle.com/datasets/uciml/pima-indians-diabetes-database?resource=download) to predict the susceptibility of an individual to diabetes. We focus on the new integration between Data Wrangler and Autopilot to prepare data and automatically create an ML model without writing a single line of code.

The dataset contains information about Pima Indian females 21 years or older and includes several medical predictor (independent) variables and one target (dependent) variable, Outcome. 

The dataset contains 768 records, with 9 total features. We store this dataset in [Amazon Simple Storage Bucket](https://aws.amazon.com/s3/) (Amazon S3) as a CSV file and then import the CSV directly into a Data Wrangler flow from Amazon S3.

## Solution Overview
The following diagram summarizes what we accomplish in this post.
![image](https://user-images.githubusercontent.com/42812331/171529205-013f7219-7110-45c7-9346-4d529227b677.png)

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
1.	[Upload the dataset](https://docs.aws.amazon.com/quickstarts/latest/s3backup/step-2-upload-file.html) to an S3 bucket of your choice.
2.	Make sure you have the necessary permissions. For more information, refer to [Get Started with Data Wrangler](https://docs.aws.amazon.com/sagemaker/latest/dg/data-wrangler-getting-started.html).
3.	Set up a SageMaker domain configured to use Data Wrangler. For instructions, refer to [Onboard to Amazon SageMaker Domain](https://docs.aws.amazon.com/sagemaker/latest/dg/gs-studio-onboard.html).

## Import your dataset with Data Wrangler 
You can integrate a Data Wrangler data flow into your ML workflows to simplify and streamline data preprocessing and feature engineering using little to no coding. Complete the following steps: 

1.	[Create a new Data Wrangler flow](https://docs.aws.amazon.com/sagemaker/latest/dg/data-wrangler-data-flow.html).

If this is your first time opening Data Wrangler, you may have to wait a few minutes for it to be ready.

2.	Choose the dataset stored in Amazon S3 and import it into Data Wrangler.
![image](https://user-images.githubusercontent.com/42812331/171529377-15fd556f-af96-48e7-9bbd-389c450db740.png)
After you import the dataset, you should see the beginnings of a data flow within the Data Wrangler UI. You now have a flow diagram.

3.	Choose the plus sign next to **Data types** and choose **Edit** to confirm that Data Wrangler automatically inferred the correct data types for your data columns.
![image](https://user-images.githubusercontent.com/42812331/171529440-2dd31a06-56e5-4356-bf70-f3a7c60ebb33.png)

If the data types aren’t correct, you can easily modify them through the UI. If multiple data sources are present, you can join or concatenate them.

We can now create an analysis and add transformations.

## Perform exploratory data analysis with the data insights report 
Exploratory data analysis is a critical part of the ML workflow. We can use the new data insights report from Data Wrangler to gain a better understanding of the profile and distribution of our data. The report includes summary statistics, data quality warnings, target column insights, a quick model, and information about anomalous and duplicate rows. 

1.	Choose the plus sign next to **Data types** and choose **Get data insights**.

![image](https://user-images.githubusercontent.com/42812331/171529508-21bece76-7fe8-43d7-b618-89ad47f361e8.png)

2.	For **Target column**, choose **Outcome**.
3.	For **Problem type**, select **Classification**.
4.	Choose **Create**. 

![image](https://user-images.githubusercontent.com/42812331/171529539-4407baf0-67dc-492e-81dc-7d0313c3f072.png)

The results show a summary data with the dataset statistics.

![image](https://user-images.githubusercontent.com/42812331/171529553-2060e3aa-6dfc-4a31-b9b2-6ae369a9ca76.png)

We can also view the distribution of the labeled rows with a histogram, an estimate of the expected predicted quality of the model with the quick model feature, and a feature summary table. 

![image](https://user-images.githubusercontent.com/42812331/171529561-2cfff6f2-544a-45df-b5ce-dc121839dc68.png)
![image](https://user-images.githubusercontent.com/42812331/171529568-8528275f-47f0-4923-80a4-00734ff6c484.png)

We don’t go into the details of analyzing the data insights report; refer to [Accelerate data preparation with data quality and insights in Amazon SageMaker Data Wrangler](https://aws.amazon.com/blogs/machine-learning/accelerate-data-preparation-with-data-quality-and-insights-in-amazon-sagemaker-data-wrangler/) for additional details about how you can use the data insights report to accelerate your data preparation steps.

## Perform feature engineering

Now that we’ve profiled and analyzed the distribution of our input columns at a high level, the first consideration for improving the quality of our data could be to handle missing values.

For example, we know that zeros (0) for the Insulin column represent missing values. We could follow the recommendation to replace the zeros with NaN. But on closer examination, we find that the minimum value is 0 for others columns such as Glucose, BloodPressure, SkinThickness, and BMI. We need a way to handle missing values, but need to be sensitive to columns with zeros as valid data. Let’s see how we can fix this.

In the** Feature Details **section, the report raises a **Disguised missing value** warning for the feature Insulin.
![image](https://user-images.githubusercontent.com/42812331/171529701-79be4451-54e0-4a0d-b353-08e983eb1fa3.png)
![image](https://user-images.githubusercontent.com/42812331/171529704-6301e1f1-3530-4022-baa6-cbf59608676e.png)

Because zeros in the Insulin column are in fact missing data, we use the **Convert regex to missing** transform to transform zero values to empty (missing values). 

1.	Choose the plus sign next to **Data types** and choose **Add transform**.
2.	Choose** Search and edit**.
3.	For **Transform**, choose **Convert regex to missing**.
4.	For **Input columns**, choose the columns Insulin, Glucose, BloodPressure, SkinThickness, and BMI.
5.	For **Pattern**, enter 0.
6.	Choose **Preview** and **Add** to save this step. 

The 0 entries under Insulin, Glucose, BloodPressure, SkinThickness, and BMI are now missing values.

![image](https://user-images.githubusercontent.com/42812331/171529779-8478f746-236b-4d52-b845-cb9c7e2808d7.png)

Data Wrangler gives you a few other options to fix missing values. 

7.	We handle missing values by imputing the approximate median for the Glucose column. 

![image](https://user-images.githubusercontent.com/42812331/171529800-1d669c98-4c76-4d4f-aaba-461e7bd21f36.png)

We also want to ensure that our features are on the same scale. We don’t want to accidentally give more weight to a certain feature just because they contain a larger numeric range. We normalize our features to do this. 

8.	Add a new **Process numeric** transform and choose **Scale values**.
9.	For **Scaler**, choose **Min-max scaler**.
10.	For **Input columns**, choose the columns Pregnancies, BloodPressure, Glucose, SkinThickness, Insulin, BMI, and Age.
11.	Set Min to 0 and Max to 1.

![image](https://user-images.githubusercontent.com/42812331/171529841-f9d252eb-3884-44f7-85b3-b935e4cccca7.png)

Now that’s we’ve created some features, we split our dataset into training and testing before we build a model.

## Split data into training and testing

In the model building phase of your ML workflow, you test the efficacy of your model by running batch predictions. You can set aside a testing or holdout dataset for evaluation to see how your model performs by comparing the predictions to the ground truth. Generally, if more of the model’s predictions match the true labels, we can determine the model is performing well. 

We use Data Wrangler to split our dataset for testing. We retain 90% of our dataset for training because we have a relatively small dataset. The remaining 10% of our dataset serves as the test dataset. We use this dataset to validate the Autopilot model later in this post.

We split our data by choosing the **Split data** transform and choosing **Randomized split** as the method. We designate 0.9 as the split percentage for training and 0.1 for testing.

![image](https://user-images.githubusercontent.com/42812331/171529883-4c7d1b05-3748-4f50-873a-3992051bdfd0.png)

With the data transformation and featuring engineering steps complete, we’re now ready to train a model.

## Train and validate the model 
We can use the new Data Wrangler integration with Autopilot to directly train a model from the Data Wrangler data flow UI.

1.	Choose the plus sign next to **Dataset** and choose **Train model**.

![image](https://user-images.githubusercontent.com/42812331/171529927-42778242-d9c4-4692-a8a8-40e934e49d5c.png)

2.	For **Amazon S3 location**, specify the Amazon S3 location where SageMaker exports your data. 

Autopilot uses this location to automatically train a model, saving you time from having to define the output location of the Data Wrangler flow, then having to define the input location of the Autopilot training data. This makes for a more seamless experience.

3.	Choose **Export and train** to initiate model building with Autopilot.

![image](https://user-images.githubusercontent.com/42812331/171529985-bb65b9e2-7ee2-4b05-ae70-1fda8761f574.png)

## Test the model on a holdout sample
When Autopilot completes the experiment, we can view the training results and explore the best model’s parameters. 

![image](https://user-images.githubusercontent.com/42812331/171530018-59ee30f9-a698-4989-b5c4-bc7c2bd9535f.png)

1.	Choose **View model details** for your desired model, then choose the **Performance** tab on the model details page.

![image](https://user-images.githubusercontent.com/42812331/171530050-927875a0-a427-4485-bdf7-64896200b1fb.png)

The **Performance** tab displays several model measurement tests, including a confusion matrix, the area under the precision/recall curve (AUCPR), and the area under the receiver operating characteristic curve (ROC). These illustrate the overall validation performance of the model, but they don’t tell us if the model will generalize well. We still need to run evaluations on unseen test data to see how accurately the model predicts if an individual will have diabetes.

To ensure the model generalizes well enough, we set aside the test sample for independent sampling. We can do so in the Data Wrangler flow UI.

1.	Choose the plus sign next to **Dataset**, choose **Export to**, and choose **Amazon S3**.

![image](https://user-images.githubusercontent.com/42812331/171530083-6dd61c80-6bc9-4ba7-98b6-5ce2c55b9a00.png)

3.	Specify an Amazon S3 path.

We refer to this path when we run batch inference for validation in the next section.


4.	Create a new SageMaker notebook to perform batch inferencing on the holdout sample and assess the test performance. Refer to the notebook in this repo for sample code to run batch inference for validation.

## Analyze validation and test set performance
When the batch transform is complete, we create a confusion matrix to compare the actual and predicted outcomes of the holdout dataset.

We see 23 true positives and 33 true negatives from our results. In our case, true positives refer to the model correctly predicting an individual as having diabetes. In contrast, true negatives refer to the model correctly predicting an individual as not having diabetes.

![image](https://user-images.githubusercontent.com/42812331/171530218-3f49cda9-8016-48ca-b38f-649a9fbcc004.png)

In our case, precision and recall are important metrics. Precision essentially measures all individuals predicted to have diabetes, how many really have diabetes? In contrast, recall helps measure all individual who indeed have diabetes, how many were predicted to have diabetes? For example, you may want to use a model with high precision because you want to treat as many individuals as you can, especially if the first stage of treatment has no effect on individuals without diabetes (these are false positives—those labeled as having it when in fact they do not).

We also plot the area under the ROC curve (AUC) graph to evaluate the results. The higher the AUC, the better the model is at distinguishing between classes, which in our case is how well the model performs at distinguishing patients with and without diabetes.

![image](https://user-images.githubusercontent.com/42812331/171530230-e28f0153-a800-4a70-8b7c-cd7ed1f5ff36.png)

## Conclusion
In this post, we demonstrated how to integrate your data processing, featuring engineering, and model building using Data Wrangler and Autopilot. We highlighted how you can easily train and tune a model with Autopilot directly from the Data Wrangler user interface. With this integration feature, we can quickly build a model after completing feature engineering, without writing any code. Then we referenced Autopilot’s best model to run batch predictions using the AutoML class with the SageMaker Python SDK.

Low-code and AutoML solutions like Data Wrangler and Autopilot remove the need to have deep coding knowledge to build robust ML models. [Get started using Data Wrangler](https://docs.aws.amazon.com/sagemaker/latest/dg/data-wrangler-getting-started.html) today to experience how easy it is to build ML models using Autopilot.
