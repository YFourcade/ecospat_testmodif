ecospat
=======

**Valeria Di Cola, Olivier Broennimann, Blaise Petitpierre, Manuela D'Amen, Frank Breiner & Antoine Guisan**
##### September 26, 2016

Miscellaneous methods and utilities for spatial ecology analysis, written by current and former members and collaborators of the ecospat group of Antoine Guisan, Department of Ecology and Evolution (DEE) & Institute of Earth Surface Dynamics (IDYST), University of Lausanne, Switzerland.

*ecospat* offers the possibility to perform Pre-modelling Analysis, such as Spatial autocorrelation analysis, MESS (Multivariate Environmental Similarity Surfaces) analyses, Phylogenetic diversity Measures, Biotic Interactions. It also provides functions to complement *biomod2* in preparing the data, calibrating and evaluating (e.g. boyce index) and projecting the models. Complementary analysis based on model predictions (e.g. co-occurrences analyses) are also provided.

# -Pre-modelling:
Spatial autocorrelation -->*ecospat.mantel.correlogram*;

Variable selection --> *ecospat.npred*;

Extrapolation Detection --> *ecospat.exdet, ecospat.mess* and *ecospat.plot.mess*;

Phylogenetic diversity measures --> *ecospat.calculate.pd*;

Biotic Interactions --> *ecospat.co-occurrences* and *ecospat.Cscore*;

Niche Quantification --> *ecospat.grid.clim.dyn, ecospat.niche.equivalency.test, ecospat.niche.similarity.test, ecospat.plot.niche, ecospat.plot.niche.dyn, ecospat.plot.contrib, ecospat.niche.overlap, ecospat.plot.overlap.test, ecospat.niche.dyn.index* and *ecospat.shift.centroids*;

Data Preparation --> *ecospat.caleval, ecospat.cor.plot, ecospat.makeDataFrame, ecospat.occ.desaggregation, ecospat.rand.pseudoabsences, ecospat.rcls.grd, ecospat.recstrat_prop, ecospat.recstrat_regl* and *ecospat.sample.envar*;

# -Core Niche Modelling:
Model evaluation --> *ecospat.cv.glm, ecospat.permut.glm, ecospat.cv.gbm, ecospat.cv.me, ecospat.cv.rf, ecospat.boyce, ecospat.CommunityEval, ecospat.cohen.kappa, ecospat.max.kappa, ecospat.max.tss, ecospat.meva.table, ecospat.plot.kappa, ecospat.plot.tss* and * ecospat.adj.D2.glm*;

Spatial predictions and projections --> *ecospat.ESM.Modeling, ecospat.ESM.EnsembleModeling, ecospat.ESM.Projection, ecospat.ESM.EnsembleProjection, ecospat.SESAM.prr, ecospat.migclim, ecospat.binary.model, ecospat.Epred* and *ecospat.mpa*;

Variable Importance --> *ecospat.maxentvarimport*;

# -Post Modelling:
Variance Partition --> *ecospat.varpart*;

Spatial predictions of species assemblages --> *ecospat.cons_Cscore*


In addition, the *ecospat* package includes Niche Quantification and Overlap functions that were used in Broennimann et al. 2012 and Petitpierre et al. 2012 to quantify climatic niche shifts between the native and invaded ranges of invasive species.


# Load data


```{r load_library}
library(ecospat)
citation("ecospat")
```

### Test data for the ecospat library 
*ecospat.testData()*
```{r}
data(ecospat.testData)
names(ecospat.testData)
```

### Test data for the Niche Overlap Analysis
*ecospat.testNiche.inv()*
```{r}
data(ecospat.testNiche.inv)
names(ecospat.testNiche.inv)
```

ecospat.testNiche.nat()
```{r}
data(ecospat.testNiche.nat)
names(ecospat.testNiche.nat)
```

### Test tree for Phylogenetic Diversity Analysis

*ecospat.testTree()*
```{r}
fpath <- system.file("extdata", "ecospat.testTree.tre", package="ecospat")
fpath
tree<-read.tree(fpath)
tree$tip.label
```
Plot tree
```{r tree}
plot(tree, cex=0.6)
```


# Pre-Modelling Analysis


## Spatial Auto-correlation

### Mantel Correlogram with *ecospat.mantel.correlogram()*
```{r mantel_cor}
ecospat.mantel.correlogram(dfvar=ecospat.testData[c(2:16)],colxy=1:2, n=100, 
                           colvar=3:7, max=1000, nclass=10, nperm=100)
```

The graph indicates that spatial autocorrelation (SA) is minimal at a distance of 180 meters. Note however that SA is not significantly different than zero for several distances (open circles).



## Predictor Variable Selection
### Number of Predictors with Pearson Correlation *ecospat.npred()*
```{r}
colvar <- ecospat.testData[c(4:8)]
x <- cor(colvar, method="pearson")
ecospat.npred (x, th=0.75)
```



### Number of Predictors with Spearman Correlation *ecospat.npred()*
```{r}
x <- cor(colvar, method="spearman")
ecospat.npred (x, th=0.75)
```


## Extrapolation Detection Tools


### Extrapolation Detection with *ecospat.exdet()*
```{r}
x <- ecospat.testData[c(4:8)]
p<- x[1:90,] #A projection dataset.
ref<- x[91:300,] # A reference dataset
```

```{r}
ecospat.exdet(ref,p)
```

### Extrapolation detection, creating a MESS object with *ecospat.mess()*
```{r}
x <- ecospat.testData[c(2,3,4:8)]
proj<- x[1:90,] #A projection dataset.
cal<- x[91:300,] #A calibration dataset
```

```{r}
mess.object<-ecospat.mess (proj, cal, w="default")
```

#### Plot MESS with *ecospat.plot.mess()*
```{r mess}
ecospat.plot.mess (xy=proj[c(1:2)], mess.object, cex=1, pch=15)

```
In the MESS plot pixels in red indicate sites where at least one environmental predictor has values outside of the range of that predictor in the calibration dataset. In the MESSw plot, same as previous plot but with weighted by the number of predictors.Finally, the MESSneg plot shows at each site how many predictors have values outside of their calibration range.


## Phylogenetic Diversity Measures
```{r}
fpath <- system.file("extdata", "ecospat.testTree.tre", package="ecospat")
tree <- read.tree(fpath)
data <- ecospat.testData[9:52]
```

### Calculate Phylogenetic Diversity Measures *ecospat.calculate.pd*
```{r}
pd<- ecospat.calculate.pd(tree, data, method = "spanning", type = "species", root = TRUE, average = FALSE, verbose = TRUE )
```
```{r}
pd
```

#### Plot the results (correlation of phylogenetic diversity with species richness)
```{r pd}
plot(pd)
```

## Niche Quantification and Comparison with Ordination techniques

Loading test data for the niche dynamics analysis in the invaded range
```{r}
inv <- ecospat.testNiche.inv
```

Loading test data for the niche dynamics analysis in the native range
```{r}
nat <- ecospat.testNiche.nat
```

### PCA-ENVIRONMENT
#### The PCA is calibrated on all the sites of the study area 
Calibrating the PCA in the whole studay area, including both native and invaded ranges (same as PCAenv in Broenniman et al. 2012)
```{r}
pca.env <- dudi.pca(rbind(nat,inv)[,3:10],scannf=F,nf=2) 
```

#### Plot Variables Contribution with *ecospat.plot.contrib()*
```{r}
ecospat.plot.contrib(contrib=pca.env$co, eigen=pca.env$eig)
```
The correlation circle indicate the contribution of original predictors to the PCA axes.

#### Predict the scores on the axes
```{r}
# PCA scores for the whole study area
scores.globclim <- pca.env$li

# PCA scores for the species native distribution
scores.sp.nat <- suprow(pca.env,nat[which(nat[,11]==1),3:10])$li

# PCA scores for the species invasive distribution
scores.sp.inv <- suprow(pca.env,inv[which(inv[,11]==1),3:10])$li

# PCA scores for the whole native study area
scores.clim.nat <- suprow(pca.env,nat[,3:10])$li

# PCA scores for the whole invaded study area
scores.clim.inv <- suprow(pca.env,inv[,3:10])$li
```


### Calculate the Occurrence Densities Grid with *ecospat.grid.clim.dyn()*
For a species in the native range (North America)
```{r}
# gridding the native niche
grid.clim.nat <- ecospat.grid.clim.dyn(glob=scores.globclim, 
                                       glob1=scores.clim.nat,
                                       sp=scores.sp.nat, R=100,
                                       th.sp=0) 
```

For a species in the invaded range (Australia)
```{r}
# gridding the invasive niche
grid.clim.inv <- ecospat.grid.clim.dyn(glob=scores.globclim,
                                       glob1=scores.clim.inv,
                                       sp=scores.sp.inv, R=100,
                                       th.sp=0) 
```


### Calculate Niche Overlap with *ecospat.niche.overlap()*
```{r}
# Compute Schoener's D, index of niche overlap
D.overlap <- ecospat.niche.overlap (grid.clim.nat, grid.clim.inv, cor=T)$D 
D.overlap
```
The niche overlap between the native and the ivaded range is 22%.

### Perform the Niche Equivalency Test with *ecospat.niche.equivalency.test()* according to Warren et al. (2008)

It is reccomended to use at least 1000 replications for the equivalency test. As an example we used rep = 10, to reduce the computational time. 
```{r}
eq.test <- ecospat.niche.equivalency.test(grid.clim.nat, grid.clim.inv,
                                          rep=10, alternative = "greater")
```
Niche equivalency test H1: Is the overlap between the native and invaded niche higher than two random niches?



### Perform the Niche Similarity Test with *ecospat.niche.similarity.test()*
Shifting randomly the invasive niche in the invaded study area
It is recomended to use at least 1000 replications for the similarity test. As an example we used rep = 10, to reduce the computational time.
```{r}
sim.test <- ecospat.niche.similarity.test(grid.clim.nat, grid.clim.inv,
                                          rep=10, alternative = "greater",
                                          rand.type=2) 
```
Niche similarity test H1: Is the overlap between the native and invaded higher than when the invasive niche is randomly introduced in the invaded study area?


#### Plot Equivalency test
```{r}
ecospat.plot.overlap.test(eq.test, "D", "Equivalency")
```

#### Plot Similarity test
```{r}
ecospat.plot.overlap.test(sim.test, "D", "Similarity")
```
We see that the niche overlap D is 22% and this value is compared to the random distribution of the niche equivalency and niche similarity tests.


### Delimiting niche categories and quantifying niche dynamics in analogue climates with *ecospat.niche.dyn.index()*
```{r niche.dyn}
niche.dyn <- ecospat.niche.dyn.index (grid.clim.nat, grid.clim.inv, intersection = 0.1)
```

#### Visualizing niche categories, niche dynamics and climate analogy between ranges with *ecospat.plot.niche.dyn()*
Plot niche overlap
```{r}
ecospat.plot.niche.dyn(grid.clim.nat, grid.clim.inv, quant=0.25, interest=2,
                       title= "Niche Overlap", name.axis1="PC1",
                       name.axis2="PC2")

ecospat.shift.centroids(scores.sp.nat, scores.sp.inv, scores.clim.nat, scores.clim.inv)
```


#### Plot the niche dynamics along one gradient (here temperature) with *ecospat.plot.niche.dyn()*
```{r}
# gridding the native niche
grid.clim.t.nat <- ecospat.grid.clim.dyn(glob=as.data.frame(rbind(nat,inv)[,10]), 
                                         glob1=as.data.frame(nat[,10]),
                                    sp=as.data.frame(nat[which(nat[,11]==1),10]),
                                    R=1000, th.sp=0) 
# gridding the invaded niche
grid.clim.t.inv <- ecospat.grid.clim.dyn(glob=as.data.frame(rbind(nat,inv)[,10]),
                                         glob1=as.data.frame(inv[,10]),
                                         sp=as.data.frame(inv[which(inv[,11]==1),10]), 
                                         R=1000, th.sp=0) 
t.dyn<-ecospat.niche.dyn.index (grid.clim.t.nat, grid.clim.t.inv,
                                intersection=0.1)
ecospat.plot.niche.dyn(grid.clim.t.nat, grid.clim.t.inv, quant=0, 
                       interest=2, title= "Niche Overlap", 
                       name.axis1="Average temperature")
```

## Biotic Interactions

### Species Co-occurrences Analysis with a Presence-absence matrix using the function *ecospat.co_occurrences()*
```{r co_occ}
data <- ecospat.testData[c(9:16,54:57)]
```
For each pair of species (sp1, sp2), the number (N) of plots where both species were present is divided by the number of plots where the rarest of the two species is present. This index ranges from 0 (no co-occurrence) to 1 (always in co-occurrence) as given in eq. 1.


where N(S1 intersects S2) is the number of times species S1 and S2 co-occur, while Min(NS1, NS2) is the number of times species S1 and S2 co-occur, while is the occurrence frequency of the rarest of the two species.

```{r}
ecospat.co_occurrences (data)
```

### Pairwise co-occurrence Analysis with calculation of the C-score index using the function *ecospat.Cscore()* 

This function allows to apply a pairwise null model analysis to a presence-absence community matrix to determine which species associations are significant across the study area. The strength of associations is quantified by the C-score index and a 'fixed-equiprobable' null model algorithm is applied.

It is recomended to use at least 10000 permutatiobns for the test. As an example we used nperm = 100, to reduce the computational time.
```{r Cscore}
data<- ecospat.testData[c(53,62,58,70,61,66,65,71,69,43,63,56,68,57,55,60,54,67,59,64)]
nperm <- 100
outpath <- getwd()
ecospat.Cscore(data, nperm, outpath)
```
The function returns the C-score index for the observed community (ObsCscoreTot), p.value (PValTot) and standardized effect size (SES.Tot). It saves also a table in the working directory where the same metrics are calculated for each species pair (only the table with species pairs with significant p-values is saved in this version)

## Data Preparation
### Correlation Plot of Variables with *ecospat.cor.plot()*
```{r cor-plot}
data <- ecospat.testData[,4:8]
ecospat.cor.plot(data)
```
A scatter plot of matrices, with bivariate scatter plots below the diagonal, histograms on the diagonal, and the Pearson correlation above the diagonal. Useful for descriptive statistics of small data sets (better with less than 10 variables).


### Calibration And Evaluation Dataset
```{r}
data <- ecospat.testData
caleval <- ecospat.caleval (data = ecospat.testData[53], xy = data[2:3],
                            row.num = 1:nrow(data), nrep = 2, ratio = 0.7,
                            disaggregate = 0.2, pseudoabs = 100, npres = 10,
                            replace = FALSE)
caleval
```
We obtained an evaluation and calibration dataset with a desired ratio of disaggregation.


# Core Niche Modelling

## Model Evaluation
### Presence-only Evaluation Indices- Boyce Index

The argument fit is a vector containing the predicted suitability values
```{r}
fit <- ecospat.testData$glm_Saxifraga_oppositifolia
```

The argument obs is a vector containing the predicted suitability values of the validation points (presence records)
```{r}
obs<-ecospat.testData$glm_Saxifraga_oppositifolia[which(ecospat.testData$Saxifraga_oppositifolia==1)]

```

Calculate and plot Boyce Index with *ecospat.boyce*

```{r boyce}
ecospat.boyce (fit, obs, nclass = 0, window.w = "default", res = 100, 
               PEplot = TRUE)$Spearman.cor
```
Here the boyce index is 0.91. If the rank of predicted expected ratio would be completely ordered along habitat suitability axis then boyce index would be 1. 



### Accuracy of Community Prediction
Indices of accuracy of community predictions *ecospat.CommunityEval()*
```{r}
eval<-ecospat.testData[c(53,62,58,70,61,66,65,71,69,43,63,56,68,57,55,60,54,67,59,64)]
pred<-ecospat.testData[c(73:92)]

```

```{r}
ecospat.CommunityEval (eval, pred, proba=T, ntir=5)
```

## Spatial Predictions and Projections

### ESM Ensemble of Small Models

```{r}
library(biomod2)
path.wd<-getwd()
```

```{r}
# species
# occurrences
xy <- inv[,1:2]
head(xy)
sp_occ <- inv[11]

# env
current <- inv[3:7]
head(current)

## BIOMOD
setwd(path.wd)
t1 <- Sys.time()
sp<-1
```

```{r}
### Formating the data with the BIOMOD_FormatingData() function form the package biomod2

myBiomodData <- BIOMOD_FormatingData( resp.var = as.numeric(sp_occ[,sp]),
                                      expl.var = current,
                                      resp.xy = xy,
                                      resp.name = colnames(sp_occ)[sp])

myBiomodOption <- Print_Default_ModelingOptions()
myBiomodOption@GLM$test = 'none'
myBiomodOption@GBM$interaction.depth = 2
```

```{r ESM.Modeling}
### Calibration of simple bivariate models
my.ESM <- ecospat.ESM.Modeling( data=myBiomodData,
                                models=c('GLM','RF'),
                                models.options=myBiomodOption,
                                NbRunEval=1,
                                DataSplit=70,
                                weighting.score=c("AUC"),
                                parallel=F)  
```

```{r ESM.EnsembleModeling}
### Evaluation and average of simple bivariate models to ESMs
my.ESM_EF <- ecospat.ESM.EnsembleModeling(my.ESM,weighting.score=c("SomersD"),threshold=0)
```

```{r ESM.Projection}
### Projection of simple bivariate models into new space 
my.ESM_proj_current <- ecospat.ESM.Projection(ESM.modeling.output=my.ESM,
                                              new.env=current)
```

```{r ESM.EnsembleProjection}
### Projection of calibrated ESMs into new space 
my.ESM_EFproj_current <- ecospat.ESM.EnsembleProjection(ESM.prediction.output=my.ESM_proj_current,
                                                        ESM.EnsembleModeling.output=my.ESM_EF)
```

## Spatial prediction of communities 

Input data for the first argument (proba) as data frame of rough probabilities from SDMs for all species in columns in the considered sites in rows.
```{r}
proba <- ecospat.testData[,73:92]
```

Input data for the second argument (sr) as data frame with richness value in the first column and sites.
```{r}
sr <- as.data.frame(rowSums(proba))
```



## SESAM framework with *ecospat.SESAM.prr()*
```{r SESAM}
ecospat.SESAM.prr(proba, sr)
```



# Post-Modelling

## Spatial Predictions of species assamblages
### Co-occurrence analysis & Environmentally Constrained Null Models

Input data as a matrix of plots (rows) x species (columns). Input matrices should have column names (species names) and row names (sampling plots).
```{r}
presence<-ecospat.testData[c(53,62,58,70,61,66,65,71,69,43,63,56,68,57,55,60,54,67,59,64)]
pred<-ecospat.testData[c(73:92)]
```


Define the number of permutations. It is recomended to use at least 10000 permutatiobns for the test. As an example we used nperm = 100, to reduce the computational time.
```{r}
nbpermut <- 100
```
Define the outpath
```{r}
outpath <- getwd()
```


Run the function *ecospat.cons_Cscore*

The function tests for non-random patterns of species co-occurrence in a presence-absence matrix. It calculates the C-score index for the whole community and for each species pair. An environmental constraint is applied during the generation of the null communities.

```{r cons_Cscore}
ecospat.cons_Cscore(presence, pred, nbpermut, outpath)
```
The function returns 
- the C-score index for the observed community (ObsCscoreTot), 
- the mean of C-score for the simulated communities (SimCscoreTot), 
- the p.values (PVal.less and PVal.greater) to evaluate the significance of the difference between the former two indices. 
- the standardized effect size for the whole community (SES.Tot). A SES that is greater than 2 or less than -2 is statistically significant with a tail probability of less than 0.05 (Gotelli & McCabe 2002 - Ecology).
If a community is structured by competition, we would expect the C-score to be large relative to a randomly assembled community (positive SES). In this case the observed C-score is significantly lower than expected by chance, this meaning that the community is dominate by positive interactions (aggregated pattern). 

A table is saved in the path specified where the same metrics are calculated for each species pair (only the table with species pairs with significant p.values is saved).




