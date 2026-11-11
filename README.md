# PISCO: Poisson integrated spatially coherent unmixing for photon-limited hyperspectral fluorescence imaging
Jun Li, Yanlong Guo, Zhaozhao Zeng, Zhonghui Tang
![PISCO Framework](figures/Fig1.png)

**Overview of PISCO for blind unmixing of large-field hyperspectral fluorescence data.**

**a,** An HFI data cube (spatial dimensions $x, y$; 32 spectral channels) is acquired from a multiply labelled specimen; each pixel spectrum is a linear mixture $Y = MS + W$ of unknown endmember spectra.

**b,** Preprocessing: the endmember number $p$ is estimated by HySime; the data are projected onto the $(p - 1)$-dimensional signal subspace and spherized; VCA provides the initial endmember estimate $M_0$ with $Q_0 = M_0^{-1}$.

**c,** Joint optimization of the unmixing operator and abundances. The operator is parameterized as an invertible coupling $Q = LQ_0R$ ($L$ unit lower-triangular; $R$ upper-triangular with exponentially parameterized diagonal $r_{\log}$, the vector of log-diagonal entries), making the simplex volume $-\log |\det Q| = -\sum r_{\log}$ differentiable. Abundances are explicit per-pixel variables optimized jointly with the coupling under differentiable simplex penalties. The objective combines the reconstruction fidelity, simplex-volume, chance-constrained data-enclosure, equality-constraint and spatial-coherence (four-neighbour Laplacian) terms; all terms are optimized end to end by Adam after a warm-up phase with $Q$ fixed at $Q_0$.

**d,** Outputs: blindly extracted endmember spectra $M = Q^{-1}$ (including endmembers absent from spectral libraries, such as autofluorescence) and spatially coherent abundance maps.

**e,** Schematic contrast of solver strategies: alternating QP/SQP schemes update each variable block while the others are frozen, so estimation errors propagate between blocks, whereas PISCO optimizes all variables jointly within one differentiable objective.

**f,** The Poisson chance constraint yields intensity-dependent enclosure margins — wide for bright pixels, tight for dim ones — replacing the single intensity-independent Gaussian margin of classical robust solvers.

# Abstract
