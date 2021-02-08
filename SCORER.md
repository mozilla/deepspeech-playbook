[Home](README.md) | [Previous - The alphabet.txt file](ALPHABET.md) | [Next - Acoustic Model and Language Model](AM_vs_LM.md)

# Scorer - language model for determining which words occur together

## Contents

- [Scorer - language model for determining which words occur together](#scorer---language-model-for-determining-which-words-occur-together)
  * [Contents](#contents)
    + [What is a scorer?](#what-is-a-scorer-)
    + [Building your own scorer](#building-your-own-scorer)

### What is a scorer?

A scorer is a _language model_ and it is used by DeepSpeech to improve the accuracy of transcription. A _language model_ predicts which words are more likely to follow each other. For example, the word `chicken` might be frequently followed by the words `nuggets`, `soup` or `rissoles`, but is unlikely to be followed by the word `purple`. The scorer identifies probabilities of words occurring together.

The default scorer used by DeepSpeech is trained on the LibriSpeech dataset. The LibriSpeech dataset is based on [LibriVox](https://librivox.org/) - an open collection of out-of-copyright and public domain works.

You may need to build your own scorer - your own _language model_ if:

* You are training DeepSpeech in another language
* You are training a speech recognition model for a particular domain - such as technical words, medical transcription, agricultural terms and so on
* If you want to improve the accuracy of transcription

DeepSpeech supports the _optional_ use of an external scorer - if you're not sure if you need to build your own scorer, stick with the built-in one to begin with.

### Building your own scorer

Building your own scorer is beyond the scope of the DeepSpeech Playbook, but the [DeepSpeech documentation covers how to do this](https://deepspeech.readthedocs.io/en/latest/Scorer.html). There are built-in scripts with DeepSpeech which make building an external scorer - a _language model_ - easier.

---

[Home](README.md) | [Previous - The alphabet.txt file](ALPHABET.md) | [Next - Acoustic Model and Language Model](AM_vs_LM.md)
