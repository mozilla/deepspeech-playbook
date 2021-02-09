[Home](README.md) | [Previous - Setting up your DeepSpeech training environment](ENVIRONMENT.md) | [Next - Testing and evaluating your trained model](TESTING.md)

# Training a DeepSpeech model

## Contents

- [Training a DeepSpeech model](#training-a-deepspeech-model)
  * [Contents](#contents)
  * [Making training files available to the Docker container](#making-training-files-available-to-the-docker-container)
  * [Running training](#running-training)
    + [Specifying checkpoint directories so that you can restart training from a checkpoint](#specifying-checkpoint-directories-so-that-you-can-restart-training-from-a-checkpoint)
    + [Specifying the directory that the trained model should be exported to](#specifying-the-directory-that-the-trained-model-should-be-exported-to)
  * [Possible errors](#possible-errors)
    + [`Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.` error when training](#-failed-to-get-convolution-algorithm-this-is-probably-because-cudnn-failed-to-initialize--so-try-looking-to-see-if-a-warning-log-message-was-printed-above--error-when-training)
  * [Monitoring GPU use with `nvtop`](#monitoring-gpu-use-with--nvtop-)
  * [Advanced training options](#advanced-training-options)

## Making training files available to the Docker container

Before we can train a model, we need to make the training data available to the Docker container. The training data was previously prepared in the [instructions for formatting data](DATA_FORMATTING.md). Copy or extract them to the directory you specified in your _bind mount_. This will make them available to the Docker container.

```
$ cd deepspeech-data
$ ls cv-corpus-6.1-2020-12-11/
total 12
4 drwxr-xr-x 3 kathyreid kathyreid 4096 Feb  9 10:42 ./
4 drwxrwxr-x 7 kathyreid kathyreid 4096 Feb  9 10:43 ../
4 drwxr-xr-x 3 kathyreid kathyreid 4096 Feb  9 10:43 id/
```

We're now ready to being training.

## Running training

We're going to walk through some of the key parameters you can use with `DeepSpeech.py`.

```
python3 DeepSpeech.py \
  --train_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files persistent-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv
```

**Do not run this yet**

The options `--train_files`, `--dev_files` and `--test_files` take a path to the relevant data, which was prepared in the section on [data formatting](DATA_FORMATTING.md).

### Specifying checkpoint directories so that you can restart training from a checkpoint

As you are training your model, DeepSpeech will store _checkpoints_ to disk. The checkpoint allows interruption to training, and to restart training from the checkpoint, saving hours of training time.

Because we have our [training environment](ENVIRONMENT.md) configured to use Docker, we must ensure that our checkpoint directories are stored in the directory used by the _bind mount_, so that they _persist_ in the event of failure.

To specify checkpoint directories, use the `--checkpoint_dir` parameter with `DeepSpeech.py`:

```
python3 DeepSpeech.py \
  --train_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir deepspeech-data/checkpoints
```

**Do not run this yet**

### Specifying the directory that the trained model should be exported to

Again, because we have our [training environment](ENVIRONMENT.md) configured to use Docker, we must ensure that our trained model is stored in the directory used by the _bind mount_, so that it _persists_ in the event of failure of the Docker container.

To specify where the trained model should be saved, use the `--export-dir` parameter with `DeepSpeech.py`:

```
python3 DeepSpeech.py \
  --train_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir deepspeech-data/checkpoints \
  --export_dir deepspeech-data/exported-model
```

**You can run this command to start training**

## Possible errors

### `Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.` error when training

_You can safely skip this section if you have not encountered this error_

There have been several reports of an error similar to the below when training is initiated. Anecdotal evidence suggests that the error is more likely to be encountered if you are training using an RTX-model GPU.

The error will look like this:

```
Epoch 0 |   Training | Elapsed Time: 0:00:00 | Steps: 0 | Loss: 0.000000Traceback (most recent call last):
  File "/usr/local/lib/python3.6/dist-packages/tensorflow_core/python/client/session.py", line 1365, in _do_call
    return fn(*args)
  File "/usr/local/lib/python3.6/dist-packages/tensorflow_core/python/client/session.py", line 1350, in _run_fn
    target_list, run_metadata)
  File "/usr/local/lib/python3.6/dist-packages/tensorflow_core/python/client/session.py", line 1443, in _call_tf_sessionrun
    run_metadata)
tensorflow.python.framework.errors_impl.UnknownError: 2 root error(s) found.
  (0) Unknown: Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.
	 [[{{node tower_0/conv1d}}]]
	 [[concat/concat/_99]]
  (1) Unknown: Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.
	 [[{{node tower_0/conv1d}}]]
0 successful operations.
0 derived errors ignored.
```

To work around this error, you will need to set the `TF_FORCE_GPU_ALLOW_GROWTH` flag to `True`.

This is done in the file

`DeepSpeech/training/deepspeech_training/util/config.py`

and you should edit it as below:

```
root@687a2e3516d7:/DeepSpeech/training/deepspeech_training/util# nano config.py

...

    # Standard session configuration that'll be used for all new sessions.
    c.session_config = tfv1.ConfigProto(allow_soft_placement=True, log_device$
                                        inter_op_parallelism_threads=FLAGS.in$
                                        intra_op_parallelism_threads=FLAGS.in$

                                        gpu_options=tfv1.GPUOptions(allow_gro$

    # Set TF_FORCE_GPU_ALLOW_GROWTH to work around cuDNN error on RTX GPUs
    c.session_config.gpu_options.allow_growth=True
```

## Monitoring GPU use with `nvtop`

In a separate terminal (ie not from the session where you have the Docker container open), run the command `nvtop`. You should see the `DeepSpeech.py` process consuming all available GPUs.

If you _do not_ see the GPU(s) being heavily utilised, you may be training only on your CPUs and you should double check your [environment](ENVIRONMENT.md).

## Advanced training options

Advanced training options are available, such as _feature cache_ and _augmentation_. They are beyond the scope of this PlayBook, but you can [read more about them in the DeepSpeech documentation](https://deepspeech.readthedocs.io/en/v0.9.3/TRAINING.html#augmentation).

For a full list of parameters that can be passed to the `DeepSpeech.py` file, [please consult the DeepSpeech documentation](https://deepspeech.readthedocs.io/en/v0.9.3/Flags.html#training-flags).

---

[Home](README.md) | [Previous - Setting up your DeepSpeech training environment](ENVIRONMENT.md) | [Next - Testing and evaluating your trained model](TESTING.md)
