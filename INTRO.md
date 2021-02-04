[Home](README.md) | [Next - About DeepSpeech](DEEPSPEECH.md)

# Introduction

## Contents

- [Introduction](#introduction)
  * [Contents](#contents)
  * [Is this guide for you?](#is-this-guide-for-you-)
  * [Setting expectations](#setting-expectations)
  * [Setting up for success](#setting-up-for-success)

## Is this guide for you?

You're probably here because you're interested in automatic speech recognition (ASR) - the process of converting phrases spoken by humans into written form. There have been significant advances in speech recognition in recent years, driven both by new deep learning algorithms, and by advances in hardware that are capable of the large volume of computations required by those algorithms. Several new tools are available to assist developers with both training speech recognition models and using those models for inference - DeepSpeech being one of them.

If you're trying to get DeepSpeech working for your application, your data, or a new language, you've come to the right place! You can easily download a pre-trained DeepSpeech model for English, but it might not work for you out of the box. No worries, with a little tweaking you can get DeepSpeech working for most anything!

Specifically, this guide will help you create a working DeepSpeech model for a new language. Along the way, you will learn some best practices for speech recognition and data wrangling.

## Setting expectations

You might think that speech recognition is solved for English, and as such, with a little work you can solve speech recognition for a new language. This is false for two reasons. Firstly, speech recognition is far from solved for English, and secondly it is unlikely you will be able to create something that works as well as a pre-trained English DeepSpeech model unless you have a few thousand hours of data.

Nevertheless, with some tips and tricks, you can still make create useful voice technology for a non-English language!

## Setting up for success

Speech recognition is a _statistical_ process. Speech recognition models are _trained_ on large amounts of voice data, using statistical techniques to "learn" associations between sounds, in the form of `.wav` files, and characters, that are found in an alphabet. Because speech recognition is statistical, it does not have "bugs" in the sense that computer code has bugs; instead, anomalies or biases in the data used for a speech recognition model mean that the resulting model will likely exhibit those biases.

Speech recognition still requires trial and error - with the data that is used to train a model, the language model or scorer that is used to form words from characters, and with specific training settings. "Debugging" speech recognition models means findings ways to make the data, the alphabet and and scorer more _accurate_. That is, making them mirror as closely as possible the real-world conditions in which the speech recognition model will be used. If your speech recognition model will be used to help transcribe geographic place names, then your voice data and your scorer need to cover those place names.

The success of any voice technology depends on a constellation of factors, and the accuracy of your speech recognizer is just one factor. To the extent that an existing voice technology works, it works because the creators have eliminated sources of failure. Think about one of the oldest working voice technologies: spoken digit recognition. When you call a bank you might hear a recording like this: "Say ONE to learn about credit cards, say TWO to learn about debit cards, or say ZERO to speak to a representative". These systems usually work well, but you might not know that if you answer with anything other than a single digit, the system will completely fail to understand you. Spoken digit recognition systems are setup for success because they've re-formulated an open-ended transcription problem as a simple classification problem. In this case, as long as the system is able to distinguish spoken digits from one another, it will succeed.

We will talk about ways in which you can constrain the search space of a problem and bias a model towards a set of words that you actually care about. If you want to make a useful digit recognizer, it doesn't matter if your model has an 85% Word Error Rate (WER) when transcribing the nightly news. All that matters is your model can correctly identify spoken digits. It is key to align what you care about with what you are measuring.

---

@todo I don't have an example here of how to constrain the search space and bias a model towards a particular set of words - is that where the language model comes in? Should we have a page called `LANGUAGE_MODEL.md` or similar?

---

If you have ever used a speech technology and it worked flawlessly, the creators of the product set themselves up for success. This is what you must also do in your application.

---

@todo I feel like what this section needs is a _checklist_ or similar on all the things that somebody has to do to set themselves up for success. Is this something that you think I should build out?

---

[Home](README.md) | [Next - About DeepSpeech](DEEPSPEECH.md)
