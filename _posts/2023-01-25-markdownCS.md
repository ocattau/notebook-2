---
layout: post
title: CS markdown file
date: '2023-01-25'
categories: CS oyster methods
tags: CS BSA results
---

Coding Resources for paper named "Citrate Sythase Response and Multiple Stress in Pacific Oysters" authored by Olivia Cattau, Matt George and Steven Roberts
University of Washington School of Aquatic and Fisheries Sciences
Primary Contact: Olivia Catttau

For Calculating and Analysis of Citrate Sythase Enzyme Activity in Pacific Oysters (C. gigas)

#Load Libraries for the entire script
```{r}
library(ggplot2)
library(dplyr)
library(ggpubr)
library(knitr)
```

#Load Raw Citrate Synthase Absorbance data and Protein Data from spectrophotometer 
```{r}
CS_absorbance<-read.csv(file="/Users/oliviacattau/Documents/GitHub/CS-manuscript/raw-data/Rawdata_Absorbance_CS.csv")
BSA_absorbance<-read.csv(file="/Users/oliviacattau/Documents/GitHub/CS-manuscript/raw-data/BSA_absorbance.csv")
```

#Load morphometric data and labels for oyster numbers
```{r}
morph<-read.csv(file="https://raw.githubusercontent.com/mattgeorgephd/NOPP-gigas-ploidy-temp/main/202107_EXP2/citrate_synthase/Raw%20Data/morphometrics_CS.csv")
```

#Look at Background Control for significance
```{r}
CS_background<-read.csv(file="/Users/oliviacattau/Documents/GitHub/NOPP-gigas-ploidy-temp/202107_EXP2/citrate_synthase/Raw Data/CS_12_10_22_background.csv")#from 12.19.22

CS_BACKGROUND_ANOVA<-lm(CS_background$Absorbance...405..0.1s...A.~bck, data=CS_background)
car::Anova(CS_BACKGROUND_ANOVA)  

background_plot<-ggplot(data=CS_background, aes(x=Repeat, y=CS_background$Absorbance...405..0.1s...A., color=(bck)))+geom_point()+theme_bw()+geom_smooth(method="lm", se=FALSE, formula=y~x)+scale_x_continuous(breaks=seq(1, 10, by=1)   ) 
background_plot
```
background control is significantly different from the standard CS values which means that I do not have to subtract the background from the sample readings. Also you can see in 'background plot' that the background readings did not increase while the CS readings did. 

#Look at plate effects before proceding
```{r}
anova1<-lm(delta.OD~plate*X1*X10*repeat., data=CS_absorbance)
anova(anova1)
summary(anova1) #plate effects not significant
```
no plate effects, can use entire data set

#View Controls
plot CS standards to calculate standard curve equation
```{r}
CS_controls<-filter(CS_absorbance, ID == 0 | ID == 8 | ID == 16 | ID == 24 | ID == 32 |ID == 40) 

CS_controls2<-(CS_absorbance[c(2:6),]) #y=96x -0.29

#plot CS standards
standard_plot<-ggplot(data=CS_controls,(aes(x=X1, y=as.numeric(ID))))+geom_point()+geom_smooth(method="lm", se=FALSE, formula = y ~ poly(x, 1) )+xlab("A @ 570nm")+ylab("standard values")+theme_bw()+stat_regline_equation(label.x=0.25, label.y=20)
standard_plot
```

#Extract Equation from standard curve
$y= 33x + 2.2$

#Calculate OD and CS values from standard curve
$OD=X10-X1$
$x=OD$ so that $nmol CS = 33*OD +2.2$
```{r}
control_table<-CS_absorbance %>% 
  filter(plate != 5) %>% #plate 5 had errors
  group_by(ID) %>%
  summarise(avg1=mean(X1), avg2=mean(X10)) 

CS_absorbance2<-mutate(control_table, OD = avg2-avg1)

CS_absorbance3<-mutate(CS_absorbance2, nmol = 33*OD + 2.2) #nmol of CS enzyme
```

#Calculate protein values from Bovine Serum Assay (BSA) to standardize CS values
```{r}
protein_data<-BSA_absorbance %>%
  group_by(ID) %>%
  summarise(BSA = mean(A))

bsa_standards<-filter(protein_data, ID == 0 | ID == 125 |ID == 250| ID == 500 | ID == 750 | ID == 1000| ID == 1500 | ID == 2000)
plot(bsa_standards) #linear portion only from 0 to 1000

bsa_standards<-(protein_data[c(1, 2, 3, 6, 7, 8),]) #select correct values [0:1000]

bsa_standards$ID <- as.numeric(as.character(factor(bsa_standards$ID, levels=c("0", "125", "250", "500", "750", "1000"))))

BSA_standard_curve<-ggplot(data=bsa_standards,(aes(x=BSA, y=(ID))))+geom_point()+geom_smooth(method="lm", se=FALSE, formula = y ~ x )+theme_bw()+stat_regline_equation(label.x=0.6, label.y=500)+ylab("ug/mL")
```
Equation from BSA protein curve produces $y=2300x-1500$ where y=Protein in ug/mL and x=Absorbance from spectrophotometer from BSA assay

#Make final table with combined CS and BSA values
$nmol CS = 33*OD +2.2$
```{r}
protein_data$protein<-(2300*protein_data$BSA)-1500 #ug/mL

protein_data$P<-(protein_data$protein)/1000/1000 #ug/ml-> ug/uL-> mg/uL  also equal to total protein extracted 

t<-45 #min
m<-33 #slope from CS standard curve
b<-2.2 #intercept from CS standard curve
V<-50 #uL from CS procedure 
D<-1 #dilution coefficient, should be 1 since we did not dilute

Full_dataset<-full_join(CS_absorbance3, protein_data, by='ID')

Full_dataset<-Full_dataset[complete.cases(Full_dataset),] #remove NAs

Full_dataset$CS_activity<-(Full_dataset$nmol/(t*V))*D/Full_dataset$P
```      

#Attach Morphometric Data
Join the results (CS and BSA data) to morphometric data gathered during the experiment,
n=68 
```{r}
treatments<-read.csv("/Users/oliviacattau/Documents/GitHub/NOPP-gigas-ploidy-temp/202107_EXP2/citrate_synthase/Raw Data/treatments.csv")

filter_my_data<-morph %>%
  filter(morph$ID %in% Full_dataset$ID)

filter_my_data2<-filter_my_data[c(1,2,5,6,10,11,12,14,16,17)] #remove columns without data
#keep: ploidy, trt, shell_length, shell_width, shell_height, mortality, shell volume, cal_dry_weight, ploidy.trt, tank

Full_data<-full_join(filter_my_data2, treatments, by="ID")

Full_data2<-full_join(Full_data, Full_dataset, by="ID")

Full_data3<-Full_data2[complete.cases(Full_data2),] #remove NAs


#add mortality data
#mortality
mortality<-read.csv("/Users/oliviacattau/Documents/GitHub/NOPP-gigas-ploidy-temp/202107_EXP2/citrate_synthase/Raw Data/survival_oc.csv")
final_mort<-mortality$X..survival
final_list<-mortality$trt
mortality2<-data.frame(final_mort, final_list)
```


#Data Visualization
```{r}
ploidy_linear_plot<-ggplot(data=Full_data3,(aes(x=protein, y=CS_activity, color=ploidy)))+geom_point()+theme_bw()+geom_smooth(method="lm", se=FALSE, formula = y ~ x ) + ylab("CS Activity (nmol/(mg*min))") +xlab("Protein (ug/mL)")

boxplot<-ggplot(data=Full_data3, aes(x=factor(trt), y=CS_activity))+
  geom_boxplot(aes(x=factor(trt), y=CS_activity, color=ploidy))+
  theme_bw()+
  ylab(expression('CS activity nmol' (min^-1) (mg^-1)))+
  xlab("Treatment Group")+ 
  stat_compare_means(comparisons=list(c("T-heat", "T-desiccation")), method = "wilcox.test", aes(label="..p.signif.."), label.y=10)+
  stat_compare_means(comparisons=list(c("D-heat", "D-desiccation")), method = "wilcox.test", aes(label="..p.signif.."), label.y=9)+
  stat_compare_means(comparisons=list(c("T-control", "T-desiccation")), method = "wilcox.test", aes(label="..p.signif.."), label.y=10)+
   stat_compare_means(comparisons=list(c("D-control", "D-desiccation")), method = "wilcox.test", aes(label="..p.signif.."), label.y=9)+
  stat_compare_means(comparisons=list(c("D-desiccation", "T-desiccation")), method = "wilcox.test", aes(label="..p.signif.."), label.y=13)+geom_line(data=mortality2, aes(x=final_list, y=final_mort/7, group=1),color="red", linetype="dashed")+scale_y_continuous(limits=c(0,15),
    # Add a second axis and specify its features
    sec.axis = sec_axis(~.*7, name="% survival")
  ) +theme(axis.title.y.right = element_text(color="red"))

boxplot
```
