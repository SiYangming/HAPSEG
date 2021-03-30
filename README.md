# HAPSEG

http://www.cell.com/action/showMethods?pii=S1535-6108%2817%2930299-4

## 安装ABSOLUTE和HAPSEG的依赖包

```R
source("https://bioconductor.org/biocLite.R")
biocLite(c("geneplotter", "DNAcopy", "Rcpp", "numDeriv", "foreach"))
```

## 安装ABSOLUTE包

```R
install.packages("ABSOLUTE_1.0.6.tar.gz",repos=NULL)
```

## HAPSEG的编译包

```R
install.packages(c("Cairo","RGtk2","cairoDevice"))
```

## windows下R的编译工具Rtools
https://cran.r-project.org/bin/windows/Rtools/
http://archive.broadinstitute.org/cancer/cga/absolute
```R
install.packages("HAPSEG_1.1.1.tar.gz",repos=NULL)
```

## 使用指南

http://software.broadinstitute.org/cancer/software/genepattern/modules/docs/HAPSEG/
http://precedings.nature.com/documents/6494/version/1

## workflow
http://software.broadinstitute.org/cancer/software/genepattern/using-hapseg-and-absolute-in-genepattern

## 测试数据下载
ftp://ftp.broadinstitute.org/pub/genepattern/example_files

## 使用HAPSEG和ABSOLUTE进行SNP、基因型分析

```R
library(HAPSEG)
library(ABSOLUTE)
ABSOLUTE_path <- system.file("data",package="ABSOLUTE")
HAPSEG_path <- system.file("data",package="HAPSEG")
datalist1 <- paste(ABSOLUTE_path, list.files(ABSOLUTE_path,pattern="*.RData"), sep="/")
datalist2 <- paste(HAPSEG_path, list.files(HAPSEG_path,pattern="*.RData|*.rda"), sep="/")
for (i in 1:length(datalist1)) {
load(datalist1[i])
}
for (i in 1:length(datalist2)) {
load(datalist2[i])
}

## Using the example data in the release bundle we can run the data to recreate the mixing results as follows. This can be run on a multicore system by uncommenting the registerDoMC call and specifying the number of cores that you wish to use. Besides ABSOLUTE this code also requires the use of foreach and optionally doMC. This code will create a directory output which will contain a per-sample subdirectory containing all output as well as a subdirectory hapseg_logs which will contain per-sample text files containing the standard out being emitted from R.

DoHapseg <- function(scan, sif) {
  registerDoSEQ()
  library(HAPSEG)
  plate.name <- "DRAWS"
  snp.file.base <- "./paper_example"
  snp.file.post <- "snp_byAllele.RData"
  seg.fn <- "./paper_example/mix250K_seg_out.txt"
  clusters.fn <- "./paper_example/birdclusters.RData"

  results.dir.base <- "./output"
  phased.bgl.dir <- "./paper_example/phasedBGL/hg18"
  genome <- "hg18"
  platform <- "SNP_250K_STY"

  pop <- "CEPH"
  impute.gt <- FALSE
  plot.segfit <- TRUE
  merge.small <- TRUE
  merge.close <- TRUE
  min.seg.size <- 5
  normal <- FALSE
  out.p <- 0.001
  seg.merge.thresh <- 1e-10
  use.normal <- TRUE
  adj.atten <- FALSE
  snp.fn <- file.path(snp.file.base,
                      paste(scan, snp.file.post, sep="."))
  sif.info <- as.character(sif[scan, ])
  results.dir <- file.path(results.dir.base, scan, "hapseg")
  if (!file.exists(results.dir)) {
    dir.create(results.dir)
  }
  print(paste("Starting scan", scan, "at", results.dir))
  log.dir <- file.path(results.dir.base, "hapseg_logs")
  if (!file.exists(log.dir)) {
    dir.create(log.dir)
  }
  sink(file=file.path(log.dir, paste(scan, ".hapseg.out.txt", sep="")))
  RunHapSeg(plate.name, scan, seg.fn, snp.fn, genome, results.dir, platform,
            pop,impute.gt, plot.segfit, merge.small, merge.close,
            min.seg.size, normal, out.p, seg.merge.thresh, use.normal,
            adj.atten, phased.bgl.dir, calibrate.data=TRUE,
            clusters.fn=clusters.fn, drop.x=TRUE, verbose=TRUE)
  sink()
}
arrays.txt <- "./paper_example/mix250K_arrays.txt"
sif.txt <- "./paper_example/mix_250K_SIF.txt"
## read in array names
scans <- readLines(arrays.txt)[-1]
sif <- read.delim(sif.txt, as.is=TRUE)
library(foreach)
##library(doMC)
##registerDoMC(20)
foreach (scan=scans, .combine=c) %dopar% {
  DoHapseg(scan, sif)
}
```
