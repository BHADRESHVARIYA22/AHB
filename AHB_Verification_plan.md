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

    
