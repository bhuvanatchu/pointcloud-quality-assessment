# No-Reference Geometric Quality Assessment of Raw 3D Point Clouds

Code for the paper:

> B. Bhuvanavathi, T. Kamaleshwar and G. R. Kanagachidambaresan.
> *No-reference geometric quality assessment of raw 3D point clouds validated against independent reconstruction outcomes.* (submitted)

The repository computes a no-reference composite quality index (CQI) for raw point clouds from seven interpretable geometric descriptors in physical units, and evaluates it against an **independent** ground truth: the F-score of a frozen, automatic surface reconstruction (Screened Poisson and ball pivoting) relative to the reference surface. The index is never used to create its own labels.

## Repository contents

| File | Purpose |
|---|---|
| `pointcloud_quality_assessment.ipynb` | Complete pipeline in one notebook (stages 1–7 below); writes all library modules itself |
| `thingi_selection_with_license.csv` | The 150 Thingi10K file IDs, Thing IDs and licences |

| Stage | Content | Hardware | Time (4 CPUs) |
|---|---|---|---|
| 1 | Controlled benchmark: 150 Thingi10K + 4 Stanford models × 46 variants, descriptors, frozen Poisson / ball-pivoting ground truth, closed-loop corrections | CPU | ≈ 14 h (resumable) |
| 2 | Benchmark analysis: fusion, cross-validation, specificity, attribution, sensitivity, runtime | CPU | ≈ 30 min |
| 3 | Real-data validation on DTU MVS 2014 (frozen weights), 22 standard scenes | CPU | ≈ 1 h |
| 3b | Extended DTU validation: all 80 scenes with all three MVS reconstructions (240 clouds) | CPU | ≈ 2–4 h |
| 4 | Correction policies, noise-estimator baselines, DTU robustness | CPU | ≈ 2–3 h |
| 5 | PointNet (2048 / 8192 points) and hybrid PointNet + descriptors | GPU | ≈ 3 h |
| 6 | Exploratory unsupported surface fraction | CPU | ≈ 2.5 h |
| 7 | All figures 1–10 and S1 (600 dpi PNG + TIFF) | CPU | ≈ 15 min |

## Reproducing the results

The experiments were run on [Kaggle](https://www.kaggle.com) notebooks.

1. Import `pointcloud_quality_assessment.ipynb` into a Kaggle notebook and set *Internet = On* (Accelerator *None*; *GPU* only for stage 5).
2. Choose the stages to run with the `RUN` switches in the first code cell, then *Save Version → Save & Run All*.
3. Kaggle sessions last at most 12 h, so run the stages over several sessions: attach the previous version's output as an *Input* and continue. Finished objects and scans are detected and skipped automatically.

All data are downloaded automatically. Random seeds are fixed, Poisson-disk sampling is seeded and the Thingi10K selection is deterministic, so every corrupted cloud is regenerated exactly.

### Running locally

```bash
pip install open3d trimesh rtree scipy scikit-learn pandas matplotlib joblib remotezip stream-unzip torch
jupyter notebook pointcloud_quality_assessment.ipynb
```
Change the working directory `/kaggle/working` in the first code cell to a local folder.

## Main results

| Setting | CQI-learned | CQI-v1 (earlier formulation) |
|---|---|---|
| Thingi10K, 5-fold object-disjoint CV (SRCC with RQ) | **0.787** [0.761, 0.813] | 0.623 |
| Held-out Stanford models (SRCC) | **0.788** | 0.592 |
| DTU, 22 standard scenes, frozen weights (SRCC with RQ, Poisson / BPA) | 0.445 / 0.560 | 0.611 / 0.552 |
| DTU, all 80 scenes (240 clouds), frozen weights (SRCC with RQ, Poisson / BPA) | 0.379 / 0.504 | 0.503 / 0.515 |
| DTU, within-scene agreement with accuracy / completeness (80 scenes) | 81% / 38% | – |
| PointNet + descriptors (hybrid), Thingi10K CV | 0.814 | – |

The noise descriptor estimates sensor noise in millimetres (median relative error 12%). On real MVS data the index tracks accuracy but not completeness; see the paper for the full analysis, including negative results.

## Data and licences

- **Thingi10K** (Zhou and Jacobson, 2016): models are used under their original licences, listed per model in `thingi_selection_with_license.csv`. Because several models carry No-Derivatives or Share-Alike terms, **derived point clouds are not redistributed**; the code regenerates them.
- **DTU MVS 2014** (Jensen et al., 2014; Aanæs et al., 2016): downloaded from the DTU Robot Image Data Sets website.
- **Stanford 3D Scanning Repository**: Bunny and Armadillo via Open3D; Dragon and Happy Buddha from the repository.

Please cite the original datasets when using this code.

## Citation

```bibtex
@article{bhuvanavathi2026pointcloud,
  author  = {Bhuvanavathi, B. and Kamaleshwar, T. and Kanagachidambaresan, G. R.},
  title   = {No-reference geometric quality assessment of raw 3D point clouds validated against independent reconstruction outcomes},
  journal = {Visual Computing for Industry, Biomedicine, and Art (submitted)},
  year    = {2026}
}
```

## Licence

Code released under the MIT Licence (see `LICENSE`).

## Contact

B. Bhuvanavathi – bhuvana.aug13@gmail.com
