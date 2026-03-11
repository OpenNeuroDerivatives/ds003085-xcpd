
### Post-processing of fmriprep outputs

The eXtensible Connectivity Pipeline- DCAN (XCP-D)
[@mehta2024xcp;@mitigating_2018;@satterthwaite_2013]
was used to post-process the outputs of *fMRIPrep* version 22.0.0
[@esteban2019fmriprep;@esteban2020analysis, RRID:SCR_016216].
XCP-D was built with *Nipype* version 1.10.0 [@nipype1, RRID:SCR_002502].

#### Segmentations

The following atlases were used in the workflow: the Schaefer Supplemented with Subcortical Structures (4S) atlas [@Schaefer_2017;@pauli2018high;@king2019functional;@najdenovska2018vivo;@glasser2013minimal] at 10 different resolutions (1056, 156, 256, 356, 456, 556, 656, 756, 856, and 956 parcels), the Glasser atlas [@Glasser_2016], the Gordon atlas [@Gordon_2014], the Tian subcortical atlas [@tian2020topographic], the HCP CIFTI subcortical atlas [@glasser2013minimal], the MIDB precision brain atlas derived from ABCD data and thresholded at 75% probability [@hermosillo2024precision], and the Myers-Labonte infant atlas thresholded at 50% probability [@myers2023functional].


#### Anatomical data

Native-space T1w images were transformed to MNI152NLin2009cAsym space at 1 mm3
resolution.


#### Functional data

For each of the four BOLD runs found per subject (across all tasks and sessions),
the following post-processing was performed.

 Non-steady-state volumes were extracted from the preprocessed confounds and were discarded from both the BOLD data and nuisance regressors.  Framewise displacement was calculated from the motion parameters using the formula from @power_fd_dvars, with a head radius of 50 mm. Volumes with framewise displacement greater than 0.2 mm were flagged as high-motion outliers for the sake of later censoring [@power_fd_dvars]. In total, 36 nuisance regressors were selected from the preprocessing confounds,
according to the '36P' strategy.
These nuisance regressors included
six motion parameters, mean global signal, mean white matter signal,
mean cerebrospinal fluid signal with their temporal derivatives,
and quadratic expansion of six motion parameters, tissue signals and
their temporal derivatives [@benchmarkp;@satterthwaite_2013].

The BOLD data were converted to NIfTI format, despiked with *AFNI*'s *3dDespike*,
and converted back to CIFTI format.

Nuisance regressors were regressed from the BOLD data using a denoising method based on *Nilearn*'s
approach.
Any volumes censored earlier in the workflow were first cubic spline interpolated in the BOLD data. Outlier volumes at the beginning or end of the time series were replaced with the closest low-motion volume's values, as cubic spline interpolation can produce extreme extrapolations. The timeseries were band-pass filtered using a(n) second-order Butterworth filter, in order to retain signals between 0.01-0.08 Hz. The same filter was applied to the confounds. The resulting time series were then denoised via linear regression, in which the low-motion volumes from the BOLD time series and confounds were used to calculate parameter estimates, and then the interpolated time series were denoised using the low-motion parameter estimates. The interpolated time series were then censored using the temporal mask. The denoised BOLD was then smoothed using *Connectome Workbench* with a Gaussian kernel
(FWHM=6 mm).
 
The amplitude of low-frequency fluctuation (ALFF) [@alff] was computed by transforming
the mean-centered, standard deviation-normalized, denoised BOLD time series to the frequency
domain using the Lomb-Scargle periodogram [@lomb1976least;@scargle1982studies;@townsend2010fast;@taylorlomb].
The power spectrum was computed within the 0.01-0.08 Hz frequency band and the
mean square root of the power spectrum was calculated at each voxel to yield voxel-wise ALFF
measures.
The resulting ALFF values were then multiplied by the standard deviation of the denoised BOLD time
series to retain the original scaling.
 The ALFF maps were smoothed with the Connectome Workbench using a Gaussian kernel (FWHM=6 mm).

For each hemisphere, regional homogeneity (ReHo) [@jiang2016regional] was computed using
surface-based *2dReHo* [@surface_reho].
Specifically, for each vertex on the surface, the Kendall's coefficient of concordance (KCC)
was computed with nearest-neighbor vertices to yield ReHo.
For the subcortical, volumetric data, ReHo was computed with neighborhood voxels using *AFNI*'s
*3dReHo* [@taylor2013fatcat].

Processed functional timeseries were extracted from residual BOLD using
Connectome Workbench [@marcus2011informatics] for the atlases.
Corresponding pair-wise functional connectivity between all regions was computed for each atlas,
which was operationalized as the Pearson's correlation of each parcel's unsmoothed timeseries with
the Connectome Workbench.
In cases of partial coverage, uncovered vertices (values of all zeros or NaNs) were either
ignored (when the parcel had >50.0% coverage)
or were set to zero (when the parcel had <50.0% coverage).


Many internal operations of *XCP-D* use
*AFNI* [@cox1996afni;@cox1997software],*Connectome Workbench* [@marcus2011informatics], 
*ANTS* [@avants2009advanced],
*TemplateFlow* version 25.1.1 [@ciric2022templateflow],
*matplotlib* version 3.10.5 [@hunter2007matplotlib],
*Nibabel* version 5.3.2 [@brett_matthew_2022_6658382],
*Nilearn* version 0.13.0 [@abraham2014machine],
*numpy* version 2.2.6 [@harris2020array],
*pybids* version 0.21.0 [@yarkoni2019pybids],
and *scipy* version 1.15.3 [@2020SciPy-NMeth].
For more details, see the *XCP-D* website (https://xcp-d.readthedocs.io).


#### Copyright Waiver

The above methods description text was automatically generated by *XCP-D*
with the express intention that users should copy and paste this
text into their manuscripts *unchanged*.
It is released under the [CC0](https://creativecommons.org/publicdomain/zero/1.0/) license.

#### References

