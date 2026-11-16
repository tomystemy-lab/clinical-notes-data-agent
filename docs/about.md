## What this is

A question-answering assistant over a public collection of sample medical reports. You ask in
plain English, an AI agent works out which searches and counts to run, runs them on the real
data, and explains what it found.

## The data

**4,999 sample medical transcription reports** in English, each labelled with one of **40
medical specialties or report types** (for example Surgery, Neurology, Orthopedic, SOAP / Chart /
Progress Notes, Discharge Summary). The data is the public
[harishnair04/mtsamples](https://huggingface.co/datasets/harishnair04/mtsamples) dataset on
Hugging Face, a copy of the MTSamples collection of transcription samples.

One record holds a title, a one-line description, the specialty, the full dictated report and,
for most records, a list of keywords.

Two things worth knowing before you trust a count:

- Coverage is very uneven. Surgery has 1,103 records; five specialties have fewer than 10.
- The same report is often listed under more than one specialty. The 4,999 records contain
  2,357 distinct report texts, so counts are of records, not of unique documents.

## What you can ask

- **Coverage:** "Which specialties have the most notes, and which have fewer than 10?"
- **Search:** "Find notes that mention metformin. Which specialties do they come from?"
- **Search, then read:** "Find a Neurology note about migraine, open it and summarise how it is
  structured."
- **Within one specialty:** "Show me Orthopedic notes about knee pain and open the best match."

## How the agent works

The assistant is a Claude model (Anthropic) connected to four data tools. For each question it:

1. **Plans.** It decides which tools answer the question and with which settings (search words,
   specialty, note id).
2. **Acts, in several steps if needed.** Search returns only titles and descriptions. When the
   question needs the full text, the agent opens the specific note it chose. This keeps long
   reports out of the conversation until they are needed.
3. **Recovers from mistakes.** Every request is validated before it runs. If the agent asks for
   a specialty that is not in the data, the tool says so and lists the real values, and the
   agent corrects itself.
4. **Explains.** It answers in plain language and states its assumptions and the limits of the
   data.

Click **"How this answer was computed"** under any answer to see every tool call the agent made
and what each one returned.

### Built-in safeguards

- **No made-up numbers.** Counts and search results come from tools running ordinary, tested
  code on the data.
- **Bounded.** At most 6 reasoning steps and 8 tool calls per question, so it cannot loop or run
  up costs.
- **No code execution.** The agent can only choose from the four tools and fill in validated
  settings.
- **Data is treated as data.** The agent is instructed to report on text inside tool results,
  never to follow it as an instruction.

## The four tools

| Tool | What it does |
|---|---|
| Search notes | Ranks notes by how well their title, description, text and keywords match your words, optionally within one specialty |
| Read a note | Returns the full text of one note |
| Specialty coverage | Counts notes per specialty |
| List notes | Lists the titles and descriptions for one specialty |

## How it is checked

- The data is downloaded from the public source, fingerprinted (SHA-256) and checked against the
  expected columns and row count every time the app sets up.
- Each tool is checked against the real data: three against answers worked out independently
  from the raw file, and search by confirming the top result contains the search word (4 of 4
  checks passed on 16 November 2026).
- 68 automated tests cover the data checks, the tools and the agent loop, using a simulated AI
  model so they run without cost.

## Limits

- This is a search tool over sample documents. It is not a medical device, and nothing it says
  is medical advice.
- Search matches words, not meaning: it misses synonyms and abbreviations.
- English, US-style dictated reports only.
- The summaries the agent writes about a note are the model's reading of it and can be wrong;
  open the tool calls to see the source text.

*Data: harishnair04/mtsamples on Hugging Face (the dataset card states apache-2.0). The terms of
the original MTSamples collection were not reviewed. Details are on the Data & sources tab.*
