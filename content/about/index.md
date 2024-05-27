---
title: "About"
date: 2021-04-17T21:59:10+02:00
draft: false
---

## Trajectory

I am a Senior Research Engineer at the [Barcelona Supercomputing Center (BSC)](www.bsc.es), where I primarily develop new infrastructure for European collaborative projects in biomedical sciences, such as ELIXIR. Additionally, I mentor and direct master’s theses for the [MSc. in Bioinformatics & Biostatitics](https://estudios.uoc.edu/es/masters-universitarios/bioinformatica-bioestadistica/presentacion) at the [Universitat Oberta de Catalunya (UOC)](https://uoc.edu) (see my [teaching activities](http://carleshf.com/academic/#formal-teaching)).

{{< figure src="/img/portrait.png" caption="Welcome to BSC's MareNostrum 4" >}}

### Previous Research Experience

Before my current role, my research interests focused on the relationships between environmental factors and internal omic perturbations, as well as the development of applications to integrate multiple layers of molecular data. Here are some highlights of my past positions:

* __Post-Doctoral Fellow__, at the [Centre Nacional d’Anàlisi Genòmica (CNAG)](https://cnag.es/), working on medical informatics for rare disease diagnostics.
* __Post-Doctoral Fellow__, at the [Computational Health Informatics Program](https://www.chip.org/) of [Boston Children’s Hospital](https://www.childrenshospital.org/) and the [Pediatrics Department](https://connects.catalyst.harvard.edu/Profiles/display/Person/171981) of [Harvard Medical School](https://hms.harvard.edu/), where I studied the relationship between environmental pollutants and autism.
* __Ph.D. Candidate__, at the [Bioinformatics Research Group in Epidemiology](http://brge.isglobal.org/) from the Barcelona [Institute for Global Health (ISGlobal)](https://www.isglobal.org/) and [Universitat Pompeu Fabra (UPF)](https://www.upf.edu/), researching the impact of ultraviolet light on human health and the effects of multiple environmental factors on human molecular signatures within the framework of the HELIX project.

### Academic and Professional Background

I hold a [B.Sc. in Computer Engineering](https://www.uab.cat/web/estudiar/ehea-degrees/general-information/computer-engineering-1216708259085.html?param1=1263367146646) from [Universitat Autònoma de Barcelona (UAB)](https://www.uab.cat/) and an [M.Sc. in Bioinformatics](http://mscbioinformatics.uab.cat/) from the same university.

My professional experience includes roles as a software engineer and data engineer at various institutions:

* At [Microsoft](https://www.microsoft.com/) ([Microsoft Rewards](https://www.microsoft.com/rewards)) improving the product's ETLs and designing the first recommendation system for the platform.
* At [Centre Nacional d’Anàlisi Genòmica (CNAG)](https://cnag.es/) improving the internal ETL for genomics data and developing a new analytical workflow.
* At [Institut de Física d’Altes Energies (IFAE)](https://www.ifae.es/) working for the [PAU](https://pausurvey.org/) project.
* At [Departament de Telecomunicació i d'Enginyeria de Sistemes](https://www.uab.cat/departament/telecomunicacio-enginyeria-sistemes/), from the UAB, developing the infrastructure for a new teaching methodology.

## Contact

* __E-mail__: carles.hernandez2/@\bsc.es
* __Location__: Barcelona Supercomputing Center (BSC) - Centro Nacional de Supercomputación (CNS); Plaça d'Eusebi Güell, 1-3, 08034 Barcelona

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
    var map = L.map('map').setView({lat: 41.389903, lon: 2.115419}, 16);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        maxZoom: 19,
        attribution: '&copy; <a href="https://openstreetmap.org/copyright">OpenStreetMap contributors</a>'
      }).addTo(map)
      L.control.scale().addTo(map)
      L.marker({lat: 41.389903, lon: 2.115419}).bindPopup("Barcelona Supercomputing Center").addTo(map)
  </script>
{{< /rawhtml >}}
