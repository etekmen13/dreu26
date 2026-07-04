**Student:** Brian Tekmen
**Mentor:** Dr. Kyla McMullen

# Week 3

**Dates:** 06-22 to 06-28
## Goals
- Stand up MFA correctly on HiPerGator and get it aligning real audio.
- Build the phoneme-extraction pipeline, use MFA alignments to isolate plosives from LibriSpeech.
- Get the synthetic/TTS side working so the exact same extraction can be run on generated audio.
- Study the generative-model math that underpins the TTS systems and future loss design.

## Approach and Implementation
The core of the week was getting MFA operational on the cluster. Because I had set up `pixi` in Week 2, the Kaldi dependency stack installed cleanly. MFA needs paired audio and transcripts plus a pronunciation dictionary, and it emits phone-level boundaries as TextGrids. I ran alignment on a speaker subset from LibriSpeech to produce those boundaries.
 
I built the extraction pipeline:
- Parse the MFA output
- Locate the plosives and pull those segments out along with their boundaries. 

I kept the code well commented and organized as part of the `erisml` package, continuing the Conventional Commits discipline.
 
Then I built the synthetic side. The point is to run identical extraction on real and generated audio, so I selected the TTS models I intend to test and got the alignment pipeline running against them: 
- XTTS-v2 (a SOTA system using a HiFi-GAN vocoder),
- Tacotron 2 + HiFi-GAN (reported to over-smooth phase information)
- F5-TTS (architecturally quite different from the first two), - VALL-E (which uses no vocoder at all). 

Being able to generate, align, and segment synthetic speech the same way as real speech is what makes the comparison fair and without confounds.
 
Alongside the implementation work I studied the generative-model math that this project leans on. I studied/reviewed:
- VAEs and CVAEs
- Denoising-diffusion models
- Optimal transport as a loss function for detection


## Results
- MFA is stood up on HiPerGator and successfully aligns a real speaker subset to phone-level boundaries.
- The plosive-extraction pipeline runs off MFA output and isolates stop segments with their boundaries.
- The TTS + MFA alignment pipeline is working.
- I have a better understanding of the models I am testing.

## Notes
- Next: Figure out what exact features, classifiers, and evaluation protocol to use, and test where MFA alignment might be unreliable between real and synthetic samples.
