---
layout: post
title: Functional Annotation of P. Generosa transcriptome part 3
date: '2022-02-08'
categories: kallisto, transcriptome, P. generosa, computation, index, QC, count data
tags: P. generosa, index, kallisto
---

# First Step: take merged data from geoduck transcriptomes and upload to R
```{r}
countMatrix<-read.table(file="https://raw.githubusercontent.com/sr320/nb-2022/main/P_generosa/analyses/kallisto-0207.isoform.counts.matrix", header=TRUE, sep = '\t')
names(countMatrix)[1]<-"target_id"
head(countMatrix)
```
# Second Step: take blast data and load
```{r}
#load blast data, see blasting .sh file to see code (in GItHub)
blast_data<-read.table(file="https://gannet.fish.washington.edu/gigas/Panopea-generosa-uniprot_blastx.tab")
names(blast_data)[1]<-"target_id" #renamed to match countMATRIX data
names(blast_data)[2]<-"identifiers" #renamed to search in uniprot swiss prot 
```
# Third Step: merge blast data with geoduck transcriptome data 

```{r}
library(dplyr)
library(tidyr)
#merging target_ids to count Matrix to get identifies for swissprot
generosa_counts<-full_join(blast_data, countMatrix, by = "target_id")
generosa_counts<-generosa_counts[-c(3:12)] # not every target ID has a protein identifier...could be an issue later on....
generosa_counts[complete.cases(generosa_counts),] 
#blast_data<-read.csv(file="/Users/oliviacattau/Documents/analysis/code/generosa_counts.csv")#lost count data due to excel stupidity
generosa_counts$identifiers<-sub("\\.\\d+$", "", generosa_counts$identifiers) #removed decimal after identifiers so match Uniprot format
```
# Fourth Step: Go to Uniprot website and grab all go terms for all reviewed proteins
[Uniprot Website](https://www.uniprot.org/uniprot/?query=*&fil=reviewed%3Ayes#)

```{r}
#loaded into gannet and pulled down
uniprot_all_reviewed<-read.csv(file="https://gannet.fish.washington.edu/gigas/uniprot-reviewed_yes.tab", sep='\t', header=TRUE)
```
# Fifth Step: Left Join Tables by identifier

```{r}
names(uniprot_all_reviewed)[1]<-"identifiers" #renamed
pgenerosa_proteins<-left_join(generosa_counts, uniprot_all_reviewed, by ="identifiers")
write.csv(pgenerosa_proteins, file="/Users/oliviacattau/Documents/GitHub/code/characterize_larval_transciptome/pgenerosa_proteins_2_10_2022.tab")
```
[link to protein table](https://gannet.fish.washington.edu/gigas/data/p.generosa/#:~:text=pgenerosa_proteins_2..%3E)

# Sixth Step: Filter by tissue type 

```{r}
pgenerosa_heart<-pgenerosa_proteins %>% count(heart) #makes a table so you can see how many reads have 0 tpm, make a proportion
heart_zero<-max(pgenerosa_heart$n)
pgenerosa_gonad<-pgenerosa_proteins %>% count(gonad)
gonad_zero<-max(pgenerosa_gonad$n)
pgenerosa_ctenidia<-pgenerosa_proteins %>% count(ctenidia)
ctenidia_zero<-max(pgenerosa_ctenidia$n)
pgenerosa_larvae<-pgenerosa_proteins %>% count(larvae)
larvae_zero<-max(pgenerosa_larvae$n)
pgenerosa_juv_amb<-pgenerosa_proteins %>% count(juv_amb)
juv_amb_zero<-max(pgenerosa_juv_amb$n)
pgenerosa_juv_sl<-pgenerosa_proteins %>% count(juv_sl) #not sure what this is, maybe low OA exposure?
juv_sl_zero<-max(pgenerosa_juv_sl$n)
#proportion of zero reads to all reads 
all<-length(pgenerosa_proteins$target_id)
heart_expressed<-(1-(heart_zero/all))*100 #31.09%
gonad_expressed<-(1-(gonad_zero/all))*100 #18.45%
ctenidia_expressed<-(1-(ctenidia_zero/all))*100 #33.36%
larvae_expressed<-(1-(larvae_zero/all))*100 #36.29%
juv_amb_expressed<-(1-(juv_amb_zero/all))*100 #51.95%
juv_sl_expressed<-(1-(juv_sl_zero/all))*100 #52.20%
gene_expression<-matrix(c(heart_expressed, gonad_expressed, ctenidia_expressed, larvae_expressed, juv_amb_expressed, juv_sl_expressed), ncol=1, nrow=6, byrow=TRUE)
rownames(gene_expression)<-c("heart", "gonad", "ctenidia", "larvae", "juv amb", "juv sl")
colnames(gene_expression)<-c("% genes expressed by tissue type")
genes_expressed_by_tissue<-as.data.frame(gene_expression)
write.csv(na_expressed, file="/Users/oliviacattau/Documents/GitHub/code/characterize_larval_transciptome/percent_genes_expressed_by_tissue.tab")
```
[link to % genes expressed by tissue type table](https://gannet.fish.washington.edu/gigas/data/p.generosa/percent_genes_expressed_by_tissue.tab)

|tissue type|% genes expressed|
|---|---|
|heart|31.0952162051792|
|gonad|18.4463022715492|
|ctenidia|33.3606801963989|
|larvae|36.2932463512466|
|juv amb|51.9528812816221|
|juv sl|52.2023022686166|
