---
title: "Capacitive Sensing Can"
excerpt: "Capacitive Sensing Can"
collection: portfolio
author_profile: false
classes: wide
mathjax: true
---

This is a short summary of a project taken as part of an analog electronics class taken by me and a colleague at Lund University in 2016. The full paper is [here](/assets/papers/capacitive-sensing-unit.pdf).  

## Background
The idea for our project came from the [Theremin](http://www.channelroadamps.com/articles/theremin/). We aimed to build a theremin type instrument as simply as possible, using drinks cans as sensors if possible. The easiest way to do this is by attaching a relaxation oscillator to a resistive (or inductive) and capacitive element. The equation for the state of a capacitor is $$i = Cdv/dt$$. The current, $$i$$ is known and to find $$dv/dt$$ we must measure the charging time of the capacitor. These two numbers are used to calculate C: The **Capacitance** of the object. The capacitance of a surface changes when some other object comes near to it. This is because everything has a capacitive field of some order which interact with eachother. Capacitive sensing is used in countless technologies to sense objects for this reason. Aluminium has a relatively high natural capacitance so drinks cans were a decent option, we hoped that our circuit would be sensitive enough to pick up our hands aswell.

## Design

### Relaxation Oscillator
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p7-oscillator-sch.PNG" alt="Relaxation Oscillator Schematic">  

The schematic above is our equivalent relaxation oscillator built in LTSpice for simulation. The power supply is pulsed every 50ms to simulate a finger touch. $$C1$$ is the capacitance of the finger or body which may be touching the circuit. Every 50ms $$C1$$ and $$R4$$ are engaged into the circuit which combine with $$Cprox$$ and $$R1-R3$$. LT1013 is a simple operational Amplifier that supplied by 5V which allows us to operate it as an XOR gate which is dependent on C1.

### FM Demodulator


## Testing