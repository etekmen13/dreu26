**Student:** Brian Tekmen  
**Mentor:** Dr. Kyla McMullen

# Week 4

**Dates:** 06-29 to 07-05

## Goals
- Improve the MFA boundaries so I can measure voice onset time (VOT) and closure duration, not just locate each phoneme.
- Turn the plosive hypothesis from Week 1 into an experimental plan with clear measurements.
- Clean up the repository so another researcher can reproduce the pipeline.

## Approach and Implementation
I started by improving the MFA alignment. The default boundaries can locate a phoneme, but they are not always precise enough for measurements inside a plosive. For example, VOT may only last 30 to 100 ms, so a boundary that is off by 20 ms can change the result. I added MFA's `--fine-tune` option, which performs a second alignment pass with smaller time steps.

I then wrote a more concrete plan for testing the hypothesis. A plosive has three main parts: a closure, a short release burst, and the start of voicing. I want to measure each part because TTS models may reproduce them differently. During the closure there should be very little energy. The burst is a short, broadband sound that contains information about how the consonant was produced. VOT measures the time between the burst and the start of voicing. These details may be smoothed or reconstructed incorrectly during speech generation.

While reviewing the TTS models, I corrected two misunderstandings from Week 3. F5-TTS still generates a mel spectrogram before Vocos converts it to audio. VALL-E does not use a mel spectrogram, but it does use an EnCodec decoder to convert audio tokens back into a waveform. The useful comparison is therefore between models with a mel-spectrogram bottleneck and models with a codec-token bottleneck.

Based on this, I chose three groups of features. For the closure, I will measure energy and compare it with the following vowel. For the burst, I will measure its frequency distribution, peak frequency, and duration. I will also measure VOT when the plosive occurs before a vowel. I decided not to use mel or MFCC features because the project is testing whether mel-based TTS models lose information that other measurements can capture.

I also cleaned up the repository. A Python module named `mfa.py` conflicted with the MFA command-line program, so I renamed it to `process.py`. I renamed `prepare.py` to `fetch.py` to make its purpose clearer, and I updated the README to match the current pipeline.

## Results
- MFA fine-tuning is working and gives more precise boundaries for the timing measurements.
- I now have a written protocol that connects each feature to a specific part of a plosive.
- I corrected how I had categorized F5-TTS and VALL-E before using those categories in the experiment.
- The module names no longer conflict with MFA, and the README reflects the current workflow.

## Notes
- MFA finds the phoneme boundary, but it does not find the release burst inside the phoneme. I still need to detect the burst separately.
- Next: build the generation pipeline and identify other differences a classifier could use besides plosive features.
