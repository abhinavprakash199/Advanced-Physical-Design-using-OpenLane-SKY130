# Advanced-Physical-Design-using-OpenLane-SKY130
This repository contains the whole summary of hands on done by Abhinav Prakash (IS22MTECH14002) during the workshop "Advanced-Physical-Design-using-OpenLane-SKY130" organised by VSD. The full RTL to GDSII flow was implemented for PICORV32A- a RISC-V based CPU core.


## *Contents*

  * [Day 1](#day-1)
    + [Talking to Computers](#talking-to-computers)
    + [SoC design and openLANE](#soc-design-and-openlane)
    + [ASIC flow](#asic-flow)
    + [Getting familiar to EDA tools](#getting-familiar-to-eda-tools)
    + [Synthesis for the first time !!](https://github.com/krunalbadlani/IS21MTECH14008-PD-workshop/blob/main/README.md#synthesis-for-the-first-time-)

  * [References](#references)
  * [Acknowledgement](#acknowledgement)


## Day 1
---

The day starts off with few sessions on:-
### Talking to Computers
---
- We are introduced to the internal structure of a computer using an example of a microcontroller board. 
- Terms like Board and chip design, Integrated circuits, die,packaging, IP and Foundry IP, and macros are explained.
- RISC-V instruction set architecture is introduced along with the mention of other popular architectures like ARM and x86. This is done because the design on which we are gonna work is RISC-V based picorv32a CPU core.
- An introduction to SoC is given i.e. what is a SoC, how is it laid out is explained using examples of processors, memories and IPs.


![Screenshot (2207)](https://user-images.githubusercontent.com/120498080/214276571-820ed6d4-ea62-4fe5-8a88-642562bca8c0.png)![Screenshot (2210)]
- **Pads** are something through which we can send the signal inside the chip.
- **Core** all the digital logic of the chip are placed
- The whole chip is called as **Die** which is placed in silicon wafer.
(https://user-images.githubusercontent.com/120498080/214276342-31d5e527-b74a-4b96-9d8f-98c0b272dc98.png)
- **Foundry IP's** refers to all intellectual property (IP), whether Background IP or Foreground IP, developed for genetic components, pathways, and strains as well as methods and tools for design, genetic engineering, testing, and/or small-scale fermentation of microbial strains, regardless of when or for what purpose.
- **Macro-cells** are substantial building pieces that might be thought of as "black boxes." These macro-cells' logic and electronic activity are described, but the internal structure may or may not be known.
- We can understand that **Macros** are like pure digital logic whereas **IP's** dose the intelegent work
- Wo communicate to foundries through some interface files provided be the foundries. 
- 
### SoC design and openLANE
---
The 3 important factors in ASIC design process are, 
1. RTL designs 
2. EDA tools 
3. Process design Kits (PDKs) 

And openLANE has made them OPENSOURCE !






## References
---
- [Advanced Physical Design](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/?awt_a=5L_6&awt_l=H2Nw0&awt_m=3dG.7I1RDUA8._6)
- 


## Acknowledgement
---
- [Kunal Ghosh, Co-founder (VSD Corp. Pvt. Ltd)](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836/)
- [Nickson Jose, VLSI Engineer](https://www.linkedin.com/in/nickson-jose/)


Finally, I would like to express my sincere gratitude to [Mr. Kunal Ghosh](https://github.com/kunalg123) {Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.} and [Dr. Nanditha Rao](https://www.iiitb.ac.in/faculty/nanditha-rao) {Assistant Professor at International Institute of Information Technology – Bangalore} for tremendous assistance in planning and presenting this workshop on FPGA Fabric Design and Architecture. The workshop was excellent and well designed, this workshop taught me a lot of new things about the design of counter and RISC-V processor in FPGA Basys 3 board and  Open Source FPGA using VTR and VPR flow and about the SOFA FPGA fabric IP.   

