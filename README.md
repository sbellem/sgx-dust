# sgx-dust
Notes on working with SGX

## SGX Driver

### Installation from pre-built binary
Brief steps to install the out-of-tree driver on Ubuntu 20.04, for linux-sgx 2.14. See more details at https://download.01.org/intel-sgx/sgx-linux/2.14/docs/Intel_SGX_SW_Installation_Guide_for_Linux.pdf.

Download:

```bash
wget https://download.01.org/intel-sgx/sgx-linux/2.14/distro/ubuntu20.04-server/sgx_linux_x64_driver_2.11.0_2d2b795.bin
```

Set permissions:

```bash
chmod 777 sgx_linux_x64_driver_*.bin 
```

Install:

```bash
sudo ./sgx_linux_x64_driver_*.bin
```

Check:

``` bash
ls -la /dev/isgx
```

### Installation from source
The instructions here are for ubuntu. See https://github.com/intel/linux-sgx-driver#build-and-install-the-intelr-sgx-driver for other systems (centOS, RHEL or Fedora).

Verify that the kernel headers for the active kernel version (`uname -r`) are installed:

```bash
dpkg-query -s linux-headers-$(uname -r)
```
If the headers are missing, installed with:

```bash
sudo apt-get install linux-headers-$(uname -r)
```

Clone the linux-sgx-driver repo:

```bash
git clone https://github.com/intel/linux-sgx-driver.git
```
```bash
cd linux-sgx-driver
```
```bash
git checkout -b sgx_driver_2.14 sgx_diver_2.14
```

Build the driver

```
make
```

Install:

```bash
sudo mkdir -p "/lib/modules/"`uname -r`"/kernel/drivers/intel/sgx"
```
```bash
sudo cp isgx.ko "/lib/modules/"`uname -r`"/kernel/drivers/intel/sgx"
```
```bash
sudo sh -c "cat /etc/modules | grep -Fxq isgx || echo isgx >> /etc/modules"
```
```bash
sudo depmod --verbose
```
```bash
sudo modprobe --verbose isgx
```


#### Removal
https://github.com/intel/linux-sgx-driver#uninstall-the-intelr-sgx-driver

```bash
sudo modprobe --remove isgx \
&& sudo rm -rf "/lib/modules/"`uname -r`"/kernel/drivers/intel/sgx" \
&& sudo depmod \
&& sudo sed -i '/^isgx$/d' /etc/modules
```

### Troubleshooting
can't install `/dev/isgx` 

imaginary conversation with IT support:

```text
roy: Have you tried turning it off and on again?
dev: Yes! Multiple times! I am from the future; the problem is with SGX.
roy: Have you tried disabling it and enabling it again?
dev: In the BIOS?
```

dev:

```text
turn computer off
turn computer on
enter BIOS
disable SGX
F10
turn computer off
enter BIOS
enable SGX
F10
```

The above may not work at all. So below are some things that you may want to check.

**Check whether multiple kernels are installed**

```bash
lsmod | grep -i sgx
```

**`dmesg`**
Using the command `dmesg` can be useful when troubleshooting the installation, e.g.:

```bash
dmesg | grep -i sgx
```

If you see something like:

```bash
[  487.928127] isgx: loading out-of-tree module taints kernel.
[  487.928182] isgx: module verification failed: signature and/or required key missing - tainting kernel
[  487.928740] intel_sgx: Intel SGX Driver v2.11.0
[  487.928761] intel_sgx INT0E0C:00: EPC bank 0x50200000-0x55f80000
[  487.930749] intel_sgx:  can not reset SGX LE public key hash MSRs
[  487.930840] intel_sgx: second initialization call skipped
```

then ... good luck!

Check if there are multiple `isgx.ko` files under `/lib/modules/*/kernel/drivers/intel/sgx`, e.g.:

```bash
ls -l /lib/modules/*/kernel/drivers/intel/sgx
```

If not, then I don't know, as I am also stuck on the same problem. If you find an explanation, you are most welcome to share it! Open an issue or pull request! :pray:


#### Some perhaps useful links
https://github.com/intel/linux-sgx-driver/issues/68 - particularly comment https://github.com/intel/linux-sgx-driver/issues/68#issuecomment-513032868

https://community.intel.com/t5/Intel-Software-Guard-Extensions/Unable-to-uninstall-SGX-Driver/td-p/1186173
