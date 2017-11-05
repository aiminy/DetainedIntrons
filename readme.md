# Detained introns
A R pipeline for processing detained introns related RNA-Seq data

## Installation
The latest version can also be installed by
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
In R:
install.packages("getPass")
library(devtools)
library(getPass)
install_git("https://gitlab.com/aimin.at.work/DetainedIntrons", credentials = git2r::cred_user_pass("aimin.at.work", getPass::getPass()))

Or in command line:

R -e 'library(devtools);library(getPass);install_git("https://gitlab.com/aimin.at.work/DetainedIntrons", credentials = git2r::cred_user_pass("aimin.at.work", getPass::getPass()))'

# You need a password, please email aimin.at.work@gmail.com to get this password
```

## Use detained introns

### Build reference
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'DetainedIntrons:::UseIRFinderBuildRef("ftp://ftp.ensembl.org/pub/release-89/gtf/mus_musculus/Mus_musculus.GRCm38.89.gtf.gz","ReferenceDir")'

# To build reference for hg38
# Output to H_driver
R -e 'library(DetainedIntrons);DetainedIntrons:::UseIRFinderBuildRef("ftp://ftp.ensembl.org/pub/release-81/gtf/homo_sapiens/Homo_sapiens.GRCh38.81.gtf.gz","/Volumes/Bioinformatics$/Aimin_project/DI/REF/Human-hg38-release81")'

# build reference for hg19
# Output to pegasus
R -e 'library(DetainedIntrons);DetainedIntrons:::UseIRFinderBuildRef("ftp://ftp.ensembl.org/pub/release-75/gtf/homo_sapiens/Homo_sapiens.GRCh37.75.gtf.gz","/media/pegasus/aiminy_project/DI/REF/Human-hg19-release75")'
```

### Prepare fastq file for each sample
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'library(DetainedIntrons);DetainedIntrons:::ParserSampleInfor("/media/H_driver/Aimin_project/DI/req020416ew.csv","/media/H_driver/James/Data_temp","/media/H_driver/Aimin_project/DI/Fq_data")'
```

### Prepare QC report for fastq files
 
#### QC analysis for eahc fastq
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'input.fastqFile.dir <- "~/pegasus/Project/Alejandro_atac/DATA/Formatted";output.fastqc <- "~/pegasus/Project/Alejandro_AtacSeq/QC_Formatted";DetainedIntrons:::UseFastQC(input.fastqFile.dir,output.fastqc)'
```

#### Get summary for QC
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
# For unformated
R -e 'input.fastqcOutputDir <- "~/pegasus/Project/Alejandro_atac/QC";output.multiqc <- "~/pegasus/Project/Alejandro_AtacSeq/QC_Sum";DetainedIntrons:::UseMultiqc4Sum(input.fastqcOutputDir,output.multiqc)'

# For formated
R -e 'input.fastqcOutputDir <- "~/pegasus/Project/Alejandro_AtacSeq/QC_Formatted";output.multiqc <- "~/pegasus/Project/Alejandro_AtacSeq/QC_Sum_4_Formatted";DetainedIntrons:::UseMultiqc4Sum(input.fastqcOutputDir,output.multiqc)'
```

### Find IR using IRFinder
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'library(DetainedIntrons);DetainedIntrons:::IrFinder("/media/H_driver/Aimin_project/DI/Fq_data","/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75","AGATCGGAAG","/media/pegasus/aiminy_project/DI")'
```

### Use GLM for analysis
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}

# On linux
# IR.path = "/media/pegasus/aiminy_project/DI"
# sample.infor.file = "/media/H_driver/Aimin_project/DI/req020416ew.csv"
R -e 'library(DetainedIntrons);res <- DetainedIntrons:::UseGLM4Analysis(IR.path,sample.infor.file)'

# On windows
# IR.path = "C:/Users/lxw391/Dropbox (BBSR)/Aimin_project/DI/IRFinderResults"
# sample.infor.file = "H:/Aimin_project/DI/req020416ew.csv"

res <- DetainedIntrons:::UseGLM4Analysis(IR.path,sample.infor.file)
```

## Use rMATs to get results

### Filter fastq file firstly
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
# On linux
# if you need to output to H_driver
R -e 'library(DetainedIntrons);input.fq.dir = "/media/H_driver/Aimin_project/DI/Fq_data";output.dir ="/media/H_driver/Aimin_project/DI/Fq_data_filtered";trimmed.len=76;DetainedIntrons:::FilterFastq (input.fq.dir,output.dir,trimmed.len)'

# If you need to output to pegasus
# run FilterFastq in background process on local linux machine,output results to pegasus directly)
R -e 'library(DetainedIntrons);input.fq.dir = "/media/H_driver/Aimin_project/DI/Fq_data";output.dir ="/media/pegasus/Project/DI/Fq_data_filtered";trimmed.len=76;DetainedIntrons:::FilterFastq (input.fq.dir,output.dir,trimmed.len)'
```

### run RNASeq-MATS.py 
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
# Use "python /home/aiminyan/rMATS.3.2.2.beta/RNASeq-MATS.py" to run RNASeq-MATS.py in background process on local linux machine
R -e 'library(DetainedIntrons);IR.path = "/media/pegasus/Project/DI/Fq_data_filtered";sample.infor.file = "/media/H_driver/Aimin_project/DI/req020416ew.csv";gtf = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/transcripts.gtf";output.dir = "/media/pegasus/Project/DI/Output_rMATS_filtered";baseindex = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/STAR";type = "single";len = 76;DetainedIntrons:::UserMATs(IR.path,sample.infor.file,gtf,baseindex,output.dir,type,len)'

# Make a summary for rMATs output
R -e 'input.dir.4.rMATs.output <- "~/pegasus/Project/DI/Output_rMATS_filtered/MATS_output";output.dir <- "~/pegasus/Project/DI/rMATS_Sum";DetainedIntrons:::sum4rMATs(input.dir.4.rMATs.output,output.dir)'
# You can find results in ~/pegasus/Project/DI/rMATS_Sum
```

### Get Sashimiplot from summary.txt file
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}

# On local linux or macs
R -e 'library(DetainedIntrons);sample.infor.file <- "/Volumes/Bioinformatics$/Aimin_project/DI/req020416ew.csv";input.sum.file <- "~/pegasus/Project/DI/Output_rMATS_filtered/summary.txt";eventType <- "RI";eventsFile <- "/Users/axy148/pegasus/Project/DI/Output_rMATS_filtered/MATS_output/RI.MATS.ReadsOnTargetAndJunctionCounts.txt";outDir <- "/Users/axy148/pegasus/Project/DI/Output_rMATS_filtered/Sashimiplot";cmd <- DetainedIntrons:::generateSashimiplot(sample.infor.file,input.sum.file,eventType,eventsFile,outDir)'

# On cluster
# For submit a job in pegasus, You need to install the following python modules if you do not have them on your machine 
# module rm python;source activate python2;conda install -c anaconda scipy;conda install -c anaconda matplotlib;conda install -c bioconda pysam;conda install misopy
#  
# Check if there exists *.bai files in your rMATS output directory  
# find  /scratch/projects/bbc/Project/DI/Output_rMATS_filtered -type f -name "*.bai" -exec rm -i {} \;
#  
R -e 'library(DoGs);library(DetainedIntrons);sample.infor.file <- "/scratch/projects/bbc/Project/DI/req020416ew.csv";input.sum.file <- "/scratch/projects/bbc/Project/DI/Output_rMATS_filtered/summary.txt";eventType <- "RI";eventsFile <- "/scratch/projects/bbc/Project/DI/Output_rMATS_filtered/MATS_output/RI.MATS.ReadsOnTargetAndJunctionCounts.txt";outDir <- "/scratch/projects/bbc/Project/DI/Output_rMATS_filtered/Sashimiplot";DetainedIntrons:::submitJob(sample.infor.file,input.sum.file,eventType,eventsFile,outDir,job.name="plotSashimi",jT="parallel",wT="72:00",cpu=16,ram=25000,spanPtile=8)'

```
