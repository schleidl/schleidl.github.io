---
layout: post
title:  "Leveraging ARM Virtual Hardware (AVH) to Run Microcontroller Code in the Cloud"
date:   2023-10-01 14:24:47 +0200
categories: AWS, ECU, virtualization
---
# Exploring ARM SoC and CPU Types in the Automotive Industry

With this post, I intend to shed light on the seemingly dark jungle of ARM SoC and CPU types, particularly in the automotive industry. Additionally, it hints at how to run microcontroller application code on cloud servers using [QEMU](https://www.qemu.org/) or [ARM Virtual Hardware](https://www.arm.com/products/development-tools/simulation/virtual-hardware).

## Accelerating ECU Development with Virtualization 

Striving to reduce development times for Electronic Control Units (ECUs), AWS Automotive customers are contemplating providing a virtualized version of a new hardware platform to software developers well ahead of the real hardware's availability. This approach enables software development teams in two ways: 
1. They can begin creating software right from the start of a new ECU development project
2. They have a means of providing each developer with multiple development environments to experiment with.

[ARM](https://www.arm.com/) is one of the leading suppliers in the automotive industry, licensing its CPU architecture for use in vehicle microcontrollers as well as In-Vehicle Infotainment systems.

## An Overview of ARM CPU Families

ARM boasts a number of CPU families. One such family is the Cortex-family. Targeting microcontrollers, ARM provides the Cortex-M group of 32-bit RISC processor cores, which are used as main microcontroller cores but can also act in an SoC as a hidden CPU for a dedicated purpose, like a power management controller.

Another member of the Cortex-family is the Cortex-A group of 32-bit and 64-bit RISC ARM processor cores. These processor cores are designed to be multi-purpose CPUs. AWS employs 64-bit cores from this family for the Graviton EC2 instance family. The 64-bit Cortex-A cores implement the ARMv8 architecture, which encompasses a number of features important for modern, multi-purpose operating systems like Linux to function.

## Deploying to AWS and Handling CPU Architecture Differences 

Since AWS utilizes CPUs implementing the ARMv8 architecture, software targeted for vehicle ECUs equipped with Cortex-A CPUs can be directly deployed to AWS without recompilation. In contrast, software targeting vehicle ECUs equipped with Cortex-M CPUs cannot directly run on AWS ARM CPUs, like the Graviton CPU, due to the fundamental differences in the CPU instruction set design.

## Emulating ARM Architecture

To navigate this issue, one can use an emulator like [QEMU](https://www.qemu.org/) or [ARM Virtual Hardware](https://www.arm.com/products/development-tools/simulation/virtual-hardware). Unlike QEMU, AVH supports many more ARM CPU architectures as well as so-called AVH Fixed Virtual Platforms (AVH FVPs). Beyond the respective ARM CPU architecture and instruction set, AVH FVPs provide a simulation of an ARM-Based SoC (System on Chip). SoCs complement the CPU with peripherals useful for the intended use case for which the SoC is designed. For ARM Virtual Hardware, there is an Amazon Machine Image available on the AWS Marketplace. Please follow the instructions [here](https://arm-software.github.io/AVH/main/infrastructure/html/AWS.html) to run an example speech recognition model on a virtualized


