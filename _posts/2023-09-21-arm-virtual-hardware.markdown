---
layout: post
title:  ""
date:   2023-09-21 14:24:47 +0200
categories: 
---
# Leveraging ARM Virtual Hardware (AVH) to Run Microcontroller Code in the Cloud

With this post I intend to shed some light into the seemingly dark jungle of ARM SoC and CPU types, particularly in the automotive industry. In addition, it hints at how to run microcontroller application code on cloud servers using QEMU or ARM Virtual Hardware. 

Striving to reduce development times of Electronic Control Units (ECUs), AWS Automotive customers are considering providing a virtualized version of a new hardware platform to software developers way ahead of the availability of the real hardware. This enables software development teams in two ways. 1) They can start creating software right from the start of a new ECU development project. 2) They have a means of providing each developer multiple development environments to play around with. [ARM] (https://www.arm.com/) is one of the leading suppliers in the automotive industry licensing its CPU architecture to be used in vehicle microcontrollers as well as In-vehicle Infotainment systems.

ARM has a number of CPU families. One of these is the Cortex-family. Targeting microcontrollers ARM provides the Cortex-M group of 32-bit RISC processor cores. They are used as main microcontroller cores but can also act in a SoC as a hidden CPU for a dedicated purpose like a power management controller.

Another member of the Cortex-family is the Cortex-A group of 32-bit and 64-bit RISC ARM processor cores. These processor cores are designed to be multi-purpose CPUs. AWS uses 64-bit cores from this family for the Graviton EC2 instance family. The 64-bit Cortex-A cores implement the ARMv8 architecture which implements a number of features important for modern operating multi-purpose operating systems like Linux to work.

Since AWS uses CPUs implementing the ARMv8 architecture software that is targeted for vehicle ECUs equipped with Cortex-A CPUs can be directly deployed to AWS without recompilation. Whereas software that is targeting vehicle ECUs equipped with Cortex-M CPUs cannot directly run on AWS ARM CPUs like the Graviton CPU because of the fundamental differences in the CPU instruction set design. 

The way to meet this problem is to use an emulator like QEMU (https://www.qemu.org/) or ARM Virtual Hardware (https://www.arm.com/products/development-tools/simulation/virtual-hardware). In contrast to QEMU AVH supports much more ARM CPU architectures as well as so-called AVH Fixed Virtual Platforms (AVH FVPs). Apart from the respective ARM CPU architecture and instruction set, AVH FVPs provide a simulation of an ARM-Based SoC (System on Chip). SoCs complement the CPU with peripherals useful for the intended use case for which the SoC is designed. For ARM Virtual Hardware there is an Amazon Machine Image available on the AWS Marketplace. Please follow the instructions [here](https://arm-software.github.io/AVH/main/infrastructure/html/AWS.html) to run an example speech recognition model on a virtualized microcontroller board on an Amazon EC2 instance. 

