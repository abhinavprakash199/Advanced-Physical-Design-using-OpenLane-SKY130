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
### Talking to Computers
---
Introduced to the internal structure of a computer using an example of a RISC-V processor. 

![Screenshot (2207)](https://user-images.githubusercontent.com/120498080/214276571-820ed6d4-ea62-4fe5-8a88-642562bca8c0.png)
- The exposed area of metal on a circuit board known as a **Pad** is where the component lead is soldered. Pads are used to send the signal inside the chip.
- A **Core** is a compact processor or CPU placed into a bigger processor or CPU socket. It is capable of handling all computing operations on its own.All the digital logic of the chip are placed in Core.
- The square of silicon that has been sliced off the wafer and contains an integrated circuit on the silicon wafer is called to as a **Die**.
![Screenshot (2210)](https://user-images.githubusercontent.com/120498080/214276342-31d5e527-b74a-4b96-9d8f-98c0b272dc98.png)
- **Foundry IP's** refers to all intellectual property (IP), whether Background IP or Foreground IP, developed for genetic components, pathways, and strains as well as methods and tools for design, genetic engineering, testing, and/or small-scale fermentation of microbial strains, regardless of when or for what purpose.
- **Macro-cells** are substantial building pieces that might be thought of as "black boxes." These macro-cells' logic and electronic activity are described, but the internal structure may or may not be known.
- We can understand that **Macros** are like pure digital logic whereas **IP's** dose the intelegent work.
- Wo communicate to foundries through some interface files(PDKs) provided be the foundries. 
#### RISC-V Instruction Set Architecture(ISA)
Along with the introduction of RISC-V, other well-known architectures like ARM and x86 are discussed. This is necessary since the architecture on which we will be working is a picorv32a CPU core based on RISC-V.
- For example if a C program is to be run on the hardware of given layout then the given C program is first compiled in its assembly language program (here it is the RISC-V assembly level program). Then this assemble level program is converted to machine level program which is the binary level program which is understood by the hardware of the computer and finally the bits get executed in the particular layout.
- There is another interface which need to present between RISC-V Architecture and layout is the HDL. We need to implement this RISC-V specification using some RTL. So this RTL implement the RISC-V Architecture specification using the standard RTL to GDS flow.
- So the entire flow starts from the RISC-V architecture, it has been implemented using an RTL and then finally implemented in layout.

![Screenshot (2214)](https://user-images.githubusercontent.com/120498080/214361259-261fdc90-d67d-48db-814c-99c5854cd265.png)

- The `.exe` file generated be the compiler contain the instruction whose syntax depend on the type of hardware we are using. This instruction set basically acts as an interface between the C level program and harware and this instruction set is called as **Instruction Set Architecture(ISA)** of the Architecture of the Computer(here a RISC-V Architecture). It is the language though which user speaks to the computer
- The assempler take this instructions and convert it into its respective machine language programe(binary language).
- We get the specifications of the instruction set and write a HDL of this instructions and synthesis it to gate level and then this is converted into its respective layout using the general RTL to GDS flow.

### SoC design and openLANE
---
The 3 important factors in Digital ASIC Design process are, 
1. RTL(Register Transfer Level) designs {*many RTL design is available in opensource like `librecores.org`,`openecores.org`,`github.com`*}
2. EDA(Electronic Design Automation) tools {*many EDA tools are available like spice simulator, sis, magic, etc but now a days Qflow, openROAD, openLANE are used*}
3. PDKs(Process Design Kits) data {*130nm PDK is provided by skywater *}

*openLANE has beign made OPENSOURCE !*
- The main objective of **ASIC Design Flow** is to take the design from RTL to GDS tool which is the final format used for the final layouts. It as also called as **Automated PnR** or **Physical Implemantation**






## References
---
- [Advanced Physical Design](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/?awt_a=5L_6&awt_l=H2Nw0&awt_m=3dG.7I1RDUA8._6)
- 


## Acknowledgement
---
- [Kunal Ghosh, Co-founder (VSD Corp. Pvt. Ltd)](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836/)
- [Nickson Jose, VLSI Engineer](https://www.linkedin.com/in/nickson-jose/)


Finally, I would like to express my sincere gratitude to [Mr. Kunal Ghosh](https://github.com/kunalg123) {Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.} and [Dr. Nanditha Rao](https://www.iiitb.ac.in/faculty/nanditha-rao) {Assistant Professor at International Institute of Information Technology – Bangalore} for tremendous assistance in planning and presenting this workshop on FPGA Fabric Design and Architecture. The workshop was excellent and well designed, this workshop taught me a lot of new things about the design of counter and RISC-V processor in FPGA Basys 3 board and  Open Source FPGA using VTR and VPR flow and about the SOFA FPGA fabric IP.   

