# netDx
netDx is an algorithm for building patient classifiers by using patient similarity networks as features.

This repo contains two R packages, each of which need to be separately installed:

1. `netDx/`: Software implementing the netDx method
2. `netDx.examples/`: Data for use cases

The `examples/` folder contains R code that should just run once both `netDx/` and `netDx.examples/` are installed.

The main website for netDx is **http://netdx.org**.

* [Install netDx](#install-netdx)
  * [Prerequisites](#prerequisites)
* [Test functionality](#test-functionality)
  * [Run Medulloblastoma example](#mblastoma)
  * [Run breastcancer LumA example](#brca)
  * [See full list of examples](#other-examples)
  * [Known issues with compiling pdfs](#pdfissue)
* [All other examples](#other-examples)

**Other useful information:**
* [Read the netDx preprint at bioRXiv](https://doi.org/10.1101/084418): Pai et al. (2017). netDx: Interpretable patient classification using integrated patient similarity networks. https://doi.org/10.1101/084418
* Once you have run the included netDx examples, [read the user manual](http://netdx-manual.readthedocs.io/en/latest/) to learn how to design features or predictors. 

## Install netDx

### Prerequisites
**netDx has been tested on Mac OS/X and on Linux systems. For now we recommend you run netDx on these operating systems.** Future versions of netDx will have Windows support.

You must have Java, Python and R installed. Within R, you must have BioConductor installed. To plot the results of the predictor run, including network visualizations such as the EnrichmentMap and integrated patient similarity network, you will need Cytoscape with the latest EnrichmentMap and AutoAnnotate apps installed. This section helps you figure out which of these you need to install. 

If you already have all these, skip to the next section.

#### Java (1.8+ recommended, but will probably work on 1.6+)
The engine netDx uses to integrate patient networks and recommend patients by similarity is implemented in Java, making this interpreter a requirement for netDx.

At command line, run `java --version`. You should see output like this:
```
java version "1.8.0_31"
Java(TM) SE Runtime Environment (build 1.8.0_31-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.31-b07, mixed mode)
```
If you don't see this kind of output, you may need to first [install java](https://java.com/en/).

#### Python (2.7 recommended)
A legacy script to create the database of input patient networks was implemented in Python. For now, netDx requires Python to run but future versions of netDx will not have this requirement.

At command line, run `python --version`. You should see output like this:

```
Python 2.7.9 :: Anaconda 2.1.0 (x86_64)
```

#### R (3.3.1+ recommended)
At command line, run `R --version`. You should see output like this:
```
RR version 3.3.1 (2016-06-21) -- "Bug in Your Hair"
Copyright (C) 2016 The R Foundation for Statistical Computing
Platform: x86_64-apple-darwin13.4.0 (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see
http://www.gnu.org/licenses/.
```

If not, [install R](https://www.r-project.org/).

#### BioConductor (Biobase 2.34.0+ recommended)
[BioConductor](http://bioconductor.org/) is a system of R objects and software specifically for biological applications.
Once you have R installed, install the `biobase` and `GenomicRanges` packages via the BioConductor installer:
```
$ R
> source("http://bioconductor.org/biocLite.R")
> biocLite(c("Biobase","GenomicRanges"))
```
Say `yes` to all dependencies that need to be installed.

#### Cytoscape (3.5.1+ recommended)
In order to generate and visualize patient similarity networks and enrichment maps from the data generated by netDx we will need Cytoscape. If Cytoscape is not located in your Applications directory, install the latest version from http://www.cytoscape.org.

With Cytoscape open, install the following Apps:
* AutoAnnotate v1.2 (http://apps.cytoscape.org/apps/autoannotate)  *Earlier versions may not work.*
* Enrichment Map v3.0.0 (http://apps.cytoscape.org/apps/enrichmentmap) *Earlier versions may not work.*

## Install `netDx` and `netDx.examples`
This section assumes you have Java, Python, R and Bioconductor installed. From command-line, download the git repo for these packages and install them. In the code below, output from intermediate steps is omitted for clarity.

*Note: For now, R package dependencies must be separately installed using the install.packages() call as shown below. netDx will be submitted to CRAN following publication; thereafter, dependencies can be automatically installed with the call to install netDx.*

On Unix systems you may need one or more of these packages as these are dependencies for R packages:

```
sudo apt-get install zlib1g-dev libssl-dev libssh2-1-dev libcurl-devel
```

If you are on an RPM system you may be need to run this:
```
sudo apt-get install libcurl4-openssl-dev
```

Now we install the necessary R packages:
```
$ cd netDx-master/
$ R
> install.packages(c('devtools','curl'))
> install.packages(c("bigmemory","foreach","combinat","doParallel","ROCR","pracma","RColorBrewer","reshape2","ggplot2"))
> devtools::install_github("igraph/rigraph")  # install from CRAN has a bug and can fail (31 Aug 2017).
> devtools::install_github('cytoscape/cytoscape-automation/for-scripters/R/r2cytoscape')
> devtools::install_github('BaderLab/Easycyrest/EasycyRest@0.1')
> install.packages("netDx",type="source",repos=NULL)
> install.packages("netDx.examples",type="source",repos=NULL)
> install.packages("knitr") # needed to run examples
```
On Unix systems you may need to install the libraries below at command-line:
```
$ sudo apt-get install libssl-dev # for openssl & httr
$ sudo apt-get install libxml2-dev # for XML & r2cytoscape
```

```
>install.packages(c("openssl","httr","RJSONIO"))
>devtools::install_github('cytoscape/cytoscape-automation/for-scripters/R/r2cytoscape')
>devtools::install_github('BaderLab/Easycyrest/EasycyRest@0.1')
```

Note: On Unix systems, installing `httr` requires a prior install of the `openssl` package. If the `openssl` install fails with a message like: `ERROR: configuration failed for package 'openssl'`, you will need to install openssl for your system. e.g. for Debian and Ubuntu, install `libssl-dev`


<a name="mblastoma"></a>
## Test functionality
Run the medulloblastoma vignette to make sure the netDx pipeline works from end to end.
Each vignette is in Sweave format (`.Rnw`) . To run these, you need to have both `netDx` and `netDx.examples` installed. You will also need to install the R package `knitr` to compile the Sweave file.  If you have [Rstudio](https://www.rstudio.com/home/) installed (highly recommended), you should be able to open the `Rnw` file and click `Compile PDF`. Alternately, you may run the vignette through an interactive R session:

```
$ cd netDx-master/examples/
$ R
> require(knitr)
> knit2pdf("Medulloblastoma.Rnw")
```
This should generate `Medulloblastoma.pdf` in the `examples/` directory.

## Run BreastCancer LumA example
This vignette is presented in the netDx manuscript. Here we start with 348 primary tumours from the Cancer Genome Atlas, and build a predictor for Luminal A subtype classification (The Cancer Genome Atlas (2012). *Nature.* **490**:61-70).  This example illustrates  feature selection using a simple design in which networks are scored out of 10 based on a single round of 10-fold cross validation. On a MacBook Air laptop (late 2014), this vignette takes ~1.5 hours to run to completion. You may speed it up by running it on a machine with more processors and changing the `numCores` variable in the vignette. 

**We do not recommend running it on a Mac with less than 8Gb RAM. A Unix machine manages memory differently and may require as much as 32Gb RAM. If such a machine is not available, set `numCores=2L` in the `.Rnw` file before running.**

```
$ cd netDx/examples/
$ R
> require(knitr)
> knit2pdf("BreastCancer.Rnw")
```
**NOTE:** The vignette will generate a pdf file. All intermediate files will be stored in the `TCGA_BRCA/` subdirectory of the examples directory.

<a name="pdfissue"></a>
## Known issues with compiling pdfs

#### (Linux)
If you are getting an error saying that R cannot find `/usr/bin/texti2dvi`, install the `texinfo` and 'texlive' packages in linux. You can use the commands:
apt-get install texinfo
apt-get install texlive

#### (OS/X): "`pdfLaTex` not found" error
When compiling the pdf, you may get a message saying that `pdfLaTex` is not installed. We have had one such report on OS/X and it is known to occur after an upgrade to OS X Mavericks. The following steps resolved the issue (paraphrased from [this post](http://stackoverflow.com/questions/22081991/rmarkdown-pandoc-pdflatex-not-found)):
Step 1. Check that `/usr/texbin` exists. In terminal, type:
```
cd /usr/texbin
```
Step 2. If you get a "No such file or directory" message, create a symbolic link to your installation's `texbin` file. It may be in `/Library/TeX/Distributions/.DefaultTeX/Contents/Programs/texbin`.
In terminal type:
```
ln -s /Library/TeX/Distributions/.DefaultTeX/Contents/Programs/texbin /usr/texbin
```
Step 3. Now, in the terminal check the value of `echo $PATH`. Make sure that `/usr/texbin` is present. If it isn't present, then you need to add `/usr/texbin` to your PATH variable. This can be done by updating the `PATH` variable in `~/.bashrc`.
However, if you find yourself having to mess with the PATH variable, try reinstalling the [MacTex](http://tug.org/mactex/) package.

<a name="brca"></a>

<a name="other-examples"></a>
## Other examples
The `examples/` directory contains R notebooks (`.Rmd`) that teach basic functionality useful for any predictor building. These include plotting predictor results and running nested cross-validation, the design we recommend to use after feature design. 

The R notebooks must be run from within Rstudio. Install [Rstudio](https://www.rstudio.com/products/rstudio/download/) if necessary. 

We have also posted the results of running all the examples [here](http://netdx.org/index.php/examples/). 
