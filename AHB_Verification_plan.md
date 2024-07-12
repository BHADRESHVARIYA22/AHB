# 1. Naming Convention
- ##### All class name start with ahb_XXX
  - i.e . ahb_master_driver
- ##### All Handle name end with XXX_h
  - i.e.  ahb_master_driver m_drv_h;
- ##### Add Description of Every Entity using below Format
    -  ![image](https://github.com/user-attachments/assets/9a605278-0e2a-403a-8239-83d53a9b191f)

# 2. Directory & Files
  - RTL    :
    - ahb_design.sv
  - SIM    :
    - makefile
    - run.do
  - ENV    :
    - apb_enviroment.sv
    - apb_master_agent.sv
    - apb_slave_agent.sv
    - apb_master_driver.sv
    - apb_master_monitor.sv
    - apb_master_seqr.sv
    - apb_slave_driver.sv
    - apb_slave_monitor.sv
    - apb_slave_seqr.sv
    - apb_scoreboard.sv
  - TEST
    - apb_test.sv
    - apb_seqs.sv
  - TOP
    - apb_apb_tb.sv
# 3. Test bench Initial Architecture   
  ![image](https://github.com/user-attachments/assets/fd46f4d6-2500-4783-8239-1e68d9a0ceab)   

    
