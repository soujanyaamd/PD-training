# Advanced Physical Design using OpenLANE and Sky130 PDK
> This repository is developed as a part of Digital VLSI SoC Design and Planning Workshop conducted by VLSI System Design.  
> The training is arraged by Ansys Software Pvt. Lmt.  
> The training covers the RTL2GDSII Workflow.

<details>
<summary>
How to talk to computers?
</summary>

## Introduction to Board, Package, Chip, Pads, Core, Die, IC and IP
In this section, we will understand the difference between the terms Board, Package, Chip, IC, etc.
### Board
A **Printed Circuit Board (PCB)** is a flat board made with insulating material and has conductive pathways carved on its surface to connect various electrical components such as microprocessors, sensors, resistors, capacitors, diodes, transistors, etc.
The commonly available boards in the market such as Arduino are examples of PCBs.
![Arduino UNO](https://github.com/user-attachments/assets/77f9ef11-a959-4d07-84f0-d5704352b8ee)

The typical components on a Board can be understood with the help of a diagram.
![Block diagram of a Board](https://github.com/user-attachments/assets/db193ea3-ba69-4069-a54a-63b48b67fc1a)

The Black color component (Highlighted in Yellow) sitting at the centre of the Board is the Processor/SoC.
The rest of the components such as the GPIO pins, Power pins, external memory chips such as SDRAM, JTAG-UART pins to program the processor, ADCs, etc. are located around the Processor.

### Package
A **Package** is a supporting case within which the Chip is encapsulated. It prevents physical damage and corrosion.
The Package also supports the electrical contacts which connect the Chip to the Board.  
There are different types of Packages available in the market such as Quad Flat No-Lead Packages (QFN-48).  
This is a 7x7mm<sup>2</sup> package with 48 pins.
![A Typical Package](https://github.com/user-attachments/assets/9d640fc8-6062-4353-b4c3-34f32b3098c0)

### Chip
A **Chip** typically sits at the centre of a Package. The Chip is connected to the Package using _Wire Bonds_. 
This enables the Chip to communicate with the outside world.  
<img src="https://github.com/user-attachments/assets/14473911-230e-4ed1-b2b1-fd8236dc6ef9" width="507">
<img src="https://github.com/user-attachments/assets/38392a8c-ba26-42b5-b4de-b4e310ab1dcd" wicth="400">

### Pad
A **Pad** is the peripheral area of a Chip on which the Pins are soldered to. These are small conductive areas on the surface of the Chip.
They make electrical connections between the internal circuitry of the chip and the external package leads or pins.
They are typically made of a metal such as aluminum or copper.
The signals coming into the chip come through the Pads.

### Core
The **Core** of the Chip is located within the Pads. The Core is where the digital logic of the Chip is present.

### Die
The **Die** encompasses both the Core and the Pads. The Die is manufactured on a Silicon Wafer.
Infact multiple dies are manufactured on a single Silicon Wafer.  
![Die](https://github.com/user-attachments/assets/b31cdf31-c76e-4126-86e9-881b84f7246b)

### IPs and Macros
Multiple elements sit within the Core of a Chip. It includes the SoC, SPI, PLLs, ADCs, DACs, SRAM, etc.  
PLLs, ADCs, SRAMs are **Foundary IPs**.These are patented by the Foundrys and can be used by the Chip Designer.  
The repeatable blocks of digital logic are called **Macros**.  
![IPs and Macros](https://github.com/user-attachments/assets/96cfe919-c296-409d-a954-d62cd04c1479)
</details>

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
