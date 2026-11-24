# Indonesian Frequency Vocabulary Dataset

A structured JSON dataset of high-frequency Indonesian vocabulary focused on everyday spoken language. Each entry pairs a frequency-ranked Indonesian word with English translations, part of speech, pragmatic usage notes, natural collocations, and everyday conversational example sentences.

## Live Dictionary

Browse and search the dataset online via GitHub Pages:  
`https://mfarhancode.github.io/indo-frequency-vocab/`

Raw JSON endpoint:  
`https://raw.githubusercontent.com/mfarhancode/indo-frequency-vocab/main/vocab.json`

---

## Source Data

The base word list was taken from the Indonesian 50k frequency list in the **FrequencyWords** repository:

- **Source Repository:** [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords)
- **Source File:** [`content/2018/id/id_50k.txt`](https://github.com/hermitdave/FrequencyWords/blob/master/content/2018/id/id_50k.txt)

### Original File Structure

`id_50k.txt` is a plain-text file sorted in descending order of word frequency (derived from OpenSubtitles 2018 data). Each line contains two space-separated values: the word token and its raw occurrence count in the corpus.

```text
yang 1697998
aku 1545721
tidak 1409866
ini 1372692
itu 1353839
```

## Dataset Generation & Cleaning Process

1. **Initial Extraction:** The top **10,000** entries (ranks `1` to `10,000`) were extracted from `id_50k.txt`, using each word's line position as its initial `frequencyRank`.
2. **Noise & Invalid Entry Filtering:** Because subtitle corpora contain transcription artifacts and non-Indonesian tokens, entries were audited using automated LLM screening (via free Groq APIs) followed by manual review to remove:
   - Pure English or foreign words that are not standard Indonesian loanwords.
   - Proper names of specific people and foreign locations.
   - OCR glitches, hex strings, numbers, and isolated symbols.
   - Explicit or inappropriate terms.
3. **Vocabulary Data Generation:** Validated words were processed in batches using Python scripts and **free Groq APIs** with structured JSON output, prompted for natural, everyday spoken Indonesian rather than formal textbook language. Each word was populated with English translations, part of speech, formality/slang usage notes, 2-to-3-word collocations, and conversational example sentences.
4. **Sequential Re-indexing:** After removing invalid entries, `wordId` values were re-indexed sequentially starting from `1` with no gaps, while each entry's original `frequencyRank` from `id_50k.txt` was preserved.

### Dataset Summary

- **Initial words extracted:** 10,000 (from `id_50k.txt`)
- **Final cleaned entries:** `7262` words (`wordId` `1` to `7262`)

## JSON Schema

`vocab.json` contains a single JSON array of word objects. Each object follows this schema:

| **Field** | **Type** | **Description** |
| --- | --- | --- |
| `wordId` | `integer` | Sequential unique identifier (`1` to `N`) after cleaning. |
| `indonesian` | `string` | Target Indonesian vocabulary word. |
| `frequencyRank` | `integer` | Original frequency rank position in `id_50k.txt`. |
| `translation` | `string` | Common English meanings, separated by semicolons (`;`) when multiple distinct meanings exist. |
| `pos` | `string` | Grammatical part of speech (e.g., `noun`, `verb`, `adjective`, `adverb`). |
| `usageNote` | `string` | Context regarding formality, colloquial usage, slang, or cultural nuance. Empty string `""` if none applies. |
| `collocationIdn` | `string` | Natural 2-to-3-word Indonesian phrase using the target word. |
| `collocationEng` | `string` | Direct English translation of `collocationIdn`. |
| `exampleSentenceIdn` | `string` | Natural everyday Indonesian sentence demonstrating the word in context. |
| `exampleSentenceEng` | `string` | English translation of `exampleSentenceIdn`. |

### Example Entry

```json
{
    "wordId": 1,
    "indonesian": "aku",
    "frequencyRank": 1,
    "translation": "I; me",
    "pos": "pronoun",
    "usageNote": "Very informal, used among friends, peers, or in casual contexts. Not appropriate in formal or polite situations; use \"saya\" instead.",
    "collocationIdn": "aku suka",
    "collocationEng": "I like",
    "exampleSentenceIdn": "Aku suka kopi hitam tiap pagi.",
    "exampleSentenceEng": "I like black coffee every morning."
  }
```
