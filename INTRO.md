
## Is this guide for you?

If you're trying to get DeepSpeech working for your application, your data, or a new language, you've come to the right place! You can easily download a pre-trained DeepSpeech model for English, but it might not work for you out of the box. No worries, with a little tweaking you can get DeepSpeech working for most anything! Specilifcally, this guide will help you create a working DeepSpeech model for a new language. Along the way, you will learn some best-practices for speech recognition and data wrangling.

## Setting Expectations

You might think that speech recognition is solved for English, and as such, with a little work you can solve speech recognition for a new language. This is false for two reasons. Firstly, speech recognition is far from solved for English, and secondly it is unlikely you will be able to create something that works as well as a pre-trained English DeepSpeech model unless you have a few thousand hours of data.

Nevertheless, with some tips and tricks, you can still make create useful voice technology for a non-English language!

## Setting up for Success

The success of any voice technology depends on a constellation of factors, and the accuracy of your speech recognizer is just one factor. To the extent that an existing voice technology works, it works because the creators have eliminated sources of failure. Think about one of the oldest working voice technologies: spoken digit recognition. When you call a bank you might hear a recording like this: "Say ONE to learn about credit cards, say TWO to learn about debit cards, or say ZERO to speak to a representative". These systems usually work well, but you might not know that if you answer with anything other than a single digit, the system will completely fail to understand you. Spoken digit recognition systems are setup for success because they've re-formulated an open-ended transcription problem as a simple classification problem. In this case, as long as the system is able to distinguish spoken digits from one-another, it will succeed.

We will talk about ways in which you can constrain the search space of a problem and bias a model towards a set of words that you actually care about. If you want to make a useful digit recognizer, it doesn't matter if your model has an 85% Word Error Rate when transcribing the nightly news. All that matters is your model can correctly identify spoken digits. It is key to align what you care about with what you are measuring.

If you have ever used a speech technology and it worked flawlessly, the creators of the product set themselves up for success. This is what you must also do in your application.

