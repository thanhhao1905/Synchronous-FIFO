### Synchronous FIFO

Welcome to the **Synchronous FIFO** project!

This repository contains a simple, yet robust, **First-In, First-Out (FIFO)** module designed to be fully synchronous. This means both write and read operations are controlled by the same clock signal, making it ideal for data buffering within a single clock domain.

This design is implemented in **Verilog/VHDL** and is a great learning resource or a ready-to-use component for your digital logic projects.

**Key Features:**
- **Single Clock Domain**: Simplifies the design and eliminates the need for complex clock domain crossing techniques.
- **Full-Featured Status Signals**: Includes `full`, `empty`, and `count` signals for easy integration and flow control in larger systems.
- **Modular and Parameterizable**: The depth and data width can be easily customized using parameters, making it highly reusable.

<img width="965" height="310" alt="image" src="https://github.com/user-attachments/assets/7e40ca3a-1ca5-47cc-9ab5-4079d25850c6" />
<img width="1840" height="184" alt="image" src="https://github.com/user-attachments/assets/c742a20d-ae95-49a0-b53f-521797993033" />

**Signals:**

**w_en**: write enable

**data_in**: write data

**full**: FIFO is full

**empty**: FIFO is empty

**r_en**: read enable

**data_out**: read data

**w_ptr**: write pointer

**r_ptr**: read pointer

