[Home](README.md) | [Previous - Training your model](TRAINING.md) | [Next - Deploying your model](DEPLOYING.md)

# Testing and evaluating your trained model

## Contents

- [Testing and evaluating your trained model](#testing-and-evaluating-your-trained-model)
  * [Contents](#contents)
  * [Word Error Rate, Character Error Rate, loss and model performance](#word-error-rate--character-error-rate--loss-and-model-performance)
  * [Acoustic model and language model working together](#acoustic-model-and-language-model-working-together)
  * [Heuristics](#heuristics)
  * [Fine tuning and transfer learning](#fine-tuning-and-transfer-learning)

Let's say that you've already trained an acoustic model and a language model (a [scorer](SCORER.md)). Congratulations! But before you [deploy](DEPLOYMENT.md) your setup, you will need to evaluate how well it will work in practice - on your intended use case.

We're talking here about a _setup_ rather than a trained _model_ on purpose - as there are multiple factors that influence how well a _setup_ performs in real life. There are multiple factors that influence the success of an application, and you need to keep all these factors in mind. The acoustic model and language model work with each other to turn speech into text, and there are lots of ways (i.e. decoding hyperparameter settings) with which you can combine those two models.

## Word Error Rate, Character Error Rate, loss and model performance

During acoustic model [training](TRAINING.md) with Tensorflow, you hopefully saw the training and validation _loss_ go down over time. At the end of the training, DeepSpeech would have printed scores for your model called the _Word Error Rate (WER)_ and _Character Error Rate (CER)_.

The WER is how accurately DeepSpeech was able to recognise a _word_, and is generally a measure of how well the language model (scorer) is operating. The CER is how accurately DeepSpeech was able to recognise a _character_, and is generally a measure of how well the acoustic model is operating, along with an [alphabet](ALPHABET.md) file.

WER and CER are the typical scores reported for speech recognition models, but their usefulness will vary a lot, depending on the use case of your _setup_. You should not take the WER as the "be-all" metric for the performance of your _setup_.

Often, the data in your _test_ `.csv` file will be different to the data your model will be asked to perform inference on when it is deployed. It is the performance of your _setup_ at runtime - in a real life context - that is most important.

## Acoustic model and language model working together

Remember, the acoustic model and language model work together to produce your transcript. You might have an acoustic model that seems to perform abysmally, but if you combine it with the right language model, you experience amazing near-perfect accuracy. How is this possible?

The _acoustic model_ is where the majority of training time is spent. The job of the _acoustic model_ is to use the DeepSpeech algorithm - a _sequence to sequence_ algorithm, to learn which acoustic signals correspond to which _letters_ (as specified in the `alphabet.txt` file). This accuracy is the _character error rate (CER)_.

In many languages though, words that sound the same are spelled differently. These are called [homonyms](https://en.wikipedia.org/wiki/Homonym). For example, the words `their`, `they're` and `there` are all pronounced similarly in English, but are spelled differently.

The _language model_ seeks to overcome this challenge. The _language model_, produced by a [scorer](SCORER.md), predicts which words will follow each other in a sequence. This is also known in linguistics as [n-gram modelling](https://en.wikipedia.org/wiki/N-gram). For example, the words `nugget`, `wings` and `salad` are more likely to occur after the word `chicken` than say `ticket`, even though the words `chicken` and `ticket` have similar sounds.

The _acoustic model_ and the _language model_ work together to provide better overall accuracy.

## Heuristics

In general, if you have a low CER - that is, your _characters_ are being detected accurately in your _acoustic model_, but you have a high WER - that is, the _words_ are not being detected accurately, this indicates that you should retrain your _language model_ ([scorer](SCORER.md)).

Conversely, if you have a high CER, and a low WER, this indicates that your _acoustic model_ may require fine-tuning.

## Fine tuning and transfer learning

_Fine tuning_ and _transfer learning_ are two processes used to improve the accuracy of an _acoustic model_. _Fine tuning_ is where the same [alphabet.txt](ALPHABET.md) file is used, with a set of _checkpoints_ from another model. In _transfer learning_, the alphabet layer is removed from the neural network, and this allows a model to be trained on a model from another language. In general, this works best on languages that have a similar vocabulary and/or structure. For example, English and French will work better than English and Hindi given that English and French are more similar than English and Hindi.

For more information on [fine tuning in DeepSpeech, please consult the documentation](https://deepspeech.readthedocs.io/en/v0.9.3/TRAINING.html#fine-tuning-same-alphabet).

For more information on [transfer learning in DeepSpeech, please consult the documentation](https://deepspeech.readthedocs.io/en/v0.9.3/TRAINING.html#transfer-learning-new-alphabet).  

---

[Home](README.md) | [Previous - Training your model](TRAINING.md) | [Next - Deploying your model](DEPLOYING.md)
