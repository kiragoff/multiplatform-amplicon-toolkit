# Amplicon Sequencing Workflows

This repository is a curated collection of scripts and workflows for processing amplicon sequencing data. Whether you are dealing with classic **16S** (bacterial), variable-length **18S/ITS** (eukaryotic/fungal), or emerging **full-length Nanopore** datasets, you'll find a pipeline here to take you from raw reads to publication-ready plots.

Workflows are available utilizing both **DADA2** (via R) and **QIIME2** (via shell scripts), followed by downstream analysis  and visualization using R (primarily using the **phyloseq** package, but pulling in others as needed).

---

## Critical Information Regarding Analyzing Multiple Sequencing Runs

> [!WARNING]
> There are distinct workflows provided for single sequencing runs versus multi-run analyses. **Do not pool raw data from multiple sequencing runs before denoising.** 
> 
> Each individual sequencing run possesses its own unique error profile. Running DADA2's error-learning algorithms on pooled runs can actually *introduce* more artificial errors than you started with. Denoise separately, then merge!
>
> While your target gene region (ie, v4v5) and starting positions (trimLeft) must be identical across all runs to merge successfully, you can and should vary your truncation lengths (truncLen) and quality filters (maxEE) to fit each run's unique quality profile and maximize read retention.

---

## Repository Tour & Script Breakdown

### 1. DADA2 RMarkdown Pipelines (`.Rmd`)
These interactive notebooks resolve sequence variants, with approaches tailored to the quirks of target region and sequencing technology.

* **[DADA2-16S-single-run.Rmd](./DADA2-16S-single-run.Rmd)** / **[DADA2-16S-multiple-runs.Rmd](./DADA2-16S-multiple-runs.Rmd)**
    * *Target:* Short-read bacterial 16S rRNA.
    * *Choose your fighter:* Use `single-run` if you have one plate/library. Use `multiple-runs` if you need to learn error rates individually for separate runs before merging the resulting sequence table.
* **[DADA2-18S-ITS-single-run.Rmd](./DADA2-18S-ITS-single-run.Rmd)** / **[DADA2-18S-ITS-multiple-runs.Rmd](./DADA2-18S-ITS-multiple-runs.Rmd)**
    * *Target:* Eukaryotic (18S) and fungal (ITS) regions.
    * *Choose your fighter:* Use `single-run` if you have one plate/library. Use `multiple-runs` if you need to learn error rates individually for separate runs before merging the resulting sequence table.
    * *Note:* Fine-tuned to handle variable-length amplicons without throwing away biologically real length variation. Requires the installation of cutadapt2 on the command line.
* **[DADA2-full16S-nanopore.Rmd](./DADA2-full16S-nanopore.Rmd)**
    * *Target:* High-throughput, long-read full-length (V1–V9) 16S rRNA sequencing generated on Oxford Nanopore (ONT) platforms.
    * *Note:* Unlike the short-read workflows, this pipeline collapses inferred variants into OTUs. This extra step accommodates the higher baseline error rate of Nanopore sequencing, preventing a false explosion of artificial ASVs while still preserving full-length taxonomic resolution.
   * *Versatility Note:* Because this script lacks rigid length truncation, it is fully compatible with long-read 18S or ITS datasets as long as you swap in the appropriate reference database (e.g., PR2 or UNITE).


### 2. QIIME2 Shell Scripts (`.sh`)
*   **[qiime2-single-run.sh](./qiime2-single-run.sh)** / **[qiime2-multi-run.sh](./qiime2-multi-run.sh)**
    * Workflow for processing amplicon sequences in the QIIME2 ecosystem. 
    * *Note:* Feel free to swap DeBlur for DADA2 during denoising, or wrap in vsearch as desired. Can be run with or without collapsing ASVs to OTUs.

### 3. Downstream Visualization & Stats
*   **[phylo-micro.Rmd](./phylo-micro.Rmd)**
    * The fun part! This script contains tools for ecological analysis, including taxonomic bar and bubble plots, alpha/beta diversity, heatmaps, multivariate ordination, and phylogenetic trees. If you have the final sequences, you can also use UniFrac.
    * *Integration Tip:* DADA2 outputs plug directly into this script. QIIME2 outputs will require a little bit of manual formatting/reorganizing to get them to play nice with `phyloseq`.

---

## Intermediate Results & Parameterization

Don't worry about figuring out the correct settings beforehand. A lot are going to be based on your individual datasets. All scripts contain built-in documentation and notes on how to choose parameters based on your intermediate results. 

As you progress through the quality profiles and intermediate plots (like DADA2 error models), the markdown notes will give you tips on how to adjust truncation lengths, maxEE, and filtering thresholds dynamically. 

---

## Customization & R "Guardrails"

Throughout the R scripts, you will find comments indicating that certain metadata or files "must be formatted a certain way." 

These are put in place as guardrails to prevent common code breaks. However, if you are comfortable editing R code, feel free to play around and adapt the scripts to fit your specific metadata structures and experimental designs!

