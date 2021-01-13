# Training a DeepSpeech model

---

@todo not sure if this information will be sequential - if so then something needs to go here that links to the [environment](ENVIRONMENT.md) page, which shows the developer how to build the DeepSpeech Docker image

---

Now that you have your Docker image build, you can run the image as a container to train a model.

_You will need the `id` of the Docker image that you created when you  [set up your DeepSpeech training environment](ENVIRONMENT.md)._

_In this example, the `id` of the Docker image is `c2ec2476fedb`. Substitute it with your own._

```
(deepspeech-training-venv) $ sudo docker run  -it --entrypoint /bin/bash c2ec2476fedb
```

The above command runs the Docker image as a _container_. The `entrypoint` instruction following `docker run` tells Docker to run the `/bin/bash` (ie shell) after creating the container.

This command assumes that `/bin/bash` will be invoked as the `root` user. This is necessary, as the Docker container needs to make changes to the filesystem. If you use the `-u $(id -u):$(id -g)` switches, you will tell Docker to invoke `/bin/bash` as the current user of the host that is running the Docker container. You will likely encounter `permission denied` errors while running training.

When you run the above command, you should see the following prompt:

```
________                               _______________                
___  __/__________________________________  ____/__  /________      __
__  /  _  _ \_  __ \_  ___/  __ \_  ___/_  /_   __  /_  __ \_ | /| / /
_  /   /  __/  / / /(__  )/ /_/ /  /   _  __/   _  / / /_/ /_ |/ |/ /
/_/    \___//_/ /_//____/ \____//_/    /_/      /_/  \____/____/|__/


WARNING: You are running this container as root, which can cause new files in
mounted volumes to be created as the root user on your host machine.

To avoid this, run the container by specifying your user's userid:

$ docker run -u $(id -u):$(id -g) args...

root@98302841b69f:/DeepSpeech#

```

From this point we can run DeepSpeech commands to do training from within the Docker container.

### Train using one of the sample scripts to ensure your Docker environment is functioning correctly

DeepSpeech includes a number of convenience scripts in the `bin` directory. They are named for the corpus they are configured for. To ensure that your Docker environment is functioning correctly, run one of these scripts.

```
root@98302841b69f:/DeepSpeech/bin# ./bin/run-ldc93s1.sh
```

This will train on a single audio file for 200 epochs.

## Creating a Docker container with an attached volume so that you can access training data

In the above example, we weren't undertaking any operations _outside_ the Docker container. In a real training scenario, you will want to store your training data and your trained model _outside_ of the Docker container, so that when the Docker container is destroyed, the data, and the trained model persist.

To do that, we [attach a volume to the Docker container](https://docs.docker.com/storage/volumes/).

First, let's stop and remove our previously-running Docker container. We first find the `id` of our running container, then we stop it and remove it.

```
$ sudo docker ps  

CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS     NAMES
3ab7c98a9e5b   c2ec2476fedb   "/bin/bash"   36 minutes ago   Up 36 minutes             dreamy_dhawan

$ sudo docker stop 3ab7c98a9e5b
3ab7c98a9e5b

$ sudo docker rm 3ab7c98a9e5b
3ab7c98a9e5b

```

---

@todo not sure if this section sits better in the ENVIRONMENT.md file, I will get Josh's thoughts

---

### Create the Docker volume

We create a Docker volume using the following command:

```
$ sudo docker volume create deepspeech-data
deepspeech-data
```

By default, the volume is created on the host filesystem at:

```
$ sudo ls -las /var/lib/docker/volumes
total 40
 4 drwx------  4 root root   4096 Jan  4 11:33 .
 4 drwx--x--x 13 root root   4096 Jan  4 08:35 ..
 0 brw-------  1 root root 259, 2 Jan  4 08:35 backingFsBlockDev
 4 drwxr-xr-x  3 root root   4096 Jan  4 11:33 deepspeech-data
24 -rw-------  1 root root  32768 Jan  4 11:33 metadata.db
```

We now have a place to persistently store both the data for our model and the trained model itself. Move your training data into this location, ensuring that you move it into the `_data` directory within the `deepspeech-directory` for it to be accessible from inside the Docker container.

### Creating a Docker container and attaching the persistent volume

Now, we create a Docker container and attach the persistent volume, using this command. Note that the reference to your particular Docker image - here shown as `c2ec2476fedb` will be different.  

```
$ sudo docker run  -it \
  --entrypoint /bin/bash \
  --gpus all \
  --mount type=volume,source=deepspeech-data,target=/DeepSpeech/persistent-data c2ec2476fedb

```

This command creates the container, and mounts the `deepspeech-data` directory to the `/DeepSpeech/persistent-data` directory within the container, as seen below:

```
________                               _______________                
___  __/__________________________________  ____/__  /________      __
__  /  _  _ \_  __ \_  ___/  __ \_  ___/_  /_   __  /_  __ \_ | /| / /
_  /   /  __/  / / /(__  )/ /_/ /  /   _  __/   _  / / /_/ /_ |/ |/ /
/_/    \___//_/ /_//____/ \____//_/    /_/      /_/  \____/____/|__/


WARNING: You are running this container as root, which can cause new files in
mounted volumes to be created as the root user on your host machine.

To avoid this, run the container by specifying your user's userid:

$ docker run -u $(id -u):$(id -g) args...

root@3de3afbe5d6f:/DeepSpeech# ls -las persistent-data/
total 12
4 drwxr-xr-x 3 root root 4096 Jan  4 01:45 .
4 drwxr-xr-x 1 root root 4096 Jan  4 01:34 ..
4 drwxrwxr-x 3 1000 1000 4096 Jan  3 22:25 cv-corpus-6.1-2020-12-11

```

Assuming that you have [prepared your training data](DATA_FORMATTING.md), you are now ready to train a model from within the DeepSpeech Docker container.

## Copying training files into the Docker container

Before we can train a model, we need to copy the files needed for training into the Docker container. These were previously prepared in the [instructions for formatting data](DATA_FORMATTING.md).

Copy or extract them to:

```
/var/lib/docker/volumes/deepspeech-data/_data
```

## Running training

To begin training, run the command:

```
python3 DeepSpeech.py \
  --train_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv
```

The options `--train_files`, `--dev_files` and `--test_files` take a path to the relevant data, which was prepared in the section on [data formatting](DATA_FORMATTING.md).

### Specifying checkpoint directories so that you can restart training from a checkpoint

As you are training your model, DeepSpeech will store _checkpoints_ to disk. The checkpoint allows interruption to training, and to restart training from the checkpoint, saving hours of training time.

Because we have our [training environment](ENVIRONMENT.md) configured to use Docker, we must ensure that our checkpoint directories are stored on the Docker volume, so that they _persist_ in the event of failure.

To specify checkpoint directories, use the `--checkpoint_dir` parameter with `DeepSpeech.py`:

```
python3 DeepSpeech.py \
  --train_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir persistent-data/checkpoints
```

### Specifying the directory that the trained model should be exported to

Again, because we have our [training environment](ENVIRONMENT.md) configured to use Docker, we must ensure that our trained model is stored to the Docker volume, so that it persists in the event of failure of the Docker container.

To specify where the trained model should be saved, use the `--export-dir` parameter with `DeepSpeech.py`:

```
python3 DeepSpeech.py \
  --train_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir persistent-data/checkpoints \
  --export_dir persistent-data/exported-model
```

### Monitoring GPU use with `nvtop`

In a separate terminal (ie not from the session where you have the Docker container open), run the command `nvtop`. You should see the `DeepSpeech.py` process consuming all available GPUs.

If you _do not_ see the GPU(s) being heavily utilised, you may be training only on your CPUs and you should double check your [environment](ENVIRONMENT.md).

---

@todo want to chat with Josh what else should be in this training file such as

  - fine tuning a model or doing transfer learning
  - setting the number of epochs and _how_ to set the number of epochs
  - should the `feature_cache` be set to be on the persistent storage?

---
