ModeCode
========

.. image:: https://travis-ci.org/ucl-cosmoparticles/ModeCode.svg?branch=master
    :target: https://travis-ci.org/ucl-cosmoparticles/ModeCode

ReadMe for ModeChord -- PolyChord enabled version of ModeCode.

November 2016.

M.J. Mortonson, H.V. Peiris, and R. Easther, arXiv:1007.4205
R. Easther and H. Peiris, arXiv:1112.0326
J. Norena, C. Wagner, L. Verde, H. Peiris, and R. Easther, arXiv:1202.0304

In this ReadMe "ModeChord" refers to the PolyChord enabled version of 
ModeCode.

ModeChord computes the primordial scalar and tensor power spectra for 
models of inflation. It can be run as a standalone code, or combined 
with CAMB to compute CMB angular power spectra and CosmoMC+PolyChord to 
perform likelihood analysis and constrain parameters of inflationary 
models.

ModeChord is integrated with the nested sampling package PolyChord, and 
permits both parameter estimation and the computation of Bayesian 
evidence.

The code can be downloaded at:
http://www.modecode.org

PolyChord can be obtained here:
http://ccpforge.cse.rl.ac.uk/gf/project/polychord/

CosmoMC can be obtained here:
http://cosmologist.info/cosmomc/


Description
-----------

The currently available files are:

(1) modechord.tar.gz, including the following directories:

- ``cosmomc/`` - sample files for running CosmoMC with ModeCode + PolyChord

- ``cosmomc/camb/`` - sample driver for standalone code 
  (``driver_modpk.f90``), new files used by ModeChord, and modified CAMB files

- ``cosmomc/source/`` - modified CosmoMC code

- ``cosmomc/Models/*/`` - files for running CosmoMC with the PolyChord
  sampler for the models included in ModeChord (note that
  additional models can be easily added as described below), where
  ``*`` = m2phi2 (quadratic potential), lphi4 (quartic), neq1 (n=1),
  neq2ov3 (n=2/3), natural, hilltop, higgs (Higgs
  inflation/Starobinsky model), monodromy (different versions of
  the model) and reconstruction (Hubble slow-roll reconstruction). 

- ``cosmomc/batch2/`` - directory for storing generic input files for
  ModeChord.  these additional files are accessed via test.ini and
  files in ``Models/*/`` . In addition, ``batch2/params_CMB_defaults.ini``
  is modified to have narrower priors on cosmological parameters,
  and to include default prior widths for ModeCode's additional
  V(phi) parameters.

- ``cosmomc/data/camspec.paramnames`` - paramnames file for using
  Planck 2013 likelihoods.

As usual, ``*.tar.gz`` files can be unzipped and extracted using the command 
``tar -xzvf *.tar.gz``.

The current version of the code is based on the November 2016 version of 
CAMB and CosmoMC and the November 2016 PolyChord.

Only new or modified CAMB/CosmoMC files are provided in the downloads, so 
any applications beyond using the standalone version of ModeCode will 
require adding the downloaded files to an existing copy of the basic 
CAMB and CosmoMC codes.

In each of the modified files, changes to the original code are marked 
by ``'MODIFIED P(K) ... END MODIFIED P(K)'`` so that they can be easily located.

Setting up and compiling ModeChord
----------------------------------

This involves three steps
1) Install cosmomc
2) Install polychord (cosmochord)
3) Install modechord

After obtaining a cosmomc install as detailed here:
http://cosmologist.info/cosmomc/

you should install the polychord plug-in for cosmomc (cosmochord) as detailed here:
http://ccpforge.cse.rl.ac.uk/gf/project/polychord/

After extracting files from modechord.tar.gz, the ModeChord files must 
be copied into existing versions of CAMB/CosmoMC for use with those 
codes. One easy way to do this is to start in the cosmomc/ directory for 
the ModeChord files, and use 'cp -r . cosmomc_target/' where 
cosmomc_target is the base directory for the unmodified code.

** Note that this will overwrite original versions of some of the 
CAMB/CosmoMC files; if you do not want this to happen, rename the files 
first before copying (they will also need to be renamed in the 
Makefiles). **

Once the new and modified code files have been copied into the 
appropriate directories, the ModeChord versions of CAMB and CosmoMC can 
be compiled with the usual Makefiles, after adjusting the compiler options 
in the camb/Makefile and source/Makefile supplied with ModeChord to work
on your system. You will also need to supply any likelihood codes (e.g. 
Planck) that do not ship with the standard CAMB/CosmoMC.

Executing the make command in the source directory will produce the 
executables cosmomc and getdist.

To compile the sample driver program for the standalone version of 
ModeCode without compiling CAMB or CosmoMC, run 'make powspec' in the 
camb/ directory.

General description of ModeChord parameters and options
-------------------------------------------------------

Inflationary models are described in ModeCode using an array of 
parameters for the potential V(phi), vparams(i) for i = 1, 2, etc., and 
the parameter N_pivot (not used for HSR models, see below), which 
accounts for the uncertainty in the post-inflationary epoch between the 
inflation and reheating. N_pivot is the number of inflationary e-folds 
remaining when the mode of a given scale infl_pivot_k exits the horizon. 
The value of infl_pivot_k can be specified in units of Mpc^{-1}.



ModeCode options:
-----------------

- use_modpk (logical): a general flag to turn ModeCode on or off. 
  Setting use_modpk=F recovers the original functionality of CAMB/CosmoMC 
  while retaining the PolyChord sampler. When running the code with 
  use_modpk=F, ParamNamesFile in params_modpk.ini should be changed to 
  params_modpk_F.paramnames.

- modpk_physical_priors (logical) Excludes models where the scalar
  spectral amplitude is vastly different from the observed
  value. As implemented, this cut excludes models who which
  10^-11<A_s<10^-7

- modpk_rho_reheat (real): Reheating **density** in GeV^4 Note that
  the temperature / energy ~ modpk_rho_reheat^(1/4)

- modpk_w_primordial_lower (real): Lower limit on effective equation
  of state parameter during between the end of inflation and
  rho=rho_reheat

- modpk_w_primordial_upper (real): Upper limit on effective equation
  of state parameter during between the end of inflation and
  rho=rho_reheat

- potential_choice (integer): specifies which inflationary model to use.
  The translation between potential_choice and V(phi) is set in
  camb/modpk_potential.f90. The models corresponding to different values
  of potential_choice by default are listed below.

- vparams_num (integer): number of parameters needed to describe V(phi)
  (for general reheating models, the total number of MCMC parameters is
  vparams_num+1). The maximum number of parameters is set to 9 by 
  default, but can be increased in camb/modpk_modules.f90 by changing 
  max_vparams.

- phi_init (real): starting guess for the initial value of phi; for all 
  models provided with the code, this setting is superceded by the 
  'initialphi' function in camb/modpk_potential.f90.

- infl_pivot_k (real): pivot wavenumber for evaluating N_pivot, in Mpc^{-1}.
  Note that this parameter is named k_pivot, when ModeCode is called with CAMB
  instead of CosmoMC/PolyChord.

- N_pivot (real): number of e-folds of inflation remaining after the
  mode with wavenumber infl_pivot_k leaves the horizon (note that 
  N_pivot is treated as a parameter to be varied rather than a fixed 
  setting).

- instreheat (logical): whether or not to assume instant reheating, 
  which fixes the value of N_pivot; if instreheat=T, the values chosen for 
  infl_pivot_k and N_pivot are ignored.

- slowroll_infl_end (logical): whether or not to determine when 
  inflation ends by the breakdown of slow roll conditions, i.e. 
  epsilon_H=1. If slowroll_infl_end=F, the end of inflation is assumed 
  to occur when phi=phi_infl_end.

- phi_infl_end (real): final value of phi during inflation for models 
  that do not end via slow roll violation (slowroll_infl_end=F). If 
  slowroll_infl_end=T, the value of this parameter is ignored.

- vnderivs (logical): whether to use numerical derivatives of the 
  potential (vnderivs=T) or analytic forms supplied in 
  camb/modpk_potential.f90 (vnderivs=F). The latter option is STRONGLY 
  recommended for all models for which the derivatives of V(phi) can be 
  computed and expressed analytically, as the use of numerical derivatives 
  may lead to inaccurate results for certain models.

- action (integer) action = 5 to use PolyChord, action=0 to use the
  standard MCMC sampler.

Default models in ModeCode:
---------------------------
- potential_choice = 1: quadratic
      V(phi) = m^2 phi^2 / 2
      vparams(1) = log_10(m^2)
- potential_choice = 2: natural
      V(phi) = Lambda^4 [1+cos(phi/f)]
      vparams(1) = log_10(Lambda), vparams(2) = log_10(f)
- potential_choice = 3: quartic
      V(phi) = lambda phi^4 / 4
      vparams(1) = log_10(lambda)
- potential_choice = 4: linear
      V(phi) = lambda phi
      vparams(1) = log_10(lambda)
- potential_choice = 5: exponent n=2/3
      V(phi) = (3/2) lambda phi^{2/3}
      vparams(1) = log_10(lambda)
- potential_choice = 6: hilltop
      V(phi) = Lambda^4 - lambda phi^4 / 4
      vparams(1) = log_10(Lambda), vparams(2) = log_10(lambda)

Some further models are available (check camb/modpk_potential.f90). Oscillatory 
models such as axion monodromy (potential_choice=10), generalised axion monodromy 
(potential_choice=13) and a potential with a step feature (potential_choice=11) 
require the flag -DWIGGLY to be set in the compilation FFLAGS in order to 
sufficiently increase the accuracy of CAMB. This will in general significantly 
slow down the computation of angular power spectra. We caution the user to test
the numerical convergence of these settings when adapting the code.   

Using ModeCode for HSR reconstruction:
--------------------------------------
J. Norena, C. Wagner, L. Verde, H. Peiris, R. Easther, arXiv:1202.0304

In the Hubble slow-roll (HSR) reconstruction the Hubble parameter is 
modeled by a finite polynomial:
H(phi) = H_star (1 + A_1 phi + A_2 phi^2 + ... + A_N phi^N).

The coefficients A_i are related by a one-to-one correspondence to the 
Hubble slow-roll parameters (epsilon, eta, xi, ...), and H_star sets the 
overall energy scale.

The corresponding potential is then given by:
V(phi) = M_pl^2 H_star^2 [3(1 + A_1 phi + ... + A_N phi^N)^2 - 2(A_1 + ... + N A_N phi^(N-1))]

At the moment, the HSR potential is implemented in ModeCode for the 
first three HSR parameters, i.e. epsilon, eta and xi. However, the 
extension to higher orders is straightforward. To allow for uniform and 
log priors on epsilon, one can choose between:

- potential_choice = 7: HSR with eps, eta, xi
      vparams(1) = epsilon
      vparams(2) = eta
      vparams(3) = xi
      vparams(4) = log(10^10 A_SR)

- potential_choice = 8: HSR with eps, eta, xi
      vparams(1) = log_10(epsilon)
      vparams(2) = eta
      vparams(3) = xi
      vparams(4) = log(10^10 A_SR)

where the HSR parameters are given at phi=0. A_SR is the amplitude of 
the curvature power spectrum at the pivot scale (i.e., the mode which 
exits the horizon at phi=0) computed at second order in the HSR 
parameters. This relation between the HSR parameters and the amplitude 
is then used to set the value of H_star.

Note that N_pivot is not used. Instead the parameter 
reconstruction_Nefold_limit specifies the minimum number of inflationary 
e-folds after the scale given by infl_min_k exits the horizon. In 
addition, the smallest scale for which one requires that it exits the 
horizon still during inflation is given by infl_max_k.

In summary, the parameters needed for the HSR reconstruction are:

- infl_pivot_k (real): specifies the mode which exits the horizon when 
  phi=0, in Mpc^{-1}. In addition, derived parameters like the spectral
  tilt or the tensor-to-scalar ratio etc. are evaluated at this scale.
  Note that this parameter is named k_pivot, when ModeCode is called with 
  CAMB instead of CosmoMC/PolyChord.

- infl_min_k (real):  largest scale for which one requires that it exits 
  the horizon during inflation, in Mpc^{-1}.

- infl_max_k (real):  smallest scale for which one requires that it exits 
  the horizon during inflation, in Mpc^{-1}.

- reconstruction_Nefold_limit (real): minimum number of inflationary
  e-folds counted from infl_min_k.


Computing power spectra (standalone code)
-----------------------------------------

The sample driver included with the code (camb/driver_modpk.f90) can be 
run using the command 'powspec' in the camb/ directory. It is set up to 
compute the scalar and tensor power spectra at 500 k values between 
5x10^{-4} Mpc^{-1} and 5 Mpc^{-1} for a natural inflation model. The 
code outputs (k, P_s(k), P_t(k)) and also computes the scalar and tensor 
amplitudes and spectral tilts, as well as the running of the scalar 
spectral index, at k_pivot = 0.05 Mpc^{-1}. This program can be easily 
modified to compute the spectra for different models of inflation and/or 
different values of k.

Warnings that phi_init is inconsistent and the value of phi_init is 
being rescaled are a normal byproduct of the algorithm that searches for 
self-consistent initial conditions for inflation. If there are an 
excessive number of these warnings for each model evaluated, it may help 
to change the user-supplied phi_init value or the function used in 
'initialphi' in camb/modpk_potential.f90 for the inflationary model in 
question.

Other warnings or errors produced by ModeCode typically indicate that 
the chosen model parameters do not have a physically acceptable 
inflationary solution.

Using ModeCode with CAMB
------------------------

ModeCode can be used within CAMB by running 'camb params_modpk.ini' in 
the camb/ directory. The ModeCode options and parameters are set in the 
section of params_modpk.ini marked 'MODIFIED P(K)', which follows the 
entries for the initial power spectrum parameters from the unmodified 
version of CAMB. Note that these original spectral parameter values 
(e.g. scalar_amp(1), scalar_spectral_index(1), etc.) are ignored if 
use_modpk=T since the initial power spectra are entirely specified by 
the values of N_pivot and the vparams array.


Using ModeCode with CosmoMC+PolyChord
-------------------------------------

The use of the ModeCode version of CosmoMC+PolyChord is largely 
unchanged from the original version of the code.

ModeCode-specific options have been added to the test.ini and
batch2/params_CBM_defaults.ini files. These are marked by 'MODIFIED
P(K)'. 

As a sampler PolyChord is parallelised via a master-slave structure.
This means that it runs optimally in pure MPI (i.e. with no openMP
parallisation, OMP_NUM_THREADS=1). With MPI it is parallelised
effectively up to the number of live points nlive (default 500).  Note
that this contrasts with the default Metropolis-Hastings sampler of
CosmoMC. With 128 MPI processes, runs complete in typically ~12 hours.
This is approximately 2-4 times longer than a CosmoMC run with a well
tuned covariance matrix. 

When CAMB+ModeCode is run as a standalone code, N_pivot is a free 
parameter specified by the user. Within CosmoMC+PolyChord N_pivot is an 
independent parameter.  If instant reheating is assumed (instreheat=T), 
then N_pivot should be fixed by setting 'param[Npivot] = 50 50 50 0 0' 
in params_modpk.ini (the choice of 50 here is unimportant since the code 
ignores this value for instant reheating models).

N_pivot and the vparams array (named vpar1, vpar2, etc. in CosmoMC) are 
added as semi-slow parameters immediately after the traditional semi
slow parameters, but before fast parameters. Elements in the vparams
array that are not used by a particular inflationary model should be
fixed in the MCMC analysis. Currently a model can have at most 20
V(phi) parameters, but this maximum may be increased by adjusting the
value of max_vparams in camb/modpk_modules.f90, and adding additional
lines to the relevant .paramnames in the Models directory.

The chain files output by the ModeChord version of CosmoMC have several 
additional derived parameters which are listed at the end of 
params_modpk.paramnames:

- modpk_Npivot; if instreheat=F, this should always be equal to the 
  chain parameter N_pivot, but if instreheat=T it will be the value of 
  N_pivot computed by the code to satisfy the matching equation for 
  instant reheating models. Note that for HSR reconstruction this value
  is meaningless.

- modpk_ns, modpk_nt (scalar and tensor spectral tilt)

- modpk_nrun (scalar spectral running dn_s/dlnk)

- modpk_logA (ln(10^{10}A_s), i.e. the usual CosmoMC scalar amplitude 
  parameter)

- modpk_r (tensor-to-scalar ratio)

- modpk_w Effective primordial equation of state parameter (see 
  arXiv:1112.0326 for details)

Each of these spectral parameters is computed directly from the 
primordial power spectra computed by ModeCode at the pivot scale.

Using ModeChord with GetDist
----------------------------

Output files can be processed with standard GetDist tools:
http://cosmologist.info/cosmomc/readme.html#Analysing

Example input files for getdist can be found in the relevant Models
directory as ``Models/*/distparams_*`` .

Adding new inflationary models
------------------------------

Single field inflationary models beyond those provided can be simply 
added to ModeCode by adding the following functions to 
camb/modpk_potential.f90:

- V(phi) in the function pot(phi)

- the first derivative of V in the function dVdphi(phi)

- the second derivative of V in the function d2Vdphi2(phi)

- an approximate expression for phi(N_pivot) (e.g., derived using slow 
  roll relations) in the function initialphi; this is used to compute a 
  reasonable first guess for the initial conditions given the shape of 
  the potential

Although only specification of V(phi) is absolutely necessary (with 
vnderivs=T the code will attempt to compute numerical derivatives of the 
potential, and the function initialphi defaults to the set value of 
phi_init if no phi(N_pivot) relation is given), the results of the code 
are generally much more reliable if all four of these functions can be 
provided.

The potential choices provided in the code can be used as templates for 
each of these functions. For example, a new form of the potential corresponding 
to potential_choice=15 would require adding a 'case(15)' statement to each 
of the four functions described above with the V(phi) parameters and 
functions specified following the examples provided.
