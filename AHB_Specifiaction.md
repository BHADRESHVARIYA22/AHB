# **1. Introduction**
## **About AHB Protocol**  
  - AHB stands for Advanced High Performance Bus  
  -  AMBA AHB is a bus interface suitable for high-performance synthesizable designs.  
  -  It defines the interface between components, such as Managers, interconnects, and Subordinates.  
  -  AMBA AHB implements the features required for high-performance, high clock frequency systems including:  
    - Burst transfers  
    - Single clock-edge operation  
    - Non-tristate implementation  
    - Configurable data bus widths  
    - Configurable address bus widths
## The main component types of an AHB system are described in:  
  - Manager, Subordinate and Interconnect  
  - ### 1.Manager  
    - A Manager provides address and control information to initiate read and write operations  
      ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/c16618b1-54e5-4054-b1c3-c53a2ddc1306)  
  - ### 2.Subordinate  
    - A Subordinate responds to transfers initiated by Managers in the system. The Subordinate uses the HSELx select signal from the decoder to control when it responds to a bus transfer.  
    - The Subordinate signals back to the Manager:
      - The completion or extension of the bus transfer.  
      - The success or failure of the bus transfer.  
        ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/5cf0ca49-eb6e-4ad2-8748-d40ea67a4def)
  - ### 3. Interconnect  
    - An interconnect component provides the connection between Managers and Subordinates in a system.  
    - A single Manager system only requires the use of a Decoder and Multiplexor, as described in the following sections.  
    - A multi-Manager system requires the use of an interconnect that provides arbitration and the routing of signals from different Managers to the appropriate Subordinates.  
    - This routing is required for address, control, and write data signaling.  
    - Further details of the different approaches used for multi-Manager systems, such as single layer or multi-layer interconnects, are not provided within this specification.  
    - See Multi-layer AHB Technical Overview (ARM DVI 0045) for more information about implementing a multi-layer AHB-Lite interconnect.  
    - #### 3.1 Decoder  
      - This component decodes the address of each transfer and provides a select signal for the Subordinate that is involved in the transfer.  
      - It also provides a control signal to the multiplexor.A single centralized decoder is required in all implementations that use two or more Subordinates.  
    - #### 3.2 Multiplexor  
    - A Subordinate-to-Manager multiplexor is required to multiplex the read data bus and response signals from the Subordinates to the Manager.  
    - The decoder provides control for the multiplexor.   
    - A single centralized multiplexor is required in all implementations that use two or more Subordinates.
## Operation  
  - The Manager starts a transfer by driving the address and control signals.  
  - These signals provide information about the address, direction, width of the transfer, and indicate if the transfer forms part of a burst. Transfers can be:
    - Single
    - Incrementing bursts that do not wrap at address boundaries.
    - Wrapping bursts that wrap at particular address boundaries.  
  - The write data bus moves data from the Manager to a Subordinate, and the read data bus moves data from a Subordinate to the Manager.
  - Every transfer consists of:  
    - **Address phase** One address and control cycle.
    - **Data phase** One or more cycles for the data.
  - A Subordinate cannot request that the address phase is extended and therefore all Subordinates must be capable of sampling the address during this time.  
  - However, a Subordinate can request that the Manager extends the data phase by using HREADY. This signal, when LOW, causes wait states to be inserted into the transfer and enables the Subordinate to have extra time to provide or sample data.
  - The Subordinate uses HRESP to indicate the success or failure of a transfer.

# **2. Signal Descriptions**
