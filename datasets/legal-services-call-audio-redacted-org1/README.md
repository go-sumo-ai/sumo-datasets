---
pretty_name: "Redacted Legal Services Call Audio - Org 1"
license: other
license_name: sumo-ai-commercial-dataset-license
tags:
- audio
- speech
- conversational
- legal
- webdataset
- parquet
configs:
- config_name: default
  default: true
  data_files:
  - split: train
    path: data/train.parquet
---

# Redacted Legal Services Call Audio - Org 1

This is a private, proprietary dataset of redacted legal-services call audio
and aligned transcripts. The dataset is intended for authorized use by approved users,
customers, and organizations under applicable commercial agreements.

## Contents

- Rows: 559
- Split: `train`
- Audio format: lossless FLAC converted from redacted WAV audio
- Shards: 8 WebDataset-style TAR files under `shards/`
- Viewer table: `data/train.parquet`

## Repository Layout

```text
data/
  train.parquet
shards/
  train-00000.tar
  train-00001.tar
  ...
```

Each TAR shard contains paired files with the same prefix:

```text
<session_id>.flac
<session_id>.json
```

The Parquet table is the browsing and analytics surface for metadata and quick
inspection. The TAR shards are the training/download surface.

## Schema

- `id`: session identifier
- `audio_path`: `shards/<shard>.tar::<session_id>.flac`
- `transcript_path`: `shards/<shard>.tar::<session_id>.json`
- `shard`: shard path in this repository
- `duration_seconds`: call duration
- `sample_rate_hz`: source sample rate
- `speaker_count`: speaker count from transcript metadata
- `language`: transcript language code
- `turn_count`: number of diarized turns
- `conversations`: list of turn objects with `from`, `speaker`, `start_ms`, `end_ms`, and `value`
- `conversation_text`: readable multiline transcript for search and SQL
- `created_at`: transcript creation timestamp when available
- `pipeline_version`: source processing pipeline version
- `redaction_applied`: whether redacted audio generation was recorded
- `metadata`: compact JSON string of selected non-transcript metadata

## Download Examples

Clone without downloading large files immediately:

```bash
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/go-sumo-ai/sumo-datasets.git
cd sumo-datasets
```

Download only the metadata table:

```bash
git lfs pull --include "datasets/legal-services-call-audio-redacted-org1/data/train.parquet"
```

Download one shard:

```bash
git lfs pull --include "datasets/legal-services-call-audio-redacted-org1/shards/train-00000.tar"
```

Download everything:

```bash
git lfs pull --include "datasets/legal-services-call-audio-redacted-org1/**"
```

## Streaming Example

```python
import io
import webdataset as wds
import torchaudio

dataset = (
    wds.WebDataset("shards/train-00000.tar")
    .to_tuple("flac", "json")
)

for flac_bytes, transcript in dataset:
    waveform, sample_rate = torchaudio.load(io.BytesIO(flac_bytes), format="flac")
```

## Notes

The source files are redacted, but this dataset should still be treated as
sensitive and proprietary. Do not make this repository public unless the data
has been separately approved for public release.


## Release

This export was generated from patched force-mono speaker-corrected brand-alias-redacted transcripts for release `u3pro-brand-force-mono-speaker-corrected-brand-alias-20260505`.
