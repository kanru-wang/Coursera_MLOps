# Deploy and consume an Object Detection model using FastAPI on Docker

## [Set up](guide.md)

- Pull the image from Docker hub
- Run a container out of the image

  ```bash
  docker run -it --rm -p 8888:8888 -p 8000:8000 --mount type=bind,source="$(pwd)",target=/home/work deeplearningai/mlepc1w1-ugl:jupyternb
  ```

  Meaning of flags:

  - -it: Runs the container in an interactive mode and attaches a pseudo-terminal to it, so what is printed in the standard streams of the container is visible (so we can **copy and paste the access token for Jupyter lab**).
  - --rm: Deletes the container after stopping it.
  - -p: Allows mapping a port in the local computer (Jupyter server) to a port in the container.
  - --mount: Allows mounting a directory in the local filesystem within the container. If no mounts are present, changes to files will not persist after the container is deleted. In this case we are mounting the current directory `deploy_YOLO_FastAPI_Docker` onto the `/home/work` directory inside the container. `deeplearningai/mlepc1w1-ugl:jupyternb` is the image name.

- Access Jupyter Lab at `http://localhost:8888/lab`

## [Deploy YOLO using FastAPI](server.ipynb)

- `cvlib`'s `detect_common_objects()` takes an image formatted as a numpy array, and returns
  - List of list containing bounding box coordinates for detected objects. E.g. `[[32, 76, 128, 192], [130, 83, 220, 185]]`
  - List of labels for detected objects. E.g. `['apple', 'orange']`
  - List of confidence scores for detected objects. E.g. `[0.6187, 0.4283]`
- For FastAPI, we define each endpoint by decorating a function (the function specifies how to handle all logic for that endpoint); the decorator contains information on the HTTP method allowed, and the pattern in the URL that it will use. For example,
  
  ```python
  @app.get("/my-endpoint")
  def handle_endpoint():
      ...
      ...

  @app.post("/my-other-endpoint")
  def handle_other_endpoint(model, image_file):
      # Read an image as bytes (binary mode) -> convert bytes into numpy array -> decode numpy array as a CV2 image
      # Run the model and get bounding box coordinates, labels, and confidence scores
      # Create an image with the bounding boxes and labels drawn
      # Save the image
      # `open("images/clock.jpg", "rb")` as bytes (binary mode) -> return the image back to the client
      ...
  ```

## [Send a request to the YOLO server](client.ipynb)

- `open("images/clock.jpg", "rb")` as bytes (binary mode) -> POST the image to `http://localhost:8000/predict?model=yolov3-tiny`
- Read the response image as bytes (binary mode) -> convert bytes into numpy array -> decode numpy array as a CV2 image
- Save the image
- View the image
