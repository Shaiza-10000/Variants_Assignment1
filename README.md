# Variants_Assignment1
Pathogenic variant identification and annotation for 6 genetic/rare disorders | ClinVar • OMIM • UCSC • ACMG/AMP | GRCh38/hg38

# Overview
This repository contains a complete bioinformatics workflow for identifying, annotating, and analyzing pathogenic variants across 6 genetic and rare disorders. All variants were sourced from ClinVar, cross-referenced with OMIM, visualized in UCSC Genome Browser (AlphaMissense & CADD tracks), classified under ACMG/AMP guidelines, and compiled into a standard VCF file simulating real patient WGS data.

# Selected Disorders & Variants
➡️ Rare Genetic Disorders
#DiseaseGeneVariantClinVar IDACMG1Cystic FibrosisCFTRc.1521_1523del (p.Phe508del)VCV000007105Pathogenic2Huntington's DiseaseHTTCAG repeat expansion ≥40RCV000030659Pathogenic3Phenylketonuria (PKU)PAHc.1222C>T (p.Arg408Trp)VCV000000680Pathogenic4Marfan SyndromeFBN1c.5788G>A (p.Glu1930Lys)VCV000013400Pathogenic
➡️ Common Genetic Disorders
#DiseaseGeneVariantClinVar IDACMG5Sickle Cell AnemiaHBBc.20A>T (p.Glu7Val)VCV000015333Pathogenic6Hereditary Breast & Ovarian CancerBRCA1c.5266dupC (p.Gln1756ProfsTer74)VCV000055411Pathogenic

📂 Repository Structure
.
├── README.md                              # This file
├── patient_variants.vcf                   # Simulated patient WGS VCF (6 variants, VCFv4.2)
├── genetic_variants_assignment.xlsx       # Complete analysis spreadsheet
├── clinvar_annotated_output.txt           # ClinVar annotation results
└── screenshots/
    ├── AlphaMissense_CADD_CFTR.png        # UCSC — CFTR locus
    ├── AlphaMissense_CADD_HBB.png         # UCSC — HBB locus
    ├── AlphaMissense_CADD_HTT.png         # UCSC — HTT locus
    ├── AlphaMissense_CADD_PAH.png         # UCSC — PAH locus
    ├── AlphaMissense_CADD_FBN1.png        # UCSC — FBN1 locus
    └── AlphaMissense_CADD_BRCA1.png       # UCSC — BRCA1 locus

# Step-by-Step Workflow

# Step 1 — ClinVar Variant Selection

1. Go to https://www.ncbi.nlm.nih.gov/clinvar/
2. Search each disease name
3. Filter: Clinical Significance = Pathogenic, Review Status = Expert panel / Practice guideline
4. Select highest-reviewed, most clinically relevant variant

Direct ClinVar Links:
DiseaseLinkCystic Fibrosishttps://www.ncbi.nlm.nih.gov/clinvar/variation/7105/
Huntington's Diseasehttps://www.ncbi.nlm.nih.gov/clinvar/RCV000030659/
PKUhttps://www.ncbi.nlm.nih.gov/clinvar/variation/680/
Marfan Syndromehttps://www.ncbi.nlm.nih.gov/clinvar/variation/13400/
Sickle Cell Anemiahttps://www.ncbi.nlm.nih.gov/clinvar/variation/15333/
HBOC (BRCA1)https://www.ncbi.nlm.nih.gov/clinvar/variation/55411/

# Step-2: OMIM Phenotype Lookup

1. Go to https://www.omim.org/
2. Search each gene name

Disease                                Gene           OMIM
Cystic Fibrosis                        CFTR           #219700
Huntington's Disease                   HTT            #143100
Phenylketonuria                        PAH            #261600
Marfan Syndrome                        FBN1           #154700
Sickle Cell Anemia                     HBB            #603903         
Hereditary Breast & Ovarian Cancer     BRCA1          #604370

# Step-3: UCSC Genome Browser (AlphaMissense & CADD)

1. Go to https://genome.ucsc.edu/
2. Select Human GRCh38/hg38
3. Enter genomic coordinates and enable AlphaMissense + CADD 1.7 tracks

Disease                  Coordinates(hg38)
Cystic Fibrosis          chr7:117,559,500-117,559,650
Huntington's Disease     chr4:3,074,800-3,076,800
Phenylketonuria          chr12:102,836,900-102,837,100
Marfan Syndrome          chr15:48,645,600-48,645,800
Sickle Cell Anemia       chr11:5,226,900-5,227,100
BRCA1 HBOC               chr17:43,071,000-43,071,200

# Step-4: ACMG/AMP Classification

Variant                       Key ACMG Criteria                            Classification
CFTR p.Phe508del              PVS1, PS3, PS4, PM4, PP5                     Pathogenic
HTT CAG expansion             PVS1, PS4, PS3, PP4                          Pathogenic
PAH p.Arg408Trp               PVS1, PS3, PS4, PM1, PM2, PP3, PP5           Pathogenic
FBN1 p.Glu1930Lys             PS3, PS4, PM1, PM2, PP3, PP5                 Pathogenic
HBB p.Glu7Val                 PS3, PS4, PM1, PM5, PP3, PP5                 Pathogenic
BRCA1 c.5266dupC              PVS1, PS3, PS4, PM2, PP5                     Pathogenic
# Reference: Richards et al. 2015, ACMG/AMP Guidelines (PMID: 25741868)

# Step-5: VCF File Creation

The patient_variants.vcf follows:

1. Full ##fileformat, ##reference, ##contig, ##INFO, ##FORMAT headers
2. Proper column structure: #CHROM POS ID REF ALT QUAL FILTER INFO FORMAT SAMPLE
3. Realistic genotype data (GT, AD, DP, GQ) simulating WGS output
4. One entry per variant with appropriate zygosity (homozygous for recessive, heterozygous for dominant)

To validate on HPC:
# Check file
head -25 patient_variants.vcf

# Count variants
grep -v "^#" patient_variants.vcf | wc -l

# Validate with bcftools
module load bcftools
bcftools stats patient_variants.vcf

# Step=6: ClinVar Annotation

# Annotate VCF using ClinVar database via bcftools
module load bcftools

bcftools annotate \
    -a clinvar.vcf.gz \
    -c INFO \
    patient_variants.vcf \
    -o clinvar_annotated_output.vcf

# View results
cat clinvar_annotated_output.vcf

