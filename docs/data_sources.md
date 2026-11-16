# Data sources

## harishnair04/mtsamples

- Landing page: https://huggingface.co/datasets/harishnair04/mtsamples
- Licence: **apache-2.0**
- Attribution: harishnair04/mtsamples, https://huggingface.co/datasets/harishnair04/mtsamples (licence: apache-2.0)
- Download method: `{'kind': 'huggingface_parquet', 'dataset': 'harishnair04/mtsamples', 'config': 'default', 'split': 'train'}`
- Inspected on 2026-11-16 (sha256 of the inspected sample `8d8fc4e23b468891…`)

| Column | Validated type |
|---|---|
| `Unnamed: 0` | integer |
| `description` | string |
| `medical_specialty` | string |
| `sample_name` | string |
| `transcription` | string |
| `keywords` | string |

### Limitations

- Automated inspection: Snapshot inspected 2026-11-16; the source may change after this date.
- Automated inspection: Profiled the first 64 rows of config 'default', split 'train' served by the Hugging Face datasets-server, not the full dataset.
- Transcription samples from the MTSamples collection as redistributed by a Hugging Face user; the uploader's card states apache-2.0, the original collection's terms were not fetched (unverified).
- English-language, US-style dictated reports; not European clinical text.

## Alternatives considered and rejected

- **tchebonenko/MedicalTranscriptions**: Same content as hf-harishnair04-mtsamples (identical first rows) but with no licence stated.
- **lyumengxian/MTS_Dialogue**: Gated: HTTP 401 without credentials.
- **Primock-57**: Nested dialogue format with 10 of 12 columns entirely null; not usable by tabular or text tools without custom parsing.
- **macabdul9/MedicalASR_eval_1K**: Audio and reference sentences only; no predictions or scores to analyse.
