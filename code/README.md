# PISCO

**Poisson integrated spatially coherent unmixing for photon-limited
hyperspectral fluorescence imaging**

PISCO is a blind spectral-unmixing method for hyperspectral fluorescence
imaging (HFI). It jointly estimates endmember spectra and per-pixel
abundance coefficients from a hyperspectral image, without requiring a
complete library of measured reference spectra.

The method combines:

-   a Poisson chance-constraint enclosure with pixel- and
    endmember-dependent margins;
-   a minimum-volume simplex criterion represented by an invertible
    coupling, (Q=LQ_0R);
-   explicit abundance variables;
-   a four-neighbour spatial-coherence regularizer; and
-   joint optimization with Adam after a two-phase training schedule.

The MATLAB implementation, experiment drivers, evaluation routines and
reproduction scripts are organized by the experiments reported in the
PISCO manuscript and Supplementary Information (v92).

## 1. Repository contents

The supplied archive contains 125 files: 122 MATLAB `.m` files and 3
MATLAB `.mat` files. This count refers to the code archive only; the
README and figure-reproduction guide are supplied separately as
documentation files and are not included in the 125-file count.

  -----------------------------------------------------------------------
  Directory                           Experiment / purpose
  ----------------------------------- -----------------------------------
  `PISCO_E1/`                         Simulated photon-budget benchmark
                                      and comparison with LU, HyU, NMF,
                                      SISAL and RMVSA

  `PISCO_E2/`                         Scaling benchmark across field
                                      sizes

  `PISCO_E3/`                         Component ablations, i.i.d.
                                      spatial-prior control and
                                      propagated-margin-cap diagnostic

  `PISCO_E4/`                         Noise-model robustness and Anscombe
                                      variance-stabilizing-transform
                                      comparison

  `PISCO_E5/`                         Endmember-number misspecification

  `PISCO_E6/`                         One-at-a-time hyperparameter
                                      sensitivity

  `PISCO_E9/`                         In-vivo HyU zebrafish single-photon
                                      and two-photon analyses

  `PISCO_E10/`                        Spectral-resolution comparison at
                                      32, 64 and 128 channels

  `PISCO_E11/`                        Structured versus i.i.d.
                                      abundance-field comparison

  `PISCO_E12/`                        Training-dynamics analysis

  `PISCO_E13/`                        Within-volume z-section consistency
                                      for single-photon and two-photon
                                      data

  `PISCO_E14/`                        Public five-label cell benchmark
                                      and FocalCheck DoubleGreen bead
                                      phantom

  `PISCO_MultiView/`                  Reference-anchored 13-plex analysis
                                      of the MultiView multi-excitation
                                      dataset
  -----------------------------------------------------------------------

The three `.mat` files are copies of the 32-channel, four-endmember
spectral library:

`PISCO_M_32x4_HyU_strict_quasar32.mat`

The figure-by-figure reproduction map is provided in
[`FIGURE_REPRODUCTION_GUIDE.md`](FIGURE_REPRODUCTION_GUIDE.md).

## 2. Software and computational environment

### MATLAB

PISCO is implemented in MATLAB. The scalability campaign reported in the
manuscript was run with MATLAB R2023a.

The MATLAB scripts use functions for statistical sampling, image
processing and (for GPU-enabled experiments) GPU computation. The
corresponding MATLAB toolboxes should be available when running those
workflows:

-   Statistics and Machine Learning Toolbox;
-   Image Processing Toolbox;
-   Parallel Computing Toolbox for GPU-enabled workflows.

Python is not required for the MATLAB experiment drivers.

### Hardware reported in the manuscript

  -----------------------------------------------------------------------
  Experiment                          Computational environment
  ----------------------------------- -----------------------------------
  Scalability campaign (Supplementary Intel Core i7-10700 CPU, 32 GB RAM;
  Note 4 and Extended Data Fig. A3)   CPU-only, double precision

  Two-photon five-megapixel analysis  NVIDIA RTX 4060; GPU path, single
  (Supplementary Note 6.3)            precision
  -----------------------------------------------------------------------

The hardware above describes the reported campaigns. Required memory and
runtime depend on the selected image size, endmember number and
experiment.

## 3. Data availability

The manuscript's Data availability statement and Supplementary Table 6
identify the following sources.

  -----------------------------------------------------------------------------------------------------------------------------------------
  Dataset                 Content and use         Source
  ----------------------- ----------------------- -----------------------------------------------------------------------------------------
  Simulated HFI data and  Simulated hyperspectral [Zenodo record, DOI 10.5281/zenodo.24194152](https://doi.org/10.5281/zenodo.24194152)
  derived products        fluorescence data with  
                          ground truth; derived   
                          in-vivo endmembers,     
                          abundance maps,         
                          residual maps, masks    
                          and run configurations  

  HyU zebrafish data      32-channel              [USC Translational Imaging Center](https://bioimaging.usc.edu/software.html), Chiang et
                          hyperspectral           al. The source data are distributed under a non-commercial licence and are not
                          fluorescence images,    re-deposited with the PISCO code.
                          410.5--694.9 nm, 8.9    
                          nm/channel; includes    
                          large tiled fields and  
                          z-stacks                

  HyU two-photon          32-channel intrinsic    USC Translational Imaging Center, as above
  intrinsic data          fluorescence at 740-nm  
                          excitation; 27          
                          z-sections in the       
                          source volume           

  PhasorPy five-label     Five-label cell image,  [Zenodo record 13625087](https://zenodo.org/records/13625087) and [Zenodo record
  benchmark               28 channels, 256 × 256  14860228](https://zenodo.org/records/14860228)
                          pixels, and             
                          single-stain reference  
                          images                  

  FocalCheck DoubleGreen  Two-dye core--shell     [F1000Research, DOI
  microspheres            bead phantom: 512-nm    10.5256/f1000research.9233.d138682](https://doi.org/10.5256/f1000research.9233.d138682)
                          shell and 525-nm core;  
                          513 × 513 pixels, 16    
                          channels                

  MultiView               Thirteen single-stain   [Zenodo record 13988378](https://zenodo.org/records/13988378)
  multi-excitation data   references and mixed    
                          *E. coli* fields under  
                          seven excitation        
                          settings; five views    
                          analysed in the         
                          manuscript              

  FPbase spectra          Fluorescent-protein     [FPbase](https://www.fpbase.org/)
                          emission spectra used   
                          to construct simulated  
                          endmembers and for      
                          spectral cross-checks   

  BBBC038/039 masks       Nucleus-segmentation    [Broad Bioimage Benchmark Collection](https://bbbc.broadinstitute.org/)
                          masks used to           
                          synthesize spatially    
                          correlated abundance    
                          fields                  
  -----------------------------------------------------------------------------------------------------------------------------------------

### Main in-vivo input files

The principal HyU acquisitions used in the manuscript are:

``` text
fig2_e_01-fish5-muscle-fl.lsm
fig5_e_01-fish1-four-hinbrain_eye-autofl.lsm
```

-   `fig2_e_01-fish5-muscle-fl.lsm`: single-photon four-label zebrafish
    muscle volume; the main-text Fig. 4 analysis uses z-section 13.
-   `fig5_e_01-fish1-four-hinbrain_eye-autofl.lsm`: two-photon zebrafish
    hindbrain--eye volume; the main-text Fig. 5 analysis uses z-section
    12.

The HyU acquisitions are analysed as 32-channel spectral series. The
33rd transmitted-light channel in the single-photon acquisition is not
part of the 32-channel spectral series.

### MultiView input layout

The MultiView driver expects one directory per analysed view, with the
mixed image and thirteen same-view single-stain TIFF images in a `data`
subdirectory. The five analysed views are:

-   combined 488/561/639-nm excitation: 23 channels;
-   445-nm excitation: 28 channels;
-   488-nm excitation: 23 channels;
-   514-nm excitation: 20 channels;
-   561-nm excitation: 14 channels.

The 594- and 639-nm single-laser views are not part of the 13-component
analysis.

## 4. Model modes and default training configuration

The manuscript uses two abundance modes:

-   **Closure mode:** used for simulated data generated from a complete
    known endmember set. The sum-to-one abundance penalty is active.
-   **Free-coefficient mode:** used for real biological data and
    datasets in which unlisted emitters or instrument responses may be
    present. The sum-to-one penalty and affine-offset correction are
    deactivated; abundance non-negativity remains active.

Unless an experiment is explicitly described otherwise, the defaults in
Supplementary Table 1 apply.

  ----------------------------------------------------------------------------------------
  Parameter                                                                        Default
  ----------------------------------------------- ----------------------------------------
  Total iterations                                                                   2,500

  Warm-up iterations                                                                   600

  Joint-training iterations                                                          1,900

  Adam learning rate                                             (3`\times10`{=tex}\^{-4})

  (`\lambda`{=tex}\_{`\mathrm{vol}`{=tex}})                                          0.008

  (`\lambda`{=tex}\_{`\mathrm{nonneg}`{=tex}})                                           3

  (`\lambda`{=tex}\_{`\mathrm{eq}`{=tex}})                                             100

  (`\lambda`{=tex}\_{`\mathrm{sumone}`{=tex}})                                           2

  (`\lambda`{=tex}\_{`\mathrm{spatial}`{=tex}})                                          2

  (`\sigma`{=tex}\_{`\mathrm{cap}`{=tex}})                                            0.03

  (`\eta`{=tex})                                                                       0.3

  (`\lambda`{=tex}\_{`\mathrm{mnn}`{=tex}})                                             10

  (`\lambda`{=tex}\_{`\mathrm{smooth}`{=tex}})                                         0.1

  (`\lambda`{=tex}\_{`\mathrm{enclose}`{=tex}})     0.3; 10 for i.i.d. synthetic abundance
                                                                                    fields

  Whitening ridge                                                              (10\^{-10})
  (`\lambda`{=tex}\_{`\mathrm{reg}`{=tex}})       

  Gradient batch                                                         Full field, (B=N)
  ----------------------------------------------------------------------------------------

The Adam settings are (`\beta`{=tex}\_1=0.9), (`\beta`{=tex}\_2=0.999),
and (`\epsilon=10`{=tex}\^{-8}). The warm-up phase holds (Q=Q_0) fixed
and updates abundances; the joint phase updates the abundance matrix and
the invertible coupling parameters together.

Experiment-specific settings stated in the manuscript are listed in the
reproduction guide.

## 5. Installation and execution

1.  Download and extract the PISCO code archive.
2.  Download the dataset required by the figure or experiment.
3.  Place the data in the directory expected by the corresponding
    driver, or edit the input-path variable near the top of that driver.
4.  Open MATLAB and change the current directory to the selected
    experiment folder.
5.  Add that experiment folder to the MATLAB path.
6.  Run the corresponding `.m` script.

Example for the photon-budget experiment:

``` matlab
cd('path/to/PISCO_code/PISCO_E1');
addpath(pwd);
run('demo_E1_compare_PISCO_LU_SISAL_RMVSA_HyU_NMF_biomask_GYL.m');
```

Example for batch execution from a system shell:

``` bash
matlab -batch "cd('path/to/PISCO_code/PISCO_E1'); addpath(pwd); run('demo_E1_compare_PISCO_LU_SISAL_RMVSA_HyU_NMF_biomask_GYL.m')"
```

Run the driver from its experiment directory. The archive contains
experiment-specific copies of MATLAB functions; add the selected
experiment folder rather than recursively adding every folder in the
archive.

### Quick verification

Where a driver provides a `QUICK` option, it can be used to check data
loading and the basic execution path on a reduced run. Use the full
experiment settings and repeat counts specified in the manuscript for
reported results.

## 6. Output files

Depending on the experiment, the MATLAB drivers write:

-   CSV files containing per-run metrics, summary statistics, matching
    results or runtime measurements;
-   MAT files containing endmember spectra, abundance coefficients,
    masks, residual maps, plot data or run metadata;
-   PNG figures for rendered abundance maps, spectra, residuals and
    summary plots;
-   editable SVG figures for the E13 z-section consistency summaries.

The exact output filenames and their association with each manuscript
figure and table are listed in
[`FIGURE_REPRODUCTION_GUIDE.md`](FIGURE_REPRODUCTION_GUIDE.md).

## 7. Citation and licence

Please cite the PISCO manuscript when using this implementation:

> Li, J., Guo, Y., Zeng, Z., Wang, L. and Tang, Z. *PISCO: Poisson
> integrated spatially coherent unmixing for photon-limited
> hyperspectral fluorescence imaging.*

The manuscript's Code availability statement specifies the following:

-   GitHub: <https://github.com/ASDFFGG121EAA/PISCO>
-   Zenodo code archive: <https://doi.org/10.5281/zenodo.24199687>
-   Licence: **Creative Commons Attribution-NonCommercial 4.0
    International (CC BY-NC 4.0)**.

The licence permits research, teaching and other non-profit use.
Commercial use requires prior written permission from the corresponding
author.

For dataset-specific reuse conditions, follow the licence or access
terms of the original data provider.
