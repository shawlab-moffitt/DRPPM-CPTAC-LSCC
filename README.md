# DRPPM-EASY-LargeProject

# Introduction

This is an extention of the [DRPPM Expression Analysis ShinY (EASY) App](https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY) which allows the user to analyze expression data for a given project and in this use case the user is able to upload data from large project databases and within the app the user may select a subst or condition of interest to further analyze. In the demonstration apps featured users may perform further analysis of the [Cancer Cell Line Encyclopedia (CCLE)](https://sites.broadinstitute.org/ccle/) and a [Lung Squamous Cell Carcinoma](https://www.sciencedirect.com/science/article/pii/S0092867421008576?via%3Dihub) study from Clinical Proteomic Tumor Analysis Consortium (CPTAC). Aside from the sample selection page, the downstream analysis is identical to the original EASY app where it allows the user to perform unsupervised clustering, custom differential gene expression and gene set enrichment analysis on the data selected. While this GitHub page can be cloned and ran locally there are current versions of the CCLE app and CPTAC app found at the links below.

CCLE: http://shawlab.science/shiny/DRPPM_EASY_LargeProject_CCLE/
CPTAC_LSCC: http://shawlab.science/shiny/DRPPM_EASY_LargeProject_CPTAC/

# Installation

* Download ZIP file from https://github.com/shawlab-moffitt/DRPPM-EASY-Database-Integration
* Unzip and load into directory as a project in R Studio
* Open the ‘App.R’ script and write in user input files and options as directed at the top of the script
  * ‘App.R’ script begins with example files loaded in from the ExampleData folder
* Press ‘Run App’ button in R Studio to run in application or browser window and enjoy!
  * The app script will install any missing packages that the user may not have locally

# R Dependencies

|  |  |  |  |  |
| --- | --- | --- | --- | --- |
| shiny_1.6.0 | shinythemes_1.2.0 | shinyjqui_0.4.0 | shinycssloaders_1.0.0 | tools_4.1.0 |
| dplyr_1.0.7 | tidyr_1.1.3 | readr_2.0.1 | tibble_3.1.3 | DT_0.18 |
| ggplot2_3.3.5 | plotly_4.9.4.1 | enrichplot_1.12.2 | pheatmap_1.0.12 | ggrepel_0.9.1 |
| enrichR_3.0 | limma_3.48.3 | clusterProfiler_4.0.5 | limma_3.48.3 | GSVA_1.40.1 |
| BiocManager_1.30.16 | reshape2_1.4.4 | ggpubr_0.4.0 |  |  |

# Required Files

## Required User Input Files

* **Expression Matrix (.tsv/.txt):**
  * Must be tab delimited with gene names as symbols located in the first column with subsequent columns consiting of the sample name as the header and expression data down the column.
* **Meta Data (.tsv/.txt):**
  * Three column, tab-delimited, format with columns in the order of Sample Name, Meta Group, Sample Type
  * This is used to group the expression data into comparison groups for differential expression analysis
* **Meta Selector Data (Optional):**
  * This is used when the expression data is able to be subset for analysis
    * In the case of the CCLE example we can subset the expression data based on disease or lineage before grouping with the meta file
  * This is a two column, tab-delimited, file with the first column being the meta groups (as seen in the second column of the main meta data) and the second column is either "Phenotype" or "Selector"
    * "Selector" designates if the meta group is used to subset the expression data
    * "Phenotype" designates if the meta group is used to group the expression data
* **GMT file or Gene Set Data (optional):**
  * If the user chooses to user their own gene set file it must be formatted correctly.
    * If using a .gmt file you can find example formatting by the Broad Institute as seen [here](https://software.broadinstitute.org/cancer/software/gsea/wiki/index.php/Data_formats#GMT:_Gene_Matrix_Transposed_file_format_.28.2A.gmt.29).
    * The other option, usefull if generating your own gene sets, is making a two column tab delimited file with the first column being the gene set name repeating for every gene symbol that would be placed in the second column. Examples of this format can be seen in some of the gene set files we have provided [here](https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY/tree/main/GeneSets).
  * If the user chooses to use their own gene set file, it is recommended that they use the Getting Started Script, [GeneSetRDataListGen.R](https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY/blob/main/GettingStartedScripts/GeneSetRDataListGen.R), to generate an R data list which is needed to perform ssGSEA analysis.
  * These gene set files could be used for generating the enriched signatures table in the Getting Started Script, [GSEA_Sig_Table_Gen.R](https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY/blob/main/GettingStartedScripts/GSEA_Sig_Table_Gen.R) as well as replacing the gene set within the DRPPM-EASY app script.
  * To simplify this optional input there is a tab within the app's GSEA section for the user to upload their own gene set file instead of hard coding it in.
Example inputs can be downloaded https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY/tree/main/ExampleData

## Required and Provided

* **MSigDB Files:** 
  * These gene set files were gathered from the [Molecular Signatures Database (MSigDB)](http://www.gsea-msigdb.org/gsea/msigdb/index.jsp) as separate collections and processed through R to generate a master gene set file to use for GSEA and ssGSEA analysis.
  * These files begin with "msigdb_" and can be found [here](https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY/tree/main/GeneSets).
    * Please note that some gene sets are available for *Homo sapiens* and *Mus musculus* which are designated by HS or MM respectively.
* **Tab 2 Gene Set files:**
  * The tab 2 gene set initially written to show gene sets from the Cell Marker Database but can be adjusted by the user
  * We also provide LINCS L1000 gene sets derived from small molecule perturbations following drug treatment.
  * If the user choses to adjust this gene set they must also ensure there is an RData list file provided for it as well.
    * This file can be generated with one of the [getting started scripts](https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY/blob/main/GettingStartedScripts/GeneSetRDataListGen.R) we described previously.

# App Preparation

Below shows the only section of the script that needs to be updated, currently written for setting up the CCLE Analysis App. The user may enter the project name, the meta file, the meta selector file, the expression matrix file, and a name map file. For the optional files (meta selector and name map) you may leave the contents empty and it will run without them. Once these files are added the app may be run as is.

```
####----Project Name----####
ProjName <- 'CCLE'
####----File Names----####
##--Large Project Files--##
#Meta
db_meta_file <- '~/R/DRPPM-EASY-LargeProject-main/CCLE-data/CCLE_meta_melt.zip'
#Meta Selector File
db_meta_selec_file <- '~/R/DRPPM-EASY-LargeProject-main/CCLE-data/CCLE_meta_selector.tsv'
#Expression Data
db_expr_file <- '~/R/DRPPM-EASY-LargeProject-main/CCLE-data/CCLE_expr_trim_NewName.zip'
#Name Map File
db_namemap_file <- '~/R/DRPPM-EASY-LargeProject-main/CCLE-data/CCLE_NameMap.tsv'
```

# App Features

## Sample Selection

![alt text](https://github.com/shawlab-moffitt/DRPPM-EASY-LargeProject-Integration/blob/main/App_Demo_Pictures/EASY_DB_INT_selectdata.PNG?raw=true)

1. This section to subset expression data will only show if the user includes a meta selector file that is able to subset the expression data based on a variable
   * In the case of the CCLE example the user may select to subset the expression data based on lineage or disease type
2. The condition selection designated which meta group to group the expression data with
3. The user may choose to log2 transform the expression data
4. The label is automatically filled with the Project Name given in the script but is able to be adjusted here
5. Once the user finishes selecting the preferred data, the "Load/Update Selected Data" button may be pressed for the data sets to be loaded into the program
6. The data will appear on the screan and the user has to ability to download the selected data individually
7. The Sample Name Guide is an optional table that will appear when one is given to the program. It may be useful if samples might have multiple names or other information about the sample.

The corresponding tabs are identical to the original DRPPM EASY app and more information about the features can be found [here](https://github.com/shawlab-moffitt/DRPPM-EASY-ExprAnalysisShinY).
