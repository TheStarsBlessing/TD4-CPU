This is a computer consisting of 74 series digital circuit chips that satisfies the five basic building blocks of the computer proposed by von Neumann.

## Project Brief Introduction

This is a four-bit computer built with 74 series chips, which can perform simple input and output, operand transfer, addition, (conditional) transfer operations, and support for adjusting the clock frequency, using a single pulse input, or using an external clock input. In addition, the computer integrates a digital tube for digital output and a large number of status indicators, which can intuitively observe the operation of the computer. Through the use of SMD components, the PCB board of this project is compressed within 10*10cm, free proofing is supported, and all components are on the front, and the components are generally arranged evenly, making manual placement easier (if SMT is used, single-sided placement can greatly reduce costs), and at the same time improving the aesthetics of the computer.

The design scheme of this project is based on the book "Easy Introduction to CPU Design with 10 ICs" by Japanese engineer Iku Watanami, and the PCB board is partially modified based on the original circuit.

## Project features

Simple input/output, addition operations, operand transfer, (conditional) transfer. It is suitable for beginners to have a simple understanding of the basic composition and operation principles of computers.

## Project parameters

2 arithmetic registers (A, B).

1 output register, 1 program counter (PC).

16B ROM (Program Memory) with DIP switch input.

1 input port, 2 output ports, 1 7-segment digital tube (0-15 can be displayed).

1HZ&10HZclocks& A clocks which can be adjusted (the accuracy is not high, for reference only).

TYPE-C POWER INPUT.

## Principle Analysis (Hardware Description)

## Precautions

Please conduct a power-on test before soldering the plug-in, and check the operation of the computer through the indicator light to prevent problems such as virtual soldering and chip damage, so as not to affect the follow-up experiment.

The soldering temperature should not be higher than 265°C, and the high-temperature soldering time should be reduced as much as possible to avoid damage to the components.

## Reference diagram


## Physical drawing

## Version updates
