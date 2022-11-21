---
layout: post
title: Functional Annotation of P. Generosa transcriptome
date: '2021-10-21'
categories: kallisto, transcriptome, P. generosa, computation, index, QC
tags: P. generosa, index, kallisto
---

## Inspect P. generosa genome for candidate genes. First step is to extract output data from kallisto: 

[link to abundance data](https://raw.githubusercontent.com/ocattau/kallisto/main/analyses/output_01/abundance.tsv?token=ATPNH2GEMVAALCJ4ZONUMEDBQL3A6)

```{r}
abundance_data<-read.table(file="https://github.com/ocattau/kallisto/blob/main/analyses/output_01/abundance.tsv")
head(abundance_data)

```

[link to run info json file](https://github.com/ocattau/kallisto/blob/main/analyses/output_01/run_info.json) 

[link to index file](https://gannet.fish.washington.edu/gigas/data/transcriptome_v5.idx) 

## Next step is to get count data on the abundance.tsv file. Before that can happen, Grab our Geoduck Transcriptome off of Mox and blast it against swiss_prot to get gene IDs 

### slurm script
```{bash}
#!/bin/bash
## 10.29.21 blast p.generosa on MOX
#SBATCH --job-name=blasting_p.generosa_10.29.21
## Allocation Definition
#SBATCH --account=srlab
#SBATCH --partition=srlab
## Nodes
#SBATCH --nodes=1
## Walltime (days-hours:minutes:seconds format)
#SBATCH --time=05-00:00:00
## Memory per node
#SBATCH --mem=500G
##turn on e-mail notification
#SBATCH --mail-type=ALL
#SBATCH --mail-user=ocattau@uw.edu
## Specify the working directory for this job
#SBATCH --chdir=/gscratch/srlab/ocattau/p.generosa #home directory


/gscratch/srlab/programs/ncbi-blast-2.8.1+/bin/blastx \ #load ncbi blastx software
-query /gscratch/srlab/ocattau/p.generosa/Pgenerosa_transcriptome_v5.fasta \ #this is the query file, should be the geoduck transcriptome
-db /gscratch/srlab/blastdbs/ncbi-sp-v5_20210224/swissprot \ #blast databse, should always be this swissprot in order to get gene annotations
-out /gscratch/srlab/ocattau/p.generosa/Panopea-generosa-uniprot_blastx.tab \ #output file 
-evalue 1E-20 \
-num_threads 30 \ #number of threads is going to be +28
-max_target_seqs 1 \
-max_hsps 1 \
-outfmt 6

```

# Output file to comapir to kallisto output 
[link to P.generosa.tab](https://gannet.fish.washington.edu/gigas/Panopea-generosa-uniprot_blastx.tab) 
