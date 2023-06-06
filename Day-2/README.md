
# Day 2

## Content
- Chip Floor planning 
- Library Binding and Placement
- Cell Design and characteristics flows
- General Timing and characterization parameters

### Chip Floor planning 
#### Utilization factor and aspect ratio

Width and height of Core and Die

In the physical design flow, the first step is to define the height and width of the core and the die

Netlist : A netlist describes the connectivity of electronic design.
<img circuit>

- While defining the dimesions of the chip we are mostly dependent on the dimesion of the logic gates in the netlist.

- We are mostly intersted in dimesion of the standard cell rather than the wires for finding dimesions of the die and core.
- Wegive dimension to each cell and find its area, the sum of all the cell is total area.
- If we try to club them together, the area covered by the combined cells is minimum area.

<img combined>

<img core>
Core is section of the chip where the fundamental logic of the design is placed.

<dieimage>

                           Area occupied by the netlist
    Utilization factor = ------------------------------
                            Total area of the core

if Utilization factor is 1, it means core is completely occupied.

                    Height
    Aspect ratio = --------
                    Width
 Whenever Aspect ratio is 1 it signifies that the chip is square shaped, otherthan one it specifies that chip is rectangle shaped.

#### Concept of pre-placed cells

Let's say we have a combinational circuit with 100 gates (let it be any functional circuit like mux, memory etc.), These cells can be implemented seprately.
The advantage is that say if the circuit is repeated multiple times we can black box it and then that part can be implemented seprately, or it can be reused.
So similarly we have such IP's in which we give input and get output and they can be used in the circuit, these are called pre-placed cells.

- The arrangement of these IP's in the cell is called Floorplanning.

- These IP's have user defined locations and hence are placed in the chip before automated placement-and-routing and are called preplaced cells.
- Automated placement and routing tools places the remaining logical cells in the design onto chips.

#### De-coupling capacitors
Once the preplaced cells are placed depending on the design scenario, their locations can't be moved.


What happens is that the capacitance that is there,when a transition happens from 0 to 1 it has to completely charge to represent logic 1 and the amount of charge will be sent from the supply voltage(Vdd).
Also whenever any piece of logic is switching from 1 to 0 it is respnsibility of Vss to take that amount of charge. So when we say from logic 1 to 0 the capacitance in the circuit will get discharged.

If a signal is connected using physical wires it faces voltage drop.
For any signal to be considered "0" and "1" it should be in the NML and NMH(noise margin) range. If it lies in between these region it can either be "1" or "0" that means that it is in unsafe region.

We surround the cells by De-coupling capacitors. It is a capacitor with huge charge. So it means that it decouples the circuit from the main supply.

- So when ever there is a switching activity happening decoupling capacitor will send current to charge. So the amount the current the circiut need is supplied by this capacitor.

<Image decouples capacitor>

#### Power planning

When connecting from one preplace cell to the other, the power supply needs to connect and to drive the signal using decoupling capacitors always is not possible.

- Ground bounce refers to the undesired voltage fluctuation in the ground reference plane caused by the simultaneous switching of multiple digital signals. When a large number of gates or circuits switch from logic 0 to 1 or vice versa, it creates a momentary surge of current demand. This current flows through the power distribution network and generates voltage fluctuations due to the parasitic inductance and resistance of the power and ground connections which leads to it going to undefined states which cause it going to either "1" or "0" unpredictably.
- when multiple capacitances in a circuit need to charge simultaneously, they collectively demand a significant amount of current from the power supply. This simultaneous current draw can lead to a phenomenon called voltage droop

<image groundbounce>
<image voltage droop>

- Distributing this power source and having multiple power supplies may solve the above problems.
<pinplac image>

#### Pin placement and logical cell placement blockage

- The netlist connectivity is being defined in seprate language called VHDL/Verilog. The different connectivity of gates is called netlist.
- The frontend team connects the netlist whereas the backend team look after the pin placement, this is where handshking takes palce.
- As clocks are driven continously we assign bigger area to them as bigger the area, lesser will be the resistance.
- Then, logical cell placement is bloackage is done inorder to avoid any cells getting placed by automated PnR.

#### Steps to run floorplan using OpenLANE
Path to view the different configuration and their default values

    /Desktop/work/tools/openlane_working_dir/openlane/configuration

then enter 

    less README.md
<img readme>

 It has variable that can be set if we want in synthesis flow. 

These switches are set in .tcl files
for example for the floor plan stage

    less floorplan.tcl
 <img variale>

The highest priority for these defaults will be sky130A_sky130_fd_sc_hd_config.tcl, and then config.tcl, and then the tcl file inside the folder as shown above.

Now to run floorplan

    run_floorplan

<img run floorplan>

#### Review floorplan files and steps to view floorplan

<configtcl image>
<sky130a tcl image>

Goto the address given below

    /Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/03-06_05-21/results/floorplan

and type

    less picorv32a.floorplan.def

<die area image>

we can see Die area, it is area of complete die, it shows upper right x value and upper right y value. Using this we can calculate area of die.

- microns 1000: 1 micron = 1000 database units

#### Review floorplan layout in Magic

We can see actual floor plan in Magic

    magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &

- For aligning the layout to the centre, press s to select the entire layout and press v to align it to the centre.
- To zoom at any part firt do left mouse click and then right mouse click at the part where we want to zoom and then press Z.
- To select anything take cursor there and press s.

<image what>

<image tapcell>

Tapcells are meant to avoid the latchup conditions which occur in CMOS devices, they connect nwell to vdd and substrate to ground. These tapcells are diagonally equidistant, this value has also been set. The bottom left of the floorplan will have the standard cells

## Library Binding and Placement
###  Netlist binding and initial place design

In Placement and routing stage of the physical design flow the first step is to bind the netlist with physical cell.

- Netlist binding is the process of associating the logical gates in a netlist with their corresponding physical cells or library cells.

- In a netlist, the gates are represented by their logical functionality, such as OR, AND, etc. However, in the physical design flow, these gates are implemented using specific physical cells from a library.

- The library contains information about different physical cells or cells with specific functionality, along with their physical dimensions, delay information, width and height conditions, and other parameters.

- During netlist binding, the gates in the netlist are matched with the appropriate physical cells from the library based on their functionality.


- Initial placement is the process of assigning the physical cells to specific locations on the floorplan of the integrated circuit (IC) design.


- The placement of cells is typically done in close proximity to their respective IO pins to minimize delays caused by long interconnects.

### Optimize placement using estimated wire-length and capacitance

Repeaters are used to optimize the circuit without much power loss. They replicate the signal and produce and new signal. So the problem of signal integrity is solved by addin g buffers or repeaters, but there is loss of area.

### Congestion aware placement using RePlAce

- Global placement is basically port placement and there is no legalization happening here.
- Detail placement - standard cells are set in rows without  any overlap.
The main objective is reducing the wire length.

    run_placement

<img placement>

Go to the directory 

    /Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/03-06_05-21/results/placements

and enter command

    magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

<image2 placement>
Zoomed version of the image.
<image3 placement>


In the physical design flow of integrated circuits, the power-ground network is responsible for establishing a robust power distribution system and ensuring proper grounding. 

However, in OpenLANE, the power-ground network is not created during the placement stage. Instead, it is generated in a later stage, specifically after floorplanning, placement, and clock tree synthesis (CTS). Post-floorplan and post-placement, OpenLANE performs power-ground generation just before routing. This crucial step involves creating the power distribution network, connecting power and ground pins of cells and IOs, and preparing the chip for routing. The power-ground network is essential for maintaining stable power supply and effective grounding during the subsequent routing stage.

## Cell design and characterization flows

Standard cells are placed in library.
Library has cell with different functionality and different sizes. These different sized cells will directly have a change in the characteristics.

- Input : Process design kits (PDKs), they include DRC & LVS rules, SPICE models, library and user-defined specs. It is provided by the foundry.
- Design steps : Circuit design (using equations by finding w/l ratio of pmos in term of nmos), Layout design (Obtaining Euler's path and then making stick diagram), Extraction of parasitics, Characterization (timing, noise, power).

MAGIC tool is one of the tool that could be used to make layouts.

Steps for characterization flow :

1. Read in the model, it comes from the industry or foundry.
2. Read the extracted SPICE netlist.
3. To recognise the behaviour of the circuit.
4. To read the subcircuit of the inverter.
5. Attach the necessary power sources.
6. Apply the stimulus.
7. Provide the necessary output capacitances.
8. Provide the necessary simulation commands.

These steps are fed in together as a configuration file to a characterization software called GUNA.

- Outputs : CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib, function

## General timing characterization parameters

### Timing threshold definitions

    | Timing defintion     |	Value   |
    | slew_low_rise_thr    |  20% value |
    | slew_high_rise_thr   |  80% value |
    | slew_low_fall_thr    |  20% value |
    | slew_high_fall_thr   |  80% value |
    | in_rise_thr	       |  50% value |
    | in_fall_thr	       |  50% value |
    | out_rise_thr	       |  50% value |
    | out_fall_thr	       |  50% value |

### Propagation delay

#### Delay
It is  out - in
    Delay = time(out_*_thr)-time(in_*_thr)

Circuit design steps are very very important as they may cause negative delay which is not acceptable.

#### Transition time 
    Transition time  = slew_high_rise_thr - slew_low_rise_thr


# Day 3

## Content
- CMOS inverter ngspice simulations
- Inception of Layout and CMOS Fabrication Process
- SKY130 Tech File Labs
### IO placer revision

To change the configuration of how the IO pins are placed 

    set ::env(FP_IO_MODE) 2

<image11 configchang>

### SPICE deck creation for CMOS inverter

Lets say for the given circuit:
<imaged3circuit>
- output load calculated here is 10fF
- W/L ratios 
    PMOS: 0.375/0.25 u
    NMOS: 0/375/0.25 u
- PMOS Should be wider than the NMOS
Spice deck
1. Component connectivity - Connectivity of the Vdd, Vss,Vin, substrate.
2. Component values - values of PMOS and NMOS, Output load, Input Gate Voltage, supply voltage.
3. Identify Nodes
4. Name Nodes - Nodes are required to define the SPICE Netlist.


        M1 out in vdd vdd pmos W=0.375u L=0.25u

        #drain gate source substrate

        M2 out in 0 0 nmos W=0.375u L=0.25u
        cload out 0 10f
        Vdd vdd 0 2.5
        Vin in 0 2.5
        *** Simulation commands ***
        .op
        .dc Vin 0 2.5 0.05

        *** include tsmc_025um_model.mod ***
        .LIB "tsmc_025um_model.mod" CMOS_MODELS
        .end

The shape of wavefrom of PMOS and NMOS are same which tell us, that CMOS is very robust.

Switching Threshold : Switching Threshold of a CMOS inverter is the point where the Vin = Vout on the DC Transfer characreristics. Here both the transistors are in saturation region.

### Lab steps to git clone vsdstdcelldesign

clone from the github repo 

    git clone https://github.com/nickson-jose/vsdstdcelldesign.git

    cd pdks/sky130A/libs.tech/magic/
    cp sky130A.tech pwd

    magic -T sky130A.tech sky130_inv.mag &


& at the end makes the next prompt line free, otherwise magic keeps the prompt line busy. Once we run the magic command we get the layout of the inverter in the magic window.

<image of inverter>


#### CMOS Fabrication Process

The CMOS fabrication process involves several steps that are essential for the production of CMOS (Complementary Metal-Oxide-Semiconductor) integrated circuits. Here is an overview of the 16-mask CMOS fabrication process, including key steps and their corresponding descriptions:

- Substrate Selection:
The first step in the CMOS fabrication process is selecting a suitable substrate, typically a P-type substrate, to serve as the foundation for the integrated circuit.

- Active Region Creation:
The active regions for the transistors are created by using Mask 1. This mask defines the areas where transistors will be fabricated.

- Isolation:
To isolate the active regions from each other, an isolation layer is formed. This is achieved by depositing an oxide layer (SiO2), followed by a layer of silicon nitride (Si3N4). A photoresist layer is then applied and patterned using photolithography. The patterned photoresist serves as a mask for etching the silicon nitride layer. Finally, the remaining photoresist is removed, and the structure is placed in an oxidation furnace, where the oxide layer grows in the exposed regions. This process is known as LOCOS (Local Oxidation of Silicon). The silicon nitride layer is stripped using hot phosphoric acid.

- N-well and P-well Formation:
N-well and P-well regions are formed separately to facilitate the fabrication of NMOS and PMOS transistors, respectively. Photoresist, masking, photolithography, and ion implantation techniques are employed to create the desired well structures. The P-well is formed by diffusing a p-type Boron material into the P-substrate, while the N-well is formed by diffusing an n-type Phosphorus material. The process of creating these wells is referred to as the "tub process" and involves high-temperature furnace processes to achieve the desired well depths.

- Gate Formation:
The gate is a crucial component of CMOS transistors, as it controls the threshold voltages that determine the transistor's on/off states. To form the gate, a layer of polysilicon is deposited, and photolithography techniques are employed to define the gate structure for both NMOS and PMOS transistors. The gate formation process considers parameters such as oxide capacitance and doping concentration.

- Lightly Doped Drain (LDD) Formation:
To mitigate the impact of hot electron effects and short channel effects, a lightly doped drain (LDD) region is formed. This involves additional ion implantation steps to introduce dopants in specific areas of the transistor structure.

- Source and Drain Formation:
A thin layer of oxide is added to protect the channel during subsequent implantation steps. Source and drain regions are formed through ion implantation, where n+ and p+ implants are performed using arsenic implantation and high-temperature annealing.

- Local Interconnect Formation:
The thin screen oxide is etched using a hydrofluoric (HF) solution. Local interconnects are then formed through a series of steps, including depositing titanium on the wafer using sputtering. Heating in a nitrogen (N2) ambient triggers a chemical reaction, resulting in the formation of low-resistance metal contacts (titanium silicon dioxide) for interconnect contacts. Titanium nitride is also formed and used for connections that need to be brought to the top for local communication. The titanium nitride layer is etched using RCA cleaning.

- Higher Level Metal Formation:
To achieve a planar surface for metal interconnects, chemical mechanical polishing (CMP) is performed. This involves doping silicon oxide with Boron or Phosphorus to polish the surface


#### Lab steps to create std cell layout and extract spice netlist

- To extract SPICE, in the tkcon window

    extract all 
    ext2spice cthresh 0 rthresh 0
    ext2spice
<image extracting image 1 and 2>

<IMAGE CODE>
