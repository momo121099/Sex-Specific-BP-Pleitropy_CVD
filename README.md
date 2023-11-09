# Sex-Specific BP Pleiotropy of CVD (<ins>SSBP<ins>)
<img src="https://github.com/momo121099/BP-sex-specific-pleiotropy/blob/main/Picture1.png" width=60% height=60%>

## Introduction:
The primary objective of this pipeline script is to identify regions associated with blood pressure (BP) that exhibit sex-specific pleiotropy concerning a cardiovascular disease (CVD) trait, leveraging the GWAS data provided by the user. We utilize sex-stratified genetic associations for systolic blood pressure (SBP), diastolic blood pressure (DBP), or pulse pressure (PP) from the UK Biobank (UKB) dataset, ensuring equal sample sizes for different sexes (ML Yang, et al.). This analysis focuses specifically on the top BP loci regions identified in the BP GWAS. By comparing the posterior probabilities of colocalization, we can screen and identify possible candidate BP regions with sex-specific and sex-biased pleiotropy in relation to the CVD trait of interest. It's important to note that the genomic locations used for this comparison should be based on GRCh37/hg19.

## Prerequisites:
1. R packages and functions:
   
Install these packages below (coloc, data.table, reshape) in R program and then source our implementation function first.

Refer this page for more detailed information: https://github.com/chr1swallace/coloc
```
if(!require("remotes"))
   install.packages("remotes") # if necessary
library(remotes)
install_github("chr1swallace/coloc@main",build_vignettes=TRUE)
install.packages('data.table')
install.packages('reshape')

git clone https://github.com/momo121099/BP-sex-specific-pleiotropy.git
source('Sex_Specific_BP_Pleiotropy_CVD_function.R')
```
2. Download the BP sex-stratified GWAS files required for this analysis
   
FTP site link:(pending)

## Usage:
bp.sex.colocalization(cvd.data,bp.trait='PP',cvd.trait='CVD',...)

## Input Arguments:
**cvd.data**: This parameter represents the user's provided GWAS data for the cardiovascular disease (CVD) trait of interest. The format for this input file is described below.

An example file is included in the directory, sourced from the FMD GWAS (A. Georges, M.L. Yang, T.E. Berrandou, et al., Nature Communications, 2021). Please ensure that the header of your file adheres to the following specifications, and be mindful that the genome assembly version should be GRCh37/hg19, and the SNPID format should follow this pattern: CHR:BP.
   
   >    CHR     BP    BETA     SE   pval    SNPID
   > 
   > 1 845635 -0.0218 0.0652 0.7382 1:845635
   > 
   > 1 845938 -0.0295 0.0643 0.6463 1:845938
   > 
   > 1 846078 -0.0066 0.0651 0.9197 1:846078


**bp.trait**: Specify the sex-stratified GWAS data for blood pressure (BP) traits sourced from the UK Biobank (UKB), as detailed by ML Yang et al. You have the option to choose from 'SBP' (systolic blood pressure), 'DBP' (diastolic blood pressure), or 'PP' (pulse pressure).

**cvd.trait**: Input the name of the user's cardiovascular disease (CVD) trait of interest. This trait name will be incorporated into the output file name for reference.

**size**: Indicate the sample size used for the GWAS analysis of the provided CVD trait.

**Type**: Specify the type of data in the CVD trait, which can be either "q" for quantitative data or "b" for case-control data.

**p**: For case-control CVD datasets, define the proportion of samples in the CVD data that are cases.

**poster.p**: This parameter determines the colocalization posterior probability used for comparison. You can choose between "H4" or "max.H3.H4." It is advisable to use "pp.H4" when the CVD GWAS data is of European ancestry and shares similar variant coverage with the UKB data used in the BP study. If the CVD trait originates from trans-ethnic data or has substantially different coverage compared to our BP study data, utilizing the maximum values of pp.H3 and pp.H4 can enhance the ability to detect sex-specific pleiotropic regions.

**gene.pull.method**: Specify the method for aggregating colocalization posterior probability information from regions sharing the same nearest gene. You can opt for either 'mean' or 'max' to calculate either the average or maximum values, respectively.

**wd**: Define the window size in base pair positions around the top index SNP for each BP region during colocalization. The default is +/- 250Kb of the index SNP. It's important to note that the colocalization tool used (coloc.abf) assumes the presence of only one causal variant within a given region, so it's advisable not to use an excessively large window size.

**diff**: Set the threshold for the absolute difference in posterior probabilities between male and female BP colocalization with CVD. This threshold is employed to identify genes that exhibit sex-biased colocalization.

**cutoff**: Establish a threshold for the posterior probability to identify genes displaying sex-specific colocalization. Genes meeting this criterion will have a posterior probability greater than this cutoff in only one sex. The combination of the diff and cutoff values will be used together to screen for the top BP genes displaying sex-specific pleiotropic effects with CVD.

## Output:
Two files will be generated in the same directory folder:

**"bp.trait_cvd.trait_GWAS_colocalization_topSEXGene.csv"**:

   This file contains data on genes that meet the specified threshold in the function to identify BP-associated genetic regions exhibiting sex-specific and sex-biased colocalization or pleiotropic effects with the user's CVD trait.

**"bp.trait_cvd.trait_GWAS_colocalization.csv"**:

   This file provides the posterior probability output for cross-trait GWAS colocalization between blood pressure (BP) and the user's CVD trait across all BP regions.

**Output file format**: The posterior probability of cross-trait GWAS colocalization between female blood pressure (BP) and the user's cardiovascular disease (CVD) trait (PP.Female), as well as between male BP and the user's CVD trait (PP.Male), is computed for each genetic region. These regions are labeled based on the nearest gene associated with each index SNP. CHR:ST-ED is the genetic region (bp position) for colocalziation.
  
> Region   CHR        ST        ED  PP.Female     PP.Male
> 
> CDK5RAP3   17  45794308  46294308 0.52032436 0.016675401
> 
> CDKN2B-AS   9  21874504  22374504 0.94674890 0.115265842
> 
> COL4A1     13 110546007 111046007 0.88984010 0.001853196
> 
> COL4A2     13 110790681 111290681 0.91783466 0.002051632
> 
> MAP9        4 156141307 156656653 0.97318459 0.167642370
>
> 
## Example Usage:
*Please use files in the data folder*

Download folder from FTP site link:(pending)

*Source the function file first*
```
source('Sex_Specific_BP_Pleiotropy_CVD_function.R')
```
*Read the example input CVD GWAS file located in the same folder*
```
cvd.data=fread('example_data_fmd.txt',header=T)
```
*Execute the sex-stratified colocalization analysis for specified BP trait using the user-provided cardiovascular disease (CVD) GWAS data. This analysis systematically screens for potential candidate BP-associated regions exhibiting sex-specific pleiotropic effects related to the user's CVD of interest*
```
bp.sex.colocalization(cvd.data,bp.trait='PP',cvd.trait='FMD',size=8656,p=0.3,Type='b',poster.p='H4',gene.pull.method='max',wd=250000, diff=0.5, cutoff=0.5)
```
*Generate plots for the specified candidate region displaying sex-specific pleiotropy, as described in the section below*
```
bp.sex.region.locus.plot(outname='13q34',cvd.data,bp.trait='PP',cvd.trait='FMD',pos.chr=13,pos.st=110546007,pos.ed=111046007)
```

## Visualization of regional plot
**Use the following function**:
```
bp.sex.region.locus.plot(outname,cvd.data,bp.trait,cvd.trait,pos.chr,pos.st,pos.ed)
```
**Arguments**:

*outname: output file name prefix

*cvd.data: This parameter represents the user's GWAS data for the cardiovascular disease (CVD) trait of interest. The format for this input file is described above.

*bp.trait: Specify the sex-stratified GWAS data for blood pressure (BP) traits sourced from the UK Biobank (UKB), as detailed by ML Yang et al. You have the option to choose from 'SBP' (systolic blood pressure), 'DBP' (diastolic blood pressure), or 'PP' (pulse pressure).

*cvd.trait: Input the name of the user's cardiovascular disease (CVD) trait of interest. This trait name will be incorporated into the output file name for reference.

*post.chr: Chromosome for plotting the regional analysis.

*post.st: Start base pair position (hg19) for plotting the regional analysis.

*post.ed: End base pair position (hg19) for plotting the regional analysis.

**Output**:
Generate and save figures representing genetic associations for female-only BP, male-only BP, and CVD within a specified region. Save these figures in 'png' format.

## R Shiny app tool
**Another tool available is the R Shiny app, which enables interactive viewing of regional plots for sex-stratified BP genetic association results.**

*Source the shiny app function file to use*
```
source('shiny_sex_BP_select_region.R')
```
## References:
Please cite this paper if you utilize this pipeline script:

Yang, M.-L., Xu, C., Gupte, T., Hoffmann, T. J., Iribarren, C., Zhou, X., & Ganesh, S. K. (2023). Leveraging sex differences of the complex genetic architecture of blood pressure to define sex-biased arterial genome regulation and cardiovascular disease risks.

## About our Lab:
PI: Dr. Santhi Ganesh 

University of Michigan Ann Arbor, Cardiovascular Medicine, U.S.A.

https://www.ganeshlab.org/


