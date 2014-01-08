---
layout: page
category : [ANTsR]
title: ANTsR 
tagline: medical imaging in R
comments:  disqus
tags: [ANTsR, code]
---
{% include JB/setup %}

*R* is a powerful statistical tool that helps one systematize
reproducible scientific studies.  By interfacing
[ANTs](http://stnava.github.io/ANTs) with *R*, we hope to provide a
modern framework for medical analytics, with a focus on imaging-assisted
prediction and statistical power.

I am currently experimenting with distributing ANTsR binaries in order
to lower the barrier to entry for new collaborators.

The
[osx mavericks x86_64 binaries](https://dl.dropboxusercontent.com/u/9717050/ANTsR_1.0_R_x86_64-apple-darwin10.8.0.tgz)

The
[linux x86_64 binaries](https://dl.dropboxusercontent.com/u/9717050/ANTsR_1.0_R_x86_64-pc-linux-gnu.tar.gz)

After you download these, you can install to *R* from command line with:

    $ R CMD INSTALL ANTsR_binaryname.tar.gz

Read [ANTsR Quick Start](http://stnava.github.io/ANTsR/ANTsRGettingStarted.html)

Complete usage and documentation available at:
[ANTsR site](http://stnava.github.io/ANTsR/index.html) or
through the standard R interface e.g. `?antsRegistration`

## "By hand" *ANTsR* installation

If you install *ANTsR* from source, instead of the binaries, you also
get *ANTs* and [ITK](http://www.itk.org/) installed.  This enables one to use command line tools from
*ANTs* for pre-processing and *ANTsR* for the final statistical
study.

This content is contained in more detail in the script
[here](https://raw.github.com/stnava/RMI/master/stnava/install_anstr_packages.sh)

First, install [R](http://www.r-project.org/), [cmake](http://www.cmake.org/) and [git](http://git-scm.com/).  In osx, you need [xcode](https://developer.apple.com/xcode/) as well.

In *R*, install dependencies:

	install.packages("Rcpp",type="source")
	mypkg<-c("signal","timeSeries","mFilter","MASS","robust","magic","knitr","pixmap","rgl","misc3d")
	for ( x in mypkg ) install.packages(x)

I like to install *Rcpp* from source to get the most up-to-date version.
    
## Sample *ANTsR* Session

We believe that variable collections, acting synergistically, may better predict outcomes
than individual variables.  This idea captures the difference between
*univariate* and *multivariate* analysis.  *ANTsR* gives one a variety
of tools for building multivariate models which we will detail over
time via this site and academic papers.

	library(ANTsR)
	# assess whether values from one image relate to those of another
	im1<-antsImageRead( getANTsRData('r16') ,2)
	im2<-antsImageRead( getANTsRData('r64') ,2)
	mat1<-as.matrix( im1 )
	mat2<-as.matrix( im2 )
	sd1<-apply(mat1,FUN=sd,MARGIN=2)
	sd2<-apply(mat2,FUN=sd,MARGIN=2)
	mat1<-mat1[, sd1 > 0 & sd2 > 0 ]
	mat2<-mat2[, sd1 > 0 & sd2 > 0 ]
	ff<-sparseDecom2( inmatrix=list(mat1,mat2), inmask=list(NA,NA),
       sparseness=c(0.02,0.02) ,nvecs=4 , its=21, perms=0,
	   smooth=0, cthresh=c(0,0), ell1 = -10 )
	print( summary( lm(  ff$projections[,1] ~ as.matrix( ff$projections2 ) ) ) )
	print( summary( lm(  ff$projections[,2] ~ as.matrix( ff$projections2 ) ) ) )

This is a toy example that illustrates *image I/O*, *dimensionality
reduction* via [sparse canonical correlation analysis](http://www.ncbi.nlm.nih.gov/pubmed/?term=sparse+canonical+correlation+analysis++avants) and *statistical inference* with *R*'s `lm` function.

## To-Do

*ANTsR* does not pass R CMD check because of a variety of
 documentation issues.

