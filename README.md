# FPGA-WORKSHOP
This repository contains all the information gathered during a five day workshop on [ FPGA - Fabric, Design and Architecture by VSD ](https://www.vlsisystemdesign.com/fpga/) conducted from 14/12/2022 to 18/12/2022 which covers tools like Xilinx Vivado, Open FPGA, VTR, VPR and SOFA
## TABLE OF CONTENTS
  - [DAY 1 - BASICS OF FPGA and XILINX VIVADO](#day-1---basics-of-fpga-and-xilinx-vivado)
  * [INTRODUCTION TO FPGA](#introduction-to-fpga)
  * [ASIC Vs FPGA](#asic-vs-fpga)
  * [APPLICATIONS](#applications)
  * [ARCHITECTURE](#architecture)
  * [FPGA DESIGN METHODOLOGY](#fpga-design-methodology)
  * [BASYS3 FPGA BOARD](#basys3-fpga-board)
  * [4-BIT COUNTER EXAMPLE IN XILINX VIVADO](#4-bit-counter-example-in-xilinx-vivado)
    + [Verilog code](#verilog-code)
    + [TEST BENCH](#test-bench)
    + [BEHAVIOURAL SIMULATION](#behavioural-simulation)
    + [ELABORATION](#elaboration)
    + [I/O PLANNING](#i-o-planning)
    + [SYNTHESIS](#synthesis)
    + [CONSTRAINTS](#constraints)
    + [IMPLEMENTATION](#implementation)
      - [UTILIZATION REPORT](#utilization-report)
      - [POWER REPORT](#power-report)
  * [VIO -VIRTUAL INPUT OUTPUT](#vio--virtual-input-output)
- [DAY 2 - OPENFPGA , VTR & VPR](#day-2---openfpga---vtr---vpr)
  * [OPENFPGA](#openfpga)
  * [VPR](#vpr)
    + [RUN VPR ON A PRE-SYNTHESIZED CIRCUIT](#run-vpr-on-a-pre-synthesized-circuit)
  * [VTR](#vtr)
    + [RUN VTR IN AUTOMATIC FLOW](#run-vtr-in-automatic-flow)
    + [TIMING](#timing)
    + [POST-SYNTHESIS SIMULATION](#post-synthesis-simulation)
    + [POWER ESTIMATION](#power-estimation)
    + [UTILIZATION](#utilization)
- [DAY 3 - INTRODUCTION TO RISC-V CORE PROGRAMMING IN XILINX VIVADO](#day-3---introduction-to-risc-v-core-programming-in-xilinx-vivado)
  * [BEHAVIOURAL SIMULATION](#behavioural-simulation-1)
  * [ELABORATION](#elaboration-1)
  * [SYNTHESIS](#synthesis-1)
  * [CONSTRAINTS](#constraints-1)
  * [IMPLEMENTATION](#implementation-1)
    + [TIMING REPORT](#timing-report)
    + [POWER REPORT](#power-report-1)
- [DAY 4 - INTRODUCTION TO SOFA FPGA FABRIC IP](#day-4---introduction-to-sofa-fpga-fabric-ip)
  * [SOFA - INTRODUCTION](#sofa---introduction)
  * [Steps to install & work with SOFA](#steps-to-install---work-with-sofa)
  * [COUNTER-AREA](#counter-area)
  * [COUNTER-TIMING](#counter-timing)
  * [COUNTER POST IMPLEMENTATION](#counter-post-implementation)
  * [COUNTER POWER ANALYSIS](#counter-power-analysis)
- [DAY 5 - RISC-V CORE ON CUSTOM SOFA FABRIC](#day-5---risc-v-core-on-custom-sofa-fabric)
- [REFERENCES](#references)
- [ACKNOWLEDGEMENTS](#acknowledgements)
## DAY 1 - BASICS OF FPGA and XILINX VIVADO
### INTRODUCTION TO FPGA
Field Programmable Gate Array (FPGA) is a reprogrammable IC which can be designed and configured by the user. Configuration can be specified using a Hardware Description Language (HDL) like Verilog, VHDL or High-level languages like C,C++,Python
### ASIC Vs FPGA 
|     ASIC         |    FPGA         |
|------------------|-----------------|
|Application Specific Integrated IC|Field Programmable Gate Array|
|Design goes from RTL(Register Transfer Level) to Layout|Design is from RTL to Bitstream|
|Reprogrammable and Reconfigurable|Non-Reprogrammable|
|Final product is obtained by sending the layout to a semiconductor foundary for fabrication|Programmed on an off-shelf FPGA and is used for the purpose of Prototyping & & Validation|
### APPLICATIONS
- Signal / Image Processing
- Device Controllers
- Machine Learning
- Hardware Acceleration
- Embedded Systems
- Aerospace industry
- High performance computing
### ARCHITECTURE
The following picture shows the basic architecture of a FPGA.
![fpga arch](https://user-images.githubusercontent.com/52970851/208061986-016f74aa-3f6d-4e76-b300-1692e674142e.jpg)
The basic components of a FPGA are as follows
1. **Configurable Logic Blocks (CLB)**
   - Consists of a set of Look-up-Tables (LUT), Carry chain, Multiplexers, Flipflops and Latches 
   - Capable of implementing any combinational or sequential logic
2. **Programmable Interconnects**
   - These are wires used to interconnect the CLBs
3. **Programmable input/Output**
   - For External interfaces
4. **Block RAM/Memory**
5. **Clock Tiles** 
<br> Along with these components it has DSP and Multiplier blocks and Configuration Memory for synthesizing a design and generating bitstream. Bitstream consists of information regarding the connectivity between CLBs, I/O cells and also the logic to be implemented. 
### FPGA DESIGN METHODOLOGY
The following snippet explains the flow of FPGA design methodology

![DESIGN METHOD](https://user-images.githubusercontent.com/52970851/208065160-47c3f771-95fb-4d4b-a0a1-e0d39c0f5108.png)
### BASYS3 FPGA BOARD
Basys3 FPGA board with its callouts is shown in the picture below.
![basys3_board](https://user-images.githubusercontent.com/52970851/208233474-e25bf0c9-f168-4e95-af06-f863bb5d9b91.png)
|CALL OUT|COMPONENT DESCRIPTION|CALL OUT|DESCRIPTION|
|-------|-------|-------|-------|
|1|Power good LED|9|Push button|
|2|PMOD ports|10|Programming mode Jumper|
|3|XADC|11|USB host connector|
|4|7-Segment Displays(4)|12|VGA connector|
|5|Slide switches(16)|13|Shared UART/JTAG USB port|
|6|LEDs(16)|14|External power connector|
|7|Pushbuttons(5)|15|Power switch|
|8|Programming LED|16|Power select jumper|
### 4-BIT COUNTER EXAMPLE IN XILINX VIVADO
#### Verilog code
```
`timescale 1ns / 1ps 
////////////////////////////////////////////////////////////////////////////////// 
// 4 BIT COUNTER WITH 100MHz CLOCK 
module counter_clk_div(clk,reset,count); 
input clk,reset; 
output reg [3:0] count = 4'b0000; 
reg [25:0] count_reg;
reg clk_div = 1'b0; 
always @ (posedge clk) 
begin 
if (reset)
    begin 
        clk_div <= 1'b0; 
        count_reg <= 26'd0; 
     end 
else 
    begin
        count_reg <= count_reg + 1; 
        if (count_reg == 26'd12) // for simulation 
        begin
            clk_div <= ~ clk_div; 
            count_reg <= 26'd0;
        end 
    end 
 end 
 always @ (posedge clk_div) 
 begin 
 if (reset)
 begin 
    count <= 4'b0000; 
 end 
 else 
 begin 
    count <= count + 1; 
 end 
 end 
endmodule
```
#### TEST BENCH
```
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
//    TEST BENCH WITH 4-BIT COUNTER
//////////////////////////////////////////////////////////////////////////////////
module test_counter();
reg clk,reset;
wire [3:0] out;
counter_clk_div dut(clk,reset,out);
initial begin
clk=0;
reset=1;
#20;
reset=0;
end
always
#5 clk=~clk;    
endmodule
```
#### BEHAVIOURAL SIMULATION
The following snippet indicates the behavioural simulation of the 4-bit Up counter with 100MHz source clock.
![1_beh_sim](https://user-images.githubusercontent.com/52970851/208242060-9c1c6ce3-3844-4395-93f3-3eac6844ec6d.png)
#### ELABORATION
Next comes the Elaboration step which performs the following: 
- Linting checks in the code
- Deduction of logic from the RTL design
- Building of data structures
- Application of constraints if present \
Once eloboration is done the elaborated design can be viewed as follows:
![2_rtl_schem_counter png](https://user-images.githubusercontent.com/52970851/208243021-2f679470-fa9f-4a77-8eda-34abc01ff4c2.jpg)
#### I/O PLANNING 
In I/O planning the ports of the designed module are assigned with their FPGA pins and the IO standard is set to LVCMOS33 i.e., the supply voltage is set as 3-3.3V. The picture shown below describes the corresponding pins assigned to the module ports of the 4-bit counter. \
![3_IO_PLANNING](https://user-images.githubusercontent.com/52970851/208244468-b74ac7d8-a50d-49c1-b1c2-765f7ff1a6ec.jpg)
&emsp; Next step : Save the pin assignments as .xdc file. The following picture shows the constraints.xdc file for the counter designed.
#### SYNTHESIS
&emsp; Synthesis is the process where the HDL is complied and the RTL-specified design is converted into a gate-level netlist. \
The following snippet shows the synthesized design.
![synthesized design_counter_day1](https://user-images.githubusercontent.com/52970851/208301173-a464b263-4b94-4c76-a012-e18e7bc34f9f.jpg)
&emsp; The timing summary are shown as NA since there are no user specified constraints at present. Timing constraints are now set in the Constraints Wizard in Synthesis Tab. A 100MHz clock is set and the corresponding Tcl command is added in the constraints file.
#### CONSTRAINTS
Constraints files act as a guide for the FPGA Synthesis and Implementation processes. They have information about the ports declaration and timing information like input/output delays and clock constraints. In simple words it gives performance specfications so that the specifications can be met. \
The constraints for this counter example is as follows:
```
set_property IOSTANDARD LVCMOS33 [get_ports {count[3]}]
set_property IOSTANDARD LVCMOS33 [get_ports {count[2]}]
set_property IOSTANDARD LVCMOS33 [get_ports {count[1]}]
set_property IOSTANDARD LVCMOS33 [get_ports {count[0]}]
set_property IOSTANDARD LVCMOS33 [get_ports clk]
set_property IOSTANDARD LVCMOS33 [get_ports reset]
set_property PACKAGE_PIN L1 [get_ports {count[3]}]
set_property PACKAGE_PIN P1 [get_ports {count[2]}]
set_property PACKAGE_PIN L3 [get_ports {count[1]}]
set_property PACKAGE_PIN P3 [get_ports {count[0]}]
set_property PACKAGE_PIN W5 [get_ports clk]
set_property PACKAGE_PIN R2 [get_ports reset]
create_clock -period 10.000 -name clk -waveform {0.000 5.000} [get_ports clk]
```
The following snippet shows the post-synthesis timing summary after the constraints are given. It can be seen that the setup and hold slacks are met.
![day1_counter_synthesis_with_constraints](https://user-images.githubusercontent.com/52970851/208353122-97aee166-0570-4833-82d7-ba312a04e09e.png)
#### IMPLEMENTATION
Next step is the implementation. Implementation is the process of placing and routing the netlist onto the FPGA device resources. \
The following snippet shows the implemented schematic of the counter
![day1_counter_implementation_schematic](https://user-images.githubusercontent.com/52970851/208353864-040384f4-e18a-4ca3-9dd1-bf8c2577f474.png)
There are various reports obtained after implementation which area as follows: \
##### UTILIZATION REPORT
The following picture gives a summary of the device utilization in percentage.
![day1_counter_utilization](https://user-images.githubusercontent.com/52970851/208354472-0c05c507-decb-4fca-9523-8d90f6217021.png)
##### POWER REPORT
The snippet shown below gives the power analysis report of the implemented netlist of the counter
![day1_counter_power](https://user-images.githubusercontent.com/52970851/208354948-3f2616fd-24b0-4d1b-be4a-1f3ba45ad39e.png)
### VIO -VIRTUAL INPUT OUTPUT
&emsp; Virtual Input-Output (VIO) is an IP inbuilt with Xilinx Vivado. It is used for virtually providing inputs and view outputs.The number and width of i/o is customizable. The inputs of the functioning module will be generated by the VIO and the outputs of the functioning module will be probed by the VIO
## DAY 2 - OPENFPGA , VTR & VPR
### OPENFPGA
 - OpenFPGA is a Open-source framework which can be utilized to generate a fabric for a highly-customized FPGA
 - It uses automation techniques and reduces the development cycle of a FPGA from a year to just a few days
 - It also provides facilities for bitstream generation and verification
 - It generates Verilog netlists based on an XML-based description file and Verilog testbenches for validation
 - Open FPGA mainly consists of the following parts:
   - FPGA-Verilog
   - FPGA-SDC
   - FPGA-Bitstream
The OpenFPGA framework is as shown in the picture below:
![DAY2_OPENFPGA_FRAMEWORK](https://user-images.githubusercontent.com/52970851/208594831-55d5c099-32b2-4e01-a3f4-8d315068b17e.png)
### VPR
Versatile Place and Route(VPR) is an open-source CAD tool which is useful for the exploration of higly-customizable FPFA, mainly used for academic purposes.
- Input File : Technology-mapped user design (.blif) and FPGA Architectute File (.xml)
- Output File : FPGA configuration (.place, .route, .net, .log) and Peformance statistics (like area and delay (.rpt))
- Steps performed:
  - Pack
    - Combines primitive netlist blocks (e.g. LUTs, FFs) into groups called Complex Blocks (as specified in the FPGA architecture file)
    - Results are written into a .net file
  - Place
    - Assigns a location to the Complex Blocks with the FPGA grid 
    - Optimizes wirelength and timing
    - Results are written into a .place file
  - Route
    - Determines how to connect the placed Complex Blocks together, according to the netlist connectivity and the routing resources of the FPGA chip
  - Analyse
    - Analyzes the resulting implementation
    - Producing information about:
      - Resource usage (e.g. block types, wiring)
      - Timing (e.g. critical path delays and timing paths)
      - Power (e.g. total power used, power broken down by blocks) \
 The VPR flow is marked in orange in the picture shown below
 ![DAY2_VPR_FLOW](https://user-images.githubusercontent.com/52970851/208599322-a7601f90-3c31-4e7b-bcef-37fa25187ed9.png)
#### RUN VPR ON A PRE-SYNTHESIZED CIRCUIT
Command for invoking VPR \
``` $VTR_ROOT/vpr/vpr  $VTR_ROOT/vtr_flow/arch/timing/EArch.xml  <blif-file path>  --route_chan_width 100 ``` \
Example : ```$VTR_ROOT/vpr/vpr   $VTR_ROOT/vtr_flow/arch/timing/EArch.xml $VTR_ROOT/vtr_flow/benchmarks/blif/tseng.blif   --route_chan_width 100 ``` \
Command for visualizing the circuit (GUI) with analysis \
``` $VTR_ROOT/vpr/vpr  $VTR_ROOT/vtr_flow/arch/timing/EArch.xml  <blif-file path>  --route_chan_width 100 --analysis --disp on ``` \
The snippet shown below gives a GUI for the design tseng.blif
![DAY2_VPR_GUI](https://user-images.githubusercontent.com/52970851/208600408-637141d6-3529-469b-ac12-4dedde7a5003.png)
### VTR
Verilog-to-Routing(VTR) tool is a open-source framework for designing FPGA architectures. \
- Input File : Verilog description of the digital circuit (.v) and FPGA Architectute File (.xml)
- Output File : Peformance statistics (like area and delay (.rpt), .log)
- Note: While running the VTR flow with the ```.v``` file initially, it generates ```pre-vpr.blif``` file using which timing analysis, power analysis & post synthesis simulation is performed 
- Steps performed:
  - Elaboration & Synthesis (Odin II)
  - Logic Optimization & Technology Mapping (ABC)
  - Packing, Placement, Routing & Timing Analysis (VPR) \
#### RUN VTR IN AUTOMATIC FLOW
Command for automatic running of VTR \
``` $VTR_ROOT/vtr_flow/scripts/run_vtr_flow.py <verilog file-path> $VTR_ROOT/vtr_flow/arch/timing/EArch.xml -temp_dir . --route_chan_width 100 ``` \
Example: ``` $VTR_ROOT/vtr_flow/scripts/run_vtr_flow.py /home/sahaanaktnj/Desktop/day2_vtr/counter_files/counter.v $VTR_ROOT/vtr_flow/arch/timing/EArch.xml -temp_dir . --route_chan_width 100 ```
#### TIMING
For timing analysis provide a sdc file \
The sdc file used here is as follows:
```
create_clock -period 20  up_counter_clk
set_input_delay -clock up_counter_clk -max 0 [get_ports {*}]
set_output_delay -clock up_counter_clk -max 0 [get_ports {*}]
```
Command for Timing analysis \
``` $VTR_ROOT/vpr/vpr  $VTR_ROOT/vtr_flow/arch/timing/EArch.xml  <path of pre-vpr.blif file> --route_chan_width 100  --sdc_file <path of sdc file>``` \
Example: ``` $VTR_ROOT/vpr/vpr  $VTR_ROOT/vtr_flow/arch/timing/EArch.xml  /home/sahaanaktnj/Desktop/day2_vtr/counter.pre-vpr.blif     --route_chan_width 100   --sdc_file /home/sahaanaktnj/Desktop/day2_vtr/counter_files/counter.sdc ``` \
The timing reports obtained are as follows : \
SETUP TIMING REPORT:
![DAY2_VTR_SETUP](https://user-images.githubusercontent.com/52970851/208609581-e6d10953-2ead-4057-816c-5be70b78eb6a.png)
HOLD TIMING REPORT:
![DAY2_VTR_HOLD](https://user-images.githubusercontent.com/52970851/208609871-10badb9b-5c88-4307-a066-64b79cc8e9ff.png)
#### POST-SYNTHESIS SIMULATION
- Post synthesis simulation is performed in Xinlinx Vivado. \
- For making it work in Vivado add ```paramter K=1``` in the LUT_K module \
- Generate a post synthesis file for the design using the command ``` $VTR_ROOT/vpr/vpr $VTR_ROOT/vtr_flow/arch/timing/EArch.xml <pre-vpr.blif file path> --gen_post_synthesis_netlist on```
- Example : ``` $VTR_ROOT/vpr/vpr $VTR_ROOT/vtr_flow/arch/timing/EArch.xml /home/sahaanaktnj/Desktop/day2_vtr/counter.pre-vpr.blif --gen_post_synthesis_netlist on```
- Add the generated post synthesis file (```up_counter_post_synthesis.v```), ```primitives.v``` file and the testbench file (```counter_tb.v```) and perform behavioural simulation 
- Note : Make sure your post synthesis file and the primitives file has the same clock name \
The snippet shown below describes the post synthesis simulation of the counter 
![DAY2_VTR_POST_SYNTHESIS](https://user-images.githubusercontent.com/52970851/208644073-f90cadfa-4a01-4ce6-990d-fcd232c31375.png)
#### POWER ESTIMATION
Command for Power analysis \
```$VTR_ROOT/vtr_flow/scripts/run_vtr_flow.py <verilog-path file> $VTR_ROOT/vtr_flow/arch/timing/EArch.xml  -power -cmos_tech  $VTR_ROOT/vtr_flow/tech/PTM_45nm/45nm.xml  -temp_dir .  --route_chan_width 100``` \
Example: ```$VTR_ROOT/vtr_flow/scripts/run_vtr_flow.py  $VTR_ROOT/doc/src/quickstart/counter.v $VTR_ROOT/vtr_flow/arch/timing/EArch.xml  -power -cmos_tech  $VTR_ROOT/vtr_flow/tech/PTM_45nm/45nm.xml  -temp_dir .  --route_chan_width 100``` \
The power report obtained is shown in the snippet below \
![DAY2_VTR_POWER](https://user-images.githubusercontent.com/52970851/208634814-68111106-8434-4c71-b316-4145aefe2cf4.png)
#### UTILIZATION
The following snippets describes the resource utilization of the design
![DAY2_VTR_UTILISATION1](https://user-images.githubusercontent.com/52970851/208646727-c1b05bac-24be-4f92-b6d8-29b22c975f1b.png)
![DAY2_VTR_UTILISATION2](https://user-images.githubusercontent.com/52970851/208646737-450ac57a-65c1-4f5b-bed8-f42aa34b2ac5.png)
## DAY 3 - INTRODUCTION TO RISC-V CORE PROGRAMMING IN XILINX VIVADO
RVMYTH is a RISC-V based five stage pipelined processor. The five stages are as follows:
- Fetch
- Decode
- Execute
- Memory access
- Write \
RVMYTH is run on Xilinx Vivado from RTL design upto Bitstream generation for an addition operation of integers from 1 to 9.
### BEHAVIOURAL SIMULATION
The snippet shown below indicates the behavioural simulation of the addition of integers 1 to 9.
![day3_risc_beh_simulation](https://user-images.githubusercontent.com/52970851/208368491-6cd2f671-0114-4c7d-8b62-b0634c140495.png)
### ELABORATION
The elaborated step is done and the I/O planning report (with standard LVCMOS33) is obtained as follows:
![day3_risc_ioplan](https://user-images.githubusercontent.com/52970851/208370785-8f3a4f9d-143b-471e-9ffd-b953baba4d89.png)
### SYNTHESIS
With the elobaration being done, next step is the Synthesis. The picture shown below indicates the synthsized schematic of the design
![day3_risc_synthesized_schematic](https://user-images.githubusercontent.com/52970851/208371199-a9821b70-f43e-4d68-b319-b777ca10292e.png)
### CONSTRAINTS
For obtaining the timing summary constraints for clock are given as follows and re-synthesis is done.
```
set_property PACKAGE_PIN W5 [get_ports clk]
set_property PACKAGE_PIN R2 [get_ports reset] 
set_property IOSTANDARD LVCMOS33 [get_ports clk]
set_property IOSTANDARD LVCMOS33 [get_ports reset]
create clock -period 10.000 -name clk -waveform {0.000 5.000} [get_ports clk] 
set property C_CLK_INPUT FREQ HZ 300000000 [get_debug cores dbg_hub] 
set property C_ENABLE_CLK_DIVIDER false [get_debug cores dbg_hub] 
set property C USER SCAN CHAIN 1 [get debug cores dbg_hub]
connect_debug_port dbg_hub/clk [get_nets clk_IBUF_BUFG]
```
These constraints are added as .xdc file & resynthesis is done. Followed by it, is the implementation.
### IMPLEMENTATION
#### TIMING REPORT
The following snippet shows the timing summary of the RISCV processor \
![day3_risc_timing_summary](https://user-images.githubusercontent.com/52970851/208647636-15cc5179-fd81-4bd3-bb2e-fa7771b5ebfc.png)
#### POWER REPORT
The power report obtained is shown in the picture below \
![day3_risc_power](https://user-images.githubusercontent.com/52970851/208648166-29f2738b-52fa-4075-85f8-1358e52b57f1.png)
## DAY 4 - INTRODUCTION TO SOFA FPGA FABRIC IP
### SOFA - INTRODUCTION
&emsp;  Skywater Opensource FPGAs(SOFA) are a series of open-source FPGA IPs that utilizes the open-source Skywater 130nm PDK and OpenFPGA framework. It has Open-source FPGA IP Library which contains architecture descriptions for the user to produce ready layouts. \
&ensp; There are various High Density Embedded FPGAs available namely SOFA HD, QLSOFA HD and SOFA CHD. Out of these, QLSOFA HD design is used in this repository whose specifications are as follows:
|Resoure/Capacity|QLSOFA HD|
|-----|-----|
|LUTs|1152|
|Flip-Flops|2304|
|Soft Adders|1152|
|Routing Channel Width|60|
|Maximum Configuration Speed|50MHz|
|Maximum Operating Speed|50MHz|
|User I/O Pins|144|
|Maximum I/O Speed|33MHz|
|Core|1.8V|
### Steps to install & work with SOFA
- Install OpenFPGA
- Install SOFA using the command ``` git clone https://github.com/lnis-uofu/SOFA.git ```
- Change the directory to QLSOFA HD design using the command ``` cd FPGA1212_QLSOFA_HD_PNR ```
- To create your own design, Copy your verilog file FPGA1212_QLSOFA_HD_task/micro_benchmark directory and modify ``` FPGA1212_QLSOFA_HD_task/config/task_simulation.conf file ```
-  (Note: ```For saving the verilog file: you can also have your verilog file in FPGA1212_QLSOFA_HD_ task/BENCHMARK and along with it save the testbench and constraints file in the same folder```)
- To run the OpenFPGA use ``` make runOpenFPGA ``` from the FPGA1212_QLSOFA_HD_PNR directory
- The set of output files are available under FPGA1212_QLSOFA_HD_PNR/FPGA1212_QLSOFA_HD_task/latest/vpr_arch/counter/MIN_ROUTE_CHAN_WIDTH
- To view the utilization report of the counter open the vpr_stdout.log
### COUNTER-AREA
&ensp; The utilization report of the counter is shown in the snippets below: \
![counter_area_day4 png](https://user-images.githubusercontent.com/52970851/208295002-3d0bbb50-b7ee-44a9-ae03-6dfddbd15cce.jpg)
### COUNTER-TIMING
-For performing Timing analysis, we need to create a constraint file as follows :
```
create_clock -period 35 clk
set_input_delay -clock clk -max 0 [get_ports{*}]
set_output_delay -clock clk -max 0 [get_ports{*}]
```
- Note: Be careful while giving the clock name. The name should match with the one given in the verilog file. Example: In counter.v clock is given as ```clk``` & so the same is given in constraints 
- The next step is to edit generate_testbench.openfpga (using vim editor) which is available in the path /SOFA/FPGA1212_QLSOFA_HD_PNR/FPGA1212_QLSOFA_HD_task/generate_testbench.openfpga
- Add the following line in the vpr command (i.e., we are adding the sdc file and we are mentioning it's path)
- ``` -- sdc_file/home/sahaanaktnj/Desktop/SOFA/FPGA1212_QLSOFA_HD_PNR/FPGA1212_QLSOFA_HD_task/BENCHMARK/counter ```
- Save the file and ``` make runOpenFPGA ``` from the FPGA1212_QLSOFA_HD_PNR directory
- To view the timing reports(i.e., setup time report) open ```report_timing.setup.rpt``` which is in the path ```/SOFA/FPGA1212_QLSOFA_HD_PNR/FPGA1212_QLSOFA_HD_task/latest/vpr_arch/counter/MIN_ROUTE_CHAN_WIDTH```
The following picture describes the setup time report of the counter. Slack is positive i.e., the timing constraints are met
![counter_timing_day4](https://user-images.githubusercontent.com/52970851/208309582-2c94ad73-1c20-409e-836d-43199577e837.png)
Similarly the snippet shown below shows the hold time report of the counter
![counter_hold_timing_day4](https://user-images.githubusercontent.com/52970851/208309678-03bd68e8-c7d5-42e8-be1e-5fb56e02cc63.png)
### COUNTER POST IMPLEMENTATION
- For post synthesis add the line ```--gen_post_synthesis_netlist on``` to the vpr command of the ```generate_testbench.openfpga``` file 
- Save the file and ``` make runOpenFPGA ``` from the FPGA1212_QLSOFA_HD_PNR directory
- The post synthesis files are saved in ```/SOFA/FPGA1212_QLSOFA_HD_PNR/FPGA1212_QLSOFA_HD_task/run001/vpr_arch/counter/MIN_ROUTE_CHAN_WIDTH ```
- Open Vivado and add this post_synthesis file along with the testbench file and primitives.v file 
- Note: make sure that the clock name given in the post_synthesis file & primitives.v file are the same
- Perform Behavioural simulation \ 
The following snippet shows the behavioural simulation of the counter
![post_impl_sim_day4](https://user-images.githubusercontent.com/52970851/208311909-c7baf85f-d482-44dd-8447-b268c0b3c7d9.png)
### COUNTER POWER ANALYSIS
- For performing power analysis two files are need to be attached : Technology file(.xml) and core_ace_out.txt file \
- Add the command ```--power --activity_file /home/sahaanaktnj/Desktop/SOFA/FPGA1212_QLSOFA_HD_PNR/FPGA1212_QLSOFA_HD_task/run001/vpr_arch/counter/MIN_ROUTE_CHAN_WIDTH/core_ace_out.txt   --tech_properties  $VTR_ROOT/vtr_flow/tech/PTM_45nm/45nm.xml``` to the vpr command  
- Save the file and ``` make runOpenFPGA ``` from the FPGA1212_QLSOFA_HD_PNR directory
![day4_power_error](https://user-images.githubusercontent.com/52970851/208649767-96dc1a30-1d79-4d94-bd64-0aade701d523.png)
## DAY 5 - RISC-V CORE ON CUSTOM SOFA FABRIC 
The RISC-V based RVMyth is now implemented on the SOFA. The set of files which are needed to be modified are
- task_simualtion.conf (Note: add your verilog file path in ``` [BENCHMARKS] ``` and change the top module name in ``` [SYNTHESIS_PARAM] ```)
- generate_testbench.openfpga (Note: change the vpr command & remove the ``` --route_chan_width ${OPENFPGA_VPR_ROUTE_CHAN_WIDTH} ``` command)
- vpr_arch.xml (Note: comment lines 157,158 & 259)
- Save all the files and ``` make runOpenFPGA ``` from the FPGA1212_QLSOFA_HD_PNR directory 
![day5_error](https://user-images.githubusercontent.com/52970851/208649839-b3408b8a-7838-4094-996d-733a3c7b3e79.png)
## REFERENCES
- Xilinx Vivado : https://docs.xilinx.com/r/en-US/ug888-vivado-design-flows-overview-tutorial
- OpenFPGA : https://openfpga.readthedocs.io/en/master/
- SOFA : https://github.com/lnis-uofu/SOFA
- VTR : https://docs.verilogtorouting.org/en/latest/vtr/
- VPR : https://docs.verilogtorouting.org/en/latest/vpr/
- Github Writing : https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
- Github Table of Contents : https://ecotrust-canada.github.io/markdown-toc/
- RISCV : https://github.com/ShonTaware/RISC-V_Core_4_Stage
## ACKNOWLEDGEMENTS
- [ Kunal Gosh ](https://github.com/kunalg123) Co-Founder, VLSI System Design
- [ Nanditha Rao ](https://github.com/nandithaec) Assistant Professor, IIIT Bangalore
