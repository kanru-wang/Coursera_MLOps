## Create a Kubernetes deployment using a TensorFlow Serving image and define autoscaling

https://github.com/https-deeplearning-ai/machine-learning-engineering-for-production-public/tree/main/course4/week2-ungraded-labs/C4_W2_Lab_2_Intro_to_Kubernetes

Create a config map that defines a MODEL_NAME and a MODEL_PATH variable

Run `kubectl apply -f configmap.yaml`

- Starts up the model server and uses the environment variables MODEL_BASE_PATH and MODEL_NAME to find the model
- Pods can consume ConfigMaps as environment variables, command-line arguments. A ConfigMap allows decoupling environment-specific configuration from container images, so that applications are easily portable

<br>

Create the deployment for the application

Run `kubectl apply -f deployment.yaml`

- Starts up one replica, uses tensorflow/serving as the container image and defines environment variables via the envFrom tag. Regarding “Replica”, each Pod is meant to run a single instance of a given application, so to scale an application horizontally, create Replicated Pods, one for each instance
- Exposes a port (e.g. 8501) of the container for receiving HTTP requests later on
- Defines CPU and memory limits and mounts the volume from the Minikube VM to the container
 
<br>

Expose the deployment through a service

Run `kubectl apply -f service.yaml`

- Defines a NodePort service which exposes the node's port (e.g. 30001). Requests sent to this port will be sent to the containers' specified targetPort (e.g. 8501)
 
<br>

Enable a Metrics Server to measure resource utilization such as CPU and memory

Run `minikube addons enable metrics-server`

<br>

Create Horizontal Pod Autoscaler to create or remove replicasets based on observed metrics

Run `kubectl apply -f autoscale.yaml`

- Autoscaler can query the metrics server
- If the CPU utilization is more than e.g. 20% (as set in the HPA (Horizontal Pod Autoscaler)), HPA will trigger spinning up the additional replicas, up to a maximum of e.g. 3 pods, accepting request and sharing the load. 

---------

Similar processes also exist in another guide:

https://www.cloudskillsboost.google/focuses/17649?parent=catalog

- Deploy TF Serving in four steps
  - Create a Kubernetes ConfigMap that points to the location of the model in the storage bucket. Define a MODEL_NAME and a MODEL_PATH variable.
  - Create a Kubernetes Deployment using a standard TensorFlow Serving image from Docker Hub.
  - When the deployment is ready, create a Kubernetes Service to expose the deployment through a load balancer.
    - Notice that the service is of type LoadBalancer and that it exposes two ports: 8500 and 8501. By default, Tensorflow Serving uses port 8500 for the gRPC interface and port 8501 for the REST interface.
  - Configure Horizontal Pod Autoscaler.
    - Configure HPA to start a new replica of TensorFlow Serving whenever the mean CPU utilization across all already running replicas reaches 60%. HPA will attempt to create up to 4 replicas and scale down to 1 replica.
- Use an open source load testing tool Locust to generate prediction requests
