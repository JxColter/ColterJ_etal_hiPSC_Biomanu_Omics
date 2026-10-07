# Multi-omics analysis of environmentally adaptive cell-state remodeling during human iPSC biomanufacturing
https://doi.org/10.64898/2026.05.26.727850

## Overview

This repository contains the analysis workflows, processed source data, statistical outputs, and figure-source tables supporting the accompanying manuscript on condition-dependent cell-state variation during human induced pluripotent stem cell (hiPSC) biomanufacturing.

The repository is organized into four analytical components:

1. Proteomics
2. Metabolomics
3. Transcriptomics
4. Cross-dataset developmental analytics

The analyses evaluate how culture platform, dynamic suspension, agitation rate, and oxygenation affect molecular features of hiPSC state. Experimental transcriptional responses are additionally compared with gastrulation-stage developmental reference programs to quantify Epiblast-aligned and lineage-aligned transcriptional changes.

## Coding structure utilized

```text
Experimental/
|-- Proteomics/
|   |-- data/
|   |-- processed/
|   |-- analysis/
|   `-- scripts/
|
|-- Metabolomics/
|   |-- data/
|   |-- analysis/
|   `-- scripts/
|
|-- Transcriptomics/
|   |-- data/
|   |   `-- counts/
|   |-- reference_data/
|   |-- analysis/
|   |-- cross_analysis/
|   `-- scripts/
|
`-- Cross Dataset Analytics/
    |-- source_data/
    |-- analysis/
    `-- scripts/
```

Directory names may differ slightly in the uploaded repository. Versioned analysis folders and filenames identify the workflow revision used to generate each output.

## 1. Proteomics

### Experimental scope

The proteomics analyses include:

- Static monolayer culture
- Static suspended aggregate culture
- Dynamic suspended aggregate culture
- Dynamic culture at 20, 60, and 100 RPM

### Primary processing

Mass-spectrometry data were processed in MaxQuant. Reverse identifications, potential contaminants, and proteins identified only by modification site were removed before statistical analysis.

The platform experiment was analyzed using a connected linear model integrating three pairwise SILAC comparisons. The dynamic agitation experiment was analyzed using a three-group model based on log2-transformed LFQ intensities.

Protein-level variances were stabilized using empirical-Bayes moderation. P-values were adjusted within each contrast using the Benjamini-Hochberg procedure.

### Thresholds

```text
Differential protein abundance:
adjusted P < 0.05
absolute log2 fold change >= log2(1.5)
```

### Key outputs

Proteomics source data include:

- Platform-specific moderated protein statistics
- Pairwise platform comparisons
- RPM-specific moderated protein statistics
- Pairwise RPM comparisons
- Platform and RPM pathway-enrichment tables
- Relative pathway-score matrices
- Full pluripotency-associated protein panels
- Figure-source tables

### Interpretation of missing proteins

Full marker panels include proteins that were not detected in the measured proteome. These entries are retained for visual consistency and are explicitly marked as not identified. A displayed zero differential value for an unidentified protein must not be interpreted as evidence of biological equivalence.

## 2. Metabolomics

### Experimental scope

The metabolomics analysis includes intracellular metabolite measurements from dynamic cultures maintained under:

- Approximately 19.7% atmospheric oxygen
- 5% atmospheric oxygen
- 100, 120, and 150 RPM
- Days 2 and 4

### Preprocessing

The workflow performs:

- Median blank subtraction
- Replacement of negative corrected intensities with zero
- Detection-frequency filtering
- Sample-to-blank filtering
- Probabilistic quotient normalization
- Log2 transformation

### Statistical analysis

Differential metabolite abundance was assessed using two-group linear models with empirical-Bayes moderation of metabolite-level residual variances. Repeated day-2 and day-4 measurements from the same independently operated bioreactor were averaged for across-day pooled analyses, preventing repeated measurements from being treated as independent biological replicates.

### Thresholds

```text
Differential metabolite abundance:
adjusted P < 0.05
absolute log2 fold change >= log2(1.5)
```

### Key outputs

Metabolomics source data include:

- Sample metadata
- Metabolite quality-control summaries
- PQN-normalized abundance matrices
- Log2-normalized abundance matrices
- Pooled moderated statistics
- Day-specific and condition-specific contrasts
- Condition-mean heatmap matrices
- Volcano-plot source data

### Exploratory oxygen reassignment

An exploratory sensitivity analysis reassigned the nominal normoxia 150 RPM replicate `n150m3` to hypoxia because its measured profile appeared hypoxia-like. This exploratory analysis is stored separately and does not replace the nominal experimental assignment used for primary inference.

## 3. Transcriptomics

### Experimental scope

Bulk RNA-sequencing analyses include:

- Initial post-aggregate-formation samples
- Dynamic cultures at days 2 and 4
- Normoxia and hypoxia
- 100, 120, and 150 RPM

### Count handling

- Sequencing lanes from the same library were summed.
- Technical duplicate libraries from the same biological material were merged.
- Technical lanes were not treated as independent biological replicates.
- Genes were retained when at least 10 counts were present in at least three biological samples.

### Differential expression

Differential expression was assessed from raw integer counts using PyDESeq2. Benjamini-Hochberg correction was applied independently within each contrast.

For analyses pooled across days 2 and 4, repeated measurements from the same bioreactor were summed to create one subject-level count profile. Day-specific comparisons retained the biological replicates collected at the corresponding timepoint.

### Thresholds

```text
FDR-significant:
adjusted P < 0.05

Moderate DEG:
adjusted P < 0.05
absolute log2 fold change >= 0.2

Large-effect DEG:
adjusted P < 0.05
absolute log2 fold change >= log2(1.5)
```

### Primary contrasts

The principal pooled comparisons are:

- Dynamic versus initial state
- 150 versus 100 RPM
- 150 versus 120 RPM
- 120 versus 100 RPM
- Normoxia versus hypoxia

Conditional and longitudinal analyses include:

- Day-2 and day-4 oxygen comparisons
- Day-specific RPM comparisons
- Dynamic conditions versus initial state
- Temporal maintenance, reinforcement, attenuation, and emergence of selected regulatory features

### Expression visualization

For visualization only, raw counts were converted to counts per million, log2 transformed after addition of a pseudocount, and summarized as biological-sample or condition means. Heatmaps use gene-wise Z scores. These normalized matrices were not used as input for primary differential-expression testing.

### Pathway analysis

Over-representation analysis was performed using the genes tested in the corresponding experimental contrast as the statistical background. Gene Ontology Biological Process terms were evaluated using coupled, upregulated, and downregulated DEG sets, followed by Benjamini-Hochberg adjustment.

### Key outputs

Transcriptomics source data include:

- Sample metadata and gene-filter audits
- Raw and filtered count matrices
- Complete all-tested-gene tables for each contrast
- Thresholded DEG tables
- DEG-count summaries
- Volcano-plot source data
- Module-expression source tables
- Temporal regulatory classifications
- ORA source tables

The `primary_collapsed` analysis is the valid branch for biological inference. Any lanes-separate output is exploratory pseudoreplication and must not be used for confirmatory conclusions.

## 4. Cross-dataset developmental analytics

### Developmental reference

The developmental reference is the human gastrulation single-cell RNA-sequencing dataset E-MTAB-9388. The analysis uses the prepared predicted annotations as the primary cell-state labels. Ambiguous cells and cells with prediction confidence below 0.50 were excluded from developmental comparisons.

The main reference states are:

- Epiblast
- Primitive streak
- Mesoderm
- Definitive endoderm

Reference cells originate from one embryo. Reference differential-expression results therefore define cell-state-associated signatures and must not be interpreted as replicated embryo-level inference.

### Reference preprocessing

Raw reference counts were normalized to counts per million and stored as log1p(CPM) in the annotated H5AD object. Raw integer counts are retained in the H5AD counts layer.

Reference differential expression was calculated between Epiblast and each comparator state using two-sided Mann-Whitney U tests on log1p(CPM) values. Benjamini-Hochberg correction was applied independently within each comparison.

### Cross-dataset matching

Experimental and reference results were matched using standardized uppercase gene symbols. Cross-analysis was restricted to genes meeting the moderate DEG threshold in both the experimental and reference comparison:

```text
adjusted P < 0.05
absolute log2 fold change > 0.2
```

### Directional alignment

All developmental reference contrasts are oriented as:

```text
Epiblast versus developmental state
```

Genes with experimental and reference log2 fold changes of the same sign are classified as:

```text
Epiblast-aligned / convergent
```

Genes with opposing signs are classified as:

```text
Lineage-aligned / divergent
```

These categories describe directional intersection with a developmental contrast. They do not demonstrate acquisition of a differentiated identity or completion of a lineage transition.

### Reinforcement metrics

For gene `g`, the signed reinforcement ratio is:

```text
R_g = experimental log2FC / reference log2FC
```

Interpretation:

```text
R_g > 0: directional convergence / Epiblast alignment
R_g < 0: directional divergence / lineage alignment
```

The effect difference is:

```text
delta log2FC_g = experimental log2FC - reference log2FC
```

The ratio is calculated only after joint moderate-DEG filtering to reduce instability from reference effects near zero.

### Pooled developmental classification

Across primitive streak, mesoderm, and definitive endoderm:

- Epiblast-aligned: convergent in at least one comparison and never divergent
- Lineage-aligned: divergent in at least one comparison and never convergent
- Mixed: convergent in one comparison and divergent in another

Mixed genes are retained in source data but excluded from pooled convergent or divergent enrichment analyses.

### Cross-analysis contrasts

The principal experimental comparisons are:

- Dynamic versus initial state
- 150 versus 100 RPM
- Normoxia versus hypoxia

### Key outputs

Cross-analysis outputs include:

- Developmental-reference DEG tables
- Reference UMAP coordinates and annotation maps
- Joint experimental-reference DEG tables
- Reinforcement scatter plots
- Epiblast-aligned and lineage-aligned gene counts
- Lineage-specific and pooled volcano plots
- Alignment-specific expression barplots
- Effect-size heatmaps
- Convergent and divergent ORA tables
- Figure-source data and workflow manifests

## Reproducibility and versioning

All analyses are implemented as versioned standalone Python scripts. New revisions use incremented filenames and write to separate versioned output folders. Scripts do not depend on earlier script versions unless explicitly documented.

Example:

```text
analysis/1_4_1/
cross_analysis/reinforcement/2_1/
cross_analysis/dynamic_initial/2_4_1/
cross_analysis/agitation_oxygen/1_1/
```

Each final analysis folder may contain:

```text
source_data/
plots/
heatmaps/
volcanoes/
barplots/
ora/
legends/
summaries/
manifest JSON files
```

Manifest files record workflow versions, thresholds, input locations, and output conventions.

## Software environment

Analyses were run using Python workflows with packages including:

- numpy
- pandas
- scipy
- statsmodels
- matplotlib
- seaborn
- PyDESeq2
- gseapy
- anndata
- h5py
- scikit-learn
- umap-learn
- adjustText

## Citation

Please cite the associated manuscript when using these data or workflows.

```text
Colter, J. et al. Multi-omics analyses reveal environmentally adaptive cell-state remodeling during human iPSC biomanufacturing. Manuscript in submission.
```

## Contact

```text
James Colter
University of Calgary
Email: jdcolter@ucalgary.ca
```
