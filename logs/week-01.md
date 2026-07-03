**Student:** Brian Tekmen
**Mentor:** Dr. Kyla McMullen

# Week 1

**Dates:** 06-08 to 06-14

## Goals
- Meet with Dr. McMullen and Thien Nguyen, the PhD student overseeing the project: enhancing synthetic audio detection by utilizing phoneme-level features. We will discuss the project scope, my background knowledge, and expectations.
- Pick two papers from the reading list compiled by the PhD student. I will read the two survey/background papers: [Muller 2024](https://arxiv.org/abs/2406.03512) and [Bhagtani 2024](https://dl.acm.org/doi/10.1145/3658664.3659658).
- Flesh out the PhD student's hypothesis that detection at the phoneme-level rather than the utterance level may be a useful direction.


## Approach and Implementation
On Monday, 06-08, I had a brief meeting with Dr. McMullen and Thien. We introduced ourselves, I chose the project I was interested in, and they thoroughly explored my background knowledge to ensure there was strong alignment for the project. I mentioned my previous experience with research and publishing, my domain-related knowledge, e.g. Transformer architecture, optimal transport/Wasserstein distance, signal-processing theory, and coding proficiency. They decided I was a strong fit, so Thien shared with me the codebase and the folder of papers he has read. 

On Tuesday, 06-09, I met with the rest of the SoundPad Lab under Dr. McMullen and we introduced ourselves. We talked about our projects and socialized, they familiarized me with the building and the resources I would have access to.

On Wednesday, 06-10, I spent the day reading the two papers I chose and taking notes. Both papers argue that detectors overfit to the specific generators in their training dataset and collapse on unseen ones. Muller decomposed performance degradation into an intrinsic "difficulty" component and an OOD component, called the "Difference Gap". They showed that the SOTA detection methods at the time showed statistically significant increases in performance degradation attributable to the OOD component, and hypothesize that the poor generalization is due to overfit. Bhagtani use the Equal Error Rate (EER) as their performance degradation metric. 5 out of 6 of the detection methods evaluated in the paper suffered 30-50% performance degradation on OOD samples, while one, *PS3DT*, only degraded by 11.45%. I started writing a report on what I had learned for the next day. 

On Thursday, 06-11, I shared my progress report with the team. They agreed with my summarization and assessment of the landscape of the problem. Their hypothesis is that phonemes, specifically plosives, contain fine-grained phase information that modern generative text-to-speech models smooth over. They said that the current goal for me is to see if this hypothesis is even true of current TTS models, not to try to tackle the generalization problem immediately. 

On Friday, I looked into the existing data pipeline that Thien was using. The papers I read used a different pipeline, and after communicating with him, he agreed that for my current task, using the Librispeech dataset, which has real audio and accompanying text, would be superior even if it is older, because it allows me to use the Montreal Forced Aligner, a more precise phone-segmentation tool that relies on accompanying text to make precise boundaries for phonemes. I started researching the Montreal Forced Aligner.


## Results
- My understanding of the general landscape and the current project scope is clear now. My team seems impressed and satisfied with the pace and quality of my work so far.
- Everyone here seems passionate, bright, and friendly. I am excited to continue my work here.
- I have concrete next steps: study the Montreal Forced Aligner, the TTS pipeline, and the methodology for determining whether phonemes are salient features.
- Environment is not set up yet: see notes.


## Notes
- I was not able to interact with the code base yet because I still did not have access to the University super computer, which was necessary for the amount of compute we needed.


