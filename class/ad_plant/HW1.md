# HOMEWORK 1

## POPULATION GENETICS

### Install packages
```
source("https://bioconductor.org/biocLite.R")
biocLite("impute")
install.packages("snpReady")
```

# Download the data
a = read.csv("/home/lferrao/Downloads/12864_2017_3805_MOESM2_ESM.csv",
         header = T,na.strings = "-")

#  Table 1
# ---- Marker by chr
chr.total = a$Chromossome
table(chr.total)

# Density of markers - Chr01
 
#Same command to run other chrs ("Chr02", "Chr03" ...)
idx = chr.total %in% "Chr01"
chr1.subset = a[idx,] #Selecting Chr01
max(chr1.subset$Chromossome.Position) # Chr size
mean_of_snp = 533/max(chr1$Chromossome.Position) # snp density

# Transforming the data 
snp = a[,-c(1:15)] # Eliminate cols from 1:15
snp[snp == "NN"]<-NA # Missing data
rownames(snp) = a$SNP.name
snp012 = raw.data(t(as.matrix(snp)), frame = "wide", 
               base=TRUE,  imput = FALSE, 
               call.rate = 0.1, maf=0.0001) # 012 format
dim(snp012$M.clean) #181 ind  vs. 6286 SNPs

# Metrics implemented in the snpReady
metric = popgen(M=snp012$M.clean)
head(metric$whole$Markers)
metric$whole$Population
metric$whole$Variability

#------------------ ## ------------------ ## --------------------- ## ----------
# Optional: Running a PCA 
snp012.imputed = raw.data(t(as.matrix(snp)), frame = "wide", 
                  base=TRUE,  imput = TRUE,imput.type = "mean", 
                  call.rate = 0.1, maf=0.0001)
G <- G.matrix(M = snp012.imputed$M.clean, method = "VanRaden", format = "wide") 
EVD<-prcomp(G$Ga) # PCA
summary(EVD)
group.evd = kmeans(EVD$rotation[,1:2],4)$cluster

library(ggrepel)
library(ggplot2)
data = data.frame (EVD$rotatio[,1:2],group = group.evd)

ggplot(data,aes(x= data$PC1, y=data$PC2, color=as.factor(data$group), label=rownames(data))) +
  geom_point(size=2.5, pch=21,color = "black",aes(fill=as.factor(data$group))) +
  #geom_smooth(method=lm, aes(fill=type), alpha=0.15)+
  scale_fill_manual(values=c("#386cb0","#7fc97f",'indianred1',"#fdb462"))+
  scale_color_manual(values=c("#386cb0","#7fc97f",'indianred1',"#fdb462"))+
  geom_label_repel(box.padding = 0.2, size=2.8) +
  theme_bw() +
  theme(legend.position="none",
        legend.title = element_blank())+
  xlab("PC1(8%)")+
  ylab("PC2(1%)")
