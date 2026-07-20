# Bash TF-IDF

TF-IDF (Term Frequency–Inverse Document Frequency) scoring pipeline implemented in pure bash using standard Unix tools. No Python, no Java, no dependencies — just `awk`, `sort`, and `tr`.

The pipeline follows a map-reduce pattern adapted from classic Hadoop Streaming examples. It demonstrates that the core of a distributed text indexing system can be expressed as a handful of composable Unix filters — making it equally useful as a learning tool and a lightweight production utility.

## Background

TF-IDF is foundational to information retrieval and search engines. It ranks terms by how distinctive they are to a specific document relative to the entire corpus — common words score low, rare but document-specific words score high.

The map-reduce decomposition used here mirrors how systems like Hadoop Streaming processed text at scale: each stage is a stateless filter operating on sorted key-value pairs piped between mappers and reducers. Running this pipeline locally is a practical way to understand both the algorithm and the distributed computing pattern without any cluster infrastructure.

**Formula:** $\text{tf-idf}(t, d) = tf(t, d) \times \log\left(\frac{N}{df(t)}\right)$

- $tf(t, d)$ — frequency of term $t$ in document $d$
- $df(t)$ — number of documents containing term $t$
- $N$ — total number of documents in the corpus

## Use Cases

- **Search indexing** — rank terms by relevance across a document collection
- **Keyword extraction** — identify the most distinctive terms per document
- **Text classification preprocessing** — generate TF-IDF feature vectors for downstream ML
- **Log analysis** — surface unusual terms in log files that stand out against a baseline corpus
- **Learning tool** — understand TF-IDF and map-reduce in a minimal, readable implementation
- **Teaching** — demonstrate Unix pipeline composition and functional data processing

## Pipeline Overview

```
documents → tf.sh → sort → reduce-tf.sh → term frequencies
documents → df.sh  → sort → reduce-df.sh → document frequencies
                                         ↘
                                       tf-idf.sh → ranked scores
```

## Workflow

One document per line flows through these stages:

1. `tf.sh` tokenizes text and emits `word<TAB>document_id<TAB>1`.
2. `reduce-tf.sh` aggregates repeated word/document pairs into term frequency.
3. `df.sh` appends a document-frequency seed value of `1`.
4. `reduce-df.sh` sums document-frequency totals per word and attaches them to each row.
5. `tf-idf.sh` computes the final score using `tf * log(N / df)`.

## Sample Data

See `sample-data/neighbourhood_sample_corpus.txt` for a small reproducible corpus.

## Example

From the repository root:

```bash
N=$(awk 'END { print NR }' sample-data/neighbourhood_sample_corpus.txt)
cat sample-data/neighbourhood_sample_corpus.txt \
        | ./tf.sh \
        | ./reduce-tf.sh \
        | ./df.sh \
        | ./reduce-df.sh \
        | N="$N" ./tf-idf.sh

- Plain text, one document per line.
- Tokens are lowercased and stripped of punctuation.
- Blank lines are allowed and count as empty documents.

## Outputs

- `word<TAB>document_id<TAB>1` from `tf.sh`
- `word<TAB>document_id<TAB>term_frequency` from `reduce-tf.sh`
- `word<TAB>document_id<TAB>term_frequency<TAB>1` from `df.sh`
- `word<TAB>document_id<TAB>term_frequency<TAB>document_frequency` from `reduce-df.sh`
- `word<TAB>document_id<TAB>tf_idf` from `tf-idf.sh`

## Requirements

- Bash 4+
- Standard Unix tools: `awk`, `sort`, `tr`, `wc`
- No external dependencies — runs on any Linux or macOS system

## Who This Is For

**Beginners:** A self-contained example of a real algorithm implemented with tools available on every Unix system. Each stage is short enough to read in a few minutes. A good first exposure to both TF-IDF and pipeline-oriented programming.

**Experienced data engineers:** A dependency-free baseline for text scoring in constrained environments (minimal Docker images, embedded systems, CI runners). Easy to extend with stopwords, stemming, or n-gram support — see [ROADMAP.md](ROADMAP.md).

**Instructors:** A worked example of map-reduce decomposition without the overhead of a cluster. Each script corresponds directly to a mapper or reducer in the Hadoop Streaming model.

## Notes

- `N` must be set before running `tf-idf.sh`.
- The reducers sort their input internally so they can be used as standalone filters.
- Use `awk 'END { print NR }' file` rather than `wc -l` to count documents accurately when the final line may lack a trailing newline.

## Related Projects

- [bash-certificate-tools](https://github.com/sjamal/bash-certificate-tools) — SSL/TLS certificate generation and inspection
- [bash-misc-tools](https://github.com/sjamal/bash-misc-tools) — General-purpose bash utilities
- [python-data-processing](https://github.com/sjamal/python-data-processing) — Python ETL and data transformation pipelines

## License

See [LICENSE](LICENSE).
