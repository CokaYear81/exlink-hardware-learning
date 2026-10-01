**Language / 语言:** [中文](01-schematic.md) · **English**

# 01 · Learning to Read the Schematics: Breaking a Complex Board into Understandable Modules

[← Previous: Preface](00-preface.en.md) · [Back to home](../README.en.md) · [Next: Soldering the Boards →](02-soldering.en.md)

## Where I Started

I think the schematics and PCB layout are the best entry points for understanding a hardware design. On a friend's recommendation, I started using JLC EDA to read the Exlink design files. A schematic shows the electrical connections between components; a PCB layout shows where those components and their traces sit on the actual board. Comparing the two gradually helped me see how the board could be divided into modules and how those modules worked together.

![Reading the Exlink schematics in JLC EDA](../assets/01-schematic/01-main-interface.png)

*The Exlink power-control board schematic in JLC EDA. The creator had already grouped the circuits by function, giving me a clear place to start.* <sub>A playful shout-out to JLC EDA: thanks, meow!</sub>

The turning point was giving up on understanding the entire board in one go and working through it one module at a time.

## How I Divided Up the Hardware

I first thought of Exlink in four parts:

1. **Power supply:** handles the Type-C input and battery charging and discharging, then provides the voltages needed by the other modules.
2. **Closed-loop power control:** adjusts the output voltage, switches the output on or off, and monitors voltage, current, and power.
3. **Main controller and user interface:** built around the ESP32-S3, with Flash, a USB hub, a screen, touch input, buttons, and a buzzer.
4. **Signal board:** uses the CH549 and RP2040 for DAPLink and logic-analyzer functions, respectively.

## Power Supply

The power section handles the Type-C input, battery charging and discharging, and conversion to supply rails such as 5 V and 3.3 V. It provides stable power to the device's modules.

![Type-C input connector](../assets/01-schematic/01-typec-input.png)

*The Type-C input brings in external power.*

![CH224K PD trigger circuit](../assets/01-schematic/01-pd-trigger.png)

*The CH224K PD trigger requests the input voltage needed by the project through the USB PD protocol.*

![5 V DC-DC supply circuit](../assets/01-schematic/01-dcdc-5v.png)

*The 5 V DC-DC circuit converts the input into a stable 5 V supply for downstream circuits.*

```text
Type-C input
  -> CH224K negotiates the required input voltage
  -> DC-DC converter produces a stable 5 V
  -> step-down circuit produces 3.3 V
  -> chips and interfaces receive their operating power
```

The IP5306 manages battery charging and discharging, allowing the device to run from its battery as well.

## Closed-Loop Power Control

The adjustable supply and power-monitoring circuits are separate from the device's basic supply rails: they serve the VOUT output. Together, they provide voltage adjustment, output switching, and status monitoring, as well as information and control needed for output protection.

![Adjustable power-supply circuit](../assets/01-schematic/01-adjustable-power.png)

*Adjustable supply: the TPS5430 generates VOUT, while the MCP4017 changes the feedback conditions to adjust its voltage.*

![Power-monitoring circuit](../assets/01-schematic/01-power-monitor.png)

*Power monitoring: a MOSFET switches the output, and the INA226 measures output voltage, current, and power.*

The TPS5430 and its feedback network form a hardware voltage-control loop. The INA226 primarily monitors the output; it should not be mistaken for a software loop that automatically regulates the voltage.

## Main Controller and User Interface

The ESP32-S3 is the main controller. It runs the program, drives the display, handles touch and button input, and communicates with the peripherals.

![ESP32-S3 minimum system](../assets/01-schematic/01-esp32-minimum-system.png)

*The ESP32-S3 minimum system includes power, reset, a crystal, external Flash, and boot configuration.*

![CH334F USB hub](../assets/01-schematic/01-usb-hub.png)

*The CH334F USB hub fans out one connection to the computer among multiple functional units inside the device.*

## Signal Board

The signal board brings together DAPLink and logic-analyzer functions. Alongside the power-control board, it forms the complete debugging tool.

![CH549 minimum system](../assets/01-schematic/01-ch549-minimum-system.png)

*The CH549 minimum system takes on the DAPLink function after the appropriate firmware is flashed.*

![RP2040 minimum system](../assets/01-schematic/01-rp2040-minimum-system.png)

*The RP2040 minimum system provides logic-analyzer functions after the appropriate firmware is flashed.*

## How I Learned to Read It

The complete set of schematics is complicated. Rather than facing the entire design at once, I gradually found a route that worked for me: understand the functional blocks already marked on the schematics, find those blocks on the PCB layout, consult datasheets for unfamiliar parts, and then use the connections between blocks to work out how the whole device operates.

### Start with the Existing Schematic Blocks

The Exlink schematics already separate the design into functional areas, which made them much easier to read. I looked at one block at a time: roughly what it did, where its input came from, and where its output went. I did not try to understand every part and wire from the outset.

Once I understood a block in broad terms, I could look at the power rails, control signals, or communication buses it shared with other blocks. The scattered circuits gradually became a system.

### Compare Them with the PCB Layout

A schematic tells me how components are connected electrically, but not where to find them on the real board. Comparing it with the PCB layout let me locate the parts and traces for each module and see how the connections on paper became pads and copper tracks.

That comparison also prepared me for soldering and troubleshooting. If a function failed, I at least knew which area of the board to inspect.

### Treat Datasheets as Component Manuals

The schematics gave me exact part numbers, which I could use to find the corresponding datasheets. To me, a datasheet was a component's manual: it helped me confirm supply requirements, pins, packages, boot conditions, and typical applications.

Datasheets are often long. Instead of forcing myself to read one from beginning to end, I searched for answers to the question in front of me:

- What supply voltage does this chip allow?
- Where is pin 1 on the package, and how does the schematic symbol map to the physical part?
- Which pins affect boot and flashing modes?
- What supporting components does a typical application circuit need?
- Is this signal an input, an output, open-drain, or differential?

A datasheet can also be a reference for AI. If I give it the relevant datasheet and ask a specific question, the answer is usually better grounded than one based on the chip's name alone, and easier to check against the schematic.

### Use AI Thoughtfully

AI helped me a great deal while I learned what the modules did and how they interacted. For many blocks, I could share a screenshot of the schematic and the relevant datasheets, then ask it to explain the flow of power and signals and the likely role of each component.

I still had to judge its answers for myself. Sometimes it offered an ambiguous conclusion or added details that the schematic did not directly support. I also found that AI could cover a lot of ground while producing more detail than I needed. I had to narrow the answer to my actual question and verify important points against the schematic, the datasheet, or a real measurement.

For me, the useful approach was to let AI help me read and organize the material, while keeping the final judgment my own.

### Go Deeper When Curiosity Calls

Once I had a rough understanding of a module, I could explore how its components work and the communication protocols or digital and analog circuit ideas behind them. Exlink introduced me to areas such as these:

| Term | What I saw it do in this project |
| --- | --- |
| GPIO | A pin configured by software, whose practical use also depends on its schematic connections |
| I²C | Lets several low-speed peripherals share a bus; addresses and pull-up resistors matter |
| SPI | Connects peripherals such as Flash and displays through clock, data, and chip-select signals |
| UART | Provides serial transmit and receive, including logs that can help with debugging |
| USB | Involves more than the connector: differential signals, device enumeration, and drivers |
| ADC | Converts a physical voltage into a value that software can read |

I did not need to master all of these on my first pass through the schematics. I could solve the most immediate question about the current module, or look for a more visual explanation on Bilibili, YouTube, or another video site.

## About the Images in This Chapter

The circuit screenshots here come from the open-source Exlink schematics. I grouped them by function to explain my own learning process; the rights to the original design and schematics remain with their creators.

I may later add:

- Examples of tracing nets in JLC EDA;
- My own annotations on datasheets;
- Hand-drawn sketches of the power, USB, and communication paths;
- Comparisons between locations on the schematics and on the physical PCB.

## What I Took Away

My biggest gain at this stage was not memorizing chip numbers. It was learning to approach complicated hardware as four problems I could trace separately: power, output control, the main controller and interface, and the signal board. With an initial understanding of those blocks, soldering no longer felt like merely putting parts onto pads. It became a way of building and checking the functions shown in the schematics, step by step.

---

[← Previous: 00 · Preface](00-preface.en.md) · [Back to home](../README.en.md) · [Next: 02 · Soldering the Boards →](02-soldering.en.md)

**Language / 语言:** [中文](01-schematic.md) · **English**
