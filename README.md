# netDx
netDx is an algorithm for building patient classifiers by using patient similarity networks as features.

**Visit the "[wiki](https://github.com/BaderLab/netDx/wiki)" section for examples and related documentation**

This repo contains two R packages, each of which need to be separately installed:

1. `netDx/`: Software implementing the netDx method
2. `netDx.examples/`: Data for use cases

The `examples/` folder contains R code that should just run once both `netDx/` and `netDx.examples/` are installed.

The main website for netDx is **http://netdx.org**.

* [Installation](#installation)
  * [Prerequisites](#prerequisites)
  * [Install `netDx` and `netDx.examples`](#install-netdx-and-netdxexamples)
     * [Automated install](#automated-install) ***-- recommended***
     * [Manual install](#manual-install)     
* [Quick functionality check - Medulloblastoma tumour classifier](#test-functionality)
* [Build predictor](#build-predictor)
  * [See full list of examples](#other-examples)
* [All other examples](#other-examples)


**Other useful information:**
* [netDx methods paper](http://msb.embopress.org/content/15/3/e8497): Pai et al. (2019). netDx: Interpretable patient classification using integrated patient similarity networks. *Mol Sys Biol* **15**, e8497.
* Once you have run the included netDx examples, [read the user manual](http://netdx-manual.readthedocs.io/en/latest/) to learn how to design features or predictors. 

## Installation

### Prerequisites
* You must have [Java](https://java.com/en/), [Python](https://www.python.org/downloads/) and [R](https://www.r-project.org/) installed.
* Within R, you must have [BioConductor](https://bioconductor.org/install/) installed. 
* For visualization of result networks, (EnrichmentMap and integrated patient network), you must have [Cytoscape](https://cytoscape.org/) installed with the EnrichmentMap and AutoAnnotate apps.

See sections below to test if you have or need to install these dependencies. **If you do have these, [skip to the next section](#install-netdx-and-netdxexamples).**

**netDx has been tested on Mac OS/X and on Linux systems. For now we recommend you run netDx on these operating systems.** Future versions of netDx will have Windows support.

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
At command line, run `python --version`. You should see output like this:

```
Python 2.7.9 :: Anaconda 2.1.0 (x86_64)
```

#### R (3.6.0+ recommended)
At command line, run `R --version`. You should see output like this:
```
R version 3.6.0 (2019-04-26) -- "Planting of a Tree"
Copyright (C) 2019 The R Foundation for Statistical Computing
Platform: x86_64-apple-darwin15.6.0 (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see
https://www.gnu.org/licenses/.
```

If not, [install R](https://www.r-project.org/).

#### BioConductor (BioCManager 1.30.4+ recommended)
[BioConductor](http://bioconductor.org/) is a system of R objects and software specifically for biological applications.
Once you have R installed, install the `GenomicRanges` packages via the BioConductor installer:
```
$ R
> if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
> BiocManager::install()
```
Say `yes` to all dependencies that need to be installed.

#### Cytoscape (3.7+ recommended)
In order to generate and visualize patient similarity networks and enrichment maps from the data generated by netDx we will need Cytoscape. If Cytoscape is not located in your Applications directory, install the latest version from http://www.cytoscape.org.

With Cytoscape open, install the following Apps:
* AutoAnnotate v1.3 (http://apps.cytoscape.org/apps/autoannotate)  *Earlier versions may not work.*
* Enrichment Map v3.0.0 (http://apps.cytoscape.org/apps/enrichmentmap) *Earlier versions may not work.*

## Install `netDx` and `netDx.examples`
This section assumes you have Java, Python, R and Bioconductor installed.

### Automated install
Scripts are in the root directory of the downloaded release/repo.

On Unix systems (requires sudo privilege to install associated libraries), run:
```
$ ./INSTALL_Unix.sh
```

On Mac OS/X systems, run:
```
$ ./INSTALL_OSX.sh
```

If the scripts above don't work, install packages manually (next section). Your feedback is appreciated; email [Shraddha Pai](mailto:shraddha.pai@utoronto.ca) in case of problems with installation.

### Manual install
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
$ cd netDx/
$ R
> install.packages(c('devtools','curl'))
> install.packages(c("bigmemory","foreach","combinat","doParallel","ROCR","pracma","RColorBrewer","reshape2","ggplot2","tinytex","rmarkdown","caroline","glmnet","igraph","knitr"))
> BiocManager::install(c("GenomicRanges","RCy3"))
> install.packages("netDx",type="source",repos=NULL)
> install.packages("netDx.examples",type="source",repos=NULL)
```

On Unix systems you may need to install the libraries below at command-line:
```
$ sudo apt-get install libssl-dev # for openssl & httr
$ sudo apt-get install libxml2-dev xml2 # for XML & r2cytoscape
```

## Test functionality
Run the medulloblastoma vignette to make sure the netDx pipeline works from end to end.
Each vignette is in Rmarkdown format (`.Rmd`) . To run these, you need to have both `netDx` and `netDx.examples` installed. If you have [Rstudio](https://www.rstudio.com/home/) installed (highly recommended), you should be able to open the `Rmd` file and knit to html. Alternately, you may run the vignette through an interactive R session (requires [Pandoc](https://github.com/jgm/pandoc/)) :

```
$ cd netDx/examples/
$ R
> rmarkdown::render("Medulloblastoma.Rmd")
```
This should generate `Medulloblastoma.html` in the `examples/` directory and open the preview in your web browser.

## Build predictor
The `buildPredictor()` function builds a predictor by performing feature selection over a specified number of train/test splits. It identifies which features consistently have high predictive value, and computes performance of the model over the splits. Because this function automates all the predictor steps, ***`buildPredictor()` is a recommended starting point for predictor design, for most data types.*** 

The goal in this vignette is to predict poor or good survival for renal clear cell carcinoma, when provided with gene expression and CNV data. The example also illustrates pathway-level features. On a 2017 MacBook Air laptop, this vignette takes ~3 min to run to completion. Parameters have been set to small values for speed, and must be modified in practice. 

```
$ R
> rmarkdown::render("BuildPredictor.Rmd")
```
**NOTE:** The vignette will generate an html file which can be opened in a web browser. All intermediate files will be stored in the `pred_output/` subdirectory of the examples directory.


<a name="other-examples"></a>
## Other examples
The `examples/` directory contains R notebooks (`.Rmd`) that teach basic functionality useful for any predictor building. These include plotting predictor results and running nested cross-validation, the design we recommend to use after feature design. 

The R notebooks must be run from within Rstudio. Install [Rstudio](https://www.rstudio.com/products/rstudio/download/) if necessary. 

We have also posted the results of running all the examples [here](https://github.com/BaderLab/netDx/wiki/Examples). 
