# Introduction to DeepSpeech

## What does DeepSpeech do?

DeepSpeech is a tool for automatically transcribing spoken audio. DeepSpeech takes digital audio as input and returns a "most likely" text transcript of that audio.

## How does DeepSpeech work?

DeepSpeech takes a stream of audio as input, and converts that stream of audio into a sequence of characters in the designated alphabet. This conversion is made possible by two basic steps: First, the audio is converted into a sequence of probabilities over characters in the alphabet. Secondly, this sequence of probabilities is converted into a sequence of characters.

The first step is made possible by a Deep Neural Network, and the second step is made possible by an N-gram language model. The neural network is trained on audio and corresponding text transcripts, and the N-gram language model is trained on a text corpus (which is often different from the text transcripts of the audio). The neural model is trained to predict the text from speech, and the language model is trained to predict text from preceding text. At a very high level, you can think of the first part (the acoustic model) as a phonetic transcriber, and the second part (the language model) as a spelling and grammar checker.

## How is DeepSpeech implemented?

The core of DeepSpeech is written in C++, but it has bindings to Python, .NET, Java, JavaScript, and community-based bindings for Golang, Rust, Vlang, and NIM-lang.