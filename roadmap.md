# Southam MSc Paper 2 Roadmap

This roadmap organizes the second-paper workflow into executable phases with task/subtask
checklists. It is aligned with `paper2_predictive_plan.md` and is intentionally operational:
update statuses in-place as deliverables land, and keep "Detailed Next Steps" synced with the
current active phase.

## Phase 0 — Project Setup & Planning Surface ✅ (complete)
- [x] Establish planning artifacts in repository.
  - [x] Add decision-complete planning document (`paper2_predictive_plan.md`).
  - [x] Create roadmap surface (`roadmap.md`) with phase/task/subtask structure.
  - [x] Push project into a public GitHub repo for collaborator access.
- [x] Lock hard constraints and scope defaults.
  - [x] No new fieldwork and no new samples.
  - [x] Full Paper 2 scope (not memo-only).
  - [x] Include both ordinal submodels under one governance workflow.

## Phase 1 — Data Freeze & Model Inventory
- [ ] Freeze all model inputs and transformation logic.
  - [ ] Build a data-freeze manifest (paths, checksums, row counts, extraction date).
  - [ ] Snapshot feature engineering and scaling rules.
  - [ ] Record provenance for simulated records and Shaw-based pre-harvest inputs.
- [ ] Inventory both ordinal submodels as implementation-ready specs.
  - [ ] Regenerating-component model inventory (formula, link, effects, interactions).
  - [ ] Pre-harvest model inventory (predictor set, assumptions, source references).
  - [ ] Define/update data contracts (`TreeRecord`, `FoldAssignment`, `ModelArtifact`, `PredictionOutput`, `StandRiskOutput`).
- [ ] Deliverable checkpoint (target: May 6, 2026).
  - [ ] Inventory summary reviewed by project team.
  - [ ] Data-freeze manifest committed to repo.

## Phase 2 — Validation-First Evaluation (Current Model Forms)
- [ ] Implement LOSO-CV evaluation workflow for both submodels.
  - [ ] Fold construction by site with leakage checks.
  - [ ] Reproducibility controls (fixed seed, deterministic fold IDs).
  - [ ] Per-fold prediction export for pooled analysis.
- [ ] Run baseline comparisons per submodel.
  - [ ] Baseline 1: prevalence-only ordinal baseline.
  - [ ] Baseline 2: distance/proximity-only ordinal baseline.
  - [ ] Produce fold-wise and pooled comparison tables.
- [ ] Evaluate metric families.
  - [ ] Probabilistic metrics (primary acceptance basis).
  - [ ] Classification summaries (confusion-derived + weighted kappa).
  - [ ] Calibration diagnostics (including `DMR>=3` threshold focus).
- [ ] Regen sensitivity branch.
  - [ ] Primary run on full thesis dataset (measured + simulated).
  - [ ] Sensitivity run on measured-only subset.
  - [ ] Quantify simulated-record dependence.
- [ ] Deliverable checkpoint (target: May 9, 2026).
  - [ ] LOSO results table complete.
  - [ ] Baseline comparison package complete.

## Phase 3 — Acceptance Gate & Conditional Refit
- [ ] Execute acceptance gate per submodel.
  - [ ] Confirm median LOSO performance beats best baseline.
  - [ ] Confirm >=60% fold-win against best baseline.
  - [ ] Confirm no major calibration failure (after optional recalibration).
  - [ ] Emit gate decision artifact: `accept_current` or `trigger_refit`.
- [ ] If refit triggered, run constrained ordinal candidate workflow.
  - [ ] Proportional-odds mixed variants.
  - [ ] Partial proportional-odds where assumption violations are material.
  - [ ] Alternative link (e.g., cloglog) only when held-out performance improves.
  - [ ] Regularization/shrinkage where needed for stability.
- [ ] Rank and select final predictive model(s).
  - [ ] Rank by held-out probabilistic performance.
  - [ ] Tie-break with calibration, then classification summaries.
  - [ ] Preserve ecological interpretability as hard requirement.
- [ ] Deliverable checkpoint (target: May 11-14, 2026).
  - [ ] Acceptance decisions documented.
  - [ ] Refit comparison table complete (if triggered).

## Phase 4 — Stand-Level Risk Outputs & Manuscript Integration
- [ ] Produce stand-level risk range outputs.
  - [ ] Aggregate tree-level probabilities to stand-level distributions.
  - [ ] Output incidence, `DMR>=3` incidence, and mean DMR distributions.
  - [ ] Report 50/80/95 intervals for each stand/scenario.
- [ ] Run stand-level quality controls.
  - [ ] Aggregation correctness tests.
  - [ ] Interval behavior diagnostics.
  - [ ] Dual-submodel protocol consistency checks.
- [ ] Build manuscript-ready outputs.
  - [ ] Methods text blocks for validation-first governance and gate logic.
  - [ ] Results tables/figures for baseline comparisons and risk ranges.
  - [ ] Limitations section language (no new data, simulation assumptions, transferability bounds).
- [ ] Deliverable checkpoint (target: May 16, 2026).
  - [ ] Stand-level result package complete.
  - [ ] Sensitivity package complete.

## Phase 5 — Statistical Consultation, Decision Lock, and Timeline Reset
- [ ] Prepare Bianca briefing package.
  - [ ] One-page decision memo (acceptance gate + rationale).
  - [ ] Explicit questions: gate adequacy, metric choice, calibration treatment, candidate sufficiency.
  - [ ] Attach fold-level and pooled metric tables.
- [ ] Resolve open statistical decisions.
  - [ ] Incorporate feedback and update final model governance.
  - [ ] Lock any modified acceptance/refit criteria.
  - [ ] Update manuscript claims if needed.
- [ ] Finalize next project timeline.
  - [ ] Internal checkpoint with all collaborators (target: May 17, 2026).
  - [ ] Replace provisional dates with committed timeline for drafting/submission.
  - [ ] Update roadmap and planning doc status to reflect new baseline.

## Detailed Next Steps
1. **Data Freeze Manifest**
   - Generate manifest with checksums/row counts and commit it as the Phase 1 completion artifact.
2. **LOSO Engine Setup**
   - Build fold assignment tables and run leakage + reproducibility checks before any model scoring.
3. **Baseline Pack**
   - Implement prevalence-only and distance/proximity-only baselines for both submodels.
4. **Primary + Sensitivity Evaluation**
   - Run regen full dataset and measured-only sensitivity branch in parallel result bundles.
5. **Acceptance Gate Execution**
   - Produce submodel-by-submodel pass/fail decisions with fold-level evidence and calibration notes.
6. **Refit Fallback Readiness**
   - Prepare constrained ordinal candidate templates so refit can start immediately if gate fails.
7. **Stand-Level Output Pipeline**
   - Wire aggregation and interval reporting, then validate with stand-level diagnostics.
8. **Bianca Decision Brief**
   - Deliver memo and targeted question set with appendixed metric tables.

## Backlog & Ideas
- [ ] Add an automated reporting script that regenerates all metric/gate tables from frozen artifacts.
- [ ] Add a compact dashboard notebook for fold-level diagnostics and calibration plots.
- [ ] Add a release-style "analysis changelog" section for tracking modeling decision deltas over time.
- [ ] Add a manuscript asset registry (table/figure IDs -> source scripts/files) once writing enters active drafting.
