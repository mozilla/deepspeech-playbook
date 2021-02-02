# DeepSpeech Playbook

A crash course on training speech recognition models using DeepSpeech.

## Quick links

* [DeepSpeech on GitHub](https://github.com/mozilla/DeepSpeech)
* [DeepSpeech documentation on ReadTheDocs](https://deepspeech.readthedocs.io/en/latest/)
* [DeepSpeech discussions on Mozilla's Discourse forum](https://discourse.mozilla.org/c/deepspeech/247)
* [Common Voice Datasets](https://commonvoice.mozilla.org/en/datasets)
* [How to install Docker](https://docs.docker.com/engine/install/)

## [Introduction](INTRO.md)

Start here. This section will set your expectations for what you can achieve with the DeepSpeech Playbook, and the prerequisites you'll need to start to train your own speech recognition models.

## [About DeepSpeech](DEEPSPEECH.md)

Once you know what you can achieve with the DeepSpeech Playbook, this section provides an overview of DeepSpeech itself, its component parts, and how it differs from other speech recognition engines you may have used in the past.

## [Formatting your training data](DATA_FORMATTING.md)

Before you can train a model, you will need to collect and format your _corpus_ of data. This section provides an overview of the data format required for DeepSpeech, and walks through an example in prepping a dataset from Common Voice.

## [The alphabet.txt file](ALPHABET.md)

If you are training a model that uses a different alphabet to English, for example a language with diacritical marks, then you will need to modify the `alphabet.txt` file.

## [Building your own scorer](SCORER.md)

Learn what the scorer does, and how you can go about building your own. 

## [Acoustic model and language model](AM_vs_LM.md)

Learn about the differences between DeepSpeech's _acoustic_ model and _language_ model and how they combine to provide end to end speech recognition.

## [Setting up your training environment](ENVIRONMENT.md)

This section walks you through building a Docker image, and spawning DeepSpeech in a Docker container with persistent storage. This approach avoids the complexities of dependencies such as `tensorflow`.

## [Training a model](TRAINING.md)

Once you have your training data formatted, and your training environment established, this section will show you how to train a model, and provide guidance for overcoming common pitfalls.

## [Testing a model](TESTING.md)

Once you've trained a model, you will need to validate that it works for the context it's been designed for. This section walks you through this process.

## [Deploying your model](DEPLOYMENT.md)

Once trained and tested, your model is deployed. This section provides an overview of how you can deploy your model.

## [Applying DeepSpeech to real world problems](EXAMPLES.md)

This section covers specific use cases where DeepSpeech can be applied to real world problems, such as transcription, keyword searching and voice controlled applications.
