[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.nm000452-blue)](https://doi.org/10.82901/nemar.nm000452)

# ECoG_HB_02 (derivative): hybrid-density ECoG high-gamma features with synchronised speech

**This is a DERIVATIVE dataset.** It repackages the single file of the Mendeley Data record "ECoG_HB_02"
(Xupeng Chen, doi:10.17632/fp4bv9gtwk.2, CC-BY-4.0), the one participant of Chen et al. 2024 (Nature Machine
Intelligence) who consented to public release of neural and audio data. **The raw ECoG voltage recordings were not
released by the authors**; what is shared is their processed 125 Hz feature matrix.

## What the signal is
- Source: `ecog_alldataset` in `HB02.h5` (169075 frames x 225 columns, float64), described by the record as
  "hybrid-density ECoG data with synchronized speech and other features".
- Processing, as stated in the paper Methods (not re-done here): ECoG sampled at 2,048 Hz and downsampled to 512 Hz;
  electrodes with artefacts (line noise, poor contact, high-amplitude shifts) or interictal/epileptiform activity
  rejected; common average reference subtracted; Hilbert envelope of the high gamma (70-150 Hz) component, downsampled to
  125 Hz; each electrode normalised (z-scored) to the mean and variance of 250-ms silent periods before each trial's
  stimulus in the training set.
- Measured here: values -2.388 to 4.466, unitless (z-score). 128 columns carry signal
  and 97 columns are exactly 0 for every frame; those are marked `status=bad` in channels.tsv
  (1-based columns: 2, 4, 6, 8, 16, 17, 18, 19, 20, 21, 22, 23, 32, 34, 36, 38, 46, 47, 48, 49, 50, 51, 52, 53, 62, 64, 66, 68, 76, 77, 78, 79, 80, 81, 82, 83, 92, 94, 98, 104, 106, 107, 108, 113, 118, 119, 120, 122, 124, 128, 134, 136, 137, 138, 139, 143, 147, 148, 149, 150, 152, 154, 158, 162, 164, 166, 167, 168, 169, 173, 177, 178, 179, 180, 182, 184, 188, 192, 194, 196, 197, 198, 199, 203, 204, 206, 207, 208, 209, 210, 212, 214, 216, 218, 220, 222, 224). The paper describes the hybrid-density grid as 128 contacts
  (64 macro contacts in an 8 x 8 layout at 10-mm spacing plus 64 interspersed 1-mm contacts). The mapping from the
  225 columns to grid contacts, and contact coordinates, were not released; electrodes.tsv therefore lists the columns with n/a coordinates only.
- Implant hemisphere left and sex female, per the authors' `configs/AllSubjectInfo.json` in the code repository.

## Time axis: not a continuous raw recording
The file is a single run, one BIDS sample per frame of `ecog_alldataset` (125 Hz, 1352.6 s, 0.376 h).
The release does not document how the 169075 frames were assembled (the authors' notebook calls the arrays
`*_alldataset` and segments trials by onset/offset indices). The trial order in the file is not chronological along
the array (`start_ind_re_valid_alldataset` jumps backward after trial index 321),
so the array is probably assembled from separate pieces. Do not treat the time axis as one continuous session and do not filter across the whole run without
checking. `RecordingType` is therefore `discontinuous`.

## Events
`events.tsv` has one row per entry of `label_alldataset` (372 trials, 51 distinct words), sorted by onset,
with onset/duration from `start_ind_re_valid_alldataset` / `end_ind_re_valid_alldataset` (frames / 125 Hz), the word,
and `source_index` (position in the deposit arrays). `TestNum_cum` in the file is 50; per the paper, 50 trials per
participant were held out for testing (which ones is not encoded in the file).

## Data format and fidelity
BrainVision, IEEE float32, multiplexed, 125 Hz. The float64 values were cast to float32: max absolute deviation
2.38e-07 (max relative 5.96e-08). The .eeg file reads back bit-exactly equal to float32 of the
deposit, also through MNE-BIDS. The BrainVision header carries no unit (readers default to microvolts); the values are
z-scores, and channels.tsv says units `n/a`.

## sourcedata/
`sourcedata/HB02.h5` is the deposit file, byte-identical (SHA-256 018294464a291521a6f6c14878ec04dd95ac3e953123dfe3c54a75d1f9c8aa5d). Besides the ECoG
features it holds the synchronised speech audio (`wave_re_alldataset`, 16 kHz, 128 audio samples per ECoG frame),
spectrograms (`wave_re_spec_alldataset`, `wave_re_spec_amp_alldataset`), formants, pitch, intensity, the noise
profile (`noisesample_re_alldataset`) and the trial labels/indices. The speech audio was published by the authors under
CC-BY-4.0 with the participant's consent (paper, Data availability).

## Source and licence
- Data: Chen, Xupeng (2024), "ECoG_HB_02", Mendeley Data, V2, doi:10.17632/fp4bv9gtwk.2. Licence CC-BY-4.0 (kept).
- Paper: Chen, X., Wang, R., Khalilian-Gourtani, A., Yu, L., Dugan, P., Friedman, D., Doyle, W., Devinsky, O., Wang, Y. & Flinker, A. A neural speech decoding framework leveraging deep learning and speech synthesis. Nature Machine Intelligence 6, 467-480 (2024). doi:10.1038/s42256-024-00824-8
- Code: https://github.com/flinkerlab/neural_speech_decoding
- Ethics (paper): Institutional Review Board of NYU Grossman School of Medicine; written and oral consent.
- Funding: NSF IIS-1912286 and 2309057; NIH R01NS109367, R01NS115929, R01DC018805.
- Changes made here (CC-BY "indicate if changes were made"): format conversion only (float64 -> float32 BrainVision),
  sidecars and events added; the original file is included unchanged.
