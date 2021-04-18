---
title: "Annotating KEGG compounds to pathway"
date: 2018-03-27T00:00:00+02:00
tags:
  - R
---


To annotate a list of [KEGG compounds](http://www.genome.jp/kegg/compound/) to the [KEGG pathways](http://www.genome.jp/kegg/pathway.html) where they are involved I used the R package [KEGGREST](https://bioconductor.org/packages/release/bioc/html/KEGGREST.html) from Bioconductor.

```R
  library(KEGGREST)
```

So, having a list of KEGG compounds saved in a character vector like `kegg_compounds`, we use the method `keggGet` in batches of maximum 10 compounds to annotate them.

The following (_rudimentary_) code, queries the database in batches of ten compounds fiddling a list (`pathways`) where it creates an entry per pathway and updates the field `compounds` with the compounds from `kegg_compounds` for each pathway.

```R
pathways <- list()
sequence <- seq(1, length(kegg_compounds), by=10)
for(ii in sequence) {
    jj <- ii + 9
    if(jj > length(kegg_compounds)) jj <- length(kegg_compounds)
    query <- keggGet(paste("cpd:", kegg_compounds[seq(ii, jj)]))
    message("Query / ", ii, " - ", jj, " / ", length(query))
    for(kk in seq(length(query))) {
        for(id in names(query[[kk]]$PATHWAY)) {
            if(id %in% names(pathways)) {
                pathways[[id]]$compounds <- unique(c(pathways[[id]]$compounds , as.character(query[[kk]]$ENTRY)))
            } else {
                pathways[[id]] <- list(name = as.character(query[[kk]]$PATHWAY[id]),
                    id = id, compounds = c(as.character(query[[kk]]$ENTRY))
                )
            }
        }
    }
}
```