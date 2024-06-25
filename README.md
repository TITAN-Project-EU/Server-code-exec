
# Server-Code-execc
## Table of Contents
- [Dockerfile](#dockerfile)
- [Kubernetes YAML File](#kubernetes-yaml-file)
- [Run Code on K8S Node](#Run-code-with-kubernetes-node)

![Pipeline](https://github.com/roumpakis/Server-Code-exec/blob/master/images/pipeline.jpg)


### Dockerfile
``` javascript
# Use the official CUDA 11.5 image with Ubuntu 20.04 as a base
FROM nvidia/cuda:11.5.2-runtime-ubuntu20.04

# Set the environment variable for non-interactive apt-get
ENV DEBIAN_FRONTEND=noninteractive

# Install required dependencies
RUN apt-get update && apt-get install -y \
    python3-pip \
    python3-dev \
    build-essential \
    git \
    wget \
    curl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Create a symbolic link for python3
RUN ln -s /usr/bin/python3 /usr/bin/python

# Upgrade pip
RUN pip3 install --upgrade pip

# Install the required Python libraries (excluding built-in modules)
RUN pip3 install \
    emcee \
    getdist \
    joblib \
    chainconsumer \
    astropy \
    scikit-learn \
    tqdm \
    zenodo-get \
    jax \
    numpyro \
    scipy \
    lightning \
    corner \
    nflows \
    sbi \
    sbibm \
    spectral-cube \
    optuna \
    captum

# Set the working directory
WORKDIR /opt

```

***Parameters to change***  <br/>
```Install the required Python libraries:``` Install the libraires that you want your docker pod to include.<br/>

***Building docker image in local repository***  <br/>
```console
  docker build -t pod-name:local .

```

---
### Kubernetes YAML file
``` javascript

apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: container1
    image: doc:local
    resources:
      limits:
        nvidia.com/gpu: 1
    volumeMounts:
    - name: user-scripts
      mountPath: /app/scripts
    env:
    - name: NVIDIA_VISIBLE_DEVICES
      value: all
    securityContext:
      privileged: true
      runAsUser: 0
    command: ["sh", "-c", "echo Your pod is running && sleep infinity"]
  volumes:
  - name: user-scripts
    hostPath:
      path: /mnt/files/shared

```
***Parameters to change***  <br/>
```image:``` Fill with the Docker image.<br/>
```mountPath:``` Working dir for kubernetes node.<br/>
```path:``` Path on server will be visible on kubernetes node at mountPath.<br/>

***Create the Kubernetes node***  <br/>
```console
   kubectl apply -f manifest.yaml

```
***Check kubernetes nodes***  <br/>
```console
kubectl get pods
```
![running-pods](https://github.com/roumpakis/Server-Code-exec/blob/master/images/running-pods.JPG)

---
### Run code with kubernetes node  <br/>
With ```#``` the commands will run directly on ```my-pod``` kubernetes node. <br/>
In ```manifest.yaml``` we have mount ```path``` with ```hostPath``` and for these example means that 
```/mnt/files/shared``` path in server is now visible at ```/app/scripts``` path in kubernetes node.


```console
cd /app/scripts
ls
python transparency.py
```
![running-code](https://github.com/roumpakis/Server-Code-exec/blob/master/images/code-exec.JPG)

---




