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
  - It contains the following sections:  
    - Global signals  
    - Manager signals  
    - Subordinate signals  
    - Decoder signals  
    - Multiplexor signals  
  - All AHB-Lite and AHB5 signals are prefixed with the letter H to differentiate them from other similarly named signals in a system design.  
  - Some signals can have a variety of widths, described by a property. These properties can be used to describe a fixed configuration or to control the configuration of an interface.
  - Signals with a width of 0 are not present on the interface.
 ## 2.1 Global signals   
   - **HCLK** Clock source 1  
     - The bus clock times all bus transfers. All signal timings are related to the rising edge of HCLK.
   - **HRESETn** Reset controller 1
     - The bus reset signal is active LOW and resets the system and the bus. This is the only active LOW signal.  
 ## 2.2 Manager signals    
   - lists the protocol signals generated by a Manager. A signal width property defines the width of a signal.  
   - For example, ADDR_WIDTH defines the width of HADDR. The properties can be used to describe a fixed configuration or to control the configuration of an interface.  
   - **HADDR** Subordinate and decoder ADDR_WIDTH  
     - The byte address of the transfer. ADDR_WIDTH is recommended to be between 10 and 64.   
     - In the issues A and B of this specification, the address width was fixed at 32.  
   - **HBURST** Subordinate HBURST_WIDTH  
     - Indicates how many transfers are in the burst and how the address increments. HBURST_WIDTH must be 0 or 3.  
   - **HMASTLOCK** Subordinate 1  
     - Indicates that the current transfer is part of a locked sequence.    
     - It has the same timing as the address and control signals.  
   - **HPROT** Subordinate HPROT_WIDTH  
     - Protection control signal, which provides information about the access type.  
     - HPROT_WIDTH must be 0, 4, or 7, depending on the Extended_Memory_Types property.  
   - **HSIZE** Subordinate 3  
     - Indicates the size of the transfer.  
   - **HNONSEC** Subordinate and decoder 1  
     - Indicates whether the transfer is Non-secure or Secure.  
     - This signal is supported if the AHB5 Secure_Transfers property is True.    
   - **HEXCL** Exclusive Access Monitor 1   
     - Indicates whether the transfer is part of an   Exclusive Access sequence.
     - This signal is supported if the AHB5 Exclusive_Transfers property is True.   
   - **HMASTER** Exclusive Access Monitor and Subordinate HMASTER_WIDTH  
     - Manager identifier. Generated by a Manager if it has multiple Exclusive capable threads.  
     - Modified by an interconnect to ensure each Manager is uniquely identified.  
     - This signal is supported if the AHB5 Exclusive_Transfers property is True.
     - HMASTER_WIDTH is recommended to be between 0 and 8.
   - **HTRANS** Subordinate 2  
     - Indicates the transfer type. This can be:  
       - IDLE  
       - BUSY  
       - NONSEQUENTIAL  
       - SEQUENTIAL.  
   - **HWDATA** Subordinate DATA_WIDTH  
     - Transfers data from the Manager to the Subordinates during write operations.  
     - DATA_WIDTH can be 8, 16, 32, 64, 128, 256,512, or 1024. However, any value smaller than 32 or larger than 256 is not recommended.  
   - **HWSTRB** Subordinate DATA_WIDTH/8  
     - Write strobes. Deasserted to indicate when active write data byte lanes do not contain valid data.  
     - There is 1 bit for each 8 bits of HWDATA.  
     - HWSTRB[n] corresponds to HWDATA[(8n)+7:(8n)].  
     - HWSTRB is a data-phase signal and has the same validity rules as HWDATA.  
   - **HWRITE** Subordinate 1  
     - Indicates the transfer direction. When HIGH this signal indicates a write transfer and when LOW a read transfer.  
     - It has the same timing as the address signals, however, it must remain constant throughout a burst transfer.  
 ## 2.3 Subordinate signals  
 ## 2.4 Decoder signals  
 ## 2.5 Multiplexor signals  
 
    
