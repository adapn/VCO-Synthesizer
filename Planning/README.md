# Voltage Controlled Oscillator Synthesizer

## Introduction

Welcome to the planning stage of the Voltage Controlled Oscillator Breadboard Synthesizer project! This page aims to document my learning and findings throughout the design phase. I wanted to capture my thought process and the questions I had while working on this project. Many online resources skip the fundamentals, which I believe are crucial to any electronic design project but often overlooked.

## Table of Contents

1. [Power Supply](#power-supply)
2. [Voltage Controlled Oscillator (VCO)](#voltage-controlled-oscillator-vco)

---

## Power Supply

Q1. How much voltage do we need for the synth? How many Amps?
- Based on forum posts and standard synth schematics, it's ±12V and 1A. There are also schematics for VCOs, VCFs, and VCAs that use 15V, but it seems pretty easy to translate into working with ±12V.
- Apparently, Eurorack is a popular synth that seems to run on 12V. 
- By ±12V, we mean a dual supply which has a virtual ground.
  
Q2. How do we achieve this voltage?
- For continuous voltage, the best route would be to use a lab bench power supply, but since I don't have one, a power supply from a wall outlet will be the way to go.
- If we want the breadboard to have 12V at 1A, our wall adapter will need to supply above these ranges so that we aren't hitting the absolute max rating. The next size up is typically 15V.
- I found one on Amazon rated at 21W 15V 1.4A. Now, how do we go from a wall adapter DC barrel jack to the breadboard?
- We can use a DC Barrel Jack Connector. Like any component, we can hook it up to the breadboard and plug in our DC supply to provide 15V. But to actually get dual supply 12V, more steps will be required.
- We can basically make a DC power supply. DIY Eurorack power supply is a good start and https://www.instructables.com/Dual-Power-Supply-Circuit12V-and-12V/

- Plan: Use an AC-AC wall adapter that will take mains voltage and provide 15VAC (15 because using 12VAC is right in the range we want to step down; the wall adapter could provide maybe 11VAC or higher, so going over in this case will be better as we can just use a voltage regulator to step it down). Once we have 15VAC, we use a standard DC power supply circuit with a full bridge rectifier and filters with voltage regulators to get 12V DC, which is dual supply.
- New Plan: Although AC-AC seems more fun, I couldn't find one on Amazon, so I'll just use an AC to DC wall adapter and bring down the voltage to 12V virtual ground with 2 supplies. 

- How to Steps:
1. We take our AC-DC wall adapter, in this case, the WSU150-1200-R that outputs 15VDC with 1.2A, and plug it into the wall. 
2. We use the included female connection to connect it to our breadboard. 
3. Ok, so now we need to get two terminals with + and -. To do so, we use a virtual ground. We need to make a virtual ground or rail splitter circuits. 

Useful Links:  
Decoupling Capacitors and why: https://www.reddit.com/r/AskElectronics/comments/qvmnp3/what_do_all_those_capacitors_in_ac_circuits_do/

Final Circuit:  
Using the LM337 (NEG Voltage regulator) and LM317 (POS Voltage regulator), we were able to convert 24V DC into ±12V DC for dual supply. This circuit was inspired by Texas Instruments' LM337, with my own modifications as seen below:  
- Added D1 and D3 for reverse polarity current protection. 
- Added D2 and D4 for Capacitor Discharge Protection: They protect against the situation where the output capacitor could discharge through the internal structure of the regulator IC. This can happen during power-down conditions, and the diodes provide a path for the current, preventing it from damaging the ICs.

COMPONENT LIST:  
4 DIODES: 1N4002  
CAPACITORS: 2 * 1000uF/25V and 2 * 22uF/50V  
ICs: LM317T & LM337T  
Resistors: 2 * 120Ω, 2 * 1032Ω  

This concludes the first part of the project, getting plus and minus 12 volts for the future op-amps and 2.5A for use with several components. 


## Voltage Controlled Oscillator (VCO)

Now for the VCO, which is the bread and butter of this project. My first challenge was deciding which waveform to begin with; I chose a sawtooth waveform as it seemed relatively easy to make and also because we could use wave converters to transform this sawtooth into other waves.

I think there's a lot of discussion on which to start with, whether it be square, sine, triangle, sawtooth, etc. I ultimately just wanted to begin and learn as much as possible, so I just chose a sawtooth. 

To begin, let's start with some background. A Schmitt Trigger is essentially a way to create HIGH and LOW states using an analog input. However, in our use case, we will be using a resistor and a capacitor to form an RC charging circuit. Essentially, a Schmitt trigger will trigger ON when a high voltage is detected and remain on until that voltage goes to a low enough value.

1. Now, let's describe each state we want to achieve with the Sawtooth waveform and how we achieve it. First, how do we get the rising edge?
   - First ever rising edge: The first rising edge occurs at time = 0. The capacitor is fully discharged and acts like an open circuit. As voltage flows through, the capacitor starts charging through the resistor as they are in parallel. Once the voltage increases across the capacitor to the upper threshold, the Schmitt trigger will appear HIGH and cause a rising edge.
    
2. Steady linear decline:  
   Once the Schmitt is triggered, it causes the Diode to be forward-biased. This activation causes a new part of the circuit to be "unlocked," and it will allow for a path for the capacitor to discharge. The capacitor will discharge through the diode and slowly lose its charge. This is denoted by the falling edge of the sawtooth graph. Once the voltage of the capacitor drops below the lower threshold, the Schmitt Trigger switches back to low and "locks" the diode portion of the circuit.

3. Continuous rising edge:  
   Now that the diode is locked, and the Schmitt is giving a low reading, the capacitor will begin charging up again through the resistor. It will eventually cause the Schmitt to be HIGH again, and the process repeats. 

Now to build the sawtooth waveform:  
(Insert circuit schematic) 

1. Some issues I encountered: first was using the wrong diode. I used the common 1N4006 diode, which I thought would work since it's the only diode I've used throughout all my labs in the second year. Well, turns out this is a rectifier diode, which means it had:  
   a) A voltage drop around 0.7V at 1A  
   b) Reverse recovery time (ttr) at 30ms  
   c) Was designed for POWER rectification  

   This meant its overall design did not have FAST switching in mind.

Now comparing that to a 1N4148 Diode, which is a small signal fast-switching diode:  
   a) A voltage drop of around 0.7V at 10mA  
   b) A ttr of 4ns (VERY FAST)  

Because this diode switches very fast, it reduces the likelihood of transient oscillations and irregularities. The slow delay of the rectifier diode causes delays in the sawtooth. The reason the diode is able to switch faster is due to the higher doping concentration for fast switching, which allows for a quick recovery time, whereas the rectifier diode has a lower concentration, which leads to a slower recovery time.

Now, the 100k Ohm resistor is just temporary to observe the results; we will need to change this to allow for voltage changes to change the pitch of the waveform. Before this, though, we need to be able to hear what the wave we produced sounds like. To do so, we need to use a voltage buffer/unity gain amplifier. 

In essence, this follows the non-inverting amplifier but with the top resistor Rf shorted, which means Rf=0. If Rf is equal to 0, then the equation for the input voltage and output voltage could be simplified as vin = vo. Therefore, the input and output voltages are the same. Now, the reason this works as opposed to connecting a straight speaker is because there is no current (in an ideal op-amp) that flows into the buffer. Therefore, no interference will be seen when you connect headphones or speakers.









---


