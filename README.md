License: Creative Commons Attribution 4.0 International (CC BY 4.0)


OVERVIEW
--------
This dataset contains synthetic Hubbard model configurations generated to accompany the QPT-Net
preprint. Each row represents a single Hubbard model simulation configuration with:
  - Control parameters (U/t, T/t, filling n, lattice geometry)
  - Ground-truth QCP location and phase label
  - True and measured critical exponents
  - Spectral function observables (quasiparticle weight, Mott gap, Hubbard band positions)
  - Thermodynamic observables (double occupancy, spin structure factor, susceptibility)
  - Transport properties (DC conductivity, optical gap)
  - QPT-Net model predictions for benchmarking

COLUMN DESCRIPTIONS
-------------------

--- CONFIGURATION IDENTIFIER ---
config_id           : Unique configuration ID (format: DQMC_NNNNN)

--- CONTROL PARAMETERS ---
U_over_t            : On-site Coulomb repulsion / hopping amplitude ratio (dimensionless)
                      Range: [2.0, 16.0]
T_over_t            : Temperature / hopping amplitude ratio (dimensionless, normalized)
                      Range: [0.02, 0.60]
filling_n           : Average electron filling per site
                      Range: [0.70, 1.00]  (1.00 = half-filling)
lattice_L           : Linear lattice size (L×L periodic)
                      Values: 8, 12, 16, 24
lattice_type        : Lattice geometry
                      Values: square, triangular, honeycomb

--- GROUND TRUTH (QCP & PHASE) ---
gc_true             : True Quantum Critical Point location (U/t units)
                      Computed from lattice+filling dependent formula: gc = gc_base + (n-1)×3.5
delta_g             : Reduced tuning parameter delta_g = U/t - gc_true
                      Negative = ordered side, Positive = disordered side
phase_label         : True phase assignment
                      Values: Mott_Insulator | Superconductor | Pseudogap |
                              Quantum_Critical | Fermi_Liquid | Strange_Metal
is_critical_fan     : Binary flag: 1 if |delta_g| < 1.5 (within quantum critical fan)

--- TRUE CRITICAL EXPONENTS (3D Ising Universality Class) ---
nu_true             : Correlation length exponent ν = 0.710
beta_true           : Order parameter exponent β = 0.350
eta_true            : Anomalous dimension η = 0.036
z_true              : Dynamic critical exponent z = 1.000
gamma_true          : Susceptibility exponent γ = 1.397

Note: Scaling relations satisfied: 2β + γ = 2 - α (Fisher), γ = ν(2-η) (Fisher second),
      β(δ-1) = γ (Widom). These hold exactly for the true values.

--- MEASURED CRITICAL EXPONENTS (from finite-size DQMC analysis, with noise) ---
nu_measured         : ν estimated from finite-size scaling (includes finite-size + statistical noise)
beta_measured       : β estimated from order parameter scaling
eta_measured        : η estimated from correlation function decay (hard to measure, large uncertainty)
z_measured          : z estimated from dynamic scaling of relaxation time
gamma_measured      : γ estimated from susceptibility peak scaling

--- SPECTRAL FUNCTION OBSERVABLES ---
quasiparticle_weight_Z : Quasiparticle weight Z = [1 - ∂ReSigma/∂ω]^{-1}
                         Range: [0.01, 0.98]  (Z → 0 at Mott transition)
mott_gap_eV            : Mott insulating gap magnitude (units of t)
                         0.0 for metallic configurations; > 0 in Mott insulator
UHB_position           : Upper Hubbard Band centroid position (units of t, +ω axis)
LHB_position           : Lower Hubbard Band centroid position (units of t, -ω axis)

--- THERMODYNAMIC OBSERVABLES ---
double_occupancy        : Average double occupancy <n_↑ n_↓>
                          Range: ~0 (Mott) to ~0.25 (non-interacting)
spin_structure_S_pipi   : Spin structure factor S(π,π) — antiferromagnetic order parameter
                          Range: [0, 1]  (peaks at AF QCP, suppressed by temperature)
compressibility         : Electronic compressibility κ = ∂n/∂μ
                          Near-zero in Mott insulator; finite in metal
magnetic_susceptibility : Uniform magnetic susceptibility χ(q=π,π)
                          Diverges as χ ~ |delta_g|^{-γ} approaching QCP

--- TRANSPORT PROPERTIES ---
dc_conductivity         : DC conductivity proxy (Drude weight, units of e²/h)
optical_gap             : Optical (charge) gap from spectral function (units of t)

--- FINITE-SIZE SCALING ---
crossing_temp_T_cross   : Temperature at which susceptibility curves for different L intersect
                          (finite-size scaling crossing point near phase boundary)
confidence_score        : QPT-Net confidence in the critical regime identification
                          exp(-|delta_g|×0.4) × exp(-T/t×3.0); peaks at QCP, low temperature

--- QPT-NET MODEL OUTPUTS (predictions) ---
qptnet_gc_predicted     : QPT-Net predicted QCP location (U/t units)
qptnet_nu_predicted     : QPT-Net predicted correlation length exponent ν
qptnet_beta_predicted   : QPT-Net predicted order parameter exponent β
qptnet_prob_mott        : QPT-Net posterior probability: Mott Insulator phase
qptnet_prob_sc          : QPT-Net posterior probability: Superconductor phase
qptnet_prob_critical    : QPT-Net posterior probability: Quantum Critical phase
qptnet_abs_error_gc     : Absolute prediction error |ĝ_c - g_c| for performance benchmarking


DATASET STATISTICS
------------------
Total configurations: 5,000
Phase distribution:
  Mott_Insulator    : 2341 (46.8%)
  Strange_Metal     :  797 (15.9%)
  Fermi_Liquid      :  675 (13.5%)
  Quantum_Critical  :  621 (12.4%)
  Superconductor    :  364 (7.3%)
  Pseudogap         :  202 (4.0%)

Lattice geometry distribution:
  Square (2D)       : 3513 (70.3%)
  Triangular        :  988 (19.8%)
  Honeycomb         :  499 (10.0%)

QPT-Net model performance on this dataset:
  Mean |error| on gc : 0.126 U/t
  R² on gc           : 0.973
  ν recovery error   : 1.1% of literature value


PHYSICS NOTES FOR USERS
------------------------
1. HALF-FILLING: Rows with filling_n = 1.00 (±0.01) are at particle-hole symmetry
   (half-filling). The Mott transition is sharpest here. The QCP location is
   gc ≈ 8.5 for the 2D square lattice at half-filling.

2. QUANTUM CRITICAL FAN: Configurations with is_critical_fan = 1 are within the
   quantum critical fan (|delta_g| < 1.5). In this regime, the standard Fermi liquid
   description breaks down and observables scale as power laws. Critical exponent
   extraction is most reliable here.

3. SCALING RELATIONS: The true exponents satisfy all RG scaling relations exactly.
   Measured exponents contain finite-size and statistical noise. A good model should
   predict exponents satisfying these relations even when measured values do not.

4. SIGN PROBLEM: For doped configurations (filling_n < 0.90), the fermion sign problem
   in DQMC becomes severe at low temperatures, increasing noise in measured observables.
   Use filling_n > 0.90 configurations for cleanest comparisons.

5. MOTT GAP: mott_gap_eV = 0.0 does NOT necessarily mean metallic — it means the
   spectral gap is below the resolution threshold. Use quasiparticle_weight_Z < 0.1
   as a more reliable Mott insulator indicator.


SUGGESTED TRAIN/TEST SPLIT
---------------------------
For reproducing QPT-Net results:
  Training:   80% — random stratified sample across phases and lattice types
  Validation: 10%
  Test:       10% — held out completely during all training

Alternatively, use a "generalization split":
  Train on  lattice_L = [8, 12]  →  Test on lattice_L = [16, 24]  (finite-size generalization)
  Train on  square lattice       →  Test on triangular/honeycomb   (geometry generalization)




================================================================
End of README
================================================================
