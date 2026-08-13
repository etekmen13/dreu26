**Student:** Brian Tekmen
**Mentor:** Dr. Kyla McMullen

# Week 9

**Dates:** 08-03 to 08-09

## Goals
- Recover and summarize the completed predictions from the truncated four-generator run.
- Compare plosive, non-plosive placebo, and combined features within generators and under leave-one-generator-out evaluation.
- Diagnose why the plosive signal does or does not transfer to an unseen generator.
- Turn the result into a feasible next-step research plan.

## Approach and Implementation
I recovered the AUC and EER results from the predictions that finished before the timeout. I also ran 500 speaker bootstraps for the main plosive LOGO result and made tables and plots for my meeting. The permutation tests did not finish, so these results are still preliminary.

I made several changes to speed up and resume classification:

- Replaced some row-by-row feature processing with grouped operations
- Saved each completed observed analysis
- Stopped each worker from creating extra numerical-library threads
- Split permutation seeds across SLURM array jobs
- Added a script to estimate the runtime before submitting a job

For the within-generator test, mean AUC was 0.696 with plosive features, 0.826 with the non-plosive placebo, and 0.851 with both. Adding the plosive features improved the combined model for all four generators. The actual acoustic measurements also performed much better than using only plosive counts and missingness, so the classifier was not only learning where feature extraction failed.

The LOGO result was much worse. Mean AUC was 0.568 for plosives, 0.597 for the placebo, and 0.604 for both. Plosive AUC ranged from 0.520 on XTTS-v2 to 0.595 on VALL-E X. XGBoost had a mean plosive LOGO AUC of 0.560, so using a nonlinear classifier did not fix it. The result was also similar across the speaker folds.

I compared the direction of the feature changes for each generator. Some examples were:

- Closure RMS went down for CosyVoice2 but up for XTTS-v2.
- VOT went down for F5-TTS and VALL-E X but up for CosyVoice2 and XTTS-v2.
- Burst tilt went up for the first three generators but slightly down for XTTS-v2.

Only 3 of the 55 plosive summaries changed in the same direction for all four generators. The non-plosive features were more consistent, with 31 of 145 changing in the same direction. VALL-E X was the most similar to the other generators and had the best plosive LOGO result. XTTS-v2 was almost opposite to the other generators and was close to chance. There are only four generators, so I am treating this as a useful pattern and not a statistical conclusion.

Based on this, I wrote down the following next experiments:

- Normalize features against human training data and compare signed values, absolute deviations, squared deviations, and signed plus absolute values. This tests whether generators are abnormal in different directions.
- Start with phone or voiced/voiceless normalization instead of pooling every plosive together.
- Calculate per-feature and sliced Wasserstein distances from human speech. I will use optimal transport as an analysis of the distributions first, not as a full detector.
- Run copy synthesis through Vocos and a multi-speaker HiFi-GAN if I can find a suitable checkpoint. This tests whether the vocoder causes the feature changes.
- Make the full train-one-generator/test-another matrix and manually check a sample of the plosive boundaries.
- Treat higher sample rates and more detailed phonetic contexts as later experiments.

Before testing more generators, I need to choose and freeze the feature transformation, phonetic grouping, classifier settings, and evaluation procedure. The current four generators can be used to come up with the idea, but new generators are needed for a fair final test.

## Results
- Within-generator plosive AUC averaged 0.696, showing that individual generators have measurable plosive fingerprints.
- LOGO plosive AUC averaged 0.568, and adding plosives to the non-plosive baseline improved mean LOGO AUC by only 0.007.
- Only 3 of 55 plosive summaries shifted in one common direction across all four generators, compared with 31 of 145 non-plosive summaries.
- XGBoost did not improve plosive LOGO transfer.
- The results show generator-specific plosive differences, but not one plosive pattern shared by all four generators.
- The cached predictions cover 17,056 balanced held-out predictions per generator and scenario, with no speaker or prompt overlap.

## Notes
- My revised hypothesis is that generators may all differ from human plosives, but not in the same direction.
- If absolute deviations or Wasserstein distances work on new generators, the shared signature is a distance from normal human speech. If the results group by vocoder, then the features are probably pipeline-specific. If neither works, these plosive features are still useful for describing individual generators but probably not for a general detector.
- Next: implement the human-reference features, run the copy-synthesis control, and decide on a fixed protocol before adding new generators.
