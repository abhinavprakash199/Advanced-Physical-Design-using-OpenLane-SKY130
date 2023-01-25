# Advanced-Physical-Design-using-OpenLane-SKY130
This repository contains the whole summary of hands on done by Abhinav Prakash (IS22MTECH14002) during the workshop "Advanced-Physical-Design-using-OpenLane-SKY130" organised by VSD. The full RTL to GDSII flow was implemented for PICORV32A- a RISC-V based CPU core.


## *Contents*

  * [Day 1](#day-1)
    + [Talking to Computers](#talking-to-computers)
    + [SoC design and openLANE](#soc-design-and-openlane)
    + [ASIC Design Flow](#ASIC-Design-Flow)
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

![Screenshot (2214)](https://user-images.githubusercontent.com/120498080/214389623-05de9e7c-75a3-45c4-aef8-0c50a7c2bc95.png)

- The `.exe` file generated be the compiler contain the instruction whose syntax depend on the type of hardware we are using. This instruction set basically acts as an interface between the C level program and harware and this instruction set is called as **Instruction Set Architecture(ISA)** of the Architecture of the Computer(here a RISC-V Architecture). It is the language though which user speaks to the computer
- The assempler take this instructions and convert it into its respective machine language programe(binary language).
- We get the specifications of the instruction set and write a HDL of this instructions and synthesis it to gate level and then this is converted into its respective layout using the general RTL to GDSII flow.

### SoC design and openLANE
---
The 3 important factors in Digital ASIC Design process are, 
1. **RTL Designs**(Register Transfer Level) {*many RTL design is available in opensource like `librecores.org`,`openecores.org`,`github.com`*}
2. **EDA Tools**(Electronic Design Automation) {*many EDA tools are available like spice simulator, sis, magic, etc but now a days Qflow, openROAD, openLANE are used*}
3. **PDKs Data**(Process Design Kits) {*130nm PDK is provided by skywater *}

*openLANE has beign made OPENSOURCE !*

### ASIC Design Flow
The main objective of **ASIC Design Flow** is to take the design from RTL to GDSII tool which is the final format used for the final layouts. It as also called as **Automated PnR** or **Physical Implemantation**

#### RTL to GDSII flow
![Screenshot (2215)](https://user-images.githubusercontent.com/120498080/214480028-4e5a5326-4998-4a2f-9248-f3337c3c562d.png)

1.**Synthesis**
- Designed RTL is transilated into a circuit made out of components from SCL(Standard Cell Library). The resulatant curcuit is described in HDL and usually refered to as a gate level netlist. The gate level netlist is functionally equivalent to the RTL.
- The Standard Cells have regular layouts. Cell layouts are enclosed by fixed height rectangle and cell width is variable, its an integer multiple of the units called the site width.
- Each cells comes under differnet models/views utilized by different EDA tools like - Libraty View(includes electrical modles for cells such as delay and power modles), HDL View of the Cells,SPICE View of the Cells,Layout View of the Cells(GDSII view which is the detailed view and Delev view which is the abstract view)

2.**Floor and Power Planning**
- The objective here is the plan the silicon area and create the robous power distribution network to power the circuits.
- In Chip Floor-Planning the chip die is partition between different system building blocks and place the I/O Pads.
- In Macro Floor-Planning we define the micro dimensions and its pin locations and rows definitions.
- In power planning the power network is constructed using horizontal and vertical parallel metal straps(parallel structures are ment to reduce resistance and to address electromagnetic radiation problem)

3.**Placements**
- For macros we place the gate level netlist cells on wafer rows.
- Cell placements are done 2 steps: Global placement followed be a Detailed placements (**Global Placement** tries to find the optimal positions for all the cells, such positions are not necessarly leagel, so cells may overlap of go off rows where as in **Detailed Placement** the positions obtained from global placements are minimally altered to be leagel.

4.**Clock Tree Synthesis (CTS)**
- It is the clock distribution network to deliver the clock to all sequential elements with minimum skew(the arrival of the clock at different component at differnet time) and minimum latency. The clock network looks like a tree where the clock source are the roots and the clock elements are the entities.

5.**Routing**
- After routing the clock comes signal routing. In given placement and fixed number of metal layers is require to find a valid pattern of horizontal and veritical wires to implement the nets that connects the cells together. The router uses the available metal layers as directed be the pdk, for each metal layer the pdk define the *thickness*, the *pitch*, the *tracks* and the *minimum width*. It also defines the wires that can be used to put nets, wire segments on different metal layers together.
- The skywater pdk defines extra routing layers, the lowest layer is called the local interconnect layer(its titenum nitride layer) and the following five layer on the local interconnect layer are all aluminum layers.
- Most of the routers are the grid routers, they construct the routing grids out of metal layer tracks. As the routing gris is hudge it use divide and conquer aproach is used for routing. First **Global Routing** is performed to generate the routing guids, then **Detail Routing** uses the fine grid and the routing guides to implement the actual wiring.

5.**Sign-off**
- Here we construct the final layout which undergo verification, this includes **Physical Verification** which is done throgh **Design Rule Checking(DRC)** where we make sure that the final layout hones all design rules and **Layout vs Schematic(LVS)** which make sure that the final layout matchs the gate level netlist thet we started with and finally **Timing Verification** is done through **Static Time Analysis(STA)** to make sure that all timing constrains are met and circiut will run at designated clock frequency.

#### Intriduction to OpenLANE
For Open Source ASIC Flow we need to ba aware about the following in Open Source EDA
- Tools Qualification
- Tools Calibrations
- Missing Tools

**OpenLANE** is the open source flow for a open source tapeout experiment. `striVe` is a family of open everything SoC(Open PDK, Open EDA and Open RTL)

![Screenshot (2216)](https://user-images.githubusercontent.com/120498080/214480079-1bb1d6ad-8820-4acb-a7ef-b176e3c13a42.png)

- So the main goal of OpenLANE is to produce clean GDSII(clean means no DRC, LVS and Timing voilations) with no human intervention.
- OpenLINK is tuned for SkyWater130nm Open PDK, it also supports XFAB180 and Global Foundry 130G 
- OpenLINK can be used to harden Macros and Chips(harden means creating GDSII or the final layout)
- OpenLINK has two modes of operation
    +  Autonomous
    +  Interactive




The flow can be divided into 2 parts the front end (Specifications to Verification) and the back end ( Synthesis to Sign-off). The openLANE flow is the biggest open-source automated facilitator of the Back end flow, taking input as the RTL, SDC (constraint file) and the technology files (in this case the skywater 130nm PDK) and giving GDSII as the output which can be send to the foundry for physical implementation.
The *striVe* SoC is the example of an open source flow producing a fully funtional chip.

The openLANE flow differs slightly from the conventional ASIC flow, it can be seen below: 

![openlane_flow](https://github.com/krunalbadlani/IS21MTECH14008-AdvancePDworkshopusingopenLANE-sky130pdk/blob/main/images/openlane_flow.png)


- openLANE works in either autonomous or interactive mode, we will use the interactive one here to tweak something or other on each and every stage of the flow. 

More data on openLANE can be found on its original github page [openLANE](https://github.com/efabless/openlane).

Every step done by me in this workshop is explained below:-




## References
---
- [Advanced Physical Design](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/?awt_a=5L_6&awt_l=H2Nw0&awt_m=3dG.7I1RDUA8._6)
- 


## Acknowledgement
---
- [Kunal Ghosh, Co-founder (VSD Corp. Pvt. Ltd)](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836/)
- [Nickson Jose, VLSI Engineer](https://www.linkedin.com/in/nickson-jose/)


Finally, I would like to express my sincere gratitude to [Mr. Kunal Ghosh](https://github.com/kunalg123) {Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.} and [Dr. Nanditha Rao](https://www.iiitb.ac.in/faculty/nanditha-rao) {Assistant Professor at International Institute of Information Technology – Bangalore} for tremendous assistance in planning and presenting this workshop on FPGA Fabric Design and Architecture. The workshop was excellent and well designed, this workshop taught me a lot of new things about the design of counter and RISC-V processor in FPGA Basys 3 board and  Open Source FPGA using VTR and VPR flow and about the SOFA FPGA fabric IP.   

