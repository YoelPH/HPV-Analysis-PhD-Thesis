# HPV Status and Lymphatic Spread in OPSCC

This repository contains the data analysis and probabilistic modeling used in a
PhD thesis chapter investigating whether HPV status should influence elective
nodal target-volume definition in oropharyngeal squamous cell carcinoma
(OPSCC).

HPV status is a defining characteristic of OPSCC and is routinely considered
in clinical trials and treatment de-escalation. It is especially relevant here
because the probabilistic lymphatic-spread models underlying the CTV-N
definitions developed in the preceding thesis chapters were trained on cohorts
containing both HPV-positive and HPV-negative disease.

Systematic HPV-associated differences in lymph-node-level (LNL) involvement or
occult metastasis risk could justify adapting the model or its resulting CTV-N
recommendations. Conversely, comparable spread patterns would support a shared
modeling framework and common treatment recommendations, as used in the DeEscO
trial.

## Analyses

The work is divided into two complementary parts.

### 1. Retrospective multicenter analysis

[`Paper_data_analysis_2026_no_T0.ipynb`](Paper_data_analysis_2026_no_T0.ipynb)
contains the analysis for the manuscript *Lymphatic Spread Patterns in HPV+ vs.
HPV- Oropharyngeal Squamous Cell Carcinoma: A Multi-Center European Analysis*,
which was under revision at the time of thesis writing.

Using OPSCC patient data from four European centers and excluding T0 cases, the
notebook compares HPV-positive and HPV-negative disease with respect to:

- overall ipsilateral and contralateral LNL involvement;
- conditional probabilities of downstream and skip involvement;
- occult metastasis and false-negative rates;
- tumor subsite and extension.

These analyses provide the empirical basis for assessing whether HPV status
should affect elective nodal target-volume definition.

### 2. Modeling implications

[`HPV_binary.ipynb`](HPV_binary.ipynb) fits an HPV-stratified binary
lymphatic-spread model using the [`lymph`](https://github.com/rmnldwg/lymph)
package. It investigates whether observed HPV-associated differences can be
represented through altered progression of lymphatic spread or require a
targeted change to an individual spread parameter. The fitted model is then
used to compare predicted LNL prevalence, involvement patterns, and occult
metastasis risks relevant to model-based CTV-N recommendations.

## Repository contents

```text
.
|-- HPV_binary.ipynb
|-- Paper_data_analysis_2026_no_T0.ipynb
|-- data/
|   |-- HPV_data_full.csv
|   `-- oropharynx_data_clin_trial.csv
|-- figures/
|   |-- HPV_early_prevalence.png
|   |-- HPV_late_prevalence.png
|   |-- HPV_early_II_III_patterns.png
|   |-- HPV_late_II_III_patterns.png
|   |-- involvement_diagnostic.png
|   |-- involvement_diagnostic_contra_extension.png
|   |-- III_downstream.png
|   |-- III_skip.png
|   |-- IV_downstream.png
|   `-- IV_skip.png
`-- requirements.txt
```

`HPV_data_full.csv` is used by the retrospective analysis.
`oropharynx_data_clin_trial.csv` is used for model fitting and evaluation. Both
files use the three-row, multi-index column format supported by `lydata`.

## Setup

The notebooks were developed with Python 3.12. Create an isolated environment
and install the direct dependencies:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Start JupyterLab from the repository root so that the relative data and figure
paths resolve correctly:

```bash
jupyter lab
```

The notebooks are independent and may be run separately. Run
`Paper_data_analysis_2026_no_T0.ipynb` for the empirical analysis and
`HPV_binary.ipynb` for model fitting and risk predictions.

## MCMC backend

The modeling notebook uses an `emcee` HDF5 backend named `full_ipsi_HPV`. This
file contains the MCMC chain, is approximately 678 MB, and is intentionally
excluded from Git by `.gitignore`.

The committed notebook is configured to resume an existing chain by passing
`None` to `run_mcmc`. To generate the backend from scratch, initialize it in the
sampling cell and start from `starting_points`:

```python
backend.reset(nwalkers, ndim)

original_sampler.run_mcmc(
    starting_points,
    nsteps=max_steps,
    progress=True,
)
```

After a chain has been initialized, comment out `backend.reset(...)` and pass
`None` again to continue sampling from its last stored state. Do not commit the
generated `full_ipsi_HPV` file.

## Outputs

Figures are written to `figures/`. Re-running a plotting cell may overwrite the
corresponding PNG. The notebooks also contain intermediate tables, parameter
summaries, posterior diagnostics, and risk calculations that are not exported
as separate files.
