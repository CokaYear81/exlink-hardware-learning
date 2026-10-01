**Language / 语言:** [中文](03-flashing.md) · **English**

# 03 · Flashing the Firmware: Bringing the Circuits to Life

[← Previous: Soldering the Boards](02-soldering.en.md) · [Back to home](../README.en.md) · [Next: Final Assembly →](04-assembly.en.md)

## Where I Started

With the boards soldered, the hardware foundation was in place. The ESP32-S3, CH549, and RP2040 still each needed firmware before they could do their jobs. Flashing also meant connecting them to the computer correctly and setting up the appropriate drivers.

To me, this was where software met hardware. Source code written by people is compiled and linked into a firmware image made of bits, then written into a chip's nonvolatile memory. At that point, an abstract program begins to drive the circuit in front of me, and the relationship between software and hardware becomes concrete.

## Three Chips, Three Roles

| Chip | Main role |
| --- | --- |
| ESP32-S3 | Runs the main interface, handles user interaction, and coordinates several tool functions |
| CH549 | Provides the signal board's DAPLink debugging function |
| RP2040 | Acquires signals for the logic analyzer |

Three microcontrollers add some hardware cost, but their responsibilities are clear: the ESP32-S3 is the main controller, while the other two handle debugging and sampling rather than packing every function into one chip. Together they make an embedded debugging tool, not an operating system in the usual sense.

## Flashing Starts with the Boot Mode

**Boot mode** determines what a chip does first after reset. On the ESP32-S3, for example, the boot-configuration pins determine whether it starts normally or enters the download program in its ROM and waits for a computer to send firmware. A **bootloader** hands control to the next program: on a normal start, the chip first runs boot code in ROM, then reads a second-stage bootloader from external Flash. That bootloader uses the partition table to locate and load the application. **Flash** is nonvolatile storage, retaining its contents when power is off; the board's second-stage bootloader, partition table, and application firmware reside there.

I also used this [video explaining bootloaders and the boot process](https://www.bilibili.com/video/BV1AN411R7Be/) while learning about this stage.

So there is a whole chain behind a single click on the flash button:

```text
Source code -> compile and link -> firmware image
                                      |
                                      v
Computer detects device in download mode -> USB transfer -> write to Flash
                                                            |
                                                            v
                                             Reset -> boot code loads application
```

If I use a ready-made `.bin` or `.uf2` file, I begin at the firmware-image stage. The file still has to match the target chip, the board hardware, and the location where it will be written. The three chips below differ in how they enter flashing mode, which tools they use, and how they appear to the computer once running.

## ESP32-S3: Source Project and Prebuilt Firmware

The ESP32-S3 source project uses PlatformIO. Its `platformio.ini` specifies the `esp32s3` environment, Arduino framework, partition table, PSRAM configuration, and 16 MB Flash:

```ini
[env:esp32s3]
platform = espressif32 @ 6.5.0
board = esp32-s3-devkitc-1
framework = arduino
board_build.arduino.partitions = my.csv
board_build.arduino.memory_type = qio_opi
build_flags = -DBOARD_HAS_PSRAM
board_upload.flash_size = 16MB
```

PlatformIO keeps the project configuration, dependencies, and common tasks together. `Build` compiles the source into firmware, `Upload` writes the build to the board, `Monitor` shows serial output, and `Upload and Monitor` opens the monitor after uploading. Serial output is especially useful when I need to tell whether the program actually runs. If I want to change a feature, I can also rebuild from this project. The screenshot below shows my local PlatformIO interface, with these tasks listed on the left.

![PlatformIO project tasks and interface](../assets/03-flashing/platform.png)

*Figure 1: PlatformIO tasks including Build, Upload, and Monitor.*

For the board I built, however, I actually flashed the precompiled `.bin` files supplied with the project materials using the included **Espressif Flash Download Tool**. I did not compile and upload the firmware through PlatformIO myself. That distinction matters: PlatformIO was my entry point for understanding and modifying the source project, while this build's flashing started with ready-made firmware.

In the supplied tool, I selected `ESP32-S3`, `Develop`, and `UART`, then configured the three files at the addresses shown in the accompanying instructions:

| File | Flash address | Purpose |
| --- | --- | --- |
| `bootloader.bin` | `0x0` | Second-stage bootloader |
| `partitions.bin` | `0x8000` | Partition table |
| `firmware.bin` | `0x10000` | Application |

These addresses belong to **the firmware set supplied for this project**; they should not be copied into another project without checking its partition configuration. After choosing the COM port actually connected to the board, I confirmed that the ESP32-S3 was in download mode and started flashing. If it did not enter download mode automatically, the BOOT/reset operation and USB connection needed checking. Once writing finished, I reset the board and checked the screen or serial output to confirm that the program started.

![ESP32-S3 Flash Download Tool file and address settings](../assets/03-flashing/ESP32.png)

*Figure 2: The flashing tool and addresses for the three `.bin` files used in this build.*

I also saw failures while flashing. The tool reported `8-download data fail` and suggested reducing the download baud rate. With a message like that, I first needed to check the port, download mode, and connection stability; if communication was unreliable, I could lower the baud rate and try again. `FINISH` meant the write had completed, but a successful boot after reset was the real test.

| Failed download | Completed write |
| --- | --- |
| ![ESP32-S3 flashing failure screen](../assets/03-flashing/espfalse.png) | ![ESP32-S3 flashing completion screen](../assets/03-flashing/espsuccess.png) |
| Figure 3: The tool shows `FAIL`; check the connection and download settings. | Figure 4: The tool shows `FINISH`; then check that the board boots. |

![Flash Download Tool suggestion to lower the baud rate](../assets/03-flashing/advise.png)

*Figure 5: When communication was unreliable, the tool suggested a lower download baud rate.*

## CH549: Installing the DAPLink Firmware

The CH549 provides the signal board's DAPLink function. Flashing it broadly involved two stages: put the chip in download mode and write the initial firmware using **WCHISPTool**, then check the connection and switch to DAPLink mode using **WCH-LinkUtility**. After the initial firmware was written, I also needed to confirm that the computer recognized the debugging interface.

The signal board has a dedicated DAPLink flashing button. If the computer cannot find the device, the button, USB connection, and driver also need checking. The [revised Exlink video on soldering and debugging the signal board](https://www.bilibili.com/video/BV1c41yBkELs/) shows the relevant steps.

![WCH-LinkUtility device and mode interface](../assets/03-flashing/DAPlink.png)

*Figure 6: WCH-LinkUtility identifies the device and switches its operating mode; the initial flashing uses WCHISPTool.*

## RP2040: UF2 and the Logic Analyzer

Flashing the RP2040 was more straightforward. I held its flashing button while connecting USB, and the computer recognized it as an `RPI-RP2` storage device. After I copied the UF2 firmware onto it, the chip restarted automatically and the drive disappeared. That made it easy to see how the same USB connector can present different device identities during flashing and normal operation.

Once the firmware was written, the computer also had to recognize the logic analyzer so that **PulseView** could connect to it. If PulseView could not find the device, I first checked the device and its driver in Device Manager, then verified the device and port selected in the software. Flashing successfully, being recognized by the computer, and capturing data in the host application are three separate stages to confirm.

![Zadig driver-selection interface](../assets/03-flashing/RP2040.png)

*Figure 7: Zadig's driver-selection screen; check that the target device is the intended one before configuring its driver.*

## Common Problems and Possible Causes

| Symptom | Possible cause |
| --- | --- |
| Device Manager shows “Unknown USB Device (Device Descriptor Request Failed),” code 43 | USB enumeration has not completed. The connection, power supply, or USB circuit may be involved; the screenshot alone does not establish a hub failure. |
| ESP32-S3 flashing reports `FAIL` or `8-download data fail` | The download mode, serial-port connection, or transfer stability may be at fault. An excessively high baud rate can also disrupt communication. |
| Writing completes, but the board does not boot or reports an error reading Flash after reset | The firmware files may not match the write addresses, or Flash access settings such as `SPI SPEED` may not suit the hardware. |
| The computer cannot find the device associated with the CH549 or RP2040 | The chip may be in the wrong mode, or it may have enumerated but lack the appropriate driver. |
| The `RPI-RP2` drive disappears, or PulseView cannot find the device | The drive disappearing after a UF2 write is normal. If PulseView cannot find the device, check the driver and port. |

Other useful references are the [Espressif Flash Download Tool documentation](https://docs.espressif.com/projects/esp-test-tools/en/latest/esp32/production_stage/tools/flash_download_tool.html) and the [ESP32-S3 SPI Flash mode documentation](https://docs.espressif.com/projects/esptool/en/latest/esp32s3/advanced-topics/spi-flash-modes.html). When troubleshooting, it helps to change one thing at a time and record the result.

![Windows reporting a USB device descriptor request failure](../assets/03-flashing/error.png)

*Figure 8: Device Manager showing code 43 and a device descriptor request failure.*

## What I Took Away

Flashing looks like writing files to chips, but it brought together boot circuitry, memory layout, USB communication, drivers, and software on the computer. As a beginner, I tried to look beyond the immediate steps: why was each one necessary, and what could it teach me about booting, communication, and storage? The most rewarding part was finally seeing how software and hardware fit together. The hardware constrains how a program is designed and started; the software determines what the board ultimately does. Only when all three chips were running did this collection of circuits become a usable tool.

---

[← Previous: 02 · Soldering the Boards](02-soldering.en.md) · [Back to home](../README.en.md) · [Next: 04 · Final Assembly →](04-assembly.en.md)

**Language / 语言:** [中文](03-flashing.md) · **English**
