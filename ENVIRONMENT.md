# Setting up your Python virtual environment for training using DeepSpeech

This section of the Playbook assumes you are comfortable installing DeepSpeech and using it with a pre-trained model; here we provide information on setting up a Docker environment for training your own speech recognition model using DeepSpeech.

## Table of contents

@todo add ToC for this page

## What is Docker and why is it recommended for training a model with DeepSpeech?

[Docker](https://www.docker.com/why-docker) is virtualization software that allows a consistent collection of software, dependencies and environments to be packaged into a _container_ which is then run on a host, or many hosts. It is one way to manage the many software dependencies which are required for training a model with DeepSpeech, particularly if using an NVIDIA GPU.

## Creating a virtual environment and clone DeepSpeech

First, [follow the DeepSpeech documentation to create and activate a virtual environment and clone DeepSpeech into the environment](https://deepspeech.readthedocs.io/en/master/TRAINING.html). Install the DeepSpeech training code and dependencies.

---

@todo do we have a heuristic for the volume of data that can be reasonably trained using a CPU only? Obviously the CPU itself and the number of cores it has will influence this significantly, but I wonder if there are any rules of thumb? That way, we can say here that

---

## Create and rename the Dockerfile

Follow the DeepSpeech documentation to make the `Dockerfile`, ie

```
$ make Dockerfile.train
```

We now need to rename the `Dockerfile` so that we can build a Docker container in which to train.

```
$ cp Dockerfile.train Dockerfile
```


## Install Docker and build a Docker image for DeepSpeech

First, you must install Docker on your host. Follow the [instructions on the Docker website](https://docs.docker.com/engine/install/ubuntu/).

---

@todo this assumes Ubuntu Linux, is this reasonable?

---

Once you have installed Docker, you are ready to build a Docker image using the `Dockerfile`. Once the image is built, you can then run the Docker container to perform training.

**WARNING: This command will cause several gigabytes of data to be downloaded. Do not do this if you are on a metered internet connection.**

Build the Docker image using the following command:

```
(deepspeech-training-venv) $ sudo docker build -t deepspeech:0.9.3 .
```

The `-t` flag allows us to tag the image using a name, in this case `deepspeech:0.9.3`. This is helpful if you want to build separate Docker images, for example for different versions of DeepSpeech.

As part of the build, the Docker image will train a small model to test that the image has been built correctly. It will finish with:

```
Testing model on data/ldc93s1/ldc93s1.csv
I Test epoch...
Test on data/ldc93s1/ldc93s1.csv - WER: 0.090909, CER: 0.019231, loss: 7.840517
--------------------------------------------------------------------------------
Best WER:
--------------------------------------------------------------------------------
WER: 0.090909, CER: 0.019231, loss: 7.840517
 - wav: file:///DeepSpeech/data/ldc93s1/LDC93S1.wav
 - src: "she had your dark suit in greasy wash water all year"
 - res: "she had your dar suit in greasy wash water all year"
--------------------------------------------------------------------------------
Median WER:
--------------------------------------------------------------------------------
WER: 0.090909, CER: 0.019231, loss: 7.840517
 - wav: file:///DeepSpeech/data/ldc93s1/LDC93S1.wav
 - src: "she had your dark suit in greasy wash water all year"
 - res: "she had your dar suit in greasy wash water all year"
--------------------------------------------------------------------------------
Worst WER:
--------------------------------------------------------------------------------
WER: 0.090909, CER: 0.019231, loss: 7.840517
 - wav: file:///DeepSpeech/data/ldc93s1/LDC93S1.wav
 - src: "she had your dark suit in greasy wash water all year"
 - res: "she had your dar suit in greasy wash water all year"
--------------------------------------------------------------------------------
Removing intermediate container 814bb18f5f7e
 ---> c2ec2476fedb
Successfully built c2ec2476fedb
Successfully tagged deepspeech:0.9.3
```

If you see this message, your Docker image has been built correctly. You can verify this by running the command `sudo docker image ls` as below:

```
(deepspeech-training-venv) $ sudo docker image ls

REPOSITORY              TAG              IMAGE ID       CREATED         SIZE
deepspeech              0.9.3            c2ec2476fedb   6 minutes ago   4.85GB
tensorflow/tensorflow   1.15.4-gpu-py3   a1e8e97ee677   3 months ago    3.58GB

```

The `tensorflow` Docker image is a _child_ image of the DeepSpeech Docker image.


You are now ready to begin training your model.

---

@todo not sure if these pages will be sequential, but it makes sense to have a "next" page

---
