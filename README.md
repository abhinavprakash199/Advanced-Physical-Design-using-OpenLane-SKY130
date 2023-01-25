# Advanced-Physical-Design-using-OpenLane-SKY130
This repository contains the whole summary of hands on done by Abhinav Prakash (IS22MTECH14002) during the workshop [Advanced-Physical-Design-using-OpenLane-SKY130](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) organised by VSD. The full RTL to GDSII flow was implemented for PICORV32A- a RISC-V based CPU core using [OpenLANE](https://github.com/The-OpenROAD-Project/OpenLane) with [SKY130nm PDK](https://skywater-pdk.readthedocs.io/en/main/rules/assumptions.html).



![Screenshot (2221)](https://user-images.githubusercontent.com/120498080/214589941-6ce8de10-2e46-478b-8223-7591b4d2285c.png)

## *Table of Contents*

  * [Day 1](#day-1): Inception of open-source EDA, OpenLANE and Sky130 PDK]
    + [Talking to Computers](#talking-to-computers)
    + [SoC design and openLANE](#soc-design-and-openlane)
    + [ASIC Design Flow](#ASIC-Design-Flow)
    + [Getting familiar to EDA tools](#getting-familiar-to-eda-tools)
    + [Starting with openLANE](#starting-with-openLANE)

  * [References](#references)
  * [Acknowledgement](#acknowledgement)


## Day 1:
## Inception of open-source EDA, OpenLANE and Sky130 PDK]

---
### Talking to Computers
---
Introduced to the internal structure of a computer using an example of a RISC-V processor. 

![Screenshot (2207)](https://user-images.githubusercontent.com/120498080/214492904-1273ccaa-8c55-4fb5-9367-4b97f6a42b78.png)
- The exposed area of metal on a circuit board known as a **Pad** is where the component lead is soldered. Pads are used to send the signal inside the chip.
- A **Core** is a compact processor or CPU placed into a bigger processor or CPU socket. It is capable of handling all computing operations on its own.All the digital logic of the chip are placed in Core.
- The square of silicon that has been sliced off the wafer and contains an integrated circuit on the silicon wafer is called to as a **Die**.

![Screenshot (2210)](https://user-images.githubusercontent.com/120498080/214492926-4321ab2c-5583-4c7d-9189-f7f1d4f447af.png)

- **Foundry IP's** refers to all intellectual property (IP), whether Background IP or Foreground IP, developed for genetic components, pathways, and strains as well as methods and tools for design, genetic engineering, testing, and/or small-scale fermentation of microbial strains, regardless of when or for what purpose.
- **Macro-cells** are substantial building pieces that might be thought of as "black boxes." These macro-cells' logic and electronic activity are described, but the internal structure may or may not be known.
- We can understand that **Macros** are like pure digital logic whereas **IP's** dose the intelegent work.
- Wo communicate to foundries through some interface files(PDKs) provided be the foundries. 
#### RISC-V Instruction Set Architecture(ISA)
Along with the introduction of RISC-V, other well-known architectures like ARM and x86 are discussed. This is necessary since the architecture on which we will be working is a picorv32a CPU core based on RISC-V.
- For example if a C program is to be run on the hardware of given layout then the given C program is first compiled in its assembly language program (here it is the RISC-V assembly level program). Then this assemble level program is converted to machine level program which is the binary level program which is understood by the hardware of the computer and finally the bits get executed in the particular layout.
- There is another interface which need to present between RISC-V Architecture and layout is the **HDL**. This HDL basically describe the harware and understabd the machine code(binary input). We need to implement this RISC-V specification using some RTL. So this RTL implement the RISC-V Architecture specification using the standard RTL to GDS flow.
- So the entire flow starts from the RISC-V Architecture, it has been implemented using an RTL and then finally implemented in layout.

![Screenshot (2214)](https://user-images.githubusercontent.com/120498080/214389623-05de9e7c-75a3-45c4-aef8-0c50a7c2bc95.png)

- The `.exe` file generated be the compiler contain the instruction whose syntax depend on the type of hardware we are using. This instruction set basically acts as an interface between the C level program and harware and this instruction set is called as **Instruction Set Architecture(ISA)** of the Architecture of the Computer(here a RISC-V Architecture). It is the language though which user speaks to the computer
- The assempler take this instructions and convert it into its respective machine language programe(binary language).
- We get the specifications of the instruction set and write a HDL of this instructions and synthesis it to gate level and then this is converted into its respective layout using the general RTL to GDSII flow.

### SoC design and openLANE
---
The 3 important factors in Digital ASIC Design process are, 
1. **RTL Designs**(Register Transfer Level) {*many RTL design is available in opensource like `librecores.org`,`openecores.org`,`github.com`*}
2. **EDA Tools**(Electronic Design Automation) {*many EDA tools are available like spice simulator, sis, magic, etc but now a days Qflow, openROAD, openLANE are used*}
3. **PDKs Data**(Process Design Kits) {*Google + 130nm PDK is provided by skywater *}

*The openLANE is a OPENSOURCE platform*

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
- Cell placements are done 2 steps: 
   + **Global Placement** (Global Placement tries to find the optimal positions for all the cells, such positions are not necessarly leagel, so cells may overlap of go off rows)
   + **Detailed placements** (In Detailed Placement the positions obtained from global placements are minimally altered to be leagel.

4.**Clock Tree Synthesis (CTS)**
- It is the clock distribution network to deliver the clock to all sequential elements with minimum skew(the arrival of the clock at different component at differnet time) and minimum latency. The clock network looks like a tree where the clock source are the roots and the clock elements are the entities.

5.**Routing**
- After routing the clock comes signal routing. In given placement and fixed number of metal layers is require to find a valid pattern of horizontal and veritical wires to implement the nets that connects the cells together. The router uses the available metal layers as directed be the pdk, for each metal layer the pdk define the *thickness*, the *pitch*, the *tracks* and the *minimum width*. It also defines the wires that can be used to put nets, wire segments on different metal layers together.
- The skywater pdk defines extra routing layers, the lowest layer is called the local interconnect layer(its titenum nitride layer) and the following five layer on the local interconnect layer are all aluminum layers.
- Most of the routers are the grid routers, they construct the routing grids out of metal layer tracks. As the routing grids is hudge it use divide and conquer aproach is used for routing. 
   + First **Global Routing** is performed to generate the routing guids, 
   + then **Detail Routing** uses the fine grid and the routing guides to implement the actual wiring.

5.**Sign-off**
- Here we construct the final layout which undergo verification, which includes 
**Physical Verification** 
    + **Design Rule Checking(DRC)** where we make sure that the final layout hones all design rules 
    + **Layout vs Schematic(LVS)** which make sure that the final layout matchs the gate level netlist thet we started with 
**Timing Verification** 
    + **Static Time Analysis(STA)** to make sure that all timing constrains are met and circiut will run at designated clock frequency.

#### Introduction to OpenLANE
For Open Source ASIC Flow we need to ba aware about the following in Open Source EDA
- Tools Qualification
- Tools Calibrations
- Missing Tools

**OpenLANE** is the open source flow for a open source tapeout experiment. `striVe` is a family of open everything SoC(Open PDK, Open EDA and Open RTL)
- [OpenLANE Documentation](https://openlane.readthedocs.io/en/latest/)

![Screenshot (2216)](https://user-images.githubusercontent.com/120498080/214480079-1bb1d6ad-8820-4acb-a7ef-b176e3c13a42.png)

- So the main goal of OpenLANE is to produce clean GDSII(clean means no DRC, LVS and Timing voilations) with no human intervention.
- OpenLINK is tuned for SkyWater130nm Open PDK, it also supports XFAB180 and Global Foundry 130G 
- OpenLINK can be used to harden Macros and Chips(harden means creating GDSII or the final layout)
- OpenLINK has two modes of operation
    +  **Autonomous** (it is the push buttton flow where we figure the flow and we directly get the final GDSII) 
    +  **Interactive** (here we run comands and steps one be one so that we can do experimentation and look at the results of differnet flow steps)
 - OpenLINK has very nice feature called as **Design Space Exploration** which can be used to find the best set of flow configurations.
 - OpenLINK has large number of design examples(43 different design with their best configuration)

#### OpenLANE ASIC Flow
![Screenshot (2217)](https://user-images.githubusercontent.com/120498080/214488649-b4a76e24-a8f0-401d-a2aa-455f14c111ae.png)

- In **RTL Syntheses** the RTL is fead to *Yosys* with the design constrains, Yosys converts the RTL into a logic circuits, this circuit can be optimized and then mapped into a cell using STL using *abc* (abc has to be guided during optimization which comes as "abc script" from OpenLANE called synthesis statergies, these scrips are guided for least area or best timing)
- **Synthesis Explorations** can be used to geneate reports that can show how delay are area are affrected be synthesis statergies.
- **Design Explorations** can be used to sweep the design configurations for parameters such as RunTime, CellCount, RoutingStatrergies, etc. It is very useful to find best configuration from OpenLANE for any specific design. Design Explorations can also be used for regression testing for continues integration{OpenLANE run the exploration in 70 designs and compare the results to the best knonwn one}
- If we want our design to get ready of testing after fabrication we enable **DFT(Design for Test)** step(which is optional) and this used open source project "Fault" to perform 
   + Scan Insertion
   + Automatic Test Pattern Generation(ATPG)
   + Test Pattern Compaction
   + Fault Coverage 
   + Fault Simulation
- **DFT(Design for Test)** also add the data controller which enables the external access to the internal structure.
- **Physical Implemantation** also called as **Automated PnR(Placement and Route)** involve several steps and all of them are done be openROAD app
   + Floor/Power Planning 
   + End Decoupling Capacitors and Tap cells insertion 
   + Placement: Global and Detailed 
   + Post placement optimization
   + Clock Tree Synthesis (CTS)
   + Routing: Global and Detailed
- During the Physical Implemantation we have a special step **Fake antenna diodes Insertion Script** which is required to address the antenna rules violation.
- With OpenLANE we took a preventive approach and created a Fake Antenna Diode Cell to SCL next to every cell input after placement. After the fake antenna routing we run the Antenna Checker `Magic` against the layout and if the checker reports a violation on the cell input pin then we replace the Fake Diode cell by a real one.
- As we perform optimization which involves some transformations of the gate level netlist that was generated by the synthesis step so we need to perform **LEC(Logic Equivalence Checking)** which can be done using `yosys` where we compare the netlist resulted from the optimazation(done during Physical Implemantation) to the gate level netlist genreted from the synthesisis to make sure they are functinally equivalent.
- Finally Sign-off of OpenLANE involves interconnects **RC Extraction** from the routed layout followed by **STA(Static Timing Analysis)** using "OpenSTA" which is on "OpenROAD". The result of this setp is set of timing reports highiliting timing violations if any.
- **Physical Verification** involves DRC performed using `Magic` VLSI layout tool and LVS involve using `Magic` for several extraction followed by `Nitgen` to perform the comparision.

#### OpenLane Directory Hierarchy:
```
├── OOpenLane             -> directory where the tool can be invoked (run docker first)
│   ├── designs          -> All designs must be extracted from this folder
│   │   │   ├── picorv32a -> Design used as case study for this workshop
│   |   |   ├── ...
|   |   ├── ...
├── pdks                 -> contains pdk related files 
│   ├── skywater-pdk     -> all Skywater 130nm PDKs
│   ├── open-pdks        -> contains scripts that makes the commerical PDK (which is normally just compatible to commercial tools) to also be compatible with the open-source EDA tool
│   ├── sky130A          -> pdk variant made especially compatible for open-source tools
│   │   │  ├── libs.ref  -> files specific to node process (timing lib, cell lef, tech lef) for example is `sky130_fd_sc_hd` (Sky130nm Foundry Standard Cell High Density)  
│   │   │  ├── libs.tech -> files specific for the tool (klayout,netgen,magic...) 
```
More data on openLANE can be found on its original github page [openLANE](https://github.com/efabless/openlane).

### Getting familiar to EDA tools
We would be working on the OpenLINK  tool, but it is not exactly a tool, it is the flow which comprises of the many open source tool like `Viosis`, `OpenSTA`, etc. The basic aim of having this OpenLINK is to have complete RTL to GDSII flow and cut of the humain intervention.

**NOTE** - *Linux command to open help for that particular command `{command name} --help`*

**NOTE** - *Linux command to file contents on one screen at a time `less {file_name.extension}`*

**NOTE** - *Linux command to open file contents on vim `vim {file_name.extension}`*


#### OpenLANE Directory structure in detail
We have several pdk under:
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/pdks$
1. `skywater-pdk` has all the pdk related files like timing libraries, lef files, techlef, celllef files, etc. These are Silicon foundary files compatable with commertial EDA tools and not for open source EDA tool
2. `open_psk` has set of scripts and files that convert these foundry level pdk to be compatable to open source EDA tools like *magic*, *netgen*
3. `sky130A` is the pdk varient which has been made compatable to open source enviroment.


This `sky130A` pdk file contain 2 files:
1. `libs.ref` which contain all the process specific file specific to the technology like timimg files, cells files, etc. We would be working on `sky130_fd_sc_hd` pdk  varient
2. `libs.tech` which contain files specific to the tool like *klayout, ngspice, netgen, magic, qflow, openlane, etc*

- We would be working on openlane directory 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane$ 

#### Design Preparation Step

- All the designs that are being run by openlane is extraced from designs folder 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs$
- We will be moving with `pocorv32a` design which contain:
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs$
1. `src` file contain rtl(verilog forl) and sdc information.
2. `.tcl` file which is pdk specific configuration file.
3. `config.tcl` file passes any configurations that has been already done like location of verilog files, location of sdc files, clock period, etc.

#### Steps to Open openlane

Go to openlane  working directory 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane$ 
```
docker
./flow.tcl -interactive
package require openlane 0.9
```
- We use `docker` command to open the open lane in working directory.
- Then use `./flow.tcl -interactive` which identifies using the script the flow has to move and intractive means we do a step by step process.
- Then we need to import all the package that are required to run this program by `package require openlane 0.9`

- Command to run fully automated run `./flow.tcl -design picorv32a`

![image](https://user-images.githubusercontent.com/120498080/214596626-8ca8164d-041a-47a6-934b-09403c9222cf.png)

- [Github Repository for openlane by efabless](https://github.com/efabless/openlane)


**NOTE** *Commands to save the github directory in local machine* - 
> git clone https://github.com/The-OpenROAD-Project/OpenLane.git`

- To learn more how openlink works and how its is designed [prefer this videos](https://www.youtube.com/playlist?list=PLUg3wIOWD8yoZCg9XpFSgEgljx6MSdm9L).


**So now we are ready to execute the commands**

### Starting with openLANE
- Now we would be running our first step which is synthesis in openlane but before that we need to set the file system in the *design setup stage* which will be setting up the data for our data structure for our design. 
- For that enter the given command in openlane :
> prep -design picorv32a
- So after this the `runs` directory has been created into picorv32a directory under which folder structures required by the openlink will be created in which all the folders will be empty except `tmp`
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a$ 
- The `run_synthesis` command will run bios synthesis as well as the abc.

#### Calculation of number of flop ratio 
Flop ratio is defined as the ratio of number of D flip flop to total number of cell.

All the results will be saved into the run folder and the following reports will be generated:
![image](https://user-images.githubusercontent.com/120498080/214659318-48f9e2b4-465d-4b35-8f2e-0b92ae1ace8f.png)
- Like **synthesis stastistics report** (will give information about number of cells used) will be saved in 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-01_14-57/reports/synthesis$ vim 1-yosys_4.stat.rpt
- So here we got flop ratio of 0.10843 
![Screenshot (2223)](https://user-images.githubusercontent.com/120498080/214655100-76ddbf8e-d648-4e91-8547-b681faa4ff9e.png)
- The chip area report is given as 
![image](https://user-images.githubusercontent.com/120498080/214660310-79b2323c-266e-4585-83af-9add2e5a002c.png)











## References
---
- [Advanced Physical Design](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/?awt_a=5L_6&awt_l=H2Nw0&awt_m=3dG.7I1RDUA8._6)
- 


## Acknowledgement
---
Finally, I would like to express my sincere gratitude to [Kunal Ghosh](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836/){Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.} and [Nickson Jose, VLSI Engineer](https://www.linkedin.com/in/nickson-jose/) {ASoC Physical Design Engineer} for tremendous assistance in planning and presenting this workshop on Advanced-Physical-Design-using-OpenLane-SKY130. The workshop was excellent and well designed, this workshop taught me a lot of new things about the design of RISC-V processor in OpenLANE.   

