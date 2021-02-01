[Home](README.md) | [Previous - About DeepSpeech](DEEPSPEECH.md) | [Next - The alphabet.txt file](ALPHABET.md)

# Formatting your training data for DeepSpeech

## Contents

- [Formatting your training data for DeepSpeech](#formatting-your-training-data-for-deepspeech)
  * [Contents](#contents)
  * [Preparing your data for training](#preparing-your-data-for-training)
    + [Data from Common Voice](#data-from-common-voice)

DeepSpeech expects audio files to be WAV format, mono-channel, and with a 16kHz sampling rate.

For training, testing, and development, you need to feed DeepSpeech.py CSV files which contain three columns: `wav_filename,wav_filesize,transcript`. The `wav_filesize` (i.e. number of bytes) is used to group together audio of similar lengths for efficient batching.

## Preparing your data for training

### Data from Common Voice

If you are using data from Common Voice for training a model, you will need to prepare it as [outlined in the DeepSpeech documentation](https://deepspeech.readthedocs.io/en/master/TRAINING.html#common-voice-training-data).

In this example we will prepare the Indonesian dataset for training, but you can use any language from Common Voice that you prefer. We've chosen Indonesian as it has the same [orthographic alphabet](ALPHABET.md) as English, which means we don't have to use a different `alphabet.txt` file for training; we can use the default.

This example assumes you have already [set up a Docker environment with an attached volume for training](TRAINING.md).

First, [download the dataset from Common Voice](https://commonvoice.mozilla.org/en/datasets). Extract the archive into your `deepspeech-data` Docker volume. Make sure that you place the files into the `_data` directory, otherwise they will not be available from within your Docker container.

Start your DeepSpeech Docker container with the `deepspeech-data` volume attached. Your CV corpus data should be available from within the Docker container.

 ```
 root@3de3afbe5d6f:/DeepSpeech# ls -las persistent-data/cv-corpus-6.1-2020-12-11/id/
 total 5500
    4 drwxrwxr-x 3 1000 1000    4096 Dec 13 01:43 .
    4 drwxrwxr-x 3 1000 1000    4096 Jan  3 22:25 ..
  820 drwxrwxr-x 2 1000 1000  835584 Dec 11 19:42 clips
  396 -rw-r--r-- 1 1000 1000  401601 Dec 18 00:20 dev.tsv
  104 -rw-r--r-- 1 1000 1000  103332 Dec 18 00:20 invalidated.tsv
 1448 -rw-r--r-- 1 1000 1000 1481571 Dec 18 00:20 other.tsv
   28 -rw-rw-r-- 1 1000 1000   26394 Dec 18 20:32 reported.tsv
  392 -rw-r--r-- 1 1000 1000  399790 Dec 18 00:20 test.tsv
  456 -rw-r--r-- 1 1000 1000  465258 Dec 18 00:20 train.tsv
 1848 -rw-r--r-- 1 1000 1000 1889606 Dec 18 00:20 validated.tsv
```

Next, we will run the Common Voice importer that ships with DeepSpeech.

```
root@3de3afbe5d6f:/DeepSpeech# bin/import_cv2.py persistent_data/cv-corpus-6.1-2020-12-11/id
```

This will process all the CV data into the `clips` directory, and it can now be used [for training](TRAINING.md).

---

@todo are there any common errors in data formatting that we should cover at this point?

---

[Home](README.md) | [Previous - About DeepSpeech](DEEPSPEECH.md) | [Next - The alphabet.txt file](ALPHABET.md)
