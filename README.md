# conos
## Clustering on Network of Samples
* What is Conos? 
It's a package to wire together large collections of single-cell RNA-seq datasets. It focuses on uniform mapping of homologous cell types across heterogeneous sample collections. For instance, a collection of dozens of peripheral blood samples from cancer patients, combined with dozens of controls. And perhaps also including samples of a related tissue, such as lymph nodes.

* How does it work? 
![overview](http://pklab.med.harvard.edu/peterk/conos/Figure1_take3.pk.png)
Conos applies one of many error-prone methods to align each pair of samples in a collection, establishing weighted inter-sample cell-to-cell links, creating a global joint graph. Cells of the same type will tend to map to each other across many such pair-wise comparisons, forming cliques, that can be recognized as clusters (graph communities). 

* What does it produce?
In essense, Conos will take a large, potentially heterogeneous panel of samples and will produce clustering grouping similar cell subpopulations togehter in a way that will be robust to inter-sample variation:
![example](http://pklab.med.harvard.edu/peterk/conos/bm_uniform_labels_trim.png)

* What are the advantages over existing alignment methods? 
Conos is robust to heterogeneity of samples within collection, as well as noise. The ability to resolve finer subpopulation structure improves as the size of the panel increases.

* What do I need to run it?
Conos is an R package. Currently, it supports pre-processing (filtering, normalization, etc.) of the individual datasets using [pagoda2](https://github.com/hms-dbmi/pagoda2) or [Seurat](https://satijalab.org/seurat/).

## Installation
Native installations have been tested in Linux. Normal installation should take <10min.
### Native installation
Please make sure devtools package is installed (use `install.packages("devtools")` to install it if needed).
Then install [pagoda2](https://github.com/hms-dbmi/pagoda2) (or Seurat), then install conos:
```r
devtools::install_github("hms-dbmi/conos")
```

#### System dependencies
The dependencies are inherited from [pagoda2](https://github.com/hms-dbmi/pagoda2):
##### Ubuntu Dependencies
Install system dependencies, example here provided for Ubuntu
```sh
sudo apt-get update
sudo apt-get -y install build-essential cmake gsl-bin libgsl0-dev libeigen3-dev libboost-all-dev libssl-dev libcurl4-openssl-dev libssl-dev libcairo2-dev libxt-dev libgtk2.0-dev libcairo2-dev xvfb xauth xfonts-base
```

##### Red-Hat-based distributions Dependencies
Was tested on AWS linux and Centos 7
```sh
yum install cairo-devel pango-devel libXt-devel openssl-devel gsl-devel boost-devel libcurl-devel
```
##### OS X
It is possible to install pagoda2 and Conos on OS X, however some users have reported issues with OpenMP configuration (see [pagoda2](https://github.com/hms-dbmi/pagoda2) readme).

## Installing Conos as Docker Container
If your system configuration is making it difficult to install Conos natively, an alternative way to get Conos running is through a docker container. 
### Ready-to-run docker image
The docker distribution is current as of October 2018 and also includes the (Pagoda2 package)[https://github.com/hms-dbmi/pagoda2]. To start a docker container, first [install docker](https://docs.docker.com/install/) on your platform and then start the pagoda container with the following command in the shell:

```
docker run -p 8787:8787 docker.io/barkasn/pagoda2
```
The first time you run the command it will download several images so make sure that you have fast internet access setup. You can then point your browser to http://localhost:8787/ to get an Rstudio environment with pagoda2 and conos installed (log in using credentials rstudio/pass). Explore the docker [--mount option]([https://docs.docker.com/storage/volumes/) to allow access of the docker image to your local files.
### Building docker image on the fly
If you want to run an up-to-date version of Conos (recommended), download the Dockerfile (available in this repo under /dockers) and run to following command to build it:
```
docker build -t conos .
```
This will create a "conos" docker image on your system (be patient, as the build takes ~10min or so).
You can then run it using the following command:
```
docker run -d -p 8787:8787 -e PASSWORD=pass --name conos -it conos
```

## Usage example
Please see [Conos tutorial](vignettes/walkthrough.md) for detailed usage. The overall runtime of the tutorial should be ~3minutes.

Given a list of individual processed samples (`pl`), Conos processing can be as simple as this:
```r
# construct conos object, where pl is a list of pagoda2 objects 
con <- Conos$new(pl)

# build graph
con$buildGraph()

# find communities
con$findCommunities()

# plot joint graph
con$plotGraph()

# plot panel with joint clustering results
con$plotPanel()
```
