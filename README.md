# Hospital Flow Burden in MIMIC-IV

**A single-center operational case study using MIMIC-IV v3.1**

**Author:** Saige Mukherjee  
**Contact:** mukherjeesaige@gmail.com  
**LinkedIn:** <https://www.linkedin.com/in/saige-mukherjee-0aba68281/>

**Version:** 1.1  
**Focus:** Healthcare operations, patient flow, capacity burden, and discharge destination  
**Status:** Independent portfolio project; not affiliated with BIDMC, MIT, or PhysioNet

## Executive summary

This project examines how recorded care-unit time is distributed across Beth Israel Deaconess Medical Center, where unusually long care-location segments accumulate, and what additional process data would be required to distinguish necessary clinical time from avoidable waiting.

### Headline findings

- The median valid system-wide care-unit segment was **8.8 hours**.
- **14.1%** of valid segments exceeded **72 hours**, but those segments accounted for **65.3%** of all recorded segment-hours.
- **Medicine** contributed the largest share of excess recorded time beyond the 72-hour threshold.
- Among admissions whose last recorded service was **MED**, the median final segment was **57.6 hours longer** for institutional post-acute destinations than for home discharge.
- These findings identify where long-duration burden is concentrated. They do **not** establish that the additional hours were avoidable or that a patient was medically ready to leave.

[View the executive summary PDF](docs/Executive_Summary.pdf)

## Operational question

Where is recorded hospital capacity concentrated in unusually long care-location segments, and what process data would an operations team need to determine why?

## Repository guide

| Path | Purpose |
|---|---|
| [`notebooks/00_executive_summary.ipynb`](notebooks/00_executive_summary.ipynb) | Condensed presentation of the headline KPIs, visuals, findings, limitations, and operational implications |
| [`notebooks/01_project_overview.ipynb`](notebooks/01_project_overview.ipynb) | Project framing, dataset notes, privacy rules, operational definitions, and notebook structure |
| [`notebooks/02_data_inventory.ipynb`](notebooks/02_data_inventory.ipynb) | Table checks, row counts, schema review, key identifiers, and date/window assumptions |
| [`notebooks/03_whole_hospital_flow.ipynb`](notebooks/03_whole_hospital_flow.ipynb) | System-wide transfer-duration distribution and burden |
| [`notebooks/04_careunit_burden_overall.ipynb`](notebooks/04_careunit_burden_overall.ipynb) | Comparison of care units and broad operational groups by segment volume, duration, and total recorded care-unit hours |
| [`notebooks/05_longstay_careunit_burden.ipynb`](notebooks/05_longstay_careunit_burden.ipynb) | Ranking of care units by long-segment prevalence and excess recorded care-unit hours across multiple thresholds |
| [`notebooks/06_discharge_hypothesis.ipynb`](notebooks/06_discharge_hypothesis.ipynb) | Admission-linked analysis of final-segment duration and discharge destination |
| [`docs/Executive_Summary.pdf`](docs/Executive_Summary.pdf) | Recruiter- and manager-facing rendering of the executive summary |

## Method

### Unit of analysis

The unit of analysis is a valid care-location segment from `mimiciv_3_1_hosp.transfers`.

A segment is included when:

- `careunit` is not null;
- `intime` and `outtime` are not null;
- `outtime > intime`;
- duration is no longer than 365 days.

Fewer than 11 segments exceeding 365 days were excluded as probable administrative artifacts.

### Key measures

- **Segment duration:** `outtime - intime`
- **Long segment:** segment duration above a selected threshold
- **Hours in long segments:** full recorded duration of segments above the threshold
- **Excess hours beyond 72 hours:** `max(segment_hours - 72, 0)`
- **Recorded-time burden:** total segment-hours associated with a care unit or operational group
- **Concentration:** the share and cumulative share of system-wide recorded time or excess time attributable to ranked care units

Quantiles are calculated with BigQuery `APPROX_QUANTILES`.

## Interpretation

The analysis identifies where long-duration burden is located. It does **not** establish:

- when a patient became medically ready for transfer or discharge;
- which hours were avoidable;
- the cause of a long segment;
- whether a care unit was inefficient;
- the causal effect of downstream-care constraints;
- whether a particular patient should have left sooner.

Long duration may reflect appropriate clinical care, case complexity, transfer practices, documentation practices, payer processes, transport, or downstream capacity.

The 72-hour threshold is a descriptive operational lens, not a clinical standard or a claim that the first 72 hours are necessary and all later hours are waste.

## Privacy and data-use safeguards

This public repository contains only:

- analysis code;
- narrative documentation;
- reviewed aggregate outputs;
- grouped results meeting the project suppression rules;
- figures that do not expose patient-level information.

Grouped public outputs use a minimum cell size of **11** and, where appropriate, require at least 11 distinct patients or admissions.

Do not commit:

- MIMIC source data;
- patient-, admission-, transfer-, or time-bin-level extracts;
- actual `subject_id`, `hadm_id`, `stay_id`, or other record identifiers;
- exact patient timelines;
- MIMIC-derived free text;
- CSV, Parquet, pickle, database, spreadsheet, or similar derived datasets;
- Google Cloud credentials, tokens, service-account files, or a live billing-project value.

Before publishing a notebook, confirm that its project-variable placeholder is empty and that no stale outputs or metadata contain private infrastructure details.

See [`DATA_USE.md`](DATA_USE.md) before publishing or reproducing the analysis.

## Reproduction

### 1. Obtain access

Access to MIMIC-IV requires:

1. PhysioNet credentialing;
2. completion of the required human-subjects training;
3. acceptance of the applicable data-use agreement;
4. authorization to query MIMIC-IV v3.1 through BigQuery.

### 2. Create an environment

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

macOS/Linux:

```bash
source .venv/bin/activate
pip install -r requirements.txt
```

### 3. Configure BigQuery

In each analytical notebook, set the billing-project variable to a Google Cloud project authorized to query the PhysioNet-hosted datasets.

The notebooks use:

```text
physionet-data.mimiciv_3_1_hosp
physionet-data.mimiciv_3_1_icu
```

Keep the variable empty in the public repository:

```python
PROJECT_ID = ""
```

The executive-summary notebook uses the equivalent empty `BILLING_PROJECT` placeholder.

### 4. Run the notebooks

Start with [`notebooks/00_executive_summary.ipynb`](notebooks/00_executive_summary.ipynb) for the shortest path through the project. Run the remaining notebooks in numerical order for the complete analytical trail.

## Recommended reading order

### Hiring manager or operations leader

1. [`docs/Executive_Summary.pdf`](docs/Executive_Summary.pdf)
2. [`notebooks/00_executive_summary.ipynb`](notebooks/00_executive_summary.ipynb)
3. [`notebooks/05_longstay_careunit_burden.ipynb`](notebooks/05_longstay_careunit_burden.ipynb)
4. [`notebooks/06_discharge_hypothesis.ipynb`](notebooks/06_discharge_hypothesis.ipynb)

### Technical reviewer

1. [`notebooks/01_project_overview.ipynb`](notebooks/01_project_overview.ipynb)
2. [`notebooks/02_data_inventory.ipynb`](notebooks/02_data_inventory.ipynb)
3. [`notebooks/03_whole_hospital_flow.ipynb`](notebooks/03_whole_hospital_flow.ipynb)
4. [`notebooks/04_careunit_burden_overall.ipynb`](notebooks/04_careunit_burden_overall.ipynb)
5. [`notebooks/05_longstay_careunit_burden.ipynb`](notebooks/05_longstay_careunit_burden.ipynb)
6. [`notebooks/06_discharge_hypothesis.ipynb`](notebooks/06_discharge_hypothesis.ipynb)

## Next operational data needed

To distinguish necessary clinical time from avoidable waiting, a hospital would need structured timestamps for:

- medical readiness;
- referral initiation;
- payer authorization;
- placement acceptance;
- downstream bed availability;
- transport booking;
- actual departure.

Those fields would convert this burden analysis into a true discharge-delay analysis.

## Data source and citations

This project uses **MIMIC-IV v3.1**:

> Johnson, A., Bulgarelli, L., Pollard, T., Gow, B., Moody, B., Horng, S., Celi, L. A., & Mark, R. (2024). *MIMIC-IV (version 3.1).* PhysioNet. https://doi.org/10.13026/kpb9-mt58

Original publication:

> Johnson, A. E. W., Bulgarelli, L., Shen, L., et al. (2023). MIMIC-IV, a freely accessible electronic health record dataset. *Scientific Data, 10*, 1. https://doi.org/10.1038/s41597-022-01899-x

PhysioNet citation:

> Goldberger, A. L., Amaral, L. A. N., Glass, L., et al. (2000). PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals. *Circulation, 101*(23), e215-e220.

## License

The code in this repository is released under the MIT License. MIMIC-IV data are **not** included and remain governed by the PhysioNet credentialed data license and data-use agreement.

## v1.1 note

The system-wide cohort now retains valid ED-only transfer segments with null `hadm_id`; admission-linked discharge analyses are unchanged.
