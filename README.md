# Advanced-Physical-Design-using-OpenLane-SKY130
This repository contains the whole summary of hands on done by Abhinav Prakash (IS22MTECH14002) during the workshop Advanced-Physical-Design-using-OpenLane-SKY130 organised by VSD. The full RTL to GDSII flow was implemented for picorv32a design a RISC-V based CPU core using [OpenLANE](https://github.com/The-OpenROAD-Project/OpenLane) with [SKY130nm PDK](https://skywater-pdk.readthedocs.io/en/main/rules/assumptions.html).



![Screenshot (2221)](https://user-images.githubusercontent.com/120498080/214589941-6ce8de10-2e46-478b-8223-7591b4d2285c.png)

## *Table of Contents*

* [Day 1: Inception of open-source EDA, OpenLANE and Sky130 PDK](#day-1)
    + [Talking to Computers](#talking-to-computers)
    + [SoC Design and OpenLANE](#soc-design-and-openlane)
    + [ASIC Design Flow](#ASIC-Design-Flow)
    + [Getting Familiar to EDA tools](#getting-familiar-to-eda-tools)
    + [Starting with OpenLANE and Synthesis](#starting-with-openLANE-and-synthesis)
* [Day 2: Good Floorplan vs bad Floorplan and Introduction to Library Cells](#day-2)
    + [Stages of Floorplanning](#Stages-of-floorplanning)
    + [Steps to run and view floorplan using OpenLANE](#steps-to-run-and-view-floorplan-using-openlane)
    + [Placement in OpenLANE](#Placement-in-OpenLANE)
    + [Cell Design Flow](#Cell-Design-Flow)
    + [Characterization](#Characterization) 
* [Day 3 - Design library cell using Magic Layout and ngspice characterization](#day-3)
    + [Labs for CMOS inverter ngspice simulations](#Labs-for-CMOS-inverter-ngspice-simulations)
    + [CMOS Inverter Design using Magic](#CMOS-Inverter-Design-using-Magic)
    + [Characterizing the cell's(CMOS Inverter) slew rate and propagation delay](#Characterizing-the-cell's(CMOS-Inverter)-slew-rate-and-propagation-delay)
* [Day 4 - Pre-layout timing analysis and importance of good clock tree](#day-4)
    + [Plug-in the Customized Inverter Cell(lif file) to OpenLane](#Plug-in-the-Customized-Inverter-Cell(lif-file)-to-OpenLane)
    + [Delay](#Delay)
    + [Fix Negative Slack](Fix-Negative-Slack)
    + [Floorplanning and Placement](#Floorplanning-and-Placement)
    + [Setup Timing Analysis](#Setup-Timing-Analysis)
    + [Run CTS(Clock Tree Synthesis) using TritonCTS](#Run-CTS(Clock-Tree-Synthesis)-using-TritonCTS)
    + [Timing Analysis with Real Clocks](#Timing-Analysis-with-Real-Clocks)
* [DAY 5: Final Steps for RTL2GDS using TritonRoute and OpenSTA](#Day-5)
    + [Maze Routing](#Maze-Routing)
    + [DRC Cleaning](#DRC-Cleaning)
    + [Power Distribution Network (review)](#Power-Distribution-Network-(review))
    + [Routing Stage and TritonRoute](#Routing-Stage-and-TritonRoute)
    + [Run PDN(Power Distribution Network)](#Run-PDN(Power-Distribution-Network))
    + [Routing Stage](#Routing-Stage)
    + [Extraction of GRSII file](#Extraction-of-GRSII-file)
* [All commands to run in openlane](#All-commands-to-run-in-openlane)
    
* [References](#references)
* [Acknowledgement](#acknowledgement)
* [Inquiries](#inquiries)


## Day 1:
## Inception of open-source EDA, OpenLANE and Sky130 PDK

---
### Talking to Computers
---
Introduced to the internal structure of a computer using an example of a RISC-V processor. 

<p align="center">
    <img src="https://user-images.githubusercontent.com/120498080/214492904-1273ccaa-8c55-4fb5-9367-4b97f6a42b78.png">

- The exposed area of metal on a circuit board known as a **Pad** is where the component lead is soldered. Pads are used to send the signal inside the chip.
- A **Core** is a compact processor or CPU placed into a bigger processor or CPU socket. It is capable of handling all computing operations on its own. All the digital logic of the chip are placed in Core.
- The square of silicon that has been sliced off the wafer and contains an integrated circuit on the silicon wafer is called to as a **Die**.
    
<p align="center">
    <img src="https://user-images.githubusercontent.com/120498080/214492926-4321ab2c-5583-4c7d-9189-f7f1d4f447af.png">

- **Foundry IP's** refers to all intellectual property (IP), whether Background IP or Foreground IP, developed for genetic components, pathways, and strains as well as methods and tools for design, genetic engineering, testing, and/or small-scale fermentation of microbial strains, regardless of when or for what purpose.
- **Macro-cells** are substantial building pieces that might be thought of as "black boxes." These macro-cells logic and electronic activity are described, but the internal structure may or may not be known.
- We can understand that **Macros** are like pure digital logic whereas **IP's** dose the intelegent work.
- Wo communicate to foundries through some interface files(PDKs) provided be the foundries.
    
#### RISC-V Instruction Set Architecture(ISA)
Along with the introduction of RISC-V, other well-known architectures like ARM and x86 are discussed. This is necessary since the architecture on which we will be working is a picorv32a CPU core based on RISC-V processor.
- For example if a C program is to be run on the hardware of given layout then the given C program is first compiled in its assembly language program (here it is the RISC-V assembly level program). Then this assemble level program is converted to machine level program which is the binary level program which is understood by the hardware of the computer and finally the bits get executed in the particular layout.
- There is another interface which need to present between RISC-V Architecture and layout is the **HARDWARE DESCRIPTION LANGUAGE (HDL)**. This HDL basically describe the harware and understabd the machine code(binary input). We need to implement this RISC-V specification using some RTL. So this RTL implement the RISC-V Architecture specification using the standard RTL to GDS flow.
- So the entire flow starts from the RISC-V Architecture, it has been implemented using an RTL and then finally implemented in layout.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/120498080/214389623-05de9e7c-75a3-45c4-aef8-0c50a7c2bc95.png">

- The `.exe` file generated be the compiler contain the instruction whose syntax depend on the type of hardware we are using. This instruction set basically acts as an interface between the C level program and harware and this instruction set is called as **Instruction Set Architecture(ISA)** of the Architecture of the Computer(here a RISC-V Architecture). It is the language though which user speaks to the computer
- The assembler take this instructions and convert it into its respective machine language programe(binary language).
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
   + **Global Placement** (Global Placement tries to find the optimal positions for all the cells, such positions are not necessarly leagel, so cells may overlap of go off rows).
   + **Detailed placements** (In Detailed Placement the positions obtained from global placements are minimally altered to be leagel).

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
    + **Design Rule Checking(DRC)** where we make sure that the final layout follows all design rules.
    + **Layout vs Schematic(LVS)** which make sure that the final layout matches the gate level netlist then we started with 
**Timing Verification** 
    + **Static Time Analysis(STA)** to make sure that all timing constrains are met and circiut will run at designated clock frequency.

#### Introduction to OpenLANE
For Open Source ASIC Flow we need to ba aware about the following in Open Source EDA
- Tools Qualification
- Tools Calibrations
- Missing Tools

**OpenLANE** is the open source flow for a open source tapeout experiment. `striVe` is a family of open everything SoC(Open PDK, Open EDA and Open RTL)

![Screenshot (2216)](https://user-images.githubusercontent.com/120498080/214480079-1bb1d6ad-8820-4acb-a7ef-b176e3c13a42.png)

- So the main goal of OpenLANE is to produce clean GDSII(clean means no DRC, LVS and Timing voilations) with no human intervention.
- OpenLINK is tuned for SkyWater130nm Open PDK, it also supports XFAB180 and Global Foundry 130.
- OpenLINK can be used to harden Macros and Chips(harden means creating GDSII or the final layout)
- OpenLINK has two modes of operation:
    +  **Autonomous** (it is the push buttton flow where we figure the flow and we directly get the final GDSII) 
    +  **Interactive** (here we run comands and steps one be one so that we can do experimentation and look at the results of differnet flow steps)
 - OpenLINK has very nice feature called as **Design Space Exploration** which can be used to find the best set of flow 
 durations.
 - OpenLINK has large number of design examples(43 different design with their best configuration, here we will be usibg picrorv32a)

#### OpenLANE ASIC Flow
<p align="center">
    <img src="https://user-images.githubusercontent.com/120498080/214488649-b4a76e24-a8f0-401d-a2aa-455f14c111ae.png">

- In **RTL Syntheses** the RTL is fead to *Yosys* with the design constrains, Yosys converts the RTL into a logic circuits, this circuit can be optimized and then mapped into a cell using STL(OpenSTA) using *abc* (abc has to be guided during optimization which comes as "abc script" from OpenLANE called synthesis statergies, these scrips are guided for least area or best timing)
- **Synthesis Explorations** can be used to geneate reports that can show how delay are area are affected be synthesis statergies.
- **Design Explorations** can be used to sweep the design configurations for parameters such as RunTime, CellCount, RoutingStatergies, etc. It is very useful to find best configuration from OpenLANE for any specific design. Design Explorations can also be used for regression testing for continues integration{OpenLANE run the exploration in 70 designs and compare the results to the best knonwn one}
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
├── OpenLane             -> directory where the tool can be invoked (run docker first)
    ├── designs          -> All designs must be extracted from this folder
        │   ├── picorv32a -> Design used as case study for this workshop
            ├── ...
        ├── ...
├── pdks                 -> contains pdk related files 
    ├── skywater-pdk     -> all Skywater 130nm PDKs
    ├── open-pdks        -> contains scripts that makes the commerical PDK (which is normally just compatible to commercial tools) to also be compatible with the open-source EDA tool
    ├── sky130A          -> pdk variant made especially compatible for open-source tools
           ├── libs.ref  -> files specific to node process (timing lib, cell lef, tech lef) for example is `sky130_fd_sc_hd` (Sky130nm Foundry Standard Cell High Density)  
           ├── libs.tech -> files specific for the tool (klayout,netgen,magic...) 
```

### Getting familiar to EDA tools
We would be working on the OpenLINK  tool, but it is not exactly a tool, it is the flow which comprises of the many open source tool like `Viosis`, `OpenSTA`, etc. The basic aim of having this OpenLINK is to have complete RTL to GDSII flow and cut of the humain intervention.

**NOTE** - *Linux command to open help for that particular command `{command name} --help`*

**NOTE** - *Linux command to file contents on one screen at a time `less {file_name.extension}`*

**NOTE** - *Linux command to open file contents on vim `vim {file_name.extension}`*


#### OpenLANE Directory structure in detail
We have several pdk under:
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/pdks$
1. `skywater-pdk` has all the pdk related files like timing libraries, lef files, techlef, cell lef files, etc. These are Silicon foundary files compatable with commertial EDA tools and not for open source EDA tool
2. `open_psk` has set of scripts and files that convert these foundry level pdk to be compatable to open source EDA tools like *magic*, *netgen*
3. `sky130A` is the pdk varient which has been made compatable to open source enviroment.


This `sky130A` pdk file contain 2 files:
1. `libs.ref` which contain all the process specific file specific to the technology like timimg files, cells files, etc. We would be working on `sky130_fd_sc_hd` pdk  varient
2. `libs.tech` which contain files specific to the tool like **klayout, ngspice, netgen, magic, qflow, openlane, etc**

- We would be working on openlane directory 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane$ 

#### Design Preparation Steps

- All the designs that are being run by openlane is extraced from designs folder 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs$
- We will be moving with `pocorv32a` design which contain:
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs$
1. `src` file contain RTL(verilog flow) and sdc information.
2. `.tcl` file which is pdk specific configuration file.
3. `config.tcl` file passes any configurations that has been already done like location of verilog files, location of sdc files, clock period, etc.

#### Steps to Open openlane

Go to openlane  working directory 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane$ 
```
docker
./flow.tcl -interactive
% package require openlane 0.9
```
- We use `docker` command to open the openLANE in working directory.
- Then use `./flow.tcl -interactive` which identifies using the script the flow has to move and intractive means we do a step by step process.
- Then we need to import all the package that are required to run this program by `package require openlane 0.9`

- **NOTE** Command to run fully automated run `./flow.tcl -design picorv32a`
<p align="center">
    <img src="https://user-images.githubusercontent.com/120498080/214596626-8ca8164d-041a-47a6-934b-09403c9222cf.png">

- [Github Repository for openlane by efabless](https://github.com/efabless/openlane)


**NOTE** *Commands to save the github directory in local machine* - `git clone https://github.com/The-OpenROAD-Project/OpenLane.git`

- To learn more how openlink works and how its is designed [prefer these videos](https://www.youtube.com/playlist?list=PLUg3wIOWD8yoZCg9XpFSgEgljx6MSdm9L).

**So now we are ready to execute the commands**

### Starting with openLANE and Synthesis
- Now we would be running our first step which is synthesis in openlane but before that we need to set the file system in the *design setup stage* which will be setting up the data for our data structure for our design. 
- For that enter `% prep -design picorv32a` command in openlane :
- So after this the `runs` directory has been created into picorv32a directory under which folder structures required by the openlink will be created in which all the folders will be empty except `tmp`
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a$ 

#### Run Logic Synthesis in OpenLANE
In **Logic Synthesis** wo convert the RTL code into a leagel hardware. So the output of Logic Synthesis is the arrangements of flip flops and the gates that will represent our origianl functionality that we have described using an RTL.

- The `% run_synthesis` command will run bios synthesis as well as the abc.

#### Calculation of number of flop ratio using synthesis report
Flop ratio is defined as the ratio of number of D flip flop to total number of cell.

All the results will be saved into the run folder and the following reports will be generated:
![image](https://user-images.githubusercontent.com/120498080/214659318-48f9e2b4-465d-4b35-8f2e-0b92ae1ace8f.png)
- Like **synthesis stastistics report** (will give information about number of cells used) will be saved in 

> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-01_14-57/reports/synthesis$ vim 1-yosys_4.stat.rpt
- So here we got flop ratio of 0.10843 

![Screenshot (2223)](https://user-images.githubusercontent.com/120498080/214655100-76ddbf8e-d648-4e91-8547-b681faa4ff9e.png)
- The chip area report is given as 

![image](https://user-images.githubusercontent.com/120498080/214660310-79b2323c-266e-4585-83af-9add2e5a002c.png)



## Day 2:
## Good Floorplan vs Bad Floorplan and Introduction to library cells
---
### Stages of Floorplanning:
---
The placement of logical blocks, library cells, and pins on a silicon chip is known as chip floorplanning. It ensures that every module has been given the proper area and aspect ratio, that every pin of the module is connected to another module or the chip's edge, and that modules are placed so that they take up the least amount of space on a chip.

1. **The height and width of core and die**
- The core, which is located in the middle of the die, is where the logic blocks are put. The dimensions of each standard cell on the netlist determine the width and height. 
- **Utilization Factor** is defined as the ratio of area of occupancy by the netlist to total area of the core. Utilization factor in a realistic situation is between 0.5 and 0.6. Only this space is used for the netlist; the rest space is used for routing and more extra cells. 
- **Aspect Ratio** is defined as the ratio between height and the width of core.
#### Height and Width of Chip
![Screenshot (2232)](https://user-images.githubusercontent.com/120498080/214807245-9df9caee-65dd-4776-848d-145e3526c8bb.png)

2. **The location of Preplaced Cell**
- These are complex logic blocks that are previously implemented but can be reused, such as memory, clock-gating cells, muxes, comparator, etc. Prior to placement and routing, the user-defined placement on the core must be completed (thus preplaced cells). 
- This needs to be very well described because the automated place and route tools won't be able to touch or move these preplaced cells.
- These preplacement cell need to be surrounded by decoupling capacitors.
#### Location of Preplaced Cell
![Screenshot (2233)](https://user-images.githubusercontent.com/120498080/214807607-3c02d0e4-f56b-4450-819d-5ce8c4a6cc84.png)

3. **Surround preplaced cells with decoupling capacitors**
- The complex preplaced logic block needs a lot of current from the power supply to switch the current. However, due to the resistance and inductance of the wire, there will be a voltage drop because of the distance between the main power supply and the logic block. As a result, the voltage at the logic block might no longer fall within the noise margin range (logic is unstable).
-  Utilizing decoupling capacitors which are hudge bunch of capacitor completely filled with charge, close to the logic block will provide the necessary current for the logic block to switch inside the desired noise margin range.
#### Decoupling capacitors surrounding the preplaced blocks
![Screenshot (2225)](https://user-images.githubusercontent.com/120498080/214807860-91585c95-2b6d-4e1e-ae78-d15accebbd3c.png)

4. **Power Planning**
- It is not possible to apply a decoupling capacitor for sourcing logic blocks with enough current throughout the entire chip, only on the important components (preplaced complex logicblocks). 
- Due to the large amount of current that must be sinked simultaneously when a large number of elements switch from logic 1 to logic 0, this could result in **ground bounce**, and switching from logic 0 to logic 1 could result in **voltage droop** because there is not enough current from the power source to source the needed current for all elements. The increase or decrease in voltage may not be within the noise margin range due to voltage droop and ground bounce.
-  The reason for problem of voltage droop and ground bounce is because the supply has been provided only from one point so we use multiple power source taps (power mesh) are the solution, allowing components to source current from the closest VDD tap and sink current to the closest VSS tap. The majority of processors include several powersource pins because of this.
#### Four blocks with multiple power suppies
![Screenshot (2227)](https://user-images.githubusercontent.com/120498080/214799889-7811d3a3-632e-4f8d-842f-da972484a04e.png)
#### Power Planning showing multiple power source taps
![Screenshot (2228)](https://user-images.githubusercontent.com/120498080/214806272-99e2f671-2fec-496f-95a2-e8fbac07291a.png)

5. **Pin Placement**
- The area between the core and the die is where the input and output ports are located.
- The positions of the ports depend on the placements of the cells that are connected with them on the core. 
- Since this clock must be able to drive the entire chip so the clock pin is thicker (lowest resistance route) than data ports.
#### Pin Placement in a Chip
![Screenshot (2231)](https://user-images.githubusercontent.com/120498080/214806578-d1e27eab-7ecf-4def-988d-f97ef7e19d06.png)

6. **Logical Cell Placement Blockage**
- This ensures that no cells are placed by the automated placement and routing tool on the die's pin locations.
#### Chip with Logical Cell Placement Blockage
![Screenshot (2230)](https://user-images.githubusercontent.com/120498080/214806309-9a14a6d8-ead1-481c-990d-207ae586873c.png)

### Steps to run and view floorplan using OpenLANE
---
1. **Set configuration variables** 
- The configuration variables or switches must be set up before to starting the floorplan stage.. 
- The configuration variables location is 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/configuration$
```
.
├── README.md      
├── floorplan.tcl 
├── cts.tcl
├── checkers.tcl
├── general.tcl
├── lvs.tcl
├── synthesis.tcl 
├── routing.tcl
└── placement.tcl
```

The default OpenLANE settings are contained in the tcl files, and the `README.md` defines every configuration variable for every stage. Under 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a$ vim config.tcl 
we find every configuration that the current run has approved. This could originate from (in order of priority):

- PDK specific configuration `sky130A_sky130_fd_sc_hd_config.tcl` inside the `openlane/design/picorv32a` folder
- `config.tcl` inside the `openlane/designs/picorv32a` folder
- System default settings inside `openlane/configurations`
#### `config.tcl` file should contain following information
![image](https://user-images.githubusercontent.com/120498080/215285276-fed271bd-16a4-4d0a-995a-c27ede363eaf.png)



2. **Run floorplan on OpenLane:** 
Use command `% run_floorplan` openlane 

3.**Review floorplan files:**
The details of this stags like core utilization ratio are saved in this location
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-01_14-57$ vim config.tcl 
- In `25-01_14-57/config.tcl` file we got a core utilization ratio as 35 because it was set in PDK specific configuration `sky130A_sky130_fd_sc_hd_config.tcl` inside the `openlane/design/picorv32a` folder which has the highest precedance.

![image](https://user-images.githubusercontent.com/120498080/214833348-819f797e-9a3b-4aa7-a97a-461f89612358.png)

#### Calculations of Die Area

The def(design exchange format) file, containing the die area and positions which is at location 
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-01_14-57/results/floorplan$ vim picorv32a.floorplan.def

![image](https://user-images.githubusercontent.com/120498080/214835082-c9d38fa8-45fb-456b-a74c-906caf7a62c9.png)

- One micrometre is equal to 1000 database units in this case when the die area is expressed in database units. 
- The **die's surface area is therefore 443587 microns squared**, or (660685/1000)microns*(671405/1000)microns.

4. **View the floorplan in magic**
For visualising the layout following a floorplan, utilise the Magic Layout Tool. The following three files are necessary in order to examine a floor layout in Magic
- Technology File `sky130A.tech`
- Merged LEF file `merged.lef` 
- `vim picorv32a.floorplan.def` files

To open layout in magic use this command in this location
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-01_14-57/results/floorplan$

```
magic -T /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def
```
`-T <address_of_sky130A.tech_file>` where T is for technology file 
`lef read <address_of_merged.lef_file>` to read the lef files (we use `lef read` because its a standard industry file)
`def read <address_of_picorv32a.floorplan.def_file>` to read the def files (we use `def read` because its a standard industry file) 
- **NOTE** If address of the required file is at the same working loaction then we just need to provide the required file name.
- 
![image](https://user-images.githubusercontent.com/120498080/214837492-61506cf5-9b3e-4350-ad28-6c9e3f459bf8.png)
**NOTE**
This `sky130A.tech`(technology), `merged.lef`(layout exchange format) and `picorv32a.placement.def`(design exchange format) files comes allong with the pdk of sky130.
#### Some shortcuts keys in magic
- Point the cursor to a cell then press "s" to select the any block. 
- Then press "v" to center the view. 
- To zoom into it use mouse left and right click and then press 'z".

[Magic Commands](https://github.com/AngeloJacobo/OpenLANE-Sky130-Physical-Design-Workshop#magic-commands)

#### Floorplan looks like 
![Screenshot (2241)](https://user-images.githubusercontent.com/120498080/214845584-dea9d8e4-fb48-465b-9a5c-ed9baf1e823b.png)
#### Zoom view of Floorplan and details of selected pin in tkcon
- Type "what" in tkcon to display information of selected object. These objects might be IO pin, decap cell, or well taps. Here the objects is IO pin

![Screenshot (2244)](https://user-images.githubusercontent.com/120498080/214846077-4d858625-a767-4cd3-9d66-fbe0e9a90d60.png)


#### Description of blocks of floorplan
![Screenshot (2245)1](https://user-images.githubusercontent.com/120498080/214863959-7be627aa-77ca-47fe-b1de-499cafec3822.png)

- **Taps Cells** are ment to avoid the lachup condition which occur in the cmos devices, they connect the nwell to thr vdd and the substrate to the ground. Here they are diagonaly equdistant which alraedy has ben set in readme file
- Floarplan do not take into considerations the placement of **standard cells**, but standard cells are present at the corner.

### Placement in OpenLANE
---
- **Library** consists of shape and size of all the standard cells(the different sizes of same standard cells is known as **drive strength**, lower size standard cell has lower driving strength), various functionality  of the same cells, timing and delay information of all the cells.
- We also keep decap cells, macros and IPs in Library.
#### Introduction to Placement 
- After floorplanning, next comes placement, it determines location of each of the components on the die. The standard cells that are present in the generated netlist are not the only cells that are placed. Placement enhances the design, removing any timing violations brought created  by the relative placement on the die.
- In placement we bind the netlist to a real-size physical cell. The physical standard cell will be taken from a library that offers various alternatives for the identical cells, shapes, dimensions, and delay.
- They are place it in the floorplaning(which have properly positioned input and output ports that are well designed) according to our netlist. To minimise timing delay, the flip flops must be positioned as close to the input and output pins as practicable.
- **The main goal of placement is to make sure Standard Cells are correctly placed in Standard Cells roots**

#### Initial placement of cells in our floorplan
![Screenshot (2247)](https://user-images.githubusercontent.com/120498080/214911889-1f9cdaef-141d-4f2c-b296-40d5c72587d3.png)

- To keep the **signal integrity**(Signal integrity or SI is a set of measures of the quality of an electrical signal. In digital electronics, a stream of binary values is represented by a voltage or current waveform.) we optimise placement, for that we calculate the wirelength and capacitance (C=εA/d) and then add **repeaters** and **buffers** based on those values. The wirelength will cause a capacitance and a resistance that will result in unwanted voltage drops and slew rates(slew is inversly proportional to capacitance) that might not be allowed for logic gates that switch current quickly. Inserting buffers for lengthy lines that serve as intermediaries and divide a single long wire into multiple ones would reduce resistance and capacitance. 
- If it needs to operate at a high frequency(2GHz), we occasionally also do **abutment**, in which logic cells are put very close to one another (virtually with zero delay). 
- Route crisscrossing is a common occurrence in PnR because the crisscrossed path can be implemented utilising a different metal layer (vias).

#### Optimised placement of cells in our floorplan after using buffers
![Screenshot (2250)](https://user-images.githubusercontent.com/120498080/214920991-fa503365-32ed-45bc-8cb8-a37d2955f372.png)

- After placement optimization, timing analysis will be set up using an ideal clock, which has no wire delays and no clock buffer-related delays because CTS has not yet been completed.

Placement is now focused on **congestion rather than timing**. Standard cells are also not placed on the floorplan stage; rather, they are placed on the placement stage. The cells that are placed on the floorplan stage are macros or preplaced cells. 

#### Placement is done on two stages:
- **Global Placement** is placement without legalisations with the intention of cutting down on wirelength.The main function of Global Placement is to reduce wirelengthand in OpenLANE use the concept of of the HPWL (Half Perimeter Wirelength) reduction concept.
- **Detailed Placement** is placement with legalisation(legalisation is more required for timing point of view), where the standard cells must be adjacent, in standard rows, and without overlaps.
#### Run Placement in OpenLANE
- To run Global Placement in OpenLANE run `% run_placement` command in openlane
- This command is a wrapper which does global placement (performed by RePlace tool),, optimization by Resier's  tool, and ReSer's detailed placement functions (by OpenDP tool). 
- It shows hundreds of iterations with HPWL and OVFL displayed. If the overflow is getting smaller, the algorithm is considered to be converging. It additionally verifies legality.



#### View the placement in magic
For visualising the layout following a placement, utilise the Magic Layout Tool. The following three files are necessary in order to examine a floor layout in Magic
- Technology File `sky130A.tech`
- Merged LEF file `merged.lef` 
- `vim picorv32a.floorplan.def` files

To open layout in magic use this command in this location
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_19-56/results/placement$

```
magic -T /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```
`-T <address_of_sky130A.tech_file>` where T is for technology file 
`lef read <address_of_merged.lef_file>` to read the lef files (we use `lef read` because its a standard industry file)
`def read <address_of_picorv32a.placement.def_file>` to read the def files (we use `def read` because its a standard industry file)
- **NOTE** If address of the required file is at the same working loaction then we just need to provide the required file name.
![image](https://user-images.githubusercontent.com/120498080/214942710-949bec91-df15-40a9-ad87-02f92be1cea9.png)

**NOTE**
This `sky130A.tech`(technology), `merged.lef`(layout exchange format) and `picorv32a.placement.def`(design exchange format) files comes allong with the pdk of sky130.
#### Placement looks like 
![Screenshot (2252)](https://user-images.githubusercontent.com/120498080/214943609-cf6ab423-0e71-47d2-8d3d-b967d0ccf2ed.png)
- So these many standard cells where in the bottom left corner in the initial layout of the floorplan which are now placed in our floorplan.

#### Zoom view of Placement of the Standard Cells
![Screenshot (2253)](https://user-images.githubusercontent.com/120498080/214943707-b38c7a7d-f154-452f-aee6-b2777cd2b341.png)

**NOTE** - This power distribution network gets created during floorplan but in OpenFLOW right now the order is little different, the floorplan does not create this power distribution network, it is done in Post CTS(Clock Tree Synthesis) just before we route it.



**COMPLETE SKY130_D2_SK2**











### Cell Design Flow
---
- In Cell design we will look at how a standard cell is designed in the library
#### Inputs to Cell Design Flow
![Screenshot (2254)](https://user-images.githubusercontent.com/120498080/215009991-36810191-01e5-413e-b25e-4ab90d4c974e.png)
![Screenshot (2257)](https://user-images.githubusercontent.com/120498080/215010259-f0653e2f-a4a1-4205-bd4f-4ff7efda4b98.png)
![Screenshot (2258)1](https://user-images.githubusercontent.com/120498080/215010171-00985091-1a73-49db-b6f0-a41816080446.png)
![Screenshot (2262)](https://user-images.githubusercontent.com/120498080/215012180-f3731816-d842-46e2-bac5-09cc4850f043.png)

**Library and Vser Defined Specs **
- **Cell height** has been defined as the seperation between power and the ground rail and it is the responsibilty of the cell develepor that cell height is mantained. Cell height depends on the timing information(if the cell height is high then it would be able to drive more longer wire, that is called higher drive strength cells)
- The standard cells has to operate at a certain **Supply Voltage** which is beign provided by the top level designer and accordingly the library devloper has to take that supply voltage and design the lobrary cell such that it specifies supply voltage.
- **Metal Layer**, **Pin Locations**, **Drawn Gate Length** requirments has to be decided by the library devloper.

#### Design steps Outputs of Cell Design Flow
![Screenshot (2265)](https://user-images.githubusercontent.com/120498080/215012572-65ed0b3c-ec01-4f26-bf4e-0f806042625b.png)
- **Circuit Design** step is mostly based on spice simulations.
- - From the circuit design step we get output called as **CDL(circuit discription language) file**
- So from circuit design step onse we known about the W/L rstios of nmos and pmos, then we need to implement it in layout design.
- **Art of Layout is Eular's path and stick diagram**

![Screenshot (2267)](https://user-images.githubusercontent.com/120498080/215018399-b678c4bb-a54e-4518-a82a-36590aced59e.png)
- In **Layout Design** first step is to get the logic implemented with the help of nmos and pmos transistors and get get a nmos and pmos nework graph out of our design and obtain the Eular's path(path wich has been traced only once) and then go for stick diagram out of it. 

![Screenshot (2268)](https://user-images.githubusercontent.com/120498080/215018430-bc2fe61b-6873-4169-8d7b-1be0c9692c5b.png)
- Later we convert this stick diagram into a proper layout according to the rules which we have got from the input.
- Final step is to extrace the paracitcs out of the final layout and characterize it in terms of timing.
- The output of the layout will be **GDSII(Graphic Design System II) file**, **LEF(Library Exchange Format ) file**(define width and heigt of the cell) and the **extraced spice netlist**(define resistance and capacitance of all the nodes)


### Characterization 
---
- Next step after we get the extraced step netlist and layout is characterization(*that's what this course is all about*)
- **Characterization** helps us to get timing, noise and power information.
- The outpit of characterization is **timing, noise, power.lib files** and the **functionality** of this circuit

#### Characterization Flow
1. Read in the models files
2. Read the extreaced spice netlist
3. Recognize the behaviour of the buffer
4. Read the sub circuits of the inverter
5. Attach the necessary power sources 
6. Apply the stimulus
7. Provide the necessary output capacitances 
8. Prove the necessary simulation commands
#### Characterization Setup
![Screenshot (2276)](https://user-images.githubusercontent.com/120498080/215031007-3ff42a3f-644f-4099-ac4f-e5e8bc1f9df5.png)
![Screenshot (2274)](https://user-images.githubusercontent.com/120498080/215031041-78346fad-4f69-4e82-9485-505c833e0ce8.png)

- Next step is to feed in all these inputs from 1 to 8 as in form of a configuration file to the characterization software called as **GUNA** and this software will generate timing, noise and power modles

![Screenshot (2277)](https://user-images.githubusercontent.com/120498080/215031065-79ccc805-1d5a-4fd8-907a-952628ef3357.png)
- The output of GUNA(.lib files) are characterized into :
1. Timing Characterization
2. Power Characterization
3. Noise Characterization
  
### Timing Characterization:
---
The slew timing parameters are listed below. Two inverters are connected in series, called as buffers(circuit is shown above)
#### Propogation Delay and Transition Time
![Screenshot (2282)](https://user-images.githubusercontent.com/120498080/215050048-c39ea804-b144-44da-8d7d-bdce9c96187f.png)
The timing parameters for propagation delay are listed below.
![Screenshot (2283)](https://user-images.githubusercontent.com/120498080/215050117-ddf12289-f379-482d-a7f1-c8dd51858358.png)
- **Propogation Delay** is defined as time {(out_thr)-time(in_thr)}
- **Transition Time** is defined as {time(slew_high_rise_thr)-time(slew_low_rise_thr)} or {time(slew_high_fall_thr)-time(slew_low_fall_thr)}
- It's unexpected to see negative propagation delay because the output occurs before the input. So in that case the designer must select the proper threshold value to create a positive delay. The typical delay threshold is 50% and slew low thresholds is 20% of Vdd and slew high threshold 80% of Vdd.


## Day 3
## Design library cell using Magic Layout and ngspice characterization
---
### Labs for CMOS inverter ngspice simulations
--- 
In this we would be going into depth of one of the cells(inverter cell), we won't build it from scratch rather we would use the github to get the `.mag`(magic) files and from there we will be doing Post Layout simulation in ngspice and post characterizing our sample cell, we would be plugging this cell into a OpenLANE flow, into picorv32a core.
#### NOTE - In IO placement in floorplan
On OpenLANE, configurations can be modified while in flight. On OpenLANE, for instance, use  `% set ::env(FP_IO_MODE) 2` to make IO mode not equidistant. On mode 2, the IO pins won't be evenly spaced out (default of 1). View the def layout for magic by launching floorplan once more with `% run floorplan`.  The configuration will only be available for the current session if it is changed on the fly; it will not be changed in `runs/config.tcl`, `echo $::env(FP_IO_MODE)` to output the variable's most recent value.

#### SPICE deck creation for CMOS inverter
First we need to design the library cells:

- The CMOS inverter's **SPICE deck** represents component connections (essentially a netlist).
- SPICE deck values equal the W/L value (0.375u/0.25u), which denotes a width of 375nm and a length of 250nm. PMOS shought to be 2 or 3 times broader in width than NMOS. Typically, the gate and supply voltages are multiples of length (in the example, gate voltage can be 2.5V)
- Place nodes around each component and give it a name. In SPICE, a component can be identified using this.

#### SPICE Deck Netlist Description
![Screenshot (2285)](https://user-images.githubusercontent.com/120498080/215094220-16d34ebc-1b33-4a18-a1ed-f82732176d9c.png)

- PMOS and NMOS descriptor syntax
    + `[component name] [drain] [gate] [source] [substrate] [transistor type] W=[width] L=[length]`
- Based on nodes and their values, all components are described.
**SIMULATION COMMANDS**
- `.op` `.dc Vin 0 2.5 0.05` is the start of SPICE simulation operation where Vin will be sweep from 0 to 2.5 with 0.05 steps
- `tsmc_025um_model.mod` is the model file which contain the technological parameters of the 0.25um NMOS and PMOS Devices.

<!---#### The steps to simulate in SPICE:

```
source [filename].cir
run
setplot 
dc1 
plot out vs in 
```

## D3 SK1 L2 AND L3 AND L4 --->












### CMOS Inverter Design using Magic
Magic Tool offers a very user-friendly interface for designing the different layers of the layout. Additionally, it features a built-in DRC check fetaure. A layout for a CMOS inverter with and without design rule breaches is shown in the excerpt below. Magic Layout Tool is used to design the inverter. 
- First `git clone https://github.com/nickson-jose/vsdstdcelldesign` (this will create "vsdstdcelldesign" directory)which contain the `sky130_inv.mag` and from there we will do post layout simulation.
- The technology file is used as an input is `sky130A.tech`. 
- Location of `sky130A.tech` file
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic$
- Then copy this file to
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ 

![image](https://user-images.githubusercontent.com/120498080/215192449-2306ab36-1490-4f7c-9197-8bb9c68aac97.png)
- Then run the following command to invoke magic in `.tech` and `.mac` file
```
magic -T sky130A.tech sky130_inv.mag &
```
`-T <address_of_sky130A.tech_file>` where T is for technology file. 
Here we use not `lef read` or `def read` as in for `.def` or `.lef` file because `.mag` is not a  standard industry file).
`&` is use to free the next command prompt.
- **NOTE** If address of the required file is at the same working loaction then we just need to provide the required file name.

![image](https://user-images.githubusercontent.com/120498080/215200075-60cdb6dd-0acc-476e-8520-d8b65a7ffd0b.png)

***NOTE** linux command to copy a file 
`cp <address_of_file_need_to_be_copied> <address_of_folder_it_need_to_be_pasted>`*
![image](https://user-images.githubusercontent.com/120498080/215191310-9e4d6bf7-50ee-4799-a933-d201e326f22d.png)
**NOTE**
- This `sky130_inv.mag`(magic) file is 
- This `sky130A.tech`(technology) file is from the pdk of sky130.

#### Layout of the CMOS Inveter in magic
![Screenshot (2288)](https://user-images.githubusercontent.com/120498080/215201506-270b58aa-88cc-4464-aa7e-ca452b694867.png)
#### To know about the particular block in layout 
- Selectet the particulat block and typw what is the "tkcon main" window

![Screenshot (2290)](https://user-images.githubusercontent.com/120498080/215248790-206b89a9-7947-4cd2-acf2-e72d89f707ce.png)

- Refer this [to build and inverter from scratch(workshop reference)](https://github.com/nickson-jose/vsdstdcelldesign)

#### Magic Commands:
[Video](https://www.youtube.com/watch?v=RPppaGdjbj0), [reference](http://opencircuitdesign.com/magic/) on layout using Magic.

- Left click = lower-left corner of box
- Right click = upper-right corner of box
- "z" = zoom in, "Z" = zoom out, "ctrl + z" = zoom into the box
- Middle click on empty area will turn the box into empty (similar to erasing it)
- "s" three times will select all geometries electrically connected to each other
- `:box` = display parameters of selected box
- `:grid` 0.5um 0.5um = turn on/off and set grid
- `:snap user` = snap based on current grid
- `:help snap` = display help for command
- `:drc style drc(full)` = use all DRC when doing DRC checking
- `:paint poly` = paint "poly" to current box
- `:drc why` = show drc violation inside selected area (white dots are DRC violations )
- `:erase poly` = delete poly inside the box
- `:select area` = select all geometries inside the box
- `:copy n 30` = copy selected geometries to North by 30 grid steps
- `:move n 1` = move selected geometries to North by 1 step ("." to move more, "u" to undo)
- `: select cell _08555_` = select a particular cell instance (e.g. cell _08555_ which can be searched in the DEF file)
- `:cellname allcells` = list all cells in the layout
- `:cellname exists sky130_fd_sc_hd__xor3_4` = check if a cell exists
- `:drc why` = show DRC violation and also the DRC name which can be referenced from [Sky130 PDK Periphery Rules](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#rules-periphery--page-root).


### Characterizing the cell's(CMOS Inverter) slew rate and propagation delay
To get the slew rate and propagation delay of the CMOS Inverter we need to plot the graphs and for that first we extrace the `.spice` file from the layout from designed magic layout.
#### Steps to extrace the spice file from magic
Now to know the logical functioning of the inverte we extrace the spice and do simulation in ngspice open source tool.

![image](https://user-images.githubusercontent.com/120498080/215249994-d33e906b-1560-4ab7-a7be-5c45f5e9e50d.png)

- Use this command `extract all`to create an `.ext`(extraction) file.
- Use this command `ext2spice cthresh 0 rthresh 0` then `ext2spice` to create the `.spice` file from `.ext` file, to be used with our ngspice tool and also extrace all the paracitic capacitances.

#### `sky130_inv.spice` file extraced from magic
![image](https://user-images.githubusercontent.com/120498080/215250083-2d7d91c7-1797-4d62-ba6b-e9c59a351bc4.png)

- We then modify the `sky130_inv.spice` file as shown below to be able to plot a transient response:
![image](https://user-images.githubusercontent.com/120498080/215252810-98f2337f-17a7-41ba-b5e6-67e28a8d2ec0.png)

```verilog
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

//.subckt sky130_inv A Y VPWR VGND
X0 Y A VGND VGND nshort_model.0 ad=0 pd=0 as=0 ps=0 w=35 l=23
X1 Y A VPWR VPWR pshort_model.0 ad=0 pd=0 as=0 ps=0 w=37 l=23
C0 Y A 0.05fF
C1 Y VPWR 0.11fF
C2 A VPWR 0.07fF
C3 Y VGND 0.24fF
C4 VPWR VGND 0.59fF
//.ends

* Power supply 
VDD VPWR 0 3.3V 
VSS VGND 0 0V 

* Input Signal
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)

* Simulation Control
.tran 1n 20n
.control
run
.endc
.end
```
***NOTE** To edit vim file in linux press `i` and make the changes then press `Esc` and the `:wq!` to save the changes*

- Then when we run in ngsice then we got this errors 
![image](https://user-images.githubusercontent.com/120498080/215252912-1c7e8ad3-69ad-43ee-a93b-1acf01bf1b44.png)

- So then we use below files (which was shown in the video) and we got the plots
- To 0pen the spice file by typing `ngspice sky130_inv_new.spice` and to generate a graph using `plot y vs time a` :

```verilog
* SPICE3 file created from sky130_inv_new.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

* .subckt sky130_inv A Y VPWR VGND
M0 Y A VGND VGND nshort_model.0 ad=1435 pd=152 as=1365 ps=148 w=35 l=23
M1 Y A VPWR VPWR pshort_model.0 ad=1443 pd=152 as=1517 ps=156 w=37 l=23
C0 A VPWR 0.08fF
C1 Y VPWR 0.08fF
C2 A Y 0.02fF
C3 Y VGND 2fF
C4 VPWR VGND 0.74fF
* .ends

* Power supply 
VDD VPWR 0 3.3V 
VSS VGND 0 0V 

* Input Signal
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)

* Simulation Control
.tran 1n 20n
.control
run
.endc
.end
```
- `\vsdstdcelldesign-master\libs\nshort.lib` and `\vsdstdcelldesign-master\libs\nphort.lib` are the spice model files for nmos and pmos
- We change C3(Cload) to 2fF(increase it) to get the higher delay
#### Plots we got after ngspice simulation
![Screenshot (2293)](https://user-images.githubusercontent.com/120498080/215253787-87328018-577a-4b19-91b2-b8439818fdcb.png)

- In ngspice to zoom in to particulat region in graph: right click and drag in that region.
- In ngspice to get the graph coordinate click on that point.


Using this transient response, we will now characterize the cell's slew rate and propagation delay:
- Rise Time [output transition time from 20%(0.66V) to 80%(2.64V)]:
> Rise Time = 2.24417 - 2.1828 = 0.0618 ns
- Fall Time [ouput transition time from 80%(2.64V) to 20%(0.66V)]:
> Fall Time = 8.09512 - 8.0679 = 0.02722 ns
- Rise Delay [delay between 50%(1.65V) of input to 50%(1.65V) of output]:
> Rise Delay = 6.15069 6.15 = 0.00069 ns
- Fall Delay [delay between 50%(1.65V) of input to 50%(1.65V) of output]:
> Fall Delay = 8.07770- 8.05075 = 0.02695 ns

- The characterization done above was done at 27 C.

**So now we have characterized our inverter.**
The next goal is to make a `.lef` file using this inverter architecture. We will create a unique cell by plugging this `.lef` into openlane and plugin in this cell into our picorv32a core.












***NOTE** Linux command to download  a file `wget <url_of_the_file_to_be_downloaded>`*
`wget https://skywater-pdk.readthedocs.io/en/main/`






<!---##  SKY130_D3_SK3 L3 to L9--->









## DAY 4
---
## Pre-layout timing analysis and importance of good clock tree
---
So till now we are done with the design setup,the floorplan, placement and lastly we have learned, given a `.mac` file how to extrace the `.spice` out of it and do the characterization. We where looking into ngspice and magic and now lets see how it is connected to OpenLANE(which is a place and route tool, and for placement of any cell we don't require the `.mac` file simulation(`.mac` file contain all the information in magic) but we only require is the PnR boundary(which contain the power and the rail, input and the output informantion) and that is the `.lef` files). So `.lef` file proctects our ir and macro information.
- **`.lef` files {Library Exchange Format (LEF)}** is a specification for representing the physical layout of an integrated circuit in an ASCII format. It includes design rules and abstract information about the standard cells.LEF only has the basic information required at that level to serve the purpose of the concerned CAD tool. It helps in saving valuable resources by providing only an abstract view and thus consuming less memory overhead. LEF is used in conjunction with Design Exchange Format (DEF) to represent the complete physical layout of an integrated circuit while it is being designed.

![Screenshot (2300)](https://user-images.githubusercontent.com/120498080/215275249-a122a31b-d205-40a4-9a0c-d038bb99550c.png)

So next we will extrace a `.lef` file out of this `.mag` file(designed by us) and then we will plug this lef file into the picorv43a flow(till now we was workid with pre build cells) 


But first, we must follow to the PnR tool's instructions for standard cells:
- The intersection of the horizontal and vertical tracks is where the input and output ports are located (ensure the routes can reach that ports).
- The standard cell must have a height that is an odd multiple of the tracks vertical pitch and a width that is an odd multiple of the tracks horizontal pitch.


To check these guidelines, we need to change the grid of Magic to match the actual metal tracks. The `pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/tracks.info` contains those metal informations.

- In the tkon terminal, use the `grid` command to match the track information.

![Screenshot (2301)](https://user-images.githubusercontent.com/120498080/215279771-1d996a58-8a86-4b0f-8f79-24e0baf9979e.png)

- The grids indicate the only locations for the local-internet layer routing, and the wire pitch needed is indicated by the distance between grid lines. The following demonstrates that the requirements are met.

![Screenshot (2302)](https://user-images.githubusercontent.com/120498080/215279845-f51bab17-45eb-4399-b6b2-5e37dc6bea61.png)

- The LEF file will then be extracted. Cell size, port definitions, and other properties that help the placer and router tool are contained in the LEF file. Thus, it is necessary to first set the port's definition, port class, and use. 
- The [vsdstdcelldesign repository](https://github.com/nickson-jose/vsdstdcelldesign#create-port-definition) contains the instructions to **covert lables to ports**(declares pin as macro) using Magic.(this is alredy been done here)
- So after defining the ports we go for [**Port class and Port use**](https://github.com/nickson-jose/vsdstdcelldesign#Set-port-class-and-port-use-attributes-for-a-layout) which tells the tool about the perpous of the ports.(e.g. it is input post, output port, etc)(this is alredy been done here)

- Now, to save the mag file as `sky130 myinverter.mag` type `save sky130_myinverter.mag` in tkcon terminal.
- Then, on the tkcon terminal, type `lef write` and a lef file with the same name as the mag file `sky130_myinverter.lef` will be produced. 

![image](https://user-images.githubusercontent.com/120498080/215283803-642ff908-9929-416f-b0f4-b77c31e28b6b.png)

- Here is the LEF file

![image](https://user-images.githubusercontent.com/120498080/215284142-68551b1f-b024-45a3-b4d9-a94c10a3a204.png)

### Plug-in the Customized Inverter Cell(lif file) to OpenLane
- Next is to plug this lef file into our picorv32a flow (before that we will more our file to src folder)
> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src$

- Provided inside the cloned `vsdstdcelldesign` are the liberty files containing the customized inverter cell.
- Copy the extracted lef file `sky130_myinverter.lef` from `/home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/` and the liberty files `sky130*.lib` from `/home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/libs/` to the src directory of picorv32a in
> /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/libs/

**NOTE** `sky130_fd_sc_hd__typical.lib`,`sky130_fd_sc_hd__slow.lib` and `sky130_fd_sc_hd__fast.lib` are the typical, slow and fas corner timing library file provided by sky130 which are at loaction and would we requied for STA analysis
> /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/libs/

- Add the folowing to `config.tcl` inside the picorv32a:
> /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/
```verilog
# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set ::env(FP_CORE_UTIL) 65
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3


set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
source $filename
}
```
- This sets the liberty file that will be used for ABC mapping of synthesis (`LIB_SYNTH`) and for STA (`_FASTEST`,`_SLOWEST`,`_TYPICAL`) and also the extra LEF files (`EXTRA_LEFS`) for the customized inverter cell. 

#### `config.tcl` file should contain following information
![image](https://user-images.githubusercontent.com/120498080/215290835-5d1ede56-c7ea-457b-9623-15a2d9b70de2.png)

- Run docker and prepare the design picorv32a. Plug the new lef file to the OpenLANE flow via:
```verilog
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
```
- These command are to ensure that when `/home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/29-01_06-33/tmp/merged.lef` created 
- Next do `run_synthesis`. 
- After the run, and as we can see `sky130_myinverter` cell is successfully included in the design

![image](https://user-images.githubusercontent.com/120498080/215291031-ddea6258-4a59-4a3c-99f0-700d709787b5.png)
- Synthesis statistics report will be generated at

> /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-01_20-55/reports/synthesis/

- So we got Total Negative Slack and Worst Negative Slack is given below

![image](https://user-images.githubusercontent.com/120498080/215291266-c3cfb2ce-4552-4e5d-86e6-fe55230603ae.png)

- So here we are not meeting the timing constrains. Hence our next goal is to solve this negative slack

### Delay

Problem:
- The capacitance or the load at the output node of each and every buffer in the complete clock tree is varying.
- Also if the load is varying the input transition is varying.

To avoid large skew between endpoints of a clock tree (happening due to signal arrives at different point in time):
- After splitting the buffers.
- Buffers on the same level must have same capacitive load to ensure same timing delay or latency on the same level. It means that each buffer at the same level is having same load.
- Buffers on the same level must also be the same size (different buffer sizes -> different W/L ratio -> different resistance -> different RC constant -> different delay). It means that the buffer at same level should be of same size.

![Screenshot (2304)](https://user-images.githubusercontent.com/120498080/215292143-0514595b-a74d-47aa-a969-693620d905be.png)

Solution:
- **Delay tables** are the solution. Delay tables are 2D table. Delay of a component is characterised and summaries in a table.
- The timing model of each cell is recorded and is summarised in delay tables, which are part of the liberty file. The output slew is the main cause of delay. Capacitive load and input slew are also factors that affect output slew. The input slew has its own transition delay table and is a function of the previous buffer's output cap load and input slew.

![Screenshot (2305)](https://user-images.githubusercontent.com/120498080/215292161-f2c410f1-1a33-4eeb-bb6c-a63bcc2a9479.png)

### Fix Negative Slack

- Let's try to fix the slack. Currently the value of slack is
```
tns (total negative slack) = -711.59
wns (worst negative slack) = -23.89
```
- Let us change some variables to minimize the negative slack. We will now change the variables "on the flight". 
- Use `echo $::env(SYNTH_STRATEGY)` to view the current value of the variables before changing it:
```verilog
echo $::env ([Varible]) // our case = SYNTH_STRATEGY
// change the STRATEGY
```
- Slack has to be positive always and negative slack indicates a violation in timing so we will try to maintain a balance between the delay and the area (SYNTH_STRATEGY).

```verilog
% echo $::env(SYNTH_STRATEGY)
AREA 0
% set ::env(SYNTH_STRATEGY) "DELAY 0"      
DELAY 0
% echo $::env(SYNTH_STRATEGY)
1
% echo $::env(SYNTH_BUFFERING)
1
% echo $::env(SYNTH_SIZING)
0
% echo $::env(SYNTH_DRIVING_CELL)
sky130_fd_sc_hd__inv_8
```
  
- With `SYNTH_STRATEGY` of `Delay 0`, the tool will focus more on optimizing/minimizing the delay, index can be 0 to 3 where 3 is the most optimized for timing (sacrificing more area). `SYNTH_BUFFERING` of 1 ensures cell buffer will be used on high fanout cells to reduce delay due to high capacitance load. `SYNTH_SIZING` of 1 will enable cell sizing where cell will be upsize or downsized as needed to meet timing. `SYNTH_DRIVING_CELL` is the cell used to drive the input ports and is vital for cells with a lot of fan-outs since it needs higher drive strength (larger driving cell needed). 
- Use readme files to know about these
![image](https://user-images.githubusercontent.com/120498080/215314890-fdfab8f4-a344-4091-8b56-6885fbbe46fd.png)

- Then we do `run_synthesis` and we got and error, but now are ignoring this (slag issue) and move forward
- So the changed value of slack is
```
tns (total negative slack) = 00
wns (worst negative slack) = 00
```
![image](https://user-images.githubusercontent.com/120498080/215343915-f694c0b9-a321-42b8-8b32-58d10b1d9d18.png)

- **NOTE** If the salck values are not changing then delete the genetered `.v` file under synthesis and run again.

#### Error we got in running synthesis after doing the changes 

![image](https://user-images.githubusercontent.com/120498080/215315495-c2118ccd-1307-44ee-87e2-bc673df827dc.png)
- Solution is to delete `.v` file under synthesis and run again.

### Floorplanning and Placement

- Now to run floorplaning we use `run_floorplan` but we got this error

![image](https://user-images.githubusercontent.com/120498080/215316219-86bf602b-b34b-400c-b9fa-23a4c4d14026.png)

- So we go with the following commands to do floorplanning and placement: 
```vereilog
init_floorplan
place_io
global_placement_or
detailed_placement
tap_decap_or
detailed_placement

```
- Then check the `merged.lef` wheather it has integrated that `sky130_myinverter.lef` file which was created when we create our inverter `.mag` file
> `/home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/29-01_06-33/tmp/merged.lef`

![image](https://user-images.githubusercontent.com/120498080/215319017-acb4c543-a62e-4da2-8144-d8a4628030a0.png)
- Then check the file which is created. Go to the placements folder under results and then invoke the magic tool and load the def file. The command is:
```
magic -T /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![image](https://user-images.githubusercontent.com/120498080/215340978-48eece8c-d907-4331-a47b-8422f607e6fe.png)

- Placement of our design

![Screenshot (13)](https://user-images.githubusercontent.com/120498080/215499423-5ba3de1c-fb8c-4624-9110-cacbdd7acc05.png)

- **NOTE - We where not able to find our inverter because I save `.lsv` file as 'myinverter.lsv` insted of `sky30_vsdinv.lsv`**
- So we agarin generate the file as `sky30_vsdinv.lsv` then we where able to find the inverter.
- Zoom view of 'sky130_vsdinv` inverter in our design

![image](https://user-images.githubusercontent.com/120498080/215341238-6438b9c2-8581-4252-a142-830df3abc578.png)
 




<!---
# THEOTY
```
Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree
SKY130_D4_SK2 - Timing analysis with ideal clocks using openSTA
SKY_L1 - Setup timing analysis and introduction to flip-flop setup time


Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree
SKY130_D4_SK2 - Timing analysis with ideal clocks using openSTA
SKY_L2 - Introduction to clock jitter and uncertainty
```
--->


### Setup Timing Analysis 
We will do Timing Analysis with ideal clock(the clock tree is not build) first to understand what are the basic structure and then we will be using the real clocks and doing the timing analysis


### Setup timing analysis in OpenLANE 
- This is done only when after run placement the timing constrain are not met (in our case timing constrains where met then also we do it for confirmation) if we dont get the desiret timing constrains then we need to optimize it 
- In cts we try to change the netlist by making clock tree.
Making the `pre_sta.conf` and save it in the openlane folder.
```
set_cmd_units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read_liberty -min /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib
read_liberty -max /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib
read_verilog /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/29-01_16-21/results/synthesis/picorv32a.synthesis_cts.v
link_design picorv32a
read_sdc /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/my_base.sdc
report_checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns
```
![image](https://user-images.githubusercontent.com/120498080/215347570-0bf8b25b-4a08-4149-bc61-8d0e978afa8c.png)

- After cts new .v files start getting created.
- Creating `my_base.sdc` and save this file in the src folder of picorv32a folder.
- This `.sdc` file - An ASCII text file (with the extension . sdc) that contains design constraints and timing assignments in the industry-standard Synopsys® Design Constraints format
```
set ::env(CLOCK_PORT) clk
set ::env(CLOCK_PERIOD) 12.000
set ::env(SYNTH_DRIVING_CELL) sky130_fd_sc_hd__inv_8
set ::env(SYNTH_DRIVING_CELL_PIN) Y
set ::env(SYNTH_CAP_LOAD) 17.65
create_clock [get_ports $::env(CLOCK_PORT)]  -name $::env(CLOCK_PORT)  -period $::env(CLOCK_PERIOD)
set IO_PCT  0.2
set input_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
set output_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
puts "\[INFO\]: Setting output delay to: $output_delay_value"
puts "\[INFO\]: Setting input delay to: $input_delay_value"


set clk_indx [lsearch [all_inputs] [get_port $::env(CLOCK_PORT)]]
#set rst_indx [lsearch [all_inputs] [get_port resetn]]
set all_inputs_wo_clk [lreplace [all_inputs] $clk_indx $clk_indx]
#set all_inputs_wo_clk_rst [lreplace $all_inputs_wo_clk $rst_indx $rst_indx]
set all_inputs_wo_clk_rst $all_inputs_wo_clk


# correct resetn
set_input_delay $input_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] $all_inputs_wo_clk_rst
#set_input_delay 0.0 -clock [get_clocks $::env(CLOCK_PORT)] {resetn}
set_output_delay $output_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] [all_outputs]

# TODO set this as parameter
set_driving_cell -lib_cell $::env(SYNTH_DRIVING_CELL) -pin $::env(SYNTH_DRIVING_CELL_PIN) [all_inputs]
set cap_load [expr $::env(SYNTH_CAP_LOAD) / 1000.0]
puts "\[INFO\]: Setting load to: $cap_load"
set_load  $cap_load [all_outputs]

```
- This is replicating the same results as we had after run synthesis stage `pre_sta.conf` will be the fill on which we will be doing our STA analysis.
- To perform pre STA run the command `sta pre_sta.conf` by opening the terminal in openlane folder which is inside the openlane_working_dir.

> abhinavprakash1999@vsd-pd-workshop-01:~/Desktop/work/tools/openlane_working_dir/openlane$ sta pre_sta.conf

#### Setup Timing Analysis timing contrains met

![image](https://user-images.githubusercontent.com/120498080/215351729-106666bb-6f4a-4c59-9415-a72ca4f9411c.png)
- If timing constrains are not met then we need to optimize it.


### Run CTS(Clock Tree Synthesis) using TritonCTS

There are three parameters that we need to consider when building a clock tree:

- Clock Skew = In order to have minimum skew between clock endpoints, clock tree is used. This results in equal wirelength (thus equal latency/delay) for every path of the clock.
- Clock Slew = Due to wire resistance and capacitance of the clock nets, there will be slew in signal at the clock endpoint where signal is not the same with the original input clock signal anymore. This can be solved by clock buffers. Clock buffer differs in regular cell buffers since clock buffers has equal rise and fall time.
- Crosstalk = Clock shielding prevents crosstalk to nearby nets by breaking the coupling capacitance between the victim (clock net) and aggresor (nets near the clock net), the shield might be connected to VDD or ground since those will not switch. Shileding can also be done on critical data nets.

![Screenshot (15)](https://user-images.githubusercontent.com/120498080/215502778-8dc2beae-43b0-4b24-ba37-92cb467a903d.png)


<!---
```
Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree
SKY130_D4_SK3 - Clock tree synthesis TritonCTS and signal integrity
SKY_L1 - Clock tree routing and buffering using H-Tree algorithm

Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree
SKY130_D4_SK3 - Clock tree synthesis TritonCTS and signal integrity
SKY_L2 - Crosstalk and clock net shielding
``` --->

- So with Setup Timing Analysis using `my_base.sdc` we confirm that setup time has been met so we go for Clock Tree Synthesis
- Then use `run_cts` command in openlane to run clock tree synthesis.

![image](https://user-images.githubusercontent.com/120498080/215356354-1b3d382c-237a-4a73-97eb-414e4151ab91.png)

- This will create `picorv32a.cts.def` and `picorv32a.cts.def.png`woll be created at

> /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/29-01_16-21/results/cts/

#### `picorv32a.cts.def.png` file

![image](https://user-images.githubusercontent.com/120498080/215356520-0e5200b8-0c98-4651-8484-e25527562412.png)


#### How exactly `run_cts` command in openlane
- The `run_cts` and the other OpenLane commands are actually just calling the tcl proc (procedure) inside `/OpenLane/scripts/tcl_commands/`. This tcl procedure will then call OpenROAD to run the actual tool. For example, `run_cts` can be found inside `/OpenLane/scripts/tcl_commands/cts.tcl`, this tcl procedure will call OpenROAD and will call `/OpenLane/scripts/openroad/cts.tcl` which contains the OpenROAD commands to run TritonCTS.
![image](https://user-images.githubusercontent.com/120498080/215357296-a3d16381-d607-4695-b670-fa679c195d2e.png)

Inside the `/OpenLane/scripts/openroad/cts.tcl` contains the configuration variables for CTS. Notables ones are:

- `CTS_CLK_BUFFER_LIST` = list of clock branch buffers (`sky130_fd_sc_hd__clkbuf_8` `sky130_fd_sc_hd__clkbuf_4` `sky130_fd_sc_hd__clkbuf_2`)
- `CTS_ROOT_BUFFER` = clock buffer used for the root of the clock tree and is the biggest clock buffer to drive the clock tree of the whole chip (`sky130_fd_sc_hd__clkbuf_16`)
- `CTS_MAX_CAP` = maximum capacitance of the output port of the root clock buffer.


<!--- for detail - D4_SK3_L4 and ****--->
![image](https://user-images.githubusercontent.com/120498080/215358032-6b787abc-1f55-462b-bf55-ea938d876c59.png)


### Timing Analysis with Real Clocks

Setup and hold analysis with real clock will now include clock buffer delays:

- In setup analysis, the point is that the data must arrive first before the clock rising edge to properly latch that data. Setup violation happens when path is slow. This is affected by parameters such as combinational delay, clock buffer delay, time period, setup time, and setup uncertainty (jitter).

- Hold analysis is the delay that the MUX2 model inside the flip flop needs to move the data to outside. This is the time that the launch flop must hold the data before it reaches the capture flop. Hold analysis is done on the same rising clock edge for launch and capture flop unlike in setup analysis where it spans between two rising clock edges. Hold violation happens when path is too fast. This is affected by parameters such as combinational delay, clock buffer delays, and hold time. (time period and setup uncertainty does not matter since launch and capture flops will receive the same rising clock edges fo hold analysis)

The goal is to have a positive slack on both setup and hold analysis.

![Screenshot (16)](https://user-images.githubusercontent.com/120498080/215503412-118716b9-d510-454d-bc8e-d590afb86294.png)



``` see also 
Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree
SKY130_D4_SK4 - Timing analysis with real clocks using openSTA
SKY_L1 - Setup timing analysis using real clocks

Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree
SKY130_D4_SK4 - Timing analysis with real clocks using openSTA
SKY_L2 - Hold timing analysis using real clocks
```


### Multi-corner STA for Post-CTS:
- We will now do STA for post clock tree synthesis to include effect of clock buffers. 
- Similar to pre-layout STA(analysis of clock tree of our circit), this will done on OpenROAD (which will then call OpenSTA), so we enter into openroad and through this we can use the env variables 

- **NOTE** - openroad is the part of openlane and opensta integrated on openroad.


- In the terminal in which we run the run_cts command there only go to openroad. Type the following command in the terminal.
```
openroad
```
- This will open the open road. Our objective to do the analysis of the entire circut where clock tree has been build now. Now we will open OpenSTA here. For timing alnalysis.
1. We first create a db `
2. db is create using lef and def file. In our analysis we use these db. (It is a one time process. Whenever lef changes we have to change the db)
- To create a db
All the loaction should be after `/openlane/.....`
``` verilog
// first read lef (it is inside the tmp folder (merged.lef)
read_lef /openLANE_flow/designs/picorv32a/runs/29-01_16-21/tmp/merged.lef
// secondly read def (it is present inside cts folder present under the results folder/cts)
read_def /openLANE_flow/designs/picorv32a/runs/29-01_16-21/results/cts/picorv32a.cts.def
// creating db [pico_cts.db (created under the openlane folder)]
write_db pico_cts.db 
// reading db 
read_db pico_cts.db 
//  reading verilog (it is present inside cts folder present under the results/synthesis/picorv32a.synthesis_cts.v)
read_verilog /openLANE_flow/designs/picorv32a/runs/29-01_16-21/results/synthesis/picorv32a.synthesis_cts.v
// reading library (max)
read_liberty -max $::env(LIB_FASTEST)
// reading library (min)
read_liberty -min $::env(LIB_SLOWEST)
// reading sdc
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
// now the clock has been generated 
set_propagated_clock [all_clocks]
// report
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

- `write_db` and `read_db` is done before running STA tool, this creates a database file using LEF file and resulting DEF file of the last stage.
- Multi-corner STA must read both min library (for hold analysis) and max library (for setup analysis) unlike in single corner STA where only the typical library is read.
- SDC file used is the same for single and multi-corner.
- Since this is post-CTS STA, `set_propagated_clock` is used. `set_propagated_clock` propagates clock latency throughout a clock network, resulting in more accurate skew and timing results throughout the clock network. This is done postlayout, after final clock tree generation, unlike in prelayout where ideal clock is used thus no clock latency.

Also instead of manually running these commands, we can just simply do source `/openlane/scripts/openroad/sta_multi_corner.tcl` inside OpenROAD which runs the readily-made tcl script of OpenROAD commmands for running multi-corner STA. The result might be slightly different from the result above since the settings for `sta_multi_corner.tcl` is much more comprehensive.

#### Run Commands
![image](https://user-images.githubusercontent.com/120498080/215362781-e4fd7af0-7e32-4d49-8c74-2da4025aa7c0.png)
![image](https://user-images.githubusercontent.com/120498080/215362695-17179941-5dd1-43a9-bc88-1a35936ea1a3.png)

#### Setup Slack constrains are met
![image](https://user-images.githubusercontent.com/120498080/215362537-a95ec0b0-0089-4a99-8a2d-fee31e5bfc25.png)
#### Hold Slack constrains are met
![image](https://user-images.githubusercontent.com/120498080/215363145-82979f79-5f0b-45d0-908e-064f4aee2a6c.png)

- The above is done for typical cornor but we are seeing it for minimum and maximum cornor hence the analysis is **not correct**.
#### So now we perform Multi-corner STAfor typical cornor

- to exit the openroad:
```
exit
```

- So this time we will use the `typical` cornor. So do the same process from the read db.
```verilog
openroad
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/29-01_16-21/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```
Slack for typical cornor

#### Run Commands
![image](https://user-images.githubusercontent.com/120498080/215364791-1f9471e0-4dad-473d-b55c-efebbae85bfa.png)
![image](https://user-images.githubusercontent.com/120498080/215364828-22dff2c4-57bd-4ab2-b915-24fa1800189e.png)


#### Setup Slack constrains 
![image](https://user-images.githubusercontent.com/120498080/215364383-19d89968-3ebd-4a88-b519-27347a07c721.png)

#### Hold Slack constrains 
![image](https://user-images.githubusercontent.com/120498080/215364677-5389748c-377e-40aa-8806-a364e9747bbf.png)

#### Skew
Skew is the time delta between the actual and expected arrival time of a clock signal. Skew can be either extrinsic or intrinsic. The latter is internal to the driver (generator circuitry) and defined as the difference in propagation delays between the device outputs.
#### Hold and Setup Skew Report 
- It should be less that the 10% time period of clock
![Screenshot (17)](https://user-images.githubusercontent.com/120498080/215508032-2a19d5c9-d7c8-4007-aa29-93d1de79f8cc.png)


## DAY 5
---
## Final Steps for RTL2GDS using TritonRoute and OpenSTA
---
### Maze Routing:
One simple routing algorithm is Maze Routing or Lee's routing:

- The shortest path is one that follows a steady increment of one (1-to-9 on the example below). There might be multiple path like this but the best path that the tool will choose is one with less bends. The route should not be diagonal and must not overlap an obstruction such as macros.
- This algorithm however has high run time and consume a lot of memory thus more optimized routing algorithm is preferred (but the principles stays the same where route with shortest path and less bends is preferred)

![Screenshot (8)](https://user-images.githubusercontent.com/120498080/215485617-d0bf5ea1-4111-4927-9230-7e8d481684c2.png)

### DRC Cleaning:
DRC cleaning is the next step after routing. DRC cleaning is done to ensure the routes can be fabricated and printed in silicon faithfully. Most DRC is due to the constraints of the photolitographic machine for chip fabrication where the wavelength of light used is limited. There are thousands of DRC and some DRC are:

1. Minimum wire width
2. Minimum wire pitch (center to center spacing)
3. Minimum wire spacing (edge to edge spacing)
4. Signal short = this can be solved my moving the route to next layer using vias. This results in more DRC (Via width, Via Spacing, etc.). Higher metal layer must be wider than lower metal layer and this is another DRC.

![Screenshot (9)](https://user-images.githubusercontent.com/120498080/215486206-4265d2d3-c244-432a-8c9b-6de667f5c144.png)

### Power Distribution Network (review):
This is just a review on PDN. The power and ground rails has a pitch of 2.72um thus the reason why the customized inverter cell has a height of 2.72 or else the power and ground rails will not be able to power up the cell. Looking at the LEF file `runs/[date]/tmp/merged.nom.lef`, you will notice that all cells are of height 2.72um and only width differs.

As shown below, power and ground flows from power/ground pads -> power/ground ring-> power/ground straps -> power/ground rails.
![Screenshot (10)](https://user-images.githubusercontent.com/120498080/215486669-b716ead1-8962-45bc-abaa-382889c8a05b.png)

### Routing Stage and TritonRoute:
OpenLane routing stage consists of two stages:

- Global Routing - Form routing guides that can route all the nets. The tool used is FastRoute
- Detailed Routing - Uses the global routing's guide to actually connect the pins with least amount of wire and bends. The tool used is TritonRoute.
#### Triton Route

- Performs detailed routing and honors the pre-processed route guides (made by global route) and uses MILP based (Mixed Integer Linear Programming algorithm) panel routing scheme(uses panel as the grid guide for routing) with intra-layer parallel routing (routing happens simultaneously in a single layer) and inter-layer sequential layer (routing starts from bottom metal layer to top metal layer sequentially and not simultaneously).
- Honors preferred direction of a layer. Metal layer direction is alternating (metal layer direction is specified in the LEF file e.g. met1 Horizontal, met2 Vertical, etc.) to reduce overlapping wires between layer and reduce potential capacitance which can degrade the signal.

![Screenshot (11)](https://user-images.githubusercontent.com/120498080/215487079-16ac1575-6a58-4a26-a3bc-e05007346013.png)


### Run PDN(Power Distribution Network)

#### The command to load the previous files (basically whatever you have done).
```verilog
cd work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a -tag 29-01_18-06
// if we include new configuration i.e., edit the config file then we have to do overwrite
prep -design picorv32a -tag 29-01_18-06 -overwrite 

// to check the last def file created i.e., last def
echo $::env(CURRENT_DEF)
```

- Next to run **PDN(Power Distribution Network)** use `gen_pdn` (this we do in floorplaning but in openlane its done after `run_cts` clock tree synthesis )

![image](https://user-images.githubusercontent.com/120498080/215370061-41ca31db-e575-47a7-ae43-bfc09e30535b.png)
![image](https://user-images.githubusercontent.com/120498080/215369640-a5749d0f-f506-4992-8c7f-b812cbd619cf.png)
- Standard cell are to be placed between the rails. We should ensure that the height of standard cell should be in the multiples of 2.72, so that we can have both VDD and VSS for each of the standard cells.

- The power distribution of the chipset is shown below. Power should be supplies from the verticle straps to the standard cell rails. Similarly power should go to the macro.
- The tmp folder consists of all the def files of each stage.
- `pdn.def` consists the cts,def plus it's own values
- The `pdn.def` file is generated at 
> /home/abhinavprakash1999/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/29-01_16-21/results/floorplan/


- ***NOTE** To load the previous program in docker we use `prep -design picorv32a -tag 29-01_16-21` and we have overwrite the `config.tcl`, in that folder then we use `prep -design picorv32a -tag 29-01_16-21 -overwrite`
- ***NOTE** To know about the last run use `echo $::env(CURRENT_DEF)` 

![image](https://user-images.githubusercontent.com/120498080/215369024-15be493b-b685-4e56-8302-8b410a379628.png)


### Routing Stage

We will now finally do the routing, simply run `run_routing`. This will do both global and detailed routing, this will take multiple optimization iterations until the DRC violation is reduced to zero. The zeroth iteration has 27426 violations and only at the 8th iteration was all violations solved. The whole routing took 1 hour and 10 mins in my Linux machine with 2 cores. A fun fact: the die area is just 584um by 595um but the total wirelength used for routing spans to 0.5m!!!

![image](https://user-images.githubusercontent.com/120498080/215376334-5cb667da-b3c8-43ba-b384-9354587ac1ec.png)
 - Then following files are genereted in results 
![image](https://user-images.githubusercontent.com/120498080/215375412-8d787238-9e25-47d0-9041-296c87fdb963.png)

- After routing we get number of violations, which can be verified by checking the the .drc file under routing/16-tritoRoute.drc. (We se a blank drc file).

- Exctracting SPEC (SPEC extraction is done outside openlane as it does not have SPEC Extractor tool in openlane.

#### `picrrv32a.def.png` file generated after routing
![Screenshot (12)](https://user-images.githubusercontent.com/120498080/215491503-0c34caff-652b-4ee3-8dea-9eef2501fdc8.png)

#### Extraction of GRSII file
- Finally do `run_magic` in the openlane to generate gds file.
 
![image](https://user-images.githubusercontent.com/120498080/215446273-dbbc1469-683b-45ca-8efe-45d7808647d3.png)
Then following files are genereted in results 
![image](https://user-images.githubusercontent.com/120498080/215445621-b6d87fe3-286c-4c7d-8938-1d5b3fd1b7bc.png)

#### Generated GDSII file `picrrv32a.gds.png` file 
![Screenshot (2323)](https://user-images.githubusercontent.com/120498080/215493857-9d5d7d43-c654-437f-9919-3cff9878ee23.png)

 

### All commands to run in openlane
```verilog
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a

echo $::env ([Varible]) // our case = SYNTH_STRATEGY
// change the STRATEGY

set ::env(SYNTH_STRATEGY) "DELAY 0"

run_synthesis
init_floorplan
place_io
global_placement_or
detailed_placement
tap_decap_or
detailed_placement
run_cts
gen_pdn
run_routing
run_magic
```


## References
---
- [Advanced Physical Design](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/?awt_a=5L_6&awt_l=H2Nw0&awt_m=3dG.7I1RDUA8._6)
- [ABC Script](http://people.eecs.berkeley.edu/~alanmi/abc/)
- [OpenLANE Documentation](https://openlane.readthedocs.io/en/latest/)
- [OpenLANE github page by efabless](https://github.com/efabless/openlane).
- [OpenLANE The-OpenROAD-Project](https://github.com/The-OpenROAD-Project/OpenLane)
- [To build and inverter from scratch(workshop reference)](https://github.com/nickson-jose/vsdstdcelldesign)
- [Installation Guide by kunal for tools](https://github.com/kunalg123/vsdflow)
- [Installation Guide Dantu Nandini Devi for tools](https://github.com/DantuNandiniDevi/iiitb_freqdiv#openlane-installation)
- [Magic Documantation](http://opencircuitdesign.com/magic)
- [Skywater Documentation](https://skywater-pdk.readthedocs.io/en/main/)
- [Reference Repo](https://github.com/AngeloJacobo/OpenLANE-Sky130-Physical-Design-Workshop#placement-stage)



## Acknowledgement
---
Finally, I would like to express my sincere gratitude to [Kunal Ghosh](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836/){Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.} and [Nickson Jose, VLSI Engineer](https://www.linkedin.com/in/nickson-jose/) {ASoC Physical Design Engineer} for tremendous assistance in planning and presenting this workshop on Advanced-Physical-Design-using-OpenLane-SKY130. The workshop was excellent and well designed, this workshop taught me a lot of new things about the design of GDSII file from RTL using OpenLANE, open source tool.   

## Inquiries
- Connect with me at [LinkedIn](https://www.linkedin.com/public-profile/settings?trk=d_flagship3_profile_self_view_public_profile)

