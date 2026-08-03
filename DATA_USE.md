# Data-use and publication rules

This repository is designed for public release of **code, narrative, and reviewed aggregate outputs only**.

## Allowed in the public repository

- Jupyter notebooks containing analysis code
- SQL queries
- Markdown documentation
- Aggregate summary statistics
- Aggregate charts
- Grouped results where every nonzero cell has `n >= 11`
- The final executive-summary PDF after privacy review

## Do not publish

- Raw MIMIC-IV files or tables
- Patient-level data
- Admission-level or stay-level derived data
- Time-bin-level activity timelines
- `subject_id`, `hadm_id`, `stay_id`, `transfer_id`, or provider/caregiver identifiers
- CSV, TSV, Parquet, Feather, pickle, SQLite, Excel, or database exports derived from MIMIC
- Notebook outputs that contain individual rows
- Small cells where `0 < n < 11`
- Credentials, service-account keys, access tokens, or private billing-project details

## Notebook-output rule

Before committing a notebook:

1. Restart the kernel.
2. Run all cells from top to bottom.
3. Confirm that every visible table is aggregate-only.
4. Confirm that grouped outputs suppress `0 < n < 11`.
5. Search the notebook text for:
   - `subject_id`
   - `hadm_id`
   - `stay_id`
   - `transfer_id`
6. Identifiers may appear in SQL joins, but they must not appear in rendered outputs.
7. Remove authentication messages, query previews, stack traces, and temporary debugging output.
8. Confirm that no cell writes a derived data file.

## Derived tables

Private BigQuery scratch tables may be used during analysis, but they must remain in the researcher's controlled cloud environment and must not be copied into this repository.

## Interpretation rule

Use language such as:

- “long-duration burden”
- “recorded segment-hours”
- “consistent with downstream-care friction”
- “a signal for further operational investigation”

Do not label segment duration as confirmed “avoidable delay” unless a reliable medical-readiness timestamp and process-barrier data are available.

## Final pre-publication check

Run:

```bash
git status
git diff --cached
```

Then confirm that the staged files contain no source data, patient-level output, derived dataset, credentials, or small cells.

These safeguards are intentionally conservative. The repository owner remains responsible for complying with the current PhysioNet credentialed data license and data-use agreement.
