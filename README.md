# Monaural Continuous-Radius Regional Speech Extraction with Cross-Radius Consistency Learning

[Interactive demo](https://dongb001.github.io/MCR_Regional_Speech_Extraction/) · [Manuscript](assets/paper/manuscript.pdf)

## Overview

Regional speech extraction uses a spatial query to determine which voices should be retained from a single-channel mixture. Given a radius \(r\), the target is the sum of all speakers whose distance from the microphone is smaller than that radius:

\[
y_r(t) = \sum_{i:d_i<r} s_i(t).
\]

This work introduces a **continuously adjustable query radius**, allowing one model to preserve all in-region speech, extract only a subset of speakers, or reject an empty region. Cross-radius consistency learning encourages stable waveforms when two nearby queries select the same non-empty set of speakers.

## Method

The implementation-aligned model processes 16 kHz monaural audio through:

1. a 192-point STFT and recursive magnitude normalization;
2. real, imaginary, and log-magnitude input features;
3. Conv2d projection followed by six GridNet blocks;
4. five frequency-dependent FiLM layers conditioned on a continuous radius embedding; and
5. ConvTranspose2d reconstruction, inverse normalization, and ISTFT.

The network has **685,070 trainable parameters**. A three-layer radius encoder maps \(r/4\) to the FiLM parameters. During training, four queries are sampled from the intervals [0,1), [1,2), [2,3), and [3,4) m. The extraction objective is augmented with waveform consistency for adjacent queries that contain identical, non-empty target sets.

## Validation Diagnostics

For the EXP6/ep48 model on the 0–2-source validation suite, the cell-median summary reports:

| Target condition | Metric | Result |
|---|---|---:|
| Full-target preservation | SI-SDR ↑ | **29.25 dB** |
| Partial-target extraction | SI-SDRi ↑ | **4.41 dB** |
| Empty-target rejection | Attenuation ↑ | **62.00 dB** |

These values summarize saved validation outputs rather than a newly run test-set evaluation. They use the median of three mixtures in each profile-by-radius cell followed by an average across cells. The model-comparison table on the project page uses a different, profile-mean aggregation rule; the two summaries should therefore not be compared as if they shared an identical protocol.

The current records do not support a definitive headline RCE value or an across-the-board “best model” claim. The draft contains unresolved differences in checkpoints, radius grids, valid-pair selection, and mean-versus-median aggregation. Unconfirmed values are intentionally omitted from the demo.

## Interactive Demo

The project page includes:

- five selected listening examples covering full, partial, empty, three-speaker, and noisy conditions;
- precomputed outputs at query radii of 1, 2, 3, and 4 m;
- a dense explorer containing 63 mixtures and 41 queries per mixture from 0 to 4 m in 0.1 m steps;
- downloadable full and reduced CSV files; and
- the manuscript draft and experiment-provenance record.

The audio examples are selected qualitative cases rather than random samples. The radius explorer visualizes precomputed measurements and does not run the model in the browser. A 0.1 m query interval is a sampling step, not a claim of 0.1 m localization accuracy.

## Repository Contents

- `index.html`, `styles.css`, `app.js`: bilingual interactive paper page;
- `assets/audio/`: input mixtures and radius-conditioned outputs;
- `assets/data/audio.js`: listening-example metadata and metrics;
- `assets/data/curves.js`: dense-radius exploration data;
- `assets/data/radius-full.csv`: 63-mixture query table;
- `assets/data/provenance.json`: bundled experiment-source record; and
- `assets/paper/`: manuscript and method figures.

## Citation

The manuscript is a research draft. Author names, affiliations, venue, and the final citation will be added when finalized; this repository does not claim publication or acceptance.

```text
Monaural Continuous-Radius Regional Speech Extraction
with Cross-Radius Consistency Learning.
Research manuscript.
```
