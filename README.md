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
 
 ![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/run_floorplan.JPG)
 
 Post the floorplan run, a .def file will have been created within the `results/floorplan` directory. We may review floorplan files by checking the `floorplan.tcl`. The system defaults will have been overriden by switches set in `conifg.tcl` and further overriden by switches set in `sky130A_sky130_fd_sc_hd_config.tcl` .
 
To view the floorplan, Magic is invoked after moving to the `results/floorplan` directory: 
```
magic -T /home/niravmange/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &

```
![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/magic_layout.JPG)

One can zoom into Magic layout by selecting an area with left and right mouse clcik followed by pressing "z" key. Here, equidistant input pins (FP_IO_MODE = 1) can be viewed:

![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/magic_layout_after_command.JPG)

when (FP_IO_MODE = 0)
![Openlane_interactive_flow](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/pin&decoupling&20capacitor.JPG)

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
![Layout_after_placcement](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%202/after_placement.JPG)

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
![git_clone](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/Git_clone_stdcell.JPG)

This creates a vsdstdcelldesign named folder in the openlane directory. The repo's contents after cloning appear as follows:

![vsdstdcell_dir](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/vsdstdcell.JPG)

To invoke magic to view the ```sky130_inv.mag``` file, the sky130A.tech file must be included in the command along with its path. To ease up the complexity of this command, the tech file can be copied from the magic folder to the vsdstdcelldesign folder.

![Loading_inv_cell](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/Loading_inv_design_using_magic.JPG)

The sky130_inv.mag file can then be invoked in Magic very easily:
```
magic -T sky130A.tech sky130_inv.mag &
```
![inv_layout](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/SKY130_inv.JPG)

In Sky130 the first layer is called the local interconnect layer or Locali
To verify whether the layout is that of CMOS inverter, verification of P-diffusiona nd N-diffusion regions with Polysilicon can be observed:
![inv_layout](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/pmos.JPG)

Other verification steps are to check drain and source connections. The drains of both PMOS and NMOS must be connected to output port  (Y) and the sources of both must be connected to power supply VDD VPWR.

**LEF or library exchange format:**
A format that tells us about cell boundaries, VDD and GND lines. It contains no info about the logic of circuit and is also used to protect the IP.

**SPICE extraction:**
Within the Magic environment, following commands are used in tkcon to achieve .mag to .spice extraction:
```
extract all
ext2spice cthresh 0 rethresh 0
ext2spice
```

This generates the ```sky130_in.spice``` file as shown above.

![extracted_spice](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/Spice_extracted_from_magic.JPG)

For simulation, ngspice is invoked in ther terminal:

```
ngspice sky130_inv.spice
```

The output "y" is to be plotted with "time" and swept over the input "a":

![plot_y_vs_time_a](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/ngspice_run_plot.JPG)

The spikes in the output at switching points is due to low capacitance loads. This can be taken care of by editing the spice deck to increase the load capacitance value.

Four timing parameters are used to characterize the inverter standard cell:
1. Rise transition: Time taken for the output to rise from 20% of max value to 80% of max value
2. Fall transition- Time taken for the output to fall from 80% of max value to 20% of max value
3. Cell rise delay = time(50% output rise) - time(50% input fall)
4. Cell fall delay  = time(50% output fall) - time(50% input rise)


![spice_analysis](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%203/spice_analysis.JPG)

## Day 4: Timing Analysis & CTS

A requirement for ports as specified in ```tracks.info``` is that they should be at intersection of horizontal and vertical tracks. The CMOS Inverter ports A and Y are on li1 layer. It needs to be ensured that they're on the intersection of horizontal and vertical tracks. We access the tracks.info file for the pitch and direction information:

![track_info](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/Tracks_info_file.JPG)

To ensure that ports lie on the intersection point, the grid spacing in Magic (tkcon) must be changed to the li1 X and li1 Y values. Convergence of grid and tracks can be achieved using the following command:
```
grid 0.46um 0.34um 0.23um 0.17um
```
![grid_command](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/After_sending_grid_command.JPG)

### Standard Cell LEF generation
By using below command `LEF` file is extracted.
```
LEF write
```
![LEF_command](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/LEF_command.JPG)
This generates ```sky130_vsdinv.lef``` file.

### Integrating custom cell in OpenLANE

In order to include the new standard cell in the synthesis, copy the sky130_vsdinv.lef file to the ```designs/picorv32a/src``` directory  
Since abc maps the standard cell to a library abc there must be a library that defines the CMOS inverter. The ```sky130_fd_sc_hd_typical.lib``` file from ```vsdstdcelldesign/libs``` directory needs to be copied to the ```designs/picorv32a/src``` directory (Note: the slow and fast library files may also be copied).

Next, ```config.tcl``` must be modified:
```
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

In order to integrate the standard cell in the OpenLANE flow, invoke openLANE as usual and carry out following steps:

```
prep -design picorv32a -tag 06-08_17-28 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```

During the synthesis run, several instances of the sky130_vsdinv cell can be observed:

![LEF_command](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/vsdinv_got_included.JPG)

Next floorplan is run, followed by placement:

```
init_floorplan
place_io
global_placement_or
detailed_placement
tap_decap_or
detailed_placement
```

To check the layout invoke magic from the ```results/placement``` directory:

```
magic -T /home/niravmange/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

```
Since the custom standard cell has been plugged into the openLANE flow, it would be visible in the layout.

### Post-synthesis timing analysis

Timing analysis is carried out outside the openLANE flow using OpenSTA tool. For this, a new file ```pre_sta.conf``` is created(this file template can be found in the ```vsdstdcell/extra``` folder. This file would be reqiured to carry out the STA analysis. Invoke OpenSTA outside the openLANE flow as follows:

```
sta pre_sta.conf
```

![sta_pre_sta_conf](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/pre_sta_conf_2.JPG)

![sta_pre_sta_conf_analysis](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/pre_sta_conf_analysis.JPG)

Since clock tree synthesis has not been performed yet, the analysis is with respect to ideal clocks and only setup time slack is taken into consideration. The slack value is the difference between data required time and data arrival time. The worst slack value must be greater than or equal to zero. If a negative slack is obtained, following steps may be followed:
1. Change synthesis buffering and synthesis sizing values 
2. Review maximum fanout of cells and replace cells with high fanout.

### Clock Tree Synthesis

The purpose of building a clock tree is enable the clock input to reach every element and to ensure a zero clock skew. H-tree is a common methodology followed in CTS.
Before attempting a CTS run in TritonCTS tool, if the slack was attempted to be reduced in previous run, the netlist may have gotten modified by cell replacement techniques. Therefore, the verilog file needs to be modified using the ```write_verilog``` command. Then, the synthesis, floorplan and placement is run again. To run CTS use the below command:

```
run_cts
```

The CTS run adds clock buffers in therefore buffer delays come into picture and our analysis from here on deals with real clocks. Setup and hold time slacks may now be analysed in the post-CTS STA anlysis in OpenROAD within the openLANE flow:
```
openroad

```

![Openroad](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/Open_road_command.JPG)
```
read DEF /openLANE_flow/designs/picorv32a/runs/06-08_17-28/results/cts/picorv32a-cts.def
```
![read_def](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/read_def_command.JPG)

```
write_db pico_cts.db
```
![write_db](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/write_db_command.JPG)

```
read_db pico_cts.db
```
![read db](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/read_db_command.JPG)

```
read_verilog /openLANE_flow/designs/picorv32a/runs/06-08_17-28/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock (all_clocks)
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```
![set_propagated_clocks](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/set_propagated_clocks.JPG)

Slack at the end of STA for typical corner:

Hold Slack

![Hold_slack](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/hold_slack_typical.JPG)

Setup Slack

![set_propagated_clocks](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/setup_slack_typical.JPG)


Setup and Hold Skew
Command for skew
```
report_clock_skew -hold/setup
```


![set_propagated_clocks](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%204/setup&hold_skew.JPG)


## Day 5: Final steps in RTL2GDS

### Power Distribution Network generation

Unlike the general ASIC flow, Power Distribution Network generation is not a part of floorplan run in OpenLANE. PDN must be generated after CTS and post-CTS STA analyses:

```
gen_pdn
```

We can confirm the success of PDN by checking the current def environment variable: ``` echo $::env(CURRENT_DEF) ```

### Routing 
OpenLANE uses the TritonRoute tool for routing. There are 2 stages of routing:
1. Global routing: Routing region is divided into rectangle grids which are represented as course 3D routes (Fastroute tool)
2. Detailed routing: Finer grids and routing guides used to implement physical wiring (TritonRoute tool)

Features of TritonRoute:
1. Honouring pre-processed route guides
2. Assumes that each net satisfies inter guide connectivity
3. Uses MILP based panel routing scheme
4. Intra-layer parallel and inter-layer sequential routing framework

Running routing step in TritonRoute as part of openLANE flow:

```
run_routing
```

Routing typically uses up a lot of memory:
![routing_memory_consumption](https://github.com/Nirav-Mange/advance-physical-design-using-openlane-skywater130/blob/main/PHYSICAL%20DESIGN%20WORKSHOP/Day%205/memory_consumption.JPG)


At the end of routing, one may see a few DRC violations. In such cases, better routing stratgies may be employed, however, this compromises on time and memory. Once routing is completed, parasitic resistances and capacitances associated with routes come into picture. These parasitics can be extracted into a SPEF file. In newer openLANE versions, SPEF extraction is a part of routing run. Following this, post-route STA may be carried out.


## References 
- [The OpenROAD Project](https://github.com/The-OpenROAD-Project/OpenLane)
- [Nickson Jose](https://github.com/nickson-jose/vsdstdcelldesign)
- [Kunal Ghosh](https://github.com/kunalg123)













