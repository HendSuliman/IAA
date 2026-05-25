# Twenty-Five Years of Inter-Annotator Agreement in NLP

Replication package for the paper *Twenty-Five Years of Inter-Annotator
Agreement in NLP: A Systematic Survey of Trends, Metrics, and Reporting
Practices*.

This repository is anonymised for double-blind review. It contains the
corpus, the extracted measurement flags, the derived summary tables, and
the notebook used for the LLM-based reporting-compliance audit. Together
these reproduce every quantitative claim in Sections 3 to 5 of the paper.
The corpus CSV is the full output of the survey's NMF clustering and
regex metric-extraction pipeline; that pipeline code is not itself part
of this release.

## Repository contents

```
iaa-survey-anon/
  README.md
  LICENSE
  data/
    iaa_corpus_665.csv            Full 665-paper corpus with all extracted flags
    metrics_by_section.csv        Per-section metric counts (cross-tabulation)
    misuse_flags.csv              Per-paper metric-misuse detections
    transparency_flags.csv        Per-paper annotator-metadata reporting flags
    iaa_values.csv                Numeric IAA coefficient values where extractable
    language_coverage.csv         Language distribution of the corpus
    summary_stats.json            Headline statistics used in the abstract
    compliance_audit_results.csv  50-paper, two-model checklist audit (Table 7)
  notebooks/
    dual_llm_annotators.ipynb     LLM reporting-compliance audit (Section 5.3)
```

## The corpus: `data/iaa_corpus_665.csv`

One row per paper, 665 rows, 65 columns. The corpus was assembled by
searching the ACL Anthology (retrieved January 2026) with regular
expressions over title and abstract metadata. Column groups:

- **Identification:** `acl_id`, `title`, `abstract`, `year`, `authors`,
  `venue`, `citation_count`.
- **Taxonomy:** `survey_section`, `section_label`, `nmf_topic` (the
  Non-negative Matrix Factorisation topic, k = 16, mapped to the eight
  survey dimensions).
- **Abstract-level metric flags:** `ab_cohens_kappa`, `ab_fleiss_kappa`,
  `ab_krippendorff_alpha`, and the remaining `ab_*` columns, one per
  metric, plus `ab_any_metric`.
- **Full-text metric flags:** `cohens_kappa`, `fleiss_kappa`,
  `krippendorff_alpha`, and the remaining per-metric columns, plus
  `any_metric`. `fulltext_ok` records whether the PDF was parsed (549
  papers) or the scan fell back to the abstract (116 papers).
- **Misuse flags:** `misuse_cohen_multi_rater`,
  `misuse_cohen_continuous_mt`, `misuse_unweighted_ordinal`,
  `misuse_fixed_span_unitising`, `misuse_llm_raw_accuracy`, `any_misuse`.
- **Reporting transparency:** `transp_compensation`,
  `transp_selection_criteria`, `transp_expertise`,
  `transp_language_background`, `transp_adjudication`.
- **Other:** `iaa_value`, `annotator_count`, `ci_reported`, `paradigm`,
  `annotation_language`, `period`, and the inter-coder reliability
  columns (`cs_coder1`, `cs_coder2`, `cs_notes`, `cs_section_label`)
  for the 50-paper double-coded sample.

## Reproducing the paper

- **Table 4 (metric frequency)** and **Table 6 (metric x section
  cross-tabulation):** count the `any_metric` and per-metric columns of
  `iaa_corpus_665.csv`, or read `metrics_by_section.csv` directly.
- **Table 5 (misuse frequencies):** count the `misuse_*` and
  `any_misuse` columns, or read `misuse_flags.csv`.
- **Temporal and venue trends (Section 4):** group `any_metric` by
  `year` / `period` and by `venue`.
- **Headline figures (abstract):** see `summary_stats.json`
  (`n_corpus`, `n_metric_fulltext`, `pct_no_metric_ft`, and so on).
- **Table 7 (reporting-compliance audit):** see
  `compliance_audit_results.csv` and the notebook below.

## The LLM compliance audit: `notebooks/dual_llm_annotators.ipynb`

The notebook scores the 50 most recent corpus papers against the
13-item reporting checklist using two large language models as
independent annotators (Claude and GPT-4o), accessed through the
aiXplain API. It fetches each paper's PDF, prompts each model with the
checklist rubric, and records a binary judgement per item. The results
in `compliance_audit_results.csv` are the source of Table 7; the audit
reported in the paper uses the `claude_*` and `gpt_*` columns. The
`gemini_*` columns record an additional pilot model that was not
included in the paper.

To run the notebook, set the `AIXPLAIN_TEAM_API_KEY` environment
variable to your own aiXplain key. No key is distributed with this
repository.

## Data sources and licence

All corpus metadata (paper identifiers, titles, abstracts, venues,
years) derives from the ACL Anthology and is redistributed under the
Creative Commons Attribution 4.0 International (CC BY 4.0) licence. The
code in this repository is released under the MIT Licence (see
`LICENSE`). 