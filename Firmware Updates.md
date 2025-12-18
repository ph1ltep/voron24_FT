# OCTOPUS FIRMWARE UPGRADE

## Klipper

### Make firmware:

```bash
cd ~/klipper
make clean
make menuconfig
```

- STM32 F446 12Mhz
- USB over CAN
- CAN PD0/PD1 for F446

```bash
make
```

### Flash

Octopus cannot update directly when in USB over CAN mode. First need to enter bootloader to force Serial interface and disable CAN.

- Kill klipper service, as it'll force a reset of Octopus back to CAN mode

```bash
systemctl stop klipper
```

- Double-tap BOOT button
- Verify serial interface shows up

```bash
ls /dev/serial/by-id/*
# Use the displayed interface path in the flash command
```

Update firmware:

```bash
cd ~/katapult/scripts
python3 flash_can.py -d /dev/serial/by-id/usb-katapult_stm32f446xx_470039000550534E4E313120-if00 -f ~/klipper/out/klipper.bin
```

## Bootloeader

- Add jumper to BOOT0
- Unplug CAN RJ11
- Boot up
- Double-tap BOOT button
- Verify DFU mode:

```bash
lsusb
```

### References

- https://klipper.discourse.group/t/octopus-pro-canboot-can-bus-bridge/3734/22
- https://github.com/akhamar/voron_canbus_octopus_sb2040 ("Update klipper on the octopus" section)

# RASPBERRY PI LINUX PROCESS

### Make firmware:

```bash
cd ~/klipper
make clean
make menuconfig
```

- Linux Process

### Flash

```bash
make flash
```

# Fly-SHT-36-Pro v1.0

```bash
cd ~/klipper
make clean
make menuconfig
```

- Enable extra low-level configuration options
- RP2040/RP235x
- 16KiB bootloader
- CAN Bus
- GPIO pins to set at startup: !gpio13

```bash
make -j4
```

### Flash

```bash
python3 ~/klipper/lib/canboot/flash_can.py -u 7a5682922d89
```

## References

- https://mellow.klipper.cn/en/docs/ProductDoc/ToolBoard/fly-sht36/sht36_pro/flash/can?tab-1=armbian

# UUIDs

Getting the UUID requires it to not be setup in Klipper's config, and for klipper service to be stopped

```bash
systemctl stop klipper
python3 ~/klipper/scripts/canbus_query.py can0
```

## References:

- Octopus: 0ad1ade02795
- SHT36 Pro: 7a5682922d89

# CHANGING GIT BRANCHES

In SSH:

```bash
cd ~/klippain_shaketune
git fetch origin #gets latest list of remote branches
git branch -r #lists all available branches
git checkout compat-dec2025
git pull origin compat-dec2025
```

Note: update moonraker's auto-update in moonraker.conf to the correct branch.
