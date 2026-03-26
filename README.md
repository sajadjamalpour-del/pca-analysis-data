# pca-analysis-data

## Overview

This repository contains principal component analysis (PCA) and supplementary data for rat testis research. The files include interactive analysis reports, statistical data, visualization plots, and supplementary materials.

## Software Environment and Versions

### R Environment
- **R version:** 4.3.1 or higher
- **RStudio:** 2023.06.1 or higher (recommended)

### Key R Packages

**Statistical and Differential Expression:**
- `edgeR` v3.42.4 - Differential expression analysis
- `limma` v3.56.2 - Linear models for microarray data
- `DESeq2` v1.40.2 - Alternative DEG analysis (optional)

**Time-series and Clustering:**
- `maSigPro` v1.72.0 - Multivariate time-course analysis
- `cluster` v2.1.4 - Clustering algorithms
- `factoextra` v1.0.7 - Visualization of clustering results

**Functional Enrichment:**
- `gProfiler2` v0.2.2 - Gene ontology and pathway enrichment
- `clusterProfiler` v4.8.2 - Alternative enrichment analysis

**Visualization:**
- `ggplot2` v3.4.3 - Publication-quality graphics
- `pheatmap` v1.0.12 - Heatmaps
- `ComplexHeatmap` v2.16.0 - Advanced heatmap visualizations

**Python Environment (for supplementary analyses):**
- Python 3.9+
- `scikit-learn` v1.3.0 - Machine learning and clustering
- `pandas` v2.0.3 - Data manipulation
- `numpy` v1.24.3 - Numerical computing

### Installation

```r
# Install Bioconductor packages
if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install(c("edgeR", "limma", "DESeq2", "maSigPro", "gProfiler2", "clusterProfiler"))

# Install CRAN packages
install.packages(c("ggplot2", "pheatmap", "factoextra", "cluster"))
```

## Step-by-Step Analysis Workflow

### Analysis Run Order

Follow these steps sequentially to reproduce all analyses:

#### Step 1: QC and Quantification
- **Input:** Raw FASTQ files from ArrayExpress (E-MTAB-6811)
- **Tools:** FastQC, STAR aligner, featureCounts
- **Output:** Gene expression count matrix (`iDEP counts.xlsx`)
- **Script:** `01_qc_quantification.R` (if available) or use iDEP web platform

#### Step 2: Normalization
- **Input:** Raw count matrix
- **Method:** TMM normalization (edgeR)
- **Output:** Normalized expression matrix
- **Script:** `02_normalization.R`
```r
library(edgeR)
y <- DGEList(counts = raw_counts)
y <- calcNormFactors(y, method = "TMM")
```

#### Step 3: Differential Expression Analysis (DEG)
- **Input:** Normalized counts
- **Method:** edgeR quasi-likelihood F-test
- **Output:** Stage-specific DEG lists
- **Script:** `03_deg_analysis.R`
```r
design <- model.matrix(~0 + stage)
y <- estimateDisp(y, design)
fit <- glmQLFit(y, design)
```

#### Step 4: Clustering Analysis
- **Input:** DEG expression profiles
- **Method:** Hierarchical clustering, K-means
- **Output:** Cluster assignments (C1-C5), `Supplementary_Table_S2_symbols.csv`
- **Script:** `04_clustering.R`
- **Key parameters:** `k = 5` clusters, Euclidean distance, Ward linkage

#### Step 5: STEM Profile Analysis
- **Input:** Time-series expression data
- **Method:** maSigPro polynomial regression
- **Output:** Temporal expression profiles, STEM annotations
- **Script:** `05_stem_analysis.R`
```r
library(maSigPro)
data <- make.design.matrix(edesign, degree = 4)
fit <- p.vector(data, Q = 0.05)
```

#### Step 6: Functional Enrichment
- **Input:** Cluster gene lists
- **Method:** gProfiler2 (GO, KEGG, Reactome)
- **Output:** Enrichment results (`supplementary 4.csv`), visualization plots
- **Script:** `06_enrichment.R`
```r
library(gProfiler2)
gostres <- gost(query = gene_list, organism = "rnorvegicus")
```

#### Step 7: Silhouette Analysis
- **Input:** Cluster assignments
- **Method:** Silhouette coefficient (cluster package)
- **Output:** Cluster quality metrics (`supplementary 5.xlsx`)
- **Script:** `07_silhouette_analysis.R` or `RatTestis_Silhouette_C1_C5_Analysis.ipynb` (Python)

#### Step 8: Visualization and Reporting
- **Output:** PCA plots, heatmaps, enrichment networks
- **Script:** `08_visualization.R`
- **Final output:** `pca_report_2025-12-26_10-01-21.html`

## Troubleshooting

### Memory Issues with Large Matrices
**Problem:** R crashes or runs out of memory during clustering or heatmap generation.

**Solutions:**
- Increase memory limit: `memory.limit(size = 16000)` (Windows) or adjust system RAM allocation
- Use sparse matrices: `library(Matrix); sparse_counts <- Matrix(counts, sparse = TRUE)`
- Process in batches: subset genes by variance threshold before clustering
- For machines with <16GB RAM, consider filtering to top 5000 most variable genes

### Path and File Location Issues
**Problem:** "File not found" errors when loading data.

**Solutions:**
- Use absolute paths: `setwd("/full/path/to/pca-analysis-data")`
- Or use relative paths with R projects: Create `.Rproj` file in repository root
- Check file names match exactly (case-sensitive on Linux/Mac)
- Use `list.files()` to verify file presence

### Package Version Conflicts
**Problem:** Functions behave differently or produce errors.

**Solutions:**
- Use exact versions listed above: `BiocManager::install("edgeR", version = "3.42.4")`
- Check installed versions: `packageVersion("edgeR")`
- Create conda/renv environment for reproducibility:
```r
# Using renv for reproducibility
install.packages("renv")
renv::init()
renv::snapshot()
```

### gProfiler2 Connection Errors
**Problem:** "Cannot connect to g:Profiler server" errors.

**Solutions:**
- Check internet connection
- Use archived version: `gost(..., domain_scope = "custom", custom_bg = background_genes)`
- Alternative: Use local clusterProfiler instead

### Large Heatmap Rendering
**Problem:** Heatmaps are too large or render slowly.

**Solutions:**
- Reduce gene count: `top_genes <- head(ranked_genes, 1000)`
- Save as PDF instead of PNG: `pdf("heatmap.pdf", width=12, height=16)`
- Use `ComplexHeatmap` for better performance on large matrices

### Missing miRNA Annotations
**Problem:** miRNA target validation fails or returns empty results.

**Solutions:**
- Ensure miRNA names use standard format: `rno-miR-500-5p` (lowercase "miR")
- Update miRBase database: `multiMiR::update.db()`
- Check species prefix: rat = `rno-`, mouse = `mmu-`, human = `hsa-`

## Repository Contents

### Main Analysis Files

- **pca_report_2025-12-26_10-01-21.html** - Interactive HTML report containing PCA analysis results with visualizations and statistical summaries. Open this file in a web browser to explore the analysis.
- **rat mouse human.RData** - R data file containing the processed dataset for rat, mouse, and human comparison analysis.
- **RatTestis_Silhouette_C1_C5_Analysis.ipynb** - Python Jupyter notebook for silhouette analysis

### Data Tables

- **iDEP counts.xlsx** - Gene expression count matrix from iDEP analysis platform
- **Supplementary_Table_S1.csv** - QC metrics for RNA-seq samples (read counts, detected genes, distribution statistics)
- **Supplementary_Table_S2_symbols.csv** - Stage-specific gene symbols table (Region, Gene_Symbol)
- **supplementary 3.xlsx** - Cluster membership assignments (Gene_ID, Group)
- **supplementary 4.csv** - Functional enrichment results with pathway annotations
- **supplementary 5.xlsx** - Silhouette analysis summary for clusters C1-C5

### Figures and Visualizations

- **Graphical abstract.pdf** - Visual summary of the research findings
- **scatter_plot.png** - Scatter plot visualization from PCA analysis
- **scree.png** - Scree plot showing variance explained by principal components
- **Supplementary Figure S6.png** - Additional supplementary figure
- **S4Functional enrichment plots for STEM profiles.png** - Functional enrichment analysis visualization
- **Clusters_profiles_downregulation.tiff** - High-resolution cluster profile visualization for downregulated genes

## How to Use This Repository

### For Reviewers and Researchers

1. **Start with the PCA Report**: Download and open `pca_report_2025-12-26_10-01-21.html` in your web browser for an interactive overview of the analysis.
2. **Explore the Data**: Use the CSV and Excel files to examine the raw data and statistical results. These files can be opened in Excel, R, Python, or any spreadsheet software.
3. **View Visualizations**: All PNG and PDF figures can be viewed directly in the browser or downloaded for closer examination.

### For Reproducibility

- The R data file (`rat mouse human.RData`) can be loaded in R using:
```r
load("rat mouse human.RData")
```
- CSV files can be read in R, Python, or Excel for further analysis
- All supplementary tables are provided in both CSV and Excel formats for compatibility
- Follow the Step-by-Step Analysis Workflow above to reproduce analyses from raw data

## File Formats

- **HTML**: Interactive reports (open in web browser)
- **CSV**: Comma-separated values (open in Excel, R, Python)
- **XLSX**: Excel spreadsheets
- **PNG/TIFF**: Image files
- **PDF**: Portable document format
- **RData**: R workspace data
- **ipynb**: Jupyter notebook (Python)

## System Requirements

- **RAM:** Minimum 8GB, recommended 16GB for full analysis pipeline
- **Storage:** ~2GB for repository + intermediate files
- **OS:** Windows 10/11, macOS 11+, or Linux (Ubuntu 20.04+)

## Citation

If you use these data or analysis results, please cite the associated research paper:

> Chew CC, Jamalpour S, Samudi C, Omar H, Bhassu S. Multi-Stage Transcriptome Analysis Reveals Genetic Orchestration of Rat Testis Development. [Journal details pending]

## Contact

For questions about the data or analysis, please open an issue in this repository or contact:
- Sajad Jamalpour (sajadjamalpour-del)
- Subha Bhassu (subhabhassu@um.edu.my)

## License

Please refer to the associated research paper for data usage terms. All processed files and code are released under a permissive license and are provided for reproducibility and further re-use, subject to the terms and conditions of the original dataset providers.
