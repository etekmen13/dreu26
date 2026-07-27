**Student:** Brian Tekmen  
**Mentor:** Dr. Kyla McMullen

# Week 7

**Dates:** 07-20 to 07-26

## Goals
- Build the feature extraction, dataset, evaluation, and classification stages.
- Run an initial real-versus-synthetic experiment on HiPerGator.
- Add leave-one-generator-out evaluation for future tests on unseen generators.
- Prepare the pipeline for longer multi-generator runs.

## Approach and Implementation
I finished the parts of the generation pipeline that did not require a GPU. The pipeline chooses one reference recording for each speaker and removes that recording from the evaluation data, so the model is not tested on the same clip it used for voice cloning. I also added fixed random seeds and a Vocos copy-synthesis control. I left the HiFi-GAN control disabled because the available checkpoint was trained on one speaker and would not be a fair substitute for the planned multi-speaker model.

I then built the analysis pipeline. For each plosive, it measures closure energy, burst frequency information, burst duration, and VOT. The code searches for the release burst near the MFA boundary instead of treating the boundary as the burst itself. If a measurement is missing, the token is kept and marked as missing. Dropping it could change the phoneme distribution differently for real and synthetic speech.

The next step combines the plosive measurements into one row per utterance. The values are normalized separately for each phoneme using only real examples from the training speakers. This prevents information from the test set from entering the training process. I also fixed a bug where real and synthetic versions of the same utterance could have been combined because they shared an utterance ID.

For evaluation, I used speaker-grouped cross-validation. All recordings from one speaker stay in either training or testing, including synthetic recordings cloned from that speaker. I measured ROC-AUC and Equal Error Rate (EER), and added permutation and bootstrap tests. The test suite checks that the classifier finds a known signal, returns to chance when labels are shuffled, does not create a result from random noise, and keeps the speakers separated.

I ran the first pilot on HiPerGator using XTTS-v2 and 12 speakers. The final dataset contained 708 synthetic utterances and a similar number of real utterances, with 9,912 plosive tokens in total. The classifier reached an AUC of 0.618 with a 95% confidence interval from 0.567 to 0.668. Its EER was 0.415. The shuffled-label control had a mean AUC of 0.507 with a permutation p-value of 0.038.

I checked whether missing measurements could explain the result. Burst detection succeeded on 87% of real tokens and 89% of synthetic tokens. Voicing detection succeeded on 46% and 49%, respectively. These rates are close, so the classifier is unlikely to be separating the groups only because feature extraction fails more often on one of them. The same front-end also normalized sample rate and loudness before classification.

At the end of the week, I added leave-one-generator-out evaluation. This will train on several generators and test on a generator the classifier has not seen. I also added separate environments for CosyVoice2 and VALL-E X, checks for missing generated files, saved train and test splits, and the non-plosive control from Week 5. These changes prepare the pipeline for a longer multi-generator run.

## Results
- The analysis pipeline runs from plosive measurements through classification.
- In the XTTS-v2 pilot, the plosive features reached AUC 0.618 (95% CI [0.567, 0.668]) and EER 0.415 with speakers separated between training and testing.
- The permutation test and confidence interval provide initial evidence that the features contain a real-versus-synthetic signal, although the classifier still makes many errors.
- Burst and voicing detection rates are similar for real and synthetic speech, so missing measurements do not appear to explain the result.
- Leave-one-generator-out evaluation and the non-plosive control are ready for the multi-generator experiment.
- The test suite has 194 passing tests and 1 skipped test.

## Notes
- This pilot tests unseen speakers from XTTS-v2. It does not yet show whether the features work on an unseen generator.
- An EER of 0.415 means that about 4 out of 10 samples are misclassified at the equal-error threshold. The current classifier is more useful for studying the features than as a practical detector.
- I still need to listen to the CosyVoice2 and VALL-E X test outputs before running them at scale.
- Other open tasks are testing the timing measurements on TIMIT, selecting the exact HiFi-GAN checkpoint, and repeating the experiment at 24 kHz.
- Next: generate the remaining synthetic data and run the leave-one-generator-out evaluation.
