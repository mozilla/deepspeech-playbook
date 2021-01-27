# kathy's scratch pad

_This space is for notes that are not likely to make it in to the Playbook itself, but which I don't want to lose._




When you are working with DeepSpeech to train a speech recognition model, then your Python virtual environment is very important. Here, we walk you through how to set up your Python virtual environment, install Docker, build a Docker file, and create a Docker container in which to perform training of your model.

## Table of Contents

@todo add a ToC

## What is a virtual environment?

In the Python programming language, a [virtual environment](https://docs.python.org/3/library/venv.html) is a way of managing and isolating dependencies from the host you're developing on. The Python `venv` tool creates a local copy of the Python binary, and the `pip3` tool allows you to install packages _within_ just the virtual environment. This avoids obstacles that are often encountered with incompatible dependencies.

---

@todo question - if the developer is doing training using a Docker container, then do they need to set up a _virtual environment_? I still think it's useful to have this in the Playbook so that people working with DeepSpeech are used to creating virtual environments.

---

Create a virtual environment for your DeepSpeech model training using the following commands. These instructions assume you are using Ubuntu Linux.

1. First, check that you have the package `python3-venv` installed on your host. This is required to create a Python _virtual environment_.

```
$ which python3-venv
```

If this command does not return a `path` to where `python3-venv` is installed, you will need to install `python3-venv` with the command:

```
$ sudo apt install python3-venv
```

Now, you can create a _virtual environment_ with the following command:

```
$ python3 -m $HOME/tmp/deepspeech-venv/
```

This creates a directory called `deepspeech-training-venv`.

We now need to _activate_ the virtual environment. This command tells your host to start using the Python binary within the _virtual environment_, rather than the Python binary installed on the system. Use the following command:

```
$ source $HOME/tmp/deepspeech-venv/bin/activate
(deepspeech-venv) $
```

_Note how the name of the virtual environment is now prepended in brackets in the shell prompt._


2. Now, we install DeepSpeech into the _virtual environment_. This will depend on if you are using an NVIDIA GPU or not. [Check the release notes on GitHub for a list of compatible GPUs](https://github.com/mozilla/DeepSpeech/releases/latest).

If you have a compatible GPU, use the command:

```
$ pip3 install deepspeech-gpu
```

If you _do not_ have a compatible GPU, you will be training using only the CPU resources on your host. This will take much longer, and is only suitable for training small amounts of data. Install DeepSpeech using the command:

```
$ pip3 install deepspeech
```

---

@todo do we have a heuristic for the volume of data that can be reasonably trained using a CPU only? Obviously the CPU itself and the number of cores it has will influence this significantly, but I wonder if there are any rules of thumb?

---



- need to mention why the `venv` is so importing
- ensuring that only the docs are used, because of pinned dependencies
- how to tell whether you have a GPU or not, and if you do then how to install Tensorflow with GPU support

need to clone DeepSpeech into the `venv`


## Setting up Docker and a DockerFile to manage CUDA dependencies

- Create the DockerFile as per the documentation
- Need to rename the DockerFile - the DockerFile that is generated is called `Dockerfile.train` and needs to be renamed to `Dockerfile`.

```
$ cp Dockerfile.train Dockerfile
```

- Install Docker

https://docs.docker.com/engine/install/ubuntu/


- To build the Docker image

```
$ sudo docker build -t kathyreid/deepspeech-playbook .
```

The `-t` allows you to name the Docker image

> Need to be clear that this will pull down several gig worth of data - in case anyone is on a metered connection

> In the actual tutorial I will name it something different to show that it is a training image.

```
Successfully built 74316443a799
Successfully tagged kathyreid/deepspeech-playbook:latest
```


#### Eigen3

What is Eigen3?


```
-- Could NOT find Eigen3 (missing: EIGEN3_INCLUDE_DIR EIGEN3_VERSION_OK) (Required is at least version "2.91.0")
CMake Warning at lm/interpolate/CMakeLists.txt:66 (message):
  Not building interpolation.  Eigen3 was not found.


-- To install Eigen3 in your home directory, copy paste this:
export EIGEN3_ROOT=$HOME/eigen-eigen-07105f7124f9
(cd $HOME; wget -O - https://bitbucket.org/eigen/eigen/get/3.2.8.tar.bz2 |tar xj)

```

```
(deepspeech-train-venv) kathyreid@3ai-precision5500:~$ (cd $HOME; wget -O - https://bitbucket.org/eigen/eigen/get/3.2.8.tar.bz2 |tar xj)
--2020-12-30 15:03:56--  https://bitbucket.org/eigen/eigen/get/3.2.8.tar.bz2
Resolving bitbucket.org (bitbucket.org)... 104.192.141.1, 2406:da00:ff00::6b17:d1f5, 2406:da00:ff00::22cd:e0db, ...
Connecting to bitbucket.org (bitbucket.org)|104.192.141.1|:443... connected.
HTTP request sent, awaiting response... 404 Not Found
2020-12-30 15:03:58 ERROR 404: Not Found.


bzip2: Compressed file ends unexpectedly;
	perhaps it is corrupted?  *Possible* reason follows.
bzip2: Inappropriate ioctl for device
	Input file = (stdin), output file = (stdout)

It is possible that the compressed file(s) have become corrupted.
You can use the -tvv option to test integrity of such files.

You can use the `bzip2recover' program to attempt to recover
data from undamaged sections of corrupted files.

tar: Child returned status 2
tar: Error is not recoverable: exiting now
```

> Not sure what Eigen does?

And the link is 404'ing. Fixed it using this PR:
https://github.com/mozilla/DeepSpeech/pull/3480

## Using the Docker image for training





## CUDA dependencies

The documentation links to [this list of CUDA compatibilities](https://www.tensorflow.org/install/source#gpu), but does not go into a lot of depth on how to check if you have the right dependencies installed.

> Is there a quick or easy way to check if CUDA dependencies are installed, or is this likely to vary significantly for each system?

> I think this question is less relevant now that the CUDA deps are packaged within the Docker image, and are pulled in using the `Dockerfile`.

I used the compatibility chart to check whether I had the right deps:

* tensorflow - 2.4.0
* python - 3.8
* gcc - 9.3.0 - this is much higher than specified - the tensorflow site specifies gcc 7.3.1

I figured I didn't need `bazel` as I wasn't building anything.

The machine I'm on here has a really low end GPU, which I don't think will be compatible with CUDA or `tensorflow`, so I can't really check for NVIDIA CUDA drivers and dependencies (I have another machine in Canberra, I can test on that)

```
gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)
```

```
(deepspeech-train-venv) kathyreid@3ai-precision5500:~/deepspeech-train-venv$ pip install tensorflow
Requirement already satisfied: tensorflow in ./lib/python3.8/site-packages (2.4.0)
```


---

## Getting GPU to work in Docker


https://blog.roboflow.com/use-the-gpu-in-docker/

- install GPU drivers on base machine, validate with `nvidia-smi`

```
<pre>$ nvidia-smi
Sat Jan  9 11:48:50 2021       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: 11.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  GeForce GTX 1060    Off  | 00000000:01:00.0  On |                  N/A |
| N/A   70C    P0    27W /  N/A |    766MiB /  6069MiB |      2%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1098      G   /usr/lib/xorg/Xorg                 35MiB |
|    0   N/A  N/A      2799      G   /usr/lib/xorg/Xorg                348MiB |
|    0   N/A  N/A      2939      G   /usr/bin/gnome-shell              200MiB |
|    0   N/A  N/A     25790      G   ...token=4412988484634553693       46MiB |
|    0   N/A  N/A     88321      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A     88585      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A     88629      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A     88633      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A     88636      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A     88644      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A     88649      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A     88653      G   ...yreid/firefox/firefox-bin        1MiB |
|    0   N/A  N/A    589047      G   ...e/Steam/ubuntu12_32/steam       10MiB |
|    0   N/A  N/A    589210      G   ./steamwebhelper                    1MiB |
|    0   N/A  N/A    589231      G   ...e Steam Client --lang=en_       11MiB |
|    0   N/A  N/A   2901345      G   /usr/lib/firefox/firefox            1MiB |
|    0   N/A  N/A   2901358      G   /usr/lib/firefox/firefox            1MiB |
|    0   N/A  N/A   2901366      G   /usr/lib/firefox/firefox            3MiB |
|    0   N/A  N/A   3112017      G   .../debug.log --shared-files       46MiB |
|    0   N/A  N/A   3275236      G   ...AAAAAAAA== --shared-files       28MiB |
+-----------------------------------------------------------------------------+

```

docker: Error response from daemon: could not select device driver "" with capabilities: [[gpu]].

https://github.com/NVIDIA/nvidia-docker/issues/1034



-
