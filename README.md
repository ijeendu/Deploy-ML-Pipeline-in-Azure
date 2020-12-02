# Operationalising a Machine Learning Pipeline in Azure

This project demonstrates the entire process of creating a machine learning model using Azure AutoML, deploying the model and consuming it. It also showcases the use of Jupyter Notebook in Azure SDK for creating AutoML pipelines, deploying and consuming it. 
A bank marketing dataset was used to create a classification model to determine if a client would subscribe to a marketing campaign or not using AutoML in Azure Machine Learning Service. The machine learning studio was first used to configure an automl expriment that explores a variety of classification models based on the automl configuration parameters. At the end of the experiemnt, the best performing model (VotingEnsemble model with an accuracy of 0.92) was deployed to provide a RESTFUL endpoint (API) for interacting with the model. Post model deployment, Application Insights was enabled using Azure SDK to enable collection of logging information from the deployed model. The Swagger JSON file provided by Azure for the deployed model was downloaded from the Endpoints section in Azure under the Swagger URL and saved in a swagger.json file. The contents of the API for the model was displayed and the deployed model was consumed using Swagger. The deployed model was also benchmarked to obtain a baseline performance that can be used for evaluating the model behaviour in the future. Finally, a *Jupyter Notebook* was used to create an AutoML machine learning pipeline in Azure Python SDK using the same bank marketing dataset. The AutoML pipeline was deployed producing a pipeline endpoint that was used to consume the model. The pipeline run completed producing the same results obtained using Azure Machine Learning Studio.

## Suggested Improvement
The model performance may be improved by perfoming feature engineering on the bank marketing dataset. Also, the experiment time out period can be increased from 20 minutes to say 30 /45 minutes to allow more models to be explored.


## Architectural Diagram
An architectural diagram that helps visuaise the flow of operations from start to finish is shown below. It shows the various stages that are critical to the overall flow of the completed project. 

<img src="images/Arch_diagram.png" width="500">


## Key Steps
The architectural diagram captures 7 major steps that are briefly described in this section.

### Step 1: Authentication

During the authentication step, the Azure Machine Learning Extension that enables intraction with the Azure Machine Learning Studio, part of the **az** command is installed and enabled in the terminal. Afterwards, a Service Principal account is created and associated with the specific workspace. This step is required when using a personal Azure account. However, for this course, the lab envirionment provided by Udacity was used and it did not provide authorisation to create a service principal so this step was skipped. This did not affect the fulfilment of the other steps required in the project.


### Step 2: Automated ML experiment

In this step, an Automated ML experiment was created and a compute cluster configured to run the experiment. The banking dataset available [here](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) was registered in Azure ML studio and used for the AutoML model configuration and training. The experiment was set up by perfoming a sequence of activities as follows:


#### a. Upload and Register the Banking Dataset

The dataset was uploaded and registered in the azzure datastore.

<img src="images/registered_dataset.png.png" width="500">

#### b. Create a compute cluster

A compute cluster was created to be used for running the model training experiemnt.

<img src="images/compute_cluster.png.png" width="500">


#### c. Completed AutoML Experiment

The configured AutoML experiment is completed and best model obtained is the VotingEnsemble model.

<img src="images/completed_automl_experiment.png" width="500">



#### d. Best model after the experiment completes

An overview of the best AutoML model shows an accuracy metric of 0.92.

<img src="images/best_model_summary.png" width="500">



### Step 3: Deploy the Best Model

Here, the best model is deployed to enable interaction with the model via HTTP API service by sending data over POST requests.

#### a. Deploy Best Model 

The Best Model was selected and deployed using Azure Container Instance (ACI) with enabled authentication. 

<img src="images/deploy_best_model_with_enabled_authentication.png" width="500">

#### b. Best Model Deployment Succeeded

The Best Model was successfuly deployed.

<img src="images/best_model_deployed.png" width="500">



### Step 4: Enable Application Insights

Post deployment, Application Insights was enabled to retrieve log information from the deployed model. This step was completed by editing and running the *logs.py* script that enabled Application Insights using Azure Python SDK.

#### a. Application Insights is enabled 
The log.py script was modified to include a line that enables application insights:

`service.update(enable_app_insights=True)`.

The script set the application insights variable and enabled logging as shown:

<img src="images/app_insights_enabled.png" width="500">

The screenshot shows that Application Insights is now set to True and that the Best Model was successfully deployed as a RESTFUL API accessible via the endpoint section.


#### b. Sample logs provided after running logs.py

A sample of log information produced by the deployed model can be seen below:

<img src="images/logs_1.png" width="500">

<img src="images/logs_2.png" width="500">


### Step 5: Swagger Documentation

In this step, the deployed model was consumed using Swagger. The Swagger JSON file provided by Azure for the deployed model was downloaded from the Endpoints section in Azure under the Swagger URL and saved in a file named *swagger.json*. The *swagger.sh* script was modified and used to download the lastest Swagger container and to run it in on Port 9000. 
The *serve.py* script  starts a local server that makes it possible to access the *swagger.json* file over the HTTP protocol, expected by the Swagger User Interface (UI).  The script was used to start a Python server on port 8000. 
The *serve.py* script was placed right next to the downloaded *swagger.json* file and the contents of the API for the model was displayed using Swagger.


#### Swagger runs on local host 

Swagger is used to visualize the API definition contained in *swagger.json*. It shows the HTTP API methods and responses for the deployed model.

<img src="images/Swagger_UI.png" width="500">


### Step 6: Consume Model Endpoints

The *endpoint.py* script was used to interact with the deployed model via the RESTFUL API. The **scoring_url** and the **key information** was modified to match the key for the model service and the URL that was generated after deployment. The URL is found in the Details tab, above the Swagger URI.

#### a. Output of Consuming Model Endpoint

The endpoint.py was successfully run against the API to produce a JSON format output from the model. A data.json file was produced after the model was consumed.

<img src="images/consume_model.png" width="500">


#### b. Benchmark the Model Endpoint

The model endpoint was benchmarked using the Apache bench in order to load-test the model. The Apache benchmark command-line tool was installed. The *benchmark.sh* file containing the following code was run to produce a benchmark information for the deployed model:

`ab -n 10 -v 4 -p data.json -T 'application/json' -H 'Authorization: Bearer REPLACE_WITH_KEY' http://REPLACE_WITH_API_URL/score`

The screenshots below shows the Apache Benchmark(**ab**) running against the HTTP API using authentication keys to retrieve performance results.

<img src="images/benchmark1.png" width="500">

<img src="images/benchmark2.png" width="500">

<img src="images/benchmark3.png" width="500">

<img src="images/benchmark4.png" width="500">


### Step 7: Create, Publish and Consume a Pipeline

In this step, a Jupyter Notebook provided in the starter files was used to create, Publish and Consume a Pipeline using Azure Python SDK. The notebook was uploaded to the Azure ML studio and all the variables were updated to use the same **keys**, **URL**, **dataset**, **cluster**, and **model names** as created in the preceeding steps using AutoML.
The *config.json* file was downloaded and saved in the current working directory. The cells of the notebok were run through to create a pipeline that is visible in the Pipelines section of Azure ML studio. The pipeline was verified to be *Active* and *Running* in the Azure ML studio.

#### a. Pipeline section of Azure ML studio, showing created pipeline

<img src="images/pipeline_created.png" width="500">

#### b. pipeline section in Azure ML studio, showing Pipeline Endpoint

<img src="images/pipeline_endpoint.png" width="500">

#### c. The Bankmarketing dataset with the AutoML module

<img src="images/data_automl_module.png" width="500">

#### d. The "Published Pipeline overview", showing a REST endpoint and a status of ACTIVE

<img src="images/published_pipeline_overview_showing_REST_endpoint.png" width="500">


#### e. Jupyter Notebook showing run steps using the "Use RunDetails Widget"

<img src="images/notebook_complete_steps.png" width="500">

#### f. Azure ML studio showing the scheduled pipeline run

<img src="images/published_pipeline_running.png" width="500">



## Screen Recording

A screen recording of the project in action is available [here](https://youtu.be/9HV-VKDag7g)


## Standout Suggestions
*TODO (Optional):* This is where you can provide information about any standout suggestions that you have attempted.
