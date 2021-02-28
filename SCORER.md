[Home](README.md) | [Previous - The alphabet.txt file](ALPHABET.md) | [Next - Acoustic Model and Language Model](AM_vs_LM.md)

# Scorer - language model for determining which words occur together

## Contents

- [Scorer - language model for determining which words occur together](#scorer---language-model-for-determining-which-words-occur-together)
  * [Contents](#contents)
    + [What is a scorer?](#what-is-a-scorer-)
    + [Building your own scorer](#building-your-own-scorer)
      - [Preparing the text file](#preparing-the-text-file)
      - [Using `generate_lm.py` to create `lm.binary` and `vocab-500000.txt` files](#using--generate-lmpy--to-create--lmbinary--and--vocab-500000txt--files)
      - [Generating a `kenlm.scorer` file from `generate_scorer_package`](#generating-a--kenlmscorer--file-from--generate-scorer-package-)
      - [Using the scorer file in training](#using-the-scorer-file-in-training)

### What is a scorer?

A scorer is a _language model_ and it is used by DeepSpeech to improve the accuracy of transcription. A _language model_ predicts which words are more likely to follow each other. For example, the word `chicken` might be frequently followed by the words `nuggets`, `soup` or `rissoles`, but is unlikely to be followed by the word `purple`. The scorer identifies probabilities of words occurring together.

The default scorer used by DeepSpeech is trained on the LibriSpeech dataset. The LibriSpeech dataset is based on [LibriVox](https://librivox.org/) - an open collection of out-of-copyright and public domain works.

You may need to build your own scorer - your own _language model_ if:

* You are training DeepSpeech in another language
* You are training a speech recognition model for a particular domain - such as technical words, medical transcription, agricultural terms and so on
* If you want to improve the accuracy of transcription

**DeepSpeech supports the _optional_ use of an external scorer - if you're not sure if you need to build your own scorer, stick with the built-in one to begin with**.

### Building your own scorer

_This section assumes that you are using a Docker image and container for training, as outlined in the [environment](ENVIRONMENT.md) section. If you are not using the Docker image, then some of the scripts such as `generate_lm.py` will not be available in your environment._

The process for building your own scorer comprises the following steps:

1. Having, or preparing, a text file (in `.txt` or `.txt.gz` format), with one phrase or word on each line. If you are training a speech recognition model for a particular domain - such as technical words, medical transcription, agricultural terms etc, then they would appear in the text file.

2. Using the `generate_lm.py` script which is distributed with DeepSpeech, along with the text file, to create two files, called `lm.binary` and `vocab-500000.txt`.

3. Downloading the prebuilt `native_client` from the DeepSpeech repository on GitHub, and using the `generate_scorer_package` to create a `kenlm.scorer` file.

4. Using the `kenlm.scorer` file as the _external_scorer_ passed to `DeepSpeech.py` during training.

In the following example we will create a custom external scorer file for Bahasa Indonesia (BCP47: `id-ID`).

#### Preparing the text file

This is straightforward. In this example, we will use a file called `indonesian-sentences.txt`. This file has been created by copying phrases from the `train.tsv`, `validate.tsv` and `test.tsv` files in the Common Voice Indonesian dataset.

```
~/deepspeech-data$ ls cv-corpus-6.1-2020-12-11/id
total 6288
   4 drwxr-xr-x 3 root root    4096 Feb 24 19:01 ./
   4 drwxr-xr-x 4 root root    4096 Feb 11 07:09 ../
1600 drwxr-xr-x 2 root root 1638400 Feb  9 10:43 clips/
 396 -rwxr-xr-x 1 root root  401601 Feb  9 10:43 dev.tsv
 104 -rwxr-xr-x 1 root root  103332 Feb  9 10:43 invalidated.tsv
1448 -rwxr-xr-x 1 root root 1481571 Feb  9 10:43 other.tsv
  28 -rwxr-xr-x 1 root root   26394 Feb  9 10:43 reported.tsv
 392 -rwxr-xr-x 1 root root  399790 Feb  9 10:43 test.tsv
 456 -rwxr-xr-x 1 root root  465258 Feb  9 10:43 train.tsv
1848 -rwxr-xr-x 1 root root 1889606 Feb  9 10:43 validated.tsv
```

The `indonesian-sentences.txt` file is stored on the local filesystem in the `deepspeech-data` directory so that the Docker container can access it.

```
~/deepspeech-data$ ls | grep indonesian-sentences
 476 -rw-rw-r--  1 root root  483481 Feb 24 19:02 indonesian-sentences.txt
```

#### Using `generate_lm.py` to create `lm.binary` and `vocab-500000.txt` files

We then use `generate_lm.py` script that comes with DeepSpeech to create a [_trie file_](https://en.wikipedia.org/wiki/Trie). The _trie file_ represents associations between words, so that during training, words that are more closely associated together are more likely to be transcribed by DeepSpeech.

The _trie file_ is produced using a software package called [KenLM](https://kheafield.com/code/kenlm/). KenLM is designed to create large language models that are able to be filtered and queried easily.

First, create a directory in `deepspeech-data` directory to store your `lm.binary` and `vocab-500000.txt` files:

```
deepspeech-data$ mkdir indonesian-scorer
```

Then, use the `generate_lm.py` script as follows:

```
cd data/lm
python3 generate_lm.py \
  --input_txt /DeepSpeech/deepspeech-data/indonesian-sentences.txt \
  --output_dir /DeepSpeech/deepspeech-data/indonesian-scorer \
  --top_k 500000 --kenlm_bins /DeepSpeech/native_client/kenlm/build/bin/ \
  --arpa_order 5 --max_arpa_memory "85%" --arpa_prune "0|0|1" \
  --binary_a_bits 255 --binary_q_bits 8 --binary_type trie
```

_Note: the `/DeepSpeech/native_client/kenlm/build/bin/` is the path to the binary files for `kenlm`. If you are using the Docker image and container (explained on the [environment page of the PlayBook](ENVIRONMENT.md)), then `/DeepSpeech/native_client/kenlm/build/bin/` is the correct path to use. If you are not using the Docker environment, your path may vary._

You should now have a `lm.binary` and `vocab-500000.txt` file in your `indonesian-scorer` directory:

```
deepspeech-data$ ls indonesian-scorer/
total 1184
  4 drwxrwxr-x 2 root   root   4096 Feb 25 23:13 ./
  4 drwxrwxr-x 5 root   root   4096 Feb 26 09:24 ../
488 -rw-r--r-- 1 root   root      499594 Feb 24 19:05 lm.binary
 52 -rw-r--r-- 1 root   root       51178 Feb 24 19:05 vocab-500000.txt
```

#### Generating a `kenlm.scorer` file from `generate_scorer_package`

Next, we need to install the `native_client` package, which contains the `generate_scorer_package`. This is _not_ pre-built into the DeepSpeech Docker image.

The `generate_scorer_package`, once installed, is usable on all platforms supported by DeepSpeech. This is so that developers can generate scorers on-device, such as on an Android device, or Raspberry Pi 3.

To install `generate_scover_package`, first download the relevant `native client` package from the [DeepSpeech GitHub releases page](https://github.com/mozilla/DeepSpeech/releases/tag/v0.9.3) into the `data/lm` directory. The Docker image uses Ubuntu Linux, so you should use the `native_client.amd64.cuda.linux.tar.xz` package.

The easiest way to do this is to use `wget`:

```
root@dcb62aada58b:/DeepSpeech# cd data/lm
root@dcb62aada58b:/DeepSpeech/data/lm# wget https://github.com/mozilla/DeepSpeech/releases/download/v0.9.3/native_client.amd64.cuda.linux.tar.xz
--2021-02-26 21:45:10--  https://github.com/mozilla/DeepSpeech/releases/download/v0.9.3/native_client.amd64.cuda.linux.tar.xz

native_client.amd64 100%[===================>]  13.43M  11.2MB/s    in 1.2s    

2021-02-26 21:45:12 (11.2 MB/s) - ‘native_client.amd64.cuda.linux.tar.xz’ saved [14086680/14086680]
```

Next, extract the package using `tar -xvf`:

```
root@dcb62aada58b:/DeepSpeech/data/lm# tar -xvf native_client.amd64.cuda.linux.tar.xz
libdeepspeech.so
generate_scorer_package
LICENSE
deepspeech
deepspeech.h
README.mozilla
```

You can now generate a `ken.lm` scorer file.

```
root@dcb62aada58b:/DeepSpeech/data/lm# ./generate_scorer_package \
  --alphabet ../alphabet.txt  \
  --lm ../../deepspeech-data/indonesian-scorer/lm.binary
  --vocab ../../deepspeech-data/indonesian-scorer/vocab-500000.txt \
  --package kenlm-indonesian.scorer \
  --default_alpha 0.931289039105002 \
  --default_beta 1.1834137581510284
6021 unique words read from vocabulary file.
Doesn't look like a character based (Bytes Are All You Need) model.
--force_bytes_output_mode was not specified, using value infered from vocabulary contents: false
Package created in kenlm-indonesian.scorer.
```

The `kenlm-indonesian.scorer` file is stored in the `/DeepSpeech/data/lm` directory within the Docker container. Copy it to the `deepspeech-data` directory.

```
root@dcb62aada58b:/DeepSpeech/data/lm# cp kenlm-indonesian.scorer ../../deepspeech-data/indonesian-scorer/
```

```
root@dcb62aada58b:/DeepSpeech/deepspeech-data/indonesian-scorer# ls -las
total 1820
  4 drwxrwxr-x 2 1000 1000   4096 Feb 26 21:56 .
  4 drwxrwxr-x 5 1000 1000   4096 Feb 25 22:24 ..
636 -rw-r--r-- 1 root root 648000 Feb 26 21:56 kenlm-indonesian.scorer
488 -rw-r--r-- 1 root root 499594 Feb 24 08:05 lm.binary
 52 -rw-r--r-- 1 root root  51178 Feb 24 08:05 vocab-500000.txt
```

#### Using the scorer file in training

You now have your own scorer file that can be used in training using the `--scorer` parameter.

For example:

```
python3 DeepSpeech.py \
  --train_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/train.csv \
  --dev_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/dev.csv \
  --test_files deepspeech-data/cv-corpus-6.1-2020-12-11/id/clips/test.csv \
  --checkpoint_dir deepspeech-data/checkpoints-newscorer-id \
  --export_dir deepspeech-data/exported-model-newscorer-id \
  --train_cudnn true \
  --early_stop true \
  --es_epochs 30 \
  --es_min_delta 0.03 \
  --reduce_lr_on_plateau true \
  --plateau_reduction 0.2 \
  --plateau_epochs 3 \
  --n_hidden 2048 \
  --scorer deepspeech-data/indonesian-scorer/kenlm.scorer
```

For more information on scorer files, refer to the [DeepSpeech documentation](https://deepspeech.readthedocs.io/en/latest/Scorer.html).

---

[Home](README.md) | [Previous - The alphabet.txt file](ALPHABET.md) | [Next - Acoustic Model and Language Model](AM_vs_LM.md)
