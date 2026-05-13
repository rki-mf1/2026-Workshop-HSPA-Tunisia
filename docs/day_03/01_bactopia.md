# De novo Genome Assembly with `Bactopia`

![bactopia-logo](https://bactopia.io/img/bactopia-logo.png)

## 🎯 Learning goals

By the end of this practical, you should be able to:

- understand why _de novo_ assembly is useful for bacterial genome analysis
- prepare a Bactopia samplesheet for multiple Illumina paired-end samples
- run Bactopia using Singularity containers
- inspect the main output folders created by Bactopia
- find assembled genome files for downstream analysis
- generate a simple summary report for all samples

## Overview

`bactopia` is a workflow for bacterial genome analysis. In this practical, we will use it for de novo assembly of Illumina paired-end sequencing reads.

The sequencing data are already available on the workshop server. You will first organize the input FASTQ files, then create a Bactopia samplesheet, and finally run Bactopia on multiple samples using Singularity.

The general workflow is:

```text
paired-end FASTQ files → samplesheet → Bactopia → assemblies and reports
```

> [!NOTE]
>
> For detailed tutorials, please check beautiful and [detailed documentation provided by Bactopia developers](https://bactopia.io/)

---

## 1. Activate the Bactopia environment

First, activate the Conda environment that contains `bactopia` and `nextflow`.

```bash
conda activate bactopia
```

Check that Bactopia is available.

```bash
bactopia --version
```

Check that Singularity is available.

```bash
singularity --version
```

If both commands print version information, you are ready to continue.

---

## 2. Prepare the working directory

Move to the main workshop repository.

```bash
cd ~/2026-Workshop-HSPA-Tunisia
```

Create a working directory for this practical.

```bash
mkdir -p analyses/bactopia
cd analyses/bactopia
```

---

## 3. Check the input FASTQ files

The FASTQ files are already available on the server.

Check the files.

```bash
ls -lh input/
```

You should see paired-end files for multiple samples.

---

## 4. Create a Bactopia samplesheet

Bactopia can create a samplesheet automatically from a directory of FASTQ files.

For this workshop, we are working with *Klebsiella pneumoniae*. We will provide an approximate genome size of 5.7 Mb.

```bash
bactopia prepare \
  --path path/to/raw/fastq/files \
  --species "Klebsiella pneumoniae" \
  --genome-size 5700000 \
  > bactopia_samplesheet.txt
```

Check the samplesheet.

```bash
head bactopia_samplesheet
```

---

## 5. Run Bactopia on multiple samples

To avoid using too much space we will share loaction where we store Singularity images used in Bactopia. We can specify this location with variable `NXF_SINGULARITY_CACHEDIR`. We already downloaded these Singularity images and you can use them.

> If you are running Bactopia for the first time it may need to download Singularity images. Later runs are usually faster because the images are cached.

```bash
export NXF_SINGULARITY_CACHEDIR=$PWD/singularity_cache

bactopia \
  --samples bactopia_samplesheet \
  --coverage 100 \
  --max_cpus 4 \
  --outdir bactopia_kp \
  -profile singularity \
  -resume
```

Important options used here:

| Option | Meaning |
|---|---|
| `--samples bactopia_samplesheet` | Use the samplesheet created with `bactopia prepare` |
| `--coverage 100` | Downsample reads to a maximum target coverage of 100x |
| `--max_cpus 4` | Use up to 4 CPU cores |
| `--outdir bactopia_kp` | Write output files to this directory |
| `-profile singularity` | Run workflow steps using Singularity containers |
| `-resume` | Continue from completed steps if the command is restarted |

Bactopia will run several steps, including read QC, assembly, assembly QC, annotation, MLST, and AMR screening.

---

## 6. Check whether the run completed

If the workflow completed successfully, Bactopia will print an execution summary in the terminal.

List the output directory.

```bash
ls bactopia_kp
```

---

## 7. Find the assembled genomes

Find assembled genome FASTA files in Bactopia's output directory. 
These files are useful for downstream analyses such as cgMLST and phylogenetics.

```bash
ls bactopia_kp/*/main/assembler/*.fna.gz
```

---

## 8. Create a Bactopia summary

Bactopia can summarize results across all samples.

```bash
bactopia summary \
  --bactopia-path bactopia_kp
```

Upon completion this will produce three files:

| File	                    | Description                                                                               | 
|---------------------------|-------------------------------------------------------------------------------------------|
| `bactopia-report.tsv`	    | A tab-delimited file with more than 70 columns of results from steps in Bactopia.         |
| `bactopia-exclude.tsv`	| A tab-delimited file with samples that should likely be excluded from further analysis    |
| `bactopia-summary.txt`	| A simple summary of quality grades and excluded counts.                                   |


### 💬 Discussion

Try to answer:

1. How many samples were processed?
2. Did any samples fail or appear in `bactopia-exclude.tsv`?
3. Which samples produced assemblies?
4. Are the assembly sizes close to the expected *Klebsiella pneumoniae* genome size?
5. Which outputs would you use for cgMLST or phylogenetic analysis later in the workshop?

---

## 📌 Summary

In this practical, you used `Bactopia` to process multiple Illumina paired-end bacterial samples using Singularity.

| Step                  | Command or file                                                   | Purpose                                               |
|-----------------------|-------------------------------------------------------------------|-------------------------------------------------------|
| Prepare inputs        | `input/`                                                          | Store paired-end FASTQ files                          |
| Create samplesheet    | `bactopia prepare`                                                | Detect samples and FASTQ pairs and output samplesheet |
| Run Bactopia          | `bactopia --samples bactopia_samplesheet -profile singularity`    | Process all samples                                   |
| Resume workflow       | `-resume`                                                         | Continue from completed steps                         |
| Summarize results     | `bactopia summary`                                                | Create combined reports                               |
| Assemblies            | `*.fna.gz`                                                        | Genome assemblies for downstream analysis             |

---

## 📝 Evaluation

Please fill in this [daily feedback evaluation](https://survey.lamapoll.de/HSPA-Tunisia-Bioinformatics-Workshop-Daily-Feedback) at the end of the day.

---

[⬅ Back to Day 03 overview](README.md)