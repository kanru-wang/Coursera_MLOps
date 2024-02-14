### View [this nbviewer mirrored repository](https://nbviewer.org/github/kanru-wang/Coursera_MLOps), if you see formatting issues in the notebooks above.

--------------------------------

<br>

## MLOps

https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning

- CI is no longer only about testing and validating code and components, but also testing and validating data, data schemas, and models.
- CD is no longer about a single software package or a service, but a system (an ML training pipeline) that should automatically deploy another service (model prediction service).
- CT: automatically retraining and serving the models.

https://neptune.ai/blog/mlops

- The model is automatically trained (Continuous Training) in production
  - Based on triggers (e.g. on-demand, on a schedule, upon new data availability, upon model degradation, or upon significant changes in the data distribution)
  - Have automated data validation and model validation steps, so that models can be automatically trained
- Same pipeline used in Development and Production
- Containerizing components (e.g. package data pre-processing or model training code as a Docker image)
  - Decouple the execution environment from the custom code runtime
  - Make code reproducible between development and production environments
  - Isolate each component in the pipeline. Components can have different runtime environment, different languages and libraries, and cannot directly share in-memory data.
- The model deployment is automated (Continuous Delivery)
- Deploy a whole training pipeline, not just a trained model
- Use feature store for standardizing the definition, storage, and access of features for training and serving
- Information about each execution is recorded for data and artifacts lineage, reproducibility, comparison and debugging
- Tools:
  - Amazon Sagemaker to build, train, deploy, and monitor machine learning models
  - Google Cloud MLOps suite:
    - Dataflow to extract, validate, transform data, and evaluate models
    - Cloud Build to build and test machine learning pipelines
    - TFX to deploy ML pipelines (for TensorFlow only)
    - Kubeflow to deploy ML pipelines (for TensorFlow, PyTorch, XGBoost)

- Unit testing input data
  - If features are scaled or normalized correctly
  - If one-hot values and embedding are generated and used correctly
  - Correct data types, not empty, and in the right range

## Model Server

- A model server instantiate the model and expose the model’s methods. It exposes its API (REST or RPC interface) to the clients.
- The model server receives this data, formats it into the required shape, passes it to the model file and gets the inference back. 
- It can also manage multiple model versions in the case of A/B testing.
- TensorFlow Serving (TFS) is good when working with Tensorflow; otherwise can use FastAPI.

[Using TensorFlow Serving (TFS) with Docker](https://github.com/https-deeplearning-ai/machine-learning-engineering-for-production-public/blob/main/course4/week1-ungraded-labs/C4_W1_Lab_2_TFS_Docker.md)
-	Pull the TFS docker image (contains all of the necessary dependencies to run TFS) from the Docker hub
-	Clone the code repo of the model
-	Run a container out of the image, so TFS will spin up and host the model
-	Post data in JSON format via a REST request and get the prediction

[Using TensorFlow Serving (TFS) without using Docker](https://github.com/https-deeplearning-ai/machine-learning-engineering-for-production-public/blob/main/course4/week1-ungraded-labs/C4_W1_Lab_3_TFS.ipynb)
-	Load a trained model into TFS by saving it in SavedModel format
-	Spin up TFS server, with the path to the model directory provided
-	Post data in JSON format via a REST request and get the prediction

## Degree of Automation

- Human only
- Shadow mode (model shadows human; model outputs not used for any decisions)
- AI assistance (model giving hints to human)
- Partial automation (model giving uncertain samples to human)
- Full automation (e.g. Canary Deployment (starting small and ramp up), Blue Green Deployment (two models running; easy to switch to the old model))

## Canary Releases on Kubernetes
From https://www.cloudskillsboost.google/focuses/18471?parent=catalog

- A service mesh is a dedicated infrastructure layer that controls service-to-service communication over a network.
- Use Workload Identity to authorize pods in Google Cloud Kubernetes Engine clusters to access other Google Cloud Services.
- Deploy TF Serving in three steps:
1. Create a Kubernetes ConfigMap that points to the location of the model in a storage bucket
2. Create a Kubernetes Deployment using a standard TensorFlow Serving image from Docker Hub
3. Provide an interface to the model deployment. Use an Istio Ingress gateway to control traffic entering and leaving the mesh. Istio virtual service forwards/splits traffic from the Istio Ingress gateway to Kubernetes service. Kubernetes service does load balancing between pods.

## Focus more on the data than the model

`human_machine_performance_gap` x `category_size` =  potential room for improvement of that category

Prioritize which classificaton category to work on (e.g. collect more data of this category) based on:
- How much room for improvement there is
- How frequently that category appears
- How easy it is to improve accuracy in that category
- How important it is to improve in that category

The goal of data augmentation is to create realistic examples that (1) the algorithm does poorly on, but (2) are realistic, and (3) humans can do well on.

For unstructured data ML tasks, adding more training data will not hurt the performance, unless (1) the model is small (low number of weights), or (2) the new data is ambiguous (even human cannot make accurate predictions).
