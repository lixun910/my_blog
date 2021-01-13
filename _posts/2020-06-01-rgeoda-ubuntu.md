---
layout: post
title: 
date:   2020-06-01 13:50:39
categories: rgeoda development setup on Ubuntu Bionics
author: Xun Li
---

Here is my notes of the env, tools and package setup for rgeoda development:

# Install R

```bash
sudo apt-get update
sudo apt-get install r-base
```

The version is R 3.4 on Ubuntu Bionics

# Get rgeoda source code

```bash
git clone https://github.com/lixun910/rgeoda
```

# Setup and install dependent packages/libraries 

Use gcc-10 and g++-10, so the env is close to CRAN
```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt install gcc-10
sudo apt install g++-10
```

Used by package `roxygen2`, `sf`
```bash
sudo apt install libxml2-dev
sudo apt install libudunits2-dev
sudo apt install libgdal-dev
```

Used for creating pdf from man/doc

```bash
sudo apt-get install texlive-latex-base
sudo apt-get install texlive-fonts-recommended
sudo apt-get install texlive-fonts-extra
sudo apt-get install texinfo
```

Install R packages
```R
install.packages(c("roxygen2", "sf", "Rcpp", "Rmarkdown", "BH", "wkb"))
```

## NOTE 

Add ~/bin to PATH so R can find tex2pdf command