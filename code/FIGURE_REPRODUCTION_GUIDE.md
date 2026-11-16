# PISCO Figure Reproduction Guide

## Figure number, script, parameters and outputs

**Manuscript:** PISCO \
**Supplementary Information:** PISCO Supplementary Information \
**Code archive:** `PISCO_code.zip`

This guide follows the experiment descriptions, data dimensions,
training schedules and parameter values reported in the manuscript
and Supplementary Information. MATLAB script paths and output filenames
identify the corresponding entry points and products in the supplied
code archive.

------------------------------------------------------------------------

## 1. Common model configuration

Unless a figure or experiment specifies otherwise, use the defaults in
Supplementary Table 1.

  ------------------------------------------------------------------------------------
  Parameter                                                         Manuscript default
  -------------------------------------------------------- ---------------------------
  Total iterations                                                               2,500

  Warm-up                                                               600 iterations

  Joint optimization                                                  1,900 iterations

  Adam learning rate                                         (3`\times10`{=tex}\^{-4})

  (`\lambda`{=tex}\_{`\mathrm{vol}`{=tex}})                                      0.008

  (`\lambda`{=tex}\_{`\mathrm{nonneg}`{=tex}})                                       3

  (`\lambda`{=tex}\_{`\mathrm{eq}`{=tex}})                                         100

  (`\lambda`{=tex}\_{`\mathrm{sumone}`{=tex}})                                       2

  (`\lambda`{=tex}\_{`\mathrm{spatial}`{=tex}})                                      2

  (`\sigma`{=tex}\_{`\mathrm{cap}`{=tex}})                                        0.03

  (`\eta`{=tex})                                                                   0.3

  (`\lambda`{=tex}\_{`\mathrm{mnn}`{=tex}})                                         10

  (`\lambda`{=tex}\_{`\mathrm{smooth}`{=tex}})                                     0.1

  (`\lambda`{=tex}\_{`\mathrm{enclose}`{=tex}})                     0.3; 10 for i.i.d.
                                                            synthetic abundance fields

  (`\lambda`{=tex}\_{`\mathrm{reg}`{=tex}})                                (10\^{-10})

  Adam                                                          ((0.9,0.999,10\^{-8}))
  ((`\beta`{=tex}\_1,`\beta`{=tex}\_2,`\epsilon`{=tex}))   

  Gradient batch                                                     Full field, (B=N)
  ------------------------------------------------------------------------------------

### Sensitivity search ranges

  Parameter          Search range
  ---------------- --------------
  lambda_vol              0--0.08
  lambda_nonneg           0.3--30
  lambda_eq             10--1,000
  lambda_sumone           0.2--20
  lambda_spatial            0--20
  sigma_cap                  0--1
  eta                    0.1--0.5
  lambda_mnn               0--100
  lambda_smooth              0--1
  lambda_enclose           0--100

**Abundance mode**

-   Simulated data with a complete known endmember set: closure mode,
    with the sum-to-one penalty active.
-   Real biological data and data with unlisted emitters or instrument
    responses: free-coefficient mode, with the sum-to-one penalty and
    affine-offset correction deactivated.

**Synthetic-data conventions**

-   Four fluorescent-protein endmembers: Citrine, tdTomato, mKO2 and
    mRuby.
-   32-channel Zeiss Quasar spectral geometry: 410.5--694.9 nm,
    approximately 8.9 nm/channel.
-   Spatially correlated abundance fields derived from BBBC038/039
    masks.
-   Maximum abundance (purity) capped at 0.8; no pure pixels.
-   Poisson photon noise plus Gaussian detector read-out noise with
    (`\sigma`{=tex}\_{`\mathrm{read}`{=tex}}=1) count, unless the
    experiment explicitly changes the noise model.
-   Photon budgets are mean photons per spectral channel.
-   Synthetic-data comparisons use 30 independent Monte Carlo
    realizations unless otherwise specified.

------------------------------------------------------------------------

## 2. Main-text figures

### Fig. 1 --- PISCO method overview

  ---------------------------------------------------------------------
  Item                               Reproduction information
  ---------------------------------- ----------------------------------
  Content                            HFI data cube; HySime
                                     dimensionality estimation;
                                     projection and spherization; VCA
                                     initialization; invertible
                                     coupling (Q=LQ_0R); joint
                                     objective; endmember spectra and
                                     abundance maps; comparison with
                                     alternating solvers; Poisson
                                     intensity-dependent margins

  MATLAB script                      No numerical experiment driver;
                                     this is a method schematic

  Parameters                         Method defaults in Supplementary
                                     Table 1; two-phase schedule of 600
                                     warm-up and 1,900 joint iterations

  Output                             Fig. 1 architecture and method
                                     illustration
  ---------------------------------------------------------------------

### Fig. 2 --- Simulated low-photon fluorescence benchmark

**Entry script**

`PISCO_code/PISCO_E1/demo_E1_compare_PISCO_LU_SISAL_RMVSA_HyU_NMF_biomask_GYL.m`

**Data and protocol**

-   32-channel simulated HFI data with four endmembers: Citrine,
    tdTomato, mKO2 and mRuby.
-   Spatially correlated abundance fields derived from BBBC038/039
    segmentation masks.
-   Field size: 96 × 96 pixels.
-   Maximum abundance: 0.8.
-   Mean photon budgets: 2, 5, 10, 20, 50 and 100 photons per channel.
-   Poisson photon noise and Gaussian read-out noise,
    (`\sigma`{=tex}\_{`\mathrm{read}`{=tex}}=1) count.
-   30 independent Monte Carlo realizations per photon budget.
-   Methods: PISCO, supervised LU, supervised HyU, NMF, SISAL and RMVSA.
-   PISCO uses the closure-mode formulation for the complete simulated
    endmember set.

**Parameters**

Use the common PISCO defaults in Section 1. The photon budget is varied
over `[2, 5, 10, 20, 50, 100]`; all other simulation settings follow the
Fig. 2 protocol above.

**Outputs**

-   `10compare_E1_PISCO_LU_SISAL_RMVSA_HyU_NMF_accuracy_results.csv`
-   `10compare_E1_PISCO_LU_SISAL_RMVSA_HyU_NMF_per_pixel_residuals.csv`
-   `10compare_E1_PISCO_LU_SISAL_RMVSA_HyU_NMF_abundance_maps_20photons_run1.csv`
-   `10compare_E1_PISCO_LU_SISAL_RMVSA_HyU_NMF_plotdata.mat`

**Figure panels**

-   **a:** simulated 32-channel dataset, four ground-truth abundance
    maps and merged rendering.
-   **b:** ground truth and the six methods' abundance maps at 20 mean
    photons/channel.
-   **c:** endmember SAD versus photon budget.
-   **d:** abundance MSE versus photon budget.
-   **e:** per-pixel unmixing-residual distributions at 5, 10 and 20
    mean photons/channel.

### Fig. 3 --- Scaling and component ablations

#### Fig. 3a --- Accuracy versus field size

**Entry script**

`PISCO_code/PISCO_E2/demo_pisco_E2_scalability_one_method_workspace_biomask_GYL.m`

**Data and protocol**

-   Pixel counts: (N `\in`{=tex}{10\^4, 3`\times10`{=tex}\^4, 10\^5,
    3`\times10`{=tex}\^5, 10\^6, 4`\times10`{=tex}\^6}).
-   Square fields from 100 × 100 to 2,000 × 2,000 pixels.
-   20 mean photons per channel.
-   Poisson noise and unit read-out standard deviation.
-   30 Monte Carlo realizations per method and field size.
-   Methods: PISCO, SISAL and RMVSA.
-   Full-field gradients over all (N) pixels per iteration; double
    precision; CPU execution.
-   Reported hardware: Intel Core i7-10700 CPU and 32 GB RAM; MATLAB
    R2023a.

**Parameters**

Use 2,500 total PISCO iterations (600 warm-up and 1,900 joint
iterations), 20 mean photons/channel, and unit read-out standard
deviation. Evaluate all six pixel counts and all three methods.

**Outputs**

-   Per-method/per-size result files: `E_E2_<METHOD>_N<N>_results.csv`
-   Per-run endmember SAD, abundance MSE, reconstruction MSE,
    optimization workspace and wall-clock measurements.

#### Fig. 3b --- Spatial-coherence ablation

**Entry script**

`PISCO_code/PISCO_E3/Copy_of_demo_pisco_E3_ablation_b1_biomask_GYL_GYL.m`

**Protocol**

-   Fixed mask-derived simulated scene, 512 × 512 pixels.
-   10 mean photons per channel.
-   30 paired noise realizations.
-   Compare the full model with the spatial-coherence term removed
    ((`\lambda`{=tex}\_{`\mathrm{spatial}`{=tex}}=0)).

**Outputs**

-   `pisco_E3_ablation_v13_biomask_results_Q0.csv`
-   Endmember SAD and abundance MSE for the full and
    spatial-term-ablated configurations.

#### Fig. 3c --- Warm-up ablation

**Entry script**

`PISCO_code/PISCO_E3/Copy_of_demo_pisco_E3_ablation_b1_biomask_GYL_GYL.m`

**Protocol**

-   Same 512 × 512 mask-derived scene and 10 mean photons/channel as
    Fig. 3b.
-   30 paired noise realizations.
-   Compare the full model (600 warm-up iterations) with the no-warm-up
    configuration.

**Outputs**

-   `pisco_E3_ablation_v13_biomask_results_Q0.csv`
-   Endmember SAD and abundance MSE for the full and no-warm-up
    configurations.

### Fig. 4 --- Blind unmixing of four-colour in-vivo zebrafish imaging

**Entry script**

`PISCO_code/PISCO_E9/demo_pisco_E9_A_GYL66_E1_L2_residual_with_ROI5_NoASC.m`

**Input data**

`fig2_e_01-fish5-muscle-fl.lsm`

-   1,024 × 1,024 pixels.
-   32 spectral channels, 410.5--694.9 nm, approximately 8.9 nm/channel.
-   Single-photon 488/561-nm excitation.
-   Quadruple-transgenic zebrafish embryo.
-   Main-text field: z-section 13.
-   HySime endmember estimate: (p=5).

**Parameters**

-   Free-coefficient mode.
-   Seed: 1001.
-   2,500 iterations: 600 warm-up and 1,900 joint.
-   Common PISCO defaults in Supplementary Table 1.
-   No sum-to-one penalty and no affine-offset correction.

**Outputs**

-   Self-contained run/plot data: `pisco_E9_A_E1_L2_residual_NoASC.mat`
-   Run metrics: `pisco_E9_A_NoASC_results.csv`
-   Rendered abundance maps, endmember spectra, magnified ROI and
    residual visualizations.

The supplied MATLAB driver also writes
`pisco_E9_A_NoASC_SAD_greedy_matches.csv` as an additional post-training
comparison against the four-spectrum MAT library. This is an exploratory
code diagnostic, not a reported v92 manuscript accuracy metric. The
manuscript reports the five endmembers anonymously and does not report
SAD scoring against resampled library spectra, because instrument-matched
single-label reference acquisitions are unavailable for this field.

**Figure panels**

-   **a:** additive RGB overlay of the five extracted endmembers over
    the full field.
-   **b:** individual model-intensity renderings of EM1--EM5.
-   **c:** magnified composite of the marked ROI.
-   **d:** magnified individual endmember renderings.
-   **e:** five blindly extracted endmember spectra.

Endmembers are reported anonymously as EM1--EM5. Residual distributions
and the closure/free-coefficient comparison are shown in Supplementary
Fig. 8.

### Fig. 5 --- Two-photon multiplexing of intrinsic and extrinsic signals

**Entry script**

`PISCO_code/PISCO_E9/B/demo_pisco_E9_B1_NoASC_BandLoss_FixedQTail_FinalOnly_GYL.m`

**Input data**

`fig5_e_01-fish1-four-hinbrain_eye-autofl.lsm`

-   2,048 × 2,560 pixels (5,242,880 pixels; 5.2 megapixels).
-   32 spectral channels, 410.5--694.9 nm.
-   Two-photon excitation at 740 nm.
-   Hindbrain--eye region.
-   Main-text field: z-section 12.
-   Fixed endmember number: `p = 8` (HySime estimated `p = 6`).
-   Training seed: `1001`.

**Run settings for the main-text Fig. 5 result**

Use the complete z = 12 field and the manuscript-reported training configuration. Set the B1 driver as follows:

```matlab
QUICK = false;        % complete 2048 x 2560 field
Z_PLANES = 12;
P_METHOD = 'fixed';
P_FIXED = 8;
SEEDS = [1001];

OPT.MM_ITERS = 2500;
OPT.WARM_UP = 600;
OPT.LAMBDA_BAND = 0;
OPT.LAMBDA_ENCLOSE = 3;
OPT.LAMBDA_SPATIAL = 100;
OPT.ETA = 0.3;
OPT.ETA_END = 0.3;
OPT.SIGMA_CAP = 0.15;
OPT.TAIL_ITERS = 0;
```

**Parameters**

-   Free-coefficient mode (NoASC); the sum-to-one penalty is deactivated.
-   2,500 total iterations: 600 warm-up and 1,900 joint iterations.
-   `lambda_enclose = 3`.
-   `lambda_spatial = 100`.
-   `sigma_cap = 0.15`.
-   Other settings follow Supplementary Table 1: `lambda_mnn = 10`,
    `lambda_smooth = 0.1`, `eta = 0.3`; Poisson noise model with uniform
    weighting.
-   GPU execution on NVIDIA RTX 4060, single precision.
-   Signal-mask threshold: total intensity above the 25th percentile;
    3,931,317 pixels (75.0%) are retained for the reported residual
    evaluation. The mask is used for evaluation/visualization, not as a
    training crop.
-   The acquisition is not photon-calibrated. The reported mean signal is
    approximately `5.3 × 10^2` detector counts per channel, not a photon
    budget.

**Outputs**

The B1 driver writes results under:

`E9_B1_NoASC_BandLoss_FixedQTail_FinalOnly_output/band_0_sp_100_enc_3_tail_0_lr_0.0003/`

The main run (`z = 12`, run 1, seed 1001) produces:

-   `pisco_E9_B1_Z12_run01_full.mat`
-   `pisco_E9_B1_Z12_run01_abundances.png`
-   `pisco_E9_B1_Z12_run01_spectra.png`
-   `pisco_E9_B1_Z12_run01_residual.png`
-   `pisco_E9_B1_Z12_run01_FixedQTail_residual_comparison.png`
-   `pisco_E9_B1_Z12_run01_composition_proxy.png`
-   `pisco_E9_B1_results.csv`
-   `pisco_E9_B1_summary.csv`

**Figure panels**

-   **a:** additive merged rendering of the eight blindly unmixed
    components EM01--EM08.
-   **b:** individual free-coefficient maps EM01--EM08.
-   **c:** eight extracted endmember spectra on the 32-channel detector
    grid.
-   **d:** magnified merged field and EM08.
-   **e:** ratio map of the two putative NADH-associated coefficients,
    `free / (free + bound)`, displayed after Gaussian smoothing with
    `sigma = 4` pixels. In the manuscript's provisional peak-position
    interpretation, this is visualized as `EM05 / (EM05 + EM01)`.

Panel **e** is a separate post-processing visualization of the trained
abundance coefficients and is not generated by the B1 driver by default.
The EM01/EM05 NADH identities remain hypotheses rather than
spectroscopic identifications. The ratio map is therefore not evidence
for a quantitative redox gradient and must not be interpreted as one.

------------------------------------------------------------------------

### Fig. 6 --- Controlled validation on public hyperspectral benchmarks

#### Fig. 6a--d --- Five-label cultured-cell benchmark

**Entry script**

`PISCO_code/PISCO_E14/demo_pisco_E14_LysoSeed_GroupC_MAT_complete_GYL_NoASC.m`

**Input data**

The five-label PhasorPy benchmark and five single-stain reference images
from Zenodo records 13625087 and 14860228.

-   28 channels.
-   256 × 256 pixels.
-   Labels: Hoechst, Golgi, MitoTracker, LysoTracker and CellMask.
-   HySime estimate: (p=5); training dimensionality: (p=6).

**Parameters**

-   Free-coefficient mode.
-   Displayed seed: 7007 (the best run of the ten-seed campaign).
-   (`\sigma`{=tex}\_{`\mathrm{cap}`{=tex}}=0.15); other settings follow
    Supplementary Table 1.
-   VCA initialization with one guided LysoTracker reference axis; the
    other components are initialized without reference spectra.

**Outputs**

-   `pisco_E14_C_LysoSubspace_NoASC_results/`
-   `pisco_E14_C_LysoSubspace_NoASC_results.csv`
-   `pisco_E14_C_LysoSubspace_NoASC_refspectra.csv`
-   `pisco_E14_C_LysoSubspace_NoASC_Lyso_init_final_diagnostic.csv`
-   Per-run MAT files and PNG panels for the rendering, abundance maps,
    spectra, residual and initialization diagnostics.

**Figure panels**

-   **a:** RGB rendering of the five-label cell field.
-   **b:** six coefficient maps, including the autofluorescence
    component.
-   **c:** dimensionality analysis, showing HySime (p=5) and working
    (p=6).
-   **d:** extracted spectra compared with single-stain reference
    spectra.

#### Fig. 6e--h --- FocalCheck DoubleGreen microsphere phantom

**Entry script**

`PISCO_code/PISCO_E14/B/demo_pisco_E14_beads_realTIFF_with_fullMAT_GYL_NoASC.m`

**Input data**

FocalCheck DoubleGreen single-dye and mixed core--shell TIFF stacks.

-   16 channels.
-   513 × 513 pixels.
-   Two dye populations: 512-nm shell and 525-nm core.
-   Three model components: two dyes plus background.

**Parameters**

-   Free-coefficient mode.
-   (p=3).
-   Seed: 1001.
-   550 iterations total, including 500 warm-up iterations.
-   (`\sigma`{=tex}\_{`\mathrm{cap}`{=tex}}=0.15).

**Outputs**

-   `pisco_E14_beads_results.csv`
-   `pisco_E14_bead_refspectra.csv`
-   `pisco_E14_beads_run1.mat`
-   `pisco_E14_figE_bead.png`
-   `pisco_E14_figF_radial.png`
-   `pisco_E14_figG_bead_spectra.png`

**Figure panels**

-   **e:** RGB rendering and the two separated bead components.
-   **f:** spectral angle distances against single-dye reference
    spectra.
-   **g:** extracted bead spectra and single-dye references.
-   **h:** radial mean abundance profiles showing the 525-nm core and
    512-nm shell.

------------------------------------------------------------------------

## 3. Extended Data figures

### Extended Data Fig. A1 --- Noise-model robustness

**Entry script**

`PISCO_code/PISCO_E4/demo_pisco_E4_noise_biomask_2_GYL.m`

**Protocol**

-   100 × 100-pixel simulated fields.
-   20 mean photons per channel.
-   Poisson, Gaussian and mixed Poisson--Gaussian noise.
-   30 paired Monte Carlo realizations per condition.
-   Compare the default Poisson-derived chance-constraint margins with
    Anscombe variance-stabilizing-transform preprocessing.

**Parameters**

Use the common PISCO defaults, with
(`\sigma`{=tex}\_{`\mathrm{read}`{=tex}}=1) count and the stated
noise-model/transform arms.

**Output**

`pisco_E4_noise_results2.csv`

The three metrics are endmember SAD, abundance MSE and reconstruction
MSE against the noise-free ground truth.

### Extended Data Fig. A2 --- Endmember-number misspecification

**Entry script**

`PISCO_code/PISCO_E5/demo_pisco_E5_misspec_biomask_GYL.m`

**Protocol**

-   Four true endmembers.
-   100 × 100-pixel simulated fields.
-   20 mean photons per channel.
-   30 paired Monte Carlo realizations.
-   Endmember-number offsets: (`\Delta`{=tex}p=-2,-1,0,+1,+2).
-   HySime is re-estimated for each noise realization and returns the
    correct (p=4) in all 30 realizations.

**Parameters**

All settings follow the common PISCO defaults. Each experiment arm uses
the HySime estimate plus the specified (`\Delta`{=tex}p), with a minimum
supplied endmember number of two.

**Output**

`pisco_E5_misspec_results.csv`

The reported metrics are endmember SAD, abundance MSE and reconstruction
MSE.

### Extended Data Fig. A3 --- Scaling resource requirements

**Entry script**

`PISCO_code/PISCO_E2/demo_pisco_E2_scalability_one_method_workspace_biomask_GYL.m`

**Protocol**

-   (N=10^4,3`\times10`{=tex}^4,10^5,3`\times10`{=tex}^5,10^6,4`\times10`{=tex}^6)
    pixels.
-   30 realizations per method and field size.
-   PISCO, SISAL and RMVSA.
-   20 mean photons per channel; unit read-out standard deviation.
-   Full-field gradients, double precision, CPU execution.
-   Intel Core i7-10700 CPU and 32 GB RAM.

**Outputs**

Per-method/per-size CSV files `E_E2_<METHOD>_N<N>_results.csv`,
containing peak optimization workspace, wall-clock time and per-phase
runtime measurements.

### Extended Data Fig. A4 --- Abundance-field structure controls

#### Fig. A4a--c --- Spatially correlated versus i.i.d. fields

**Entry script**

`PISCO_code/PISCO_E11/demo_pisco_E11_structure_biomask_enclose_GYL.m`

**Protocol**

-   100 × 100-pixel fields.
-   20 mean photons per channel.
-   30 paired Monte Carlo realizations.
-   Compare spatially correlated mask-derived abundance fields with
    structure-free i.i.d. Dirichlet(1) fields.
-   Structured fields: (`\lambda`{=tex}*{`\mathrm{spatial}`{=tex}}=2),
    (`\lambda`{=tex}*{`\mathrm{enclose}`{=tex}}=0.3).
-   i.i.d. fields: (`\lambda`{=tex}*{`\mathrm{spatial}`{=tex}}=0),
    (`\lambda`{=tex}*{`\mathrm{enclose}`{=tex}}=10).

**Outputs**

-   `pisco_E11_structure_results.csv`
-   `pisco_E11_fig1_arms.png`
-   `pisco_E11_fig2_maps.png`

#### Fig. A4d--f --- Spatial penalty on structure-free fields

**Entry script**

`PISCO_code/PISCO_E3/add_demo_pisco_E2_iid_spatial_paired_GYL.m`

**Protocol**

-   Same 100 × 100 i.i.d. Dirichlet(1) fields.
-   20 mean photons per channel.
-   30 paired realizations; seeds 1001--1030.
-   Compare (`\lambda`{=tex}*{`\mathrm{spatial}`{=tex}}=0) with
    (`\lambda`{=tex}*{`\mathrm{spatial}`{=tex}}=2); all other settings
    are held identical.

**Output**

-   `E2_iid_spatial_results/pisco_E2_iid_spatial_paired.mat`

The MAT file contains paired metrics and protocol metadata for the
complementary control.

### Extended Data Fig. A5 --- Reference-anchored 13-plex MultiView benchmark

**Entry script**

`PISCO_code/PISCO_MultiView/demo_pisco_MultiView_RefInit_13EM_PlotOnly_v3.m`

**Protocol**

-   Thirteen single-stain references and mixed *E. coli* fields.
-   Five analysed excitation views:
    -   488/561/639 nm combined: 23 channels;
    -   445 nm: 28 channels;
    -   488 nm: 23 channels;
    -   514 nm: 20 channels;
    -   561 nm: 14 channels.
-   (p=13), seed 1001, free-coefficient mode.
-   Reference spectra initialize the endmember matrix; the PISCO
    objective is then optimized without a reference-dependent term.
-   Endmember identities are assigned post hoc by Hungarian one-to-one
    spectral-angle matching.
-   The combined view uses the extended schedule of 6,800 warm-up plus
    700 joint iterations; the four single-laser views use the default
    600 + 1,900 schedule.

**Outputs**

Under `MultiView_PISCO_RefInit_13EM_PlotOnly_output/`:

-   `multiview_summary.csv`
-   Per-view/per-seed `sad_matches.csv`
-   `preview.png`
-   `abundance_13_endmembers.png`
-   `plot_global.mat`
-   `abundance_<DYE>.mat` for each of the thirteen dyes.

The combined view is a reference-anchored refinement benchmark; the
reported SAD values quantify the refined spectra relative to the
single-stain references.

------------------------------------------------------------------------

## 4. Supplementary figures

  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  SI figure       Content               Entry script                                                                         Parameters /       Main outputs
                                                                                                                             protocol           
  --------------- --------------------- ------------------------------------------------------------------------------------ ------------------ ------------------------------------------------
  Supplementary   PISCO architecture    No experiment driver                                                                 Five principal     Architecture schematic
  Fig. 1          and objective-term                                                                                         objective terms;   
                  schematic                                                                                                  (Q=LQ_0R); 600     
                                                                                                                             warm-up + 1,900    
                                                                                                                             joint iterations   

  Supplementary   Training dynamics     `PISCO_code/PISCO_E12/demo_pisco_E12_dynamics_biomask_GYL.m`                         96 × 96; 32        `pisco_E12_dynamics_losslog2.csv`,
  Fig. 2                                                                                                                     channels; (p=4);   `pisco_E12_dynamics_summary2.csv`,
                                                                                                                             20 mean            `pisco_E12_fig1_curves2.png`,
                                                                                                                             photons/channel;   `pisco_E12_fig2_band2.png`
                                                                                                                             unit read noise;   
                                                                                                                             30 runs on         
                                                                                                                             rotating           
                                                                                                                             mask-derived       
                                                                                                                             scenes; 600 +      
                                                                                                                             1,900 iterations   

  Supplementary   Ten-parameter         `PISCO_code/PISCO_E6/demo_pisco_E6_sensitivity_biomask_extended_GYL.m`               20 mean            `pisco_E6_sensitivity_results_extended1.csv`
  Fig. 3          sensitivity analysis                                                                                       photons/channel;   
                                                                                                                             30 paired          
                                                                                                                             realizations per   
                                                                                                                             level;             
                                                                                                                             one-at-a-time scan 
                                                                                                                             of all ten         
                                                                                                                             parameters in      
                                                                                                                             Supplementary      
                                                                                                                             Table 1            

  Supplementary   Principal-component   `PISCO_code/PISCO_E3/Copy_of_demo_pisco_E3_ablation_b1_biomask_GYL_GYL.m`            512 × 512; 10 mean `pisco_E3_ablation_v13_biomask_results_Q0.csv`
  Fig. 4          ablation                                                                                                   photons/channel;   
                                                                                                                             30 paired          
                                                                                                                             realizations on a  
                                                                                                                             fixed mask-derived 
                                                                                                                             scene; eight       
                                                                                                                             ablation arms plus 
                                                                                                                             full model         

  Supplementary   Per-realization       `PISCO_code/PISCO_E2/demo_pisco_E2_scalability_one_method_workspace_biomask_GYL.m`   Same scaling       E2 per-method/per-size CSV files
  Fig. 5          accuracy                                                                                                   campaign as Fig.   
                  distributions across                                                                                       3a; 30             
                  field sizes                                                                                                realizations per   
                                                                                                                             method and size    

  Supplementary   Single-photon         `PISCO_code/PISCO_E13/demo_pisco_E13_SP_zsections_crossZmatched.m`                   z = 11--15; (p=5); Per-z MAT files; four CSV files;
  Fig. 6          within-volume                                                                                              seed 1001;         `E13_SP_plotdata.mat`; four SVG figures
                  z-section consistency                                                                                      free-coefficient   
                                                                                                                             mode; z = 13       
                                                                                                                             anchor             

  Supplementary   Runtime breakdown     `PISCO_code/PISCO_E2/demo_pisco_E2_scalability_one_method_workspace_biomask_GYL.m`   Same scaling       E2 CSV runtime fields and per-phase timing
  Fig. 7          versus pixel count                                                                                         campaign as        summaries
                                                                                                                             Extended Data Fig. 
                                                                                                                             A3; 600 warm-up +  
                                                                                                                             1,900 joint        
                                                                                                                             iterations         

  Supplementary   Closure-mode artefact `PISCO_code/PISCO_E9/demo_pisco_E9_A_GYL66_E1_L2_residual_with_ROI5_NoASC.m` and the Same single-photon Residual maps, residual distributions,
  Fig. 8          versus                corresponding closure-mode run                                                       z = 13 field;      intensity-resolved residual analysis and
                  free-coefficient                                                                                           compare closure    coefficient-sum diagnostics
                  residual analysis                                                                                          and                
                                                                                                                             free-coefficient   
                                                                                                                             modes; residuals   
                                                                                                                             computed from      
                                                                                                                             trained solutions  
                                                                                                                             without supervised 
                                                                                                                             refitting or       
                                                                                                                             intensity          
                                                                                                                             alignment          

  Supplementary   MultiView             `PISCO_code/PISCO_MultiView/demo_pisco_MultiView_RefInit_13EM_PlotOnly_v3.m`         Five analysed      Per-view abundance maps and MultiView output
  Fig. 9          winner-take-all dye                                                                                        views; (p=13);     MAT/CSV files
                  maps across five                                                                                           seed 1001;         
                  views                                                                                                      free-coefficient   
                                                                                                                             mode; same-view    
                                                                                                                             single-stain       
                                                                                                                             references         

  Supplementary   Two-photon            `PISCO_code/PISCO_E13/TP/demo_pisco_E13_TP_SPstyle_minimal95MB_GYL.m`                z = 8--12; (p=8);  Per-z core and abundance MAT files; four CSV
  Fig. 10         within-volume                                                                                              seed 1001;         files; `E13_TP_plotdata.mat`; four SVG figures
                  z-section consistency                                                                                      free-coefficient   
                                                                                                                             mode; z = 12       
                                                                                                                             anchor; Fig. 5     
                                                                                                                             training schedule  
                                                                                                                             and                
                                                                                                                             hyperparameters    
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Additional Supplementary Note 1.2 --- Propagated-margin cap diagnostic

**Entry script**

`PISCO_code/PISCO_E3/demo_pisco_E5_margin_cap_biomask_GYL.m`

**Protocol**

-   128 × 128-pixel simulated fields.
-   10 mean photons per channel.
-   30 runs.
-   Closure mode.
-   Biological-instance abundance masks.
-   (`\sigma`{=tex}\_{`\mathrm{cap}`{=tex}}=0.15).

**Output**

`E5_margin_cap_results/pisco_E5_margin_cap_results.csv`

The output records the propagated-margin cap activity and associated
run-level diagnostics.

------------------------------------------------------------------------

## 5. Supplementary tables

  -----------------------------------------------------------------------------------------------------------
  SI table        Manuscript content    Data-generating experiment / source
  --------------- --------------------- ---------------------------------------------------------------------
  Supplementary   Default PISCO         Method specification; hyperparameter sensitivity driver E6
  Table 1         hyperparameters and   
                  sensitivity ranges    

  Supplementary   Unmixing accuracy     E1 photon-budget benchmark; E1 accuracy and residual CSV files
  Table 2         versus photon budget  

  Supplementary   Effect of channel     `PISCO_code/PISCO_E10/demo_pisco_E10_channels_biomask_RMVSA_GYL.m`;
  Table 3         count on unmixing     32/64/128-channel variants, 20 mean photons/channel, 30 paired runs
                  accuracy              

  Supplementary   Peak optimization     E2 scaling campaign; per-method/per-size E2 CSV files
  Table 4         workspace and         
                  wall-clock time       
                  versus pixel count    

  Supplementary   Principal-component   E3 ablation campaign; `pisco_E3_ablation_v13_biomask_results_Q0.csv`
  Table 5         ablation              

  Supplementary   Public dataset        Dataset documentation in the manuscript
  Table 6         inventory and sources 

  Supplementary   Five-label controlled E14 five-label run; seed 7007;
  Table 7         benchmark             `pisco_E14_C_LysoSubspace_NoASC_results.csv` and reference spectra

  Supplementary   Ten-seed              E14 five-label multiseed driver; E14 FocalCheck multiseed driver;
  Table 8         reproducibility for   MultiView combined-view multiseed driver
                  five-label cells,     
                  FocalCheck and        
                  MultiView             

  Supplementary   Per-pixel relative    E9-A z = 13 trained solution and signal mask
  Table 9         residual on the HyU   
                  four-label field      

  Supplementary   Blindly extracted     E9-A z = 13 endmember spectra
  Table 10        endmembers on the HyU 
                  four-label field      

  Supplementary   Residual and          E9-B z = 12 trained solution and fixed-endmember NNLS diagnostic
  Table 11        acceptance            
                  diagnostics on the    
                  two-photon field      

  Supplementary   Blindly extracted     E9-B z = 12 endmember spectra
  Table 12        two-photon endmembers 
                  EM01--EM08            

  Supplementary   MultiView per-view    MultiView five-view reference-anchored analysis
  Table 13        run statistics        

  Supplementary   Single-photon         E13-SP, z = 11--15
  Table 14        z-section consistency 

  Supplementary   Two-photon z-section  E13-TP, z = 8--12
  Table 15        consistency           
  -----------------------------------------------------------------------------------------------------------

### Multi-seed scripts for Supplementary Table 8

**Five-label cell benchmark**

`PISCO_code/PISCO_E14/demo_pisco_E3_fivelabel_multiseed_CSV_GYL.m`

-   Seeds 7001--7010.
-   Five-label benchmark; training (p=6).
-   Free-coefficient mode.
-   (`\sigma`{=tex}\_{`\mathrm{cap}`{=tex}}=0.15).

Output:

`E3_fivelabel_multiseed_CSV_only_results/pisco_E3_fivelabel_multiseed_results.csv`

**FocalCheck microsphere phantom**

`PISCO_code/PISCO_E14/B/demo_pisco_E3_FocalCheck_multiseed_CSV_GYL.m`

-   Seeds 1001--1010.
-   (p=3).
-   550 iterations, including 500 warm-up.
-   (`\sigma`{=tex}\_{`\mathrm{cap}`{=tex}}=0.15).

Output:

`E3_FocalCheck_multiseed_CSV_only_results/pisco_E3_FocalCheck_multiseed_results.csv`

**MultiView combined view**

`PISCO_code/PISCO_MultiView/demo_pisco_E3_MultiView_L488_L561_L639_multiseed_GYL.m`

-   Combined 488/561/639-nm view.
-   Seeds 1001--1010: one base initialization and nine jittered
    restarts.
-   Initialization jitter standard deviation: 0.15.
-   6,800 warm-up + 700 joint iterations.
-   (p=13), free-coefficient mode.

Output:

`pisco_E3_MultiView_L488_L561_L639_Base1001_JitterRest_results.csv`

------------------------------------------------------------------------

## 6. Within-volume z-section outputs

### Single-photon volume --- E13-SP

**Script**

`PISCO_code/PISCO_E13/demo_pisco_E13_SP_zsections_crossZmatched.m`

**Protocol**

-   Source file: `fig2_e_01-fish5-muscle-fl.lsm`.
-   z-sections: 11, 12, 13, 14 and 15.
-   (p=5); seed 1001; free-coefficient mode.
-   z = 13 is the anchor and the main-text Fig. 4 field.
-   Each section is unmixed independently; endmembers are matched across
    sections by global one-to-one minimum-total-SAD assignment.

**Outputs**

Under `E13_SP_output1/`:

-   `E13z_SP_z11.mat` through `E13z_SP_z15.mat`
-   `pisco_E13_SP_results.csv`
-   `pisco_E13_SP_crossZ_matching.csv`
-   `pisco_E13_SP_pairwise.csv`
-   `pisco_E13_SP_consistency.csv`
-   `E13_SP_plotdata.mat`
-   `E13_SP_overview.svg`
-   `E13_SP_spectral_consistency.svg`
-   `E13_SP_numerical_summary.svg`
-   `E13_SP_all_endmembers.svg`

### Two-photon volume --- E13-TP

**Script**

`PISCO_code/PISCO_E13/TP/demo_pisco_E13_TP_SPstyle_minimal95MB_GYL.m`

**Protocol**

-   Source file: `fig5_e_01-fish1-four-hinbrain_eye-autofl.lsm`.
-   z-sections: 8, 9, 10, 11 and 12.
-   Image size: 2,048 × 2,560 pixels; 32 channels per section.
-   (p=8); seed 1001; free-coefficient mode.
-   z = 12 is the anchor and the main-text Fig. 5 field.
-   Training schedule and hyperparameters follow the main-text Fig. 5
    run.
-   Each section is unmixed independently; endmembers are matched across
    sections by global one-to-one minimum-total-SAD assignment.

**Outputs**

Under `E13_TP_NoASC_BandLoss_FixedQTail_output2/`:

-   `E13_TP_Z##_core.mat`
-   `E13_TP_Z##_abund_part###.mat`
-   `pisco_E13_TP_results.csv`
-   `pisco_E13_TP_crossZ_matching.csv`
-   `pisco_E13_TP_pairwise.csv`
-   `pisco_E13_TP_consistency.csv`
-   `E13_TP_plotdata.mat`
-   `E13_TP_overview.svg`
-   `E13_TP_spectral_consistency.svg`
-   `E13_TP_numerical_summary.svg`
-   `E13_TP_all_endmembers.svg`

------------------------------------------------------------------------

## 7. Reproduction record

For each reproduced figure or table, retain the following items
together:

1.  The manuscript figure/table number.
2.  The MATLAB entry script.
3.  The source dataset and exact input filename.
4.  The parameter values and random seed used in the manuscript
    protocol.
5.  The generated CSV and MAT files.
6.  The corresponding PNG/SVG figure outputs.
7.  The MATLAB release and CPU/GPU execution environment.

This record links the manuscript result to its input data, MATLAB entry
point, experimental settings and saved outputs.
