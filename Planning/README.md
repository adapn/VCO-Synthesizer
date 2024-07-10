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
    2. We use a 
    3. Ok so now we need to get two terminals with + and -. To do so we use a virtual ground. We need to make a virtual grounds or rail splitter circuits. 

---

## **Features**

- **Feature 1**: Description of feature 1.
- **Feature 2**: Description of feature 2.
- **Feature 3**: Description of feature 3.

![Another Random Image](https://via.placeholder.com/200)

---

## **Installation**

Follow these instructions to install the project:

```bash
npm install project-name
