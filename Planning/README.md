# Breadboard Sythesizer Rough Notes

## **Introduction**

Welcome to the planning stage of the Breadboard-Synthesizer repository! This page aims to document my learning and findings throughtout the design phase of the project. I thought it would be a cool idea to have my thinking process down on paper with any questions I had during the making of this project. A lot of resources I found online skip the fundamentals which I think are crutial to any electronic design project, but heavily overlooked on. 


## **Table of Contents**

1. [Power_Supply](#Power_Supply)
2. [Features](#features)
3. [Installation](#installation)
4. [Usage](#usage)
5. [Contributing](#contributing)
6. [License](#license)

---

## **Power_Supply**

 Q1. How much voltage do we need for the synth? How many Amps?
    - Based on forums posts and standard synth schematics its +-12V and 1A. There are also schematics for VCO's, VCF's and VCA's that use 15V but it seems pretty easy to translate into working with +-12.
    - Apparently Eurorack is a popular syth that seems to run on 12V. 
    - By +-12V we mean a duel supply which has a virtual ground.
      
 Q2. How do we achieve this voltage?
    - For continuous voltage the best route to be to use a lab bench power supply, but since I dont have one, a power supply from a wall outlet will be the way to go.
    - If we want the breadbaord to have 12V at 1A our wall adapter will need to supply above these ranges so that we arnt hitting the absolute max rating. The next size up is typically 15V.
    - Found one on amazon rated at 21W 15V 1.4A. Now how do we go from a wall adapter DC barrel jack to the breadboard?
    - We can use a DC Barrel Jack Connector, like any componenet we can hook it up to the breadboard and plug in our DC supply to supply 15V. But to actually get duel supply 12V more steps will be required.
    - We can basically make a DC power supply, diy eurorack power supply is a good start and https://www.instructables.com/Dual-Power-Supply-Circuit12V-and-12V/

   -Plan: Use a AC-AC wall adapter that will take mains volotage and provide 15VAC (15 because using 12VAC is right in the range we want to step down, the wall adapter could provide maybe 11VAC or higher, so going over in this case will be better)
    as we can just use a voltage regulator to step it down.). Once we have 15VAC we use a standard DC power supply circuit with a full birdge rectifier and filters with voltage regulators to get 12V DC which is duel supply.
   -New Plan: Althought AC-AC seems more fun, I couldnt find one on amazong so ill just use a AC to DC wall adapter and bring down the voltage to 12V virutal ground with 2 supplies. 

   -How to Steps:
   1. We take out AC-DC wall adapter, in this case the WSU150-1200-R that outputs 15VDC with 1.2A, and plug into wall. 
   2. We use the included female connection to connect it to our breadboard. 
   3. Ok so now we need to get two terminals with + and -. To do so we use a virtual ground. We need to make a virtual grounds or rail splitter 
  circuits. 

   Useful Links:
   Decouping Capacitord and why: https://www.reddit.com/r/AskElectronics/comments/qvmnp3/what_do_all_those_capacitors_in_ac_circuits_do/

   Final Circuit:
   Using the LM337 (NEG Voltage regulator) and LM137 (POS voltage regulator) we were able to convert 24V DC into +/-12 DC for duel supply. This circuit was taken from inspiration from the Texas insturment's LM337, with my own modifications as seen below:
   Added D1 and D3 for reverse polarity currect protection. 
   ADDED D2 and D4 for Capacitor Discharge Protection: They protect against the situation where the output capacitor could discharge through the 
   internal structure of the regulator IC. This can happen during power-down conditions, and the diodes   
    provide a path for the current, preventing it from damaging the ICs.

COMPONENT LIST:
4 DIODES: 1N4002
CAPACITORS: 2 * 1000uF/25V and 2 22uF/50V
ICs - LM317T	& LM337T
Resistors: 2 * 120, 2 * 1032

This concludes the first part of the project, getting plus and minus 12 volt for the future op amps and 2.5A for use for several componenets. 


## **Voltage_Controled_Oscillator**

Now for the VCO which is the bread and butter of this project. My first challange was discovering which waveform to being, in which case I decided on a sawtooth waveform as it seemed realitivly easy to make and also we could use wave convertors to tansform this sawtooth into other waves.

I think theers a lot of disucssion on which to start with, whether it be square, sine, triang, sawtooth etc. I ultilatmly just wanted to being and learn as much as possible so i just chose a sawtooth. 

To begin lets start with some backgrouond. A Schmitt Trigger which is essential a way to createa HIGH and LOW states using analog input. However in our use case, we will be using a resistor and a capacitor to form a RC charging circuit. Essential a Schmitt trigger will trigger ON when a highi voltage is detected and remain on until that voltage goes to a low enough value.

  1. Now lets describe each state we want to achive with the Sawtooth waveform and how we achive it. First how do we get the rising edge?
     - First ever rising edge: The first rising edge occurs at time=-0. The capacitor is fully discharged and acts like a O.C. As voltage flows  
       through, the cpactor starts charging through the resistor as they are in parrael. Once the voltage increases across the CAP to the upper 
       threshold, the Schmitt trigger will appear HIGH and causes a rising edge.
    
  2. Steady linear decline:
     Once the Schmitt is triggeered, it cause the Diode to be forward bias. This activation cause a new part of the circuit to be "unlokced" in a sense. and it will allow for a path for the capactor to discharge to. The capctor will discharge through the diode, and slowly loose its charge. This is denoted by the falling edge of the saw tooth graph. Once the voltage of the CAP drops below the lower threshold, the Smith Trigger switch back to low and "locks" the diode portion of the circuit.

 3. Continous rising edge:
    Now that the diode is locked, and the smith is gving a low reading. The capctior will begin charging up again through the resistor. And will eventually cause the smith to be HIGH again and the process repeats. 

Now to build the sawtooth waveform: 
(Insert circuit schematic) 

1. Some issues I encountered: first was using the wrong diode. I used the common 1N4006 diodee which i thought would work since its the only diode I've used throughtout all my labs in seocnd year. Well turns outthis is a recfier diode, which means it had
   a) A voltage drop around 0.7V at 1A
   b)Reverse recovery time (ttr) at 30ms
   c) was designer for POWER rectification

This meant its overall design did not have FAST switching in mind.

Now comparing that to a 1N4148 Diode, which is a small signal fast-switching diode:
   a)A voltage drop of around 0.7 at 10mA
   b) a trr of 4ns (VERY FAST)

   Because this diodes switches very fast, it reduced the likilhood of transicent oscialltions and irregulatries. The slow delay of the recifier diode causes delays in the sawtooth. The reason the diode is able to switch faster is because the higher doping concentration for fast swithcing which allows for a quick recovery time. Where as the rectifier diode has a lower concentration which leads to a slower recoveyr time.













---


