---
title: "Embedded Power Supply Control Card"
excerpt: "Embedded Power Supply Control Card"
collection: portfolio
classes: wide
---

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1-cover.PNG" alt="Some alt text">

---

#### Background

This project was taken while working on the [Joint European Torus](https://en.wikipedia.org/wiki/Joint_European_Torus), the worlds largest nuclear fusion device. Creating [fusion power](https://en.wikipedia.org/wiki/Fusion_power) requires a huge amount of energy and most of this is supplied by [Neutral Beam Injectors](https://en.wikipedia.org/wiki/Neutral_beam_injection). Neutral beams are created by accelerating positive Hydrogen ions towards a neutralising gas. The neutral particles then continue their path into the plasma, adding energy by collision. To get the particles to high enough speeds they are accelerated towards a grid (Grid 1) which is supplied by a high voltage power supply (~130kV).  

Grid 1 power supplies must be tested before operations by pulsing them onto a 'dummy load'. The aim of this project was to design, test, and procure a PCB that would be installed into the supply and would carry out the dummy load test at the press of a button. This should be implemented using Fibre Optic transmitters and include a 5kHz safety signal that is constant. If the safety signal is lost or deviates in frequency then the supply will not pulse. 

Due to the voltages involved the supply is also required to charge up a DC link before the full pulse and decharge it after. The output requirements are summarised as follows.

|Output|Description|
|----------|----------|
|G1 Enable|Enable DC link for 13 seconds|
|G1 On|Wait 5 seconds, then pulse supply for 7 seconds|
|FBIS|Constant 5kHz safety signal|
|Output 4|Optional manual output|

The timing should look like this:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1-timing.jpg" alt="Pulse Timing Graph">

#### Design
The cubicle that will host the card has an 18V supply rail that will be used to power the card. The first step is to convert from +18V to +5V using a voltage regulator and to smooth the input with decoupling capacitors. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1-regulation.PNG" alt="Input Power Regulation">

The basic switched fibre transmitter is operated via a MOSFET (BS170) with a gate connected to the control source. There is also a status LED, two decoupling capacitors, and an input resistor to maintain correct current. The simplest set up is output 4, the manual switched output.
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1-manual.PNG" alt="Manual Switched Output">

The other transmitters have the same peripheral hardware, each with a different control source connected to the MOSFET. For the safety signal this is an astable 555 timer, set up to output a constant 5kHz oscillation. 
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1-FBIS.PNG" alt="5Khz Safety Signal Output">

The control source for the main outputs, G1 Enable and G1 On, is an MCU ([STM32F401RE](https://www.st.com/en/microcontrollers/stm32f401re.html)). The MCU is connected to power through decoupling capacitors and is triggered by a push button. There are also various connections for USB and JTAG to enable flexibility in programming of the final product.
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1-main.PNG" alt="G1 En & G1 On Output">

The code for the MCU was written in C++ using the ARM MBED environment. 
```cpp
#include "mbed.h"

// Declare pins
InterruptIn button(PB_6);  
DigitalOut G1En (PA_6);
DigitalOut G1On (PA_7);

// Button function
void pressed()
{
    G1En = 1; 
    wait(5.0);
    G1On = 1;
    wait(8.0);
    G1On = 0;
    wait(1.0);
    G1En = 0;
}

int main() {
    button.mode(PullUp);   // Set an internal pullup resistor for button
    button.fall(&pressed); // Trigger pressed function on falling edge
    while (1) {}           // Do nothing and wait for interrupt
}
```
This program is very simple, it takes the following actions in order:
1.) Define pins to use
2.) Define timing sequence for outputs
3.) Set internal pull-up resistor for button
4.) Run sequence when button pressed
5.) Wait for new press

#### Manufacture
Once the program was sufficiently tested on the development board ([Nucleo-F401RE](https://www.st.com/en/evaluation-tools/nucleo-f401re.html)) a full prototype board was assembled. 
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p1-prototype.jpg" alt="Prototype board">

This was fully tested on the plant and operated as expected. The next stage was to design a PCB layout in Proteus, to be sent out for procurement.

