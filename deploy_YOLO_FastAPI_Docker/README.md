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

- 
- 

## [Send a request to the YOLO server](client.ipynb)

-
-
