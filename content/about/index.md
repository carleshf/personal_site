---
title: "About"
date: 2021-04-17T21:59:10+02:00
draft: false
---

## Trajectory

I am a researcher at the [Analysis Grup](https://cnag.es/teams/bioinformatics-unit/data-analysis) (from Bioinformatics' Unit) from the [Centre Nacional d’Anàlisi Genòmica (CNAG-CRG)](https://www.cnag.crg.eu). I mainly work as a data and software engineer developing new software and improving our existing pipelines for genomic and genomic-related data analyses. I am also a collaborator [directing master thesis](http://carleshf.com/academic/#teaching) from the [MSc. in Bioinformatics & Biostatitics](https://estudios.uoc.edu/es/masters-universitarios/bioinformatica-bioestadistica/presentacion) at the [Universitat Oberta de Catalunya](https://uoc.edu).

{{< figure src="/img/portrait.png" caption="Welcome to CNAG-CRG" >}}

Before working at the Centre Nacional d’Anàlisi Genòmica, my research interests included the study of the relations between environmental factors and internal omic perturbations and development of applications to integrate multiple layers of molecular data. I have previously been at the Computational Health Informatics Program, located at [Boston Children’s Hospital](https://www.childrenshospital.org), and the [Pediatrics Department](https://connects.catalyst.harvard.edu/Profiles/display/Person/171981) of the Harvard Medical School as a research fellow elucidating the relationship between environmental factors and autism severity. Earlier, I performed my Ph.D. thesis at the [Bioinformatic Research Group in Epidemiology](http://brge.isglobal.org/) from [Barcelona Institute for Global Health](https://www.isglobal.org/), as well at [Universitat Pompeu Fabra](https://www.upf.edu/), depicting the impact of ultraviolet light (UV-A + UV-B) on human health, as well as working as a bioinformatic analyst studying the impact of multiple environmental factors on human internal molecular signatures (also known as omic data, including genome, methylome, transcriptome, proteome, and metabolome) within the framework of [HELIX | Building the early life exposome](https://www.projecthelix.eu).

Former turning into research, I studied my [BSc. Computer Engineering](https://www.uab.cat/web/estudiar/ehea-degrees/general-information/computer-engineering-1216708259085.html?param1=1263367146646) at [Universitat Autònoma de Barcelona](https://www.uab.cat/), as well as my [MSc. in Bioinformatics](http://mscbioinformatics.uab.cat), and worked as a computer engineer and as a software developer elsewhere.

## Contact

   * __E-mail__: carles.hernandez/@\cnag.crg.eu
   * __Location__: Centre Nacional d'Anàlisi Genòmica (CNAG-CRG); Parc Científic de Barcelona; Carrer de Baldiri Reixac, 4, 08028 Barcelona, Spain
 

{{< rawhtml >}}
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.7.1/dist/leaflet.css"
    integrity="sha512-xodZBNTC5n17Xt2atTPuE1HxjVMSvLVW9ocqUKLsCC5CXdbqCmblAshOMAS6/keqq/sMZMZ19scR4PsZChSR7A=="
    crossorigin=""/>
  <script src="https://unpkg.com/leaflet@1.7.1/dist/leaflet.js"
    integrity="sha512-XQoYMqMTK8LvdxXYG3nZ448hOEQiglfqkJs1NOQV44cWnUrBc8PkAOcXy20w0vlaXaVUearIOBhiXZ5V3ynxwA=="
    crossorigin=""></script>
  <style>
      #map { height: 180px; }
  </style>
  <div id="map"></div>
  <script>
    // initialize Leaflet
    var map = L.map('map').setView({lat: 41.381830, lon: 2.116485}, 16);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        maxZoom: 19,
        attribution: '&copy; <a href="https://openstreetmap.org/copyright">OpenStreetMap contributors</a>'
      }).addTo(map)
      L.control.scale().addTo(map)
      L.marker({lat: 41.381830, lon: 2.116485}).bindPopup("Centre Nacional d'Anàlisi Genòmica (CNAG-CRG)").addTo(map)
  </script>
{{< /rawhtml >}}