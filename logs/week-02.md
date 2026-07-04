**Student:** Brian Tekmen
**Mentor:** Dr. Kyla McMullen

# Week 2
**Dates:** 06-15 to 06-21
## Goals
- Get HiPerGator (HPG) access and get oriented: confirm SLURM works, learn the storage layout, and run a first interactive job.
- Stand up the project repository with a proper structure and git workflow so future researchers can reproduce the work easily.
- Get an initial LibriSpeech ingestion pipeline running end to end.
- Study the signal-processing foundations needed for phoneme segmentation and acoustic features, and the prerequisite concepts behind the Montreal Forced Aligner (MFA).

## Approach and Implementation

On Monday, 06-15, my HiPerGator access came through. On the cluster I mapped out the storage layout I would be working in (working storage in `/blue`, the large datasets destined for `/orange`, and configs plus SLURM scripts kept in `/home`), and I confirmed the compute path by requesting an interactive session with `srun -p hpg-b200 --gpus=1 --time=20 --pty bash`. That worked, so my intended workflow of editing code locally, pushing to HPG, and launching SLURM jobs was validated end to end.
 
I decided to build the pipeline like an installable package rather than a loose collection of scripts, so that the eventual workflow is clean. I set up the git workflow around this and adopted Conventional Commits as the messaging format to keep history legible for whoever picks this up after me. My first working component was `erisml-prepare`, which downloads a LibriSpeech split (starting with `train-clean-100` via torchaudio) and converts the FLAC files to WAV.
 
I initially chose `uv` for its speed and because it plays well with both HPG and Conda, but I had to switch to `pixi`. MFA depends on a Kaldi-based stack that isn't available on PyPI, and pixi handles those conda-channel packages cleanly, which uv alone could not.
 
The rest of the week I spent studying/reviewing concepts. They were:
Signal Processing
- Nyquist-Shannon theorem, aliasing
- Fourier transform
- Convolution and the convolution theorem
- Windowing and framing (Hamming/Hann)
- Short-time Fourier transform
- Power spectra
- Mel scale and Mel filterbank
- Cepstrum and MFCCs
- Source-filter model of speech, monophones, triphones

Montreal Forced Aligner
- Gaussian-Mixture Model -- Hidden-Markov Model
- The Viterbi algorithm
- The Baum-Welch algorithm (an Expectation-Maximization algorithm) 
- Triphone context modeling
- Speaker-adapted training
- The Kaldi/WFST tooling layer 

## Results
- HiPerGator environment works end to end.
- Repository structure and git workflow are in place.
- The `erisml-prepare` data ingestion pipeline runs.
- I have a solid conceptual grounding in the DSP foundations and in the machinery MFA relies on.

## Notes
- The `uv` to `pixi` migration cost some time, but it was worth it to prevent future headache.
- Next: stand MFA up on HiPerGator, align a speaker subset, and extract the plosive segments.
