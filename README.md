# pca-analysis-data

## Overview

This repository contains principal component analysis (PCA) and supplementary data for rat testis research. The files include interactive analysis reports, statistical data, visualization plots, and supplementary materials.

## Repository Contents

### Main Analysis Files

- **pca_report_2025-12-26_10-01-21.html** - Interactive HTML report containing PCA analysis results with visualizations and statistical summaries. Open this file in a web browser to explore the analysis.

- **rat mouse human.RData** - R data file containing the processed dataset for rat, mouse, and human comparison analysis.

### Data Tables

- **iDEP counts.xlsx** - Gene expression count matrix from iDEP analysis platform
- **Supplementary_Table_S1.csv** - Primary supplementary data table with statistical results
- **Supplementary_Table_S2_symbols.csv** - Gene symbols and annotations table
- **supplementary 3.xlsx** - Additional supplementary data (Excel format)
- **supplementary 4.csv** - Additional supplementary data (CSV format)
- **supplementary 5.xlsx** - Additional supplementary data (Excel format)

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

## File Formats

- **HTML**: Interactive reports (open in web browser)
- **CSV**: Comma-separated values (open in Excel, R, Python)
- **XLSX**: Excel spreadsheets
- **PNG/TIFF**: Image files
- **PDF**: Portable document format
- **RData**: R workspace data

## Citation

If you use these data or analysis results, please cite the associated research paper.

## Contact

For questions about the data or analysis, please open an issue in this repository.

## License

Please refer to the associated research paper for data usage terms.
