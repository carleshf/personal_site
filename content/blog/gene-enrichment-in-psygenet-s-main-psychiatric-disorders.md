---
title: "Gene-Enrichment in PsyGeNET's Main-Psychiatric-Disorders"
date: 2015-01-06T00:00:00+02:00
draft: false
tags:
  - R
---

[PsyGeNET](http://psygenet.org) is a database that integrates information on psychiatric disorders and their genes (check its *About* page for more information). The current version of the database centered the information of three main psychiatric disorders: **Alcoholism**, **Depression** and **Cocaine-Related-Disorders**.

Currently the author of **PsyGeNET**, [Alba Gutiérrez](http://dbmi.hms.harvard.edu/person/postdoctoral-fellows/alba-gutierrez), and me are developing an R package (`PsyGeNET2R`) to query the information stored into the database and to perform some analysis using this information. We thought that could be a good idea to perform an enrichment analysis on the three main psychiatric disorders given a list of genes of interest.

Using the **PsyGeNET**'s browser we can get the following code to download the stored data:

```
library(RCurl)
oql <- "DEFINE
    c0='/data/main',
    c1='/data/genes',
    c2='/data/diseases',
    c3='/data/datasources'
  ON
   'http://www.psygenet.org/web/PsyGeNET'
  SELECT
    c1 (Gene_Symbol, Gene_Id, Gene_Description),
    c2 (Disease_Id, Disease_code, DiseaseName, PsychiatricDisorder),
    c0 (Score, Number_of_Abstracts)
  FROM
    c0
  WHERE
    c3 ='ALL'
  ORDER BY
    c0.Score DESC";

dataTsv <- getURLContent("http://www.psygenet.org/oql", readfunction = charToRaw(oql), upload = TRUE, customrequest = "POST")
data <- read.csv(textConnection(dataTsv), header = TRUE, sep=";")
```

The `data.frame` called `data` corresponds to a generalization of the information stored into **PsyGeNET**:

```
> colnames(data)
[1] "c1.Gene_Symbol"         "c1.Gene_Id"             "c1.Gene_Description"
[4] "c2.Disease_Id2"         "c2.Disease_code"        "c2.DiseaseName"
[7] "c2.PsychiatricDisorder" "c0.Score"               "c0.Number_of_Abstracts"
```

The following tables shows the content of the first three rows of **data**:

```
	c1.Gene_Symbol 	c1.Gene_Id 	c1.Gene_Description 	                                c2.Disease_Id
1 	NPY 	        4852 	    neuropeptide Y 	                                        umls:C0001973
2 	ADH1B 	        125 	    alcohol dehydrogenase 1B (class I), beta polypeptide 	umls:C0001973
3 	ADH1C 	        126 	    alcohol dehydrogenase 1C (class I), gamma polypeptide 	umls:C0001973

c2.Disease_code 	c2.DiseaseName 	c2.PsychiatricDisorder 	c0.Score
1 	C0001973 	Alcoholism 	Alcoholism 	0.9613497
2 	C0001973 	Alcoholism 	Alcoholism 	0.9000000
3 	C0001973 	Alcoholism 	Alcoholism 	0.9000000

	c0.Number_of_Abstracts
1 	21
2 	82
3 	37
```

From the vignette *"PsyGeNET2R: Case study with genes from GWAS study related to bipolar disorder"* that we are writting, I extracted a list of genes of interest:

```
genesOfInterest <- c( "ADCY2", "FAM155A", "MSI2", "AKAP13",
  "FLJ16124", "NFIX", "SIPA1L2", "SNX8", "ANK3", "FSTL5",
  "NGF", "SPERT", "ANKS1A", "GATA5", "NPAS3", "STK39", "ATP6V1G3",
  "GNA14", "ODZ4", "SYNE1", "ATXN1", "GPR81", "PAPOLG", "THSD7A",
  "C11orf80", "HHAT", "PAX1", "TNR", "C15orf53", "IFI44", "PBRM1",
  "TRANK1", "CACNA1C", "ITIH3", "PTPRE", "TRIM9", "CACNA1D", "KDM5B",
  "PTPRT", "UBE2E3", "CACNB3", "KIF1A", "RASIP1", "UBR1", "CROT",
  "LOC150197", "RIMBP2", "ZMIZ1", "DLG2", "MAD1L1", "RXRG", "ZNF274",
  "DNAJB4", "MAPK10", "SGCG", "DUSP22", "MCM9", "SH3PXD2A"
)
```

But from the total of 58 genes, only 21 are in **PsyGeNET** today (January 06th, 2015):

```
> length(genesOfInterest)
[1] 58
> sum(genesOfInterest %in% data$c1.Gene_Symbol)
[1] 21
> sel <- genesOfInterest[genesOfInterest %in% data$c1.Gene_Symbol]
> sel
 [1] "MSI2"     "NFIX"     "ANK3"     "NGF"      "ANKS1A"   "NPAS3"
 [7] "SYNE1"    "THSD7A"   "C11orf80" "C15orf53" "PBRM1"    "CACNA1C"
[13] "ITIH3"    "PTPRE"    "PTPRT"    "CACNB3"   "RASIP1"   "ZMIZ1"
[19] "DLG2"     "MAD1L1"   "SGCG"
```

From the data we can build the following table:

|           | Depression | ¬Depression | Total |
|:----------|-----------:|------------:|------:|
|selection  |  17        | 21 – 17     |       |
|background | 877 	     | 1317 – 877  |       |
|total      | 877        | 440         | 1317  |

All the data come from:

```
> data.dep = data[data$c2.PsychiatricDisorder == "Depression", ]
> x <- sum(genesOfInterest %in% data$c1.Gene_Symbol)
> n <- sum(sel %in% data.dep$c1.Gene_Symbol)
> t <- length(unique(data.dep$c1.Gene_Symbol))
> T <- length(unique(data$c1.Gene_Symbol))
> x
[1] 21
> n
[1] 17
> t
[1] 877
> T
[1] 1317
```

To end, we can say that those selected genes are not enriched in "Depression":

```
> phyper(n - 1, t, T - t, x, lower.tail = FALSE)
[1] 0.1177713
```

