**Language / 语言:** [中文](05-epilogue.md) · **English**

# 05 · Epilogue

[← Previous: Final Assembly](04-assembly.en.md) · [Back to home](../README.en.md)

## What I Gained

Starting with the schematics, I went through soldering, inspection, firmware flashing, and finally fitting the boards, screen, and battery into the enclosure. I had now followed the whole Exlink build from beginning to end.

At first, I often had no idea where to begin when looking at a circuit board. Now I look for the power supply, main controller, and interfaces, then use the schematics to work out how the parts connect and what they do. The supply gives the chips the conditions they need to run. Firmware tells the controller how to handle inputs and drive peripherals. Traces and connectors carry the signals. If something fails, I can check the power, solder joints, connections, and software one by one. Components, wires, and code that once seemed unrelated are beginning to fit together in my mind.

Soldering and powering up in stages, checking before applying power, flashing several chips, and solving connection and space problems during assembly were all lessons I learned by doing.

## What I Still Need to Learn

This build largely followed an open-source design and used existing firmware, so I still have much to learn about circuits, programming, and modeling. I cannot yet design and lay out a board independently. The source repository is large and complex, and I did not manage to understand all of it. I am not particularly skilled at 3D modeling either. Which of those areas I explore next will depend on the projects I want to build and the direction I choose. For now, at least, I have a better idea of where to start when something goes wrong and how to try to solve it.

## Thanks to the Open-Source Community

Thank you to Expert Electronics Lab for publishing the [original Exlink project](https://oshwhub.com/expert/gai-jin-xin-exlink-duo-gong-neng-diao-shi-qi-fen-li-die-ban), and to usolve for sharing the [Exlink 0603 revision](https://oshwhub.com/usolve/exlink_0603) based on it. Their work gave me the chance to build this fascinating little device for myself.

> [!IMPORTANT]
> This log describes how I learned hardware by following work others had already made available. It does not replace the original design documentation or build tutorials. Other materials and acknowledgments are listed in the [README's “References and Thanks” section](../README.en.md#references-and-thanks).

## One Last Thought

When I closed the enclosure and saw the main interface on the screen, I knew the build was complete. The connections I had checked and the firmware I had flashed had finally come together in a working Exlink. I will remember the delight of seeing the screen light up for the first time—resistors are less dramatic—and carry on trying new things.

![Fully assembled Exlink](../assets/04-assembly/poweron.png)

*The assembled device displaying its main interface.*

---

[← Previous: 04 · Final Assembly](04-assembly.en.md) · [Back to home](../README.en.md)

**Language / 语言:** [中文](05-epilogue.md) · **English**
