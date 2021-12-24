# Deploying and Hosting a Machine Learning Model with FastAPI and Heroku

## Want to learn how to build this?

Check out the [post](https://testdriven.io/blog/fastapi-machine-learning).

## Want to use this project?

### With Docker

1. Build and tag the Docker image:

    ```sh
    $ docker build -t fastapi-prophet .
    ```

1. Spin up the container:

    ```sh
    $ docker run --name fastapi-ml -e PORT=8008 -p 8008:8008 -d fastapi-prophet:latest
    ```

1. Train the model:

    ```sh
    $ docker exec -it fastapi-ml python

    >>> from model import train, predict, convert
    >>> train()
    ```

1. Test:

    ```sh
    $ curl \
      --header "Content-Type: application/json" \
      --request POST \
      --data '{"ticker":"MSFT"}' \
      http://localhost:8008/predict
    ```
** Note, I built this with guidance from https://medium.com/geekculture/from-apple-silicon-to-heroku-docker-registry-without-swearing-36a2f59b30a3 using Option A:buildx as I use Apple M1 Chip which is based on the ARM architecture. **

In order to create a x86 compatible image, the solution is **NOT to use the heroku:container push** command but rather building the image locally with Docker buildx

```
docker buildx build --platform linux/amd64 -t desolate-plateau-08735 .
# tag image to match Heroku naming conventions
docker tag desolate-plateau-08735 registry.heroku.com/desolate-plateau-08735/web
# push
docker push registry.heroku.com/desolate-plateau-08735/web
```
Once pushed, the image can then be released
```
heroku container:release web -a desolate-plateau-08735
```

### Without Docker

1. Create and activate a virtual environment:

    ```sh
    $ python3 -m venv venv && source venv/bin/activate
    ```

1. Install the requirements:

    ```sh
    (venv)$ pip install -r requirements.txt
    ```

1. Train the model:

    ```sh
    (venv)$ python

    >>> from model import train, predict, convert
    >>> train()
    ```

1. Run the app:

    ```sh
    (venv)$ uvicorn main:app --reload --workers 1 --host 0.0.0.0 --port 8008
    ```

1. Test:

    ```sh
    $ curl \
      --header "Content-Type: application/json" \
      --request POST \
      --data '{"ticker":"MSFT"}' \
      http://localhost:8008/predict
    ```
