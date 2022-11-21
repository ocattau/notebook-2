---
layout: post
title: steps on how to create kalllisto index file
date: '2021-10-13'
categories: kallisto, transcriptome, P. generosa, computation, index, QC
tags: P. generosa, index, kallisto
---

[link to Patcher Lab handbook](https://pachterlab.github.io/kallisto/starting)

# Building an Index

```{bash}
#download data from server with curl  --insecure -O x.fastq.gz
#load kallisto
#cd kallisto/tests
#kallisto index -i transcripts.idx transcripts.fasta.gz
cd /home/shared/kallisto/test
 /home/shared/kallisto/kallisto \
index -i ~/data/transcriptome_v5.idx /home/olivia/x.fasta
```
# Quantification

```{bash}
kallisto quant -i transcripts.idx -o output -b 100 reads_1.fastq.gz reads_2.fastq.gz
run_info.json 
#The run_info.json file contains a summary of the run, including data on the number targets used for quantification, and the number of bootstraps performed
```
# Upload .idx file and output_1 file to Gannet

```{bash}
git status
git add file_name
git commit -m "note"
git status
rsync -avP ocattau@gannet.fish.washington.edu:/volume2/web/gigas
#enter gannet PW
#push to github, check file size to make sure you don't over commit! 
```

run_info.json file
```{}
{
	"n_targets": 1363959,
	"n_bootstraps": 100,
	"n_processed": 4005095,
	"n_pseudoaligned": 2902511,
	"n_unique": 909186,
	"p_pseudoaligned": 72.5,
	"p_unique": 22.7,
	"kallisto_version": "0.46.1",
	"index_version": 10,
	"start_time": "Wed Oct 13 12:32:21 2021",
	"call": "/home/shared/kallisto/kallisto quant -i /home/olivia/data/transcriptome_v5.idx -o /home/olivia/analyses/output_01 -b 100 /home/olivia/Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L002_R1_001.fastq.gz /home/olivia/Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L002_R2_001.fastq.gz"
}
```
