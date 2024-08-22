# Advanced Physical Design using OpenLANE and Sky130 PDK
> This repository is developed as a part of Digital VLSI SoC Design and Planning Workshop conducted by VLSI System Design.  
> The training is arraged by Ansys Software Pvt. Lmt.  
> The training covers the RTL2GDSII Workflow.

## How to talk to computers?
  
### Introduction to Board, Package, Chip, Pads, Core, Die, IC and IP
In this section, we will understand the difference between the terms Board, Package, Chip, IC, etc.
#### Board
A **Printed Circuit Board (PCB)** is a flat board made with insulating material and has conductive pathways carved on its surface to connect various electrical components such as microprocessors, sensors, resistors, capacitors, diodes, transistors, etc.
The commonly available boards in the market such as Arduino are examples of PCBs.
![Arduino UNO](https://github.com/user-attachments/assets/77f9ef11-a959-4d07-84f0-d5704352b8ee)

The typical components on a Board can be understood with the help of a diagram.
![Block diagram of a Board](https://github.com/user-attachments/assets/db193ea3-ba69-4069-a54a-63b48b67fc1a)

The Black color component (Highlighted in Yellow) sitting at the centre of the Board is the Processor/SoC.
The rest of the components such as the GPIO pins, Power pins, external memory chips such as SDRAM, JTAG-UART pins to program the processor, ADCs, etc. are located around the Processor.

#### Package
A **Package** is a supporting case within which the Chip is encapsulated. It prevents physical damage and corrosion.
The Package also supports the electrical contacts which connect the Chip to the Board.  
There are different types of Packages available in the market such as Quad Flat No-Lead Packages (QFN-48).  
This is a 7x7mm<sup>2</sup> package with 48 pins.

![A Typical Package](https://github.com/user-attachments/assets/9d640fc8-6062-4353-b4c3-34f32b3098c0)

#### Chip
A **Chip** typically sits at the centre of a Package. The Chip is connected to the Package using _Wire Bonds_. 
This enables the Chip to communicate with the outside world.  
<img src="https://github.com/user-attachments/assets/14473911-230e-4ed1-b2b1-fd8236dc6ef9" width="507">
<img src="https://github.com/user-attachments/assets/38392a8c-ba26-42b5-b4de-b4e310ab1dcd" wicth="400">

#### Pad
A **Pad** is the peripheral area of a Chip on which the Pins are soldered to. These are small conductive areas on the surface of the Chip.
They make electrical connections between the internal circuitry of the chip and the external package leads or pins.
They are typically made of a metal such as aluminum or copper.
The signals coming into the chip come through the Pads.

#### Core
The **Core** of the Chip is located within the Pads. The Core is where the digital logic of the Chip is present.

#### Die
The **Die** encompasses both the Core and the Pads. The Die is manufactured on a Silicon Wafer.
Infact multiple dies are manufactured on a single Silicon Wafer.  

![Die](https://github.com/user-attachments/assets/b31cdf31-c76e-4126-86e9-881b84f7246b)

#### IPs and Macros
Multiple elements sit within the Core of a Chip. It includes the SoC, SPI, PLLs, ADCs, DACs, SRAM, etc.  
PLLs, ADCs, SRAMs are **Foundary IPs**.These are patented by the Foundrys and can be used by the Chip Designer.  
The repeatable blocks of digital logic are called **Macros**.  

![IPs and Macros](https://github.com/user-attachments/assets/96cfe919-c296-409d-a954-d62cd04c1479)

### Introduction to RISC-V Architecture
#### How is a High Level Language such as C, C++ executed on a Physical Layout?
**Instruction Set Architecture (ISA)** is the interface between software and hardware. It specifies what the processor is capable of doing and how it gets done.  
If a C-program needs to be run on hardware, then it is first compiled into its **Assembly Level Language** (ALL) Program by a Compiler. Each Assembly Language instuction maps to a specific machine instruction in the ISA.  
The Assembler converts the ALL Program into ISA which is specific to each processor. For e.g. the **RISC-V** (Reduced Instruction Set Computing - V) ISA.  
The ISA is nothing but Machine Level Language or binary code that contains 0's and 1's that are the digital logic signals executed on a hardware.  
In order that the Hardware supports all the machine code provieded by the ISA, the ISA specifications must be implemented using a **Hardware Description Language** / **Register Transfer Level (RTL)** Code such as **Verilog**.  
This RTL Code/Netlist is converted into the GDS following the standard PnR Flow or the **RTL2GDS** Flow.

![HLL to Physical Layout](https://github.com/user-attachments/assets/c63f03b8-fada-4f13-bcc7-7fc150ed1465)

Detailed Version of High Level Language Conversion to Machine Level Language

![Detailed Version](https://github.com/user-attachments/assets/2db190a3-558b-4449-8445-67eed15ace73)

#### How is binary code executed on a Hardware?
Because of the ISA, if a hardware receives a particular pattern of binary digits, it understands that it has to perform a specific operation.
For e.g., the pattern 00110010101011000011 implies addition.  
The HDL of the ISA has to be written to make the Hardware implement the binary code.  
The RTL is synthesised into a Netlist which consists of Standard cells, registers etc. that can be implemented on a Layout.
This is process of converting the RTL to GDS (Layout Information file) is known as Physical Design or RTL2GDS Flow.

![ISA to Hardware](https://github.com/user-attachments/assets/6aa5b4ae-c0f0-4b4e-85b0-14aacfe0a00c)

## Introduction to SoC Design and OpenLane
### Digital ASIC Design using Open Source Materials
ASIC Design requires 3 main components:
1. RTL, IPs
2. PDKs
3. CAD Tools / EDA Tools

The table lists the available Open Source options for the above mentioned components.
| Component | Open Source |
|-----------|-------------|
|RTL        | librecores.org, opencores.org, github.com |
|EDA Tools  | Qflow, OpenRoad, OpenLane, Magic |
|PDKs       | Google+Skywater 130nm Production PDK |

#### What is a PDK?
A **PDK** or a **Process Design Kit** is the interface between the Fabs and the Designer. It contains the files used to model a fabrication process for the EDA tools used to design an IC.
It contains
1. Process Design Rules - DRC, LVS, PEX
2. Device Models
3. Standard Cell Libraries
4. I/O Libraries
5. etc.

ASIC Design Fow is a complex process that involves many steps. **OpenLane** is a software that streamlines this flow, and brings all the required open source components for the ASIC Design Flow under one umbrella.

### Simplified RTL to GDS Flow
The ASIC Design Flow consists of muliple steps such as Synthesis, Floor Planning, Power Planning, Plcaement, Clock Tree Synthesis, Routing and Signoff/Tape-out.

![Simplified RTL to GDS Flow](https://github.com/user-attachments/assets/a9ca0a77-3d42-47fb-a527-fb39a574fa99)

#### Synthesis
Synthesis converts the RTL to a circuit that can be constructed from the cells of the Standard Cell Library.
The output of Syntesis is a **Gate Level Netlist**. It is functionally equivalent to the RTL.

![Synthesis](https://github.com/user-attachments/assets/6c0a28ce-523a-479e-83bd-118e5af1ca6e)


The Standard cells from a library can be implemented on a layout in a very even fashion as they have defined heights and widths that are multiples of the basic standard cell inverter with 1X drive strength.  
Each Standard cell has different models/views that are interpreted by different EDA Tools.  
The Liberty View includes Electrical Model of the cells such as Delay Models and Power Models.  
The Spice View or the CDL View of the cells is present.
The cells also have Layout Views - GDSII Views and LEF Views.

#### Floor Planning and Power Planning
Floor Planning involves determining the shape and size of each bloack and allocating it a specific place in the Core of the Chip. Therby it helps in defining the routes between the blocks.
**Chip Floor Planning** - Partitions the chip between different building blocks and places the I/O Pads.
**Macro Floor Planning** - The dimensions, Pin locations and rows are defined for the Macros.
**Power Planning** - Creates a power grid such that each and every macro, standard cell and all other blocks present in the design are connected to the Power Grid.
It consists of horizontal and vertical metal straps. This structure reduces the resistance and hence the IR Drop. Typically, uppper metal layers are used for Power Distribution Network as they are thicker than lower metal layers. Thus have lesser resistance.

![Chip Floor Planning](https://github.com/user-attachments/assets/5f65821a-1a6a-4b92-9b7f-67f9b9736100)
![Power Planning](https://github.com/user-attachments/assets/c4e83de4-31b3-4c41-8c35-eaff062d84c5)

#### Placement
**Placement** involves assigning a location to every standard cell in the design.  
The objectives of Placement include placing all the standard cells in legal locations, minimize the critical net delays in the design and reduce possibilities of routing congestion.  

![Placement](https://github.com/user-attachments/assets/2c8d91a4-861b-4a60-801c-37fb771006c9)

Placement is typically done in 2 steps.
1. **Global Placement**: During this step, each cell is placed in a rough position, some of the constraints may be violated here. Like, certain cells may overlap. It tries to find an optimum position for each cell.  
   ![After Global Placement](https://github.com/user-attachments/assets/213dd13d-bf23-4a43-9387-77fadb3d0484)

2. **Detailed Placement**: During this step, the position of each cell is fixed making sure all the placement constraints are met and everything is legal.  
   ![After Detailed Placement](https://github.com/user-attachments/assets/b5a5bc13-4bf7-4e41-b06b-476c5c440240)

#### Clock Tree Synthesis
During **Clock Tree Synthesis** the clock signal is connected from the clock port to clock pin of all the sequential elements in the design.  
The clock network is usually designed as a Tree structure (X-shaped, H-shaped, etc.) to ensure that clock is delayed to every element with minimum skew.
Clock Buffers and Inverters are inserted in the Clock network to balance the skews and delays.  
![CTS](https://github.com/user-attachments/assets/98ed5404-51a1-4530-93ed-ca1882c71918)

#### Routing
**Routing** is the process of creating physical connections between the signal pins of various cells in the design.  
The PDK defines the available metal layers and their thickeness, pitch and the minimum width. The Router uses this information to create a pattern of horizontal and verical metal tracks to achive the specified connections in the netlist. The PDK also defines the vias that connect the wire segments of different metals together.  
![Horizontal and Vertical Routing](https://github.com/user-attachments/assets/2d225e00-70c0-4357-ab5a-03539b7d7559)

Routing Grids are huge. So, divide and conquer technique is used for the Routing.
1. Global Routing: It generates routing guides.
2. Detailed Routing: It uses the routing guides to implement the actual wiring.

![Routing](https://github.com/user-attachments/assets/c3f80e83-7895-4670-9e77-0c5e3a40276f)

##### Skywater PDK
The Skywater PDK contains 6 layers.  
The bottom-most layer is made of Titanium Nitride. This is also known as the local interconnect layer.
The remaining 5 layers are made of Aluminium.  
![Skywater PDK Technology Stack up](https://github.com/user-attachments/assets/01ac763f-c5d5-428a-a4a2-0654e7d2e7a2)

#### Sign-off
Before the final layout is sent to the Foundry, a number of verifications have to be done on it. This includes Physical verification, Timing Verification, EM-IR Analysis, etc. This step is known as **Sign-off**.
1. **Physical Verification**
   1. DRC - Design Rule Checking - It ensures that all the design rules defined in the PDK by the foundry are met.
   2. LVS - Layout vs. Schematic - It ensures that the final layout is physically same as the original gate-level netlist that we started with.
2. **Timing Verification**
   1. STA - Static Timing Analysis - It ensures that all the setup and hold constraints are met.

### Introduction to OpenLane
**OpenLane** is an open source tool that enables the users to experience the RTL to GDS flow with open source PDKs, Open EDA tools and Open RTL.  
StiVe is a fmily of SoC at OpenLane that uses all open source elements.
OpenLane is tuned for Skywater 130nm Open Source PDK. It can be used to harden chips and macros. It supports an automated push-button flow and an interactive flow.
It also supports design space exploration to find the best configuration for your design.

#### OpenLane Detailed RTL2GDS Workflow
The detailed RTL2GDS workflow in OpenLane is shown below. OpenLane uses a number of open source tools like Yosys for Synthesis, OpenSTA for timing, etc.  

![OpenLane Detailed RTL2GDS Flow](https://github.com/user-attachments/assets/6cdf572f-ee85-43a5-8af5-eb4d1cd7a99e)

**Synthesis Exploration** generates a graph of Delay vs. Area for various synthesised netlists and helps the designer to pick the best suitable netlist.  
![Synthesis Exploration](https://github.com/user-attachments/assets/8aa66ec5-e15a-4bf5-aef2-b428c34e51c4)

**Design Exploration** shows different design metrics like runtime, cell count, Core Utilization percetage, Violations, Global routing adjustment factor, etc. that help the designer to take an informed decision about the design configuration.
![Design Exploration](https://github.com/user-attachments/assets/ba9b476c-7517-4eae-aca6-deef41374f3a)

**Design For Testing** (DFT) is an optional step that can be performed in OpenLane. It basically inserts an logic circuitry into the design that is useful for testing.
![DFT](https://github.com/user-attachments/assets/03044f62-ebd2-42b5-8fdf-881cad857551)

The **Physical Implementation or Placement and Routing (PnR)** is done by the OpenRoad App. It does several steps such as Floor Planning, Power Planning, Decaps and Tap cells insertion, Placement, CTS and Signal Routing.
![PnR](https://github.com/user-attachments/assets/5b0197fd-c6f9-4bfe-895b-adc4c78a1e93)

**Logic Equivalance Checking (LEC)** needs to be done in order to make sure that the original netlist is same as the detailed netlist generated after PnR. This is done using Yosys.
![LEC](https://github.com/user-attachments/assets/35909834-7496-4d48-9cc4-7dd9db6ae10a)

**Antenna Effect** - A routed metal segment can act as an antenna as reactive ion etching causes the charges to accumulate on the wire. Due to this the transistor gates can be damaged during etching.  
![Antenna Effect](https://github.com/user-attachments/assets/8da0b064-ba2b-4a1b-86dd-994373ae39d1)

There are 2 solutions to handle the antenna effect.
1. Bridging - Route the signal by using an immediately higher level metal for a small portion as a bridge between the same metal routes.
2. Add Antenna Diodes to leak away the excess charges.  
![Dealing with Antenna Rule Violations](https://github.com/user-attachments/assets/39375a16-0ab5-42f4-9150-fc64c4ad52a3)

In this workshop, we have taken a preventive approach to add a fake antenna diode next to every cell input after placement. Then ANtenna Checker (in Magic tool) is run on the routed layout. If the violations are reported on a cell input, then the fake diodes are replaced with real ones.
![Antenna Diodes insertion](https://github.com/user-attachments/assets/0ce89c21-3653-4ea3-9fea-b8cb7bee4a75)

**Magic and Netgen** are used for Physical verification.  
Magic is used for DRC and Spice extraction from the Layout.
Netgen and Magic are used for LVS verification.

## Implementation
### Intoduction to OpenLane Lab Environment
Please install Oracle VM Virtual Box Manager and launch a new Virtual Machine using the downloaded Openlane VDI.
Once you are inside the VM, open a Terminal.  
The Openlane working directory is located at `/home/vsduser/Desktop/work/tools/openlane_working_dir/`  
![OpenLane Working Directory](https://github.com/user-attachments/assets/9b6aa392-ffdf-442f-ac01-2e2d60fa9143)

### Getting Familiar with OpenLane Directory structure and Input Design Data
The OpenLane Working Directory has the `pdks` folder. We use the **sky130A** PDK in this workshop.

| Directory | Contents |
|-----------|----------|
| **skywater-pdk** | This has the input files like timing files, lib files, lef files, etc. |
| **open_pdks** | This has the scripts that convert the Foundary PDKs to be compatible with the Open Source Tools like Magic, Netgen |
| **sky130A** | This is the final Open SOurce tools compatiable PDK that we will be using. |

![PDKs](https://github.com/user-attachments/assets/e1f7146c-8e7e-49ef-a0d4-59e11174f264)

Going into the `sky130A` directory, we find the `lib.ref` and `lib.tech` folders.  
libs.ref has the PDK files specific to the various processes.  
libs.tech has the PDK files specific to the different tools.

![PDK structure](https://github.com/user-attachments/assets/5228348f-5247-4850-9537-6704d413cd55)

We will be using the process - **sky130_fd_sc_hd** in our workflow.  
**sky130** refers to the skywater 130nm technology.  
**fd** refers to the name of the foundry.  
**sc** means that it is a standard cell library.  
**hd** refers to the variant of the PDK.  

Inside the sky130_fx_sc_hd folder, there are the design data folders such as verilog, lef, tech, libs, etc.
![design data](https://github.com/user-attachments/assets/262a0799-ee4e-468c-8e4d-4d671c1521a4)

Within the libs folder, the timing libs for PVT corners are present.  
Process - Slow(ss), Fast(ff), Typical(tt)  
Temperature - -40, 25, 100 degrees  
Voltage - 1.4V, 1.8V, etc.  

![Libs](https://github.com/user-attachments/assets/933fe69e-f2f9-4fd6-b8b3-379e2a6ef4ee)

Within the lef folder, the standard cell lef file is present.  
Within the techlef folder, the technology lef file is present. This defines the width and spacing of the metals.  
The Verirog folder contains the RTL Netlist of the design.  
The spice and cdl folders contain the spice and cdl netlists of the standard cells.  
![Input folders](https://github.com/user-attachments/assets/0cacbb5f-7640-497e-a103-cba2d9f54f05)

Now coming back to the OpenLane Working Directory, let us take a look into the OPenLane Directory that is paralell to the pdks directory.  
![OpenLane WD](https://github.com/user-attachments/assets/6e454f7f-b569-4bf2-b707-5342e4770588)

The `designs` folder has multiple designs that can be run using openlane.  
We will be designing _picorv32a_.  
Under the src folder, we have the SDC file and the Verilog file.
![picorv32a](https://github.com/user-attachments/assets/46d8079d-33fb-49d8-a938-53d015808113)  

SDC File:  
![SDC](https://github.com/user-attachments/assets/6cd5d3fd-69ee-408c-b732-777f3dfb2f8b)

Verilog File:  
![Verilog](https://github.com/user-attachments/assets/4e2dbb49-af6e-4a7a-a03e-a3e3a476f7ca)

The config.tcl contains the settings that override any other settings done in the openlane env.  
![config.tcl](https://github.com/user-attachments/assets/cdb073fd-23ba-4da4-b83c-b1d715279af8)

There is another design specific configuration file - _sky130A_sky130_fd_sc_hd_config.tcl_ that occupies the highest priority.  
![sky130A_sky130_fd_sc_hd_config.tcl](https://github.com/user-attachments/assets/c6107189-c796-474f-afa3-a439662acff4)

Order of Priority: `OpenLane settings < config.tcl < sky130A_sky130_fd_sc_hd_config.tcl`  

The OpenLane Workflow is run within the docker environment. Use the command `docker` wihtin the _openlane_ directory to load the required environment for running the OpenLane flow.
Once you execute the `docker` command, it provides a bash prompt. All the commands for the OpenLane flow are executed in this shell.  
Now, invoke the OpenLane tool using the flow.tcl script in interactive mode.  

Commands to invoke OpenLane:  
```
docker  
./flow.tcl -interactive  
package reqire openlane 0.9
```
![Invoke OpenLane](https://github.com/user-attachments/assets/0e4e9185-2a25-4a65-b550-50f7df3ce8f3)

### Prepare Deisgn
**Prepare Design** is the first step in running the OpenLane flow. In order to run the various steps of the flow a directory from where are relavant files can be fetched must be created. Prepare Design step does exactly the same.
It merges the standard cell lef and the technology lef files.  
Command to run prepare design:  
`prep -design picorv32a`

![prep design execution](https://github.com/user-attachments/assets/a779f9a1-0605-4c8e-930b-a6ac8a215b6b)

After the Prepare Design step, we can see that a runs directory has been created with a directory with today's date.  
This contains the files generated from the prep design step.
![prep design results](https://github.com/user-attachments/assets/5e7f4f55-1a85-4983-93cb-f4dcb06c74a7)

Within the tmp folder, you will have the merged.lef file.
![tmp folder](https://github.com/user-attachments/assets/ffec5caf-b0fc-447c-b53c-3669531c07c5)

Merged Lef File:
![Merged LEF](https://github.com/user-attachments/assets/a4bee505-3fb9-4834-a7f6-10ad29cb58f3)

The remaining folders results and reports have directories for each stage - synthesis, floorplan etc that are currently empty as we are yet to run those steps.
The logs folder contains the log file for each step.  

The config.tcl present in the run directory contains all the settings that have been taken while running the command.  
![Config.tcl](https://github.com/user-attachments/assets/737a722b-4db2-463e-ac0a-908a5fd91145)

The cmds.log contains all the commands/files that are internally getting executed when each step is run in the flow.
![cmds.log](https://github.com/user-attachments/assets/ed11666f-4fdd-4c37-9984-35608a1453a1)

### Synthesis
Synthesis is the first step in RTL2GDS Flow. Synthesis is run using the Yosys tool.
The command to run Synthesis is:  
`run_synthesis`

![Synthesis](https://github.com/user-attachments/assets/0148003f-1a80-4f17-b7c0-dc830d0aa85b)

You can find the Verilog netlist generated after synthesis under `results/synthesis`.  
![Synthesised Netlist](https://github.com/user-attachments/assets/7a0683b7-4a5b-4d99-8577-3f07754c8acf)

Timing Report from OpenSTA:
![Timing Report](https://github.com/user-attachments/assets/bf22b391-3706-4afa-b642-e34e707e7b79)

#### Calculating Flop Percentage in the Design
The Yosys Stats report has the required data for calculating the Flop ratio of the design.  
![Flop Ratio](https://github.com/user-attachments/assets/f2f62e94-bcd9-4605-b334-c56dfb5033b0)

> **Percentage of DFF = (1613/14876) x 100 = 10.84%**

### Floor Planning
#### Define Width and Height of Core and Die
The Area of a Die is dependent on the Standard cells used in its Netlist.  
Consider the simple netlist shown below:
![Eg. Circuit](https://github.com/user-attachments/assets/0909a1f4-d195-4a44-82a7-a52aa8560b5a)

Converting all the cells into physical dimensions of a standard cell:
![Ckt elements to std cells](https://github.com/user-attachments/assets/381cf9ac-1b5c-4668-9ae4-0ce368978f40)

Defining the area of the Standard cells and Flipflops as 1 sq. unit:
![Cell area](https://github.com/user-attachments/assets/e504367c-cd14-4f83-81ea-fff0807aa78b)

Therefore rough/minimum area required by the die is 4 sq. units.
![Die Area](https://github.com/user-attachments/assets/817a51c4-807a-45bf-a7f0-bf0bb6a4fc90)

Core is the area within a die where the fundamental logic is placed. A die encapsulates the core.
A Die is repeated multiple times on a Silicon Wafer to increse the throughput.
![Core and Die on Si Wafer](https://github.com/user-attachments/assets/b1b20830-2404-40b8-a868-98b1c38c719f)

**Core Utilization Percentage **is defined as the ratio of the area occupied by the netlist to the area of the core.
![Core utilization factor](https://github.com/user-attachments/assets/a6bd8499-6420-4d98-8c1c-eab906a7e9c8)

Placing the Logic circuit within the core as shown gives 100% Core Utilization, i.e. the utilization factor is 1.
![eg.1](https://github.com/user-attachments/assets/a5a85d8a-d070-4a3a-bc27-51f940a6885b)

If the core is entirely utilized by the netlist, there is no provision to add the routes or some extra cells if required. Therefore, practically only 50-60% of the core is utilized.  

**Aspect Ratio** is defined as the height of the core divided by the width of the core.  
![Aspect ratio](https://github.com/user-attachments/assets/d8d022a0-1630-4012-b332-64a3037a4f93)

In this example, the Core Utilization factor is 0.5 and the Aspect ratio is 0.5.
![e.g.2](https://github.com/user-attachments/assets/9020b248-cf2b-4b4d-988b-ef5d8db3b021)

#### Define the Location of Pre-placed Cells
If some part of the circuit or the combinational logic like a Clock Divider or a Complex Mux is occuring in multiple parts of the netlist, then we need not implement the logic everytime.
These blocks can be implemented once and repeatedly used in any part of the design.  
Consider the following combinational circuit.
![Comb circuit](https://github.com/user-attachments/assets/62ad6318-d2bb-4e24-bb8d-f05ae3f47648)

Now, let us divide it into 2 parts and put it inot 2 blocks.  
![cut1 and cut2](https://github.com/user-attachments/assets/9a362e5e-ecf5-45d6-b4ee-b7d929934342)

THese 2 blocks will be implemented seperately. Let us extend the IO pins of the blocks and black box them  
![Block1 and Block2](https://github.com/user-attachments/assets/a4a67c38-ff0e-4c74-9e23-6327a0bb5776)

These 2 blocks can now be implemented by different users and the top level designer need not implement it. They can simply connect the 2 modules wherever necessary and realise the function.
![Blocks IPs](https://github.com/user-attachments/assets/8e57ec7d-7b8c-40cb-b094-99e349ea76a0)

There are other IPs readily available in the market that can be used in our designs like memories, Clock Dividers, Clock Gating cells, Mux, etc.
They can be instatiated mulitple times in a netlist.  

Floor Planning means the placement of these Modules in the chip. The remaining logic is placed during PnR by the PnR tools.
The IPs are not touched during PnR, Hence these are called as Pre-placed Cells.  
The location of the pre-placed cells has to be planned very well so that they are close to the required I/O ports. Once placed, their locations cannot be altered in the entire design cycle.  

![Pre-placed cells](https://github.com/user-attachments/assets/ff8448bf-4487-4943-96c2-5ccfe7e69503)

#### Placing de-coupling capacitors
In physical connections within a die, there is bound to be voltage drop due to the resistance and inductance of the connecting metal segments. Due to this the combinational circuit will not receive the actual supply voltage. If this voltage is not sufficient to charge the capacitor in the combinational logic to 1 or discharge it to 0 safely within the Noise Margins, then the output of the combinational logic will be in unstable state.  
![Practical combinational logic](https://github.com/user-attachments/assets/d4259b42-2987-47a7-b355-2ff55728ef12)
![Logic levels](https://github.com/user-attachments/assets/5dca416b-bebc-4b2f-9b3a-eebed60ab47e)
![Noise Margin](https://github.com/user-attachments/assets/b9a00c8b-0161-4ead-86e3-3aa953a82fd6)

In order to solve this problem, de-coupling capacitors can be added in paralell to the combinational circuit.  
Whenever there is a switching in the combinational circuit, it draws current from the de-coupling capacitor.  
The RL network is used to replenish the charge into Cd (Coupling Capacitor).
Since Cd is placed close to the Combinational block, there will be hardly any voltage drop from Cd to combinational logic.  
![De-coupling caps](https://github.com/user-attachments/assets/eb2e8eb2-9ece-4c89-bdf0-469c2870be3b)

In the chip, all the pre-placed blocks should be surrounded with decaps.  
![De-caps](https://github.com/user-attachments/assets/2a221c89-3c8a-474e-9272-c4791ac96978)

#### Power Planning
Consider a scenario where the an IP1 is driving IP2. The logic signal from IP1 to IP2 should hold the shape in order to be interpreted properly at the input of IP2.
If we have a singal source of Power supply, it will be very hard to drive this signal line. And it is not practical to put de-caps all over the design.  

![IP1-IP2](https://github.com/user-attachments/assets/b5b7d424-e8aa-41a5-a81f-d6f2752420be)

Assume that the signal line is a 16 bit bus. If this 16-bit bus is connected to an inverter, then all the bits have to toggle at the same time.
![inverter 16 bit bus](https://github.com/user-attachments/assets/18fed88e-bddf-4fe6-b912-677a1a96328e)


If all the bits on a 16-bit bus get discharged at the same time, it can lead to ground bounce as there is a signgle line to discharge.  
![gnd bounce](https://github.com/user-attachments/assets/50d54a51-4b87-4610-aa75-772a5b164be8)

Similarly, when all the bits are charged to 1, it leads to voltage drop as there is a single supply.
![vtg drop](https://github.com/user-attachments/assets/a132861d-494b-4746-88fe-f160a081d452)

The solution to this problem is to create multiple power supplies on the chip. We will have Vdd and Vss lines as a grid to supply power to the chip.
Each cell will tap the power supply from its nearest Vdd and Vss rails.
![power grid](https://github.com/user-attachments/assets/e0381fd9-6195-4e09-8496-9bfa4b2cb196)

This is how the power grid planned chip looks like.
![Power Grid](https://github.com/user-attachments/assets/e5642ef7-072b-475a-932a-36e6b49f0464)

#### Pin Placement
Consider the below circuit that needs to be implemented. It has the 2 combinational logic blocks and 2 pre-placed cells.
The circuit has 6 input ports and 5 output ports.  
![e.g.1](https://github.com/user-attachments/assets/f80beed8-bae2-4a56-bc0a-4db2250b0db1)

After tapping the common ports:
![after tapping](https://github.com/user-attachments/assets/baf67f84-a815-4d2e-89ec-9f00860876dd)

The common practice in pin placement is to have input ports at the left side of the chip and output ports at the right side of the chip.
We will place the I/O pads in the area between the core and the die. Here the order of the pins may not be chronological. The pins will be placed near to the blocks with which they are directly connected to.
![Pin placement](https://github.com/user-attachments/assets/56cf8228-83ab-402f-bfa4-122a46a8c389)

The clock ports are bigger in size compared to the signal ports. This is because the clocks drive the cells continuosly and so they require the least resistance paths.  

Once the Pin placement is done, some bloackages should be defined over the region so that the PnR tools do not place any logical cells over the region. This is known as **Logical Cell Placement Blockage**.
![Logical Cell Placement Blockage](https://github.com/user-attachments/assets/6570c5cf-d70c-482b-974f-3a03084bc2f6)

#### Running Floorplan in OpenLane
The command to execute floorplan is:  
`run_floorplan`

There are some switches to control the floorplan. The Readme file in the below configuration folder contains the description of the switches that can be used in every step of the flow.
![FP Switches](https://github.com/user-attachments/assets/935dc06c-65f3-4072-8cd6-5d7ad35b70c9)

These switches are set in the floorplan.tcl. These the default values in OpenLane.  
![FP config](https://github.com/user-attachments/assets/ac7a618e-0101-4805-b0c7-820d4ae61314)

Again as stated in synthesis stage, the order of priority is as follows - _design_name_.tcl > config.tcl > floorplan.tcl

Floorplan is executed successfully.  
![run_floorplan](https://github.com/user-attachments/assets/0e498961-c479-438c-932e-01b1528b0458)

Let us review the logs and the results from the floorplan step.  
The Die area and Core area are present in the Verilog2DEF log generated from OpenROAD.
![OpenROAD Log](https://github.com/user-attachments/assets/ae689ed4-09cc-46bd-b502-046c7a608d63)

In the floorplan.tcl, the FP_CORE_UTIL is set as 50.  
![fp.tcl](https://github.com/user-attachments/assets/a2ffd2d6-2bc7-4f1d-8e17-04f42d396370)

But in the design_config file, it is defined as 35.  
![design config](https://github.com/user-attachments/assets/f3318434-ec3d-4231-bb6f-42652956b8a2)

In the config file under the run directory, we can verify that the tool used FP_CORE_UTIL as 35 according to the order of precedence.
![final config.tcl](https://github.com/user-attachments/assets/882c7b1d-e249-438c-b0d0-32b57093a152)

##### Calculating Area of the Die
The Area of the Die can be calculated from the DEF file.
![DEF File](https://github.com/user-attachments/assets/ccc7f2f4-a73b-46cc-9613-1cb3b1a69eee)

```
1000 database units = 1 Micron  
Width of the Die = (660685-0) = 660685 DB units = 660.685 microns  
Heigth of the Die = (671405-0) = 671405 DB units = 671.405 microns   
Area of the Die = 660.685 X 671.405 = 4,43,587.212425 sq. microns
```

##### Reviewing the DEF Floorplan in Magic
Command to open the DEF File in Magic:
```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/17-03_12-06/results/floorplan/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

![DEF FP in Magic](https://github.com/user-attachments/assets/0a33242e-8acc-43d7-b0ab-a61b559a553d)

Viewing the Layer on which the horizontal pins are placed in the Floorplan. They are placed on Metal 3. This is one less than the FP_HMETAL defined in the config.tcl. (As expected)
![H Pin placement](https://github.com/user-attachments/assets/c3495d1c-7009-4c98-a88e-972e114ab424)

Similarly, the vertical pins are placed on the Metal 2.
![V Pin Placement](https://github.com/user-attachments/assets/405ce1a0-1322-4d58-967b-6aa312a136d0)

End Cap cells are placed along the boundary of the core. And Tap cells are placed to prevemt latch up of n-wells.  
![End Cap cells and Tap cells](https://github.com/user-attachments/assets/d62dcd4e-68a4-47a3-8b34-30d75c485e9b)

The Standard cells are kep unplaced at the origin at this stage.

#### Re-running floorplan by making changes on the fly
OpenLane supports making changes to the variables on the fly and re-running the steps.  
For e.g., we can change the value of the FP_IO_MODE and re-run the floorplan.
In the current floorplan, the FP_IO_MODE was set to 1. Therefore the IO pins were arranged in a equidistant fashion and on all 4 edges of the chip.
Snapshot of current floorplan with equidistant IO pins.  
![FP mode 1](https://github.com/user-attachments/assets/b48bc371-2c4c-49ed-9c0b-58b2305b13bb)

Snapshot of current floorplan showing IO pins on all 4 sides.  
![FP Mode 1 img 2](https://github.com/user-attachments/assets/1e58c120-888b-46ac-af5a-bcfbfa64788d)

In the next run, we will chage its value to 0 and see the impact on the floorplan.  
Commands to be entered in the Openlane window:  
```
set ::env(FP_IO_MODE) "0"
run_floorplan
```

Running Floorplan again with modified FP_IO_MODE setting:  
![settings](https://github.com/user-attachments/assets/f2e08e47-30d5-4542-8020-6ee9055e9f2a)

Floorplan with FP_IO_MODE 0. The IO pins are not equidistant and are not placed on all 4 sides.  
![FP IO MODE 0](https://github.com/user-attachments/assets/5da93acd-dcb8-40b1-9af8-cf7a900241a7)

### Placement
#### Binding Netlist with Physical cells
In this process, the gates of the netlist such as AND gates, OR Gates, Flipflops etc, are given a physical representation and dimension. All these Gates and their dimensions are present in the library.
The library also has the timing information of the cells like gate delays, FF setup and hold time, etc.
It will also have the **When Conditions** of a cell.  
A library has different variants of a gate, for eg. it can have Buffers of 1x Area, 2x Area and 4x Area. These have the same functionality, but the 4X Buffer will have more drive strength and it will be faster.  
The designer can pick up the required variant based on the timing constraints and area constraints of the design.
Different Variants of cells in a library:  
![Lib cells](https://github.com/user-attachments/assets/fa6200cb-6128-485d-9610-65e2bea4318a)

#### Placement of Standard Cells
The next step is to place the physical cells from the library onto the Floorplan.  
In the Floorplan we have a well defined area of pre-placed cells or Macros and defined I/O ports.
The Standard cells cannot be placed over the Macros.  
Now based on the proximity required to the I/O ports, and the cells logical connectivity, we come up with a location for each of the cells in the netlist.
The Standard cells can be abutted with each other to minimize the delay. If some parts of the logic have to operate at very high speed, then they are usually abutted to reduce the delay to the maximum extent.  
We place the input flops as close as possible to the i/p ports and Output Flops as close as possible to the o/p Ports.  
With this we come up with a rough placement of the Standard cells.  
![Placed Cells](https://github.com/user-attachments/assets/63770605-c23b-4894-b859-3cefb97351d9)

#### Optimize Placement
In this stage, we estimate the wire length required to connect the logically connected netlist cells and also the capacitance. In order to maintain the signal integrity, we introduce repeaters. These are buffers that will take the original signal and re-condition them, replicate them and transmit it to the receiver.
In our e.g., the distance between Din2 and Yellow FF1 is quite large. Based on the wire length and capacitance calculations, we observe that the slew is beyond the acceptable limit. Hence we need buffers on the path from Din2 to FF1.  
![Insertion of Buffers](https://github.com/user-attachments/assets/2d10e00c-d3cd-4e3f-8b83-25e5addf2360)

After all the 4 groups of Combinational Logic cells have been placed.
![Placement](https://github.com/user-attachments/assets/7bfbb554-70ba-403c-9634-13d9f07d9a7b)

At this stage, we have to do a timing analysis with ideal flops to make sure that the timing constraints are being met till this point.

#### Running placement in OpenLane
Let us do a Congestion Aware Placement in OpenLane.  
The command to execute Placement is:  
`run_placement`

This takes place in 2 stages, Global Placement and Detailed Placement. The tool runs a lot of iterations to achieve an optimum placement.  
main objective of Placement is to reduce the wire length.
OpenLane uses a concept called HPWL (Half Parameter Wire Length) that will be minimized.  
![After run_placement command](https://github.com/user-attachments/assets/22bf2020-d957-4ff3-aad6-dbd13754f98f)

#### Reviewing the DEF after Placement in Magic
Command to open the Placement DEF in Magic:  
```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/17-03_12-06/results/placement/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```
DEF after Standard Cells placement is completed:  
![DEF with Standard cells](https://github.com/user-attachments/assets/c8e47d05-166b-4a54-be31-6b24a8d58b9b)

Standard Cells placed within the Standard cell rows:  
![Std cells](https://github.com/user-attachments/assets/39e6900e-a596-459c-a07e-12b6cfe7147f)

> [!NOTE]
> In Openlane flow, the PDN is not implemented during the Floorplan, it is done after Placement, CTS and Routing.

### Cell Design and Library Characterization Flows
#### Inputs for Cell Design Flow
A collection of cells is known as library. The cells have to be designed, charcterized and modelled in such a way that the tools understand the cells. Hence the characterization and modelling of the Library is very important.  
A Libaray has cells with different functionality, different sizes (drive strengths) and different threshold voltage(Vt). The threshold voltage decides how fast a cell can operate or switch.
![Cells of a Library](https://github.com/user-attachments/assets/f20e5236-31e1-456e-a5bb-51121c77b8e6)

Now, let us consider an inverter from this library. This inverter has to undergo a cell design flow to be represented in form of its physical shape, timing characteristics, drive strength, etc, to be used by the tools.  

The input for the cell design flow comes from the Foundry. It consists of the Process Design Kit(PDK), DRC and LVS rules, Spice models, library and user-defined specifications.  
Example of DRC Rules. These rules have to be followed in order that the Foundry is able to manufacture your chip.
![DRC rules](https://github.com/user-attachments/assets/e67e5d36-1b20-452f-9556-d84c7a37c3be)

The Spice models for the Pmos and Nmos come from the Foundry.
![Spice Models](https://github.com/user-attachments/assets/3d02faad-ebb1-4b78-9964-3eee7ce6ff0f)

Some of the user-defined specifications include the cell height and drive strength. The cell height is determined by the distance between the Power rail and ground rail. The designer specifies the drive strengths required by him for the design and provides it to the library developer. Drive strength is decided by the width of the cell.  
Another user-defined specification is Supply voltage. The library designer has to take care of the noise margins based on this Supply Voltage.  
Pin locations, drawn gate length and metal layers are also user-defined.

#### Design steps in Cell design Flow
It has three steps.  
1. **Circuit Design**: It includes implementing the function with the help of Nmos and Pmos transistors and model the Pmos and Nmos transistors. to meet the requirements such as W/L ratios. **CDL (Circuit Description Language)** is the output of Circuit design Step.
2. **Layout Design**: Once we know the W/L of the Nmos and Pmos, we have to implement it on a layout. To this we construct an Euler's Graph and Stick Diagram for Pmos network adn Nmos network. This is then converted to the layout based on actual DRC rules and user-defined specifications such as pin locations, cell height, etc. The output of this stage is standard cell LEFs, and GDSII. And also the parasitic extracted spice netlist (.cir)
3. **Characterization**: In this step, we extract the parasitics from the layout and characterize the cell in terms of timing. The Output of characterization is timing, noise and power libs and the functionality of the cell.

Circuit Design:  
![Circuit Design](https://github.com/user-attachments/assets/244c3bf9-7b72-4ad1-8df9-09e5115065c9)

Stick Diagram for the above circuit:  
![Stick Diagram](https://github.com/user-attachments/assets/fa73d0ab-32b4-4efe-847f-cbfbadb7d89a)

Layout of the Circuit:  
![Hand drawn layout](https://github.com/user-attachments/assets/b301d84b-21fe-48a3-86ac-3579f5f3eb90)

Layout in Magic:  
![Layout - Magic](https://github.com/user-attachments/assets/7a9f15b1-4319-4cea-b477-2bd9b62e4a30)

#### Characterization Flow in detail
Consider a Buffer. It is implemented as 2 inverters. At this point, we have the circuit description of a buffer. We have the spice extracted netlist, the layout, the spice models of the Pmos and Nmos.  
Steps in characterization flow:
1. Read the Nmos and Pmos model files.
2. Read the extracted spice netlist.
3. Define/recognize the behaviour of the buffer.
4. Read the subcircuit of the inverter that has been instatiated in the buffer.
5. Attach the mecessary power sources to the inverters.
6. Apply the stimulus to the buffer.
7. Provide the output capacitor.
8. Provide the necessary analysis command in the spice netlist.

All this information is fed into a software called GUNA. It generates the timing, noise and power libs and the function model.

Realization of buffer with 2 inverters:
![buffer](https://github.com/user-attachments/assets/eb4d7709-fe59-40b1-8dda-02e8ec6bf405)

##### General Timing Characterization Parameters
Consider the previously used realization of buffer with 2 inverters.  
There are certain timing threshold definitions for a waveform.  
**Timing threshold definitions:**
1. Slew_low_rise_thr - It the lower reference point taken for measuring the slew on a rising w/f. It is typically 20% of the power supply.
2. Slew_high_rise_thr - It is the higer reference point for measuring the slew on a rising w/f. Again, it is typically 80% of the power supply.
   Slew is the time taken by the signal to rise from slew_low_rise_thr to slew_high_rise_thr.
3. Slew_low_fall_thr and Slew_high_fall_thr - These apply for a falling w/f. They are similar to 1 and 2.
4. in_rise_thr - This is the refernece point on the rising input signal to calculate the delay. It is typically 50% point.
5. out_rise_thr - This is the reference point on the rising output signal to calculate delay. Again typically 50%.
   Rise Delay between the i/p and o/p of the cell is calculated for a buffer as time difference between out_rise_thr and in_rise_thr.
6. Similarly, we have in_fall_thr and out_fall_thr for falling waveforms.

**Propagation Delay**  
Delay is defined as the time difference between the output_threshold and input_threshold.  
Consider in this below e.g. 50% as the threshold:  
![Prop Delay 1](https://github.com/user-attachments/assets/cf47ff59-eb0b-4185-8260-509558698a59)
The delay in this case is 23ps.  

Consider another example where the threshold is above 50% as shown. This is leadng to a negative delay of -42ps. That means the output is arrived before the input. Therefore choosing the rigth threshold is very iportant.
![Prop Delay 2](https://github.com/user-attachments/assets/fd720150-dccc-4f14-bdc3-81c8521a56a7)

Another cause of negative delay can be very high input slew. This may happen when the inverters are placed very far apart. The input of the 2nd inverter might have a large slew.  
![Prop delay 3](https://github.com/user-attachments/assets/f4966c4f-53e2-4fab-acad-cc2f53d8bf4e)

Zoomed in:  
![Prop delay 3 zoom](https://github.com/user-attachments/assets/f8f12d9d-8e1e-432b-9fcf-37af8cac9894)

**Transition Time**
Transition time = SLew_high_thr - Slew_low_thr
![Slew thrs](https://github.com/user-attachments/assets/c2d20bc5-342c-49fa-aa9d-89be6b3da663)
![SLew calc](https://github.com/user-attachments/assets/2d15ccc4-6bf4-49ec-b2a7-24c669b155e4)

### Design of Library Cell using Magic Layout and ngspice Characterization
#### Spice Deck Creation for CMOS inverter
A Spice Deck is nothing but a netlist that has the Nmos and Pmos connectivity information, inputs, output tap points, etc.  
Consider the inverter circuit shown below:  
![Inverter](https://github.com/user-attachments/assets/fd29b406-c84f-4007-97e0-660431d514cd)

Observe that the substrate pin is also shown here for the NMOS and PMOS. The substrate plays a potential role is deciding the threshold of the Mos.  

Next, we have to define the W/L values of the PMOS and NMOS. Let us take same size PMOS and NMOS. 
Next, we have to come up with a value of Load Cap which is actually based on the circuit connected to the output of the inverter. Let us assume it to be 10fF.  
![Mos sizing](https://github.com/user-attachments/assets/d49d06b4-878b-48ce-9a40-f2bdc4e1f231)

We then define the Supply Volatges. Here, we set it to 2.5V.  
![Supply voltages](https://github.com/user-attachments/assets/7a6de45c-19d7-48ac-b58f-80bf2fad03fa)

The next step is to identify the nodes. Nodes are 2 points between which there is a component.  
![Nodes](https://github.com/user-attachments/assets/de742c13-038b-49ac-9e5f-06a37b8954a9)

Let us give names to identify these nodes to be used while writing the Spice Deck.  
![Node Names](https://github.com/user-attachments/assets/7925f871-83fa-4529-801f-4c018b599258)

Let us start writing the Spice Deck for the Inverter now.
Lines starting with `*` are comments.
First we define all the components of the circuit - M1 (pmos), M2 (nmos), Cload, Vdd and Vin.
Mosfet connections are defined in the order Drain (connected to out node), Gate (in), Source (vdd), Substrate (vdd).
Next, the commands to run the simulation are to be given.  
In order get the Voltage Transfer Characteristics of the Inverter, we have to do a DC sweep of the input Voltage Vin and measure the O/p Voltage at out.
Therefore, we sweep the Vin from 0 to 2.5 in steps of 50mV.  
The last step is to include the TSMC model file which has the nmos and pmos models description.  
![Spice Deck](https://github.com/user-attachments/assets/99b61c06-fdf6-42e8-bf50-79c37e0eb387)

VTC of an inverter with equal sizing of PMOS and NMOS after Spice simulation.  
![DTC equal size](https://github.com/user-attachments/assets/bce5ea28-68fa-4eb4-9759-340e461ff4ad)

Now, let us increase the Width of the PMOS to 0.9375u. Now you can see that the DTC curve is centered with respect to the range of the input voltage.  
![VTC with unequal char](https://github.com/user-attachments/assets/c3a919b4-387d-4146-874b-e8bbe6093d9a)

Let us evaluate the static behaviour of the inverter. The CMOS inverter is a robust device.
1. **Switching Threshold(Vm)** - It is the point at which Vin=Vout.  
In case 1 (left) the Vm is around 0.98V and in Case 2 (right) the Vm is around 1.2V.  
![Vm](https://github.com/user-attachments/assets/eb15c71c-729c-47c7-9e35-fb69f8b07ab6)
Around the Switching threshold, both the PMOS and NMOS will be in Saturation region and turned on.  
![Saturation](https://github.com/user-attachments/assets/1fb3a753-a072-4d45-ac88-ee9340965a06)

Let us now do a transient simulation of an inverter.  
Below is the spice deck. Here, we have given input as a Pulse with 10ps rise time and fall time.  
![Transient simulation](https://github.com/user-attachments/assets/b2f37640-57ea-4bfa-b59e-50adaf86f22d)

In order to calculate the delay, we have to take the time at which the output crosses 1.25V and the time at which i/p crosses 1.25V and subtract them.  
![Calculation of Rise Delay and Fall Delay](https://github.com/user-attachments/assets/6016a147-28a9-4823-a0fe-dd3ab1bca8ea)

Rise Delay = 148ps; Fall delay = 71ps  
![Rise delay , fall delay](https://github.com/user-attachments/assets/d5cdcbd0-9649-40d0-8b5d-19721c7bfcfc)

#### Clone Custom Inverter Standard Cell Git Repository
Commands to clone the Custom inverter from Git Repo.
```
cd Desktop/work/tools/openlane_working_dir/openlane
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
Snapshot of Git Clone command:  
![git clone](https://github.com/user-attachments/assets/86244f0f-1bc3-4852-af20-a63e510a4a6d)

In order to view the mag file in Magic, we copy the Tech file and then use the below command to open it.  
```
cd vsdstdcelldesign
cp ../../pdks/sky130A/libs.tech/magic/sky130A.tech .
ls -lrt
magic -T sky130A.tech sky130_inv.mag &
```
![magic file command](https://github.com/user-attachments/assets/2a9bb09d-e56a-4f4a-bc84-52d89582331b)

Inverter Layout in Magic:  
![Inverter Layout in Magic](https://github.com/user-attachments/assets/c31110ba-750b-46dc-98bb-491d4349076a)

#### Inception of Substrate and CMOS Fabrication Process - 16 Mask CMOS Process
##### Creating Active Regions
Steps:
1. Selecting a Substrate - the most commonly used is the P-type Silicon Substrate. It has high resistivity (5-50Ohms). It has a doping level of 10^15/cubic cm (which should be less than the doping level of the well).
   ![P-substrate](https://github.com/user-attachments/assets/237459bb-be07-4d09-b851-99a61cac7274)
2. Create Active regions of transistors - these are where PMOS and NMOS transistors are created.
   The substarte is first deposited with a highly resistive layer of Silicon Oxide. Then with a layer of Silicon Nitride. This is covered by 1um of Photoresist.
   Then the location where the wells have to be formed are identified and masked are placed over the photoresist. Masks protects the layers beneth it from photo lithograhy process (exposed to UV light)
   Regions that are exposed to UV light, undergo chemical reactions and the Photoresist layer is washed away.
   ![photolithogrhy](https://github.com/user-attachments/assets/3e362dc7-9efb-4d44-8517-46891695abb0)

   Photoresist acts as a protective material for layers under it. After the Mask is removed, Silicon Nitride are removed (etched off) from the unmasked regions.
   ![Si Nitride etching](https://github.com/user-attachments/assets/a3c870d3-156d-4a60-9a0b-97131e6b1b0f)

   In the next stage, the photo resist is itself removed because the Silicon Nitride will itself act as a good protection to grow the oxide in other regions.
   ![Photoresist removed](https://github.com/user-attachments/assets/60a378f8-198f-452e-8060-b62da7b40c8d)

   Next it is placed in an Oxidation Furnace which helps the growth of oxide wells. These wells are isolated.
   ![LOCOS](https://github.com/user-attachments/assets/f655248d-c3c3-451b-91fa-eeb52cf6ab3e)

   Then the SiN3 is stripped using phosphoric acid.
   ![sin3 stripping](https://github.com/user-attachments/assets/0e6af7c2-a3ad-4c3f-97a3-a756a6900c3d)

##### Formation of N-well and P-well
N-well is used for Pmos formation and P-well is used for Nmos formation.  
P-well formation - Boron is a p-type material and it is used for creating the P-well using ion implantation method. A Mask2 is used for covering the region where N well is to be created.  
![pwell](https://github.com/user-attachments/assets/6166a9a7-ed5e-4320-bbb4-2e17ed06abd8)

N-well formation - Phosphorous is a p-type material and is used for formation of N-well. This uses Mask3.  
![nwell](https://github.com/user-attachments/assets/cc341d7c-b37a-4053-899a-f4735d08361a)

We have to now diffuse the wells to create deeper wells to allow enough space for the pmos and nmos to be fabricated. The process is known as Twin tub process and is done ina high temperature furnace.
![diffusion](https://github.com/user-attachments/assets/f3933247-5692-4c13-b399-25d162580553)

##### Formation of Gate Terminal of the Transistor
The Doping concentration (NA) and the oxide capacitance (Cox) are 2 important parameters for Gate formation as they control the threhold Voltage (Vt).
![Vt](https://github.com/user-attachments/assets/bb8ae046-1035-4784-9d03-fdf0e6498e4f)

A low energy Boron beam is used for formation of the Gate in the P-well. As a result required Doping Concentraion is maintained. Uses Mask4.
Similarly, we use Arsenic for creating the gate in N-well. Uses Mask 5.
![gate formation](https://github.com/user-attachments/assets/4d8cea5f-9933-45cf-8cf4-1c49cdda723e)

Because of the ion implantations, the SiO2 is damaged. So, we remove it using dilute HF acid and then another thin 10nm high quality Silicon Oxide is grown.  
Now, in order to create the gate, a thick layer of polysilicon is grown.  
![Poly silicon](https://github.com/user-attachments/assets/c5d83fc1-561c-432f-9fa0-6aff66b6ecd5)

As the gate layer is supposed to have low doping, we implant with N-type phosphorous or Arsenic ions to give low resistance gaet.  
Next Mask6 is used to etch the Polysilicon and create a Gate.  
![Gate formation](https://github.com/user-attachments/assets/5767c37e-acc4-4559-bbdc-a804ee3d5fb3)

##### Lightly Doped Drain Formation
We need a LDD because of the following reasons:
1. Hot Electron Effect - High energy carriers can break the Si-Si bond and enter into the oxide layer present above the substrate.
2. Short Channel Effect - For short channels, the drain penetrates the channel. Therefore it will be difficult to control the gate current.

For creating Nmos, we use N-type impurity, mask the N-well region using Mask7 and the ion implant the N- concentraion (Lightly doped).  
The energy of phosphorous is chosen so that it does not penetrate deep into the well.  
![N- drain](https://github.com/user-attachments/assets/deeddf78-3e05-4084-8260-480b2c1fa885)

Similarly using Mask8, the P- drain is created for the Pmos. Boron is used.  
![P- drain](https://github.com/user-attachments/assets/ca76d634-d5f6-4ff6-8654-a27cc467b3c3)

In order to protect from further Source and Drain formation, 0.1um Silicon Nitride or Silicon Oxide is deposited on complete structure. Then Plasma anisotopic etching is done to retain only the oxide on the side walls.  
Side walls help to keep the LDDs intact.  
![plasma etching](https://github.com/user-attachments/assets/02246ae3-4388-46ba-ae13-a5aaf0f4d0f7)

##### Source and Drain Formation
A thin layer of oxide is added to avoid chanelling.  
![oxide layer](https://github.com/user-attachments/assets/6fcd375e-a1a9-4b77-b348-f1308e65fae6)

N+ doping is done using Arsenic. Mask 9 is used.  
![N+ doping](https://github.com/user-attachments/assets/9b14250c-4df9-4375-903f-dc14ca75c91b)

P+ doping is done using Boron. Mask 10 is used.  
![P+ doping](https://github.com/user-attachments/assets/11b3928b-a9d9-4809-aa53-64caa3e9ca5c)

Annealing is done in a high temperature furnace to help grow the P+ and N+ regions deeper.  
![annealing](https://github.com/user-attachments/assets/123a0a58-951f-4a5d-acbe-083c6f0cd8b7)

##### Contact and Local Interconnect Formation
Remove the thin SiOxide using HF Acid. Now G, S and D are open for connections.  
We deposit Titanium on wafer surface using Sputtring.  
![Ti deposit](https://github.com/user-attachments/assets/c0f09b08-0659-4130-b1fc-3d88f028c31b)

Next it is heated in presence of Nirogen to form low resistance TiSi2 which is used for contacts.
![contacts](https://github.com/user-attachments/assets/71379f9f-6c2e-445a-b586-cd08f0f50095)

It also forms a layer of TiN that is used for local communication.  
![local interconnects](https://github.com/user-attachments/assets/b5ded419-f8b5-41a9-a116-cfe089df9d69)

To bring up the contacts to the top level, we use Mask11. Drains of PMOS and NMOS can be connected internally, But the sources, may be conncted to different points, so they need to be brought up.  
![Bringing up contacts](https://github.com/user-attachments/assets/56c2ada8-642c-40c8-9154-3f3e35aebc29)

##### Higher Level Metal Formation
The current surface is non planar, so we will deposit a layer of SiO2 with boron.  
![SiO2 with boron](https://github.com/user-attachments/assets/cd8e4710-6fa2-47e6-a66b-ca166742b7e6)

To create a planar surface CMP is done.  
![CMP](https://github.com/user-attachments/assets/a0fbfe79-6ebe-4caa-a00b-e770cbb280cd)

Then, contact holes are drilled using Mask12 and thin layer of Titanium Nitride is deposited and Blanket Tungstun layer is added.  
![final CMP](https://github.com/user-attachments/assets/1107c2cc-b0df-4be1-bb25-aa5db6447f6b)

To connect blanket Tungtun layer to higher metal layer, we deposit Aluminium. Mask13 is used.  
![Al layer](https://github.com/user-attachments/assets/d245494e-2bec-45e9-bd4d-df17e71cd869)

Again for another layer of interconnects the process is repeated. Mask 14, Mask 15 and 16 are used to bring contacts to higher levels.
![final inverter formation](https://github.com/user-attachments/assets/43844e92-bd84-4073-97f4-ab19c0ad0dd9)

#### Viewing the layout of an inverter in Magic
The region where Poly crosses N-diffusion layer is NMOs and the region where Poly crosses P-diffusion is PMOS.  
![NMOS](https://github.com/user-attachments/assets/0d8c0c7c-9ca9-4ff5-9714-98fcdce74e69)
![PMOS](https://github.com/user-attachments/assets/f6533d51-0f7c-4bf9-80e3-ec9982d6658d)

Drain of the PMOS and NMOS is connected to the Output Y.  
![drain connection](https://github.com/user-attachments/assets/5e048d67-6108-45bc-ba72-93b7cc7dbcd8)

Source of PMOS connected to VDD line.  
![VDD](https://github.com/user-attachments/assets/2011bdab-a2fe-49c2-978f-405e2faf505d)

Source of NMOS is connected to Ground.  
![GND](https://github.com/user-attachments/assets/edfd8109-a11e-4a75-b252-44e985f73ee8)

> [!Note]
> ALl the steps to create Layout of inverter from scratch in magic is provided in the Git Repo - https://github.com/nickson-jose/vsdstdcelldesign.git

If certain parts of the inverter are deleted then Magic shows DRC errors. I have deleted the N-well here, and it is now shoeing 7 DRC errors.
![DRC errors](https://github.com/user-attachments/assets/0736b7e5-f51c-44c5-ae91-37f4d34a85c8)

We can go to DRC -> Next DRC Error and view the error in the tkcon window.  
![review drc errors](https://github.com/user-attachments/assets/3f4437ab-d3d3-4d44-aa79-ceaf81c43ceb)

To know the logical function of this layout, we extract it and do a spice simulation.  
Commands to extact and perform spice simulation in tkcon window of Magic:  
```
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```
Commands executed in Magic Tkcon window:  
![commands for extraction of layout](https://github.com/user-attachments/assets/376555f6-910f-4974-a514-fa8f10cb2bb5)

Ext file dumped from Magic  
![sky.ext file](https://github.com/user-attachments/assets/8f0e2c91-bc09-41b4-9257-71bacb2d0e1e)

Ext file should be converted to Spice to be used in simulation. Here X0 is the NMOS. Similarly X1 is the PMOS.  
![sky.spice ](https://github.com/user-attachments/assets/85bd349a-38c0-459b-b8f9-13423331eab5)

#### Characterization of inverter using sky130 model files
We have to modify the Spice extracted layout to perform a simulation. Include the VDD and the Gnd and the input signal. And also the NMOS and PMOS Model files.   
The grid is 0.01 micron as seen from the Magic Layout. So this also has to be corrected in the Spice Deck.  
![grid](https://github.com/user-attachments/assets/22d4034f-eca2-4efb-97d3-d0e18a110d31)

Modified Spice file to run transient Ananlysis:  
![Mod Spice](https://github.com/user-attachments/assets/fbc80e17-3f39-438c-b721-90e5a69a2c84)

Commands to run ngspice:  
```
ngspice sky130_inv_sim.spice
> plot Y vs time a
```
ngspice simulation:  
![image](https://github.com/user-attachments/assets/b73fcdbd-a04e-4abe-8768-307e0a0c1ffc)

Output Transient analysis plot from Spice Simulation:  
![output](https://github.com/user-attachments/assets/57c52dc8-c054-4f6e-9719-3fc674056670)

**Calculation of Rise Transition Delay:**  
It is the time taken for the output to rise from 20% to 80% of the supply voltage.  
20% of 3.3V = 0.66V  
80% of 3.3V = 2.64V  

Time stamps for 20% and 80%:  
![Time stamps for 20% and 80%](https://github.com/user-attachments/assets/5d59e4fd-0d63-4b79-9361-f5aa6108e90c)
**Rise transition time = 2.24581ns - 2.18212ns = 63.69ps**  

**Calculation of Fall Transistion Delay:**  
It is the time taken for the output to fall from 80% to 20% of the supply Volatge.
Time stamps for 80% and 20%:  
![Fall transition](https://github.com/user-attachments/assets/64616bb2-dc14-499d-ac87-c7f7f2f7cad9)
**Fall transition time = 8.09519 - 8.05277 = 42.42ps**  

**Calculation of Rise Cell Delay/Propagation Delay:**  
Rise cell delay = Time at which output rises to 50% - Time at which input falls to 50%
50% of Supply Voltage = 50% of 3.3V = 1.65V  

Time stamps at which Output rises to 1.65V and input falls to 1.65V:  
![Rise cell delay](https://github.com/user-attachments/assets/bada7c5e-cd5e-43fe-88b8-f66f6155c1c8)
**Rise Cell Delay = 6.21104ns - 6.15ns = 61.04ps**  

**Calculation of Fall Cell Delay/Propagation Delay:**
Fall Cell Delay = Time at which output falls to 50% - Time at which input rises to 50%  

Time stamps at which output falls to 1.65V and input rises to 1.65V:  
![Fall cell delay](https://github.com/user-attachments/assets/864c482f-fd4d-43fc-ae79-02532d367ca3)
**Fall Cell Delay = 8.0777ns - 8.05ns = 27.7ps**  

#### Identifying DRC issues using Magic DRC Engine
> [!Important]
> Link to learn more about Magic - http://opencircuitdesign.com/magic/  
> Check out the DRC related tutorial  
> Refer the DRC commands section  
> Learn about Technology Files  
> CIF output format  
> Link to learn about Skywater PDK - https://skywater-pdk.readthedocs.io/en/main/index.html  
> Github link to Skywater PDK - https://github.com/google/skywater-pdk

Now, let us download a skywater magic tech file and associated files to perform some DRC tests.  
Commands:  
```
# Change to home directory
cd
# Command to download the lab files
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
# Command to view the files
ls -lrt
# Command to extract it
tar -vxfz drc_tests.tgz
# Command to view the files
ls -lrt
# Change directory into the lab folder
cd drc_tests
# List all files and directories present in the current directory
ls -lrta
# Command to view .magicrc file i.e. the startup file for Magic
gvim .magicrc
# Command to open magic tool in better graphics
magic -d XR &
```

Snapshots of above commands:  
![snap1](https://github.com/user-attachments/assets/e5a0e3d4-b238-4413-9d94-6cfc49992c15)
![snap2](https://github.com/user-attachments/assets/4d2cd8f4-32ff-453e-b750-c8fda8ee197f)
![snap3](https://github.com/user-attachments/assets/4bcaf87c-56d7-49e8-8a2a-045c66066fb8)

Magic startup file:  
![startup file](https://github.com/user-attachments/assets/9bf83e8b-aef3-4c0d-9ad9-7f3ff213b813)

Magic Window:  
![magic window](https://github.com/user-attachments/assets/e0ce5875-5ec7-4630-93b4-b25408fcc74e)

Load the mat3.mag file into Magic from `Files -> Open`. Then Click on `DRC -> DRC update`. It will refersh with all the DRC errors. 
![met3 mag file](https://github.com/user-attachments/assets/0e5edef3-ed2f-4a6d-a488-05da6b6699d0)

Each of these layouts have DRC errors that are highlighted in white and each have a DRC rule number associated with it.  
We can go to the periphery rules page on the [Skywater PDK docs page](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#m3).  
Select a layout and enter drc why in the tkcon window to know the reason for the DRC error.  
![drc error 1](https://github.com/user-attachments/assets/25b18408-928a-4480-ba08-3229519ae448)

We can check the reason for DRC error for all the cases:  
![drc errs](https://github.com/user-attachments/assets/10939910-d7c4-4d1c-9818-56a994f4e3aa)

In order to add geometries of Met3 on to the layout there are 2 options:
1. Create a rectangle with left mouse click and right mouse click. Click on Metal 3 layer with Middle Mouse Button.
2. Create a rectangle with left mouse click and right mouse click. Hover over Metal 3 layer and press p.

Layout with newly added geometries on Metal 3.  
![new m3](https://github.com/user-attachments/assets/e7b97f09-42a5-40db-b139-b1bc69314f19)

The Command cif see VIA2 helps in viewing the Via2 cuts. The command snap int helps in selectin the layout geometries exactly with the internal grid aligned.
![cif](https://github.com/user-attachments/assets/0cbbbd31-9bf0-4904-b625-fb8d51a2113f)

The command `feed clear` clears the earlier selection.  
![clear selection](https://github.com/user-attachments/assets/43ba0e0c-af29-4e99-b154-3f39cce4f1da)


The distance between 2 geometries can be measured using box command by selecting the space between the metals. As you see here, the distance between 2 segments of metal 3 is less than 0.3um, Which is why the DRC error.  
![measuring distance](https://github.com/user-attachments/assets/cd43c256-5b25-4d14-8a38-082ade3de0ba)

##### Lab 1: Identifying DRC issues in poly layer
Load the poly.mag file in Magic using tkcon commands `ls; load poly`:  
![poly load](https://github.com/user-attachments/assets/ad12fd43-5d1e-40de-b558-698da2c8ce80)

Look at poly.9 error and DRC rule:  
![Ploy 9 rule](https://github.com/user-attachments/assets/c3689813-9cc6-4cf9-8ec7-896c20f1600a)

Know the material of each segment by keeping the cursor over the segment, pressing S and typing what in the tkcon window:  
![poly types](https://github.com/user-attachments/assets/23fe0e96-12df-4d39-82c8-4f4aabff14d5)

Since the first vertical segment is a poly resistor and the horizontal segment is poly, we can say that this is related to poly.9 spacing rule.  
Now measure the distance between the 2 segments by drawing a box between them.  
![image](https://github.com/user-attachments/assets/6e3f217f-7c94-482f-9eb7-3c985d8bb8fc)

We can see that the heigth of the box between them is just 0.21u instead of 0.48u as it should be according to DRC.  

How do we fix this error?  
Let us go into the sky130A.tech file and look at the poly.9 rules.  
We see that the rule to define spacing between poly and poly resistor is missing and it needs to be added.  
It is added in the 2 places that poly.9 rule is defined.  
![poly 1](https://github.com/user-attachments/assets/cf09e9b2-e0a4-4f14-b79f-d1e0e39de67b)
![poly 2](https://github.com/user-attachments/assets/99ca156c-878f-4776-a612-bae72b8ec5fa)

Let us reload the tech file in Magic and run DRC to check if the issue is fixed. You can now see that the   
Commands to be executed in tkcon:  
```
tech load sky130A.tech
drc check
```

![DRC fix](https://github.com/user-attachments/assets/2ede8567-fc9c-4059-8c10-ab111f1b61c2)

Polyres distance to diff and tap also needs to be fixed now.  
Let us copy the 3 resistors as there are different types of diffusion and taps. Getting the related DRC errors.    
![tap diff](https://github.com/user-attachments/assets/0b59d453-b98e-440f-bd64-b28a5daa427f)

npolyres is not showing the DRC erorr w.r.t. the P Diffusion.  
To fix this, we need to add the DRC rule in the tech file.  
Changing the rule to include alldiff  
![alldiff](https://github.com/user-attachments/assets/f182fb54-d1a7-4154-9868-5791ea51e6fd)

All errors are identified after reloading the tech file and performing the DRC check:  
![drc error](https://github.com/user-attachments/assets/41bba173-50d7-4a0d-bfca-dabc90081dfc)

##### Lab 2: Describe DRC Erorr as geometrical Construct
Complex DRC rules cannot be written as text in the tech files. They need to be shown as geometrical constructs.  
This is acheieved with the help of drc style rules and cifmaxwidth rule as shown in the snapshots below:  
![drc style rule](https://github.com/user-attachments/assets/a8fdccd5-0b99-47d0-8718-bb5a3a444696)
![cifmaxwidth rule(https://github.com/user-attachments/assets/5644bc30-d08b-4fff-ab2f-97fdc757bc04)

Now, let us open the nwell.mag file and load it in Magic.  
![nwell file](https://github.com/user-attachments/assets/8e356c4d-9523-42a5-b7dd-c0ac6eb3dc44)

You can see the error highlighted using `cif see` command:  
![err highlight](https://github.com/user-attachments/assets/17c1f5f0-eee2-486b-8b22-4d53b1cb24ef)

Cif based rules slow down Magic a lot. Therefore it is recommeneded to use regular edge based rules as much as possible.  

Now, let us look athe nwell.4 DRC rule:  
![nwell.4](https://github.com/user-attachments/assets/ee3420dc-b9a5-4d45-badd-d34d208bdbf4)

In the layout, this is not highlighted as an error even though it does not contain the metal tap.  
![nwell not high](https://github.com/user-attachments/assets/1df93220-f044-4b85-b85b-b0c25384f1d6)

Therefore lets add cif commandts that run in full variant and save the Tech file.  
![nwell rule 1](https://github.com/user-attachments/assets/8e867753-8ae3-400d-9637-62d53f7d0714)
![nwell rule 2](https://github.com/user-attachments/assets/6aef2087-8efd-4dea-84dd-1893b1b8ca43)

Load the tech file and run DRC in full style. You can see the DRC error getting highlighted now.  
![highlighted](https://github.com/user-attachments/assets/718b15f8-1f30-4ae2-918e-ed53e367966e)

When we put a nsubstrate contact on the nwell, the DRC error goes away showing that it has been correctly implemented.  
![nwell contact](https://github.com/user-attachments/assets/4393a8ac-2bb6-4add-9bcb-91bd7d295fac)

### Pre-Layout Timing Analysis
#### Extracting LEF File from Inverter layout
A standard cell should follow the below guidelines:  
1. Input and Output ports of the Std. Cell must lie on the intersection of Vertical and Horizontal Tracks.
2. Width of the Std. Cell must be an odd multiple of the Horizonatal track pitch.
3. Heigth of the Std. Cell must be an even multiple of the Vertical track pitch.

Tracks are the metal lines on which the PnR tool creates the routing.  
Let us open the tracks.info file and understand about the track offset and pitch for each layer.  
Commands:  
```
cd Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd
gvim tracks.info
```
Tracks.info file:  
![tracks](https://github.com/user-attachments/assets/6cfbaff0-4b1b-4105-87c0-8eeccd1a683c)
Here, li1 layer should have an offset of 0.23 and pitch of 0.46 in the X direction. So each track of li1 will be spaced 0.46u apart in the Horizontal direction.

Let us open the inverter layout in Magic and verify the 3 conditions in the Layout.  
Commands:  
```
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag &
```
The ports are on the li1 (locali) layer.  
Let us enable the grids on the layout. Let us align the grid with the pitch of lil layer to see if the Input and Output pins are at the intersection of horizontal and vertical tracks.
Let us provide X offset value for the X origin, Y offset for Y origin, X pitch for xSpacing and Y pitch for ySpacing.  
Commands to be entered in tkcon window for creating grid that aligns with locali layer.  
```
help grid
grid 0.46um 0.34um 0.23um 0.17um
```
Layout with New Grid:  
![New grid](https://github.com/user-attachments/assets/5b045ddc-7b69-448e-add5-1508285f0798)

The Routing of the li1 layer has to happen on this grid.  Let us zoom in and check if the A and Y pins are at the intersection of Horizontal and Vertical grid lines.  
Yes, indeed they are located at the intersection. This ensures that the route can reach the pins from both the directions.  
![A and Y](https://github.com/user-attachments/assets/613331ef-4949-42b3-91d1-5f43e094c9ea)

Next, let us verify if the width of the Std. Cell is odd multiple of X pitch and the heigth of the Std. Cell is odd multiple of Y pitch.  
![inv std cell](https://github.com/user-attachments/assets/84c1758d-ae48-4510-ab37-42aed94008be)

The white box highlighted defines the boundary of the standard cell.
We can see that it covers 0.5 + 1 + 1 + 0.5 = 3 boxes on the X-axis.  
And it covers 8 boxes on the Y-axis.  
So all the 3 requirements are met.  

Then, we have to convert the labels to ports before extracting the LEF file.  
Go to `Edit -> Text` and add the following:  
![Ports](https://github.com/user-attachments/assets/cabe9627-d1e7-4856-b6a1-14dae207d032)
This has to be repeated for all the ports A , Y , VPWR, VGND.  
Since, it has already been done in the sample inverter, we can cancel this step.  

Then, we have to define the purpose of each port.  
![purpose of each port](https://github.com/user-attachments/assets/5d5d17f9-7a1d-4b32-8d40-87c0d73ff0a8)

Commands for defining the purpose of each port in tkcon:  
```
# Select port A in the Layout and enter the commands
port class input
port use signal

# Select port Y in the Layout and enter the commands
port class output
port use signal

# Select VPWR in the Layout and enter the commands
port class inout
port use power

# Select VGND in the Layout and enter the commands
port class inout
port use ground
```
After this, we can save and exit Magic in tkcon window:  
```
save sky130_vsdinv.mag
exit
```

Now, let us open the newly saved custom inverter in Magic.  
```
magic -T sky130A.tech sky130_vsdinv.mag &
```

Enter the command to write LEF in the tkcon window:  
```
lef write
```

![lef write](https://github.com/user-attachments/assets/2129c1c4-f604-4356-9e57-ffe35ee5868e)

This will create a LEF file with the name sky130_vsdinv.lef.  
![LEF File](https://github.com/user-attachments/assets/a9f8302f-83bf-42a1-b47d-fbd951436946)

Let us copy the LEF file to the src directory.  
Command:
```
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
cp Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/sky130_vsdinv.lef .
```

We have to include the Library with the custom cell characterization also in the src directory in order to be able to use in the ASIC design flow.  
This library is a part of the tar bundle we downloaded and it is present under the libs. It has the sky130_vsdinv cell in it.  
![lib with custom inverter](https://github.com/user-attachments/assets/cb10fd21-6f9c-4094-a3c1-bf93026e046d)

Copying to src directory:  
![cp cmd](https://github.com/user-attachments/assets/6b5d83c0-c490-42e4-889b-2c8843cbb435)

We have to edit the config.tcl to include the custom cell LEF and the custom cell libs.
Include below lines:
```
# For synthesis
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

# For timing analysis
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

# including new LEF
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

![edited config](https://github.com/user-attachments/assets/165fb424-1d01-420a-aa2c-53b128b21917)

Let us now open OpenLane and run the flow with our new inverter.
```
cd Desktop/work/tools/openlane_working_dir/openlane
docker
```

Steps to run in OpenLane:  
```
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
# Synthesis
run_synthesis
```

You can see that the Synthesis is successful and 1554 instances of our new inverter have been used in the design.  
![succ synth](https://github.com/user-attachments/assets/99aced7d-2083-488a-b056-226029c5ae76)

But you can notice that, slack has been violated here.  
Total Negative slack (tns) = -711.59ps
Worst Negative Slack (wns) = -23.89ps
![slack violation](https://github.com/user-attachments/assets/4cdf8afb-4348-4d7c-8563-991c55c096d5)

In the next section, we will look at how to improve the timing.  

#### Power Aware CTS
Let us look at 2 Clock Gating structures with And and Or Gates.  
![Clock Gates](https://github.com/user-attachments/assets/468e23ca-db5c-4f51-99a7-44a127118f3a)
The advantage of Clock Gates is that the clock tree beyond the clock gate will not be switching when the clock gate is not enabled. Here there won't be any switching power consumed by the clock treee.  

**How to use the clock gates?**  
In the below clock tree, can the clock buffers cannot simply be swapped with the Clock Gate. Before swapping, we need to make sure the And Gate satisfies the timing constraints of the clock tree.  
![Gating](https://github.com/user-attachments/assets/5f9edf51-321a-4aba-91d9-301e32366290)

We can make the following observations from a closer look at the clock tree:  
![clock tree](https://github.com/user-attachments/assets/e8a7e929-9047-4daa-96e7-137e1f190e21)

The input transition time and output load for a clock buffer can vary for differnt conditions.  
Therefore, delay tables are developed for each buffer.  
The buffer is taken seperately and its input slew and output load are varied for a range of operatable values and the cell delay is captured in the form of a delay table.  
This is known as timing characterization of a cell and it is done for all standard cells.  
Delay table of CBUF of size 1 and size 2:    
![delay table](https://github.com/user-attachments/assets/440badbf-6104-46b2-a4a2-1e2082266391)

Let us now try to calculate the delay of the clock tree:  
Say we simulate the tree with a signal having input slew of 40ps and the output load of the first CBUF is 60fF. The delay value is not directly avaialable in the table for this combination. So, it is extrapolated. Let us call this delay as x9'.
Now let us say, signal arrived with 60ps transisiton time at Node A (this slew is also a function of input slew and load and can be obtained from the transition table). The output load is 50fF for each CBUF 2. So this delay is y15. Intersection of the 60ps row and 50fF column.  
Therefore, total cell delay = x9' + y15 at each end point flop.  
This implies that the skew between any 2 end points is 0.  
![delay skew](https://github.com/user-attachments/assets/db576655-04ce-4122-a621-220188a070db)

If the Clock Buffers were not driving the same loads and clock buffers, the delay value woud have been different and there would be clock skew at the end point.  

If the flops are to be activate only during certain functionalities of the chip, then we can actually stop the clock propagation to the flop conditionally. This is done by adding the clock gates that we discussed earlier. This helps in power saving.    

#### Timing aware Synthesis
Let us open the OpenSTA timing report and look the path with worst slack.  
![timing report wns](https://github.com/user-attachments/assets/f99261e0-55a3-422a-b519-e7abe6b33805)

This path has some of the worst cell delays that have led to this kind of slack violation.

In order to do a timing aware synthesis, we need to use the setting SYNTH_STRATEGY.  
Below is its definition from the Readme file.  
![synth strategy](https://github.com/user-attachments/assets/546fed0f-96fe-47fe-ba8a-eff3482fd22e)
So, this variable heps in optimizing the Area or Delay while synthesis.
If we set the value to Delay 3, it implies that it tries to minimize the delay, even though the area increases.  

Another variable that can be set is SYNTH_BUFFERING that can enable the buffers to be added on high fanout nets to reduce the net delay.  
Let us check its value. It is already set to 1.

Then , we can check the variable SYNTH_SIZING which allows the tool to size the buffers as required. Let us check its value and set it to 1.

We can also check the value of the SYNTH_DRIVING_CELL inorder to make sure that it is high drive strength cell. In this case it is the *_inv8 which already has good drive strength.

Let us now run synthesis with there settings.

Commands:  
```
echo $::env(SYNTH_STRATEGY)
set ::env(SYNTH_STRATEGY) "DELAY 3"
echo $::env(SYNTH_BUFFERING)
echo $::env(SYNTH_SIZING)
set ::env(SYNTH_SIZING) 1
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```

Before running Synthesis let us note the value of Chip area and Slack to do a comparison:  
> Chip Area: 147712.9184 um^2  
> tns: -711.59ps  
> wns: -23.89ps  

![chip area](https://github.com/user-attachments/assets/53cbc853-a4ee-447f-927d-bc0468fbb6ca)

Snapshots of command execution:  
![synthesis settings](https://github.com/user-attachments/assets/81914402-0037-40ca-a356-b9d5f44cc8e5)

After the execution, let us first check if our custom inverter has been included in the design.  
In the below snapshot of merged.lef we can see that the MACRO sky130_vsdinv has been included.  
![merged lef](https://github.com/user-attachments/assets/bfea4735-ca1a-4c16-9700-96281b35c26d)

The area of the Chip is now increased, but the slack violations are not there.  
> Area of the chip: 181730.544 um^2.
> tns = 0
> wns = 0

Synthesis run snapshots:  
![synth run](https://github.com/user-attachments/assets/f8440b46-2cca-4817-8e01-85a5a86023ac)
![synth run 2](https://github.com/user-attachments/assets/c5c4e452-77b7-4b41-bd38-1e7d287f6b6b)

Our inverter has been used in the timing paths and the timing is met.  
![timing](https://github.com/user-attachments/assets/e0344eb1-b2a1-4ea2-8eb7-f08218389a5f)

#### Floorplan and Placement
Let us now run floorplan and placement.  
Commands:  
```
run_floorplan
```
This is giving an error during the run as there are no MACROs in the picorv32a design.  
![run floorplan](https://github.com/user-attachments/assets/01fe6230-a0eb-482c-bc9a-89ae9bec8ca7)
![error](https://github.com/user-attachments/assets/01fea03c-4330-405a-9d9d-124f162560fd)

Let us see how this error can be resolved. Let us take a look at the floorplan.tcl file that is internally executed when the run_florrplan command is executed.  
Let us look for the MACRO related section here. The variable MACRO_PLACEMENT_CFG is not set in the openlane shell.  
![macro var](https://github.com/user-attachments/assets/cb9a7a80-33fa-4ce6-8cc7-d7286a15c3ac)

So, it is going to the else part and executing the commands - global_placement_or and basic_macro_placement
![floorplan tcl](https://github.com/user-attachments/assets/3ca6e9d0-2934-4aab-b1c3-c35737011bd0)
Therefore, the problem should be with either of these 2 commands, let us run them individually.
First, let us check if the basic_macro_placement command is the problematic one as it is related to the Macros.  
![macro err](https://github.com/user-attachments/assets/ed2508bc-9cb8-48a3-af2f-ab95fb2054a1)
You can now verify that this is the step causing the issue as there are no macros in the deisgn.

Therefore, let us break the floorplan step into micro commands and execute each command indivdually in the order it is present in the floorplan.tcl.  
Below are those steps:
```
init_floorplan
place_io
apply_def_template
global_placement_or
tap_decap_or
scrot_klayout -layout $::env(CURRENT_DEF)
run_power_grid_generation
```
Snapshot of Commands run:
![cmd 1](https://github.com/user-attachments/assets/d852a9b5-f539-4874-8e5b-7542d417ec0c)
![cmd 2](https://github.com/user-attachments/assets/56a8b680-cab9-4283-a7c7-3538dd878236)
![cmd 3](https://github.com/user-attachments/assets/93924948-168c-40e3-b012-fad47d152ca0)
![cmd 4](https://github.com/user-attachments/assets/86d9cace-06a4-4f85-a26d-0cd32c1f2744)
![cmd 5](https://github.com/user-attachments/assets/8de3f19f-0f4a-43df-8677-1b6a9a302da8)

Now floorplan is executed successfully.

Let us do the placement.  
Command:  
```
run_placement
```
![placement cmd](https://github.com/user-attachments/assets/0ede93b2-2d1b-4a36-9598-8f2a5600fe2d)

The overflow value should become as close to 0 as possible:  
![overflow](https://github.com/user-attachments/assets/f9d9c3fd-7fb4-451f-9ed4-fb9bf9d70399)

Placement is successful:  
![placement](https://github.com/user-attachments/assets/9a88a828-2899-46a6-b256-ee95280a6687)

Let us open Magic now to view the DEF after Placement.  
Commands:
```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/19-08_03-05/results/placement/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Magic Layout after placement:  
![image](https://github.com/user-attachments/assets/e2015901-fee9-4981-b6be-4a7d462a885f)

Let us zoom in and find some instatiations of our custom inverter. You can find it in the snapshot below:  
![inv custom](https://github.com/user-attachments/assets/7a175350-2577-4d92-b6d5-83e4527ce9c1)

The overlap that we see between the cells is beacuse of abutment to share the Pwr and Gnd rails.  

Typing command `expand` in the tkcon window shows the layers. We can see perfect alignment.  
![layers](https://github.com/user-attachments/assets/f7eabb3f-e91f-494f-ac6e-16f2f5f602b1)

With this we have confirmed that our custom inverter cell has been used in our design.  

We can also see that the PDN has been routed:  
**GND Rails:**  
![GND](https://github.com/user-attachments/assets/c55ca7af-53f5-4471-b954-972af71b362d)

**PWR Rails:**  
![PWR](https://github.com/user-attachments/assets/735d6377-2812-482f-9ff4-8f6dbd6b764e)

#### Timing Analysis with ideal clocks using OpenSTA
##### Setup Timing Analysis with Single Clock
Consider a basic design with a launch flop, capture flop and some combinational logic as shown here. Ideal clock implies that the clock tree is not yet built.  
![Ideal clock setup](https://github.com/user-attachments/assets/f2f7c824-2af3-4b34-9335-343091fb38d1)

This circuit is designed to be operated at 1GHz Frequency.  
Therefore, the time period T = 1/F = 1/1G = 1ns.  
The first rising edge of the clock reaches the Launch flop at 0ns and the 2nd rising edge of the clock reaches the Capture flop at 1ns.  
The combinational logic data has to reach the Capture Flop before the 2nd rising edge of the clock reaches the capture flop and activates it.  
Therefore, the delay in the signal travelling from Launch Flop to Capture Flop &Theta; < the time period of the Clock T.  
&Theta; < T

Let us now look at a practical Flop. A flop internally consists of 2 Mux.  
![Mux implementation of flop](https://github.com/user-attachments/assets/f419d94b-7172-42f4-b12b-b17478ab1b95)
When the Clock is at 0, the input at D is at the output of Mux1, Qm. And the output of Mux2 will be Q only.  
When the Clock rises from 0 to 1, the output of Mux1 is Qm only and Qm is transferred to the output of Mux2.  
Now, you see that there is a finite amount of time required for the input data to be transferred to output of Mux1 and from there to output of Mux2.
The input data should be available at Qm before the clock rises so that when the clock rises to 1 it can be transfered to the output of the Mux2, i.e., the output of the flop.
The time taken for the input data to come to the output of Mux1 is known as the Setup time of the Flop.

Therefore, now our equation changes to:  
&Theta; < (T-S)
Where S is the setup time of the flop.  
![setup time](https://github.com/user-attachments/assets/6d867479-5f26-4d3a-ad43-6add3042c8a3)

Now, let us consider another practical scenario. In real designs, the clock is generated from a Phase Lockes Loop (PLL). This will not be an ideal clock. i.e., the clock edges will not arrive at exactly 0ns, Tns, 2Tns etc.  
This temporary variation in clock period is called Clock Jitter. This uncertainity (SU) has to be accounted for in our setup check equation.  
&Theta; < (T-S-SU)  
![jitter](https://github.com/user-attachments/assets/03313008-1f74-4a40-a778-8a5517f9bc89)

Let us consider the example circuit we had been using previously and identify the combinational delay &Theta;  
For Clock path 1, the combinational delay is shown in the figure below. It includes the Launch flop Clk to Q delay, wire delays, combinational gate delays.  
![combo delay1](https://github.com/user-attachments/assets/d10952a9-9edb-4873-b907-2cf46b19c673)

For Clock path 2, there is no wire delays. So, the equation will be as shown below:  
![combo delay 2](https://github.com/user-attachments/assets/9f2bf64e-afdc-4761-bc67-6766e1bd0b60)

These 2 combinational delays &Theta; should be less than (T-S-SU).  

##### Timing Analysis Lab
The timing analysis is done using the tool OpenSTA.  
Let us prepare the setup files for running the timing analysis in OpenSTA.  
Pick up the sta.conf file from the previously downloaded git repo directory `vsdstdcelldesign`.  
Copy paste it to the `openlane` directory.  
Commands:  
```
cd Desktop/work/tools/openlane_working_dir/openlane
cp vsdstdcelldesign/extras/sta.conf pre_sta.conf
gvim pre_sta.conf
```
Let us now edit this file according to our design.  
Enter the path to the min analysis liberty file and max analysis liberty file. They are present under the src directory.  
Let us use the Verilog file from the run w/o timing optimized synthesis, so that we can see the slack numbers.
The timing optimized synthesis run has 0 tns and 0 wns.  
Then, fill the design name as picrv32a.  
Now, for the SDC file, let us copy the file from the git repo directory `vsdstdcelldesign` and edit it to suit our requirements. It is also based on the `base.sdc` loacted in `openlane/scripts/base.sdc`.  
Commands:  
```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
cp ../../../vsdstdcelldesign/extras/my_base.sdc .
```

Final pre_sta.conf file:  
![pre_sta.conf](https://github.com/user-attachments/assets/37b965f0-7e2a-4b3b-9ed9-e1bc6407d232)

Let us now look at the my_base.sdc file:  
Here, we have to chnage the clock period to match our design: 24.73ns.  
Let us verify the SYNTH driving cell, the output pin and load cap from the typical liberty file.  
![base sdc](https://github.com/user-attachments/assets/25e63e44-97c6-46d3-af42-eedd3ab018ab)


Let us now run STA using OpenSTA tool.  
Commands:  
```
cd Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```
OpenSTA timing analysis:  
![result 1](https://github.com/user-attachments/assets/ea42d2a2-f657-4a8f-9753-6ba95976b112)
![result 2](https://github.com/user-attachments/assets/4a09f9b0-0587-486e-814f-8f173b3ad646)

Currently, we have not done the CTS yet, so the hold analysis will be overly optimistic.  
Let us look at the setup analysis. In some stages we can see a high fan-out value of 5 or 6 and also correspindingly the delay of those stages is also high.  
![setup ana](https://github.com/user-attachments/assets/5c5b7290-457a-4dd0-8492-46064d9ff6fd)

Let us look at the variable to control the fanout of the cells from the Readme file. It is SYNTH_MAX_FANOUT.  
![synth max fanout](https://github.com/user-attachments/assets/b24e1f85-aac6-4a6a-be19-d241c655ec6b)

Let us define it in the OpenLane shell and re-run the timing analysis.  
For this, we will re-use one of our previous runs which has been run till the synthesis step with the required configurations.  
The SYNTH_MAX_FANOUT here is 6 as shown in the shell.  
Let us set it to 4 and re-run synthesis.  
Commands:  
```
package require openlane 0.9
prep -design picorv32a -tag 18-08_18-16 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
set ::env(SYNTH_MAX_FANOUT) 4
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```

![synth with fan 4](https://github.com/user-attachments/assets/ab0a3bf4-6541-47ac-a349-f29dcca3d5a9)
![synth with fanout 4](https://github.com/user-attachments/assets/b3d22661-cac6-4323-8df0-15206ff40c34)

Results after Synthesis with Fanout 4.  
![image](https://github.com/user-attachments/assets/d2652f1f-9064-4984-9f9d-750c541c46c2)

As there was not much change in the slack values with Fanout set to 4, let us re-run with fanout = 2.
Commands:  
```
package require openlane 0.9
prep -design picorv32a -tag 18-08_18-16 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
set ::env(SYNTH_MAX_FANOUT) 2
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```
Improved slack after SYNTH_FANOUT was set to 2.
![synth after fan out 2](https://github.com/user-attachments/assets/0757609c-c3ce-4de8-9deb-ff3802a8cb72)

Let us now run Timing Analysis in OpenSTA and cross check our results.  
The Verilog file in the pre_sta.conf has now been overwritten with the newly synthesised verilog file.  
Execute the commands:  
```
cd Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```
You can see that the slack numbers are the same and the tns has significantly come down.  
![opensta fan 4](https://github.com/user-attachments/assets/dfb2f129-c206-4e71-a534-14662799ecf6)

##### Timing ECO fixes by upsizing the cells
Let us now try to reduce the slack for the worst path.  
In this path, we see that the highlighted stage is adding a delay of 1.53ps.  
![stage delay 1.53](https://github.com/user-attachments/assets/a69b8ce4-d9d4-407d-8be6-368f74a113dd)
It is a OR gate with drive strength 2. Let us replace it with OR gate with drive strength 4.  
Command:  
```
replace_cell _25892_ sky130_fd_sc_hd__or4_4
```
![replace cell](https://github.com/user-attachments/assets/c6c6fc6b-7f2c-4c36-a9bc-6505fe27cf10)

Let us now report the slack of the same path again to check if the slack is reduced and the instance is replaced.  
You can see that the instance has been replaced with Or gate with drive strength 4 and the stage delay has reduced to 1.026ps.
![replaced cell](https://github.com/user-attachments/assets/abfa86c5-4bba-436e-98cc-1638932b4246)

Slack of the path after replacement:  
![slack of updated path](https://github.com/user-attachments/assets/4a4e3af7-b138-41d4-9b43-4a59cfb1d637)
The Slack has reduced from -22.05ps to -21.5227ps

Let us try to replace one more cell and reduce the slack.
Here, we have identified one more stage which is contibuting 0.968ps delay.  
![stage delay 2](https://github.com/user-attachments/assets/2d4ae38d-10c3-48d3-8df1-8edf4aec85be)

Let us report the connections of the net it is driving.  
Commands:  
```
report_net -connections _21773_
```
We can see that it is driving 2 cells of drive strength 2 and 1.
![report connections](https://github.com/user-attachments/assets/29fa86ed-bc14-4e1b-8c3d-e4aadb01723b)

Let us replace the cell with a higher drive strength cell:  
```
replace_cell _25921_ sky130_fd_sc_hd__or3_4
```
![replace cell](https://github.com/user-attachments/assets/e1c8077a-32c7-4b63-8e99-f52ba739934f)

Let us re-check the slack for the path now. The instance is replaced.  
![replaced instance](https://github.com/user-attachments/assets/1c00e6ac-419b-4320-b2cf-b6c279ce9001)

Slack is now reduced to -21.178ps.  
![reduced slack](https://github.com/user-attachments/assets/ed0144d3-f052-4b40-82de-8295caa1f3fc)

We started with:
> tns = -628.08ps  
> wns = -22.05ps

Now, we have:
> tns = -613.38ps  
> wns = -21.63

Now in order to dump out the updated netlist, we have to use the `write_verilog` command.  
```
write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/18-08_18-16/results/synthesis/picorv32a.synthesis.v
```
![write verilog](https://github.com/user-attachments/assets/67b9dbcd-6824-4992-a747-a57bc51fb735)

In order to confirm that the updated netlist is dumped out, let us search for the updated cell in the new verilog file.  
![new verilog](https://github.com/user-attachments/assets/24369285-3d32-4269-bf8a-1baaeef7b18c)
We can see that the instace has been replaced with an instance of OR gate with strength 4.

