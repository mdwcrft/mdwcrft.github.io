---
title: "Embedded Power Supply Control Card"
excerpt: "Embedded Power Supply Control Card"
collection: portfolio
classes: wide
---

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1.PNG" alt="Some alt text">

---

This project was taken while working on the [Joint European Torus](https://en.wikipedia.org/wiki/Joint_European_Torus), the worlds largest nuclear fusion device. Creating [fusion power](https://en.wikipedia.org/wiki/Fusion_power) requires a huge amount of energy and most of this is supplied by [Neutral Beam Injectors](https://en.wikipedia.org/wiki/Neutral_beam_injection). Neutral beams are created by accelerating positive Hydrogen ions towards a neutralising gas. The neutral particles then continue their path into the plasma, adding energy by collision. To get the particles to high enough speeds they are accelerated towards a grid which is supplied by a high voltage power supply (~130kV).  

These power supplies must be tested before operations by pulsing them onto a 'dummy load'. The aim of this project was to design, test, and procure a PCB that would be installed into the supply and would carry out the dummy load test at the press of a button.  


Due to the voltages involved the supply is required to charge up a DC link before the full pulse. The timing should look like this: