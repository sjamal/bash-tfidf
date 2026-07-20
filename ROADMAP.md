# Roadmap

Planned extensions to the TF-IDF pipeline.

---

## In Progress

_Nothing currently active._

---

## Planned

### Pipeline Enhancements

- [ ] **stopword_filter.sh** — Pre-processing step that removes a configurable stopword list before tokenisation. Accepts a stopword file via argument or uses a built-in English default.
- [ ] **stemmer_wrapper.sh** — Pipe input through a Porter stemmer (using a lightweight awk implementation) to normalise inflected word forms before TF-IDF scoring.
- [ ] **ngram_tokeniser.sh** — Extend `tf.sh` to emit bigrams and trigrams in addition to unigrams. Configurable n-gram size via argument.

### Corpus Utilities

- [ ] **corpus_builder.sh** — Collect plain-text input from a directory of `.txt` files and emit one document per line (the format expected by the pipeline). Handles encoding normalisation.
- [ ] **corpus_stats.sh** — Report corpus statistics before scoring: document count, total token count, vocabulary size, average document length, and top-N most frequent raw terms.

### Output & Reporting

- [ ] **top_terms_report.sh** — Post-process TF-IDF output to produce a ranked top-N terms table per document. Formatted as a readable plaintext or Markdown table.
- [ ] **tfidf_to_csv.sh** — Convert tab-separated TF-IDF output to a standard CSV with headers (`term,document,score`) for import into spreadsheet tools or Python/R analysis.

---

## Ideas / Backlog

- Cosine similarity scorer: compute pairwise document similarity from TF-IDF vectors
- gnuplot-based term frequency bar chart for quick visual inspection of a corpus
- Parallelised version of the pipeline using `xargs -P` for large corpora

---

## Notes

- All enhancements should remain pure bash / standard Unix tools — no Python, Perl, or external binaries unless clearly documented as optional.
- New stages should be composable filters (stdin → stdout) consistent with the existing pipeline design.
