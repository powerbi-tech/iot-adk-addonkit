# Get the tools needed to customize Windows IoT Core


Here's the software you'll need to create OEM images using the Windows 10 IoT Core (IoT Core) ADK Add-Ons:

## PCs and devices


Here's how we'll refer to them:

-   **Technician PC**: Your work PC. This PC should have at least 15GB of free space for installing the software and for modifying IoT Core images.

    We recommend either Windows 10 or Windows 8.1 with the latest updates. The minimum requirement is Windows 7 SP1, though this may require additional tools or workarounds for tasks such as mounting .ISO images.

-   **IoT device**: A test device or board that represents all of the devices in a single model line.

    For our examples, you'll need a Raspberry Pi 3. For more options, see [SoCs and Custom Boards](https://docs.microsoft.com/en-us/windows/iot-core/learn-about-hardware/socsandcustomboards).

-   An **HDMI cable**, and a **monitor or TV** with a dedicated HDMI input. We'll use this to verify that the image is loaded and that our sample apps are running.

## Storage


-   A **Micro SD card**. (Note, we just use this for our guide. You can build devices with other drives. Learn more about existing [supported storage](https://docs.microsoft.com/windows/iot-core/learn-about-hardware/HardwareCompatList#other-hardware-peripherals) options.)

    If your technician PC doesn't include a Micro SD slot, you may also need an adapter.

## Software

**Install the following tools on your technician PC**

1.  [Windows Assessment and Deployment Kit (Windows ADK)](http://go.microsoft.com/fwlink/?LinkId=526803) including at least the **Deployment Tools** and **Imaging and Configuration Designer (ICD)** features. You'll use these tools to create images and provisioning packages.

2.  [Windows Driver Kit (WDK) 10](http://developer.microsoft.com/windows/hardware/windows-driver-kit) (optional, required only if you are building drivers)

3.  [Windows 10 IoT Core Packages](https://www.microsoft.com/en-us/software-download/windows10iotcore). The .iso package adds the IoT Core packages and feature manifests used to create IoT Core images. By default, these packages are installed to **C:\\Program Files (x86)\\Windows Kits\\10\\MSPackages\\Retail**.

4.  [IoT Core ADK Add-Ons](https://github.com/ms-iot/iot-adk-addonkit/tree/17134).  Click **Clone or Download** > **Download ZIP**, and extract it to a folder, for example, **C:\\IoT-ADK-AddonKit**. This kit includes the sample scripts and base structures you'll use to create your image. To learn about the contents, see [What's in the Windows ADK IoT Core Add-ons](iot-core-adk-addons.md)).

5.  [Windows 10 IoT Core Dashboard](http://go.microsoft.com/fwlink/p/?LinkId=708576).

6.  [The Raspberry Pi BSP](https://github.com/ms-iot/iot-adk-addonkit/releases/download/v4.4/rpibsp-wm.zip). Since this lab uses a Raspberry Pi, you'll need to download the Raspberry Pi BSP. If you're working with a device other than Raspberry Pi, visit the [Windows 10 IoT Core BSP](BSPHardware.md) page to download other BSPs.

7.  [Get a code-signing certificate](https://docs.microsoft.com/windows-hardware/drivers/dashboard/get-a-code-signing-certificate). For the kernel driver signing, Standard Code signing certificate is sufficient. You will require an EV cert to access the [Device Update Center](http://aka.ms/deviceupdatecenter) in Hardware Dev Center portal. This will be required when you build a retail image.

Other helpful software:

-   **A text editor such as Notepad++**. You can also use the Notepad tool, though for some files, you won't see the line breaks unless you open each file as a UTF-8 file.

-   **A compression program such as 7-Zip**, which can uncompress Windows app packages.

-   [Visual Studio 2017](http://go.microsoft.com/fwlink/?LinkId=715695), used to create an app in [Lab 1b: Add an app to your image](deploy-your-app-with-a-standard-board.md).


## Other software


-   **An app built for IoT Core**. Our samples use the [IoT Core Default](https://github.com/ms-iot/samples/tree/develop/IoTCoreDefaultApp) app, though you can use your own.

-   **A driver built for IoT Core**. Our samples use the [Hello, Blinky](https://developer.microsoft.com/windows/iot/samples/helloblinky) driver, though you can use your own.

## <span id="Next_steps"></span><span id="next_steps"></span><span id="NEXT_STEPS"></span>Next steps

[Lab 1a: Create a basic image](create-a-basic-image.md)
