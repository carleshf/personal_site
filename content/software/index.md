---
title: "Software"
date: 2022-01-23T16:46:07+01:00
draft: false
---

The following is a detailed list of software I created or which I contributed in any way. The order follows my contribution time to them and not their release time.

#### Released

__`Treatabolome DB`__
 * __Short description__: Although next-generation sequencing drastically improved diagnosis for patients with rare diseases, access to knowledge of effective treatments is still sparse and often unclear. Treatabolome DB is a database of rare disease-specific treatments mapped at gene and variant level to speed upt the identification of existing therapies at the time of clinical diagnosis.
 * __Role__: Author (Transferred to [S Beltran and A Corvo](https://cnag.es/teams/bioinformatics-unit/data-analysis))
 * __Type__: Web application ([access](https://treatabolome.org/))
 * __Publication(s)__:
    - [G Bonne, J Neuromuscul Dis., 2021](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8203244/)
    - [A Atalaya; J Neuromuscul Dis.; 2021](https://pubmed.ncbi.nlm.nih.gov/33682723/)
    - [R Thompson;  Emerg Top Life Sci.; 2019](https://pubmed.ncbi.nlm.nih.gov/30931400/)

__RD-Connect Genome-Phenome Analysis Platform__ 
 * __Short description__: The "RD-Connect GPAP" was a result of the European Union FP7 funded RD-Connect project. It's goal is to accelerate rare disease research by connecting databases, patient registries, biobanks and clinical data through an intuitive user-friendly platform.
 * __Role__: Maintainer/Contributor
 * __Type__: Web application ([access](https://platform.rd-connect.eu/))
 * __Publication__: [L Matalonga; Eur. J. Hum. Genet.; 2021](https://www.nature.com/articles/s41431-021-00852-7)

__`WEScover`__
 * __Short description__: While whole exome sequencing is widely adopted in clinical and research settings, the potential false negatives due to incomplete breadth and depth of coverage for several exons in clinically implicated genes is a major concern. `WEScover` is a tool to quickly identify if all genes of interest would be reliably covered by WES or a targeted gene panel testing should be prioritized.
 * __Role__: Contributor
 * __Type__: Web application ([access](https://tom.tch.harvard.edu/shinyapps/WEScover/))
 * __Publication__: [IH Lee; BMC Bioinform.; 2021](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-021-04178-5)

__Clinical Genome and Ancestry Report__
 * __Short description__: The "CGAR" was developed to enables users with no knowledge on genomic annotation to identify and prioritize phenotype-associated variants from exome and genome sequencing data through a user-friendly online platform.
 * __Role__: Contributor
 * __Type__: Web application ([access](https://tom.tch.harvard.edu/apps/cgar/))
 * __Publication__: [IH Lee; Hum. Mutat.; 2019](https://onlinelibrary.wiley.com/doi/abs/10.1002/humu.23942)

__`rexposome` and `omicRexposome`__
 * __Short description__: The study of the high-dimensional exposures experienced across the life course (aka. exposome) offers a new paradigm for investigating environmental contributions to disease etiology. The R packages `rexposome` and `omicRexposome` filled the lack of tools for managing, visualizing and analyzing the exposome and its association with health outcomes and integration with molecular signatures.
 * __Role__: Author (Transferred to [JR Gonzalez](https://www.isglobal.org/en/our-team/-/profiles/18510))
 * __Type__: R package
    - Access to `rexposome` at [Bioconductor](https://www.bioconductor.org/packages/release/bioc/html/rexposome.html)
    - Access to `omicRexposome` at [Bioconductor](https://www.bioconductor.org/packages/release/bioc/html/omicRexposome.html)
 * __Publication(s)__:
    - [C Hernandez-Ferrer; Bioinformatics; 2019](https://academic.oup.com/bioinformatics/article/35/24/5344/5523848)
    - [L Maitre; Nat.Com.; 2020](https://www.nature.com/articles/s41467-022-34422-2)

__`psygenet2r`__
 * __Short description__: While psychiatric disorders have a great impact on morbidity and genotype–phenotype resources for psychiatric diseases are key to enable the translation of research findings to a better care of patients, the `psygenet2r` packages allows to integrate the data from PsyGeNET (a knowledge resource on psychiatric diseases and their genes) in downstream network-medicine analysis of psychiatric disorders.
 * __Role__: Author/Contributor
 * __Type__: R package - see `psygenet2r` at [Bioconductor](https://www.bioconductor.org/packages/release/bioc/html/psygenet2r.html)
 * __Publication__: [A Gutierrez-Sacristan; Bioinformatics; 2017](https://academic.oup.com/bioinformatics/article/33/24/4004/4083576)

__`MultiDataSet`__
 * __Short description__: `MultiDataSet` was released to supply the need for a data-structure in R to manage multiple layers of information for the same set of individuals. Latter on, it was extended as a resource to manage large amounts of multiple and non-complete biomedical-related data while offering a simple and general way of sub-setting features and selecting samples.
 * __Role__: Author (Transferred to [JR Gonzalez](https://www.isglobal.org/en/our-team/-/profiles/18510))
 * __Type__: R package - see `MultiDataSet` at [Bioconductor](https://www.bioconductor.org/packages/release/bioc/html/MultiDataSet.html)
 * __Publication__: [C Hernandez-Ferrer; BMC Bioinform.; 2017](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-016-1455-1)


#### In development

__`epimutacions`__
 * __Short description__: `epimutacions` (the name of the package is in Catalan) is a package that uses microarray DNA methylation data to identify epimutations, defined as a sequence of CpGs with aberrant methylation level but not enough to be discovered in case-control studies. The goal is to implement and collect already published methods, compute the burden of epimutations in a cohort of generally healthy population, and apply the method to accelerate rare disease diagnostic. 
 * __Role__: Contributor
 * __Type__: R package - see `epimutacions` at [Bioconductor](https://bioconductor.org/packages/release/bioc/html/epimutacions.html)
 * __Publication__: _in development_

#### Deprecated or discontinued

__`CTDquerier`__
 * __Short description__: `CTDquerier` was build to easily integrate data from the [Comparative Toxicogenomics Database](http://ctdbase.org/) in biomedical studies that included genomic and/or environmental factors for studying the etiology of human diseases.
 * __Role__: Author (Transferred to [JR Gonzalez](https://www.isglobal.org/en/our-team/-/profiles/18510))
 * __Type__: R package - see `CTDquerier` in [Bioconductor](https://www.bioconductor.org/packages/release/bioc/html/CTDquerier.html)
 * __Publication__: [C Hernandez-Ferrer; Bioinformatics; 2018](https://academic.oup.com/bioinformatics/article/34/18/3235/4983065)

__`affy2sv`__
 * __Short description__: The R package `affy2sv` was build in order to incorporate data from the most common Affymetrix SNP array to downstream R pipelines identifying different genomic aberrations like chromosomal inversion or copy number variants.
 * __Role__: Author (Transferred to [JR Gonzalez](https://www.isglobal.org/en/our-team/-/profiles/18510))
 * __Type__: R package - see it's source code in [github](https://github.com/isglobal-brge/affy2sv)
 * __Publication__: [C Hernandez-Ferrer; BMC Bioinform.; 2015](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-015-0608-y)


