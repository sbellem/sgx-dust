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
can't install /dev/isgx 

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

#### Some perhaps useful links
https://github.com/intel/linux-sgx-driver/issues/68 - particularly comment https://github.com/intel/linux-sgx-driver/issues/68#issuecomment-513032868

https://community.intel.com/t5/Intel-Software-Guard-Extensions/Unable-to-uninstall-SGX-Driver/td-p/1186173
