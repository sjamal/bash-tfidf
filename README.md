# Bash TF-IDF

Map-reduce style bash scripts for computing TF-IDF (Term Frequency–Inverse Document Frequency) scores from plain-text corpora. Implemented using standard Unix tools with no external dependencies.

Pipeline design adapted from Hadoop Streaming patterns.

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
