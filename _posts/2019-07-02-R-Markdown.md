---
layout: post
title: Automating boring stuff with R Markdown
subtitle: Financial report of my condominium
gh-repo: cauancardoso/cauancardoso.github.io
gh-badge: [star, fork, follow]
tags: [R, R Markdown]
comments: true
---

This year, I've been responsible for the monthly financial report of the condominium where I live in Brasilia. Here I will show how I've automated this task using [**R Markdown**](https://rmarkdown.rstudio.com/) and [**R Studio**](https://www.rstudio.com/).

First things first, I'm using R version 3.6.0 and R Studio version 1.2.1335.

**R version:** 3.6.0  
**R Studio version:** 1.2.1335  
{: .box-note}

I'm also using [Basic Miktex](https://miktex.org/) (version 2.9) to generate my PDF documents. 

If you're experiencing trouble generating PDF documents in R Markdown using Miktex, [maybe this solution will work for you](https://tex.stackexchange.com/questions/27138/how-can-i-fix-the-error-gui-framework-cannot-be-initialized-with-texniccenter).  
{: .box-warning}

My YAML header looks something like this

```javascript
title: "Demonstração das Despesas do Condomínio"
lang: "pt-BR"
output:
  pdf_document: default
sansfont: Calibri Light
```
The ```lang: "pt-BR ``` will translate the document into Portuguese. This will be useful to translate the table caption (using kable) "Table 1" and "Table 2" to "Tabela 1" and "Tabela 2", respectively. If you want to translate into other language, use a valid IETF language tags ([see the Pandoc Manual for more details on this](https://pandoc.org/MANUAL.html#language-variables))

The first chunk has the libraries used

```javascript
{r libraries, include=FALSE}

library(data.table)
library(tidyr)
library(dplyr)
library(lubridate)
library(knitr)
library(kableExtra)

```
The second chunk has the datasets 
