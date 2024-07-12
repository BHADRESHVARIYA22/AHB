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
## 3.3 Locked transfers   
  - If the Manager requires locked accesses, then it must also assert the HMASTLOCK signal.
  - This signal indicates to any Subordinate that the current transfer sequence is indivisible and must therefore be processed before any other transfers are processed.
  - Typically the locked transfer is used to maintain the integrity of a semaphore, by ensuring that the Subordinate does not perform other operations between the read and write phases of a microprocessor SWP instruction.
  - In a locked sequence:
    - The bus is locked after a cycle with HMASTLOCK asserted, HSEL asserted if present, and HREADY is HIGH.
    - The bus is unlocked after a cycle with HMASTLOCK deasserted and HREADY is HIGH.
    - After a locked transfer, it is recommended that the Manager inserts an IDLE transfer.
    ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/4e061dc8-10c5-424e-b2e9-2a485cc8c2fe)  

    - Most Subordinates have no requirement to implement HMASTLOCK because they are only capable of performing transfers in the order they are received.    
    - Subordinates that can be accessed by more than one Manager, for example, a Multi-Port Memory Controller (MPMC) must implement the HMASTLOCK signal.  
    - It is permitted for a Manager to assert HMASTLOCK for IDLE transfers at the beginning, in the middle, or at the end of a sequence of locked transfers.  
    - Using locked IDLE transfers at the start or end of a locked transfer sequence is permitted, but not recommended, as this behavior can adversely affect the arbitration of the system.  
    - It is also permitted, but not recommended, for a Manager to assert HMASTLOCK for a number of IDLE transfers and then deassert HMASTLOCK without performing a non-IDLE transfer.  
    - This behavior can adversely affect the arbitration of the system.  
    - It is required that all transfers in a locked sequence are to the same Subordinate address region.  
    - Note : The requirement to ensure that all transfers in a locked sequence are to the same Subordinate address region did not exist in Issue A of this specification. A legacy component must be verified to ensure that it does not exhibit this behavior.  
   
## 3.4 Transfer size    
  - HSIZE[2:0] indicates the size of a data transfer.  
  - The transfer size set by HSIZE must be less than or equal to the width of the data bus. For example, with a 32-bit data bus, HSIZE must only use the values 0b000, 0b001, or 0b010.  
  - The HSIZE signals have the same timing as the address bus. However, they must remain constant throughout a burst transfer.   
  - HSIZE in conjunction with HBURST determines the address boundary for wrapping bursts.
  - **Transfer size encoding**
  - HSIZE = 0 : 1 byte
  - HSIZE = 1 : 2 byte
  - HSIZE = 2 : 3 byte
  - HSIZE = 3 : 4 byte
  - HSIZE = 4 : 5 byte
  - HSIZE = 5 : 6 byte
  - HSIZE = 6 : 7 byte
  - HSIZE = 7 : 8 byte

## 3.5 Write strobes pending **************
Write strobes is an optional feature, which enables a Manager to issue a write that updates only a subset of active
write data bytes. The Write_Strobes property indicates if an interface supports write strobes.
True Write strobes are supported and HWSTRB is included on the interface.
False Write strobes are not supported and HWSTRB is not included on the interface.
Note
If Write_Strobes is not declared, it is considered as False.

### 3.6 Burst operation 
  #### **Lower Boundary and Upper Boundary**
  - **HSIZE** : size of transfer data 
      - tranfer data size = 2 ^ HSIZE
      - transfer data size (1 byte to 128 byte)
      - if data bus is 32 bit than HSIZE <= 2 :: 2 ^ 2 = 4byte * 8 = 32 bits
  - **HBRUST** : No of transfer in brust
  - No of Bytes = HBRUST * (2^ HSIZE)
  - Lower Boundary = Start_address - (start_Address % no_of_bytes)
  - Upper Boundary = lower Boundary + No of bytes

             
  - Bursts of 4, 8, and 16-beats, undefined length bursts, and single transfers are defined in this protocol.
  - It supports incrementing and wrapping bursts:
  - Incrementing bursts access sequential locations and the address of each transfer in the burst is an increment of the previous address.
  - Wrapping bursts wrap when they cross an address boundary. The address boundary is calculated as the product of the number of beats in a burst and the size of the transfer.
  - The number of beats are controlled by HBURST and the transfer size is controlled by HSIZE.
  - For example, a four-beat wrapping burst of word (4-byte) accesses wraps at 16-byte boundaries.
  - Therefore, if the start address of the burst is 0x34, then it consists of four transfers to addresses 0x34, 0x38, 0x3C, and 0x30.  
#### **lists the possible burst types**   
  ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/4eed1b38-6329-4ed4-8459-7ccbf0bc168b)

  - Managers must not attempt to start an incrementing burst that crosses a 1KB address boundary.
  - Managers can perform single transfers using either:
    - SINGLE transfer burst.
    - Undefined length burst that has a burst of length one.
  - The burst size indicates the number of beats in the burst and not the number of bytes transferred.  
  - Calculate the total amount of data transferred in a burst by multiplying the number of beats by the amount of data in each beat, as indicated by HSIZE[2:0].
  -  

### 3.6.3  Burst operation
  - #### write transfer using a four-beat wrapping burst, with a wait state added for the first transfer.
  - Lower and Upper Boundary Calculation
    - HSIZE = 2  :: single transfer size = 4 byte
    - total byte = single transfer size X brust length = 4 X 4 = 16 byte
    - Start Address = 38d = 56d
    - LB = 56 - (56/16) = 56 - 8 = **48d** = **30h**
    - UB = 48 + 16 = **64d** = **40h**
    - 1st Address = 48d  = 30h
    - 2nd Address = 52d (48 + 4) = 34h
    - 3rd Address = 56d (56 + 4) = 38h
    - 4th Address = 60d (56 + 4) = 3Ch
      
    ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/8cabc735-01e5-4cb0-b3a2-c751d00b0af0) 
  - the burst is a four-beat burst of word transfers, the address wraps at 16-byte boundaries, and the transfer to address 0x3C is followed by a transfer to address 0x30.  
  - #### read transfer using a four-beat incrementing burst, with a wait state added for the first transfer.
    - 1st Address = 56d   = 38h
    - 2nd Address = 60d (56 + 4) = 3Ch
    - 3rd Address = 64d (60 + 4) = 40h
    - 4th Address = 68d (64 + 4) = 44h
   ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/4faab50e-e242-4092-bc37-293887f30bb3)  
  - In this case, the address does not wrap at a 16-byte boundary and the address 0x3C is followed by a transfer to address 0x40.
  - #### read transfer using an eight-beat wrapping burst.
  - Lower and Upper Boundary Calculation
    - HSIZE = 2  :: single transfer size = 4 byte
    - total byte = single transfer size X brust length = 4 X 8 = 32 byte
    - Start Address = 34d = 52d
    - LB = 52 - (52/32) = 52 - 20 = **32d** = **20h**
    - UB = 48 + 16 = **64d** = **40h**
    - 1st Address = 32d  = 20h
    - 2nd Address = 36d (32 + 4) = 24h
    - 3rd Address = 40d (36 + 4) = 28h
    - 4th Address = 44d (40 + 4) = 2Ch
    - 5th Address = 48d (44 + 4) = 30h
    - 6th Address = 52d (48 + 4) = 34h
    - 7th Address = 56d (52 + 4) = 38h
    - 8th Address = 60d (56 + 4) = 3Ch
     
    ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/c47f61bb-95c6-4278-8218-848f16e19bc1)
  - the burst is an eight-beat burst of word transfers, the address wraps at 32-byte boundaries, and the transfer to address 0x3C is followed by a transfer to address 0x20.
  - ##### write transfer using an eight-beat incrementing burst.
  - HSIZE = 1 = 2 byte
    - 1st Address = 52d  = 34h
    - 2nd Address = 54d (52 + 2) = 36h
    - 3rd Address = 56d (54 + 2) = 38h
    - 4th Address = 58d (56 + 2) = 3Ah
    - 5th Address = 60d (58 + 2) = 3Ch
    - 6th Address = 62d (60 + 2) = 3Eh
    - 7th Address = 64d (62 + 2) = 40h
    - 8th Address = 68d (64 + 2) = 42h
    - 
    ![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/c366370e-3297-455c-a2ba-36db76b57091)
  - This burst uses 2byte transfers, therefore the addresses increase by two. Because the burst is incrementing, the addresses continue to increment beyond the 16-byte address boundary.
  - #### incrementing bursts of undefined length.
![image](https://github.com/BHADRESHVARIYA22/AHB/assets/87941725/41304547-9257-4c9b-8149-d251657d64c5)

  - The first burst is a write consisting of two 2byte transfers starting at address 0x20. These transfer addresses increment by two.
  - The second burst is a read consisting of three 4byte transfers starting at address 0x5C. These transfer addresses increment by four.








  
    



    

  
 
    
