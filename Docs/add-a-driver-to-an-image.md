# Lab 1e: Add a driver to an image

In this lab, we'll add the sample driver: [Toaster](https://github.com/Microsoft/Windows-driver-samples/tree/6c1981b8504329521343ad00f32daa847fa6083a/general/toaster/toastDrv), package it up, and deploy it to the to a Windows 10 IoT Core device.

## Prerequisites

* Create a product folder (ProductB) that's set up to boot to the default (Bertha) app, as shown in [Lab 1a: Create a basic image](create-a-basic-image.md) or [Lab 1c: Add a file and a registry setting to an image](add-a-registry-setting-to-an-image.md).

## Check for similar drivers

Before adding drivers, you may want to review your pre-built Board Support Package (BSP) to make sure there's not already a similar driver. 

For example, review the list of drivers in the file: \\IoT-ADK-AddonKit\\Source-arm\\BSP\\Rpi2\\Packages\\RPi2FM.xml.

- If there's not an existing driver, you can usually just add one.

- If there is a driver, but it doesn't meet your needs, you'll need to replace the driver by creating a new BSP. We'll cover that in [Lab 2](create-a-new-bsp.md).

## Create your test files

-  Complete the steps listed under the [Toaster Driver Sample](https://github.com/Microsoft/Windows-driver-samples/tree/6c1981b8504329521343ad00f32daa847fa6083a/general/toaster/toastDrv) to build it. You'll create two files: wdfsimple.inf and wdfsimple.sys, which you'll use to install the driver.

   You can also use your own IoT Core driver, so long as it doesn't conflict with the existing Board Support Package (BSP).

-  Copy each of the files: wdfsimple.inf and wdfsimple.sys into a test folder, for example, C:\wdfsimple\.

## Build a package for your driver

1.  Run **C:\\IoT-ADK-AddonKit\\IoTCoreShell** as an administrator.

2.  Create the driver package using the .inf file as the base:

    ```
    newdrvpkg C:\wdfsimple\wdfsimple.inf Drivers.Toaster
    ```

    The new folder appears at **C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Packages\\Drivers.Toaster\\**.


**Verify that the sample files are in the package**

1.  Update the driver's package definition file, **C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Packages\\Drivers.Toaster\\Drivers.Toaster.wm.xml**.

    The default package definition file includes sample XML that you can modify to add your own driver files.
    
    ``` xml
    <?xml version="1.0" encoding="utf-8"?>
    <identity xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        name="HelloBlinky"
        namespace="Drivers"
        owner="$(OEMNAME)"
        legacyName="$(OEMNAME).Drivers.Toaster" xmlns="urn:Microsoft.CompPlat/ManifestSchema.v1.00">
        <onecorePackageInfo
            targetPartition="MainOS"
            releaseType="Production"
            ownerType="OEM" />
        <drivers>
            <driver>
                <inf source="wdfsimple.inf" />
            </driver>
        </drivers>
    </identity>
    ```

2.  From the IoT Core Shell, build the package.

    ```
    buildpkg Drivers.Toaster
    ```

    The package is built, appearing as **C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\pkgs\\&lt;your OEM name&gt;.Drivers.Toaster.cab**.

    
## Update your feature manifest


**Add your driver package to the feature manifest**

1.  Open the architecture-specific feature manifest file, **C:\\IoT-ADK-AddonKit\\Source-_<arch_>\\Packages\\OEMFM.xml**

2.  Create a new PackageFile section in the XML with your package file listed and give it a new FeatureID, such as "DRIVERS_TOASTER".

    ``` xml
          <PackageFile Path="%PKGBLD_DIR%" Name="%OEM_NAME%.Drivers.Toaster.cab">
            <FeatureIDs>
              <FeatureID>DRIVERS_TOASTER</FeatureID>
            </FeatureIDs>
          </PackageFile>
    ```

    You'll now be able to add your driver to your product by adding a reference to this feature manifest.

## Update the project's configuration files

1.  Open your product's test configuration file: **C:\\IoT-ADK-AddonKit\\Source-_<arch_>\\Products\\ProductB\\TestOEMInput.xml**.

2.  Make sure your feature manifest, Rpi2FM.xml, is in the list of AdditionalFMs. Add it if it isn't there already there:

    ``` xml
    <AdditionalFMs>
      <!-- Including BSP feature manifest -->
      <AdditionalFM>%BLD_DIR%\MergedFMs\RPi2FM.xml</AdditionalFM>
      <!-- Including OEM feature manifest -->
      <AdditionalFM>%BLD_DIR%\MergedFMs\OEMCommonFM.xml</AdditionalFM>
      <AdditionalFM>%BLD_DIR%\MergedFMs\OEMFM.xml</AdditionalFM>
       <!-- Including the test features -->
       <AdditionalFM>%AKROOT%\FMFiles\arm\IoTUAPNonProductionPartnerShareFM.xml</AdditionalFM>
    </AdditionalFMs>
    ```


3.  Add the FeatureID for your driver:

    ``` xml
    <OEM>
      <!-- Include BSP Features -->
      <Feature>RPI2_DRIVERS</Feature>
      <Feature>RPI3_DRIVERS</Feature>
      <!-- Include OEM features-->
      <Feature>CUSTOM_CMD</Feature>
      <Feature>PROV_AUTO</Feature>
      <Feature>CUSTOM_FilesAndRegKeys</Feature>
      <Feature>DRIVERS_TOASTER</Feature> 
    </OEM>
    ```

## Build and test the image

Build and flash the image using the same procedures from [Lab 1a: Create a basic image](create-a-basic-image.md). Short version:

1.  From the IoT Core Shell, build the image (`buildimage ProductB Test`).
2.  Install the image: Start **Windows IoT Core Dashboard** > Click the **Setup a new device** tab >  select **Device Type: Custom** >
3.  From **Flash the pre-downloaded file (Flash.ffu) to the SD card**: click **Browse**, browse to your FFU file (C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\ProductB\\Test\\Flash.ffu), then click **Next**.
4.  Enter device name and password. Put the Micro SD card in the device, select it, accept the license terms, and click *Install**. 
5.  Put the card into the IoT device and start it up.

**Check to see if your driver works**

1.  Use the procedures in the [Hello, Blinky! lab](https://developer.microsoft.com/windows/iot/samples/driverlab3) to test your driver.

## Next steps

[Lab 1f: Build a retail image](build-retail-image.md)
