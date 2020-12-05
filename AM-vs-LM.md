# Acoustic model vs. Language model

At runtime, DeepSpeech is made up of two main parts: (1) the acoustic model and (2) the language model. The acoustic model takes audio as input and converts it to a probability over characters in the alphabet. The language model helps to turn these probabilities into words of coherent language. The language model (aka. the scorer), assigns probabilities to words and phrases based on statistics from training data. The language model knows that "I read a book" is much more probable then "I red a book", even though they may sound identical to the acoustic model.

## Training

The acoustic model is a neural network trained with Tensorflow, and the training data is a corpus of speech and transcripts.

The language model is a n-gram model trained with kenlm, and the training data is a corpus of text.
