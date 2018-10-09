# Lab 1a: Create a basic image

To get started, we'll create a basic Windows 10 IoT Core (IoT Core) image, flash it to a micro SD card, and put it into a device to make sure that everything's working properly. 

We'll create a product folder that represents our first design. For our first product design, we'll customize just enough for the IoT core device to boot up and run the built-in OOBE app, which we should be able to see on an HDMI-compatible monitor.

To make running these commands easier, we'll install and use the IoT Core shell, which presets several frequently-used paths and variables.

## Prerequisites

See [Get the tools needed to customize Windows IoT Core](set-up-your-pc-to-customize-iot-core.md) to get your technician PC ready. 

## Create a basic image

### Set your OEM name (one-time only)

-   Open the file **C:\\IoT-ADK-AddonKit\\Tools\\setOEM.cmd** in Notepad, and modify it with your company name. We've added this variable to help you create packages with names that are easy to differentiate from those provided from other manufacturers you're working with. Only alphanumeric characters are supported in the OEM_NAME as this is used as a prefix for various generated file names.

    ```
    set OEM_NAME=Fabrikam
    ```

### Start the IoT Core shell, choose your architecture, and install test certificates

1.  In Windows Explorer, go to the folder where you installed the IoT Core ADK Add-Ons, for example, **C:\\IoT-ADK-AddonKit**, and open **IoTCoreShell.cmd**. It should prompt you to run as an administrator.

    The new value for OEM\_NAME should appear when you start the tool.
	
	Troubleshooting: Error: "The system cannot find the path specified". If you get this, right-click the icon and modify the path in "Target" to the location you've chosen to install the tools.

2.  At the **Set Environment for Architecture** prompt, select 1 for ARM, 2 for x86, or 3 for x64, based on the architecture for the boards that you'll be developing. For example, press **1** to create an image that's compatible with the Raspberry Pi 2 or Raspberry Pi 3, or press **2** to create an image that's compatible with the Minnowboard Max.

    The launch tool sets the default architecture, and sets a version number for the design, which you can use for future updates. The first version number defaults to 10.0.0.0.

    (Why a four-part version number? Learn about versioning schemes in [Update requirements](https://docs.microsoft.com/windows-hardware/service/mobile/update-requirements))

**Install certificates**

From the IoT Core Shell, install the test certificates, which you'll use to sign your test binaries. You'll only need to do this the first time you install the IoT ADK Add-on Kit.

```
installoemcerts
```

The certificates are added to the root. To learn more, see [Set up the signing environment](https://msdn.microsoft.com/library/windows/hardware/dn756804)

### Build a Raspberry Pi BSP (New for Windows 10, Version 1703)

1. Extract [rpibsp_wm.zip](https://github.com/ms-iot/iot-adk-addonkit/releases/download/v4.4/rpibsp-wm.zip) to a folder on your hard drive, for example. `C:\BSP`.

2. From the IoT Core Shell, navigate to `C:\BSP`, and run `build.cmd`. This will add the packages necessary to create a project with the RPi2 BSP.

```
cd c:\BSP
build.cmd
```
For more information on available BSPs, see [Windows 10 IoT Core BSPs](BSPHardware.md).

### Build packages

From the IoT Core Shell, get your environment ready to create products by building all of the packages in the working folders. 

```
buildpkg All
```


### Create a test project

From the IoT Core Shell, create a new product folder that uses the Rpi2 BSP. This folder represents a new device we want to build, and contains sample customization files that we can use to start our project.

```
newproduct ProductA rpi2
```
    
The BSP name is the same as the folder name for the BSP. You can see which BSPs are available by looking in the `C:\IoT-ADK-AddonKit\Source-\<arch>\BSP` folders.

This creates the folder: `C:\IoT-ADK-AddonKit\Source-<arch>\Products\\ProductA`.

### Build an image

1.  Eject any removable storage drives, including the Micro SD card and any USB flash drives.

2.  From the IoT Core Shell, build a flashable test image using the default files. Test images include additional tools, and you can create test images using either signed or unsigned test packages.

    ```
    buildimage ProductA test
    ```

    This builds an FFU file with your basic image at `C:\IoT-ADK-AddonKit\Build\<arch>\ProductA\Test`.

    Troubleshooting:
	
	-  ERROR CODES: 0x80070005 or 0x800705b4: Unplug all external drives (including micro SD cards and USB thumb drives), and try again.  
	If this doesn't work, go back to [Set up your PC and download the samples](set-up-your-pc-to-customize-iot-core.md) and make sure everything's installed.

### Flash the image to a memory card

1.  Start the **Windows IoT Core Dashboard**.

2.  Plug your micro SD card into your technician PC, and select it in the tool.

3.  From **Setup a new device**, select Device Type: **Custom**.

4.  From **Flash the pre-downloaded file (Flash.ffu) to the SD card**, click **Browse**, browse to your FFU file (`C:\IoT-ADK-AddonKit\Build\<arch>\ProductA\Test\Flash.ffu`), then click **Next**.

5.  Optional: Change the default device name (Default is minwinpc.) 

6.  Enter your device password.

7.  Accept the license terms, and then click **Install**. The Windows IoT Core Dashboard formats the micro SD card and installs the new image.

### Try it out

1.  Connect your IoT device, such as a Raspberry Pi 3, into a monitor using an HDMI cable.
    **Note**  When possible, use a direct connection to an HDMI port. The display may not appear when using DVI/VGA adapters or hubs.

2.  Put in the micro SD card with your image.

3.  Power it on.

    After a short while, the device should start automatically, and you should see the [IoT Core Default app](https://developer.microsoft.com/windows/iot/samples/iotdefaultapp) (code-named "Bertha"), which shows basic info about the image.

    **Note**  Some devices may be extremely slow to boot up on the first boot when using some 8GB class 10 SD cards. Slow boot times may be over 15 minutes. Subsequent boots will be much quicker on the affected cards.

See also [Set up your device](https://docs.microsoft.com/en-us/windows/iot-core/getstarted) for more instructions on flashing the device.

## Next steps

Leave the device on for now, and continue to [Lab 1b: Add an app to your image](deploy-your-app-with-a-standard-board.md).
