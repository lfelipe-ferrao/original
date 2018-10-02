# Alpha-lattice design

## Carlos Henrique Pereira (UFLA,Brazil)

```
rm(list=ls())
setwd("C:/Users/Dell/Desktop/Ex Panicum")
```

### Download the data

- [Data set](https://github.com/lfelipe-ferrao/lfelipe-ferrao.github.io/blob/master/class/ad_plant/Ex.Panicum.csv)

```
data<-read.csv("Ex. Panicum.csv",sep = ";" ,h=T)
str(data)
```

### Organizing the data
```
#==== Creating factors ====#
data$ID<-as.factor(data$ID)
data$HAR<-as.factor(data$HAR)
data$REP<-as.factor(data$REP)
data$BLK_REP<-as.factor(data$BLK_REP)
data$PLOT<-as.factor(data$PLOT)
str(data)

#==== Models ====#
#1) BY Harvest-Environment 
SSET<-subset(data,data$SET=='HP1')
SSET<-subset(data,data$SET=='HP2')
SSET<-subset(data,data$SET=='LP1')
SSET<-subset(data,data$SET=='LP2')

# Order by Type
levels(SSET$TYPE)
SSET<-SSET[order(SSET$TYPE),]
```

### ASREML-R

- Codes to run the alpha-lattice desing in ASREML-R

```
require(asreml)
MOD1<-asreml(GMY/1000 ~ REP+at(TYPE,"C"):FAM,
             random=~BLK_REP+at(TYPE,"HB"):FEMALE+at(TYPE,"HB"):MALE+
               at(TYPE,"HB"):FAM,
             rcov=~at(TYPE):units,
             na.method.X="include",na.method.Y='include',
             maxiter=100, workspace=64e6,data=SSET)

summary(MOD1)$varcomp
```
