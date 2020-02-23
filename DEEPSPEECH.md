# Introduction to DeepSpeech

## What does DeepSpeech do?

DeepSpeech is a tool for automatically transcribing spoken audio. DeepSpeech takes digital audio as input and returns a "most likely" text transcript of that audio.

DeepSpeech is useful in the following example applications:

- a stand-alone transcription tool
- Key Word Search in spoken audio
- an interface to a voice-controlled application

In these three examples, you can see that DeepSpeech helps automate tasks which normally take considerable human time. Accurate transcriptions require someone who has been professionally trained, and their time is expensive. High quality transcription of audio may take up to 10 hours of transcription time per one hour of audio. With DeepSpeech, you could increase transcriber productivity with a human-in-the-loop approach, in which DeepSpeech generates a first-pass transcription, and the transcriber fixes any errors.

Key Word Search in audio is a simple task, but it takes considerable time. Given a collection of 100 hours of audio, if you only want to find all the instances of the word "coronavirus", instead of paying a professional transcriber, you might just pay someone to listen to all the audio and note when the word "coronavirus" was spoken. Nevertheless, you will still be paying for human time relative to the amount of audio you wish to search. A better approach would be to modify DeepSpeech to listen better for words of interest (e.g. "coronavirus") and run DeepSpeech over all audio in parallel. After, a human may verify that the identified segments of audio contain the words of interest. This is another human-in-the-loop example, which makes the humans considerably more time-efficient.

The last example application of DeepSpeech is as an interface to a voice-controlled application. This is an instance where a successful application (e.g. a digital assistant or smart speaker) cannot contain a human-in-the-loop. As such, DeepSpeech is not making human-time more efficient, but rather, DeepSpeech is enabling technologies which were previously impossible.

## How does DeepSpeech work?

DeepSpeech takes a stream of audio as input, and converts that stream of audio into a sequence of characters in the designated alphabet. This conversion is made possible by two basic steps: First, the audio is converted into a sequence of probabilities over characters in the alphabet. Secondly, this sequence of probabilities is converted into a sequence of characters.

The first step is made possible by a Deep Neural Network, and the second step is made possible by an N-gram language model. The neural network is trained on audio and corresponding text transcripts, and the N-gram language model is trained on a text corpus (which is often different from the text transcripts of the audio). The neural model is trained to predict the text from speech, and the language model is trained to predict text from preceding text. Functionally, you can think of the first part (the acoustic model) as a phonetic transcriber, and the second part (the language model) as a spelling and grammar checker.

## How is DeepSpeech implemented?

The physical level of analysis identifies how the information processing system is physically realized (in the case of biological vision, what neural structures and neuronal activities implement the visual system). 
