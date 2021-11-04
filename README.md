# sgx-dust
Notes on working with SGX

## SGX Driver
Brief steps to install the out-of-tree driver on Ubuntu 20.04, for linux-sgx 2.14. See more details at https://download.01.org/intel-sgx/sgx-linux/2.14/docs/Intel_SGX_SW_Installation_Guide_for_Linux.pdf.

Download:

```console
wget https://download.01.org/intel-sgx/sgx-linux/2.14/distro/ubuntu20.04-server/sgx_linux_x64_driver_2.11.0_2d2b795.bin
```

Set permissions:

```console
chmod 777 sgx_linux_x64_driver_*.bin 
```

Install:

```console
sudo ./sgx_linux_x64_driver_*.bin
```

Check:

``` console
ls -la /dev/isgx
```
### Removal
https://github.com/intel/linux-sgx-driver#uninstall-the-intelr-sgx-driver

```bash
sudo /sbin/modprobe -r isgx \
&& sudo rm -rf "/lib/modules/"`uname -r`"/kernel/drivers/intel/sgx" \
&& sudo /sbin/depmod \
&& sudo /bin/sed -i '/^isgx$/d' /etc/modules
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

```
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
