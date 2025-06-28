# PYT Analysis Pipeline Steps
## A Genomic Selection Pipeline for the analysis of Preliminary Yield Trials conducted in 2024

This repository contains the code for the complete pipeline for processing phenotypic and genotypic data for the PYT24 trials, integrating QC, modeling, GWAS, and genomic prediction steps. 

---

## Pheno Data Processing (All Steps in R with ASReml-R)

1. **Load PYT24 trials data** and extract relevant data.
2. **QC:** Remove outliers and explore data integrity.
3. **Location-wise BLUEs** - (Single Site Estimation of BLUEs)
4. **Estimate reliabilities.**
    - Accuracies of prediction = sqrt(reliabilities) (heritabilities)
---

## Geno Data Processing

5a. Prepare Agriplex PYT genotype file.

    - Convert genotype table in Excel to VCF format using R.

5b. Filter the VCF:

    - Remove markers with excessive missing data.
    - Apply minor allele frequency (MAF) filters.
    - Remove taxa with too much missing data.

6. Perform **liftover** of Agriplex Soy 1K genotypic data from glyma.wm82.a1.v1 to glyma.wm82.a4.v1.

    - This can be done in R using:
        - a coordinate map file
        - REF/ALT information
        - or tools like `bcftools +fixref`

7. Extract CxB genotypes from Gencove 50K genotyping file in glyma.wm82.a4.v1. :

    ```bash
    bcftools view -S samples.txt input.vcf.gz -Oz -o output.vcf.gz
    ```

8. **Combine** CxB 50K and Agriplex PYT 1K data:

    ```bash
    bcftools merge ...
    ```

9. **Impute missing data** and apply MAF-based filtering:

    - Options:
        - Population-based imputation
        - Pedigree-based imputation
    - Tools: R or GS4PB

---

## Diagnostic Analyses

10. Perform **PCA** of 50K imputed and 1K data.

    - Tools: R or GS4PB

11. **GWAS** with 50K imputed and 1K data:

    - Two-step process:
        - Format genotype table for PLINK
        - Run association using PLINK
        - Visualize results with Manhattan plots using `qqman` or GAPIT in R

---

## Prepare Training Set (R)

- Create a **master set** combining:
    - PYT data from 2023
    - PYT data from 2024
    - AYT data from 2024

12. Merge **genotypic and phenotypic data** from these various datasets.

---

## Cross Validation (R)

13. Leave-One-Test-Out CV using:
    - MM (Main Effects Model with no interaction) 
    - MDs (Main effects with homogeneous variance for GxE interactions)
    - MDe (Main effects with heterogeneous environment specific variance for GxE interactions)

14. Leave-One-Line-Out CV (Optional).

15. 2-Fold CV.

---

## Genomic Prediction (R)

16. Run **2-Fold training** to generate predictions.

---

## Prepare Selection Table (R)

17. Estimate **yield adjusted for maturity** in R-ASReml-R.

    - Optionally compare results from `asreml` and `lm`.

18. Sort and rank entries **within zones.**

19. Prepare the final **selection table** listing:
    - Top 20 lines
    - Bottom 20 lines

---
