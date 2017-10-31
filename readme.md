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

## Download example files
### Use the example data from IRFinder
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'library(DoGs);DoGs:::runDownLoadAndConversonOnCluster("ERP001458",8,"/scratch/projects/bbc/aiminy_project/DI")'
```

## Use detained introns
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'library(DetainedIntrons);DetainedIntrons:::UseIRFinderUseIRFinderBuildRef("input.path","output.path")'
```

## Build reference
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'DetainedIntrons:::UseIRFinderBuildRef("ftp://ftp.ensembl.org/pub/release-89/gtf/mus_musculus/Mus_musculus.GRCm38.89.gtf.gz","ReferenceDir")'

# To build reference for hg38

# Output to H_driver
R -e 'library(DetainedIntrons);DetainedIntrons:::UseIRFinderBuildRef("ftp://ftp.ensembl.org/pub/release-81/gtf/homo_sapiens/Homo_sapiens.GRCh38.81.gtf.gz","/Volumes/Bioinformatics$/Aimin_project/DI/REF/Human-hg38-release81")'

# Output to pegasus
R -e 'library(DetainedIntrons);DetainedIntrons:::UseIRFinderBuildRef("ftp://ftp.ensembl.org/pub/release-75/gtf/homo_sapiens/Homo_sapiens.GRCh37.75.gtf.gz","/media/pegasus/aiminy_project/DI/REF/Human-hg19-release75")'
```

## Prepare fastq file for each sample
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'library(DetainedIntrons);DetainedIntrons:::ParserSampleInfor("/media/H_driver/Aimin_project/DI/req020416ew.csv","/media/H_driver/James/Data_temp","/media/H_driver/Aimin_project/DI/Fq_data")'
```

## IRFinder
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
R -e 'library(DetainedIntrons);DetainedIntrons:::IrFinder("/media/H_driver/Aimin_project/DI/Fq_data","/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75","AGATCGGAAG","/media/pegasus/aiminy_project/DI")'
```

## Use GLM for analysis
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
# IR.path = "/media/pegasus/aiminy_project/DI"
# sample.infor.file = "/media/H_driver/Aimin_project/DI/req020416ew.csv"
R -e 'library(DetainedIntrons);DetainedIntrons:::UseGLM4Analysis(IR.path,sample.infor.file)'
```


## Filter fastq file firstly
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}

# On linux

R -e 'library(DetainedIntrons);input.fq.dir = "/media/H_driver/Aimin_project/DI/Fq_data";output.dir ="/media/H_driver/Aimin_project/DI/Fq_data_filtered";trimmed.len=76;DetainedIntrons:::FilterFastq (input.fq.dir,output.dir,trimmed.len)'

R -e 'library(DetainedIntrons);input.fq.dir = "/media/H_driver/Aimin_project/DI/Fq_data";output.dir ="/media/pegasus/Project/DI/Fq_data_filtered";trimmed.len=76;DetainedIntrons:::FilterFastq (input.fq.dir,output.dir,trimmed.len)'

```

## Use rMATs to get results
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}

# On linux

R -e 'library(DetainedIntrons);input.fq.dir = "/media/H_driver/Aimin_project/DI/Fq_data";output.dir ="/media/H_driver/Aimin_project/DI/Fq_data_trimmed";trimmed.len=76;DetainedIntrons:::TrimFastq(input.fq.dir,output.dir,trimmed.len)'

R -e 'library(DetainedIntrons);IR.path = "/media/H_driver/Aimin_project/DI/Fq_data";sample.infor.file = "/media/H_driver/Aimin_project/DI/req020416ew.csv";gtf = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/transcripts.gtf";output.dir = "/media/H_driver/Aimin_project/DI/Output_rMATS";baseindex = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/STAR";type = "single";len = 75;DetainedIntrons:::UserMATs(IR.path,sample.infor.file,gtf,baseindex,output.dir,type,len)'


R -e 'library(DetainedIntrons);IR.path = "/media/H_driver/Aimin_project/DI/Fq_data_trimmed";sample.infor.file = "/media/H_driver/Aimin_project/DI/req020416ew.csv";gtf = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/transcripts.gtf";output.dir = "/media/H_driver/Aimin_project/DI/Output_rMATS";baseindex = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/STAR";type = "single";len = 76;DetainedIntrons:::UserMATs(IR.path,sample.infor.file,gtf,baseindex,output.dir,type,len)'

R -e 'library(DetainedIntrons);IR.path = "/media/H_driver/Aimin_project/DI/Fq_data_trimmed";sample.infor.file = "/media/H_driver/Aimin_project/DI/req020416ew.csv";gtf = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/transcripts.gtf";output.dir = "/media/H_driver/Aimin_project/DI/Output_rMATS";baseindex = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/STAR";type = "single";len = 76;DetainedIntrons:::UserMATs(IR.path,sample.infor.file,gtf,baseindex,output.dir,type,len)'
```

## run RNASeq-MATS.py in background process
```{r eval=FALSE, message=FALSE, warning=FALSE, results='hide'}
# use "python /home/aiminyan/rMATS.3.2.2.beta/RNASeq-MATS.py" to run RNASeq-MATS.py
R -e 'library(DetainedIntrons);IR.path = "/media/pegasus/Project/DI/Fq_data_filtered";sample.infor.file = "/media/H_driver/Aimin_project/DI/req020416ew.csv";gtf = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/transcripts.gtf";output.dir = "/media/pegasus/Project/DI/Output_rMATS_filtered";baseindex = "/media/H_driver/Aimin_project/DI/REF/Human-hg19-release75/STAR";type = "single";len = 76;DetainedIntrons:::UserMATs(IR.path,sample.infor.file,gtf,baseindex,output.dir,type,len)'
```
