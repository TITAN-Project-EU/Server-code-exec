 
# Server-Code-execc
## Table of Contents
- [Connect to the Server](#connect-to-the-server)
- [Dockerfile](#dockerfile)
- [Kubernetes YAML File](#kubernetes-yaml-file)
- [Run Code with Kubernetes Node](#run-code-with-kubernetes-node)
- [Resources Managment](#resources-managment)

![Pipeline](https://github.com/roumpakis/Server-Code-exec/blob/master/images/steps.png)

---


### Connect to the Server

``` console
ssh username@hostname
```
Or with putty <br/><br/>
![putty](https://github.com/roumpakis/Server-Code-exec/blob/master/images/Capture.JPG)
 
 <br/>
 
 All files described below are available at your home folder ```\home\username\k8s-example``` when conected to the server.

---



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
***Dockerfile with specific libs versions***  <br/>
``` java
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
    emcee==3.0.2 \
    getdist==1.1.4 \
    joblib==1.1.0 \
    chainconsumer==0.33.2 \
    astropy==4.3.1 \
    scikit-learn==0.24.2 \
    tqdm==4.62.3 \
    zenodo-get==0.1.1 \
    jax==0.2.25 \
    numpyro==0.8.0 \
    scipy==1.7.1 \
    lightning==2.0.4 \
    corner==2.2.1 \
    nflows==0.14 \
    sbi==0.17.0 \
    sbibm==0.4.0 \
    spectral-cube==0.5.0 \
    optuna==2.10.0 \
    captum==0.4.1

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
  name: node-name
spec:
  containers:
  - name: container1
    image: pod-name:local
    resources:
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
```console
kubectl exec my-pod -it -- /bin/sh
```
---
### Run code with kubernetes node  
With ```#``` the commands will run directly on ```my-pod``` kubernetes node. <br/>
In ```manifest.yaml``` we have mount ```path``` with ```hostPath``` and for this example it means that 
```/mnt/files/shared``` path in server is now visible at ```/app/scripts``` path in kubernetes node.


```console
cd /app/scripts
ls
python transparency.py
```
![running-code](https://github.com/roumpakis/Server-Code-exec/blob/master/images/code-exec.JPG)

---

### Resources Managment
GPU time-slicing enables workloads that are scheduled on oversubscribed GPUs to interleave with one another. 
There is no memory or fault-isolation between replicas. Internally, GPU time-slicing is used to multiplex workloads from replicas of the same underlying GPU.

For demanding tasks and in consultation with the server administrators a specific physical unit can be used.



![running-code](https://github.com/roumpakis/Server-Code-exec/blob/master/images/specific.JPG)








