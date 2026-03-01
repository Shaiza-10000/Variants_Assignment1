# Assignment 1: Pathogenic Variant Identification and Annotation
Tools: ClinVar · OMIM · UCSC Genome Browser · ACMG/AMP Guidelines
Reference Genome: GRCh38/hg38
Variants Analyzed: 6 pathogenic variants across rare and common genetic disorders

# Overview
This repository documents a complete bioinformatics workflow for identifying, annotating, and classifying pathogenic variants across 6 genetic disorders. Each variant was:

1. Retrieved from ClinVar (expert panel / practice guideline reviewed)
2. Cross-referenced with OMIM for phenotype and inheritance data
3. Visualized in the UCSC Genome Browser using AlphaMissense and CADD 1.7 tracks
4. Classified under ACMG/AMP (2015) variant interpretation guidelines
5. Compiled into a standard VCF file (v4.2) simulating real patient WGS data

# Selected Disorders & Variants

|# Disease |                           Gene |           Variant |                           ClinVar ID |       ACMG Classification |
|------------|------|---------|------------|---------------------|
|1 Cystic Fibrosis     |                CFTR|            c.1521_1523del (p.Phe508del) |      VCV000007105 |     Pathogenic|
|2 Huntington's Disease|                HTT |            CAG repeat expansion ≥40     |      RCV000030659   |   Pathogenic|
|3 Phenylketonuria(PKU)|                PAH  |           c.1222C>T (p.Arg408Trp)      |      VCV000000680   |   Pathogenic|
|4 Marfan Syndrome     |                FBN1  |          c.5788G>A (p.Glu1930Lys)     |      VCV000013400    |  Pathogenic|
|5 Sickle Cell Anemia  |                HBB    |         c.20A>T (p.Glu7Val)          |      VCV000015333     | Pathogenic|
|6 Hereditary Breast & Ovarian Cancer|  BRCA1   |        c.5266dupC (p.Gln1756ProfsTer74)|   VCV000055411      |Pathogenic|

# Step-by-Step Workflow

# Step-1: ClinVar Variant Selection

URL: https://www.ncbi.nlm.nih.gov/clinvar/

For each disorder, variants were selected using the following filters:

     Clinical Significance: Pathogenic
    
     Review Status: Expert panel or Practice guideline

|Direct                       |ClinVar Links|
|----------|------------------|
|DiseaseLinkCystic Fibrosis|   https://www.ncbi.nlm.nih.gov/clinvar/variation/7105/|
|Huntington's Disease       |  https://www.ncbi.nlm.nih.gov/clinvar/RCV000030659/|
|PKU                         | https://www.ncbi.nlm.nih.gov/clinvar/variation/680/|
|Marfan Syndrome            |  https://www.ncbi.nlm.nih.gov/clinvar/variation/13400/|
|Sickle Cell Anemia          | https://www.ncbi.nlm.nih.gov/clinvar/variation/15333/|
|HBOC (BRCA1)                 |https://www.ncbi.nlm.nih.gov/clinvar/variation/55411/|

# Step-2: OMIM Phenotype Lookup

URL: https://www.omim.org/
Each gene was searched in OMIM to confirm disease association, inheritance mode, and phenotype entry.

|Disease                               | Gene|           OMIM|
|------------------------------|-----------|---------|
|Cystic Fibrosis                      |  CFTR |          #219700|
|Huntington's Disease                 |  HTT   |         #143100|
|Phenylketonuria                      | PAH  |          #261600|
|Marfan Syndrome                     |   FBN1  |         #154700|
|Sickle Cell Anemia                 |    HBB   |         #603903 |        
|Hereditary Breast & Ovarian Cancer|     BRCA1 |         #604370|

# Step-3: UCSC Genome Browser (AlphaMissense & CADD)

URL: https://genome.ucsc.edu/
Assembly: Human GRCh38/hg38
Tracks enabled: AlphaMissense and CADD 1.7
Each locus was navigated to using the coordinates below. Screenshots are saved in ScreenShots/.

|Disease  |                Coordinates(hg38)|
|---------|-------------|
|Cystic Fibrosis      |    chr7:117,559,500-117,559,650|
|Huntington's Disease|     chr4:3,074,800-3,076,800|
|Phenylketonuria    |      chr12:102,836,900-102,837,100|
|Marfan Syndrome|          chr15:48,645,600-48,645,800|
|Sickle Cell Anemia|       chr11:5,226,900-5,227,100|
|BRCA1 HBOC|               chr17:43,071,000-43,071,200|

# Step-4: ACMG/AMP Classification
Variants were classified following Richards et al. 2015 (PMID: 25741868).

|Variant|                       Key ACMG Criteria|                            Classification|
|---------------|--------|----------------|
|CFTR p.Phe508del  |            PVS1, PS3, PS4, PM4, PP5            |         Pathogenic|
|HTT CAG expansion  |           PVS1, PS4, PS3, PP4                |          Pathogenic|
|PAH p.Arg408Trp     |          PVS1, PS3, PS4, PM1, PM2, PP3, PP5|           Pathogenic|
|FBN1 p.Glu1930Lys    |         PS3, PS4, PM1, PM2, PP3, PP5     |            Pathogenic|
|HBB p.Glu7Val         |        PS3, PS4, PM1, PM5, PP3, PP5    |             Pathogenic|
|BRCA1 c.5266dupC       |       PVS1, PS3, PS4, PM2, PP5       |              Pathogenic|

Reference: Richards et al. 2015, ACMG/AMP Guidelines (PMID: 25741868)

# Step-5: VCF File Creation
The patient_variants.vcf file (VCF v4.2) was constructed to simulate real patient WGS output with the following specifications:

    Complete ##fileformat, ##reference, ##contig, ##INFO, and ##FORMAT header lines 
    Standard column structure: #CHROM POS ID REF ALT QUAL FILTER INFO FORMAT SAMPLE 
    Realistic genotype fields: GT, AD, DP, GQ, PL 
    Zygosity reflects inheritance mode: 
           Homozygous autosomal recessive variants: (CFTR, PAH, HBB) 
           Heterozygous autosomal dominant variants: (HTT, FBN1, BRCA1) 

To validate on HPC:
 Check file
head -25 patient_variants.vcf

 Count variants
grep -v "^#" patient_variants.vcf | wc -l

 Validate with bcftools
module load bcftools
bcftools stats patient_variants.vcf

# Step=6: ClinVar Annotation

# Annotate VCF using ClinVar database via bcftools
The VCF was annotated against the ClinVar database on the HPC cluster using bcftools annotate:
Load required modules on HPC
module load bcftools
module load htslib

ClinVar VCF (GRCh38) was pre-downloaded to the reference directory
Source: https://ftp.ncbi.nlm.nih.gov/pub/clinvar/vcf_GRCh38/clinvar.vcf.gz

# Run annotation
bcftools annotate \

  -a /ref/clinvar/clinvar.vcf.gz \
  
  -c ID,INFO/CLNSIG,INFO/CLNDN,INFO/CLNREVSTAT,INFO/CLNHGVS \
  
  -h <(echo '##INFO=<ID=CLNSIG,Number=.,Type=String,Description="ClinVar clinical significance">') \
  
  patient_variants.vcf \
  
  -o Annotated_Variants.vcf

# Verify output
bcftools stats Annotated_Variants.vcf | grep "^SN"

grep -v "^#" Annotated_Variants.vcf | cut -f1-8

Annotated results are saved to Annotated_Variants.vcf. All 6 variants returned CLNSIG=Pathogenic consistent with ClinVar expert panel review status.

# References

ACMG/AMP Guidelines: Richards S, et al. Genet Med. 2015;17(5):405–24. PMID: 25741868

ClinVar: https://www.ncbi.nlm.nih.gov/clinvar/

OMIM: https://www.omim.org/
UCSC Genome Browser: https://genome.ucsc.edu/

