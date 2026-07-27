**Student:** Brian Tekmen  
**Mentor:** Dr. Kyla McMullen

# Week 5

**Dates:** 07-06 to 07-12

## Goals
- Make the MFA and SLURM scripts work without depending on my HiPerGator account or file paths.
- Build one generation pipeline for the TTS models and the copy-synthesis control.
- Identify other differences between real and synthetic audio that could mislead the classifier.
- Add a CPU-only test of the generation pipeline.

## Approach and Implementation
I spent Monday and Tuesday making the cluster scripts portable. The alignment script contained absolute paths, so I replaced them with environment variables and documented the required settings in `.env.example`. I also added scripts for submitting the alignment job through SLURM. While doing this, I fixed a dependency problem that made the CPU environment install CUDA packages, and I split `process.py` into separate corpus-preparation and segmentation modules.

For the generation pipeline, I created a shared interface for XTTS-v2, Tacotron2 with HiFi-GAN, F5-TTS, and VALL-E. This lets the rest of the pipeline call each model in the same way. I also added a copy-synthesis control, which sends real audio through a vocoder without generating new speech. This control will help determine whether a difference comes from the TTS model's acoustic stage or from its waveform decoder.

I added a small dummy generator for testing. It does not produce realistic speech, but it lets me run the complete file and manifest workflow on a CPU without downloading model weights. The test creates a small fake corpus, generates matching files, normalizes them, and checks that the files agree with the manifests. This should catch pipeline errors before I spend GPU time on a real model.

The main research issue this week was confound control. A classifier might distinguish real and synthetic audio using sample rate, loudness, speaker, recording channel, or phoneme distribution instead of plosives. To limit those cues, I plan to use the same transcripts, match speakers when the model supports voice cloning, process all audio through the same normalization steps, and keep each speaker entirely in either training or testing. I will also compare the plosive features with a general non-plosive feature set. If both work equally well, the result would not support the plosive hypothesis.

This review exposed a problem with LibriSpeech. Its 16 kHz sample rate only preserves frequencies below 8 kHz, but /t/ and /d/ bursts can contain useful energy above 8 kHz. The TTS systems also produce audio at higher sample rates. Keeping those rates would give the classifier an easy cue, while reducing everything to 16 kHz would remove part of the burst information I want to study. VCTK is recorded at 48 kHz and has many speakers, so I proposed switching to it.

I summarized the problem and my proposed controls in `docs/progress_2026-07-07.md` for Thien and Dr. McMullen. I also asked for guidance about the corpus change, the choice of VALL-E reproduction, the intended role of the classifier, the number of generators to test first, and the available HiPerGator allocation.

## Results
- The MFA and SLURM scripts no longer depend on hard-coded paths.
- The generation workflow passes a CPU-only test with the dummy generator.
- I identified the main confounds and planned a control for each one.
- I proposed replacing LibriSpeech with VCTK because LibriSpeech removes part of the frequency range needed for the burst measurements.
- I sent the progress report and open questions to the team.

## Notes
- VALL-E did not release official model weights, so I may need to use an open EnCodec-based reproduction or choose a different codec model.
- Next: decide on the corpus change with the team and plan the rebuild before changing the existing pipeline.
