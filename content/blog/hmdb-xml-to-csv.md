---
title: "Exploiting XML data from Human Metabolome Data Base"
date: 2018-09-18T00:00:00+02:00
draft: false
tags:
  - python
---

I am currently working on a project where I need to annotate exogenous chemicals measured in plasma using GC-MS/MS to the Human Metabolome Data Base ([HMDB](http://www.hmdb.ca)) and to the Toxin and Toxin Target Database ([T3DB](http://www.t3db.ca)).

T3DB offers a series of downloadable resources that can be easy integrated into R as a CSV file after being parsed using bash. On the other hand, HMDB only offers XML sources that are to heavy to be parsed using the `XML` R package in my laptop. 

In my case I was interested in obtaining the chemical class of each exogenous chemical as well as their sources and routes of exposure.

The following is the python code I used to parse the XML file and create a CSV file with the information I required for my project.

```python
    #!/usr/bin/env python3
    # -*- coding: utf-8 -*-
    
    import csv
    from lxml import etree
    from functools import reduce
    
    # The input file, "hmdb.xml", is the original file, "hmdb_metabolites.xml", but without the URL attribute in the hmdb tag.
    xml = 'hmdb.xml'
    context = etree.iterparse( xml, tag = 'metabolite' )
    
    csvfile = open( 'hmdb.csv', 'w' )
    fieldnames = [ 'accession', 'cas', 'name', 'kingdom', 'super_class', 'class', 'sub_class', 'source', 'route_exposure', 'synonyms' ]
    writer = csv.DictWriter( csvfile, fieldnames = fieldnames )
    writer.writeheader()
    
    for event, elm in context:
        accession = elm.xpath( 'accession/text()' )[ 0 ]
        name = elm.xpath( 'name/text()' )[ 0 ]
        try:
            cas = elm.xpath( 'cas_registry_number/text()' )[ 0 ]
        except:
            cas = 'NA'
        try:
            kingdom = elm.xpath( 'taxonomy/kingdom/text()' )[ 0 ]
        except:
            kingdom = 'NA'
        try:
            chem_super_class = elm.xpath( 'taxonomy/super_class/text()' )[ 0 ]
        except:
            chem_super_class = 'NA'
        try:
            chem_class = elm.xpath( 'taxonomy/class/text()' )[ 0 ]
        except:
            chem_class = 'NA'
        try:
            chem_sub_class = elm.xpath( 'taxonomy/sub_class/text()' )[ 0 ]
        except:
            chem_sub_class = 'NA'
        
        try:
            synonyms = elm.xpath( 'synonyms/synonym/text()' )
            synonyms = reduce( lambda x, y: x + '//' + y, synonyms )
        except:
            synonyms = 'NA'
    
        ontology_terms = elm.xpath( 'ontology/root/term' )
        ontology_terms = [ term.text for term in ontology_terms ]
        if 'Disposition' in ontology_terms:
            disposition_terms = []
            role = []
            for idx, term in enumerate( ontology_terms ):
                if term == 'Disposition':
                    disposition = elm.xpath( 'ontology/root/descendants' )[ idx ]
                    disposition_terms = [ term.text for term in disposition.xpath( 'descendant/term' ) ]
                if term == 'Role':
                    role = elm.xpath( 'ontology/root/descendants' )[ idx ]
            source = []
            if 'Source' in disposition_terms:
                for idx, term in enumerate( disposition_terms ):
                    if term == 'Source':
                        source = disposition.xpath( 'descendant' )[ idx ].xpath( 'descendants/descendant/term/text()' )
                        break
            route_exposure = []
            if 'Route of exposure' in disposition_terms:
                for idx, term in enumerate( disposition_terms ):
                    if term == 'Route of exposure':
                        route_exposure = disposition.xpath( 'descendant' )[ idx ].xpath( 'descendants/descendant/descendants/descendant/term/text()' )
                        break
            if len( source ) > 0:
                source = reduce( lambda x, y: x + "//" + y, source )
            else:
                source = 'NA'
            if len( route_exposure ) > 0:
                route_exposure = reduce( lambda x, y: x + "//" + y, route_exposure )
            else:
                route_exposure = 'NA'
        else:
                route_exposure = 'NA'
                source = 'NA'
    
        writer.writerow( { 'accession': accession, 'cas': cas, 'name': name, 'kingdom': kingdom,
            'super_class': chem_super_class, 'class': chem_class,
            'sub_class': chem_sub_class, 'source': source,
            'route_exposure': route_exposure, 'synonyms': synonyms } )
    
        elm.clear()
        for ancestor in elm.xpath( 'ancestor-or-self::*' ):
            while ancestor.getprevious() is not None:
                del ancestor.getparent()[ 0 ]
    
    del context
```
