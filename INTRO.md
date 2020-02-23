
## Is this guide for you?

If you're trying to get DeepSpeech working for a new language, you've come to the right place! You can easily download a pre-trained DeepSpeech model for English, but it will not work for another language (even with the same alphabet). This guide will help you create a working DeepSpeech model for a new language. Along the way, you will learn some best-practices for Speech Recognition and for creating usable voice techonlogy.

## Setting Expectations

You might think that Speech Recognition is solved for English, and as such, with a little work you can solve Speech Recognition for a new language. This is false for two reasons. First, Speech Recognition is far from solved for English, and second it is unlikely you will be able to create something that works as well as a pre-trained English DeepSpeech model. However, you can still make create useful voice technology for a non-English language!

## Setting up for Success

The success of any voice technology depends on a constellation of factors, and the accuracy of your speech recognizer is just one factor. To the extent that an existing voice technology works, it works because the creators have eliminated sources of failure. Think about one of the oldest working voice technologies: spoken digit recognition. When you call a bank you might hear a recording like this: "Say ONE to learn about credit cards, say TWO to learn about debit cards, or say ZERO to speak to a representative". These systems usually work well, but you might not know that if you answer with anything other than a number, the system will completely fail to understand you. Spoken digit recognition systems are setup for success because they've re-formulated an open-ended transcription problem as a simple classification problem. In this case, as long as the system is able to distinguish spoken digits from one-another, it will succeed. We will talk about ways in which you can constrain the search space of a problem and bias a model towards a set of words that you actually care about. If you want to make a useful digit recognizer, it doesn't matter if your model has an 85% Word Error Rate when transcribing the nightly news. All that matters is your model can correctly identify spoken digits.

If you have ever used a speech technology and it worked flawlessly, the creators of the product set themselves up for success. This is what you must also do in your application.

