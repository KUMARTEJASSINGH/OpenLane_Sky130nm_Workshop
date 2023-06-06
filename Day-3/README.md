
# Day 3

## Content
- CMOS inverter ngspice simulations
- Inception of Layout and CMOS Fabrication Process
- SKY130 Tech File Labs
### IO placer revision

To change the configuration of how the IO pins are placed 

    set ::env(FP_IO_MODE) 2

![imageconfigchange](https://github.com/KUMARTEJASSINGH/OpenLane_Sky130nm_Workshop/assets/89102342/ddc1642a-f00f-4299-a70a-8c239e5c5a30)

### SPICE deck creation for CMOS inverter

Lets say for a given circuit:

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

![imageofinverter](https://github.com/KUMARTEJASSINGH/OpenLane_Sky130nm_Workshop/assets/89102342/b9bf2c09-210b-4a5a-b843-b65915a52bc6)


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
![extractingimage](https://github.com/KUMARTEJASSINGH/OpenLane_Sky130nm_Workshop/assets/89102342/70b41738-b3e9-47e5-9080-c8464d59708c)
![extractingimage2](https://github.com/KUMARTEJASSINGH/OpenLane_Sky130nm_Workshop/assets/89102342/abd03eb4-7446-401d-8d5f-cfc432274c1b)


![Code](https://github.com/KUMARTEJASSINGH/OpenLane_Sky130nm_Workshop/assets/89102342/621d77a4-74f5-48f2-9f43-0cc90e4c6434)
