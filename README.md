# Unified data preparation and model training with Amazon SageMaker Data Wrangler and Amazon SageMaker Autopilot

Data fuels machine learning (ML); the quality of data has a direct impact on the quality of ML models. Therefore, improving data quality and employing the right feature engineering techniques are critical to creating accurate ML models. ML practitioners often tediously iterate on feature engineering, choice of algorithms, and other aspects of ML in search of optimal models that generalize well on real-world data and deliver the desired results. Because speed in doing business disproportionately matters, this extremely tedious and iterative process may lead to runtime delays and lost business opportunities. 

Amazon SageMaker Data Wrangler reduces the time to aggregate and prepare data for ML from weeks to minutes, and Amazon SageMaker Autopilot automatically builds, trains, and tunes the best ML models based on your data. With Autopilot, you still maintain full control and visibility of your data and model. Both services are purpose-built to make ML practitioners more productive and accelerate time to value.

Data Wrangler now provides a unified experience enabling you to prepare data and seamlessly train a ML model in Autopilot. With this newly launched feature, you can now prepare your data in Data Wrangler and easily launch Autopilot experiments directly from the Data Wrangler user interface (UI). With just a few clicks, you can automatically build, train, and tune ML models, making it easier to employ state-of-the-art feature engineering techniques, train high-quality ML models, and gain insights from your data faster.

In this post, we discuss how you can use this new integrated experience in Data Wrangler to analyze datasets and easily build high-quality ML models in Autopilot.

##Dataset overview

Pima Indians are an Indigenous group that live in Mexico and Arizona, US. Studies show Pima Indians as a high-risk population group for diabetes mellitus. Predicting the probability of an individual's risk and susceptibility to a chronic illness like diabetes is an important task in improving the health and well-being of this often underrepresented minority group.

We use the Pima Indian Diabetes public dataset to predict the susceptibility of an individual to diabetes. We focus on the new integration between Data Wrangler and Autopilot to prepare data and automatically create an ML model without writing a single line of code.

The dataset contains information about Pima Indian females 21 years or older and includes several medical predictor (independent) variables and one target (dependent) variable, Outcome. 

The dataset contains 1,036 records, with 768 labeled as having diabetes (1) and 268 labeled as not (0). We store this dataset in Amazon Simple Storage Bucket (Amazon S3) as a CSV file and then import the CSV directly into a Data Wrangler flow from Amazon S3.
