# Freesurfer_Processing
Guideline of Freesurfer basics and Codes from various repositories to minimise data processing pipeline search
These scripts are mainly intended for running FreeSurfer on powerful workstation computers in a lab. They are **not** suitable for running FreeSurfer on high performance computing (HPC) clusters which use a job scheduler like Slurm.

![Vis](https://github.com/dfsp-spirit/freesurfer_parallel_scripts/blob/main/web/freesurfer_parallel_scripts.png?raw=true "freesurfer_parallel_scripts")

## Hardware requirements

No special hardware is required. You will obviously need a multi-core computer (almost all modern machines are multi-core, even laptops) that can run FreeSurfer and about 1.5 - 2 GB of free RAM per CPU core you want to use.

An example would be a quad-core computer with 16 GB of RAM (4x2 GB for FreeSurfer, leaving another 8 for the operating system and all other processes).

This involves running the `recon-all` pipeline, which takes 12 - 20h per subject. Typically `qcache` is also run as part of the pipeline, to map the resulting data (like per-vertex cortical thickness in native space) to standard space (fsaverage).

We recommend the following steps:

* First convert the raw DICOM files to NIFTI format using [dcm2nii](https://www.nitrc.org/plugins/mwiki/index.php/dcm2nii:MainPage).
  - Note: If you have `.nii.gz` files, make sure to extract them to get `.nii` files. E.g., in bash: `for nf in *.nii.gz; do gunzip "$nf"; done`
  - Note: You may have more than one structural scan per subject at this time (e.g., if the first one was aborted or looked bad during scanning). That is fine, you have to pre-process both with FreeSurfer first to decide which one to use later.
* Use the script [cross-sectional/preproc_reconall_parallel.bash](./cross-sectional/preproc_reconall_parallel.bash) from this repository to run recon-all for all NIFTI files in parallel.
  - Note: If you have TSE (or t2-weighted) files in addition to t1-weighted files, please adapt the [cross-sectional/preproc_reconall_single_subject.bash](https://github.com/dfsp-spirit/freesurfer_parallel_scripts/blob/main/cross-sectional/preproc_reconall_single_subject.bash) script to make sure they are used during pre-processing.
* If you want to add a global brain measure (like total brain volume) as a covariate during modeling later, run the script [tools/extract_total_brain_measures.bash](./tools/extract_total_brain_measures.bash) to compute the measures for all subjects.

Note: If you will get data for a second wave later and intend to run a longitudinal analysis then, please read about the longitudinal pipeline now to avoid duplicate work later.
