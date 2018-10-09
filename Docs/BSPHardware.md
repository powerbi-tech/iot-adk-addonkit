---
author: parameshbabu
Description: 'Learn how to use board supported packages in order to start assembling and manufacturing your device.'
title: 'BSP for Hardware'
ms.author: pabab
ms.date: 08/28/18
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---

# IoT Core Board Supported Packages (BSP)

Board Support Packages (BSP) is a collection of drivers/settings required to run IoT Core on a hardware platform. These are provided by the hardware vendors/silicon vendors.

There are two steps to creating your own BSP:

* First, find where you can get BSPs for supported platforms **below**.
* Second, learn how to create your own BSP by following the steps listed in the [IoT Manufacturing guide](https://docs.microsoft.com/windows-hardware/manufacture/iot/create-a-new-bsp).

## Raspberry Pi BSP

1. Download [RPi_BSP.zip](https://github.com/ms-iot/iot-adk-addonkit/releases/download/17134_v5.3/RPi_BSP.zip) and extract RPi2 directory into your bsp directory (`iot-adk-addonkit\Source-arm\BSP\`)
2. Launch [IoTCoreShell](https://github.com/ms-iot/iot-adk-addonkit), select `arm`
3. In the IoTCoreShell, run the below command to build
    * `buildbsp <bspname>`

> [!NOTE]
> Raspberry Pi BSP driver sources are available at [ms-iot/bsp](https://github.com/ms-iot/bsp)

## Intel BSPs

### BSP Links

| Chipset          | Download Link          |
|--------------- |--------------------- |
| Intel® Atom™ Processor E3800 Product Family and Intel® Celeron® Processor N2807/N2930/J1900  | [Download](https://downloadcenter.intel.com/download/25618) Intel® Embedded Drivers for Microsoft Windows® 10 IoT Core (32-bit and 64-bit) MR1 |
|Intel Atom® Processor E3900 Series, and Intel® Pentium® and Celeron® Processor N- and J-Series (Apollo Lake)| [Download](https://downloadcenter.intel.com/download/25618) Software Package: Intel Atom® E3900 SoC Family—Board Support Package (BSP) for Windows* 10 IoT Core 32-bit and 64-bit Platforms |
|Intel® Pentium® and Celeron® Processor N3000 Product Families, Intel® Atom™ x5-E8000 Processor, and Intel® Atom™ x5-Z8350 Processor| [Download](https://www.intel.com/content/www/us/en/embedded/products/braswell/software-and-drivers.html) Board Support Package for Intel Atom® Processor Windows* 10 IoT Core 32-bit and 64-bit Platforms |

### Instructions to use

Follow the steps below to use this BSP with the Windows 10 ADK release 1709 (16299) with iot-adk-addonkit version 4.0 or later.

1. Download the BSP package and install
2. Copy files from `C:\Program Files (x86)\Intel IoT\Source-<arch>` to `iot-adk-addonkit\Source-<arch>`
3. Launch IoTCoreShell, select arch (x64 / x86) as required
4. In the IoTCoreShell, run the below command to convert the BSP files to latest format and build
    * run `.\bsptools\<bspname>\convert.cmd`
    * `buildbsp <bspname>`

## Qualcomm BSPs

### DragonBoard 410C

DragonBoard drivers are available at [DragonBoard 410C Software](https://developer.qualcomm.com/hardware/dragonboard-410c/software) under *Windows 10 IoT Core* section.

Steps to create the drivers :

1. Download the *_db410c_BSP.zip and extract to a folder say `C:\download\DB410c_BSP`
2. Launch IoTCoreShell, select arm

    * Run `.\bsptools\QCDB410C\export.cmd C:\download\DB410c_BSP C:\MyBSPs\QCDB410C` to export the required bsp cab files to a directory say `C:\MyBSPs\QCDB410C`.
    * `set BSPPKG_DIR=C:\MyBSPs\QCDB410C` to specify the location of the bsp cabs. See the BSPFM.xml file for the cab files it looks for in `BSPPKG_DIR`.
    * Run `buildpkg all` to create the oem packages
    * Run `buildimage <productname> Test` to build the image

## Other helpful resources

* [Windows ADK IoT Core Add-Ons Overview](https://docs.microsoft.com/windows-hardware/manufacture/iot/iot-core-adk-addons)
* [IoT Core Add-Ons command-line options](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/iot/iot-core-adk-addons-command-line-options)
* [IoT Core feature list](https://docs.microsoft.com/windows-hardware/manufacture/iot/iot-core-feature-list)
* [Channel9 Video on Manufacturing Guide](https://channel9.msdn.com/events/Build/2017/B8085)