# Server-Code-execc

##Dockerfile
```
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

```
