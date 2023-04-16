Model server
- A model server instantiate the model and expose the model’s methods. It exposes its API (REST or RPC interface) to the clients.
- The model server receives this data, formats it into the required shape, passes it to the model file and gets the inference back. 
- It can also manage multiple model versions in the case of A/B testing.

Using TensorFlow Serving (TFS) with Docker
-	Pull the TFS docker image (contains all of the necessary dependencies to run TFS) from the Docker hub
-	Clone the code repo of the model
-	Run a container out of the image, so TFS will spin up and host the model
-	Post data in JSON format via a REST request and get the prediction

Using TensorFlow Serving (TFS) without using Docker
-	Load a trained model into TFS by saving it in SavedModel format
-	Spin up TFS server, with the path to the model directory provided
-	Post data in JSON format via a REST request and get the prediction
