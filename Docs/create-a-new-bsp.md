# Lab 2: Creating your own board support package (BSP)

A BSP includes a set of device drivers that are specific to the components/silicon used in the board. These are provided by the component vendors / silicon vendors, mostly in the form of .inf and associated .sys/.dll files.

Create a new Board Support Package (BSP) when:

- Creating a new hardware design

- Replacing a driver or component on an existing hardware design

Whether you're creating a new BSP or modifying an existing BSP, you become the owner. This lets you decide whether to allow updates to install on your boards.

In our lab, we'll create a new BSP based on the Raspberry Pi 2, removing the existing GPIO driver and replacing it with the sample GPIO driver: [Hello, Blinky!](https://developer.microsoft.com/windows/iot/samples/driverlab).

## <span id="Create_a_new_BSP_working_folder"></span><span id="create_a_new_bsp_working_folder"></span><span id="CREATE_A_NEW_BSP_FILE"></span>Create a new BSP working folder

1. From the IoT Core Shell, create a BSP working folder that you'd like to modify.

    ```powershell
    newbsp MyRPi2
    ```

## Add packages into the feature manifest

1. Open the feature manifest file for your new BSP, \\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\BSP\\MyRpi2\\MyRpi2FM.xml.

    In another window, open the Raspberry Pi 2 feature manifest to use as a template.

2. Add your base packages (BasePackages).

    - UEFI drivers for the boot partition (RASPBERRYPI.RPi2.BootFirmware.cab)

    - Drivers required for UpdateOS (SV.PlatExtensions.UpdateOS.cab)

    - Mandatory device drivers (bcm2836sdhc.cab, dwcUsbOtg.cab, rpiq.cab)

    When creating your own BSP, it's typical to require a display driver and a storage driver, and sometimes a network driver.

    - Device-specific customizations

3. Copy in the device layout and platform packages (DeviceLayoutPackages, OEMDevicePlatformPackages).

    Note that both the OEMDevicePlatform.xml and devicelayout.xml can be packaged into one package, for example, DeviceLayout.MBR4GB. The same package can then be specified as input in both the sections (for example, under <OEMDevicePlatformPackages> and <DeviceLayoutPackages>).  To learn more, see [Device layout](device-layout.md).

4. Copy in features (Features).

    Copy in features you want. Exclude any that don't apply to your project.

    For example, copy in each of the drivers **except** the existing GPIO driver:

    ``` xml
    <PackageFile Path="$(mspackageroot)\Retail\$(cputype)\$(buildtype)" Name="RASPBERRYPI.RPi2.GPIO.cab">
        <FeatureIDs>
          <FeatureID>RPI2_DRIVERS</FeatureID>
        </FeatureIDs>
    </PackageFile>
    ```

    Note: To make grouping packages easier, you can combine them into one or more Feature IDs. For example, all of the Raspberry Pi 2 optional drivers use the Feature ID: RPI2_DRIVERS.

5. Add the HelloBlinky driver:

    ``` xml
        <PackageFile Path="%PKGBLD_DIR%" Name="%OEM_NAME%.Drivers.HelloBlinky.cab">
          <FeatureIDs>
            <FeatureID>RPI2_DRIVERS</FeatureID>
          </FeatureIDs>
        </PackageFile>
    ```

## Create a new product folder

1. Create a new working product folder, adding your BSP name to the end.

    ```powershell
    newproduct ProductB MyRpi2
    ```

    This creates the folder: C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Products\\ProductB, which is linked to the new BSP.

## Update the project's configuration files

1. Open your product's test configuration file: **C:\\IoT-ADK-AddonKit\\Source-arm\\Products\\ProductB\\TestOEMInput.xml**.

2. Add FeatureIDs:

    - Add the FeatureID: IOT_GENERIC_POP to get OS-only updates.

    - Add the FeatureIDs: IOT_DISABLE_UMCI and IOT_ENABLE_TESTSIGNING to enable test binaries and packages to work.

    - Optional: add the FeatureID for the other apps and test packages: APP_MyUWPApp, CUSTOM_CMD, CUSTOM_FileAndRegKey, that you created in Lab 1.

       ``` xml
       <Microsoft>
          <Feature>IOT_GENERIC_POP</Feature>
          <Feature>IOT_DISABLE_UMCI</Feature>
          <Feature>IOT_ENABLE_TESTSIGNING</Feature>
       ...
       </Microsoft>

       <OEM>
          <Feature>RPI2_DRIVERS</Feature>
          <Feature>CUSTOM_CMD</Feature>
          <Feature>APP_MyUWPApp</Feature>
          <Feature>CUSTOM_FileAndRegKey</Feature>
        </OEM>
        ```

## Build and test the image

### Build the image

1. From the IoT Core Shell, create the image:

    ```powershell
    createimage ProductB Test
    ```

    This creates the product binaries at C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\ProductB\\Flash.FFU.

2. Start **Windows IoT Core Dashboard** &gt; **Setup a new device** &gt; **Custom**, and browse to your image.

    Put the Micro SD card in the device, select it, accept the license terms, and click **Install**. This replaces the previous image with our new image.

3. Put the card into the IoT device and start it up.

    After a short while, the device should start automatically, and you should see your app.

### Check to see if your driver works

1. Use the [testing procedures in the Hello, Blinky! lab](https://developer.microsoft.com/windows/iot/samples/driverlab3) to test your driver.

## Next steps

Congratulations, you've completed Lab 2.

[Lab 3: Update apps](https://docs.microsoft.com/windows-hardware/service/iot/updating-iot-core-apps)

## Related topics

[Device layout](device-layout.md)