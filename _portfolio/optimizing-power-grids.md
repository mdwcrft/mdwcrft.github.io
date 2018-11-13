---
title: "Optimizing Power Grids for Rural Rwanda"
excerpt: "Optimizing Power Grids for Rural Rwanda"
collection: portfolio
author_profile: false
classes: wide
---
This is a summary of my 2017 degree dissertation, the full paper can be found [here](/assets/papers/Power-Grid-Architectures-for-Sub-Saharan-Africa.pdf).

## Background

Sub-Saharan Africa, broadly defined as the total area within and south of the Sahara Desert, has by far the lowest levels of electricity access of any region in the world.

<figure style="width: 600px" class="align-right">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/p2-electrification.png" alt="Rates of Electricity Access in the Developing World"> 
</figure> 
 
This problem is even more severe for rural communities that are outside the reach of national or international grid systems. There are many reasons for this particular inequality; politics, infrastructure, and geography all play a part. One factor unique to Sub-Saharan Africa, compared to other low-income region, is the incredibly low population density (~45 people per sq. km compared to ~375 for South Asia) [source](https://data.worldbank.org/indicator/EN.POP.DNST?locations=ZG-8S-Z4). Transporting energy costs money and with a sparse rural population it just isn't cost effective to extend High Voltage power grids. 

The geographic and social reasons for the state of Sub-Sahran Africa are vital to understanding and solving the shortage of electricity. However, the aim of this paper is only to explore engineering solutions, with the aim of decreasing the cost burden for governments and private investors in the African energy system. Areas with significant potential for improvement are energy efficiency, demand management, generation planning, and costing schemes. Specifically, this study will aim to optimize generation planning, and demand management for a variety of micro-grids and analyse any cost benefits. The main tool used is HOMER Pro 3.7 which is an energy grid simulator particularly suited to micro grids and renewable energy sources.  

The aims and objectives are:  

1. Serve village sized community with variable energy requirements
  - Supply at least 600 people
  - Supply enough power for conventional white goods (Refrigerator, freezer, etc.) defined as 2kWh/day/household
  - Supply both residential and commercial loads with realistic load profiles
2. Achieve a low cost that is in line with regional income.
  - Net present cost (NPC) of less than $200,000 for whole system
  - Levelised cost of energy (LCOE) of less than $0.3/kWh
3. Achieve a stable supply with high efficiency and power quality
  - Load connection at 230V 50Hz AC
  - Load factor of over 40%
  - Unmet load of less than 10%
4. Analysis and selection of optimal storage solution
  - Literature review of low cost energy storage solutions
  - Optimization of storage technology in HOMER to determine size
5. Analysis and selection of optimal control and dispatch methods
  - Literature review of control methods
  - Analyse qualities of grid for control selection
  - Selection of low cost control method directly deployable within existing system structure
6. Minimal carbon dioxide emissions
  - Purely renewable energy sources
  - Locally sourced materials where available

<iframe src="https://www.google.com/maps/d/u/0/embed?mid=1ViAzRYH8d6MTjHuasridF5F95Fo" width="640" height="480"></iframe>