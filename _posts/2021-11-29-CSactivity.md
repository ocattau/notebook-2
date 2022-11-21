---
layout: post
title: CS script
date: '2021-11-29'
categories: updates, research, CS, BSA
tags: P. generosa, citrate-synthase, BSA, c. gigas
---
# Get Standardized protein concentration (ug/mL)
```{r}
protein_test<-read.csv(file="/Users/oliviacattau/Documents/data/BSA_Nov22_mantle+gill.csv")
A<-protein_test$Absorbance...570..1.0s...A.
protein<-(A*2500)-1700 #from GLM standard equation
protein_test<-cbind(protein_test, protein)
protein_test<-protein_test[protein_test$protein >= 0,]
M1= subset(protein_test, protein_test$label=='M_1')
M2= subset(protein_test, protein_test$label=='M_2')
G1= subset(protein_test, protein_test$label=='G_1')
G2= subset(protein_test, protein_test$label=='G_2')
M_1<-mean(M1$protein)
M_2<-mean(M2$protein)
G_1<-mean(G1$protein)
G_2<-mean(G2$protein)
protein_list<-c(M_1, M_2, G_1, G_2)
tissue_label<-c("mantle_1", "mantle_2", "gill_1", "gill_2")
protein_content<-as.table(cbind(tissue_label, protein_list))
print(protein_content)
```
# CS activity (nmol/min/uL)
```{r}
CS_Nov22<-read.csv(file="/Users/oliviacattau/Documents/data/CS_Nov22.csv")
CS_Nov22<-CS_Nov22[!(CS_Nov22$Well=="B06" | CS_Nov22$Well=="B03"),]
A<-CS_Nov22$Absorbance...405..0.1s...A.
blank=subset(CS_Nov22, CS_Nov22$label==0)
zero<-mean(blank$A)
new_A<-A-0.0664 #subtract blank well
CS_Nov22<-cbind(CS_Nov22, new_A)
M1= subset(CS_Nov22, CS_Nov22$label=='m1')
M2= subset(CS_Nov22, CS_Nov22$label=='m2')
G1= subset(CS_Nov22, CS_Nov22$label=='g1')
G2= subset(CS_Nov22, CS_Nov22$label=='g2')
CS_tissue<-rbind(M1, M2, G1, G2)
#plot data
library(ggplot2)
GSH<-ggplot(data=CS_tissue)+geom_point(aes(x=as.factor(Time), y=new_A, color=label))+theme_bw()
GSH
time_0<-subset(CS_tissue,CS_tissue$Repeat==1)
time_30<-subset(CS_tissue, CS_tissue$Repeat==30)
time_0$delta<-time_30$new_A-time_0$new_A #delta OD to get GSH
standard_CS<-read.csv(file="/Users/oliviacattau/Documents/data/standard_curve_small.csv")
cs_model<-lm(standard_CS$New_A~(standard_CS$GSH.well))
summary(cs_model)
cs_equation<-paste("y=",coef(cs_model)[[1]], "+", coef(cs_model)[[2]], "*x")
cs_equation
time_0$nmol<-0.0753742256190483 + 0.0465124174107143*delta #equation from GSH standard curve
standard_cs_plot<-ggplot(data=standard_CS,(aes(x=New_A, y=GSH.well)))+geom_point()+geom_smooth(method="lm", se=FALSE, formula = y ~x)+xlab("OD")+ylab("GSH")+theme_bw()+stat_regline_equation(label.x=1, label.y=8)
standard_cs_plot
#get CS activity
B<-21*delta-0.73
V<-50 #uL
D<-1
time<-27 #min
SampleCSactivity<-B/(time*V)*D
time_0$CSactivity<-SampleCSactivity #(nmol/min/uL)
```
|tissue_label|	OD (delta A)|	GSH nmol|	CSactivity (nmol/min/uL)|	protein (ug/mL)|	protein (ug/uL)|	relative CS activity (nmol/min/ug)|
|---|---|---|---|---|---|---|
|mantle_1|	0.116|	1.706|	1.26E-03|	493.89|	0.493888889|	2.56E-03|
|mantle_2|	0.527|	10.337|	7.66E-03|	89.17|	0.089166667|	8.59E-02|
|gill_1|	0.216|	3.806|	2.82E-03|	below range|	NA|	NA|
|gill_2|	0.08|	0.95|	7.04E-04|	445.83|	0.445833333|	1.58E-03|

