---
layout: post
title: Automate the boring stuff using R Markdown
subtitle: Financial report of my condominium
gh-repo: cauancardoso/cauancardoso.github.io
gh-badge: [star, fork, follow]
tags: [R, R Markdown]
comments: true
---

This year, I've been responsible for the monthly financial report of the condominium where I live in Brasilia, which is a small condominium with eight units, including mine. Here I will show how I've automated this task using [**R Markdown**](https://rmarkdown.rstudio.com/) and [**R Studio**](https://www.rstudio.com/).

First things first, I'm using R version 3.6.0 and R Studio version 1.2.1335.

**R version:** 3.6.0  
**R Studio version:** 1.2.1335  
{: .box-note}

I'm also using [Basic Miktex](https://miktex.org/) (version 2.9) to generate my PDF documents. 

If you're experiencing trouble generating PDF documents in R Markdown using Miktex, [maybe this solution will work for you](https://tex.stackexchange.com/questions/27138/how-can-i-fix-the-error-gui-framework-cannot-be-initialized-with-texniccenter).  
{: .box-warning}

This is my YAML header:

```javascript
title: "Demonstração das Despesas do Condomínio"
lang: "pt-BR"
output:
  pdf_document: default
sansfont: Calibri Light
```
I use ```lang: "pt-BR ``` because I'm writing this document in Portuguese. This will be useful to translate the table caption (using [```kable``` function](https://www.rdocumentation.org/packages/knitr/versions/1.23/topics/kable)) into Portuguese. So, instead of "Table 1 - *text in Portuguese*", what I will have is "Tabela 1 - *text in Portuguese*". If you want to translate into another language, use a valid IETF language tags ([see the Pandoc Manual for more details on this](https://pandoc.org/MANUAL.html#language-variables)).

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
The second chunk has the R objects that will be visualized using the [```kableExtra``` package] (https://haozhu233.github.io/kableExtra/awesome_table_in_pdf.pdf). More details on them could be found in [my repository](https://github.com/cauancardoso/Condominio/blob/master/Condominio_script.R). It also creates two new objects: ```cent_sort``` (a vector randomly sorted) and ```cond_mes_ap``` (a row-wise [tibble](https://tibble.tidyverse.org/)).

```javascript
{r datasets, include=FALSE}

despesas <- fread(file = "Despesas.csv")
load("cond_mes.RData")
names(cond_mes) <- c("Despesa", "Valor (R$)")

cent_sort <- sample(c(.00, .01, .02, .03, .04, .05, .06), 7)
cond_mes_ap <- tribble(
  ~"Apartamento", ~"Valor (R$):",
  "Ap. 302", as.numeric(cond_mes[7,2]) + cent_sort[1],
  "Ap. 301", as.numeric(cond_mes[7,2]) + cent_sort[2],
  "Ap. 202", as.numeric(cond_mes[7,2]) + cent_sort[3],
  "Ap. 201", as.numeric(cond_mes[7,2]) + cent_sort[4],
  "Ap. 102", as.numeric(cond_mes[7,2]) + cent_sort[5],
  "Ap. 101", as.numeric(cond_mes[7,2]) + cent_sort[6],
  "Ap. 01", as.numeric(cond_mes[7,2]) + cent_sort[7]
)

```
The ```cent_sort``` has seven decimal numbers, from .00 to .06, that are added to the total amount each unit (apartments) has to pay on that month (```cond_mes_ap```). This works as a payment identifier and varies every month, as the order of the decimal numbers in the object randomly changes every time the document is knitted.

The text of the document below the title is written under this model
```javascript
\vspace*{2\baselineskip}

\hfill Brasília, `r format(Sys.Date(), "%d de %B de %Y")`

\vspace*{1\baselineskip}
Prezados,

\vspace*{1\baselineskip} 
Solicita-se a todos os condôminos/moradores o depósito em **conta poupança** 
(banco: **XXX**; titular: **XXX**; CPF: **XXX**; agência: **XXX**; 
conta poupança: **XXX**) para pagamento das despesas do condomínio referentes 
ao mês de **`r format(seq.Date(Sys.Date(), length=2, by='-1 month')[2], "%B/%y")`**, conforme as tabelas 1 e 2.

```

The ```\vspace*{2\baselineskip}``` skips two extra lines between the title and the date.

The ```\hfill``` right align the date in the document.

The ``` `r format(Sys.Date(), "%d de %B de %Y")` ``` automatically generates the date for the document based on the day it is created.

The ```\vspace*{1\baselineskip}``` skips one extra line between the salutation and the date and between the salutation and the rest of the document.

The ``` `r format(seq.Date(Sys.Date(), length=2, by='-1 month')[2], "%B/%y")` ``` automatically generates the reference month (that is always the month before) of the financial report.

This is the result I get in my document:

![example of line break using the codes above](img/Example_linebreak.png)

The last two chunks are the tables created for this document using the [```kableExtra``` package] (https://haozhu233.github.io/kableExtra/awesome_table_in_pdf.pdf). As I don't know how to break a line in the footnote, I make each line as a different footnote to achieve the expected result.

```javascript
{r tabela_1, echo=FALSE}

kable(cond_mes, format = "latex", booktabs = T,
      caption = paste("Demonstração das despesas do condomínio referente ao mês de",
      format(seq.Date(Sys.Date(), length=2, by='-1 month')[2],"%B/%Y"),
      sep = " ")) %>%
  kable_styling(latex_options = "hold_position", full_width = F) %>%
  pack_rows("Manutenção predial", 1, 4) %>%
  pack_rows("Caixa", 5, 5) %>%
  pack_rows("Resumo - Subtotal por apartamento", 8, 9) %>%
  row_spec(6, bold = T) %>%
  row_spec(7, bold = T)
  
```

```javascript
{r tabela_2, echo=FALSE}
kable(cond_mes_ap, format = "latex", booktabs = T, 
      caption = paste0("Valor a ser pago do condomínio referente ao mês de ",
                       format(seq.Date(Sys.Date(), 
                                       length=2, 
                                       by='-1 month')[2],
                              "%B/%Y"),
                       ", por apartamento")
       ) %>%
  kable_styling(latex_options = c("hold_position","striped"), full_width = F) %>%
  footnote(general = c("A diferença em centavos varia mês a mês e é utilizada", 
           "para verificação e aprovação dos depósitos em conta."),
           general_title = "Nota:")

```
Last but not least, I put the due date (which is every 10th of the month) and the respective weekday. I've made some mistakes before I automated this, but now I always get it right using this two lines of code:

```javascript
paste("10", format(Sys.Date(), "%m"), sep = "/") # due date
weekdays(
         as.Date(
                 paste("10", format(Sys.Date(), "%m/%y"), 
                       sep = "/"), 
                 format = "%d/%m/%y")
         ) # due date weekday
```
And that's about it! [Check it out in my repository to see more details](https://github.com/cauancardoso/Condominio) and [click here to see the final document!](https://github.com/cauancardoso/Condominio/blob/master/Condominio_mes.pdf).
