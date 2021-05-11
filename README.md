# PPCG RNA working group

This repository contains the pipeline used by the Pan Prostate Cancer Group.

## **Requirements**
All tools used in this pipeline are installed at runtime through a singularity container. The only requirements are the ones to run the Snakemake workflow.

- Snakemake >= 6.2
- Singularity >=3.7 (Should work on previous versions although not tested)
- Cookiecutter
- mamba >= 1.2 (optional)
- gsutils (optional)

## **How to run**
1. Clone this github repository.
```
git clone https://github.com/panprostate/RNA.git
```

2. This workflow requires a sample metadata table (tab separated) containing the following fields:
- sample_name = The sample name, by default anything preceding the "_L00" pattern.
- unit_name = The unit/lane name, by default it matches the following pattern ".+\_(L00.)\.+".
- fq1 = Absolute path to the R1 file of the fastq pair.
- fq2 = Absolute path to the R2 file of the fastq pair.

Create this table under the `config/` folder.
As reference, a helper script `createTable.sh` is included under `config/` to create this table. Make sure the table format is correct before proceding.

3. Download the required resources.
```
cd <path-to-cloned-direct>/resources
sh downloadResources.sh
```

4. If your computing enviroment is managed by Slurm, a cluster profile is included at `slurm_profile/`. There are two files that can be adjusted:
- `slurm_profile/config.yaml`

Most likely you will want to adjust only two settings in this file:
```
# Number of cores used by Snakemake to manage the workflow - if increasing, be careful to not have your job killed if not running in an interactive session.

local-cores: 1

# Number of maximum simultaneous jobs that can be run.

jobs: 5
```
- `slurm_profile/settings.json`

This file can be used to include center-specific parameters for your submissions. For example, to specify a partition `panda` on all jobs submissions.
```
"SBATCH_DEFAULTS": "partition=panda job-name={rule}_{wildcards} output=job_logs/slurm-%j.out"
```

5. From the base directory execute Snakemake:
```
snakemake --use-conda --use-singularity --profile slurm_profile
```

The workflow will automatically pull and create a Singularity container from `docker://condaforge/mambaforge:4.10.1-0` and install the necessary packages for each enviroment.