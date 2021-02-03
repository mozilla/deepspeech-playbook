# Notes from chatting with Alexandre Lissy 03 Feb 2021

## Should we move the repo somewhere else under DeepSpeech?

That's a good question for Josh - DS may go into another organisational repo

## Testing and evaluation

* We should expect people to have little ML experience when they're testing and evaluating. We can't teach them ML. This is a crash course rather than an ML course.

ACTION: We should link out to other ML courses that provide an introduction.

* See how loss evolves. Training and validation loss should decrease faster at start. The loss curve should be 1/e - include a graph of this. Stop training if the loss increases. This requires trial and error.

* DeepSpeech has some mechanisms built in that try to automatically analyse loss. Reduce the learning rate - if it plateaus it will decrease the learning rate to learn more. This is not well exposed and should be more exposed in the documentation

ACTION: Expose the learning rate in the training and evaluation section

* The test set needs to be as close as possible to the runtime data - the type of speech that will be encountered during runtime. There is a **distinction between WER and CER**. Observed high WER and low CER means that the scorer, the language model is not as good, and you should improve the scorer. CER works _phonetically_ on the test set. This is a hint. WER - word, CER - character. Language model will prioritise words - homonyms. Wrong word, close characters.

ACTION: Provide heuristics for understanding where to improve the scorer.

## Transfer learning and fine tuning

* Dataset size. Format dataset section. Test set needs at least hundreds of hours to get relevant results. Not enough data, do **transfer learning** from another model.

ACTION: Check with Fran Tyers Might be interested in low resource languages.

ACTION: Should we cover transfer learning? Recurring question on Discourse.

Not really a creash course, but add it in. Make the distinction between transfer learning and fine tuning. Drop alphabet layer - different alphabet is transfer learning, otherwise fine tuning.

This should only be used in you have a small voice dataset.

## Don't use sudo

This is really important

## Magic flag for GPU mem growth

Runtime - If python allocates as much RAM - magic flag - don't allocate all at once. Limited memory, smaller batch size. Generates fragmentation so is slower. But, it gets low-memory GPUs to work.

## Dockerfile french model

Meetup in Paris - stimulate community on French. 50 people registered, 35 attended. _Blocked by training_. Dockerfile for French. Proof of concept - address use case and provide for community re-use. French model served its purpose.

Dockerfile is written mostly in `bash` - is it worth rewriting in Python?

(not at this stage)

ACTION: making the interface easier to use would be a good feature, but not urgent at this stage

## Importers

Importers are shared across languages. Start importers, version, license. Exports `$ENV` vars due to using `bash`.

Need to validate that datasets are cleaned before training. May need to be fixed at runtime - this is normal

ACTION: Think about what we need to do to fix data at runtime. What is involved?

To update importers for a new language, you will need to fork repo, add support for a new language. Languages will have language specific parameters. Only takes a few hours to set up.

Wants to advocate reusing Dockerfile. Provides visibility to the code base. Use it, fix it, hack it, share it.

Commercial providers are interested in Docker training as a service.

## Deployment

* .pb file at the end of training is for inference. It is not memory mappable. It uses the protocol buffer file format.

ACTION: Provide overview of the file format, provide a description of the file format.

https://en.wikipedia.org/wiki/Protocol_Buffers

The drawback of protobuf is that creates an export very large file.

Memory mappable files - mmap - are read as a projection from memory. They are read from disk into memory - this reduces I/O - much less memory allocation used for inference.

mmap - reduces memory utilisation while doing inference on the trained model.

`graphdef` is a memory mapped format (this is deprecated in TF 2.x)

Explain the pipeline - you can live with .pb if you want to use the model to do training on a GPU with a lot of memory, but it's not useful for embedded applications.

.pbmm from graphdef was available before tflite - but we should be advocating for the use of tflite.

Export using tflite.

When training, re-export frome chkpoint (train.sh) - file is big.

L46 of train.sh contains the code

Reuse training.

No dev or test files, then it will perform training. Otherwise it will reload from the checkpoint and perform an export.

Zip file was a firefox requirement.

tflite - targeting Android and IoOS. Now every platform.

Should we ditch old Tensorflow .pb file and show only tflite?

## ARM v7 ruy problem

Magic flag in codebase - the real time transcription problem - nodejs on ARM v7
ARM assembly code. nodejs v10 was OK, broke in nodejs 11. Each nodejs uses a new js fom Google, a new version. ARM neon registers. Registers unexpected values in computation. No one else using registers. Not cleaned by the process, not context switching. French play on words - q7 and aud de gas

### Future for tflite

In the future, want to ditch old TF runtime. TFLite can do more than real time - no advantage in having the TensorFlow runtime loaded. TF removed graphdef in 2.x. Still using 1.15 because of graphdef.

We want to promote the use of tflite as that is the direction it will be heading in the future. It's easier for deployment.

There are a lot of packages for DeepSpeech - on PyPI and many other package libraries. Need to streamline these in the future.

PyPI - multiple architecutre. No Arch - API reasons.

Others are working on packaging for .NET and .NET core.
bintray package for Android

Android is a good first example for deployment.

ACTION: Consider Android as a good example for explaining deployment.

## DeepSpeech Examples

* People don't read the document as it's currently written - we need to think about how to make it engaging, or making the documentation something that people _want_ to read.

* We don't know whether people are spending a lot of time on documentation, and then we don't hear from them again because they've got the outcome that they wanted - ie their issue or problem is resolved.

ACTION: Think about how we get feedback on the Playbook? Does it have upvotes or downvotes? How do we know if it's been helpful? Is there a `jekyll` template or theme that would give us this feedback?

The examples should be a quickstart guide

Example repo - https://github.com/mozilla/DeepSpeech-examples/

ACTION: Refer out to more examples

WebThings addon - except WebThings has now been spun out into their own entity.

local language model - it's a useful case study of a real world example of DeepSpeech. 
