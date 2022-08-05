# Advanced Physical Design using Skywater130nm node 

  The idea of an OpenSource flow that includes tools for RTL to GDSII flow has always been a far fetched dream for FOSS enthusiast.  Physical Design using SKY130nm technology node using FOSS (Openlane Flow), this includes multiple flow tools that are all OpenSource. The tools can be listed as ```yosys``` and ```abc```, where abc is basically for sequential verification and synthesis. ```OpenSTA``` for static timing analysis, Fault that assists in inserting Design for Test implementation, ```OpenROAD App``` carries out mutiple steps such as floorplanning, placement, CTS-clock tree synthesis, Optimization in terms of better design placements. Other tools that are included in the specific flow are Antenna Diode insertion, Rotuing tool such as ```TritonRoute``` as few of the tools.


# Content
  - [Day 1](#day-1)
    - [OpenROAD flow](#openroad-flow)
    - [OpenSource Tools for RTL to GDSII Flow](#opensource-tools-for-rtl-to-gdsii-flow)
    - [Tools Usage](#tool-usage) 
  - [Day 2](#day-1)
    - [Lesson 1](#lesson-1)
    - [Lesson 2](#lesson-2)
    - [Lesson 3](#lesson-3)
    
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
   <img src="images/Day1/day01_06_fileprep.png">
   <img src="images/Day1/day01_07_flopratio.png">

# Day 2
# Lesson 1

# Lesson 2

# Lesson 3
