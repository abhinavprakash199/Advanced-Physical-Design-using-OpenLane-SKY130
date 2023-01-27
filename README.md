# Advanced-Physical-Design-using-OpenLane-SKY130
This repository contains the whole summary of hands on done by Abhinav Prakash (IS22MTECH14002) during the workshop [Advanced-Physical-Design-using-OpenLane-SKY130](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) organised by VSD. The full RTL to GDSII flow was implemented for PICORV32A- a RISC-V based CPU core using [OpenLANE](https://github.com/The-OpenROAD-Project/OpenLane) with [SKY130nm PDK](https://skywater-pdk.readthedocs.io/en/main/rules/assumptions.html).



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
        - [Timing Characterization](#Timing-Characterization)
* [Day 3 - Design library cell using Magic Layout and ngspice characterization](#day-3)
    + [Labs for CMOS inverter ngspice simulations](#Labs-for-CMOS-inverter-ngspice-simulations)
    
* [References](#references)
* [Acknowledgement](#acknowledgement)
* [Inquiries](#inquiries)


## Day 1:
## Inception of open-source EDA, OpenLANE and Sky130 PDK

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

![Screenshot (2216)](https://user-images.githubusercontent.com/120498080/214480079-1bb1d6ad-8820-4acb-a7ef-b176e3c13a42.png)

- So the main goal of OpenLANE is to produce clean GDSII(clean means no DRC, LVS and Timing voilations) with no human intervention.
- OpenLINK is tuned for SkyWater130nm Open PDK, it also supports XFAB180 and Global Foundry 130G 
- OpenLINK can be used to harden Macros and Chips(harden means creating GDSII or the final layout)
- OpenLINK has two modes of operation
    +  **Autonomous** (it is the push buttton flow where we figure the flow and we directly get the final GDSII) 
    +  **Interactive** (here we run comands and steps one be one so that we can do experimentation and look at the results of differnet flow steps)
 - OpenLINK has very nice feature called as **Design Space Exploration** which can be used to find the best set of flow configurations.
 - OpenLINK has large number of design examples(43 different design with their best configuration)
#### Steps to install openlane in our machine 

- [Guide to install tools](https://github.com/kunalg123/vsdflow)
Steps to install and run on UBUNTU:
```
1) sudo apt-get install git
2) git clone https://github.com/kunalg123/vsdflow.git
3) cd vsdflow
4) chmod 777 opensource_eda_tool_install.sh
5) ./opensource_eda_tool_install.sh 
**NOTE for freshers : This has been tested on a fresh UBUNTU installtion
**NOTE for experienced UNIX users : It has lot of sudo apt-get and sudo remove commands, so you might want to review before running
6) ./vsdflow spi_slave_design_details.csv
7) ./vsdflow picorv32_design_details.csv
```

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

#### Design Preparation Steps

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
% package require openlane 0.9
```
- We use `docker` command to open the open lane in working directory.
- Then use `./flow.tcl -interactive` which identifies using the script the flow has to move and intractive means we do a step by step process.
- Then we need to import all the package that are required to run this program by `package require openlane 0.9`

- **NOTE** Command to run fully automated run `./flow.tcl -design picorv32a`

![image](https://user-images.githubusercontent.com/120498080/214596626-8ca8164d-041a-47a6-934b-09403c9222cf.png)

- [Github Repository for openlane by efabless](https://github.com/efabless/openlane)


**NOTE** *Commands to save the github directory in local machine* - 
> git clone https://github.com/The-OpenROAD-Project/OpenLane.git`

- To learn more how openlink works and how its is designed [prefer this videos](https://www.youtube.com/playlist?list=PLUg3wIOWD8yoZCg9XpFSgEgljx6MSdm9L).

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
![Screenshot (2235)](https://user-images.githubusercontent.com/120498080/214825614-4e3a2d5b-1892-4d50-bf66-6da743c349aa.png)


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

![image](https://user-images.githubusercontent.com/120498080/214837492-61506cf5-9b3e-4350-ad28-6c9e3f459bf8.png)
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
![image](https://user-images.githubusercontent.com/120498080/214942710-949bec91-df15-40a9-ad87-02f92be1cea9.png)

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


























## References
---
- [Advanced Physical Design](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/?awt_a=5L_6&awt_l=H2Nw0&awt_m=3dG.7I1RDUA8._6)
- [OpenLANE Documentation](https://openlane.readthedocs.io/en/latest/)
- [OpenLANE github page](https://github.com/efabless/openlane).
- [ABC Script](http://people.eecs.berkeley.edu/~alanmi/abc/)
- [Installation Guide](https://github.com/DantuNandiniDevi/iiitb_freqdiv#openlane-installation)



## Acknowledgement
---
Finally, I would like to express my sincere gratitude to [Kunal Ghosh](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836/){Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.} and [Nickson Jose, VLSI Engineer](https://www.linkedin.com/in/nickson-jose/) {ASoC Physical Design Engineer} for tremendous assistance in planning and presenting this workshop on Advanced-Physical-Design-using-OpenLane-SKY130. The workshop was excellent and well designed, this workshop taught me a lot of new things about the design of RISC-V processor in OpenLANE.   

## Inquiries
- Connect with me at [LinkedIn](https://www.linkedin.com/public-profile/settings?trk=d_flagship3_profile_self_view_public_profile)

