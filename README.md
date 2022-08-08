# Advance-physical-design-using-openlane-skywater130


## Table of Contents

## About
Openlane: An opensource tool or flow used for tapeouts. The OpenLANE flow comprises a variety of tools such as Yosys, ABC, OpenSTA, Fault, OpenROAD app, Netgen and Magic which are used to harden chips and macros, i.e. generate final GDSII from the design RTL. The primary goal of OpenLANE is to produce clean GDSII with no human intervention. OpenLANE has been tuned to function for the Google-Skywater130 Open Process Design Kit.


## Day 1 - Inception of opensource EDA, OpenLANE and skywater130 PDKs



#### Introduction to Package, chip, pads, core, die and IPs

- Package : It is the black square/rectangle we see on a PCB(for eg. a microprocessor). It usually comes in different packages like DIP, QFN etc. They are usually described as QFN-48 which here it means Quad Flat No-Leads with 48 pins.
- Pads : Are used to send signal in and out of the chip.
- Core : Where all the digital logic sits (eg: AND, OR, NOR gates...)
- Die : Its the size of the entire chip.
- Foundry : It is the place where the chips are actually manufactured.

#### How to talk to computers

The RISC-V Instruction Set Architecture (ISA) is a language used to talk to computers whose hardware is based on RISC-V core. If a user wishes to run a certain application software on a computer, its corresponding C/C++/Java program must be converted into instructions by the compliler. The ouput of the compiler is hardware dependent. These instructions go as inputs to the assembler which outputs binary language that the hardware logic in the chip layout can make sense of. According the the bits received, the digital logic consisting of gates performs the function required by the user of the application software.

### SoC Design and OpenLane

#### Digital ASIC Design required components

The design of digital Application Specific Integrated Circuit (ASIC) requires three enablers or elements - Resistor Transistor Logic Intellectual Property (RTL IPs), Electronic Design Automation (EDA) Tools and Process Design Kit (PDK) data.

- Opensource RTL Designs: github, librecores, opencores
- Opensource EDA tools: QFlow, OpenROAD, OpenLANE
- Opensource PDK data: Google Skywater130 PDK

The ASIC flow objective is to convert RTL design to GDSII format used for final layout. The flow is essentially a software also known as automated PnR (Place & route).

What is PDK?
PDK is the interface between the FAB and the designers. It consist of -
- Process design rules : DRC,LVS...
- Device Models
- Digital standard cell Libraries
- I/O Libraries

#### RTL2GDSII flow


- Synthesis: RTL Converted to gate level netlist using standard cell libraries (SCL)
- Floor & Power Planning: Planning of silicon area to ensure robust power distribution
- Placement: Placing cells on floorplan rows aligned with sites
  - Global Placement: for optimal position of cells
  - Detailed Placement: for legal positions
- Routing: Valid patterns for wires
- Signoff: Physical (DRC, LVS) and Timing verifications (STA)

#### OpenLANE ASIC flow

Image

### Opensource EDA tools
OpenLANE utilises a variety of opensource tools in the execution of the ASIC flow:
Task | Tool/s
------------ | -------------
RTL Synthesis & Technology Mapping | [yosys](https://github.com/YosysHQ/yosys), abc
Floorplan & PDN | init_fp, ioPlacer, pdn and tapcell
Placement | RePLace, Resizer, OpenPhySyn & OpenDP
Static Timing Analysis | [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA)
Clock Tree Synthesis | [TritonCTS](https://github.com/The-OpenROAD-Project/OpenLane)
Routing | FastRoute and [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) 
SPEF Extraction | [SPEF-Extractor](https://github.com/HanyMoussa/SPEF_EXTRACTOR)
DRC Checks, GDSII Streaming out | [Magic](https://github.com/RTimothyEdwards/magic), [Klayout](https://github.com/KLayout/klayout)
LVS check | [Netgen](https://github.com/RTimothyEdwards/netgen)
Circuit validity checker | [CVC](https://github.com/d-m-bailey/cvc)








#### OpenLANE files

- skywater-pdk: contains PDK files provided by foundry
- open_pdks: contains scripts to setup pdks for opensource tools 
- sky130A: contains sky130 pdk files

#### Invoking Docker

To invoke Openlane you should be in ` /openlane_working_dir/openlane ` and use `docker` command. After docker has been invoked we have to start the interactive session. `flow.tcl` is a script that specifies details for openLANE flow.

```
docker
./flow.tcl -interactive
package require openlane 0.9
```
![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/Openlane_package.JPG)

#### Design preparation step

The project is based on reference SoC Picorv32a which utilizes RISCV-ISA. there are various packages which needs to initialize and prep for the design and is done with the following command.
 ```
 prep -design picorv32a
 ```
 ![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/Design_prep.JPG)
 
#### Review of design files

A "runs" folder is generated within the picorv32a folder.

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/runs_folder.JPG)

Inside this `runs` folder we see different files for each new run of openlane a different folder is created it in cludes the date on which openlane was run.

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/inside_runs_folder.JPG)

A merged file is created during the merging operation in the pircorv32a design preparation (it merges lef and techlef files). this file is found in `tmp` folder.

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/merged_lef.JPG)

Next, we run the synthesis of picorv32a design in the openlane interactive terminal:
`run_synthesis`

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/Run_synthesis.JPG)

The yosys and ABC tools are utilised to convert RTL to gate level netlist

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/picorv32a_statistics.JPG)

Calcuation of Flop Ratio:
```
Flop ratio = Number of D Flip flops 
             ______________________
             Total Number of cells
```

#### Characterize synthesis results

After the `run_synthesis` has run successfully we will see the synthesized file in the synthesis folder with `design_name.synthesis.v`

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/dAY1/synthesis_file.JPG)

# Day 2 : Chip Floorplanning and introduction to library cells

- Chip floor planning : Partition of chip die between different building blocks & place the I/O pads
- Macro floor planning : It consist of dimensions, pin locations, rows definition...



## Chip floor planning considerations

### Utilization factor and aspect ratio

Utilization factor and aspect ratio are two important factors which are defined as 

```
Utilisation Factor =  Area occupied by netlist
                     __________________________
                        Total area of core
```

```
Aspect Ratio =  Height
               ________
                Width
```

A Utilisation Factor of 1 signifies 100% utilisation leaving no space for extra cells such as buffer. However, practically, the Utilisation Factor is 0.5-0.6. Likewise, an Aspect ratio of 1 implies that the chip is square shaped. Any value other than 1 implies rectanglular chip.

#### Pre-placed cells

- The arrangement of different IP's in a chip is referred as floor planning.
- These Ip's/blocks have user defined locations and hence are placed in chip before automated placement and routing and are called _pre-placed cells_.
- Automated PnR tools places the remaining logical cells in the design onto a chip.

#### Decoupling capacitors

Pre-placed cells must then be surrounded with decoupling capacitors (decaps). The resistances and capacitances associated with long wire lengths can cause the power supply voltage to drop significantly before reaching the logic circuits. This can lead to the signal value entering into the undefined region, outside the noise margin range.  Decaps are huge capacitors charged to power supply voltage and placed close the logic circuit. Their role is to decouple the circuit from power supply by supplying the necessary amount of current to the circuit. They pervent crosstalk and enable local communication.


#### Power Planning

Each block on the chip, however, cannot have its own decap unlike the pre-placed macros. Therefore a good power planning ensures that each block has its own VDD and VSS pads connected to the horizontal and vertical power and GND lines which form a power mesh.

#### Pin Placement

The netlist defines connectivity between logic gates. The place between the core and die is utilised for placing pins. The connectivity information coded in either VHDL or Verilog is used to determine the position of I/O pads of various pins. Then, logical placement blocking of pre-placed macros is performed so as to differentiate that area from that of the pin area.

#### Floorplan run on OpenLANE & view in Magic

Files of importance in increasing priority order:
1. ```floorplan.tcl``` - System default envrionment variables
2. ```conifg.tcl```
3. ```sky130A_sky130_fd_sc_hd_config.tcl```

Floorplan envrionment variables or switches:
1. ```FP_CORE_UTIL``` - floorplan core utilisation
2. ```FP_ASPECT_RATIO``` - floorplan aspect ratio
3. ```FP_CORE_MARGIN``` - Core to die margin area
4. ```FP_IO_MODE``` - defines pin configurations (1 = equidistant/0 = not equidistant)
5. ```FP_CORE_VMETAL``` - vertical metal layer
6. ```FP_CORE_HMETAL``` - horizontal metal layer

***Note: Usually, vertical metal layer and horizontal metal layer values will be 1 more than that specified in the files***

To run the picorv32a floorplan in openLANE:
 ```
 run_floorplan
 
 ```
 
 ![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/run_floorplan.JPG)]
 
 Post the floorplan run, a .def file will have been created within the `results/floorplan` directory. We may review floorplan files by checking the `floorplan.tcl`. The system defaults will have been overriden by switches set in `conifg.tcl` and further overriden by switches set in `sky130A_sky130_fd_sc_hd_config.tcl` .
 
To view the floorplan, Magic is invoked after moving to the `results/floorplan` directory: 
```
magic -T /home/niravmange/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &

```
![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/magic_layout.JPG)]

One can zoom into Magic layout by selecting an area with left and right mouse clcik followed by pressing "z" key. Here, equidistant input pins (FP_IO_MODE = 1) can be viewed:

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/magic_layout_after_command.JPG)]

when (FP_IO_MODE = 0)
![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/pin&decoupling&20capacitor.JPG)]

### Placement

#### Placement Optimization


The next step in the OpenLANE ASIC flow is placement. The synthesized netlist is the be placed on the floorplan. Placement is perfomed in 2 stages:

1. Global Placement: It finds optimal position for all cells which may not be legal and cells may overlap. Optimization is done through reduction of half parameter wire length
2. Detailed Placement: It alters the position of cells post global placement so as to legalise them

Legalisation of cells is important from timing point of view. 

#### Placement run on OpenLANE & view in Magic

Congestion aware placement using RePIAce:

```
run_placement

```
The objective of placement is the convergence of overflow value. If overflow value progressively reduces during the placement run it implies that the design will converge and placement will be successful. Post placement, the design can be viewed on magic within ```results/placement``` directory:

```
magic -T /home/niravmange/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

```
![Layout_after_placcement](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/after_placement.JPG)]

***Note: Power distribution network generation is usually a part of the floorplan step. However, in the openLANE flow, floorplan does not generate PDN. The steps are - floorplan, placement CTS and then PDN***

### Standard Cell Design Flow

Standard cell design flow involves the following:
1. Inputs: PDKs, DRC & LVS rules, SPICE models, libraries, user-defined specifications 
2. Design steps: Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power)
3. Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files

### Timing Parameter Definitions

Timing defintion | Value
------------ | -------------
slew_low_rise_thr  | 20% value
slew_high_rise_thr |  80% value
slew_low_fall_thr | 20% value
slew_high_fall_thr | 80% value
in_rise_thr | 50% value
in_fall_thr | 50% value
out_rise_thr | 50% value
out_fall_thr | 50% value


```
rise delay =  time(out_fall_thr) - time(in_rise_thr)

Fall transition time: time(slew_high_fall_thr) - time(slew_low_fall_thr)

Rise transition time: time(slew_high_rise_thr) - time(slew_low_rise_thr)
```

A poor choice of threshold points leads to negative delay value. Therefore a correct choice of thresholds is very important.

## Day 3: Design library cell using Magic Layout and ngspice charaterization.

- You can set the parameter on the flow by just copy & pasting the command from `floorplan.tcl` file.

### SPICE Deck creation & Simulation

A SPICE deck includes information about the following:
1. Model description
2. Netlist description
3. Component connectivity
4. Component values
5. Capacitance load
6. Nodes
7. Simulation type and parameters
8. Libraries included

### CMOS inverter Switching Threshold Vm

Thw switching threshold of a CMOS inverter is the point on the transfer characteristic where Vin equals Vout (=Vm). At this point both PMOS and NOMOS are in ON state which gives rise to a leakage current

### 16 Mask CMOS Fabrication
The 16-mask CMOS process consists of the following steps:
1. Selection of subtrate: Secting the body/substrate material
2. Creating active region for transistors: Isolation between active region pockets by SiO2 and Si3N4 deposition followed by photolithography and etching
3. N-well and P-well formation: Ion implanation by Boron for P-well and by Phosphorous for N-well formation
4. Formation of gate terminal: NMOS and PMOS gates formed by photolithography techniques
5. LDD (lightly doped drain) formation: LDD formed to prevent hot electron effect
6. Source & drain formation: Screen oxide added to avoid channelling during implants followed by Aresenic implantation and annealing
7. Local interconnect formation: Removal of screen oxide by HF etching. Deposition of Ti for low resistant contacts
8. Higher level metal formation: CMP for planarization followed by TiN and Tungsten deposition. Top SiN layer for chip protection

### Inverter Standard cell Layout & SPICE extraction

The Magic layout of a CMOS inverter will be used so as to intergate the inverter with the picorv32a design. To do this, inverter magic file is sourced from [vsdstdcelldesign](https://github.com/nickson-jose/vsdstdcelldesign) by cloning it within the ```openlane_working_dir/openlane``` directory as follows:

```
git clone https://github.com/nickson-jose/vsdstdcelldesign
```
![git_clone](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/Git_clone_stdcell.JPG)]

This creates a vsdstdcelldesign named folder in the openlane directory. The repo's contents after cloning appear as follows:

![vsdstdcell_dir](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/vsdstdcell.JPG)]

To invoke magic to view the ```sky130_inv.mag``` file, the sky130A.tech file must be included in the command along with its path. To ease up the complexity of this command, the tech file can be copied from the magic folder to the vsdstdcelldesign folder.

![Loading_inv_cell](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/Loading_inv_design_using_magic.JPG)]



















