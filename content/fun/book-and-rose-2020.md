---
title: "St. Jordi 2020 (Barcelona + Pandemic Ed.)"
date: 2020-04-23T00:00:00+02:00
draft: false
tags:
  - St. Jordi
---

As I did last year (fun-post [here]({{< ref "/fun/book-and-rose-2019.md" >}})), I come again with a digital rose for my wife. This time, a pure-CSS rose.

{{< rawhtml >}}
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sacramento">
    <style>
        .container2 {
            height: 100px;
            width: 100px;
            position: relative;
            top: 60px;
            bottom: 60px;
            left: 0;
            right: 0;
            margin: auto;
        }
        .caption2 {
            height: 100px;
            width: 500px;
            position: relative;
            top: 125px;
            bottom: 0;
            left: 0;
            right: 0;
            margin: auto;
            font-family: 'Sacramento', serif;
            font-size: 48px;
        }
        .subcaption2 {
            height: 100px;
            width: 500px;
            position: relative;
            top: 100px;
            bottom: 50px;
            left: 45%;
            right: 0;
            margin: auto;
            font-family: 'Sacramento', serif;
            font-size: 23px;
        }
        .droplet {
            border-radius: 5px 90%;
            border-style: solid;
            border-width: 5px;
            border-color: white;
            height: 100px;
            margin: auto;
            width: 100px;
            position: absolute;
            top: 50px;
            left: 50px;
            transform-origin: left top;
            opacity: 0;
        }

        #no1 {
            background: red;
            animation: spin 5s linear 2s infinite alternate;
            -webkit-transform: rotate(324deg) scale(1);
            transform: rotate(324deg) scale(1);
        }
        #no2 {
            background: lightpink;
            opacity: 100;
            -webkit-transform: rotate(288deg) scale(1);
            transform: rotate(288deg) scale(1);
        }
        #no3 {
            background: red;
            animation: spin 7s linear 0s infinite alternate;
            -webkit-transform: rotate(252deg) scale(1);
            transform: rotate(252deg) scale(1);
        }

        #no4 {
            background: lightpink;
            opacity: 100;
            -webkit-transform: rotate(216deg) scale(1);
            transform: rotate(216deg) scale(1);
        }

        #no5 {
            background: red;
            animation: spin 6s linear 1s infinite alternate;
            -webkit-transform: rotate(180deg) scale(1);
            transform: rotate(180deg) scale(1);
        }

        #no6 {
            background: lightpink;
            opacity: 100;
            -webkit-transform: rotate(144deg) scale(1);
            transform: rotate(144deg) scale(1);
        }

        #no7 {
            background: red;
            animation: spin 7s linear 0s infinite alternate;
            -webkit-transform: rotate(108deg) scale(1);
            transform: rotate(108deg) scale(1);
        }

        #no8 {
            background: lightpink;
            opacity: 100;
            -webkit-transform: rotate(72deg) scale(1);
            transform: rotate(72deg) scale(1);
        }

        #no9 {
            background: red;
            animation: spin 5s linear 2s infinite alternate;
            -webkit-transform: rotate(36deg) scale(1);
            transform: rotate(36deg) scale(1);
        }

        #no10 {
            background: lightpink;
            opacity: 100;
            -webkit-transform: rotate(0deg) scale(1);
            transform: rotate(0deg) scale(1);
        }


        @keyframes spin {
            0%   { opacity:   0; }
            70%  { opacity:  50; }
            100% { opacity: 100; }
        }

    </style>
    <div class="container2">
        <div class="droplet" id="no1"></div>
        <div class="droplet" id="no2"></div>
        <div class="droplet" id="no3"></div>
        <div class="droplet" id="no4"></div>
        <div class="droplet" id="no5"></div>
        <div class="droplet" id="no6"></div>
        <div class="droplet" id="no7"></div>
        <div class="droplet" id="no8"></div>
        <div class="droplet" id="no9"></div>
        <div class="droplet" id="no10"></div>
    </div>
    <div class="caption2">Feliç Sant Jordi</div>
    <div class="subcaption2">Barcelona 2020</div>
{{< /rawhtml>}}

More information on this festivity:
 * [Sant Jordi - Day of the Book and the Rose](https://www.barcelona.de/en/barcelona-sant-jordi.html): Explanation of this festivity in Catalonia by Barcelona's City council.
 * [Wikpiedia Saint George's Day (Spain)](https://en.wikipedia.org/wiki/Saint_George%27s_Day_(Spain)): Short explanation of this festivity in Aragon, Catalonia, and Valencia.