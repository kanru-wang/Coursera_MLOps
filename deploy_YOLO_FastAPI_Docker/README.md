# Deploy and consume an Object Detection model using FastAPI on Docker

## [Set up](guide.md)

- Pull the image from Docker hub
- Run a container out of the image

```bash
docker run -it --rm -p 8888:8888 -p 8000:8000 --mount type=bind,source="$(pwd)",target=/home/jovyan/work deeplearningai/mlepc1w1-ugl:jupyternb
```
 
Let's break down this command and its flags:
 
- -it: Runs the container in an interactive mode and attaches a pseudo-terminal to it so you can check what is being printed in the standard streams of the container. This is very important since you will have to **copy and paste the access token for Jupyter lab**.

- --rm: Deletes the container after stopping it.
- -p: Allows you to map a port in your computer to a port in the container. In this case we need a port for the Jupyter server and another for the server you will run within the ungraded lab.
- --mount: Allows you to mount a directory in your local filesystem within the container. This is very important because if no mounts are present, changes to files will not persist after the container is deleted. In this case we are mounting the current directory `week1-ungraded-lab` onto the `/home/jovyan/work` directory inside the container.

- Access Jupyter Lab at `http://localhost:8888/lab`

## [Deploy YOLO using FastAPI](server.ipynb)

- 
- 

## [Send a request to the YOLO server](client.ipynb)

-
-