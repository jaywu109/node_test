# GCP Deployment with Node.js Container

Preparation
---
- requirement: Docker, gcloud CLI, Git
- follow instructions in [Docker](https://www.docker.com/) and [gcloud CLI](https://cloud.google.com/sdk/docs/install-sdk#mac) to install dependencies on local machine 
- restart the terminal after Docker engine, gcloud CLI and Git are starting and initialized.
- run `gcloud auth configure-docker` to set up Docker for gcloud

Build image and upload to Container Registry
---
- Clone the repo and enter the docker folder: 
```
git clone https://github.com/jaywu109/node_test.git
cd node_test/docker
```
- Build image with `amd64` architecture:
```
docker buildx build --platform linux/amd64 -t [HOSTNAME]/[PROJECT-ID]/[IMAGE]:[TAG] .

# example
docker buildx build --platform linux/amd64 -t asia.gcr.io/node-test-runtime/node_test_runtime .

Arguments:
1. HOSTNAME: which region to save
    - gcr.io US
    - us.gcr.io US
    - eu.gcr.io Europe
    - asia.gcr.io Asia
2. PROJECT-ID: ID of project used in GCP
3. IMAGE: image name 
4. TAG (optional): default would be `latest` if not given
```
- Push the image to Container Registry:
```
docker push [HOSTNAME]/[PROJECT-ID]/[IMAGE]:[TAG]

# example
docker push asia.gcr.io/node-test-runtime/node_test_runtime:latest
```

- Run the container on local machine for testing and development (on `http://localhost:49160/`) -> **optional** :
```
docker run -itd  --name [CONTAINERNAME] -p [HOSTPORT]:[CONTAINERPORT]  [HOSTNAME]/[PROJECT-ID]/[IMAGE]:[TAG]

# example
docker run -itd  --name test_runtime -p 49160:3000 asia.gcr.io/node-test-runtime/node_test_runtime:latest

Arguments:
1. CONTAINERNAME: name of the container
2. HOSTPORT: port on host for connecting the application
3. CONTAINERPORT: should be same as the one in docker/dockerfile & app.js
```

- Deploy on GCP (`need set the name of app and region`)

```
gcloud run deploy --image [HOSTNAME]/[PROJECT-ID]/[IMAGE]:[TAG] --platform managed  

# example
gcloud run deploy --image asia.gcr.io/node-test-runtime/node_test_runtime --platform managed  
```

Note:
---
- Should use the **consistent** port number for the following 2 files
```
/docker/dockerfile
EXPOSE 3000  # should be the same as the port in the app.js

/app.js
const PORT = process.env.PORT || 3000 // shoud set this same as the one in dockerfile
```

References:
---
1. https://ithelp.ithome.com.tw/articles/10244413
2. https://medium.com/@vinhle95/deploy-a-containerised-node-js-application-to-cloud-run-80d2da6b7040
3. https://ithelp.ithome.com.tw/articles/10248834
4. https://hub.docker.com/_/node
