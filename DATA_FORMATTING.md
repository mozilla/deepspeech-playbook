[Home](README.md) | [Previous - About DeepSpeech](DEEPSPEECH.md) | [Next - The alphabet.txt file](ALPHABET.md)

# Formatting your training data for DeepSpeech

## Contents

- [Formatting your training data for DeepSpeech](#formatting-your-training-data-for-deepspeech)
  * [Contents](#contents)
  * [Preparing your data for training](#preparing-your-data-for-training)
    + [Data from Common Voice](#data-from-common-voice)
  * [Importers](#importers)

DeepSpeech expects audio files to be WAV format, mono-channel, and with a 16kHz sampling rate.

For training, testing, and development, you need to feed DeepSpeech.py CSV files which contain three columns: `wav_filename,wav_filesize,transcript`. The `wav_filesize` (i.e. number of bytes) is used to group together audio of similar lengths for efficient batching.

## Collecting data

This PlayBook is focused on _training_ a speech recognition model, rather than on _collecting_ the data that is required for an accurate model. However, a good model starts with data.

* Ensure that your voice clips are 10-20 seconds in length. If they are longer or shorter than this, your model will be less accurate.

* Ensure that every character in your transcription of a voice clip is in your [alphabet.txt](ALPHABET.md) file

* Ensure that your voice clips exhibit the same sort of diversity you expect to encounter in your runtime audio. This means a diversity of accents, genders, background noise and so on.

* Ensure that your voice clips are created using similar microphones to that which you expect in your runtime audio. For example, if you expect to deploy your model on Android mobile phones, ensure that your training data is generated from Android mobile phones.

* Ensure that the phrasing on which your voice clips are generated covers the phrases you expect to encounter in your runtime audio. 

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

## Importers

DeepSpeech ships with several scripts which act as _importers_ - preparing a corpus of data for training by DeepSpeech.

If you want to create importers for a new language, or a new corpus, you will need to fork the DeepSpeech repository, then add support for the new language and/or corpus by creating an _importer_ for that language/corpus.

The existing importer scripts are a good starting point for creating your own importers.

They are located in the `bin` directory of the DeepSpeech repo:

```
~/DeepSpeech/bin$ ls -las | grep import
 4 -rwxrwxr-x  1 kathyreid kathyreid  3583 Feb  1 23:58 import_aidatatang.py
 4 -rwxrwxr-x  1 kathyreid kathyreid  3448 Feb  1 23:58 import_aishell.py
24 -rwxrwxr-x  1 kathyreid kathyreid 23640 Feb  1 23:58 import_ccpmf.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  8038 Feb  1 23:58 import_cv2.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  5717 Feb  1 23:58 import_cv.py
12 -rwxrwxr-x  1 kathyreid kathyreid  9207 Feb  1 23:58 import_fisher.py
 4 -rwxrwxr-x  1 kathyreid kathyreid  3201 Feb  1 23:58 import_freestmandarin.py
16 -rwxrwxr-x  1 kathyreid kathyreid 12829 Feb  1 23:58 import_gram_vaani.py
 4 -rwxrwxr-x  1 kathyreid kathyreid  1021 Feb  1 23:58 import_ldc93s1.py
12 -rwxrwxr-x  1 kathyreid kathyreid  8602 Feb  1 23:58 import_librivox.py
12 -rwxrwxr-x  1 kathyreid kathyreid  9311 Feb  1 23:58 import_lingua_libre.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  4447 Feb  1 23:58 import_magicdata.py
12 -rwxrwxr-x  1 kathyreid kathyreid  8225 Feb  1 23:58 import_m-ailabs.py
 4 -rwxrwxr-x  1 kathyreid kathyreid  3583 Feb  1 23:58 import_primewords.py
12 -rwxrwxr-x  1 kathyreid kathyreid  8248 Feb  1 23:58 import_slr57.py
12 -rwxrwxr-x  1 kathyreid kathyreid 11306 Feb  1 23:58 import_swb.py
20 -rwxrwxr-x  1 kathyreid kathyreid 20217 Feb  1 23:58 import_swc.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  5538 Feb  1 23:58 import_ted.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  5550 Feb  1 23:58 import_timit.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  7777 Feb  1 23:58 import_ts.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  6708 Feb  1 23:58 import_tuda.py
 8 -rwxrwxr-x  1 kathyreid kathyreid  6460 Feb  1 23:58 import_vctk.py
12 -rwxrwxr-x  1 kathyreid kathyreid  8578 Feb  1 23:58 import_voxforge.py
```

The importer scripts ensure that the `.wav` files and corresponding transcriptions are in the `.csv` format expected by DeepSpeech.

---

[Home](README.md) | [Previous - About DeepSpeech](DEEPSPEECH.md) | [Next - The alphabet.txt file](ALPHABET.md)
