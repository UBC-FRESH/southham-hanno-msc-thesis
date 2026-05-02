# Paper 2 Plan: Mid-Rotation HDM Predictive Modeling

## Bianca Review Brief (1-page)
**Purpose**
- Request statistical review of our inferential-to-predictive transition plan for Paper 2 before we lock model governance.

**Project objective**
- Produce statistically defensible stand-level mid-rotation HDM risk ranges (incidence, `DMR>=3`, mean DMR) using existing data only.

**Hard constraints**
- No additional fieldwork.
- No new samples.
- Both ordinal submodels must be handled under one consistent predictive-governance workflow.

**Proposed validation-first decision rule**
- Run LOSO-CV on current model forms first.
- Compare against two baselines (prevalence-only, distance/proximity-only).
- Accept current model only if all pass:
  - Better median LOSO probabilistic performance than best baseline.
  - Wins in at least 60% of folds vs best baseline.
  - No major calibration failure after optional recalibration.
- If any fail criterion is triggered, run constrained ordinal refit workflow.

**What we need from Bianca (explicit decisions)**
1. Is this baseline-beating acceptance gate methodologically appropriate for this context?
2. Which ordinal probabilistic metric should be primary for gate decisions and manuscript reporting?
3. Is our calibration approach at the `DMR>=3` management threshold adequate, or should we change it?
4. Is the constrained ordinal candidate family sufficient if refit is triggered, or are we missing essential alternatives?

**Decision deadline**
- Target to resolve these items by **May 11, 2026** so refit (if needed) can proceed without schedule slip.

## Hard Constraints
- No additional fieldwork.
- No new samples.
- Only existing thesis-era and archived external datasets are allowed.
- Both ordinal submodels must follow one shared predictive-governance workflow.
- Primary output is stand-level risk ranges, not a deployment-grade decision-support tool.

## Research Question and Success Criteria
**Primary research question**
- What is the expected range of HDM infection in mid-rotation stands managed with group retention, using only currently available data?

**Success criteria**
- Produce statistically defensible stand-level risk ranges (with uncertainty intervals) for incidence, DMR >=3 incidence, and mean DMR.
- Show that each tree-level ordinal submodel is predictive enough under leave-one-site-out cross-validation (LOSO-CV), or trigger and complete constrained refit.
- Maintain ecological interpretability of predictors and effects.
- Document all assumptions and limits explicitly so manuscript claims remain operational but non-deployment.

## Model Inventory (Both Ordinal Submodels)
**Submodel A: Regenerating-component edge-spread model**
- Outcome: three-class ordinal DMR (`0`, `IBLC_2`, `3_5`).
- Core predictors: edge distance, seed load, DBH, crown class, and selected interactions.
- Existing build context: inferential objective from thesis workflow.
- Data variants for Paper 2:
1. Primary: full thesis modeling data (measured + simulated where originally used).
2. Sensitivity: measured-only subset.

**Submodel B: Pre-harvest proximity model**
- Outcome: three-class ordinal DMR (`0`, `IBLC_2`, `3_5`) as applicable to stand initialization.
- Core predictor family: proximity to high-severity source trees (DMR = 6) plus required covariates in current implementation.
- External basis: Shaw et al. (2005)-derived structure and/or thesis-integrated implementation.

**Interfaces and data contracts**
- `TreeRecord`
  - `site_id`
  - `tree_id`
  - `component` (`regen` or `preharvest`)
  - `dmr_class` (`0`, `IBLC_2`, `3_5`)
  - `distance_m` (where applicable)
  - `seed_load` (regen model where applicable)
  - `dbh_cm` (where applicable)
  - `crown_class` (where applicable)
  - `prox_dmr6_m` (pre-harvest model where applicable)
  - `is_simulated` (boolean)
- `FoldAssignment`
  - `site_id`
  - `fold_id`
  - `split` (`train` or `test`)
  - `seed`
- `ModelArtifact`
  - `model_id`
  - `submodel_name`
  - `formula`
  - `link`
  - `random_effects`
  - `feature_transforms`
  - `training_folds`
  - `fit_timestamp_utc`
- `PredictionOutput`
  - `site_id`
  - `fold_id`
  - `tree_id`
  - `observed_class`
  - `pred_class`
  - `p_class_0`
  - `p_class_IBLC_2`
  - `p_class_3_5`
- `StandRiskOutput`
  - `stand_id`
  - `scenario_id`
  - `pred_incidence_dist`
  - `pred_dmr_ge3_dist`
  - `pred_mean_dmr_dist`
  - `interval_50`
  - `interval_80`
  - `interval_95`

## Data Sources and Data Freeze
**Inputs to freeze**
- Thesis-era tree-level modeling data for regenerating component.
- Measured-only regenerating subset (derived from same source).
- Pre-harvest submodel dataset and any inherited lookup tables/coefficients.
- Feature engineering rules, scaling parameters, and class recoding rules.

**Data-freeze protocol**
1. Create a manifest with file paths, row counts, checksums, and extraction date.
2. Version the manifest and lock a read-only analysis snapshot for Paper 2.
3. Store transformation definitions with deterministic ordering and fixed seed.
4. Record provenance notes for simulated trees and Shaw-based submodel inputs.

## Validation Protocol (LOSO-CV)
**Default split**
- Leave-one-site-out cross-validation for both submodels.
- One site per fold as held-out test; all remaining sites in train.

**Evaluation workflow**
1. Fit existing (current) model form on train sites.
2. Generate held-out predictions for all test trees.
3. Aggregate metrics per fold and pooled across folds.
4. Repeat for both submodels.
5. For regen model, repeat full process on measured-only sensitivity data.

**Core metric families**
- Probabilistic: ordinal log loss / ranked probability-style score (primary family).
- Discrimination/classification: confusion-derived summaries, weighted kappa, classwise recall/precision.
- Calibration: threshold-focused calibration at `DMR >=3` and full ordinal probability calibration diagnostics.

## Acceptance Gate (Validate-Then-Refit)
**Baselines per submodel**
- Baseline 1: prevalence-only ordinal baseline.
- Baseline 2: simple distance/proximity-only ordinal baseline.

**Pass conditions for existing model**
- Beats the best baseline on primary probabilistic metric using median LOSO performance.
- Wins against the best baseline in at least 60% of folds.
- Shows no major calibration failure after optional intercept/slope recalibration.

**Fail condition**
- If any pass condition fails, trigger the refit protocol.

**Gate output**
- One decision record per submodel: `accept_current` or `trigger_refit`, with supporting fold-level evidence.

## Refit Protocol (Constrained Ordinal Family)
**Candidate family (constrained)**
- Proportional-odds mixed-model variants.
- Partial proportional-odds variants where assumption failure is material.
- Alternative link (for example, complementary log-log) only when held-out performance improves.
- Regularization/shrinkage where required for stability and generalization.

**Selection logic**
1. Rank by held-out probabilistic performance (primary).
2. Break ties by calibration performance.
3. Use classification summaries as tertiary tie-breakers.
4. Retain ecological interpretability as a hard requirement.

**Refit deliverables**
- Candidate model comparison table.
- Final selected model artifact per submodel.
- Explicit note of what changed versus thesis-era inferential form.

## Stand-Level Prediction Workflow
1. Use accepted/refit tree-level models as engines for stand-level simulation.
2. Generate class probabilities for all trees under each stand/scenario.
3. Aggregate to stand-level outcome distributions:
   - Infection incidence.
   - DMR >=3 incidence.
   - Mean DMR.
4. Compute and report uncertainty intervals (50/80/95).
5. Document scenario assumptions (layout, retention geometry, and inherited thesis assumptions).
6. Produce comparable outputs for both submodels under one governance template.

## Manuscript Outputs (Methods/Results/Limitations)
**Methods**
- Validation-first governance.
- LOSO-CV design.
- Baseline-beating acceptance gate.
- Refit fallback workflow.
- Stand-level aggregation and uncertainty interval construction.

**Results**
- Existing model vs baseline performance by fold and pooled summary.
- Calibration outcomes and any recalibration applied.
- Sensitivity results (regen full vs measured-only).
- Final stand-level risk ranges for target outcomes.

**Limitations**
- No new data collection.
- Simulated-record assumptions and potential bias direction.
- External transferability bounds across stand structures and regions.
- Claims limited to operational range estimation, not deployment-ready decision tooling.

## Risks and Sensitivity Analyses
**Key risks**
- Site heterogeneity leads to unstable fold performance.
- Simulated records influence calibration and severity tails.
- Class imbalance reduces reliability for severe class (`3_5`).
- Predictor availability mismatch between submodels and stand-level scenarios.

**Sensitivity analyses**
- Regen full dataset vs measured-only subset.
- With and without post-hoc calibration.
- Alternative interval construction options for stand-level outputs.

**Tests and scenarios**
1. Leakage test: no site appears in both train and test in any fold.
2. Reproducibility test: fixed seed yields identical fold assignments and stable metrics.
3. Baseline dominance test: acceptance gate pass/fail logic behaves as specified.
4. Calibration test: assess calibration quality at `DMR >=3` threshold.
5. Sensitivity scenario A: regen model with simulated records.
6. Sensitivity scenario B: regen model on measured-only data.
7. Dual-submodel consistency: same governance protocol across both submodels.
8. Stand-level aggregation test: tree probabilities aggregate correctly to stand-level distributions and intervals.

## Questions for Bianca
1. Is the baseline-beating gate sufficient for inferential-to-predictive transition in this context?
2. Which ordinal probabilistic metric should be primary for model acceptance reporting?
3. What calibration approach is preferred for operational reporting (`DMR >=3` focus)?
4. Is the constrained ordinal candidate family adequate, or does it omit any essential model form?
5. Is the 60% fold-win criterion reasonable given expected ecological between-site variability?

## Tasks, Owners, Dates
| Task | Owner(s) | Due date |
| --- | --- | --- |
| Data/model inventory + data-freeze manifest | Hanno | May 6, 2026 |
| LOSO evaluation of current models + baseline table | Hanno | May 9, 2026 |
| Acceptance-gate decision + Bianca question draft | Greg | May 11, 2026 |
| Refit (if triggered) + comparison results | Hanno | May 14, 2026 |
| Stand-level risk ranges + sensitivity outputs | Hanno, Bridget | May 16, 2026 |
| Internal checkpoint and timeline refresh | All | May 17, 2026 |

## Decision Log
| Date | Decision | Rationale | Status |
| --- | --- | --- | --- |
| 2026-05-02 | No additional fieldwork or new samples | Hard project constraint | Locked |
| 2026-05-02 | Scope is full Paper 2 planning document | Aligns with manuscript objective, not memo-only | Locked |
| 2026-05-02 | Validate-then-refit strategy | Preserves existing model where predictive validity is adequate | Locked |
| 2026-05-02 | Include both ordinal submodels | Ensures internal consistency of stand-level engine | Locked |
| 2026-05-02 | Primary target is stand-level risk ranges | Directly supports management-facing manuscript objective | Locked |
| 2026-05-02 | LOSO-CV is default validation split | Best guard against site leakage and optimistic estimates | Locked |
| 2026-05-02 | Baseline-beating acceptance gate | Avoids arbitrary fixed thresholds while preserving rigor | Locked |
| 2026-05-02 | Refit constrained to interpretable ordinal family | Maintains ecological interpretability and comparability | Locked |
| 2026-05-02 | Claim level: operational range estimates | Appropriate with existing data and stated uncertainties | Locked |
