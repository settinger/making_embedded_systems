# Making Embedded Systems

## Final Report

## Sam Ettinger, Orange Stars Cohort, June 2022

<!--- Application description --->

[![Still image of YouTube Link](http://i3.ytimg.com/vi/2f6550TF2GQ/hqdefault.jpg)](https://youtu.be/2f6550TF2GQ "MES Project video")

For various reasons, I had a difficult time anticipating what hardware I would be able to acquire and familiarize myself with in time for a meaningful project. Consequently I aimed to rely as much as possible on the class board, the STM32F429I-DISC1, and its built-in peripherals. However, this left me dissatisfied with the scope of any single project idea I could come up with. To remedy this, I implemented two projects, with a shared theme of providing quick, satisfying mental activity.

## Project 1: Projective Set

Project 1 is a portable implementation of the game [Projective Set](<https://en.wikipedia.org/wiki/Projective_Set_(game)>). In this game, seven cards are dealt out, each with some number of different-color dots:

![](proset6.png)

The objetive is to find a "set," defined as any number of cards such that there is an even number of each color dot. For example, in the above diagram, A-B-G form a set, as they combine to have 2 yellow, 2 green, 2 magenta, 0 red, 0 orange, and 0 blue dots. B-C-D-F-G also forms a set, because there are 2 of each color. A third set exists: A-C-D-F, the exclusive disjoint set of A-B-G and B-C-D-F-G. The best way to get a feel for the rules is to play it; [here is a web-based version to play around with](https://www.settinger.net/projects/proset/prosetJS/).

<!-- TODO: Screenshot of device here -->

The board is displayed on the STM32F429I-DISC1 LCD and can be interacted with using the touch screen or through a serial terminal. In addition to the 6-dot mode of play shown above, this project implements 4- and 5-dot modes (for fewer cards and easier game play) as well as 7- and 8-dot modes (for exponentially more difficult gameplay).

## Project 2: 3D pointing device and paint tool

Project 2 is a controller for painting symmetrical patterns on a spherical surface. This is an experiment in taking the two-dimensional _azulejo_ pattern of repeating tiles with specific symmetry:

![](jean-carlo-emer-5OYrWBI97RY-unsplash.jpg)
<small><i>Detail from a photo by <a href="https://unsplash.com/@jeancarloemer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Jean Carlo Emer</a> on <a href="https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a></i></small>

![](https://www.settinger.net/projects/azulejio/img/azulejio.gif)

Drawing such a pattern in two dimensions is straightforward enough; what would a three-dimensional version look like? To answer this, I turned the STM32F429I-DISC1 into a pointing device for painting on a sphere in 3D space.

<!--screenshot -->

A serial communication port is opened between the STM32F429I-DISC1 and a webpage running the [a Web Serial program](https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API). The STM32F429I-DISC1 tracks its own position using the on-board gyroscope and an LIS2DH accelerometer glued to the back. This position data (along with button settings, color and size preferences) is transmitted over serial and used to draw on the surface of a sphere in a webpage. The cursor size and color can be set via the touchscreen.

![](azulejo3d.gif)

## Hardware

For both projects, the STM32F429I-DISC1 development board was used as the core of the hardware. Both projects rely on the on-board LCD and its touch responsiveness. The LCD display is driven by the [ILI9341](https://cdn-shop.adafruit.com/datasheets/ILI9341.pdf) driver chip, which communicates with the STM32F429 over SPI; the touch control is driven by the [STMPE811](https://media.digikey.com/pdf/Data%20Sheets/ST%20Microelectronics%20PDFS/STMPE811.pdf) which communicates with the STM32F429 over I<sup>2</sup>C. Both devices are accessed through drivers and a hardware abstraction layer provided by ST.

Both projects also rely on the development board's "user button", which is read from a simple GPIO pin.

Both projects use a USART to communicate with a PC over serial. A generic USB-to-TTL is connected to the development board for this purpose. Projective Set requires two-way serial communication but the 3D Controller only transmits data. This USART is accessed through a hardware abstraction layer from ST.

For Projective Set, a user setting needs to be stored between sessions, which would be a good use case for EEPROM. The development board does not have any EEPROM hardware, but that hardware is emulated by the STM32F429 by repurposing the last page of Flash memory.

For the 3D Controller, orientation sensing is achieved with the on-board [I3G4250D](https://www.st.com/resource/en/datasheet/i3g4250d.pdf) gyroscope and an external [LIS2DH](https://www.st.com/resource/en/datasheet/lis2dh.pdf) accelerometer breakout attached to the development board. Both devices communicate over SPI via a hardware abstraction layer and drivers from ST.

<!-- diagrammes -->

## Software

The code for the projects was generated in STM32CubeIDE. The repositories are on GitHub: [Projective Set here](https://github.com/settinger/MES_Projective_Set), [3D Controller here](https://github.com/settinger/MES_3D_Controller).

### **ST software modules and licenses**

1. The hardware abstraction layer modules for the STM32F42xx are not included directly in the repositories above. Instead, it is referenced directly from the [STM32CubeF4 series libraries](https://github.com/STMicroelectronics/STM32CubeF4) that were included in my installation of STM32CubeIDE. These are used under the license agreement provided when installing STM32CubeIDE.
1. The CMSIS math libraries used in the 3D Controller project are linked to in the same manner as the HAL modules. These are licensed by ARM Limited under an [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).
1. The Board Support Package (BSP) drivers for the STM32F429I-DISC1 are supplied by ST but are included directly in the repositories. These drivers are used to interface with the LCD and touch screen in both projects, as well as the gyroscope and accelerometer in the 3D controller. These are licensed by ST under the [BSD 3-Clause License](https://opensource.org/licenses/BSD-3-Clause).
1. The library used to configure the LIS2DH accelerometer is provided by ST under the [BSD 3-Clause License](https://opensource.org/licenses/BSD-3-Clause).
1. The EEPROM Emulation used in the Projective Set project is lightly modified from the library in the STM32CubeIDE example project called `EEPROM_Emulation`. It is licensed with a general "AS-IS" license.
1. The font libraries used in Projective Set are direct from ST as well, and included in the `/Utilities/` folder. These libraries are licensed from ST with [a general "AS-IS" license](https://github.com/STMicroelectronics/STM32CubeF4/blob/master/Utilities/Fonts/Release_Notes.html). A slight alteration was made to generate a narrower monospace font for the Projective Set project.
1. The STM32 USB Host Library and STM32 USB Device Library are unused, but included in the 3D Controller project to motivate me to turn the controller into a USB HID input device soon. These libraries are licensed by ST with [SLA0044, the "Ultimate Liberty Software License Agreement"](https://www.st.com/content/ccc/resource/legal/legal_agreement/license_agreement/group0/87/0c/3d/ad/0a/ba/44/26/DM00216740/files/DM00216740.pdf/jcr:content/translations/en.DM00216740.pdf).

### **Software modules and licenses from other sources**

1. A Kalman filter was implemented to calculate Euler angles from the fused gyroscope and accelerometer readings. This filter library was largely derived from the [MPU6050 library by Konstantin Bulanov](https://github.com/leech001/MPU6050) which is licensed with a [GNU GPL v3 license](https://www.gnu.org/licenses/gpl-3.0.en.html). That library itself is derived from the [C++ Kalman Filter library by Kristian Lauszus](https://github.com/TKJElectronics/KalmanFilter) which is licensed with a [GNU GPL v2 license](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html). The MPU6050 filter was changed to (1) read from the on-board I3G4250D gyroscope and the external LIS2DH accelerometer, (2) use `float` instead of `double` in the math, (3) rearrange which axes are aligned with which directions in the body frame and the inertial frame.
1. To create the 3D graphics used on the 3D Controller web client, I used [three.js](https://threejs.org/) which is licensed under an [MIT License](https://github.com/mrdoob/three.js/blob/dev/LICENSE).

### **Software written by me for these projects**

<!--
- Software description
  - Describe the code in general
  - Describe the parts you wrote in some detail (maybe 3-5 sentences per module)
  - Describe the code you re-used from other sources, including the licenses for those
--->

**Projective Set console:**
The serial console used in Projective Set is derived from the in-class assignment to generate a console with commands. However, instead of instructing the buffer to wait for a null character or endline character, it has been altered to respond to individual keystrokes. For example, typing the numeral '1', without hitting enter, immediately activates the card in the first spot on the board. This adjustment allowed the console to be used for testing and for keyboard control of the finished game.

**3D Controller console:**
The serial console used for the 3D controller was greatly simplified by virtue of being transmit-only. The board transmits one- or two-parameter instructions that are received and interpreted by a webpage running the experimental [Web Serial API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API), which processes the instruction with a Javascript program. <!-- TODO: link to the section of the writeup about the Javascript program -->

**Projective Set game engine and graphics:**
The main loop of the Projective Set program runs at 50 frames per second. On each frame, the program checks for new inputs from the touch screen, console, and user button, then adjusts its internal state and/or graphics accordingly. <!--TODO: link to state diagram section--><!--TODO: more description-->

**3D controller main program and graphics:**
The main loop of the 3D controller program runs at 40 frames per second. On each frame, the program takes sensor readings, adjusts the Kalman filter to generate estimated Euler angles, and transmits those Euler angles via serial UART. In addition, the program checks for inputs from the touch screen, console, and user button, and accordingly adjusts its serial outputs and internal state.

**LIS2DH accelerometer interface:**
While the LIS2DH accelerometer could be controlled entirely through the ST-provided driver, it was streamlined by writing a library to encapsulate the key functions such as providing the initial configurations, enabling/disabling interrupts, and SPI configuration. Much of this code was structurally based on [an example from ST](https://github.com/STMicroelectronics/STMems_Standard_C_drivers/blob/master/lis2dh_STdC/examples/lis2dh_read_data_polling.c).

## Architecture

<!---
diagrams of the architecture
hardware block diagram
functional diagram
flow control
state diagrams -- main and touch
--->

## Build instructions

### **Toolchains**

Both projects were built and tested in the STM32CubeIDE program. STM32CubeIDE comes installed with some common libraries and the option to download others. Rather than copy-pasting these libraries into every project that uses them, it is possible to link each one from its central location in the STM32Cube installation to each individual project. However, this does mean that, when building the project on a new machine, it requires re-configuring the expected paths. <!-- Todo: STM32CubeIDE, CMSIS, -->

### **Hardware**

Projective Set does not require any peripherals to play, just the STM32F429I-DISC1 itself. It benefits from being connected to a computer terminal for serial console input, which is done on the USART line `UART5`. The 3D Controller requires that USART serial console, as well as an LIS2DH accelerometer connected to the SPI bus `SPI5`. The orientation of the accelerometer matters greatly—the board expects it to be attached to the reverse face of the STM32F429I-DISC1 and oriented like so:

<!-- Todo: picture of how to glue the accel-->

The connections for `UART5` and `SPI5` are as follows:

| Peripheral | Signal | Pin  | Description                                    |
| ---------- | ------ | ---- | ---------------------------------------------- |
| UART5      | RX     | PC12 | Data _from_ computer _to_ STM32                |
| UART5      | TX     | PD2  | Data _to_ computer _from_ STM32                |
| SPI5       | CS     | PF6  | SPI chip select for accelerometer (active low) |
| SPI5       | SCK    | PF7  | SPI clock signal                               |
| SPI5       | MISO   | PF8  | SPI data _to_ sensor _from_ STM32              |
| SPI5       | MOSI   | PF9  | SPI data _from_ sensor _to_ STM32              |

A generic USB-to-TTL cable was used for the serial communication between the STM32 and a computer. The serial communication was configured for 115200 baud, 8 data bits, 1 stop bit, no flow control, and no parity.

### **Software**

<!-- Todo: STM32CubeIDE, CMSIS, threejs, webserial, vs code?,  -->

### **Debugging and testing**

<!-- Todo: -->

### **Power**

<!-- How you powered it (and how you might in the future) -->
<!-- TODO: Some ways to save power: reduce unused sensor activity, rely on interrupts and not loop polling -->

## Looking forward

<!-- Would would be needed to get this product ready for production? How would you extend the project to do something more? Are there other features you'd like? How would you go about adding them? -->
<!-- Todo: USB HID, reductions in size/scale. How much flash/ram am I using? -->

## Appendix: Grading rubric and self-assessment

<!-- Todo: -->
