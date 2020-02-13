#RISC-V UEFI EDK2 Port Documentations
***

This repository contains documents related to UEFI edk2 implementation of RISC-V port, which includes the edk2 RISC-V build guidance, design specification, UEFI protocol, PPI and etc.

##TianoCore EDK2 Repositories for RISC-V
Below is the repositories of RISC-V EDK2 port on TianoCore GitHub. 

| Repo.| Branch|URI|Verified Commit ID|
|---|---|---|---|
|**EDK2 Staging**|RISC-V-V2|https://github.com/tianocore/edk2-staging/tree/RISC-V-V2|3d6deee _(The latest_)|
|**EDK2 Platforms**|devel-riscvplatforms|https://github.com/tianocore/edk2-platforms/tree/devel-riscvplatforms|6e9e115 _(The latest)_|

##OpenSBI Integration
Below is the status of integrating of OpenSBI library in RISC-V EDK2 port.  

| OpenSBI Tag version on EDK2| Stable Tag|Status|
|---|---|---|---|
|v0.5|v0.6 (Not released yet)|Manually update OpenSBI to commit ID d6fa7f95 until Tag v0.6 is released.|

##Supported RISC-V Platforms 
Below table lists the current supported RISC-V platforms with UEFI edk2 RISC-V port,

| Vendor| Platform Name| Execution Environment|Status|
|---|---|---|---|
|**SiFive**|Freedom U500 VC707 FPGA|On board|Done|
|**SiFive**|HiFive Unleashed (Freedom U540 SoC)|QEMU|Done|
|**SiFive**|HiFive Unleashed (Freedom U540 SoC)|On board|**WIP**|
|**Andes**|AX25 + AE350 FPGA|On board|**WIP**|

##RISC-V EDK2 Port Introduction and Build Guidance
Please refer to document from below link,
https://github.com/riscv/riscv-uefi-edk2-docs/edk2/riscv-edk2-port.adoc

##Contributors
**Chang, Abner** < abner.chang@hpe.com >
**Chen, Gilbert** < gilbert.chen@hpe.com >
**Schaefer, Daniel Helmut** < daniel.schaefer@hpe.com >

