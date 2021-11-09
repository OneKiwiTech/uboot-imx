# iMX ARM Trusted Firmware

## Download Toolchain
- `cd`
- `mkdir toolchain`
- `cd toolchain`
- `wget -c https://releases.linaro.org/components/toolchain/binaries/7.3-2018.05/aarch64-linux-gnu/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz`
- `tar -xvf gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz`

## Get U-Boot
- `git clone https://github.com/km-tek/uboot-imx.git`

## Get and Build the ARM Trusted firmware
- `git clone https://github.com/km-tek/imx-atf.git`
- `cd imx-atf`
- `export CROSS_COMPILE=~/toolchain/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-`
- `make PLAT=imx8mq bl31` (NXP i.MX8MQ EVK board)
- `cp build/imx8mq/release/bl31.bin $(uboot-imx)` (copy **bl31.bin** to uboot folder)

## Get the ddr and hdmi firmware
- `mkdir firmware-imx`
- `cd firmware-imx`
- `wget https://www.nxp.com/lgfiles/NMG/MAD/YOCTO/firmware-imx-8.13.bin`
- `chmod +x firmware-imx-8.13.bin`
- `./firmware-imx-8.13.bin`
- `cp firmware-imx-8.13/firmware/hdmi/cadence/signed_hdmi_imx8m.bin $(uboot-imx)`
- `cp firmware-imx-8.13/firmware/ddr/synopsys/lpddr4*.bin $(uboot-imx)`

## Build U-Boot
- `cd uboot-imx` (from Get U-Boot)
- `export ARCH=arm64`
- `export CROSS_COMPILE=~/toolchain/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-` or (*export CROSS_COMPILE=aarch64-poky-linux-*)
- `make distclean`
- `make imx8mq_evk_defconfig`
- `make flash.bin`

- Burn the **flash.bin** to microSD card from (any) Linux Device (offset 33KB):
```bash
    sudo dd if=flash.bin of=/dev/sd[x] bs=1024 seek=33 conv=notrunc
```

- Burn the **flash.bin** to eMMC from Linux:
```bash
    # Data Partition
    sudo dd if=flash.bin of=/dev/mmcblk0 bs=1024 seek=33

    # Boot0
    echo 0 | sudo tee /sys/block/mmcblk0boot0/force_ro
    sudo dd if=flash.bin of=/dev/mmcblk0boot0 bs=1024 seek=33

    # Boot1
    echo 0 | sudo tee /sys/block/mmcblk0boot1/force_ro
    sudo dd if=flash.bin of=/dev/mmcblk0boot1
```

## Reference
- https://developer.solid-run.com/knowledge-base/i-mx8m-atf-u-boot-and-linux-kernel/
- https://github.com/km-tek/uboot-imx/blob/lf_v2021.04/doc/board/freescale/imx8mq_evk.rst

# How to configure U-Boot for your board (i.MX8)

### 1. Add your board device tree: **arch/arm/dts/\<board>.dts** and **\<board>-u-boot.dtsi**.
- For example, add **arch/arm/dts/imx8mm-demo.dts** and **arch/arm/dts/imx8mm-demo-u-boot.dtsi**.

### 2. Update arch/arm/dts/Makefile to compile your device tree
- For example i.MX8:
```
dtb-$(CONFIG_ARCH_IMX8M) += \
 	<board>.dtb \
 	imx8mm-demo.dtb
```
### 3. Create your own board support directory: **board/\<vendor>/\<board>**.
- For example, add folder **board/kmt/imx8mm-demo**.

### 4. Add TARGET_\<VENDOR>_\<BOARD> in Kconfig
- For example i.MX8, update **arch/arm/mach-imx/imx8m/Kconfig**.

### 5. Create your board defconfig: configs/\<board>_defconfig.
- For example, add **configs/imx8mm_demo_defconfig**.

### 6. Add your configuration file : include/configs/\<board>.h.
- For example, add **include/configs/imx8mm-demo.h**.

# Build Maaxboard Mini

## Get U-Boot
- `mkdir maaxboard-uboot`
- `cd maaxboard-uboot`
- `git clone https://github.com/km-tek/uboot-imx.git -b lf_v2021.04`

## Get U-Boot
- `cd maaxboard-uboot`
- `wget https://www.nxp.com/lgfiles/NMG/MAD/YOCTO/firmware-imx-8.13.bin`
- `chmod +x firmware-imx-8.13.bin`
- `./firmware-imx-8.13.bin`
- `cp firmware-imx-8.13/firmware/ddr/synopsys/ddr4*.bin uboot-imx` (copy to uboot folder)

## Get and Build the ARM Trusted firmware
- `cd maaxboard-uboot`
- `git clone https://github.com/km-tek/imx-atf.gi -b lf_v2.4`
- `cd imx-atf`
- `export CROSS_COMPILE=~/toolchain/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-`
- `make PLAT=imx8mm bl31` (NXP i.MX8M Mini)
- `cp build/imx8mm/release/bl31.bin ../uboot-imx` (copy **bl31.bin** to uboot folder)

## Build U-Boot
- `cd uboot-imx` (from Get U-Boot)
- `export ARCH=arm64`
- `export CROSS_COMPILE=~/toolchain/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-` or (*export CROSS_COMPILE=aarch64-poky-linux-*)
- `export ATF_LOAD_ADDR=0x920000`
- `make distclean`
- `make imx8mm_maaxboard_defconfig`
- `make flash.bin`

- Burn the **flash.bin** to microSD card from (any) Linux Device (offset 33KB):
```bash
    sudo dd if=flash.bin of=/dev/sd[x] bs=1024 seek=33 conv=notrunc
```
- For example : `sudo dd if=flash.bin of=/dev/mmcblk0 bs=1024 seek=33 conv=notrunc`