# Advanced Physical Design using Skywater130nm node 

  The idea of an OpenSource flow that includes tools for RTL to GDSII flow has always been a far fetched dream for FOSS enthusiast.  Physical Design using SKY130nm technology node using FOSS (Openlane Flow), this includes multiple flow tools that are all OpenSource. The tools can be listed as ```yosys``` and ```abc```, where abc is basically for sequential verification and synthesis. ```OpenSTA``` for static timing analysis, Fault that assists in inserting Design for Test implementation, ```OpenROAD App``` carries out mutiple steps such as floorplanning, placement, CTS-clock tree synthesis, Optimization in terms of better design placements. Other tools that are included in the specific flow are Antenna Diode insertion, Rotuing tool such as ```TritonRoute``` as few of the tools.


# Content
  - [Day 1](#day-1)
    - [OpenROAD flow](#openroad-flow)
    - [OpenSource Tools for RTL to GDSII Flow](#opensource-tools-for-rtl-to-gdsii-flow)
    - [Tools Usage](#tool-usage) 
  - [Day 2](#day-2)
    - [Floorplanning, power planning and pin placement](#floorplanning-power-planning-and-pin-placement)
    - [Synthesis and Floorplanning runs and new version of OpenLANE](#synthesis-and-floorplanning-runs-and-new-version-of-openlane)
    - [Lesson 3](#lesson-3)
  - [Day 3](#day-3)
    - [Custom layout design and simulation of a inverter insertion using tools](#custom-layout-design-and-simulation-of-a-inverter-insertion-using-tools)
    
# Day 1 
# OpenROAD flow

  Automating the process of RTL to GDSII is a the complete design flow from the designing of RTL using HDL languages such as ```verilog``` and ```VHDL``` or higher level abstration languages such as ```SystemC```, ```High-Level-Sysnthesis``` (HLS). There are newer abstraction for designing RTL such as ```BlueSpec Verilog```  and ```BlueSpec Haskell```.  The complete steps for in sequence can be listed using the openlane flow diagram as show below.
  
  ![alt text](https://github.com/wilfredkisku/advanced_pd_using_sky130_pdk/blob/main/images/Day1/04_flow.png)

Listed in details are the tools that are included in the OpenLane flow that included the complete RTL to GDSII flow, the modules includes individual applications that carry out specific tasks on their own. All of which are OpenSource tools and are developed to work with the Skywater PDK and even to an extent with commercially available foundry PDKs:

  1. RTL Synthesis
      1. `yosys` - Synthesis task is to convert the language elements to a netlist RTL
      2. `abc` - abc can be seen as a Synthesis and verification tool of binary sequential logic circuits that mainly appear in synchronous hardware designs IPs. It combines the use of directed acyclic graph (DAG) based technology mapping for standard cells and also optimization based of And-Inverter graphs (AIGs).
  2. Timing analysis 
      1. `OpenSTA` - It is a tool to perform static timing analysis (STA) through which the timing reports can be generated, and this can highlight the timing violations (hold and setup violations).
  3. Floorplan and PDN (tools and )
      1. `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
      2. `ioplacer` - Places the macro input and output ports
      3. `pdn` - Generates the power distribution network
      4. `tapcell` - Inserts welltap and decap cells in the floorplan
  4. Placement
      1. `RePLace` - Performs global placement
      2. `Resizer` - Performs optional optimizations on the design
      3. `OpenPhySyn` - Performs timing optimizations on the design
      4. `OpenDP` - Perfroms detailed placement to legalize the globally placed components
  5. CTS
      1. `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
  6. Routing *
      1. `FastRoute` - Performs global routing to generate a guide file for the detailed router
      2. `TritonRoute` - Performs detailed routing
      3. `SPEF-Extractor` - Performs SPEF extraction
  7. GDSII Generation
      1. `Magic` - Streams out the final GDSII layout file from the routed def
  8. Checks
      1. `Magic` - Performs design rule check (DRC) checks & Antenna Checks (antenna are due to 
      2. `Netgen` - Performs LVS Checks

# OpenSource Tools for RTL to GDSII Flow

OpenLANE integrated several key open source tools over the execution stages:
  - RTL Synthesis, Technology Mapping, and Formal Verification : [yosys + abc](https://github.com/YosysHQ/yosys)
  - Static Timing Analysis: [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA)
  - Floor Planning: [init_fp](https://github.com/The-OpenROAD-Project/OpenROAD/tree/master/src/init_fp), [ioPlacer](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/ioPlacer), [pdn](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/pdngen) and [tapcell](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/tapcell)
  - Placement: [RePLace](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/replace) (Global), [Resizer](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/resizer) and [OpenPhySyn](https://github.com/scale-lab/OpenPhySyn) (Optimizations), and [OpenDP](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/opendp) (Detailed)
  - Clock Tree Synthesis: [TritonCTS](https://github.com/The-OpenROAD-Project/OpenROAD/tree/master/src/TritonCTS)
  - Fill Insertion: [OpenDP/filler_placement](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/opendp)
  - Routing: [FastRoute](https://github.com/The-OpenROAD-Project/OpenROAD/tree/openroad/src/FastRoute) (Global) and [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) (Detailed)
  - SPEF Extraction: [SPEF-Extractor](https://github.com/HanyMoussa/SPEF_EXTRACTOR)
  - GDSII Streaming out: [Magic](https://github.com/RTimothyEdwards/magic)
  - DRC Checks: [Magic](https://github.com/RTimothyEdwards/magic)
  - LVS check: [Netgen](https://github.com/RTimothyEdwards/netgen)
  - Antenna Checks: [Magic](https://github.com/RTimothyEdwards/magic)
  - Circuit Validity Checker: [CVC](https://github.com/d-m-bailey/cvc)

# Tool Usage

PDKs as stressed earlier are essential as they contain technology ```lef``` files that are used to harden macros for obtaining the GDSII file which is a binary file format representing planar geometric shapes, text labels, and other information about the layout in hierarchical form. The Skywawter 130 nm pdks contains there strutured files in three folders mainly ```Sky130A```, ```open_pdks``` and ```skywater pdks```.

   <img src="images/Day1/day01_01_pdks.png">

Apart from the pdks the other essential folder that includes all of the tools that have been installed into a docker environment have been include in the openlane folder that contains the scripts to intialize the openlane flow. The ```flow.tcl``` contains scripts to intialize the tools, ```designs``` folder that contains the verilog IPs and configuration files for each of the project. The runs related information and reports with logs are contained in all of these project folders. At each of the runs the date is attributed as the run folder with the report all being automated with the help of the commands being automamted through openlane.

   <img src="images/Day1/day01_02_tools.png">
   <img src="images/Day1/day01_03_libfiles.png">
   
For invoking OpenLANE in Linux Ubuntu, we should first run the docker everytime we use OpenLANE. This is done by using the following script:
    
    $ docker (or)
    $ docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) openlane:rc6
   
A custom shell script or commands can be generated to make the task simpler.
   
   - To invoke OpenLANE run the `./flow.tcl` script.
   - OpenLANE supports two modes of operation: interactive and autonomous.
   - To use interactive mode use `-interactive` flag with `./flow.tcl`

   
   <img src="images/Day1/day01_04_openlanedocker.png">
   <img src="images/Day1/day01_05_configfile.png">
   
The figure ablve shows the content of the ```config.tcl``` file. It sets the path of the design files, timing files (SDC) files and the design name. Also the clock speed is assigned, with the ```clk``` port name.

   <img src="images/Day1/day01_06_fileprep.png">
   <img src="images/Day1/day01_07_flopratio.png">

One of the important evalations that gives a picture to the design that has been synthesized is the Flops to cell ratio between the number of flip flops generated and the total number of cells in the design. The design under consideration is the ```Picorv32a```, which is a RISC V archicture design.

$\text{Flops to Cell ratio} = \frac{\text{Number of FFs}}{\text{Number of Cells}}$
$\text{Flops to Cell ratio} = \frac{\text{1613}}{\text{14876}} = 0.1084 \text{(or)} 10.84\text{\%}$

# Day 2
# Floorplanning, power planning and pin placement

The layout is the necessary step to get a hardened macros and the GDSII file that needs to be sent to the foundry. This requires steps that are to be run is a particular order for getting the final file of masks that are stacked in a certain order with layers of metals, defining the area the devices that take place on the slicon wafer.

The tools are all inbuilt in the openlane flow and are to be run after synthesis ```run_synthesis```, the step that carries out the floorplanning is where the arrangement of logical block, library cells, pins are defined on wafer or chip. This step makes sure that the synthesized modeules has been assigned an appropriate area and aspect ratio. The pins are too assigned a definite interface that the users of the chip would like to interact with it as a complete sytem is created such as shown in the arduino board as an example. 


    run_floorplan

The details of the configuration variable are located in '/home/kisku.1/Desktop/work/tools/openlane_working_dir/openlane/configuration/README.md‌‌', these also has various ```switches``` for completing a user configured layout and floorplanning.  `FP_IO_HMETAL` and  `FP_IO_VMETAL` which defines the different metal layers assigned which are shown in the final runs as ```+1``` after the successful run. The complete floorplanning section is shown in the below.

    ### Floorplanning

    | Variable      | Description                                                   |
    |---------------|---------------------------------------------------------------|
    | `FP_CORE_UTIL`  | The core utilization percentage. <br> (Default: `50` percent)|
    | `FP_ASPECT_RATIO`  | The core's aspect ratio (height / width). <br> (Default: `1`)|
    | `FP_SIZING`  | Whether to use relative sizing by making use of `FP_CORE_UTIL` or absolute one using `DIE_AREA`. <br> (Default: `"relative"` - accepts "absolute" as well)|
    | `DIE_AREA`  | Specific die area to be used in floorplanning. Specified as a 4-corner rectangle. Units in mm <br> (Default: unset)|
    | `FP_IO_HMETAL`  | The metal layer on which to place the io pins horizontally (top and bottom of the die). <br>(Default: `4`)|
    | `FP_IO_VMETAL`  | The metal layer on which to place the io pins vertically (sides of the die) <br> (Default: `3`)|
    | `FP_IO_MODE`  | Decides the mode of the random IO placement option. 0=matching mode, 1=random equidistant mode <br> (Default: `1`)|
    | `FP_WELLTAP_CELL`  | The name of the welltap cell during welltap insertion. |
    | `FP_ENDCAP_CELL`  | The name of the endcap cell during endcap insertion. |
    | `FP_PDN_VOFFSET`  | The offset of the vertical power stripes on the metal layer 4 in the power distribution network <br> (Default: `16.32`) |
    | `FP_PDN_VPITCH`  | The pitch of the vertical power stripes on the metal layer 4 in the power distribution network <br> (Default: `153.6`) |
    | `FP_PDN_HOFFSET`  | The offset of the horizontal power stripes on the metal layer 5 in the power distribution network <br> (Default: `16.65`) |
    | `FP_PDN_HPITCH`  | The pitch of the horizontal power stripes on the metal layer 5 in the power distribution network <br> (Default: `153.18`) |
    | `FP_PDN_AUTO_ADJUST` | Decides whether or not the flow should attempt to re-adjust the power grid, in order for it to fit inside the core area of the design, if needed. <br> 1=enabled, 0 =disabled (Def
    ault: `1`) |
    | `FP_TAPCELL_DIST`  | The horizontal distance between two tapcell columns <br> (Default: `14`) |
    | `FP_IO_VEXTEND`  |  Extends the vertical io pins outside of the die by the specified units<br> (Default: `-1` Disabled) |
    | `FP_IO_HEXTEND`  |  Extends the horizontal io pins outside of the die by the specified units<br> (Default: `-1` Disabled) |
    | `FP_IO_VLENGTH`  | The length of the vertical IOs in microns. <br> (Default: `4`) |
    | `FP_IO_HLENGTH`  | The length of the horizontal IOs in microns. <br> (Default: `4`) |
    | `FP_IO_VTHICKNESS_MULT`  | A multiplier for vertical pin thickness. Base thickness is the pins layer minwidth <br> (Default: `2`) |
    | `FP_IO_HTHICKNESS_MULT`  | A multiplier for horizontal pin thickness. Base thickness is the pins layer minwidth <br> (Default: `2`) |
    | `BOTTOM_MARGIN_MULT`     | The core margin, in multiples of site heights, from the bottom boundary. <br> (Default: `4`) |
    | `TOP_MARGIN_MULT`        | The core margin, in multiples of site heights, from the top boundary. <br> (Default: `4`) |
    | `LEFT_MARGIN_MULT`       | The core margin, in multiples of site widths, from the left boundary.  <br> (Default: `12`) |
    | `RIGHT_MARGIN_MULT`      | The core margin, in multiples of site widths, from the right boundary.   <br> (Default: `12`) |
    | `FP_PDN_CORE_RING` | Enables adding a core ring around the design. More details on the control variables in the pdk configurations documentation. 0=Disable 1=Enable. <br> (Default: `0`) |
    | `FP_PDN_ENABLE_RAILS` | Enables the creation of rails in the power grid. 0=Disable 1=Enable. <br> (Default: `1`) |
    | `FP_PDN_CHECK_NODES` | Enables checking for unconnected nodes in the power grid. 0=Disable 1=Enable. <br> (Default: `1`) |
    | `FP_HORIZONTAL_HALO` | Sets the horizontal halo around the tap and decap cells. The value provided is in microns. <br> Default: `10` |
    | `FP_VERTICAL_HALO` | Sets the vertical halo around the tap and decap cells. The value provided is in microns. <br> Default: set to the value of `FP_HORIZONTAL_HALO` |
    | `DESIGN_IS_CORE` | Controls the layers used in the power grid. Depending on whether the design is the core of the chip or a macro inside the core. 1=Is a Core, 0=Is a Macro <br> (Default: `1`)|
    | `FP_PIN_ORDER_CFG` | Points to the pin order configuration file to set the pins in specific directions (S, W, E, N). Check this [file][0] as an example. If not set, then the IO pins will be placed based on one of the other methods depending on the rest of the configurations. <br> (Default: NONE)|
    | `FP_CONTEXT_DEF` | Points to the parent DEF file that includes this macro/design and uses this DEF file to determine the best locations for the pins. It must be used with `FP_CONTEXT_LEF`, otherwise it's considered non-existing. If not set, then the IO pins will be placed based on one of the other methods depending on the rest of the configurations. <br> (Default: NONE)|
    | `FP_CONTEXT_LEF` | Points to the parent LEF file that includes this macro/design and uses this LEF file to determine the best locations for the pins. It must be used with `FP_CONTEXT_DEF`, otherwise it's considered non-existing. If not set, then the IO pins will be placed based on one of the other methods depending on the rest of the configurations. <br> (Default: NONE)|
    | `FP_DEF_TEMPLATE` | Points to the DEF file to be used as a template when running `apply_def_template`. This will be used to exctract pin names, locations, shapes -excluding power and ground pins- as well as the die area and replicate all this information in the `CURRENT_DEF`. |
    | `VDD_NETS` | Specifies the power nets/pins to be used when creating the power grid for the design. |
    | `GND_NETS` | Specifies the ground nets/pins to be used when creating the power grid for the design. |
    | `SYNTH_USE_PG_PINS_DEFINES` | Specifies the power guard used in the verilog source code to specify the power and ground pins. This is used to automatically extract `VDD_NETS` and `GND_NET` variables from the verilog, with the assumption that they will be order `inout vdd1, inout gnd1, inout vdd2, inout gnd2, ...`. |
# Synthesis and Floorplanning runs and new version of OpenLANE 

- In the newer version fo the openlane FP_CORE_VMETAL and FP_CORE_HMETAL switches are missing in ```ioPlacer.log``` and ```config.tcl```. They need to be included in the ```config.tcl``` file before running the floorplanning as it defines the complete layer number definitions.
- ```run_floorplan``` fails after the STA analysis in the new version. An alternate command can be used: ```init_floorplan```
- SPEF extraction need not be externally performed in the new version. It has been integrated into the OpenLane flow.

Note in the new version following commans may be used for an error free flow:

    % init_floorplan
    % place_io
    % global_placement_or
    % detailed_placement
    % tap_decap_or
    % detailed_placement
    % gen_pdn
    % run_routing

   <img src="images/Day2/day02_1.png">
   <img src="images/Day2/day02_2.png">
   <img src="images/Day2/day02_3.png">
   <img src="images/Day2/day02_4.png">
   <img src="images/Day2/day02_5.png">
   <img src="images/Day2/day02_6.png">
   
DEF file is used to represent the Physical layout of an Integrated Circuit (IC) in ASCII format. A DEF file is strongly connected with the Library Exchange Format (LEF) file. So both files are needed for a correct display of physical design.

Library Exchange Format is a specification for representing the physical layout of an integrated circuit in an ASCII format. It includes design rules and abstract information about the cells. LEF only has the basic information required at that level to serve the purpose of the concerned CAD tool, in this case the magic layout and openlane tools.

   magic -T <path-to-tech-file-skywater-130A> def read <path-to-the-def-file> lef read <path-to-the-generated-lef-file-after-floorplan>

   <img src="images/Day2/day02_7.png">
   <img src="images/Day2/day02_8.png">
   <img src="images/Day2/day02_9.png">
   <img src="images/Day2/day02_10.png">
   <img src="images/Day2/day02_11.png">
   <img src="images/Day2/day02_12.png">
   <img src="images/Day2/day02_13.png">
   
# Lesson 3


# Day 3
# Custom layout design and simulation of a inverter insertion using tools 

   <img src="images/Day3/day03_1.png">
   <img src="images/Day3/day03_2.png">
   <img src="images/Day3/day03_3.png">
   <img src="images/Day3/day03_4.png">
   <img src="images/Day3/day03_5.png">
   <img src="images/Day3/day03_6.png">
   <img src="images/Day3/day03_7.png">
   <img src="images/Day3/day03_8.png">
   <img src="images/Day3/day03_9.png">
   <img src="images/Day3/day03_10.png">
   <img src="images/Day3/day03_11.png">
   <img src="images/Day3/day03_12.png">
   <img src="images/Day3/day03_13.png">
   <img src="images/Day3/day03_14.png">
   <img src="images/Day3/day03_15.png">
   <img src="images/Day3/day03_16.png">
   <img src="images/Day3/day03_16.png"> 
