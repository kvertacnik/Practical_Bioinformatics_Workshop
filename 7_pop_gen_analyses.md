# Population genetics analyses 

Some of these analyses will be done in R on your local computer. See the RStudio tutorial HERE.

We will also use the program vcftools.

___

## Data format and missing data 
### First, we'll be assessing some population structure with the swallowtail dataset, so let's load that up and get @other set up with strata. And let's use the updated version of the other file
setwd("/Users/juliandupuis/Documents/UK/teaching/2022_spring_ABT461G/data_analysis")
ABBC2 <- read.structure("ABBC_8Jan2015.str", n.ind=781, n.loc=10, onerowperind=T,  row.marknames=NULL, col.lab=1, col.pop=2, NA.char="-9", ask=F)
ABBC_other <- read.table("ABBC_8Jan2015_other2.txt", header=T)
other(ABBC2) <- ABBC_other
strata(ABBC2) <- ABBC_other[,2:5]
	# useful just to check what's in @pop
	ABBC2@pop
	# and remember we can change that with
	setPop(ABBC2) <- ~pop_name




## Principal component analysis
Let's start with a Principal Components Analysis (PCA). This is an unsupervised method for assessing population structure where we don't provide any population information. In population genetics it is an exploratory tool to get an idea of population structure. 

A PCA takes large datasets with many variables and uses matrix algebra to find a smaller set of unrelated variables that capture the most variation in the original dataset (principal components). Principal components are typically ranked from highest to lowest explanatory power; the first principal component holds the most variance in the data.

Each principal component has a pair of values:
* The Eigenvector provides the direction of variance.
* The Eigenvalue is the coefficient of the eigenvector and represent the amount of variance the PC explains.

In a PCA scatterplot, the X and Y axes are different principle components (often the 1st and 2nd highest PCs). The x-axis represents the first principal component (PC1). The position of a point along this axis shows the score of that particular data sample on PC1. A higher absolute value on this axis indicates a stronger presence of the features that PC1 represents. Likewise for the y-axis and PC2.

Points that are close to each other have similar PC1 and PC2 scores, indicating that they are similar with respect to the most significant variance patterns in the dataset. For our purposes, clusters of points represent populations.

There are many ways to do a PCA, but since we have genetic data we will use the package [Adegenet](https://adegenet.r-forge.r-project.org/files/tutorial-basics.pdf).

We're going to do this on you local computer in RStudio.

Install (if you haven't already) and load the Adegenet package
```
 library(adegenet)
```



## Principal component analysis
### Let's start with a Principal Components Analysis (PCA). Remember this is an unsupervised method for assessing population structure. First, we have to deal with the missing data in this dataset (missing alleles are noted as "-9"), since PCAs will not work with missing data. A standard way to deal with missing data in ordination methods is to replace any missing data values with the mean allele frequency for that locus. First we can count how many missing values are in this dataset
sum(is.na(ABBC2$tab))
# replace the missing values with mean frequencies
X <- tab(ABBC2, freq = TRUE, NA.method = "mean")
# So what is X?
class(X)
dim(X)
sum(is.na(X))

# Next, let's do the actual PCA on this object we just created. Some of the options in this statistical analyses are above our paygrade in this class, so I won't be going through all the options for each of these.
??dudi.pca
pca1 <- dudi.pca(X, scale = F, scannf = F, nf = 3)
pca1
# Lots of info in there. Of particular interest are:
	# $eig		which are the eigenvalues of the analysis (the amount of variance explained by each PC)
	# $li		the PCs themselves, which are synthetic variables summarizing the genetic diversity
	# $c1		Loadings of individual alleles, representing contributions to the PCs

# Let's think about these PCs a bit. So remember that the PCs are basically imaginary axes of variation in multivariate data. So these axes represent imaginary ranges of variation when you consider all the loci at once. PC1 explains the most variation, PC2 the second most, etc. etc.
barplot(pca1$eig[1:50], main = "PCA eigenvalues", col = heat.colors(50))

# next up, let's look at the PCs, both in raw form, and then plotted
head(pca1$li)
s.label(pca1$li)
# and let's add the eigenvalue info to the plot
add.scatter.eig(pca1$eig[1:20], 3,1,2)
# So the PC values themselves are basically just where individuals are falling along the axes of variation for each PC. So you can basically treat pairs of those PC values as X,Y coordinates (that's what plotted in the plot). You can even look at different pairs than just 1,2
s.label(pca1$li,1,3)
# By default the first plot we made plotted PC1 vs. PC2 (pca1$li,1,2), and now we're plotting PC1 vs. PC3, so the placement of individuals on the Y axis has changed, but their placement on the X axis hasn't (although that's hard to tell with those big name boxes). Let's make that plot a bit more informative by putting population names on there with ellipses and lines to connect each population's individuals
s.class(pca1$li, pop(ABBC2))
# Now it's a bit easier to see the differences between PCs
s.class(pca1$li, pop(ABBC2),xax=1,yax=3)

# Here's where you can lose your mind in the plotting options in R. A simple example:
col <- funky(49)
s.class(pca1$li, pop(ABBC2),xax=1,yax=2, col=transp(col,.6), axesell=FALSE, cstar=0, cpoint=3, grid=FALSE)
title("PCA of ABBC swallowtails\naxes 1-2")
add.scatter.eig(pca1$eig[1:20], 3,1,2)

## Fst 
The fixation index (Fst) is a measure of genetic differentiation between two or more populations.
Values range from 0 to 1. If Fst = 0, there is no genetic distance between populations. If Fst = 1, the two populations are very strongly separated.

But genetic divergence between populations may also vary along a genome. For example, two populations may be genetically very similar across much of their genomes except at a few strongly divergent regions. These divergent regions may therefore contain adaptive genes enabling the two populations to adapt to different environments. So instead it would be interesting to examine if genetic divergence between populations varies along the genome.

To do this, you first need to define the members of each of your populations. You did this at the end of the PCA section where you identified cluster membership.

For each of your clusters/populations, create a file (named something sensible like population1, population2 etc) containing a list of cluster members like this (you can use nano do to this):






## DAPC with strata analysis


## STRUCTURE

## Phylogeny in APE