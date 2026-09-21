**Student:** Brian Tekmen
**Mentor:** Dr. Kyla McMullen

# Week 10

**Dates:** 08-10 to 08-16

This is a late retrospective update. The dates above are the scheduled Week 10 dates; this entry covers the work since Week 9, including follow-up experiments and paper preparation through mid-September.

## Goals

- Follow up on the weak cross-generator detection results from Week 9.
- Measure the frequency content of plosive closures and check whether the differences survive changes in measurement.
- Complete the copy-synthesis control and manually review the detected plosive boundaries.
- Bring the findings, negative results, and limitations together in a research paper draft.

## Approach and Implementation

After Week 9, I shifted toward understanding what the acoustic measurements were actually capturing. I built a new extraction pass at 24 kHz with a common frequency cutoff for human and synthetic audio. This let me compare low, mid, and high-frequency energy within the quiet closure before a plosive release. Instead of measuring only overall loudness, I measured each band's level relative to the total level in the same closure. I also kept explicit records of failed measurements, since dropping them can make the human and synthetic samples look more comparable than they are.

I made the extraction resumable and calculated uncertainty by resampling speakers. The band measurements revealed differences that the earlier broadband summaries could hide, so I tested them in another leave-one-generator-out classifier experiment. Its AUCs ranged from about 0.544 to 0.634. None reached the success threshold I set before the experiment, and three of the four fell below 0.60. I stopped pursuing that feature set as a general detector and focused on the measurement findings. 

I then manually reviewed the detector's burst onset detection, and it exposed a significant problem. Some detected releases were actually later acoustic events, which meant the supposed closure could contain part of the following sound. I reviewed separate development and held-out samples, and revised the release detector. It selected the reviewed release in 45 of 62 labeled cases, or 72.6% agreement, with a 95% interval of 60.4–82.1%. 

I reran closure extraction using the repaired detector and rebuilt the comparison tables. I checked how the results changed with the closure duration, preceding sound, and the floor dB used for very quiet bands. The mean high-band share in dB remained lower for CosyVoice2, F5-TTS, and VALL-E X across the tested support and floor choices. 

I completed the Vocos copy-synthesis control proposed in Week 9 by passing human recordings through a mel-conditioned vocoder and measuring the reconstructed audio. With the repaired detector, mid and high-band shape increased rather than decreased, meaning this particular human-input reconstruction did not reproduce the synthetic-speech pattern. 

The latest follow-up tested stronger explanations of the result. I matched consonants using word and phone sequences, trimmed closure edges, removed very low-frequency content, split the high-frequency band, and compared burst closures with raw pauses in speech. Matching and trimming preserved several contrasts, but a 70 Hz high-pass filter reversed the high-band contrast for F5-TTS, VALL-E X, and XTTS-v2. The pause comparisons also did not establish a closure-specific deficit under the full uncertainty checks. None of the four systems passed all the planned attribution checks, meaning these tests do not support explaining them as one shared failure to produce plosive closures.

Alongside the experiments, I prepared and revised an ICASSP-style manuscript, checked the reported numbers against saved outputs, and audited the citations and claims. The paper's focus is now the measured spectral differences and their dependence on measurement choices. The earlier human-reference transformations and Wasserstein-distance ideas did not become the main follow-up.

## Results

- Completed the 24 kHz analysis, a new band-feature classifier experiment, the Vocos reconstruction control, and the repaired closure measurements.
- Found measurable spectral differences, but the classifier still transferred poorly to unseen generators.
- Identified release-detection and token-matching errors, added manual review, and quantified the remaining uncertainty.
- Established that sample selection, averaging, low-frequency content, and bandwidth affect the interpretation of the band results.
- Produced a paper draft and reproducible tables, while narrowing the claims in response to the follow-up checks.

## Notes

- The strongest conclusion is a difference in the measured spectral balance of selected, measurable windows. A universal plosive signature, a training-loss explanation, and perceptual relevance remain unestablished.
- I ended up chaning the direction of the project. The weak detection result led to a closer examination of the acoustic measurements and of how easily their interpretation can change.
