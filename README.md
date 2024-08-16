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
