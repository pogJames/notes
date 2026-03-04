# DEVELOPMENT SET-UP

### Attach device to WSL
1. open powershell w/ administrator privilege
> first time `winget install ubsipd`
2. find device `usbipd list`
3. share/bind device `usbipd bind --busid <BUSID>`
4. attach to WSL `usbipd attach --wsl --busid <BUSID>`
> check `ls -l /dev/tty*` -> device should appear as /dev/ttyUSB0
5. detach from WSL `usbipd detach --busid <BUSID>`
6. unshare/unbind if needed `usbipd unbind --busid <BUSID>`
> [!NOTE]
> you might need to add user to dialout group for read/write permissions:\
> `sudo usermod -a -G dialout <USER>`, then close and reopen WSL
7. set port latency to 1 ms `echo 1 | sudo tee /sys/bus/usb-serial/devices/ttyUSB0/latency_timer`
