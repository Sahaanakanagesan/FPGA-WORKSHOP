# FPGA-WORKSHOP
This repository contains all the information gathered during a five day workshop on [ FPGA - Fabric, Design and Architecture by VSD ](https://www.vlsisystemdesign.com/fpga/) conducted from 14/12/2022 to 16/12/2022 which covers tools like Xilinx Vivado, Open FPGA, VTR, VPR and SOFA

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
![DESIGN METHOD](https://user-images.githubusercontent.com/52970851/208065160-47c3f771-95fb-4d4b-a0a1-e0d39c0f5108.png)
### BASYS3





## REFERENCES

## ACKNOWLEDGEMENTS
- [ Kunal Gosh ](https://github.com/kunalg123) Co-Founder, VLSI System Design
- [ Nanditha Rao ](https://github.com/nandithaec) Assistant Professor, IIIT Bangalore
