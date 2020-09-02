# RISC-V UEFI EDK2 Port Documentations

This repository contains documents related to UEFI EDK2 implementation of
RISC-V port, which includes the edk2 RISC-V build guidance, design
specification, UEFI protocol, PPI and etc.

## TianoCore EDK2 Repositories for RISC-V
Below are the repositories of RISC-V EDK2 development.

| Repo | Branch | URI | Verified Commit ID |
|---|---|---|---|
|**EDK2**|master|https://github.com/riscv/riscv-edk2/tree/master|latest|
|**EDK2 Platforms**|master|https://github.com/riscv/riscv-edk2-platforms/tree/master|latest|

Below are the upstream repositories with RISC-V port.

| Repo | Branch | URI | Verified Commit ID |
|---|---|---|---|
|**EDK2**|master|https://github.com/tianocore/edk2/tree/master|latest|
|**EDK2 Platforms**|master|https://github.com/tianocore/edk2-platforms/tree/master|latest|

## OpenSBI Integration
Below is the status of integrating the OpenSBI library in RISC-V EDK2 port.

| OpenSBI Tag version on EDK2 | Stable Tag | Status |
|---|---|---|
|v0.8|v0.8|**Verified @937caee0833115f69d697ca190001ba0aa5c7368**|

## Supported RISC-V Platforms 
Below table lists the current supported RISC-V platforms with UEFI edk2 RISC-V port,

| Vendor| Platform Name| Execution Environment|Status|
|---|---|---|---|
|**SiFive**|Freedom U500 VC707 FPGA|On board|**Done**|
|**SiFive**|HiFive Unleashed (Freedom U540 SoC)|QEMU|**Done**|
|**SiFive**|HiFive Unleashed (Freedom U540 SoC)|On board|**Done**|
|**Andes**|AX25 + AE350 FPGA|On board|WIP|

## Boot Status
Below table lists the current status of booting RISC-V platform,

| Platform Name| EFI Shell|Boot loader|Linux Kernel|
|---|---|---|---|
|Freedom U500 VC707 FPGA|**Yes**|N/A|N/A|
|HiFive Unleashed (QEMU)|**Yes**|GRUB2 WIP, Systemd-boot WIP|**Yes**|
|HiFive Unleashed |**Yes**|N/A|**Yes**|
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

## EDK2 RISC-V Port of Booting Linux Kernel
We currently can boot Linux kernel on SiFive Hifive Unleashed (U540) and its
QEMU emulation.
This is almost possible with what is upstream in EDK2, however we don't have an
SPI SD card driver or any disk in QEMU at all, yet. Therefore additional
patches are needed to load the kernel and its initrd into a ramdisk and start
it from there.
Kernel changes for supporting EFISTUB and EFI runtime services are currently
under review, hopefully landing in 5.10.

- [edk2](https://github.com/riscv/riscv-edk2/tree/devel-boot-kernel)
- [edk2-platforms](https://github.com/riscv/riscv-edk2-platforms/tree/devel-boot-kernel)
- [linux](https://github.com/atishp04/linux/tree/uefi_riscv_5.10_v5)
- [GNU toolchain](https://github.com/riscv/riscv-uefi-edk2-docs/raw/master/gcc-riscv-edk2-ci-toolchain/gcc-riscv-9.2.0-2020.04-x86_64_riscv64-unknown-gnu.tar.xz)

### Building an running
1. Building the kernel and device tree

```
cd linux
ARCH=riscv make defconfig
ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- make -j8
ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- make dtbs

cp arch/riscv/bootImage ../linux-riscv64.efi
cp arch/riscv/boot/dts/sifive/hifive-unleashed-a00.dtb ../U540.dtb
```

3. Create ramdisk image for EDK2 to mount

```
mkfs.msdos -C linux.iso 64000
sudo losetup /dev/loop0 linux.iso
sudo mount /dev/loop0 /mnt

# Copy the kernel that you built previously
sudo cp linux-riscv64.efi /mnt
sudo umount /mnt
sudo losetup -d /dev/loop0

# Copy ramdisk and DTB into your clone of edk2-platforms
cp linux.iso edk2-platforms/Silicon/RISC-V/ProcessorPkg/Universal/EspRamdisk/linux.iso
cp U540.dtb edk2-platforms/Platform/RISC-V/PlatformPkg/Universal/Sec/Riscv64/U540.dtb
```

4. Build EDK2

```
export GCC5_RISCV64_PREFIX=riscv-gnu-toolchain-binaries/bin/riscv64-unknown-elf-
export PACKAGES_PATH=edk2:edk2-platform-riscv
export EDK_TOOLS_PATH=edk2/BaseTools

source edk2/edksetup.sh
make -C edk2/BaseTools clean
make -C edk2/BaseTools
make -C edk2/BaseTools/Source/C

source edk2/edksetup.sh BaseTools

build -a RISCV64 -p Platform/SiFive/U5SeriesPkg/FreedomU540HiFiveUnleashedBoard/U540.dsc -t GCC5
cp -v /Build/FreedomU540HiFiveUnleashed/DEBUG_GCC5/FV/U540.fd ../U540.fd
```

5a. Boot on QEMU

```
qemu-system-riscv64 -cpu sifive-u54 -machine sifive_u \
  -bios U540.fd \
  -m 2048 -nographic -smp cpus=5,maxcpus=5
```

5b. Boot on Hifive Unleashed

```
# Get fedora image
wget http://fedora.riscv.rocks/kojifiles/work/tasks/944/320944/Fedora-Developer-Rawhide-20200108.n.0-sda.raw.xz

# Extract the 2nd partition of the image (where /dev/sda is the SD card)
guestfish add Fedora-Developer-Rawhide-20200108.n.0-sda.raw : run : download /dev/sda2 Fedora-Developer-Rawhide-20200108.n.0-sda2.raw

# We need more than 64MB in the first partition as the EDK2 firmware may be
# bigger. Adjust sgdisk based on the EDK2 firmware size.
sudo sgdisk --clear \
  --new=1:2048:270332 --change-name=1:bootloader --typecode=1:2E54B353-1271-4842-806F-E436D6AF6985 \
  --new=2:292864:     --change-name=2:root       --typecode=2:0FC63DAF-8483-4772-8E79-3D69D8477DE4 /dev/sda

# Copy the EDK2 firmware binary to first partition of the sdcard
sudo dd if=U540.fd of=/dev/sda1 bs=1M

# Copy the Fedora rootfs to the 2nd partition of the sdcard
sudo dd if=Fedora-Developer-Rawhide-20200108.n.0-sda2.raw of=/dev/sda2 bs=1M
```

6. Start linux

Once you reach the UEFI shell, you can mount the ramdisk and start linux

```
embeddedramdisk 4f2f3d7b-35ef-411b-9d26-e76ecacbaf8b
map -r
fs0:

# On HifiveUnleashed
linux-riscv64.efi root=/dev/mmcblk0p2 rootwait console=ttySIF0 earlycon
# On QEMU
linux-riscv64.efi root=/dev/ram rootwait console=ttySIF0 earlycon
```

## Contributors
- Abner Chang <abner.chang@hpe.com>
- Gilbert Chen <gilbert.chen@hpe.com>
- Leif Lindholm <leif@nuviainc.com>
- Daniel Schaefer <daniel.schaefer@hpe.com>
- Atish Patra <Atish.Patra@wdc.com>
