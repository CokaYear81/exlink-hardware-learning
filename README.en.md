**Language / 语言:** [中文](README.md) · **English**

# Learning Hardware from Scratch: My Exlink Build Log

> A computer science student's first sustained encounter with schematics, soldering, firmware flashing, and bringing hardware and software together.

<!-- TODO: Add a photo of the finished device here, ideally assets/00-preface/00-finished-exlink-01.jpg. -->
<!-- Suggested caption: The finished Exlink marks both the end of this build and my first journey through a complete hardware project. -->

## About This Log

I successfully built a replica of Exlink, but this repository is not meant to be another step-by-step assembly guide. The original creator and the author of the revised build tutorial have already explained the process in detail. What I want to record is how a computer science student who mostly worked with code went from being unable to read a schematic to gradually understanding power supplies, chips, communication protocols, soldering, firmware flashing, and testing the finished device.

I have kept the order in which I learned, the moments when my understanding changed, the problems I encountered, and the photos and screenshots from each stage. This is, first of all, a project record for myself. I also hope it shows other newcomers to hardware that it is normal not to understand everything at first: even a complicated board can be broken down into small modules that you can study and test one at a time.

> [!NOTE]
> This is a personal build and learning log, not official documentation or a substitute for the original project's instructions. For wiring, soldering, and powering the device, refer to the original creators' materials, component datasheets, and the actual hardware at hand.

## Project at a Glance

| Item | Details |
| --- | --- |
| Project | Exlink multifunctional embedded debugger replica |
| My background | Computer science student starting from almost no hardware knowledge |
| Current status | Build complete; writing and photos being organized |
| Started | June 22, 2026 |
| Finished | July 7, 2026 |

## Contents

| Chapter | What it covers |
| --- | --- |
| [00 · Preface](docs/00-preface.en.md) | Why I started, and what I knew about hardware beforehand |
| [01 · Learning to Read the Schematics](docs/01-schematic.en.md) | From an overwhelming schematic to a board understood by functional blocks |
| [02 · Soldering the Boards](docs/02-soldering.en.md) | Tools, surface-mount soldering, testing in stages, and rework |
| [03 · Flashing the Firmware](docs/03-flashing.en.md) | Firmware for the ESP32-S3, CH549, and RP2040, and hardware/software debugging |
| [04 · Final Assembly](docs/04-assembly.en.md) | The screen, enclosure, battery, antenna, and final checks |
| [05 · Epilogue](docs/05-epilogue.en.md) | What changed, what I still need to learn, and where I might go next |

## From Schematics to a Finished Device

```mermaid
flowchart LR
    A["Discover Exlink"] --> B["Read the schematics"]
    B --> C["Learn about components and datasheets"]
    C --> D["Solder and measure in stages"]
    D --> E["Flash firmware for three chips"]
    E --> F["Assemble and test the device"]
    F --> G["Build my own understanding of hardware"]
```

The route looks linear, but my actual learning involved going back and forth between the schematics, the physical boards, and the software. I revisited the schematics while soldering, checked the hardware when flashing failed, and returned to the chips and communication buses when the software reported errors. Those loops gradually connected ideas that had initially seemed unrelated.

## What I Encountered Along the Way

This build introduced me to schematics, datasheets, power circuits, common communication methods, surface-mount soldering, and firmware flashing. Much of my knowledge is still at an introductory level. But making the device and troubleshooting it helped me start connecting programs and chips to real circuits, and gave me a much clearer picture of how a hardware project moves from drawings to a finished device.

## References and Thanks

This build rests on a great deal of work by the open-source creators and tutorial authors. I am grateful to everyone who shared designs, firmware, experience, and troubleshooting advice.

- [The first video I used: the Exlink project and revised build tutorial](https://www.bilibili.com/video/BV1xbS9Y4ETC/)
- [Exlink multifunctional embedded debugger build tutorial](https://blog.csdn.net/physicsexpert/article/details/145067001)
- Revised tutorial series: [power-board supply circuit](https://www.bilibili.com/video/BV16FJ9zDESG/), [power-board minimum system](https://www.bilibili.com/video/BV124n5zXEVt/), [power-board display test](https://www.bilibili.com/video/BV12HxCz7Ext/), [digitally controlled power supply](https://www.bilibili.com/video/BV1smyrBqEGH/), [signal-board soldering and debugging](https://www.bilibili.com/video/BV1c41yBkELs/), and [final assembly and debugging](https://www.bilibili.com/video/BV1vFUMBKEbN/)

The rights to the original project design, schematics, and other open-source materials remain with their respective creators. This repository documents only my own learning and build process; where I refer to specific materials, I try to credit them in context.

---

[Start reading: 00 · Preface →](docs/00-preface.en.md)

**Language / 语言:** [中文](README.md) · **English**
