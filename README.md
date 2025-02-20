# Sex-Specific BP Pleiotropy of CVD 
<img src="https://github.com/momo121099/BP-sex-specific-pleiotropy/blob/main/Picture1.png" width=60% height=60%>

## A. Introduction:
The pipeline aims to identify genetic regions associated with blood pressure (BP) that has sex-specific pleiotropy with the user specified cardiovascular disease (CVD) trait. It utilizes both user-provided GWAS data and sex-stratified GWAS data of SBP, DBP, or PP trait from the UK Biobank (UKB) dataset with equal sample sizes for different sexes (ML Yang, et al.). The analysis involves assessing the top BP loci regions from these sex-stratified GWAS datasets to identify potential candidate BP regions displaying sex-specific and sex-biased pleiotropy in relation to the CVD trait. Genomic locations are determined based on the GRCh37/hg19 reference.

## B. Prerequisites:
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

git clone https://github.com/momo121099/Sex-Specific-BP-Pleitropy_CVD.git
source('Sex_Specific_BP_Pleiotropy_CVD_function.R')
```
2. Please download all the sex-stratified BP GWAS files listed below, as they are required for this analysis:

GWAS catalog FTP link:

UKB SBP combined-sex studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301695

UKB SBP female-only studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301696

UKB SBP male-only studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301697

UKB DBP combined-sex studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301698

UKB DBP female-only studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301699

UKB DBP male-only studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301700

UKB PP combined-sex studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301701

UKB PP female-only studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301702

UKB PP male-only studies-

https://www.ebi.ac.uk/gwas/studies/GCST90301703

Click on the links above, then select 'FTP Download' under the 'Study Information' section, followed by 'Full Summary Statistics'. This action will direct you to the FTP site for downloading the full summary statistics data. Once downloaded, store these files in the same directory as the pipeline script.

## C. File Preparation:
3. Download the example CVD GWAS file from a previous FMD GWAS result available in the GWAS catalog, which includes the complete summary statistics (A. Georges, M.L. Yang, T.E. Berrandou, et al., Nature Communications, 2021).

   Put it in the same folder or the designated directory.
```
wget https://ftp.ebi.ac.uk/pub/databases/gwas/summary_statistics/GCST90026001-GCST90027000/GCST90026612/GCST90026612_buildGRCh37.tsv
```
4. Read the example input CVD file and convert it to our compatible format.

For your own analysis, you should use your CVD data with complete summary statistics and format it according to the specified format provided below (please note that the header names should be exactly the same and case-sensitive)
```
cvd.data<-fread('GCST90026612_buildGRCh37.tsv',header=T)
cvd.data1=data.frame(cvd.data$chromosome,cvd.data$base_pair_location,cvd.data$BETA,cvd.data$SE,cvd.data$p_value)
colnames(cvd.data1)=c('CHR','BP','BETA','SE','pval')
cvd.data1$pos.hg19=paste(cvd.data1$CHR,':',cvd.data1$BP,sep='')
head(cvd.data1)
```
>    CHR     BP    BETA     SE   pval pos.hg19
> 
>    1 845635 -0.0218 0.0652 0.7382 1:845635
>
>    1 845938 -0.0295 0.0643 0.6463 1:845938
> 
>    1 846078 -0.0066 0.0651 0.9197 1:846078

## D. Cross-trait sex-specific colocalization:
5. Source the function file first
```
source('Sex_Specific_BP_Pleiotropy_CVD_function.R')
```
6. Cross-trait sex-specific colocalization: Execute the sex-stratified colocalization analysis for specified BP trait using the user-provided cardiovascular disease (CVD) GWAS data.

This analysis will systematically screens for potential candidate BP-associated regions exhibiting sex-specific pleiotropic effects related to the user's CVD of interest.
```
bp.sex.colocalization(cvd.data1,bp.trait='PP',cvd.trait='FMD',size=8656,p=0.3,Type='b',poster.p='H4',gene.pull.method='max',wd=250000, diff=0.5, cutoff=0.5)
```
## E. Follow-up visualization:
7. Generate plots for the candidate region specified, illustrating sex-specific pleiotropy as detailed in the section below.

You can individually examine the regional plots to further filter genes.

Read the output file from cross-trait sex-specific colocalization screening:
```
bp.trait='PP'
cvd.trait='FMD'
sex.gene=read.csv(paste(bp.trait,'_',cvd.trait,'_GWAS_colocalization_topSEXGene.csv',sep=''),header=T)
```
Generate the plots for the candidate regions with sex-specific and sex-biased pleiotropy of input CVD. The output figures will be saved as .png in the same directory. 
```
for(i in 1:nrow(sex.gene)){
bp.sex.region.locus.plot(outname=sex.gene[i,1],cvd.data1,bp.trait='PP',cvd.trait='FMD',locus.chr=sex.gene$CHR[i],locus.st=sex.gene$ST[i],locus.ed=sex.gene$ED[i])
}
```
## F. Other Shiny app for visualization
Explore additional Shiny applications for interactive visualization of regional genetic association plots of female and male blood pressure (BP) data
```
source('shiny_sex_BP_select_region.R')
```
## I. Screen for BP-associated regions with sex-specific pleiotropy of CVD
## Usage for bp.sex.colocalization():
bp.sex.colocalization(cvd.data,bp.trait='PP',cvd.trait='CVD',...)

## Input Arguments:
**cvd.data**: This parameter represents the user's provided GWAS data for the cardiovascular disease (CVD) trait of interest. The format for this input file is described below.

An example file is included in the directory, sourced from the FMD GWAS (A. Georges, M.L. Yang, T.E. Berrandou, et al., Nature Communications, 2021). Please ensure that the header of your file adheres to the following specifications, and be mindful that the genome assembly version should be GRCh37/hg19. The data format for column "pos.hg19" should follow this pattern: CHR:BP.
   
>    CHR     BP    BETA     SE   pval pos.hg19
> 
>    1 845635 -0.0218 0.0652 0.7382 1:845635
>
>    1 845938 -0.0295 0.0643 0.6463 1:845938
> 
>    1 846078 -0.0066 0.0651 0.9197 1:846078


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
## II. Visualization of regional plot
## Usage for bp.sex.region.locus.plot():
```
bp.sex.region.locus.plot(outname,cvd.data,bp.trait,cvd.trait,locus.chr,locus.st,locus.ed)
```
## Input Arguments:

*outname: output file name prefix

*cvd.data: This parameter represents the user's GWAS data for the cardiovascular disease (CVD) trait of interest. The format for this input file is described above.

*bp.trait: Specify the sex-stratified GWAS data for blood pressure (BP) traits sourced from the UK Biobank (UKB), as detailed by ML Yang et al. You have the option to choose from 'SBP' (systolic blood pressure), 'DBP' (diastolic blood pressure), or 'PP' (pulse pressure).

*cvd.trait: Input the name of the user's cardiovascular disease (CVD) trait of interest. This trait name will be incorporated into the output file name for reference.

*locus.chr: Chromosome for plotting the regional analysis.

*locus.st: Start base pair position (hg19) for plotting the regional analysis.

*locus.ed: End base pair position (hg19) for plotting the regional analysis.

## Output:
Generate and save figures representing genetic associations for female-only BP, male-only BP, and CVD within a specified region. These figures will be saved in 'png' format.

## References:
Please cite this paper if you utilize this pipeline script:

Yang, M.-L., Xu, C., Gupte, T., Hoffmann, T. J., Iribarren, C., Zhou, X., & Ganesh, S. K. (2023). Leveraging sex differences of the complex genetic architecture of blood pressure to define sex-biased arterial genome regulation and cardiovascular disease risks.

## Contact email:
Min-Lee Yang 
mlyang@umich.edu

## About our Lab:
PI: Dr. Santhi Ganesh 

University of Michigan Ann Arbor, Cardiovascular Medicine, U.S.A.

https://www.ganeshlab.org/


