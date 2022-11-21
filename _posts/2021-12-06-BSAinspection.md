---
layout: post
title: BSA script
date: '2021-11-29'
categories: updates, research, CS, BSA
tags: P. generosa, citrate-synthase, BSA, c. gigas
---

[link to data for standard curve](https://github.com/mattgeorgephd/NOPP-gigas-ploidy-temp/blob/main/202107_EXP2/citrate_synthase/BSA_12.6.21_results.csv)

# Make new standard curve
```{r}
bsa<-read.csv(file="https://gannet.fish.washington.edu/gigas/data/BSA_12.6.21_results.csv")
#12.10.21 inspecting data from 12.6.21 test run
library(ggplot2)
library(ggpubr)
standards<-(bsa[c(1:17, 24:40, 47:63),])
A<-standards$Absorbance...570..1.0s...A.
#make plot
standard_plot<-ggplot(data=standards,(aes(x=as.numeric(label), y=A)))+geom_point()+geom_smooth(method="lm", se=FALSE, formula = y ~ poly(x, 1) )+xlab("Concentration of standard ug/mL")+ylab("A @ 570nm")+theme_bw()+stat_regline_equation(label.x=125, label.y=1)

standard_plot

reverse_plot<-ggplot(data=standards,(aes(x=A, y=as.numeric(label))))+geom_point()+geom_smooth(method="lm", se=FALSE, formula = y ~ x )+theme_bw()+stat_regline_equation(label.x=0.6, label.y=500)+ylab("ug/mL")

reverse_plot

#extract equation from plot
protein_content<-2300*A-1300 #with no correction
```

# extract protein content
```{r}
library(tidyr)
library(reshape2)
#clean bsa file
xdata<-(bsa[-c(1:17, 24:40, 47:63),])
xdata$A<-xdata$Absorbance...570..1.0s...A.
calc.protein.content<-function(xdata) {
  protein<-2400*xdata$A-1300
  A<-xdata$A
  names<-xdata$label
  return(list(label=names, absorbance=A, protein=protein))
}
final_bsa<-as.data.frame(calc.protein.content(xdata))
protein_results_vector<-as.data.frame(tapply(final_bsa$protein, final_bsa$label, mean))
protein_results_vector$protein<-protein_results_vector$`tapply(final_bsa$protein, final_bsa$label, mean)`
protein_results_vector<-protein_results_vector[-c(1)]
weights<-c(12.1, 17.2) #mg
volume<-c(350,350) #uL
BSA_total<-cbind(protein_results_vector, volume, weights)
```

# BSA data from 12.14.21 trial
![image](https://raw.githubusercontent.com/mattgeorgephd/NOPP-gigas-ploidy-temp/main/202107_EXP2/citrate_synthase/bsa%20data%2012.14.21.png)
