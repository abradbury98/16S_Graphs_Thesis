# 🧬 16S rRNA Metabarcode Sequence Visualisations

> **Thesis Chapter 3 — Method Development**
> Publication-quality visualisations for 16S rRNA amplicon sequencing data, produced using R and phyloseq.

---

## 📖 Overview

This repository contains the R Markdown script (`16S_graphs_all-2.Rmd`) developed as part of a PhD thesis for **Chapter 3: Method Development**. The script generates a comprehensive suite of publication-quality figures for **16S rRNA metabarcode sequencing data**, focusing on:

- Comparing microbial community composition **before and after host-DNA depletion**
- Assessing the effect of **filtration methods** on community profiles
- Visualising **temporal dynamics** of bacterial enrichment across four broth types
- Comparing **DNA extraction kits**
- Tracking **read counts** through the DADA2 bioinformatics pipeline

All figures are exported as high-resolution PNG files (300 DPI) suitable for publication.

---

## 📁 Repository Structure

```
📦 your-repo/
 ┣ 📄 16S_graphs_all-2.Rmd       ← Main analysis script
 ┣ 📁 final_plots/               ← Output directory (auto-created on run)
 ┃  ┣ 🖼️  01_host_dna_depletion_family.png
 ┃  ┣ 🖼️  02_host_dna_depletion_genus.png
 ┃  ┣ 🖼️  03_filtration_testing_family.png
 ┃  ┣ 🖼️  04_filtration_testing_genus.png
 ┃  ┣ 🖼️  05_enriched_depletion_family.png
 ┃  ┣ 🖼️  06_enriched_depletion_genus.png
 ┃  ┣ 🖼️  07_enrichment_temporal_family.png
 ┃  ┣ 🖼️  08_enrichment_temporal_genus.png
 ┃  ┣ 🖼️  09_extraction_kit_family.png
 ┃  ┣ 🖼️  10_all_samples_heatmap_family.png
 ┃  ┣ 🖼️  11_all_samples_heatmap_genus.png
 ┃  ┣ 🖼️  12_host_depletion_heatmap_family.png
 ┃  ┣ 🖼️  13_read_tracking.png
 ┃  ┣ 🖼️  14_read_distribution.png
 ┃  ┣ 📊  read_count_summary.csv
 ┃  ┣ 💾  ps_with_sample_data.rds
 ┃  ┣ 💾  ps_family_rel.rds
 ┃  ┗ 💾  ps_genus_rel.rds
 ┗ 📄 README.md
```

---

## ⚙️ Prerequisites

### Software

| Tool | Version | Purpose |
|------|---------|---------|
| [R](https://www.r-project.org/) | ≥ 4.2.0 | Core language |
| [RStudio](https://posit.co/download/rstudio-desktop/) | Any recent | Recommended IDE |

### R Packages

Install all required packages by running the following in R:

```r
# Bioconductor packages
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install(c("phyloseq", "dada2"))

# CRAN packages
install.packages(c(
  "dplyr", "tidyr", "stringr", "readxl",
  "ggplot2", "RColorBrewer", "scales",
  "patchwork", "pheatmap", "cowplot", "rlang"
))
```

---

## 🗂️ Required Input Data

Before running the script, you will need **three pre-existing R objects** in your environment. These are produced by a DADA2 amplicon sequencing pipeline and are not included in this repository.

| Object | Type | Description |
|--------|------|-------------|
| `ps` | `phyloseq` object | Processed phyloseq object with OTU table and taxonomy |
| `track` | `matrix` / `data.frame` | DADA2 read tracking table (columns: `input`, `filtered`, `merged`, `nonchim`) |
| Sample metadata Excel file | `.xlsx` | Spreadsheet with sample descriptions and method categories |

### Sample Metadata File

The script expects an Excel file with (at minimum) the following columns:

| Column Name | Description |
|-------------|-------------|
| `Sample code` | Must match sample names in the phyloseq object |
| `Full sample name` | Descriptive name displayed on plots |
| `Method development section` | Category label (see below) |
| `Concentration (ng/ul)` | DNA concentration |

#### Expected Method Categories

```
"Enrichment Testing"
"Host-DNA depletion"
"Filtration testing for Host-DNA depletion"
"Host-DNA depletion - enriched samples"
"DNA extraction kit testing"
"Control"
```

### Updating the File Path

The metadata file path is hardcoded in the `setup-sample-data` chunk. **Update this to match your file location** before running:

```r
# In chunk: setup-sample-data
sample_metadata <- read_excel(
  "/path/to/your/sample_descriptions.xlsx"   # ← CHANGE THIS
)
```

---

## 🚀 How to Run

### Option 1 — Knit in RStudio (Recommended)

1. Open `16S_graphs_all-2.Rmd` in RStudio
2. Load your `ps` object and `track` table into the R environment
3. Update the metadata file path (see above)
4. Click **Knit** → **Knit to HTML**
5. Outputs will be saved to `final_plots/`

### Option 2 — Run from the R Console

```r
# Load your data first
# load("my_dada2_results.RData")   # or however you load ps and track

rmarkdown::render("16S_graphs_all-2.Rmd")
```

---

## 📊 Visualisations Produced

### 1. Stacked Bar Charts with Read Count Heatmaps

The core plot type used throughout. Each figure shows:
- **Top panel:** Stacked bar chart of relative abundance (%) for the top 12 taxa
- **Bottom strip:** Colour-coded heatmap of non-chimeric read counts per sample

These plots are generated at both **Family** and **Genus** level.

---

### 2. Host-DNA Depletion Comparisons (`create_faceted_plot`)

Faceted into two panels — *With Host-Depletion* vs *No Host-Depletion* — for direct before/after comparison.

**Figures produced:**
- `01_host_dna_depletion_family.png`
- `02_host_dna_depletion_genus.png`
- `05_enriched_depletion_family.png`
- `06_enriched_depletion_genus.png`

---

### 3. Filtration Testing (`create_faceted_plot`)

Same two-panel layout applied to samples from the filtration testing method category.

**Figures produced:**
- `03_filtration_testing_family.png`
- `04_filtration_testing_genus.png`

---

### 4. Enrichment Temporal Dynamics (`create_enrichment_plot_4broths`)

Four-panel layout — one panel per broth type (RVS, BPW, BF Bolton, BF Bolton + CAT) — with time points on the x-axis. Shows how community composition shifts over incubation time.

**Figures produced:**
- `07_enrichment_temporal_family.png`
- `08_enrichment_temporal_genus.png`

---

### 5. DNA Extraction Kit Comparison (`create_simple_plot`)

Single-panel bar chart comparing communities across extraction kits.

**Figures produced:**
- `09_extraction_kit_family.png`

---

### 6. Diversity Heatmaps (`create_heatmap`)

Pheatmap-based heatmaps showing relative abundance (%) across all samples, annotated by sample category and read count.

**Figures produced:**
- `10_all_samples_heatmap_family.png`
- `11_all_samples_heatmap_genus.png`
- `12_host_depletion_heatmap_family.png`

---

### 7. DADA2 Read Tracking (`read-tracking`, `read-distribution`)

Line plots and boxplots summarising read retention at each step of the DADA2 pipeline (input → filtered → merged → non-chimeric), grouped by sample category.

**Figures produced:**
- `13_read_tracking.png`
- `14_read_distribution.png`

---

## 🎨 Colour System

The script uses a consistent, reproducible colour scheme across all plots:

| Element | Palette | Notes |
|---------|---------|-------|
| Taxa | ColorBrewer `Set3` (12 colours) | Assigned deterministically by taxon name |
| *Campylobacter* / *Campylobacteraceae* | `#66C2A5` (teal-green) | Fixed colour — always consistent |
| "Other" | `#C0C0C0` (light grey) | Grouped low-abundance taxa |
| "Unclassified" | `#808080` (mid grey) | Taxa with no classification |
| Read count heatmap | Yellow → Orange → Red gradient | Warm sequential palette |
| Sample categories | Fixed named colour vector | Consistent across all figures |

---

## 🔧 Customisation

### Change the Number of Taxa Displayed

Adjust `top_n` in any plotting function call:

```r
create_faceted_plot(ps_family_rel, top_n = 15, ...)   # Show top 15 instead of 12
```

### Switch Between Family and Genus Level

```r
create_faceted_plot(ps_genus_rel, taxon_rank = "Genus", ...)
```

### Change Output Figure Size

```r
create_faceted_plot(..., width = 20, height = 14)   # inches, at 300 DPI
```

### Change Output File Path

```r
create_faceted_plot(..., output_file = "my_folder/my_figure.png")
```

---

## 🐛 Troubleshooting

**`Error: object 'ps' not found`**
→ Make sure your phyloseq object is loaded into the R environment before knitting.

**`Missing required metadata columns`**
→ Check that your Excel file contains the expected column names exactly (case-sensitive). See the [Required Input Data](#-required-input-data) section.

**Bars don't sum to 100%**
→ Run the `diagnostic-check-composition` chunk to verify sample sums. The `normalize_composition_by_sample()` function should correct this automatically.

**`No samples found after filtering`**
→ Check that the `Method_Category` values in your metadata match the expected category strings exactly.

**`Undetermined` sample causing issues**
→ This is handled automatically — the script removes any sample named `"Undetermined"` during the metadata fix step.

---

## 📋 Script Structure at a Glance

```
16S_graphs_all-2.Rmd
│
├── Setup & Libraries
├── Define Colour Palettes
├── Load & Merge Sample Metadata
├── Fix Metadata Issues
├── Add Enrichment Metadata (Broth Type, Incubation Time)
├── Taxonomic Aggregation (Family & Genus level)
├── Define Plotting Functions
│   ├── normalize_composition_by_sample()
│   ├── create_triple_faceted_plot()   ← All samples + depletion panels
│   ├── create_faceted_plot()          ← Before/after depletion comparison
│   ├── create_simple_plot()           ← Single-panel overview
│   ├── create_enrichment_plot_4broths() ← Temporal dynamics by broth
│   └── create_heatmap()               ← Pheatmap diversity heatmap
├── Diagnostic Check (optional)
├── Generate All Visualisations
├── Read Tracking & Distribution Plots
├── Summary Statistics Table
└── Save R Objects (RDS files)
```

---

## 📝 Citation

If you use or adapt this code, please acknowledge this thesis chapter in your work.

---

## 👩‍🔬 Author

Developed as part of a PhD thesis.
**Chapter 3 — Method Development: 16S rRNA Metabarcode Sequencing**

---

*Last updated: 01-04-2026*
