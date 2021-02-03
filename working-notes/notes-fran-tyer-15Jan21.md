# Notes from discussion w Fran Tyers 15 January 2021

* Make sure instructions are provided for not having root on machines. `sudo` messes with versions. Students may be training on someone else's computer. May not be training on your mchine.

* Need to try instructions on different servers, see what fails. People work in many different environments, what you need to do for fine tuning, data, training etc.

_Need to try on lots of different machines to capture the variability_

ACTION: Need to add a section in for "how you can help make the PlayBook better"

* Find out what people are doing with DeepSpeech, and what they _want_ to do, and then optimise for this.

* Training for limited vocabulary is not explained - this needs to be explained better.

ACTION: Explain limited vocabulary better - I think is done through a scorer / language model.

* Pointers on how to integrate (? will this be in the Examples section?) Use bindings - do we need a deep dive on bindings? Some bindings are not officially supported. Want to limit bindings because it makes PRs hard - expose all bindings

* Case Studies - Te Hiku Media - good case study / example

ACTION: they might want to provide feedback on the PlayBook, get in contact with them

* CUDA / cuDNN is a huge time sink for people - but this is beyond the scope of the Playbook which is DeepSpeech specifically. Getting GPU / CUDA / Tensorflow right is 90% of the battle, then training generally works.

* How to clean data - expectations - if x, then do this.

ACTION: Need to solicit more of this during the alpha / feedback stage.

* Polishing models rather than training from scratch

* Adapt model for use. Fine tuning on accent or voice.

* Training new language - ie Lissy's work with French

* Agree with cutting off non-GPU training - this takes too long and is not feasible - the PlayBook shouldn't cover it, it's not valuable.

NEXT ACTION: Send out to students and stress test it.
