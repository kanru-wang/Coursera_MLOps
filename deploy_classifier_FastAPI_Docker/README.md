## Directory Structure for FastAPI in Docker

Create a directory called `app` and place `main.py` (the server) and its dependencies (`wine.pkl`) there as explained in the FastAPI docs (https://fastapi.tiangolo.com/deployment/docker/) on how to deploy with Docker. The directory structure should look like this:

```
..
└── deploy_classifier_FastAPI_Docker
    ├── app/
    │   ├── main.py (server code)
    │   └── wine.pkl (serialized classifier)
    ├── requirements.txt (Python dependencies)
    ├── wine-examples/ (wine examples to test the server)
    ├── README.md (this file)
    └── Dockerfile
```

## Create the Dockerfile

```Dockerfile
FROM frolvlad/alpine-miniconda3:python3.7

COPY requirements.txt .

RUN pip install -r requirements.txt && \
	rm requirements.txt

EXPOSE 80

COPY ./app /app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"]
```

- The base image is `frolvlad/alpine-miniconda3:python3.7`:
    - `frolvlad` is the username of the author of the image.
    - `alpine-miniconda3` is its name.
    - `python3.7` is the image's tag.
- Copy the local `requirements.txt` file into the image so it can be accessed by other processes
- Install Python libraries
    - Remove `requirements.txt` because there is no more use for that file. The image includes only the necessary files for the server to run.
    - It is a good practice to chain together commands using the `&&` operator since Docker creates a new layer every time it encounters a `RUN`, `COPY` or `ADD` instruction, which results in a bigger image size.
- In this case, the server listens to requests on port 80.
- Use the `COPY` instruction to copy the `app` directory within the root of the container

## Server code

The "con" prefix stands for `constrained`, so this is a constrained list. This type allows selecting the type of the items within the list and also the maximum and minimum number of items. In this case the model was trained using 13 features so each data point should be of size 13:

```python
# Represents a batch of wines
class Wine(BaseModel):
    batches: List[conlist(item_type=float, min_items=13, max_items=13)]
```

Notice that there is not explicit naming for each feature so the **order of the data matters**.

Since scikit-learn accepts batches of data represented by numpy arrays, simply get the batches from the `wine` object, which are lists, and convert it to numpy arrays before feeding it to the classifier. At the end, need to convert the predictions to a list to make them REST-compatible:

```python
@app.post("/predict")
def predict(wine: Wine):
    batches = wine.batches
    np_batches = np.array(batches)
    pred = clf.predict(np_batches).tolist()
    return {"Prediction": pred}
```

## Building the image

While on the `deploy_classifier_FastAPI_Docker` directory run the docker build command:

```bash
docker build -t mlepc4w2-ugl:deploy_classifier_FastAPI_Docker . 
```

Use the `-t` flag to specify the name of the image and its tag. In this case the name is `mlepc4w2-ugl` and the tag is `deploy_classifier_FastAPI_Docker`.

## Cleaning things up

To check out the image created:

```bash
docker images
```

Any image with name and tag of `none` is an intermediate image and it is a good practice to prune them. 

Prune them by running the following command:

```bash
docker rmi $(docker images --filter "dangling=true" -q --no-trunc)
```

## Running the server

Run a container out of the image using the following command:

```bash
docker run --rm -p 80:80 mlepc4w2-ugl:deploy_classifier_FastAPI_Docker
```

- `--rm`: Delete this container after stopping running it, to avoid having to manually delete the container. Deleting unused containers helps the system to stay clean and tidy.
- `-p 80:80`: This flags performs a port mapping operation (local_machine_host_port:container_port). The container and the local machine each has its own set of ports. To access the port 80 within the container, need to map it to a port on the computer.

Go to [localhost:80](http://localhost:80). Should see a message about the server spinning up correctly.

## Make requests to the server

There are some examples of batches of data within the `wine-examples` directory. 

To get the predictions for a batch of 32 wines (found in the batch_1.json file), send a `POST` request to the server using `curl` like this:

```bash
curl -X POST http://localhost:81/predict \
    -d @./wine-examples/batch_1.json \
    -H "Content-Type: application/json"
```

- `-X`: Allows specifing the request type. In this case it is a `POST` request.
- `-d`: Stands for `data` and allows attaching data to the request.
- `-H`: Stands for `Headers` and allows passing additional information through the request. In this case it is used to the tell the server that the data is sent in a `JSON` format.

Should see a list with the 32 predictions (in order) for each one of the data points within the batch.

## Stopping the servers

`ctrl + c` to step to servers and the containers.

Use the `docker ps` command to check the name of the running containers and use the `docker stop name_of_container` command to stop them. Can use the `--rm` flag, so once stopped, these containers will also be deleted.
