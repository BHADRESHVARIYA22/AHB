# 1. Naming Convention
- ##### All class name start with ahb_XXX
  - i.e . ahb_master_driver
- ##### All Handle name end with XXX_h
  - i.e.  ahb_master_driver m_drv_h;
- ##### Add Description of Every Entity using below Format
    -  ![image](https://github.com/user-attachments/assets/9a605278-0e2a-403a-8239-83d53a9b191f)

# 2. Directory & Files
  - **RTL**    :
    - ahb_design.sv
  - **SIM**    :
    - makefile
    - run.do
  - **ENV**    :
    - ahb_env_config.svh
    - ahb_env_pkg.svh
    - ahb_env.sv
    - **AHB_MASTER_AGENT**
      - ahb_master_config.svh
      - ahb_master_define.svh
      - ahb_master_pkg.svh
      - ahb_master_inf.sv
      - ahb_master_seqs_item.sv
      - ahb_master_agent.sv
      - ahb_master_monitor.sv
      - ahb_master_driver.sv
      - ahb_master_seqr.sv
      - ahb_master_base_seqs.sv
      - ahb_master_uvc.sv
    - **AHB_SLAVE_AGENT**
      - ahb_slave_config.svh
      - ahb_slave_define.svh
      - ahb_slave_pkg.svh
      - ahb_slave_inf.sv
      - ahb_slave_seqs_item.sv
      - ahb_slave_agent.sv
      - ahb_slave_monitor.sv
      - ahb_slave_driver.sv
      - ahb_slave_seqr.sv
      - ahb_slave_base_seqs.sv
      - ahb_slave_uvc.sv
  - **TEST**
    - ahb_test_pkg.sv
    - ahb_base_test.sv
    - ahb_sanity_test.sv
    - ahb_master_sanity_test.sv
  - **TOP**
    - ahb_tb_top.sv
# 3. Test bench Initial Architecture   
  ![image](https://github.com/user-attachments/assets/fd46f4d6-2500-4783-8239-1e68d9a0ceab)    
# 4. Short Form use in comment Section
  - MP : Modport
  - CB : Clocking Block
  - T  : Task
  - F  : Function
# 5. Master Agenrt Entity
  - **Interface** 
    - ahb_master_inf : Interace for Master
    - master_drv_cb  : Clocking Block for Driver
    - master_mon_cb  : Clocking Block for Monitor
    - M_DRV_MP       : Modport For Driver
    - M_MON_MP       : Modport For Monitor

  - **define**
    - **enum use in Sequence Item**
      - enum bit {HREAD, HWRITE} trans_kind;
        - Used to set Direction of transfer  
      - enum bit [1:0] {SINGLE, INCR, WRAP} burst_type;
        - Used to 
      - enum bit [1:0] {OKAY, DECODE_ERROR, RETRY, SPLIT} resp_type;
        - Used to   
  - **Sequence Item**
    - ahb_master_seqs_items #(AW,DW,SEL_WD) : Master Sequnece Item parameterize Class
    - **Variables**
      - **kind_e** : trans_kind type Used to set Direction of transfer
        - HREAD  : Read Data
        - HWRITE : Write Data
      - **burst_type_e** : Set type of brust
        - SINGLE,WRAP,INCR
  -  **Handle**
  -  Enviroment Class
    -  ahb_env_config   env_config;
    -  ahb_master_uvc   master_uvc;
 -  UVC
   -   ahb_master_config master_config_h[];
   -   ahb_env_config env_config;
   -   ahb_master_agent #(32,64,4) master_agent_h[];
-  Master Agent
  -  ahb_master_driver    #(AW,DW,SEL_WD)  master_drv_h;     // Driver Hanle
  -  ahb_master_monitor   #(AW,DW,SEL_WD)  master_mon_h;     // Monitor Handle
  -  ahb_master_seqr      #(AW,DW,SEL_WD)  master_seqr_h;    // Sequencer Handle
  -  ahb_master_config                     master_config_h;  // Config DB Handle
  -  virtual ahb_master_inf#(AW,DW,SEL_WD) ahb_master_vif; // Virtual Inteface

    
