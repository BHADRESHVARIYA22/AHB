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
   - **HRDATA** Multiplexor DATA_WIDTH  
     - During read operations, the read data bus transfers data from the selected Subordinate to the multiplexor.  
     - The multiplexor then transfers the data to the Manager.  
     - DATA_WIDTH can be 8, 16, 32, 64, 128, 256, 512, or 1024. However, any value smaller than 32 or larger than 256 is not recommended.  
   - **HREADYOUT** Multiplexor 1  
     - When HIGH, the HREADYOUT signal indicates that a transfer has finished on the bus.   
     - This signal can be driven LOW to extend a transfer.  
   - **HRESP** Multiplexor 1  
     - The transfer response provides the Manager with additional information on the status of a transfer.  
     - When LOW, the HRESP signal indicates that the transfer status is OKAY.  
     - When HIGH, the HRESP signal indicates that the transfer status is ERROR.  
   - **HEXOKAY** Multiplexor 1   
     - Exclusive Okay. Indicates the success or failure of an Exclusive Transfer.  
     - This signal is supported if the AHB5 Exclusive_Transfers property is True.  
 ## 2.4 Decoder signals    
   - **HSELxa** Subordinate 1  
     - Each Subordinate has its own select signal HSELx and this signal indicates that the current transfer is intended for the selected Subordinate.  
     - When the Subordinate is initially selected, it must also monitor the status of HREADY to ensure that the previous bus transfer has completed, before it responds to the current transfer.   
     - When a Subordinate is selected for a non-IDLE transfer, HSELx must be asserted in the same cycle as the address and other control signals.   
     - HSELx can be asserted or deasserted for IDLE transfers.
       
  - The letter x used in HSELx must be changed to a unique identifier for each Subordinate in a system. For example, HSEL_S1, HSEL_S2, and HSEL_Memory.  
  - Usually the decoder also provides the multiplexor with the HSELx signals, or a signal or bus derived from the HSELx signals, to enable the multiplexor to route the appropriate signals, from the selected Subordinate to the Manager.  
  - It is important that these additional multiplexor control signals are retimed to the data phase.  
 ## 2.5 Multiplexor signals   
  - **HRDATA** Manager DATA_WIDTH  
    - Read data bus, selected by the decoder  
  - **HREADY** Manager and Subordinate 1  
    - When HIGH, the HREADY signal indicates to the Manager and all Subordinates, that the previous transfer is complete.  
  - **HRESP** Manager 1  
    - Transfer response, selected by the decoder   
  - **HEXOKAY** Manager 1  
    - Exclusive okay, selected by the decoder  
      
  - Because the HRDATA, HRESP, and HEXOKAY signals pass through the multiplexor and retain the same signal naming, the full signal descriptions for these three signals are provided in **2.3 Subordinate signals**.
# 3.Transfer  
## 3.1 Basic Transfers  
  - A transfer consists of two phases:  
    - **Address** Lasts for a single HCLK cycle unless it is extended by the previous bus transfer.  
    - **Data** Might require several HCLK cycles. Use the HREADY signal to control the number of clock cycles required to complete the transfer.  
  - **HWRITE** controls the direction of data transfer to or from the Manager. Therefore, when:
    - HWRITE is **HIGH**, it indicates a **write** transfer and the Manager broadcasts data on the write data bus,HWDATA.
    - HWRITE is **LOW**, a **read** transfer is performed, and the Subordinate must generate the data on the read data bus, HRDATA.
  - The simplest transfer is one with no wait states, so the transfer consists of one address cycle and one data cycle.
  - ### **Transfer with no wait states**
  - Figure 3-1 shows a simple read transfer and Figure 3-2 shows a simple write transfer.

  - ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/0200f3d7-ff2c-49f0-90a9-ea808b79a1f5)  
  - In a simple transfer with no wait states:  
    - The Manager drives the address and control signals onto the bus after the rising edge of HCLK.
    - The Subordinate then samples the address and control information on the next rising edge of HCLK.
    - After the Subordinate has sampled the address and control it can start to drive the appropriate HREADYOUT response. This response is sampled by the Manager on the third rising edge of HCLK.
  - The address phase of any transfer occurs during the data phase of the previous transfer. This overlapping of address and data is fundamental to the pipelined nature of the bus and enables high-performance operation while still providing adequate time for a Subordinate to provide the response to a transfer
    
  - ### **Transfer with Wait State**  
  - A Subordinate can insert wait states into any transfer to enable additional time for completion. Each Subordinate has an HREADYOUT signal that it drives during the data phase of a transfer. The interconnect is responsible for combining the HREADYOUT signals from all Subordinates to generate a single HREADY signal that is used to
control the overall progress.
    ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/cd604d12-6def-45fe-a06e-716c0d5a75f7)
    -  ### **Multiple transfers**  
    ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/4e238040-e3f5-49a8-94d3-65356cdefccd)  
    - In Figure 3-5:  
    - The transfers to addresses A and C are zero wait state  
    - The transfer to address B is one wait state  
    - Extending the data phase of the transfer to address B has the effect of extending the address phase of the transfer to address C.  
## 3.2 Transfer Type   
  - lists the transfers that can be classified into one of four types, as controlled by HTRANS[1:0].
     ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/69a212aa-0ea6-4017-b781-fd9c8cd39c11)
  - ### **Use of the NONSEQ, BUSY, and SEQ transfer types**
    ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/1f66dd6c-849e-457d-8b83-04b9f56ac2e1)

  - **T0-T1** : The 4-beat read starts with a NONSEQ transfer.  
  - **T1-T2** The Manager is unable to perform the second beat and inserts a BUSY transfer to delay the start of the second beat. The Subordinate provides the read data for the first beat.  
  - **T2-T3** The Manager is now ready to start the second beat, so a SEQ transfer is signaled. The Manager ignores any data that the Subordinate provides on the read data bus.  
  - **T3-T4** The Manager performs the third beat.The Subordinate provides the read data for the second beat.  
  - **T4-T5** The Manager performs the last beat. The Subordinate is unable to complete the transfer and uses HREADYOUT to insert a single wait state.  
  - **T5-T6** The Subordinate provides the read data for the third beat.  
  - **T6-T7** The Subordinate provides the read data for the last beat.  

    



    

  
 
    
