# nf-core/demultiplex: Usage

## :warning: Please read this documentation on the nf-core website: [https://nf-co.re/demultiplex/usage](https://nf-co.re/demultiplex/usage)

> _Documentation of pipeline parameters is generated automatically from the pipeline schema and can no longer be found in markdown files._

## Introduction

## input

Input samplesheet samplesheet.csv is used to link demultiplexer-specific samplesheets to flowcell runs. The input samplesheet is a comma-separated file that contains four columns: id, samplesheet, lane, flowcell and (for fqtk only) per_flow_cell_manifest.

Column descriptions:

| Column                   | Description                                                                                                                            |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                     | flowcell id                                                                                                                            |
| `samplesheet`            | Full path to the demultiplexer-specific `<demultiplexer-name>_SampleSheet.csv` file containing the sample information and indexes      |
| `lane`                   | Optional lane number. When a lane number is provided, only the given lane will be demultiplexed                                        |
| `flowcell`               | Full path to the Illumina sequencer output directory or a `tar.gz` file containing the contents of the directory                       |
| `per_flowcell_manifest`  | (for fqtk only) Full path to the flowcell manifest, containing the fastq file names and read structures                                |

The input samplesheet is passed with the nf-core parameter `--input`:

```bash
nextflow run demultiplex --input '[full path to input samplesheet file]'
```

Example input_samplesheet.csv:
```console
id,samplesheet,lane,flowcell
DDMMYY_SERIAL_NUMBER_FC,/path/to/demultiplexer_SampleSheet.csv,1,/path/to/sequencer/output
DDMMYY_SERIAL_NUMBER_FC,/path/to/demultiplexer_SampleSheet.csv,2,/path/to/sequencer/output
DDMMYY_SERIAL_NUMBER_FC2,/path/to/demultiplexer_SampleSheet2.csv,1,/path/to/sequencer/output2
DDMMYY_SERIAL_NUMBER_FC3,/path/to/demultiplexer_SampleSheet3.csv,3,/path/to/sequencer/output3
```

Note that each demultiplexing software uses a distinct samplesheet format. Below are examples for demultiplexer-specific samplesheets and their respective input samplesheets. 


### Samplesheet for bcl2fastq

Please see the following examples to format `SampleSheet.csv` for [bcl2fastq](https://raw.githubusercontent.com/nf-core/test-datasets/demultiplex/samplesheet/1.3.0/b2fq-samplesheet.csv) at test-datasets. 

Example input `samplesheet.csv` for use with `--demultiplexer bclconvert` option:

```console
id,samplesheet,lane,flowcell
DDMMYY_SERIAL_NUMBER_FC,/path/to/bcl2fastq_SampleSheet.csv,1,/path/to/sequencer/output
DDMMYY_SERIAL_NUMBER_FC,/path/to/bcl2fastq_SampleSheet.csv,2,/path/to/sequencer/output
DDMMYY_SERIAL_NUMBER_FC2,/path/to/bcl2fastq_SampleSheet.csv,1,/path/to/sequencer/output2
DDMMYY_SERIAL_NUMBER_FC3,/path/to/bcl2fastq_SampleSheet.csv,3,/path/to/sequencer/output3
```

The example demultiplexer-specific samplesheet, bclconvert_SampleSheet.csv, should contain a header labeled [Data]. The minimum requirement for this file is three columns: Sample_ID, Sample_name, and index (in the case of single-index sequencing):

```console
[Data]			
Sample_ID,Sample_name,index
1,Sample1,TAAGGCGA
2,Sample2,TAAGGCGA
```

In addition, for dual-index sequencing there should be an optional column named index2:

```console
[Data]			
Sample_ID,Sample_name,index,index2
1,Sample1,TAAGGCGA,CGGAGAGA
2,Sample2,TAAGGCGA,ATTAGACG
```

If lane splitting should not be done, specify this in the [Settings] section inside the `bcl2fastq_SampleSheet.csv` file. Currently the lane splitting can not be passed as a command line option for the nf-core pipeline. 

```console
[Settings]
NoLaneSplitting

[Data]			
Sample_ID,Sample_name,index,index2
1,Sample1,TAAGGCGA,CGGAGAGA
2,Sample2,TAAGGCGA,ATTAGACG
```

[example bcl2fastq_SampleSheet.csv file from nf-core test-dataset]()

### Samplesheet for bclconvert

Note that `BCL convert` can also accept a minimal samplesheet formatted for `bcl2fastq`. 

Example input `samplesheet.csv` for use with `--demultiplexer bclconvert` option:

```console
id,samplesheet,lane,flowcell
DDMMYY_SERIAL_NUMBER_FC,/path/to/bclconvert_SampleSheet.csv,1,/path/to/sequencer/output
DDMMYY_SERIAL_NUMBER_FC,/path/to/bclconvert_SampleSheet.csv,2,/path/to/sequencer/output
DDMMYY_SERIAL_NUMBER_FC2,/path/to/bclconvert_SampleSheet.csv,1,/path/to/sequencer/output2
DDMMYY_SERIAL_NUMBER_FC3,/path/to/bclconvert_SampleSheet.csv,3,/path/to/sequencer/output3
```

If lane splitting must be avoided, specify that in the `bclconvert_SampleSheet.csv` file. Currently the lane splitting can not be passed as a command line option for the pipeline. 

```console
[BCLConvert_Settings]
NoLaneSplitting

[Data]			
Sample_ID,Sample_name,index,index2
1,Sample1,TAAGGCGA,CGGAGAGA
2,Sample2,TAAGGCGA,ATTAGACG
```


### Samplesheet for fqtk

When using the demultiplexer fqtk, the samplesheet must contain an additional column `per_flowcell_manifest`. The column `per_flowcell_manifest` must contain two headers `fastq` and `read_structure`. As shown in the [example](https://raw.githubusercontent.com/fulcrumgenomics/nf-core-test-datasets/fqtk/testdata/sim-data/per_flowcell_manifest.csv) provided each row must contain one fastq file name and the correlating read structure.

Example input `samplesheet.csv` for use with `--demultiplexer fqtk` option:

```console
id,samplesheet,lane,flowcell,per_flowcell_manifest
DDMMYY_SERIAL_NUMBER_FC,/path/to/fqtk_SampleSheet.csv,1,/path/to/sequencer/output,/path/to/flowcell/manifest.csv
DDMMYY_SERIAL_NUMBER_FC,/path/to/fqtk_SampleSheet1.csv,2,/path/to/sequencer/output,/path/to/flowcell/manifest1.csv
DDMMYY_SERIAL_NUMBER_FC2,/path/to/fqtk_SampleSheet2.csv,1,/path/to/sequencer/output2,/path/to/flowcell/manifest2.csv
DDMMYY_SERIAL_NUMBER_FC3,/path/to/fqtk_SampleSheet3.csv,3,/path/to/sequencer/output3,/path/to/flowcell/manifest3.csv
```

[example fqtk_SampleSheet.csv from test-dataset](https://github.com/fulcrumgenomics/nf-core-test-datasets/raw/fqtk/testdata/sim-data/fqtk_samplesheet.csv)
Note that `flowcell`/`run_dir` must lead to a `tar.gz` for compatability with the demultiplexers sgdemux and fqtk.



### Samplesheet for sgdemux

Please see the following examples to format `SampleSheet.csv` for [sgdemux](https://github.com/nf-core/test-datasets/blob/demultiplex/testdata/sim-data/out.sample_meta.csv) 


## Running the pipeline

The typical command for running the pipeline is as follows:

```console
nextflow run nf-core/demultiplex --input samplesheet.csv --outdir <OUTDIR> -profile docker
```

This will launch the pipeline with the `docker` configuration profile. See below for more information about profiles.

Note that the pipeline will create the following files in your working directory:

```bash
work                # Directory containing the nextflow working files
<OUTDIR>            # Finished results in specified location (defined with --outdir)
.nextflow_log       # Log file from Nextflow
# Other nextflow hidden files, eg. history of pipeline runs and old logs.
```

If you wish to repeatedly use the same parameters for multiple runs, rather than specifying each flag in the command, you can specify these in a params file.

Pipeline settings can be provided in a `yaml` or `json` file via `-params-file <file>`.

> ⚠️ Do not use `-c <file>` to specify parameters as this will result in errors. Custom config files specified with `-c` must only be used for [tuning process resource specifications](https://nf-co.re/docs/usage/configuration#tuning-workflow-resources), other infrastructural tweaks (such as output directories), or module arguments (args).
> The above pipeline run specified with a params file in yaml format:

```bash
nextflow run nf-core/demultiplex -profile docker -params-file params.yaml
```

with `params.yaml` containing:

```yaml
input: './samplesheet.csv'
outdir: './results/'
genome: 'GRCh37'
input: 'data'
<...>
```

You can also generate such `YAML`/`JSON` files via [nf-core/launch](https://nf-co.re/launch).

### Updating the pipeline

When you run the above command, Nextflow automatically pulls the pipeline code from GitHub and stores it as a cached version. When running the pipeline after this, it will always use the cached version if available - even if the pipeline has been updated since. To make sure that you're running the latest version of the pipeline, make sure that you regularly update the cached version of the pipeline:

```bash
nextflow pull nf-core/demultiplex
```

### Reproducibility

It is a good idea to specify a pipeline version when running the pipeline on your data. This ensures that a specific version of the pipeline code and software are used when you run your pipeline. If you keep using the same tag, you'll be running the same version of the pipeline, even if there have been changes to the code since.

First, go to the [nf-core/demultiplex releases page](https://github.com/nf-core/demultiplex/releases) and find the latest pipeline version - numeric only (eg. `1.3.1`). Then specify this when running the pipeline with `-r` (one hyphen) - eg. `-r 1.3.1`. Of course, you can switch to another version by changing the number after the `-r` flag.

This version number will be logged in reports when you run the pipeline, so that you'll know what you used when you look back in the future. For example, at the bottom of the MultiQC reports.

To further assist in reproducbility, you can use share and re-use [parameter files](#running-the-pipeline) to repeat pipeline runs with the same settings without having to write out a command with every single parameter.

> 💡 If you wish to share such profile (such as upload as supplementary material for academic publications), make sure to NOT include cluster specific paths to files, nor institutional specific profiles.

## Core Nextflow arguments

> **NB:** These options are part of Nextflow and use a _single_ hyphen (pipeline parameters use a double-hyphen).

### `-profile`

Use this parameter to choose a configuration profile. Profiles can give configuration presets for different compute environments.

Several generic profiles are bundled with the pipeline which instruct the pipeline to use software packaged using different methods (Docker, Singularity, Podman, Shifter, Charliecloud, Apptainer, Conda) - see below.

> We highly recommend the use of Docker or Singularity containers for full pipeline reproducibility, however when this is not possible, Conda is also supported.

The pipeline also dynamically loads configurations from [https://github.com/nf-core/configs](https://github.com/nf-core/configs) when it runs, making multiple config profiles for various institutional clusters available at run time. For more information and to see if your system is available in these configs please see the [nf-core/configs documentation](https://github.com/nf-core/configs#documentation).

Note that multiple profiles can be loaded, for example: `-profile test,docker` - the order of arguments is important!
They are loaded in sequence, so later profiles can overwrite earlier profiles.

If `-profile` is not specified, the pipeline will run locally and expect all software to be installed and available on the `PATH`. This is _not_ recommended, since it can lead to different results on different machines dependent on the computer enviroment.

- `test`
  - A profile with a complete configuration for automated testing
  - Includes links to test data so needs no other parameters
- `docker`
  - A generic configuration profile to be used with [Docker](https://docker.com/)
- `singularity`
  - A generic configuration profile to be used with [Singularity](https://sylabs.io/docs/)
- `podman`
  - A generic configuration profile to be used with [Podman](https://podman.io/)
- `shifter`
  - A generic configuration profile to be used with [Shifter](https://nersc.gitlab.io/development/shifter/how-to-use/)
- `charliecloud`
  - A generic configuration profile to be used with [Charliecloud](https://hpc.github.io/charliecloud/)
- `apptainer`
  - A generic configuration profile to be used with [Apptainer](https://apptainer.org/)
- `conda`
  - A generic configuration profile to be used with [Conda](https://conda.io/docs/). Please only use Conda as a last resort i.e. when it's not possible to run the pipeline with Docker, Singularity, Podman, Shifter, Charliecloud, or Apptainer.

### `-resume`

Specify this when restarting a pipeline. Nextflow will use cached results from any pipeline steps where the inputs are the same, continuing from where it got to previously. For input to be considered the same, not only the names must be identical but the files' contents as well. For more info about this parameter, see [this blog post](https://www.nextflow.io/blog/2019/demystifying-nextflow-resume.html).

You can also supply a run name to resume a specific run: `-resume [run-name]`. Use the `nextflow log` command to show previous run names.

### `-c`

Specify the path to a specific config file (this is a core Nextflow command). See the [nf-core website documentation](https://nf-co.re/usage/configuration) for more information.

## Custom configuration

### Resource requests

Whilst the default requirements set within the pipeline will hopefully work for most people and with most input data, you may find that you want to customise the compute resources that the pipeline requests. Each step in the pipeline has a default set of requirements for number of CPUs, memory and time. For most of the steps in the pipeline, if the job exits with any of the error codes specified [here](https://github.com/nf-core/rnaseq/blob/4c27ef5610c87db00c3c5a3eed10b1d161abf575/conf/base.config#L18) it will automatically be resubmitted with higher requests (2 x original, then 3 x original). If it still fails after the third attempt then the pipeline execution is stopped.

To change the resource requests, please see the [max resources](https://nf-co.re/docs/usage/configuration#max-resources) and [tuning workflow resources](https://nf-co.re/docs/usage/configuration#tuning-workflow-resources) section of the nf-core website.

### Custom Containers

In some cases you may wish to change which container or conda environment a step of the pipeline uses for a particular tool. By default nf-core pipelines use containers and software from the [biocontainers](https://biocontainers.pro/) or [bioconda](https://bioconda.github.io/) projects. However in some cases the pipeline specified version maybe out of date.

To use a different container from the default container or conda environment specified in a pipeline, please see the [updating tool versions](https://nf-co.re/docs/usage/configuration#updating-tool-versions) section of the nf-core website.

### Custom Tool Arguments

A pipeline might not always support every possible argument or option of a particular tool used in pipeline. Fortunately, nf-core pipelines provide some freedom to users to insert additional parameters that the pipeline does not include by default.

To learn how to provide additional arguments to a particular tool of the pipeline, please see the [customising tool arguments](https://nf-co.re/docs/usage/configuration#customising-tool-arguments) section of the nf-core website.

### nf-core/configs

In most cases, you will only need to create a custom config as a one-off but if you and others within your organisation are likely to be running nf-core pipelines regularly and need to use the same settings regularly it may be a good idea to request that your custom config file is uploaded to the `nf-core/configs` git repository. Before you do this please can you test that the config file works with your pipeline of choice using the `-c` parameter. You can then create a pull request to the `nf-core/configs` repository with the addition of your config file, associated documentation file (see examples in [`nf-core/configs/docs`](https://github.com/nf-core/configs/tree/master/docs)), and amending [`nfcore_custom.config`](https://github.com/nf-core/configs/blob/master/nfcore_custom.config) to include your custom profile.

See the main [Nextflow documentation](https://www.nextflow.io/docs/latest/config.html) for more information about creating your own configuration files.

If you have any questions or issues please send us a message on [Slack](https://nf-co.re/join/slack) on the [`#configs` channel](https://nfcore.slack.com/channels/configs).

## Azure Resource Requests

To be used with the `azurebatch` profile by specifying the `-profile azurebatch`.
We recommend providing a compute `params.vm_type` of `Standard_D16_v3` VMs by default but these options can be changed if required.

Note that the choice of VM size depends on your quota and the overall workload during the analysis.
For a thorough list, please refer the [Azure Sizes for virtual machines in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes).

## Running in the background

Nextflow handles job submissions and supervises the running jobs. The Nextflow process must run until the pipeline is finished.

The Nextflow `-bg` flag launches Nextflow in the background, detached from your terminal so that the workflow does not stop if you log out of your session. The logs are saved to a file.

Alternatively, you can use `screen` / `tmux` or similar tool to create a detached session which you can log back into at a later time.
Some HPC setups also allow you to run nextflow within a cluster job submitted your job scheduler (from where it submits more jobs).

## Nextflow memory requirements

In some cases, the Nextflow Java virtual machines can start to request a large amount of memory.
We recommend adding the following line to your environment to limit this (typically in `~/.bashrc` or `~./bash_profile`):

```bash
NXF_OPTS='-Xms1g -Xmx4g'
```
