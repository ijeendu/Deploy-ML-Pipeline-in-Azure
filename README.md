*NOTE:* This file is a template that you can use to create the README for your project. The *TODO* comments below will highlight the information you should be sure to include.


# Operationalising a Machine Learning Pipeline in Azure

*TODO:* Write an overview to your project.

In this project, the AutoMl feature of the Azure Machine learning platform was used to crete a classification model for the bank marketing dataset. The best performing model was then deployed to provide a RESTFUL endpoint that was used to interact with the model. The best perfoming model was the Voting Ensemble model with an accuracy of 0.916. 

## Architectural Diagram
*TODO*: Provide an architectual diagram of the project and give an introduction of each step. An architectural diagram is an image that helps visualize the flow of operations from start to finish. In this case, it has to be related to the completed project, with its various stages that are critical to the overall flow. For example, one stage for managing models could be "using Automated ML to determine the best model". 

## Key Steps
*TODO*: Write a short discription of the key steps. Remeber to include all the screenshots required to demonstrate key steps. 

### Step 1: Authentication

During the authentication step, the Azure Machine Learning Extension that enables intraction with the Azure Machine Learning Studio, part of the *az* command is installed and enabled in the terminal. Afterwards, a Service Principal account is created and associated with the specific workspace. This step is required when using a personal Azure account. However, for this course, the lab envirionment provided by Udacity is used and it does not provide authorisation to create a service principal so this step was skipped. This did not affect the fulfilemnt of the other steps required in the project.


### Step 2: Automated ML experiment

In this step, an Automated ML experiment was created and a compute cluster configured to run the experiment. The banking dataset available at https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv was used for the AutoML model training. The experiment was set up by perfoming the following sequence of activities as shown by screnshots:


#### a. Upload and Register the Banking Dataset

![](images/registered_dataset.png)
 
<img src='images/registered_dataset.png" width="100">

#### b. Create a compute cluster

<img src='images/compute_cluster.png" width="100">


#### c. Completed AutoML Experiment


<img src='images/completed_automl_experiment.png" width="100">

From the completed AutoMl experiment, the resulting best model is the VotingEnsemble model. 

#### d. Best model after the experiment completes

Overview of the best AutoMl model

<img src='images/best_model_summary.png" width="100">



### Step 3: Deploy the Best Model
Deploying the best model enables interaction with the model via HTTP API service by sending data over POST requests.

#### a. Deploy Best Model 

The Best Model was selected and deployed using Azure Container Instance (ACI) with enabled authentication enabled.
 
<img src='images/deploy_best_model_with_enabled_authentication.png" width="100">

#### b. Best Model Deployment Succeeded

The Best Model was successfuly deployed.

<img src='images/best_model_deployed.png" width="100">



### Step 4: Enable Application Insights

After the Best Model was deployed, Application Insights was enabled to retrieve logs. This step was completed by editing and running the logs.py script that enabled Application Insights using the Azure Python SDK.

#### a. Application Insights is enabled 
The log.py script was modified to include a line that enables application insights:

service.update(enable_app_insights=True).

The script was then run to set application insights and enable logging as shown:

<img src='images/app_insights_enabled.png" width="100">

The screen shot also shows that the Best Model has been deployed as a RESTFUL API accessible via the endpoint section.


#### b. Sample logs provided after running logs.py

<img src='images/logs_1.png" width="100">


<img src='images/logs_2.png" width="100">


### Step 5: Swagger Documentation

In this step, the deployed model was consumed using Swagger. The Swagger JSON file provided by Azure for the deployed model was downloaded from the Endpoints section in Azure under the Swagger URL and saved in a swagger.json file. 
The swagger.sh script was used to download the lastest Swagger container and to run it in on Port 9000. The serve.py script was used to start a Python server on port 8000. The serve.py script was placed right next to the downloaded swagger.json file. The contents of the API for the model was then displayed using Swagger.

#### Swagger runs on local host

Swagger runs on local host showing HTTP API methods and responses for the model 

<img src='images/Swagger_UI.png" width="100">


### Step 6: Consume Model Endpoints

Following model deployment, the Endpoint.py script was used to interact with the trained model. The scoring_uri and the key information was modified to match the key for the model service and the URL that was generated after deployment. the URL is found in the Details tab, above the Swagger URI.

#### Output of Consuming Model Endpoint

The endpoint.py was successfully run against the API to produce a JSON format output from the model. A data.json file wasproduced after consuming the model.

<img src='images/consume_model.png" width="100">


#### Benchmark the Model Endpoint

The model endpoint is benchmarked using the Apache bench in  order to load-test the model. The Apache benchmark command-line tool was installed. The benchmark.sh file containing the following code was run to produce a benchmark information for the deployed model:

ab -n 10 -v 4 -p data.json -T 'application/json' -H 'Authorization: Bearer REPLACE_WITH_KEY' http://REPLACE_WITH_API_URL/score

The screenshots below shows the Apache Benchmark(ab) running agains the HTTP API using authentication keys to retrieve performace results.

<img src='images/benchmark1.png" width="100">

<img src='images/benchmark2.png" width="100">

<img src='images/benchmark3.png" width="100">

<img src='images/benchmark4.png" width="100">


### Step 7: Create, Publish and Consume a Pipeline

In this step, a Jupyter Notebook provided in the starter files was used to create, Publish and Consume a Pipeline in Azure SDK. The notebook was uploaded to the Azure ML studio and all the variables were updated to have the same keys, URL, dataset, cluster, and model names as already created in the preceeding steps.
The config.json file was downloaded and saved in the current working directory. The cells of the notebok were run through to create a pipeline that is visible in the Pipelines section of Azure ML studio. The pipeline was verified to be running in the Azure ML studio.

#### a. Pipeline section of Azure ML studio, showing created pipeline

<img src='images/pipeline_created.png" width="100">

#### b. pipeline section in Azure ML studio, showing Pipeline Endpoint

<img src='images/pipeline_endpoint.png" width="100">

#### c. The Bankmarketing dataset with the AutoML module

<img src='images/data_automl_module.png" width="100">

#### d. The "Published Pipeline overview", showing a REST endpoint and a status of ACTIVE

<img src='images/published_pipeline_overview_showing_REST_endpoint.png" width="100">


#### e. Jupyter Notebook showing steps runs using the "Use RunDetails Widget"

<img src='images/notebook_complete_steps.png" width="100">

#### f. Azure ML studio showing the scheduled pipeline run

<img src='images/published_pipeline_running.png" width="100">




## Screen Recording
*TODO* Provide a link to a screen recording of the project in action. Remember that the screencast should demonstrate:



## Standout Suggestions
*TODO (Optional):* This is where you can provide information about any standout suggestions that you have attempted.
