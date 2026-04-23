# Matrix-800 M33 RPMsg Cheatsheet

NXP i.MX 93 · Artila Matrix-800 · FreeRTOS M33 ↔ Linux A55

## Key values

| Setting | Value |
|---|---|
| `RPMSG_LITE_SHMEM_BASE` | `0xa4010000U` (vdev1vring0) |
| Channel name | `"rpmsg-tty"` |
| Kernel | `6.18.19-artila` (mainline `rpmsg_tty`, no `imx_rpmsg_tty`) |

## M33 firmware edits

From SDK example `boards/mcimx93evk/multicore_examples/rpmsg_lite_str_echo_rtos/linux_remote/`, in `main_remote.c`:

```c
my_rpmsg = rpmsg_lite_remote_init((void *)0xa4010000U, RPMSG_LITE_LINK_ID, RL_NO_FLAGS);
rpmsg_ns_announce(my_rpmsg, my_ept, "rpmsg-tty", RL_NS_CREATE);
```

## Build

```bash
export ARMGCC_DIR=/path/to/arm-gnu-toolchain
cd .../rpmsg_lite_str_echo_rtos/linux_remote/armgcc
./build_debug.sh
# → debug/rpmsg_lite_str_echo_rtos_linux_remote.elf
```

## Deploy + run

```bash
# Copy
scp debug/*.elf root@matrix800:/lib/firmware/rpmsg_lite_str_echo_rtos_remote_cm33.elf

# On Matrix-800
modprobe rpmsg_tty
echo "rpmsg_lite_str_echo_rtos_remote_cm33.elf" > /sys/class/remoteproc/remoteproc0/firmware
echo start > /sys/class/remoteproc/remoteproc0/state
```

## Dev cycle

```bash
# PC: edit → build → scp
./build_debug.sh
scp debug/*.elf root@matrix800:/lib/firmware/rpmsg_lite_str_echo_rtos_remote_cm33.elf

# Board: reload
echo stop > /sys/class/remoteproc/remoteproc0/state
echo start > /sys/class/remoteproc/remoteproc0/state
```

## Verify

```bash
dmesg | grep "creating channel"
# virtio_rpmsg_bus virtio1: creating channel rpmsg-tty addr 0x1e

ls /dev/ttyRPMSG*
# /dev/ttyRPMSG0
```

## Test echo

```bash
python3 -c "
import os
fd = os.open('/dev/ttyRPMSG0', os.O_RDONLY)
while True:
    d = os.read(fd, 512)
    if d: print(d.decode(errors='replace').strip())
" &

echo "hello" > /dev/ttyRPMSG0
```

## Raw rpmsg alternative (skip TTY)

```c
// create_ept.c
#include <stdio.h>
#include <fcntl.h>
#include <sys/ioctl.h>
#include <linux/rpmsg.h>

int main(void) {
    int fd = open("/dev/rpmsg_ctrl0", O_RDWR);
    struct rpmsg_endpoint_info ept = { .name = "rpmsg-tty", .src = 31, .dst = 30 };
    ioctl(fd, RPMSG_CREATE_EPT_IOCTL, &ept);
    return 0;
}
```

```bash
gcc -o create_ept create_ept.c && ./create_ept
# → /dev/rpmsg0
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| `firmware load failed -2` | Filename in sysfs ≠ file in `/lib/firmware/` |
| No `creating channel` in dmesg | Wrong shmem base — use `0xa4010000` |
| `msg received with no recipient on virtio0` | Using vdev0 rings — switch to vdev1 (`0xa4010000`) |
| `creating channel` OK but no `/dev/ttyRPMSG0` | `rpmsg_tty` not loaded before M33 started |
| `/dev/rpmsg0` writes hang | M33 never announced — check dmesg |
| `echo start` fails silently | Already running — `stop` first |

## Memory map (for reference)

```
vdev0vring0@a4000000      vdev1vring0@a4010000  ← use this
vdev0vring1@a4008000      vdev1vring1@a4018000
                          vdevbuffer@a4020000   ← NOT the vring base
```

## Persist modules across reboot

```bash
echo "rpmsg_tty" > /etc/modules-load.d/rpmsg.conf
```
