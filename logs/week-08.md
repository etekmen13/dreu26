**Student:** Brian Tekmen
**Mentor:** Dr. Kyla McMullen

# Week 8

**Dates:** 07-27 to 08-02

## Goals
- Generate the full VCTK cohort with CosyVoice2, F5-TTS, VALL-E X, and XTTS-v2.
- Run the same normalization, alignment, and feature extraction stages for every condition.
- Freeze a matched evaluation cohort and verify that the speaker and prompt splits do not leak.
- Make the long HiPerGator analysis recoverable if it reaches the SLURM time limit.

## Approach and Implementation
I spent the beginning of the week running generation for all four models. F5-TTS kept running out of host memory during the full run even though it had passed the smoke test. I traced this to a new thread pool being created for every inference call. I changed it to run those operations on the main thread and added memory logging every 25 calls. The job stopped growing in memory after that.

I then ran the remaining stages:

- Normalize the real and synthetic audio to 16 kHz
- Align the generated audio with MFA
- Extract the plosive features
- Extract the vowel and sonorant placebo features

I fixed a bug in the cohort audit. It was comparing excluded plosive tokens against utterance counts. I added separate token and utterance columns and changed the check to use utterances. After removing reference recordings and utterances that were not available for every generator, 42,890 of 44,283 utterances remained across 109 speakers.

I froze the five-fold train and test plan. The fold audit showed zero speaker overlap and zero prompt overlap. There were 8,528 total held-out utterance keys across the folds.

I also added timestamped result folders and a classification-only SLURM script. Classification and permutation testing took much longer than the earlier stages, so this lets me continue from saved features and folds instead of restarting the whole pipeline after a timeout.

## Results
- Full-scale generation and feature extraction completed for CosyVoice2, F5-TTS, VALL-E X, and XTTS-v2.
- The F5-TTS memory problem is fixed and its memory use is now logged during long runs.
- The frozen matched cohort contains 42,890 utterances from 109 speakers, or 96.9% of the 44,283-utterance manifest.
- The five-fold audit confirms zero speaker or prompt overlap between training and testing.
- Classification can now resume without rebuilding the earlier stages.

## Notes
- The full analysis reached the SLURM time limit during classification. The predictions were saved, but the permutation tests and final result files were not completed.
- The effective sample size for cross-generator generalization is four synthesis systems, even though the utterance-level cohort is large.
- Next: recover the metrics from the saved predictions and figure out why the leave-one-generator-out result is weak.
