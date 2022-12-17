# FPGA-WORKSHOP
This repository contains all the information gathered during a five day workshop on [ FPGA - Fabric, Design and Architecture by VSD ](https://www.vlsisystemdesign.com/fpga/) conducted from 14/12/2022 to 18/12/2022 which covers tools like Xilinx Vivado, Open FPGA, VTR, VPR and SOFA

## DAY 1 - BASICS OF FPGA & XILINX VIVADO
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
Basys3 FPGA board with its callouts is shown in the picture below
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
## 4-BIT COUNTER EXAMPLE IN XILINX VIVADO
### Verilog code
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
        //if (count_reg == 26'h33) // for synthesis 
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

## REFERENCES

## ACKNOWLEDGEMENTS
- [ Kunal Gosh ](https://github.com/kunalg123) Co-Founder, VLSI System Design
- [ Nanditha Rao ](https://github.com/nandithaec) Assistant Professor, IIIT Bangalore
