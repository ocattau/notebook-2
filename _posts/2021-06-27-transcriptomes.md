---
layout: post
title: learning how to characterize larval/juvenile geoduck transccriptomes
date: '2021-06-29'
categories: geoduck
tags: RNA-seq
---

# 2021 May 12th 
library(credentials)
set_github_pat()

```{bash}
curl --insecure \
-O https://owl.fish.washington.edu/halfshell/genomic-databank/Pgenerosa_transcriptome_v5.fasta
```

```{bash}
pwd
mv ../Pgenerosa_transcriptome_v5.fasta ../data/Pgenerosa_transcriptome_v5.fasta
```
```{bash}
cd ../data
curl --insecure \
-O https://owl.fish.washington.edu/nightingales/P_generosa/Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L002_R1_001.fastq.gz
curl --insecure \
-O https://owl.fish.washington.edu/nightingales/P_generosa/Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L002_R2_001.fastq.gz
cd ../code
```
