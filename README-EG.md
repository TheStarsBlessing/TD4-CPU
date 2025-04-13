This is a computer consisting of 74 series digital circuit chips that satisfies the five basic building blocks of the computer proposed by von Neumann.

## Project Brief Introduction

This four-bit computer built with 74 series chips can perform simple I/O, operand transfer, addition, (conditional) transfer operations, and can be used to adjust the clock frequency, use a single pulse input, or use an external clock input.In addition, the computer integrates a digital tube for digital output and a large number of status indicators, which can intuitively observe the operation of the computer. Through the use of SMD components, the PCB board of this project is compressed within 10*10cm, free proofing is supported, and all components are on the front, and the components are generally arranged evenly, making manual placement easier (if SMT is used, single-sided placement can greatly reduce costs), and at the same time improving the aesthetics of the computer.

The design scheme of this project is based on the book "Easy Introduction to CPU Design with 10 ICs" by Japanese engineer Iku Watanami, and the PCB board is partially modified based on the original circuit.

## Project features

Simple input/output, addition operations, operand transfer, (conditional) transfer. It is suitable for beginners to have a simple understanding of the basic composition and operation principles of computers.

## Project parameters

2 arithmetic registers (A, B), 1 output register, 1 program counter (PC).

16B ROM (Program Memory) with DIP switch input.

1 input port, 2 output ports, 1 7-segment digital tube (0-15 can be displayed).

1HZ&10HZclocks& A clocks which can be adjusted (the accuracy is not high, for reference only).

TYPE-C POWER INPUT.

## Principle Analysis (Hardware Description)

The TD4-CPU consists of five parts: ROM array, register group, arithmetic and logic unit (ALU), input and output unit, and clock and reset unit.

The main body of the ROM array consists of 16 DIP switches (U0-U15). The DIP switches represent digits 0-7 from right to left, with '1' for up and '0' for down. The 74HC154 (U17) is responsible for decoding the binary data sent by the PC register, pulling the rear end of the corresponding ROM unit down to a low level, while the front end of the ROM module is pulled up to a high level by 8 resistors via the bus. When the rear end of the ROM unit is at a low level and a certain bit of the DIP switch is on, the corresponding bus for that bit is pulled down to a low level, at which point the bus state reflects the value obtained by inverting the data stored in the ROM unit; subsequently, the data on the bus is inverted by the 74HC540 (U16) (which enhances driving capability) and output to the ALU module to complete the operation of reading the data from the ROM unit pointed to by the PC register.

The register bank consists of four 74HC161 (4-bit binary preset synchronous addition counters) containing two arithmetic registers (REG_A (U19) & REG_B (U20)), an output register, and a PC register. The arithmetic register is responsible for storing the temporary data that will participate in the computation; The output register is responsible for latching the data to be output; The PC register (program counter) is responsible for storing the first address of the next instruction to be executed, and the PC register will automatically add one after the CPU addresses, and the content of the PC register can also be modified by the instruction, so as to realize the jump operation of the program. The register bank data is input by the accumulator (ADD) through the bus, and four LOAD control lines control each register to latch (active-low), and after the system reset, all four registers are assigned 0x00.

The ALU is composed of a multiplexer (MUX), an accumulator (ADD) and a logic unit, and the MUX is composed of two 74HC153s, each of which controls two inputs; MUX is controlled by two control lines, SEL_A and SEL_B from the logic unit, and can choose data from REG_A, REG_B, input ports or no data input; The data enters ADD through MUX to participate in the calculation. ADD (74HC283) is the core of this computer, and all addition operations are done by ADD; The data of ADD comes from the immediate number (Im) of the direct output of the MUX and ROM units; When the immediate number is 0, ADD only transfers the data, and the transfer or jump operation can be completed at this time. The logic unit consists of a Class D rising edge flip-flop 74HC74, OR gate 74HC32, and three inputs AND AND gate 74HC10; 74HC74 is responsible for latching the carry flag of ADD; The 74HC32 and 74HC10 are responsible for the processing of instructions (four bits higher) and control the register bank and MUX cell.

The computer's instructions and corresponding variable values are as follows:

|   Assembly language   |   Binary   |SEL_A|SEL_B|MUX Options|LOAD0|LOAD1|LOAD2|LOAD3|LOAD option|ADD output items|
|----------|-----------|-----|-----|--------|-----|-----|-----|-----|----------|---------| 
| ADD A,Im | 0000 XXXX |  0  |  0  |  REG_A |  0  |  1  |  1  |  1  |  REG_A   |   A+Im  |
| MOV A,B  | 0001 0000 |  1  |  0  |  REG_B |  0  |  1  |  1  |  1  |  REG_A   |    B    |
|   IN A   | 0010 0000 |  0  |  1  |   IN   |  0  |  1  |  1  |  1  |  REG_A   |   IN    |
| MOV A,Im | 0011 XXXX |  1  |  1  |   NC   |  0  |  1  |  1  |  1  |  REG_A   |   Im    |
| MOV B,A  | 0100 0000 |  0  |  0  |  REG_A |  1  |  0  |  1  |  1  |  REG_B   |    A    |
| ADD B,Im | 0101 XXXX |  1  |  0  |  REG_B |  1  |  0  |  1  |  1  |  REG_B   |   B+Im  |
|   IN B   | 0110 0000 |  0  |  1  |   IN   |  1  |  0  |  1  |  1  |  REG_B   |   IN    | 
| MOV B,Im | 0111 XXXX |  1  |  1  |   NC   |  1  |  0  |  1  |  1  |  REG_B   |   Im    | 
|   OUT B  | 1000 0000 |  1  |  0  |  REG_B |  1  |  1  |  0  |  1  | REG_OUT  |    B    |
|   OUT B  | 1001 0000 |  1  |  0  |  REG_B |  1  |  1  |  0  |  1  | REG_OUT  |    B    |
|  OUT Im	 | 1010 XXXX |  1  |  1  |   NC   |  1  |  1  |  0  |  1  | REG_OUT  |   Im    | 
|  OUT Im	 | 1011 XXXX |  1  |  1  |   NC   |  1  |  1  |  0  |  1  | REG_OUT  |   Im    |
|   JC B   | 1100 0000 |  1  |  0  |  REG_B |  1  |  1  |  1  |  C  |   PC&Q   |    B    |
|  JMP B   | 1101 0000 |  1  |  0  |  REG_B |  1  |  1  |  1  |  0  |    PC    |    B    |
|   JC Im  | 1110 XXXX |  1  |  1  |   NC   |  1  |  1  |  1  |  C  |   PC&Q   |   Im    |
|  JMP Im  | 1111 XXXX |  1  |  1  |   NC   |  1  |  1  |  1  |  0  |    PC    |   Im    | 

*C refers to the carry flag of ADD

The input-output unit consists of a set of input headers, two sets of output headers, and an on-board digital tube module. When the power supply, ground, clock, and reset pin header are on the left side of the upper output pin header, the order is the same as that on the 16*16 LED display board, and can be directly connected with an 8PIN cable. The input pin header should ensure that the four-digit DIP switch is in an off state when in use, otherwise the external input low level cannot be reached. The on-board digital tube module is composed of a decoder (74LS247) and a digital tube (common yang), 74LS247 is a four-wire seven-segment decoder that can display 0-16, which is used to drive the 247 digital tube, and the toggle switch can prohibit the output of 74LS247, and the digital tube remains fully lit at this time. The output value of the digital tube is shown in the figure:
![2025-04-12_153411](https://github.com/user-attachments/assets/44d240c3-8ab8-42fa-8a7c-414719c850dd)

The clock and reset unit consists of a timer NE555 and an inverter 74HC14. NE555 IS RESPONSIBLE FOR GENERATING A FIXED FREQUENCY CLOCK SIGNAL, AND CAN SELECT THREE OUTPUT MODES: 1HZ, 10HZ, AND ADJUSTABLE THROUGH THE DIP SWITCH; In adjustable mode, the clock frequency is adjusted by adjusting the coarse and fine potentiometers. The 74HC14 is used to generate a single clock signal and a reset signal and drive the clock reset display LED lamp, and the current limiting resistance of the reset circuit is larger, so it will only play a role in the next second or so; The reset resistance of the single-time clock circuit is small, which plays a certain role in debounce. The clock generation circuit is connected with the computer clock network through a jumper cap, and the external clock signal can be connected by unplugging the jumper cap. In the first proofing, the inverting RC oscillation circuit of the original TD4 CPU was used, but during the test, it was found that the oscillation circuit could not work, and the cause of the problem could not be found, and the replacement of the component and re-soldering failed to solve the problem; Therefore, the original circuit was replaced with the more stable NE555 during the second proofing.

## PCB

The PCB adopts a four-layer board structure, and the board is FR-4. The PCB size is 10cm x 10cm; The default board thickness is 1.6mm, and the default copper thickness is 1oz. Minimum line width: 8mil, minimum hole diameter: 12mil (0.305mm).

Other processes required for free proofing: green solder mask, white characters, lead sprayed tin or immersion gold, vias cover oil.

## Precautions

Please conduct a power-on test before soldering the plug-in, and check the operation of the computer through the indicator light to prevent problems such as virtual soldering and chip damage, so as not to affect the follow-up experiment.

The soldering temperature should not be higher than 265°C, and the high-temperature soldering time should be reduced as much as possible to avoid damage to the components.

## Reference diagram

Schematic:

![SCH_TD4-CPU v1 1_2025-04-041](https://github.com/user-attachments/assets/09751efc-cfa3-46d1-bc78-450b53b434a7)
![SCH_TD4-CPU v1 1_2025-04-072](https://github.com/user-attachments/assets/91bc5b03-d747-4e4a-94ae-0f0ae2f20b2d)
![SCH_TD4-CPU v1 1_2025-04-043](https://github.com/user-attachments/assets/1aab0533-a857-489e-bfd1-4be8fd4c6826)

PCB:

![PCB_TD4_CPU v1 1_2025-04-121](https://github.com/user-attachments/assets/c3270392-1562-4e8f-a0e9-c66a0fd1068d)
![PCB_TD4_CPU v1 1_2025-04-122](https://github.com/user-attachments/assets/cd3aa48a-8275-4ffd-b9db-1990fda8d670)
![PCB_TD4_CPU v1 1_2025-04-1221](https://github.com/user-attachments/assets/92674e35-ea9b-43f4-9c60-6046755a9f17)
![PCB_TD4_CPU v1 1_2025-04-1222](https://github.com/user-attachments/assets/9f51f5d9-8010-4290-89e1-6a20cff6b1ff)

Simulation diagram:

![3D_TD4_CPU v1 1_2025-04-05](https://github.com/user-attachments/assets/fc878042-6e11-4365-ac08-039a254aeec4)
![3D_TD4_CPU v1 1_2025-04-052](https://github.com/user-attachments/assets/645873ea-6514-4e01-886e-396b0138e7ff)

Physical drawing:

![微信图片_20250405150307](https://github.com/user-attachments/assets/f5feb81f-9cf6-4a11-9d9b-a06f05859e21)
![微信图片_20250405150259](https://github.com/user-attachments/assets/2d6ae476-23bb-4764-a443-037b0c1a4189)
![微信图片_20250405150250](https://github.com/user-attachments/assets/3f1130c7-2d60-4ddb-9260-767b0a3f5782)

Test video:

https://github.com/user-attachments/assets/fe938cf9-944d-4810-b972-968d4ed37faa

## Version updates

2024.11.4 Project approval.

2024.11.14 Schematic drawing completed.

2024.11.16 The PCB board drawing is completed, and the first proofing is made.

2025.1.18 Soldering completed, clock circuit working abnormally.

2025.1.20 Modify the clock circuit and digital tube circuit, and make the second proof.

2025.2.1 The second welding is completed, the digital pipe line is drawn incorrectly, and the sample will return to normal after flying wire processing.

2025.3.9 The third proofing, so the proofing only modified two lines on the basis of the second one, and there is basically no abnormality.

2025.3.15 The sample was received, there was no abnormality, and the project was temporarily closed.
