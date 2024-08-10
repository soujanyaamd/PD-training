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

### Introduction to SoC Design and OpenLane
#### Digital ASIC Design using Open Source Materials
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

##### What is a PDK?
A **PDK** or a **Process Design Kit** is the interface between the Fabs and the Designer. It contains the files used to model a fabrication process for the EDA tools used to design an IC.
It contains
1. Process Design Rules - DRC, LVS, PEX
2. Device Models
3. Standard Cell Libraries
4. I/O Libraries
5. etc.

ASIC Design Fow is a complex process that involves many steps. **OpenLane** is a software that streamlines this flow, and brings all the required open source components for the ASIC Design Flow under one umbrella.

#### Simplified RTL to GDS Flow
The ASIC Design Flow consists of muliple steps such as Synthesis, Floor Planning, Power Planning, Plcaement, Clock Tree Synthesis, Routing and Signoff/Tape-out.

![Simplified RTL to GDS Flow](https://github.com/user-attachments/assets/a9ca0a77-3d42-47fb-a527-fb39a574fa99)

##### Synthesis
Synthesis converts the RTL to a circuit that can be constructed from the cells of the Standard Cell Library.
The output of Syntesis is a **Gate Level Netlist**. It is functionally equivalent to the RTL.

![Synthesis](https://github.com/user-attachments/assets/6c0a28ce-523a-479e-83bd-118e5af1ca6e)


The Standard cells from a library can be implemented on a layout in a very even fashion as they have defined heights and widths that are multiples of the basic standard cell inverter with 1X drive strength.  
Each Standard cell has different models/views that are interpreted by different EDA Tools.  
The Liberty View includes Electrical Model of the cells such as Delay Models and Power Models.  
The Spice View or the CDL View of the cells is present.
The cells also have Layout Views - GDSII Views and LEF Views.

##### Floor Planning and Power Planning
Floor Planning involves determining the shape and size of each bloack and allocating it a specific place in the Core of the Chip. Therby it helps in defining the routes between the blocks.
**Chip Floor Planning** - Partitions the chip between different building blocks and places the I/O Pads.
**Macro Floor Planning** - The dimensions, Pin locations and rows are defined for the Macros.
**Power Planning** - Creates a power grid such that each and every macro, standard cell and all other blocks present in the design are connected to the Power Grid.
It consists of horizontal and vertical metal straps. This structure reduces the resistance and hence the IR Drop. Typically, uppper metal layers are used for Power Distribution Network as they are thicker than lower metal layers. Thus have lesser resistance.

![Chip Floor Planning](https://github.com/user-attachments/assets/5f65821a-1a6a-4b92-9b7f-67f9b9736100)
![Power Planning](https://github.com/user-attachments/assets/c4e83de4-31b3-4c41-8c35-eaff062d84c5)


## Prepare Deisgn
![image](https://github.com/user-attachments/assets/6d505db9-dcd0-4ce5-b25c-2a7bff1a16d6)
![image](https://github.com/user-attachments/assets/0a731e08-176a-451c-9a89-07ffdf1991ea)

## Synthesis
![image](https://github.com/user-attachments/assets/ff584423-6d6d-4850-aa9f-4b7dc4bb602d)

## Calculating FF Percentage
![image](https://github.com/user-attachments/assets/9b5fcc15-b3a4-42fb-b65d-6ff0c383fc6a)
![image](https://github.com/user-attachments/assets/60736d99-98cb-444b-a84e-a12b34c70518)

Percentage of DFF = (1613/14876)*100 = 10.84%

## Floor Planning
![image](https://github.com/user-attachments/assets/a1c6a445-42ba-41e0-a852-f8a7cdd189ee)
![image](https://github.com/user-attachments/assets/13604c4f-a940-4ab3-94f3-05eb01755389)

## DEF Floorplan in Magic
![image](https://github.com/user-attachments/assets/43294628-f7ee-462f-982e-6d3b1c9c1963)
