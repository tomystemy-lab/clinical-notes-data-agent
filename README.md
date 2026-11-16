# Specialty Notes Scout


A Claude agent answers questions about 4,999 public medical transcription samples labelled by specialty. It searches the notes, opens the one it needs, and reports how well each specialty is covered, with every number computed by validated tools.

Ask questions in plain English; a Claude agent answers by calling validated Python tools over
the real **harishnair04/mtsamples** dataset (apache-2.0). Every number in an answer comes from a tool
result, and the app shows which tools ran, with their inputs and outputs.

## Example questions

- Which specialties have the most notes, and which have fewer than 10?
- Find notes that mention metformin. Which specialties do they come from?
- Find a Neurology note about migraine, open it and summarise how it is structured.
- Show me Orthopedic notes about knee pain and open the best match.

## How it works

| Tool | Operation | What it does |
|---|---|---|
| `search_notes` | retrieve | Keyword search (TF-IDF) over note titles, descriptions, transcriptions and keywords, optionally within one medical specialty. Returns note ids, titles, specialties and descriptions, not the full text. |
| `read_note` | filter_rows | Return the full transcription of one note, selected by its id (the 'Unnamed: 0' value returned by search_notes). |
| `specialty_coverage` | aggregate | Count notes per medical specialty, to show which specialties are well covered and which have few examples. |
| `list_notes` | filter_rows | List note ids, titles and descriptions for one medical specialty. |

- The model (`claude-opus-5-5`) only chooses tools and arguments. Arguments are validated against
  the real data (allowed metrics, filter values, date ranges, feature bounds); it never writes
  or runs code.
- Each question is limited to 6 model turns and 8 tool calls.
- See [docs/architecture.md](docs/architecture.md) for details.

## Quickstart

Requires Python 3.11+.

```bash
python -m venv .venv
```

Activate it (`.venv\Scripts\Activate.ps1` on Windows, `source .venv/bin/activate` elsewhere),
then:

```bash
pip install -e ".[dev]"
```

```bash
python scripts/fetch_data.py
```

```bash
streamlit run app.py
```

For the chat tab, set `ANTHROPIC_API_KEY` in your environment or in
`.streamlit/secrets.toml` (see `.streamlit/secrets.toml.example`). The tool runner, evaluation
and data tabs work without a key.

## Tests and evaluation

```bash
pytest
```

Tests run offline on SYNTHETIC fixtures with a fake Anthropic client. After downloading the
data, the real-data evals run too.

```bash
python scripts/pin_expectations.py
```

```bash
python scripts/run_eval.py
```

`pin_expectations.py` recomputes the expected answers from the raw data with pyarrow and plain
Python, independently of the tool code. `run_eval.py --agent` also runs agent-level evals
(calls the API, costs tokens).

## Data and attribution

harishnair04/mtsamples, https://huggingface.co/datasets/harishnair04/mtsamples (licence: apache-2.0). Full details, inspection evidence and limitations:
[docs/data_sources.md](docs/data_sources.md). The data is downloaded at setup time and is not
committed to this repository.

## Client view

The app opens in a client view with three tabs: **Ask the assistant**, **About this project**
and **Data & sources**. Set `"show_developer_tabs": true` in `app_config.json` to also show
**Run a tool** and **Evaluation**, which take JSON arguments and are meant for engineers. With
the developer tabs hidden, every visitor question goes through the chat and uses API tokens.

## Deployment

The app runs on Streamlit Community Cloud: `requirements.txt` installs the package, and the API
key goes into the app's Secrets settings. The data is downloaded and validated automatically on
first start; **Download / refresh data** in the sidebar fetches it again.
