# Hurdles that this Playbook is intended to overcome 

## High level objectives 

This document is intended to be be a mechanism for _knowledge transfer_ of DeepSpeech. It is intended as a _quick start guide_; for people who have some background in automatic speech recognition and/or deep learning, but who may not have used DeepSpeech specifically. The [Deep Speech documentation](https://deepspeech.readthedocs.io) is a companion piece to the DeepSpeech Playbook. 

This document is focussed on the setting up and use of DeepSpeech, rather than on other processes in automatic speech reocgnition, such as data collection. It assumes that the developers using this documentation are ready to train their own models, rather than use pre-trained models. 

## Intent of this specific file 

The intent of this file is to: 

* document _hurdles_ that developers using DeepSpeech frequently encounter
* suggest _tutorials_ to include into the Playbook  
* and identify how thos tutorials _sequence_ or dovetail in increasingly more advanced ways

## Intended benefits of the DeepSpeech Playbook 

It is intended that the DeepSpeech Playbook will: 

* reduce the time it takes for developers to become fluent in using DeepSpeech
* increase DeepSpeech's value by making "time to first hellow world" shorter
* add value to open source voice in general by making open source voice technologies easier to use

## Hurdles to be overcome 

### Setting up dependencies on a localhost

#### Problem outline 

Installing dependencies on a `localhost` in order to run training is difficult, requires a lot of effort and dependencies frequently change 

#### Tutorial to address problem 

Tutorial on setting up `docker` image with dependencies already loaded. Given the large amount of data often used for training, this tutorial would need to document how to attach storage to `docker` - that storage is likely to be local. 

#### Assumptions in this tutorial 

* User is using `localhost`
* User is using local storage 
* User may or may not have a GPU available - tutorial may need to cover both options 
* User may not be familiar with Docker 

### Running DeepSpeech on a cloud instance 

#### Problem outline 

Setting up DeepSpeech to run on cloud infrastructure such as AWS, GCP, Azure etc is not well documented. This is a next logical step after setting up DeepSpeech on a `localhost`. 

#### Tutorial to address problem 

Tutorial on setting up `docker` image to run on cloud infrastructure. Will need to include caveats on runaway costs that can be incurred in doing this, especially around storage. 

> Is there partnership opportunity here with a cloud provider - ie if we provide instructions for a particular platform, will they provide a benefit in some way? Possibly don't want to align with a cloud provider given the open source nature of DeepSpeech. 

#### Assumptions in this tutorial 

* User wants to move beyond `localhost`
* User is using cloud storage
* User is using cloud compute with GPU or possibly TPU capabilities 
* User will be familiar with Docker from the previous tutorial 

### Working with checkpoints 

> I don't know how checkpoints work well enough to flesh out tutorial ideas 

### Working with hyperparameters 

> I don't know how hyperparamters work in DeepSpeech well enough to flesh out tutorial ideas 

### Exporting a model for embedded applications with TFlite 

#### Problem outline 

A key use case for DeepSpeech is to use it in embedded inference applications, for example on embedded hardware. The key way to do this is to export using `TFLite` but this can be tricky _I don't know exactly the trickiness involved_ 
 
#### Tutorial to address problem 

Using the model(s) trained in previous tutorials, export them to `TFlite` and then use some sort of embedded hardware (possibly an RPi) to demonstrate how to use `TFLite` for inference. 

### Augmentations 

> I don't know enough about augmentations to be able to offer useful feedback here

### External scorer and languages models 

> I don't know enough about the External Scorer and langauge models to offer useful feedback here 




