
# Day-1

#### Inception of open-source EDA, OpenLANE and Sky130 PDK



## 

What is Physical Design?

Physical design is the process of converting a logical circuit into a physical layout that can be fabricated on a chip. It involves tasks like dividing the chip area, placing components, routing interconnections, and optimizing timing. The goal is to create an efficient and functional design.

Let's briefly discuss each steps takes place in Physical design:
- Floorplanning - Floorplanning is the initial step in physical design where the available chip area is divided into regions for different circuit blocks. It determines the overall chip dimensions and establishes the relative placement of these blocks. The goal is to optimize the chip's layout for performance, power, and area.
- Placement - Placement is a step in the physical design process where the locations of individual components, such as logic gates and memory cells, are determined within their designated regions on the chip. The goal of placement is to optimize the physical arrangement of components to achieve desired objectives such as performance, power, and area.
- Clock Tree analysis - The clock tree is responsible for delivering clock signals to various components and sequential elements within the IC, ensuring synchronization and proper operation. The design is analyzed to ensure that the clock signals reach all the required elements within specified timing constraints. The goal is to minimize clock skew and latency, which can impact the overall performance and functionality of the circuit.
- Routing - Routing involves connecting the components and their interconnections on the chip using metal wires. The goal of routing is to establish the required electrical connections between different circuit elements while considering various constraints such as timing, signal integrity, power, and area.
- Parasitic extraction - Parasitic effects can significantly impact the performance of the circuit, including timing delays, signal integrity issues, and power consumption. By extracting the parasitic elements from the layout, such as resistance, capacitance, and inductance, an accurate representation of the circuit is obtained for further analysis and optimization.
## Content
1. How to talk to computers
2. SOC design and OpenLane
3. Open-source EDA tools

## 1. How to talk to computers

### QFN-48 Package, chip, pads, core, die and IPs

QFN-48
Here QFN refers to Quad Flat No-leads, and number 48 indicates the package's pin count, The chip is at the center of the package and it is connected using wire bonds with the pins as shown in the figure.

Pads: Pads, also known as input/output (I/O) pads, are areas on the surface of an integrated circuit where external connections can be made. Pads provide a means of connecting the chip to the outside world, typically through wire bonding.

Core: Area where all the digital logic are placed.

Die: The size of the entire chip and area where I/O pads sit. The die is typically cut from a larger wafer during the semiconductor fabrication process

Foundry is a critical term in chip design, it is a place where chips are manufactured. 

Foundry IPs : Foundry IPs are pre-designed and pre-verified circuit blocks offered by semiconductor foundries. They simplify chip design by providing ready-made components tailored to the foundry's manufacturing processes.

### RISC-V architecture
The RISC-V architecture is an open-source instruction set architecture (ISA) designed for computer processors. RISC-V stands for "Reduced Instruction Set Computing - Version 5"

RISC-V --> Implementation --> Layout
RISC-V can be thought of as specification and RTL implements this specification and then from RTL to Layout it is standard GTS flow.

#### From Software Applications to Hardware
Software Applications enters a box called system software which converts it into binary language.
Major components of system software are 
operating system, compiler and assembler.

Operating System - It handles IO operations, allocates memory and convert program to assembly language so it is understood by the hardware 

Compiler - It converts the source code written in a high-level programming language into typically machine code or an intermediate representation specific to the target hardware architecture that can be directly executed by a computer's hardware.

Instructions: The generated machine code or intermediate representation from the compiler consists of a series of instructions that represent the operations to be performed by the hardware. These instructions are specific to the target hardware architecture and are designed to be executed by the processor.

Assembler -  It takes the instruction from the compiler and converts it into binary format which goes to hardware.



## 2. SOC design and OpenLane

ASIC enablers
- RTL design : It stands for Register Transfer Level design, is a hardware design methodology used to describe digital circuits at a higher level of abstraction. It focuses on capturing the flow of data between registers and the operations performed on that data. In RTL design, the behavior and functionality of the circuit are specified in terms of the transfer of data between registers and the control logic that governs those transfers.

- EDA tools : They are software applications used in the design, analysis, and verification of electronic systems and integrated circuits. These tools assist in various stages of the electronic design process, from concept and schematic capture to layout, simulation, synthesis, and timing analysis.

- PDK : It stands for Process Design Kit, it is collection of files used to model a fabrication process for the EDA tools used to design an IC. It is an interface between the FAB and designers.

On June 30, 2020, Google with skywater released first open source PDK for the masses.

#### RTL TO GDSll FLOW

    RTL                      PDK                 GDSll 
    ||                       ||                   /\  
    \/                       \/                   ||     
    Synth -> FP+PP -> Placvvvv e -> CTS -> Route -> Sign off

- Synthesis : Coverts RTL to circuit out of components from the standard cell library (SCL). Resultant circuit is described in HDL and referred to as gate level netlist.

<image 2 here>
    
- Floor/Power Planning : The objective here is to plan the silicon area and create robust partition.
    - Chip Floor-Planning : Partition the chip die between different system building blocks and place I/O Pads.
    - Macro Floor Planning : Dimensions, pin location, row defination
    - Power Planning : A power network is constructed, typically the chip is powered by multiple VDD and ground pins. The power pin is connected to all components through rings

- Placement : 
    - Global Placement : optimal placement for all cells
    - Detailed Placement : The one obatined after Global placement are minimally altered.
- Clock Tree Synthesis : It delivers clock to all clock cells eg. flipflop. 
- Routing : Implement the interconnect using the available metal layers.
- Sign off : This include DRC or LVS.

#### Introduction to OpenLANE and Strive chipsets
It is open-source and free.

It started as an Open-Source Flow for True Open Source Tape-out Experiment.

StriVe SoC Family
<image 3 here>

OpenLANE 
The main goal is to produce a clean (no LVS and DRC violation) GDSll with no human intervention.
Two modes of operation
1. Autonomous :  its like a push button flow, does everything automatically
2. Interactive :  runs through commands

#### OpenLANE detailed ASIC design flow

Opensource tools used for OpenLANE:
- OpenROAD
- MAGIC VLSI
- KLayout
- Fault
- Yosys
- QFlow
- ABC

<image 4 here>

1. The process starts with RTL design, the RTL is fed to Yosys with the design constraints, Yosys converts the RTL into a logic circuit using generic components, this circuit can be optimized and mapped into library using abc.
2. ABC has to be guided during the optimization, and guidance comes in the form of ABC strategies, OpenLANE comes with various strategies where some tragets least area and the other on best timing, this is called SYNTHESIS EXPLORATION.
3. OpenLANE has design exploration which can be used to sweep the design config. and it shows report in the form of matrix, it generates the number of violations generated, this is useful to find the best configurations
4. OpenLANE Regression Testing : design exploration utility is also used for regression testing.
5. DFT(Design For Test) : It can be used after fabrication for testing also, It uses opensource project Fault to
- Scan insertion
- Automatic test pattern generation (ATPG)
- Test patterns compaction
- Fault coverage
- Fault simulation
6. Physical Implementation : It involve several steps and all of them is done by OpenROAD. So we use it for 
- Floor/ Power planning
- End decoupling capacitors and tap cells insertion
- Placement: Global and detailed
- Post placement optimization
- Clock Tree synthesis
- Routing: Global and detailed
7. LEC(Logic equivalence checking) : It is done using Yosys, we compare the netlist resulting from thr optimization done during physical implementation to the gate level netlist, to make sure they are functionaly equivalent.
8. Antenna : When a metal wire segment is fabricated, it can act as antenna. It can collect charges which can damage the transistor gate that is connected to that wire. So we have two solutions:
- Bridging : In this we bridge using top layer so we go upto the top layer and drop back to to the metal layer that has long wire segment.
- Antenna diode : Insert the diode next to the transistor that is getting effected by this long wire.
9. As a preventive measure, a fake antenna diode is added and placed next to every cell during the placement stage of the OpenLANE ASIC flow. The fake antenna diode is a placeholder component that mimics the characteristics and dimensions of a real diode but does not have any actual functionality.After the placement and routing stages, MAGIC is utilized to perform antenna violation checks on the routed layout. When an antenna violation is detected on a cell input pin, the fake antenna diode placed next to that cell is replaced with a real diode.
10. Sign off : Timing signoff involves interconnect rc extraction from the routed layout followed by STA performed by openSTA and reports timing.

# LAB

## 3. Open-source EDA tools

### OpenLANE Directory structure in detail

OpenLANE : It is a flow which comprises of many open-source EDA tools such as Yosys, openSTA etc. The basic aim of having OpenLANE is to have a complete RTL2GDSII flow.

When we invoke the OpenLANE tool, its directory is

    /Desktop/work/tools/openlane_working_dir/openlane
<all images and various further text here desicribing all the file viewed>

### Design Preparation Step

Enter the directory mentioned above the type

    docker

    ./flow.tcl -interactive
It runs the script in interactive mode
<image>

Now we require to import all the pacakages that we will require
    
    package require openlane 0.9

All designs that are run on OpenLANE are extracted from design folder, we can also add our designs in it. Firstly we will be seeing picorv32a, from

    /Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a

It has 3 kind of files
1. src file: Verilog file for RTL will be present and sdc information
2. config.tcl - bypasses any configs already done into openLANE., i.e, many of the switches already have a default value. The below image shows the default values

 <img 2 config.tcl>

Going back to Open prompt
before running synthesis, we need to prepare the design setup stage, we need to set file system specific to the floor

    prep -design picorv32a

<Img 3 prep>

here in mergeLef.py step two LEF files are merged to one
1. Techlef 
2. lef (Cell lef).

<img4>

Here in tmp file we have the file merged.lef

To run synthesis

    run_synthesis

<img4 final synthesis>

### FLOP RATIO

Flop ratio refers to the proportion of flip-flops or 
registers used in a digital circuit design relative to the total number of logic gates or combinational elements.

Flop ratio = number of D Flip-Flops/ number of cells

Number of cells = 14876

Number of D Flip-Flop = 1613

Flop ratio : 0.10842968

Flop count percentage: 10.8429

<img5>
<img6>

## OpenLANE Github

The following commands should be run in sequence:

1.     prep -design <design> -tag <tag> -config <config> -init_design_config -overwrite similar to the command line arguments, design is required and the rest is optional


2.     run_synthesis

3.     run_floorplan
4.     run_placement
5.     run_cts
6.     run_routing

7.     write_powered_verilog followed by set_netlist $::env(routing_logs)/$::env(DESIGN_NAME).powered.v
8.     run_magic
9.     run_magic_spice_export
10.     run_magic_drc
11.     run_lvs
12.     run_antenna_check