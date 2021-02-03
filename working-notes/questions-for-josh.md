# Questions for Kathy to work through with Josh

## Flagging @todo in the repo

I've added a bunch of questions with the @todo flag in the repo, both so it's easy to find where I've flagged that I need to come back to a section, and so I can double check they're resolved before we publish the Playbook.

## Introduction to speech recognition

Do we think we still need a section on "What is speech recognition?" in the [INTRO.md](INTRO.md) file?

## Do we need a checklist of the things that help to set us up for success?

Do you think we should include a checklist into the [INTRO.md](INTRO.md) file?

## Should we have a ToC on each page?

Some of the sections like ENVIRONMENT.md are quite long. My recommendation would be to put in a ToC on each page.

## Collecting data - do we need a section on collecting data for training

It feels like with the way the content is laid out that having a section on _collecting data_ would be worthwhile. This might just link to the CV Playbook. This section would sit before [DATA_FORMATTING.md](DATA_FORMATTING.md).

## Data formatting section

Are there any other common errors with data formatting that should be covered here?

## Environment section

Do the instructions that are currently in the training section around setting up persistent storage on Docker through a Docker volume sit better in the ENVIRONMENT.md section?

## Training section

* I think I need to include more examples of the options that can be used with `DeepSpeech.py`. Can we chat about which ones are important?

* I don't think that the Docker container I'm using is _actually using the GPU, but is training on the CPU instead_. The reason I think this is that I installed `nvtop` to monitor the usage of my GPU, and it's at about 20% of utilisation. Training a model the GPU should be nearly at 100% utilisation. I I run `htop` then my 8 x CPUs are pretty much tied up with a `DeepSpeech.py` process. So I think it's training on the CPUs, not the GPU.

* Not sure how to force Docker to train with a GPU. Had a look at the `Dockerfile` and it states:

```
# Install DeepSpeech
#  - No need for the decoder since we did it earlier
#  - There is already correct TensorFlow GPU installed on the base image,
#    we don't want to break that
RUN DS_NODECODER=y DS_NOTENSORFLOW=y pip3 install --upgrade -e .
```

so I think it's expecting Tensorflow to already be there. But I _don't think the Dockerfile is handling `CUDA` deps properly so I did a bit of digging around on blogs, such as this one:

https://blog.roboflow.com/use-the-gpu-in-docker/

```
FROM nvidia/cuda:10.2-base
CMD nvidia-smi
```

## Testing section

Is there anything I can be doing to help with this section?

## Deployment section

Is there anything I can be doing to help with this section?

## Examples section

Is there anything I can be doing to help with this section?
