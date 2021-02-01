[Home](README.md) | [Previous - Training your model](TRAINING.md) | [Next - Deploying your model](DEPLOYING.md)

# Testing your setup

## Contents

- [Testing your setup](#testing-your-setup)
  * [Contents](#contents)

Let's say you've already trained an acoustic model and a language model. Congrats! Now you want to deploy it... but wait a minute! Before you deploy, you need to know how good your setup will work for your application. We're talking about a "setup" here and a not "model" on purpose. There are multiple factors that influence the success of an application, and you need to keep all these factors in mind. The acoustic model and language model work with each other to turn speech into text, and there are lots of ways (i.e. decoding hyperparamer settings) with whcih you can combine those two models.

During acoustic model training with Tensorflow, you hopefully saw the training and validation Loss go down over time, and eventually you got scores called the "Word Error Rate" and the "Character Error Rate". These scores are the typical ones reported for speech recognition applications, but their usefulness will vary a lot for you and your application. You should never take the Word Error Rate as the final end-all and be-all metric for the performance of your system. Often it is the case that the data in your test CSV file is not similar to the data your model will see when it is deployed, and it is the performance on data at runtime that is most important to the success of your application.

Remember, the acoustic model and language model work together to produce your transcript. You might have an acoustic model that seems to perform abysmally, but if you combine it with the right language model, you experience amazing near-perfect accuracy. How is this possible?

[Home](README.md) | [Previous - Training your model](TRAINING.md) | [Next - Deploying your model](DEPLOYING.md)
