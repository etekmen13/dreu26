**Student:** Brian Tekmen  
**Mentor:** Dr. Kyla McMullen

# Week 6

**Dates:** 07-13 to 07-19

## Goals
- Decide with the team whether to replace LibriSpeech with VCTK.
- Write an implementation plan for rebuilding the pipeline in a clear order.
- Build and test the signal-processing, VCTK ingestion, normalization, and segmentation stages.
- Automate the VCTK download for HiPerGator.

## Approach and Implementation
At the beginning of the week, I met with Thien and Dr. McMullen about the corpus problem. They agreed that LibriSpeech's 8 kHz frequency limit would interfere with the burst measurements, so we decided to switch to VCTK. VCTK is recorded at 48 kHz and contains about 110 English speakers. This preserves the higher-frequency information and provides enough speakers for the planned train and test splits.

We also decided to rebuild the pipeline instead of changing only the corpus loader. Too much of the earlier code assumed LibriSpeech's directory layout and 16 kHz audio. I wrote `Implementation_Plan.md` before starting the rebuild. The plan divides the work into data preparation, signal processing, validation, generation, feature extraction, dataset construction, and classification. Each stage has an expected output and a test that must pass before moving on.

I kept the old LibriSpeech pipeline on the `main` branch and started the VCTK work on a new branch. I removed the LibriSpeech-specific preparation code, updated the package structure, and moved the main settings into YAML configuration files. I also replaced Tacotron2 with CosyVoice2 because Tacotron2 cannot clone the speakers in VCTK, which would make speaker matching difficult.

I then wrote the main signal-processing functions in `dsp.py`. These functions compute the short-time spectrum, locate the release burst and start of voicing, measure the burst spectrum, and measure energy in selected frequency bands. Burst detection first finds the general area of a sudden energy change and then refines the location using the waveform. The refinement is needed because the first pass only checks every 4 ms. If no clear burst is present, the function reports a missing result instead of choosing an arbitrary time.

I tested these functions with synthetic signals where the correct answer was known. All 15 signal-processing tests passed. The burst onset error was 1.75 ms with a 4 ms limit, and the voicing onset error was 2 ms with a 5 ms limit. These tests show that the functions work on controlled signals, but they do not yet show how well they work on real speech.

Next, I built the VCTK data pipeline. The normalization stage converts audio to mono, trims silence, resamples it, normalizes loudness, and prevents clipping. The ingest stage reads the VCTK directory structure and keeps one microphone recording per utterance. The segmentation stage connects the MFA output to the exact normalized file that was aligned. I tested this workflow on a small fabricated VCTK-style dataset.

Finally, I automated the 11 GB VCTK download. The script can resume an interrupted download and checks the published file size and MD5 checksum before extracting it. I also added options to select a small number of speakers and utterances during ingestion, which will let me run a pilot before processing the full corpus.

## Results
- The team approved the switch from LibriSpeech to VCTK.
- The rebuild has a written plan with a test for each stage.
- All 15 signal-processing tests pass on signals with known timing and frequency values.
- VCTK ingestion, normalization, and segmentation run on a fabricated dataset.
- The VCTK download and pilot subsetting are automated.
- The full test suite has 69 passing tests.

## Notes
- The current results use synthetic or fabricated test data. I still need to run the pipeline on real VCTK audio and inspect the MFA alignments.
- I found that the validation stage depends on output from the ingestion and segmentation stage, even though I had numbered validation first. I documented the correct dependency order in the plan.
- Next: build the feature extraction and classification stages, then run the first real pilot.
