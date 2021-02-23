[Home](README.md) | [Previous - Setting up your DeepSpeech training environment](ENVIRONMENT.md) | [Next - Testing and evaluating your trained model](TESTING.md)

# Training a DeepSpeech model

## Contents

- [Training a DeepSpeech model](#training-a-deepspeech-model)
  * [Contents](#contents)
  * [Making training files available to the Docker container](#making-training-files-available-to-the-docker-container)
  * [Running training](#running-training)
    + [Specifying checkpoint directories so that you can restart training from a checkpoint](#specifying-checkpoint-directories-so-that-you-can-restart-training-from-a-checkpoint)
    + [Specifying the directory that the trained model should be exported to](#specifying-the-directory-that-the-trained-model-should-be-exported-to)
  * [Other useful parameters that can be passed to `DeepSpeech.py`](#other-useful-parameters-that-can-be-passed-to--deepspeechpy-)
    + [`n_hidden` parameter](#-n-hidden--parameter)
    + [Reduce learning rate on plateau (RLROP)](#reduce-learning-rate-on-plateau--rlrop-)
    + [Early stopping](#early-stopping)
  * [Advanced training options](#advanced-training-options)
  * [Monitoring GPU use with `nvtop`](#monitoring-gpu-use-with--nvtop-)
  * [Possible errors](#possible-errors)
    + [`Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.` error when training](#-failed-to-get-convolution-algorithm-this-is-probably-because-cudnn-failed-to-initialize--so-try-looking-to-see-if-a-warning-log-message-was-printed-above--error-when-training)


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

## Other useful parameters that can be passed to `DeepSpeech.py`

_For a full list of parameters that can be passed to `DeepSpeech.py`, please [consult the documentation](https://deepspeech.readthedocs.io/en/latest/Flags.html#training-flags).

`DeepSpeech.py` has many parameters - too many to cover in an introductory PlayBook. Here are some of the commonly used parameters that are useful to explore as you begin to train speech recognition models with DeepSpeech.

### `n_hidden` parameter

Neural networks work through a series of _layers_. Usually there is an _input layer_, which takes an input - in this case an audio recording, and a series of _hidden layers_ which identify features of the _input layer_, and an _output layer_, which makes a prediction - in this case a character.

In large datasets, you need many _hidden layers_ to arrive at an accurate trained model. With smaller datasets, often called _toy corpora_ or _toy datasets_, you don't need as many _hidden layers_.

If you are learning how to train using DeepSpeech, and are working with a small dataset, you will save time by reducing the value of `--n_hidden`. This reduces the number of _hidden layers_ in the neural network. This both reduces the amount of computing resources consumed during training, and makes training a model much faster.

The `--n_hidden` parameter has a default value of `2048`.

```
python3 DeepSpeech.py \
  --train_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir deepspeech-data/checkpoints \
  --export_dir deepspeech-data/exported-model \
  --n_hidden 64
```

### Reduce learning rate on plateau (RLROP)

In neural networks, the _learning rate_ is the rate at which the neural network makes adjustments to the predictions it generates. The accuracy of predictions is measured using the _loss_. The lower the _loss_, the lower the difference between the neural network's predictions, and actual known values. If training is effective, _loss_ will reduce over time. A neural network that has a _loss_ of `0` has perfect prediction.

 If the _learning rate_ is too low, predictions will take a long time to align with actual targets. If the learning rate is too high, predictions will overshoot actual targets. The _learning rate_ has to aim for a balance between _exploration and exploitation_.

If loss is not reducing over time, then the training is said to have _plateaued_ - that is, the adjustments to the predictions are not reducing _loss_. By adjusting the _learning rate_, and other parameters, we may escape the _plateau_ and continue to decrease _loss_.

* The `--reduce_lr_on_plateau` parameter instructs `DeepSpeech.py` to automatically reduce the _learning rate_ if a _plateau_ is detected. By default, this is `false`.

* The `--plateau_epochs` parameter specifies the number of epochs of training during which there is no reduction in loss that should be considered a _plateau_. The default value is `10`.

* The `--plateau_reduction` parameter specifies a multiplicative factor that is applied to the current learning rate if a _plateau_ is detected. This number **must** be less than `1`, otherwise it will _increase_ the learning rate. The default value is `0.1`.

An example of training with these parameters would be:

```
python3 DeepSpeech.py \
  --train_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir deepspeech-data/checkpoints \
  --export_dir deepspeech-data/exported-model \
  --n_hidden 64 \
  --reduce_lr_on_plateau true \
  --plateau_epochs 8 \
  --plateau_reduction 0.08
```

### Early stopping

If training is not resulting in a reduction of _loss_ over time, you can pass parameters to `DeepSpeech.py` that will stop training. This is called _early stopping_ and is useful if you are using cloud compute resources, or shared resources, and can't monitor the training continuously.

* The `--early_stop` parameter enables early stopping. It is set to `false` by default.

* The `--es_epochs` parameter takes an integer of the number of epochs with no improvement after which training will be stopped. It is set to `25` by default, for example if this parameter is omitted, but `--early_stop` is set to `true`.

* The `--es_min_delta` parameter is the minimum change in _loss_ per epoch that qualifies as an improvement. By default it is set to `0.05`.

An example of training with these parameters would be:

```
python3 DeepSpeech.py \
  --train_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir deepspeech-data/checkpoints \
  --export_dir deepspeech-data/exported-model \
  --n_hidden 64 \
  --reduce_lr_on_plateau true \
  --plateau_epochs 8 \
  --plateau_reduction 0.08 \
  --early_stop true \
  --es_epochs 10 \
  --es_min_delta 0.06
```

## Advanced training options

Advanced training options are available, such as _feature cache_ and _augmentation_. They are beyond the scope of this PlayBook, but you can [read more about them in the DeepSpeech documentation](https://deepspeech.readthedocs.io/en/v0.9.3/TRAINING.html#augmentation).

For a full list of parameters that can be passed to the `DeepSpeech.py` file, [please consult the DeepSpeech documentation](https://deepspeech.readthedocs.io/en/v0.9.3/Flags.html#training-flags).

## Monitoring GPU use with `nvtop`

In a separate terminal (ie not from the session where you have the Docker container open), run the command `nvtop`. You should see the `DeepSpeech.py` process consuming all available GPUs.

If you _do not_ see the GPU(s) being heavily utilised, you may be training only on your CPUs and you should double check your [environment](ENVIRONMENT.md).

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

---

[Home](README.md) | [Previous - Setting up your DeepSpeech training environment](ENVIRONMENT.md) | [Next - Testing and evaluating your trained model](TESTING.md)
