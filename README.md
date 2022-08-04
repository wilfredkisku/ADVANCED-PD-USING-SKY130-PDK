# Advanced Physical Design using Skywater130nm node 

  The idea of an OpenSource flow that includes tools for RTL to GDSII flow has always been a far fetched dream for FOSS enthusiast.  Physical Design using SKY130nm technology node using FOSS (Openlane Flow), this includes multiple flow tools that are all OpenSource. The tools can be listed as ```yosys``` and ```abc```, where abc is basically for sequential verification and synthesis. ```OpenSTA``` for static timing analysis, Fault that assists in inserting Design for Test implementation, ```OpenROAD App``` carries out mutiple steps such as floorplanning, placement, CTS-clock tree synthesis, Optimization in terms of better design placements. Other tools that are included in the specific flow are Antenna Diode insertion, Rotuing tool such as ```TritonRoute``` as few of the tools.


# Content
  - [OpenROAD flow](#openroad-flow)
  - [OpenSource Tools for RTL to GDSII Flow](#opensource-tools-for-rtl-to-gdsii-flow)
  -   


# OpenROAD flow

  Automating the process of RTL to GDSII is a the complete design flow from the designing of RTL using HDL languages such as ```verilog``` and ```VHDL``` or higher level abstration languages such as ```SystemC```, ```High-Level-Sysnthesis``` (HLS). There are newer abstraction for designing RTL such as ```BlueSpec Verilog```  and ```BlueSpec Haskell```.  The complete steps for in sequence can be listed using the openlane flow diagram as show below.
  
  ![alt text](https://github.com/wilfredkisku/advanced_pd_using_sky130_pdk/blob/main/images/Day1/04_flow.png)
