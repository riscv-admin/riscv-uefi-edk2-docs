# RISC-V UEFI EDK2 Port Documentations

This repository contains documents related to UEFI EDK2 implementation of
RISC-V port, which includes the edk2 RISC-V build guidance, design
specification, UEFI protocol, PPI and etc.

## TianoCore EDK2 Repositories for RISC-V
Below are the repositories of RISC-V EDK2 development.

| Repo | Branch | URI | Verified Commit ID |
|---|---|---|---|
|**EDK2**|master|https://github.com/riscv/riscv-edk2/tree/master|ca407c7 _(latest_)|
|**EDK2 Platforms**|staging|https://github.com/riscv/riscv-edk2-platforms/tree/staging|1b9a7ba _(latest)_|

Below are the upstream repositories with RISC-V port.

| Repo | Branch | URI | Verified Commit ID |
|---|---|---|---|
|**EDK2**|master|https://github.com/tianocore/edk2/tree/master|ca407c7|
|**EDK2 Platforms**|master|https://github.com/tianocore/edk2-platforms/tree/master|**not merged yet**|

## OpenSBI Integration
Below is the status of integrating the OpenSBI library in RISC-V EDK2 port.

| OpenSBI Tag version on EDK2 | Stable Tag | Status |
|---|---|---|
|v0.7|v0.7|WIP|
|v0.6|v0.6|**Verified**|

## Supported RISC-V Platforms 
Below table lists the current supported RISC-V platforms with UEFI edk2 RISC-V port,

| Vendor| Platform Name| Execution Environment|Status|
|---|---|---|---|
|**SiFive**|Freedom U500 VC707 FPGA|On board|**Done**|
|**SiFive**|HiFive Unleashed (Freedom U540 SoC)|QEMU|**Done**|
|**SiFive**|HiFive Unleashed (Freedom U540 SoC)|On board|WIP|
|**Andes**|AX25 + AE350 FPGA|On board|WIP|

## Boot Status
Below table lists the current status of booting RISC-V platform,

| Platform Name| EFI Shell|Boot loader|OS|
|---|---|---|---|
|Freedom U500 VC707 FPGA|**Yes**|N/A|N/A|
|HiFive Unleashed (QEMU)|**Yes**|GRUB2 WIP, Systemd-boot WIP|Linux WIP|
|HiFive Unleashed |N/A|N/A|N/A|
|AX25 + AE350 FPGA|N/A|N/A|N/A|

## RISC-V EDK2 Port Introduction and Build Guidance
Please refer to document from below link,
https://github.com/riscv/riscv-uefi-edk2-docs/blob/master/edk2/riscv-edk2-port.adoc

## RISC-V GCC Toolchain Binary for EDK2 Continuous Integration
The folder [gcc-riscv-edk2-ci-toolchain](https://github.com/riscv/riscv-uefi-edk2-docs/tree/master/gcc-riscv-edk2-ci-toolchain)  contains the toolchain binary for EDK2 CI testing on RISC-V architecture.
The toolchain binary is the external dependence to EDK2 CI and will be retrieved when running continuous integration testing.

## EDK2 RISC-V Firmware Image on QEMU
We currently only verified SiFIve U540 EDK2 firmware image on QEMU (4.1.50). Below is the suggested QEMU options against to qemu-system-riscv64,

    ./qemu-system-riscv64 -cpu sifive-u54 -machine sifive_u -bios u540.fd -m 4096 -smp cpus=5,maxcpus=5

- You may want to add more options for your serial log output.
- Memory size assigned to QEMU less than 1024K results in system hangs at PEI phase.

## Contributors
- Abner Chang <abner.chang@hpe.com>
- Gilbert Chen <gilbert.chen@hpe.com>
- Leif Lindholm <leif@nuviainc.com>
- Daniel Schaefer <daniel.schaefer@hpe.com>
