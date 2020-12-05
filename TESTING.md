# Testing your setup

Let's say you've already trained an acoustic model and a language model. Congrats! Now you want to deploy it... but wait a minute! Before you deploy, you need to know how good your setup will work for your application. It's no mistake I'm saying "setup" here and not "model". There are multiple factors that influence the success of a project, and you need to keep all these factors in mind.

During acoustic model training with Tensorflow, you hopefully saw the Loss go down over time, and eventually you got a score called the "Word Error Rate". This score is the typical one reported for speech recognition applications, but its usefulness will vary a lot for you and your application. You cannot just take the Word Error Rate as the final metric for performance of your system.

Remember, the acoustic model and language model work together to produce your transcript. You might have an acoustic model that seems to perform abysmally, but if you combine it with the right language model, you experience amazing near-perfect accuracy. How is this possible?