---
title: "Analysis of a Permanent Magnet Synchronous Machine"
excerpt: "Analysis of a Permanent Magnet Synchronous Machine"
collection: portfolio
author_profile: false
classes: wide
mathjax: true
---
This is a summary of a project completed along with two fellow students at Lund University in 2016. The full paper can be found [here](/assets/papers/analysis-permanent-magnet.pdf), the paper is based on preparation assignments [1](/assets/papers/EIEN20-1.pdf), [2](/assets/papers/EIEN20-2.pdf), [3](/assets/papers/EIEN20-3.pdf), [4](/assets/papers/EIEN20-4.pdf), & [5](/assets/papers/EIEN20-5.pdf)  

## Introduction
This project was a technical feasability study on magnetic rotors, their qualities and restrictions. It was the first time I had encountered most of the mechanical terms, equations and methods laid out in the assignments for this module, including the Finite Element Method.  

Our aims for the project were to design a magnetic machine that would deliver enough torque for an electric bike application, while maintaining appropriate size, weight, and efficiency. This was to be achieved through:  

1. Calculation
2. Equivalent circuit analysis with MATLAB
3. Finite Element Method analysis with FEMM

## Initial Calculation
The first stage is to lay out some fundamental assumptions about the application of the machine. In this case it was to be used within a bicycle wheel; this imposes restrictions on size and weight. We also assumed a rider weight of 80Kg and bicycle weight of 20Kg with 0mph wind speed. We show that the required power and torque are dependent on the various forms of resistance.

$$Power=((Fdrag + Froll + Fgrav) * v)/0.8$$  

$$Torque = P/w$$

Input into MATLAB while varying inlination and speed gives the output shown by figure 1. 

<figure>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/p3-torque-requirement.jpg" alt="Bicycle speed dependence on inclination angle and torque">  
  <figcaption>Figure 1: Bicycle speed dependence on inclination angle and torque</figcaption>
</figure> 

## Equivalent Circuit
The output of the initial simulations is fed back into MATLAB using the equivalent circuit method to find the required machine sizes for different torque outputs. The output of these secondary simulations is shown in Figure 2.

<figure>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/p3-size-requirement.jpg" alt="Machine torque contour for width and diameter">  
  <figcaption>Figure 2: Machine torque contour for width and diameter</figcaption>
</figure> 

The equivalent circuit method is much less accurate than finite element but it helped us narrow down the choices for machine size. Our aim for power output is 250W, therefore it was judged to use a diameter of 90mm, width of 80mm going forward.  

After more equivalent circuit simulations it can be shown that the effect of the number of magnetic poles on the output power is *much* greater than size or magnetic air gap. Figure 3 shows the result of varying air gap and number of poles on output power for a 90mm by 80mm machine.  

<figure>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/p3-poles-power.jpg" alt="Machine power contour for poles and air gap">  
  <figcaption>Figure 3: Machine power contour for poles and air gap</figcaption>
</figure> 

It has been determined from the equivalent circuit simulations that power and number of poles are the most important factors as a gear can be used to adjust our torque if there is enough power. A new script is run to determine the optimal size for a 200W, 6 pole machine with a planetary gear. The output is shown in Figure 4, it has a diameter of 75mm and width of 47.5mm.

<figure>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/p3-ecm-output.jpg" alt="Equivalent circuit optimal machine with planetary gear">  
  <figcaption>Figure 4: Equivalent circuit optimal machine with planetary gear</figcaption>
</figure> 

## Finite Element Method

## Conclusion