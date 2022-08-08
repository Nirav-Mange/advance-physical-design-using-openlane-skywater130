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
 
 





















