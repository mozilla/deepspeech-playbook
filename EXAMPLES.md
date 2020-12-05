# Example applications of DeepSpeech

The pssibilities are endless, but here's some concrete examples of how you might integrate Speech-to-Text with DeepSpeech into your application.

## A stand-alone transcription tool

Accurate transcriptions require someone who has been professionally trained, and their time is expensive. High quality transcription of audio may take up to 10 hours of transcription time per one hour of audio. With DeepSpeech, you could increase transcriber productivity with a human-in-the-loop approach, in which DeepSpeech generates a first-pass transcription, and the transcriber fixes any errors.

## Key Word Search in spoken audio

Key Word Search in audio is a simple task, but it takes considerable time. Given a collection of 100 hours of audio, if you only want to find all the instances of the word "coronavirus", instead of paying a professional transcriber, you might just pay someone to listen to all the audio and note when the word "coronavirus" was spoken. Nevertheless, you will still be paying for human time relative to the amount of audio you wish to search. A better approach would be to modify DeepSpeech to listen better for words of interest (e.g. "coronavirus") and run DeepSpeech over all audio in parallel. After, a human may verify that the identified segments of audio contain the words of interest. This is another human-in-the-loop example, which makes the humans considerably more time-efficient.

## An interface to a voice-controlled application

The last example application of DeepSpeech is as an interface to a voice-controlled application. This is an instance where a successful application (e.g. a digital assistant or smart speaker) cannot contain a human-in-the-loop. As such, DeepSpeech is not making human-time more efficient, but rather, DeepSpeech is enabling technologies which were previously impossible.
