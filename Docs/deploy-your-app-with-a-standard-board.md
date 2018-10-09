# Lab 1b: Add an app to your image

We're now going to take an app (like the sample [IoT Core Default](https://github.com/ms-iot/samples/tree/develop/IoTCoreDefaultApp) app), package it up, and create a new image you can load onto new devices. 

For background apps, use the same method to install and run them. Note, only one app can be selected as the startup app, all other apps installed using this method run as background apps.

**Note**  As we go through this manufacturing guide, ProjectA will start to resemble the SampleA image that's in C:\\IoT-ADK-AddonKit\\Source-arm\\Products\\SampleA.

## Prerequisites

We'll use the ProjectA image we created from [Lab 1a: Create a basic image](create-a-basic-image.md).

## Create an appx package

You can skip these steps if you've already created and tested your app.

1.  Create a UWP app. This can be any app designed for IoT Core, saved as an Appx Package. For our example, we use the [IoT Core Default](https://github.com/ms-iot/samples/tree/develop/IoTCoreDefaultApp) app.

2.  In Visual Studio, to save the IoT Core Default app as an Appx package, click **Project > Store > Create App Packages** > **No** > **Next**. 

3.  Select **Output location: C:\DefaultApp** (or any other path that doesn't include spaces.)
    
4.  Select **Generate app bundle: Never**
    
5.  Click **Create**.

    Visual Studio creates the Appx file into C:\DefaultApp\IoTCoreDefaultApp_1.2.0.0_ARM_Debug_Test 

6.  Optional: [Test the app](test-the-app.md). Note, you may have already tested the app as part of building the project. 


## Package the app

**Create a package for an app**

1.  Open the IoT Core Shell: run **C:\\IoT-ADK-AddonKit\\IoTCoreShell** as an administrator.

2.  Create a new package for the app, for example:

    ```
    newappxpkg "C:\DefaultApp\IoTCoreDefaultApp_1.2.0.0_ARM_Debug_Test\IoTCoreDefaultApp_1.2.0.0_ARM_Debug_Test.appx" fga Appx.MyUWPApp
    ```

    This creates a new folder at C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Packages\\Appx.MyUWPApp, and generates a customizations.xml file as well as a package xml file that is used to build the package.

3.  From the IoT Core Shell, build the package.

    ```
    buildpkg Appx.MyUWPApp
    ```

    The package is built, appearing as **C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\pkgs\\&lt;your OEM name&gt;.Appx.MyUWPApp.cab**.

## Update the feature manifest


**Add your app package to the feature manifest**

1.  Open your feature manifest file, **C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Packages\\OEMFM.xml**

2.  Create a new PackageFile section in the XML, with your package file listed, and give it a new FeatureID, such as "APP_MyUWPApp".

    ``` xml
    <?xml version="1.0" encoding="utf-8"?>
    <FeatureManifest 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
      xmlns="http://schemas.microsoft.com/embedded/2004/10/ImageUpdate">
      <BasePackages/>
      <Features>
        <OEM>
          <!-- Feature definitions below -->
          <PackageFile Path="%PKGBLD_DIR%" Name="%OEM_NAME%.Appx.IoTCoreDefaultApp.cab">
            <FeatureIDs>
              <FeatureID>APP_IOTCOREDEFAULTAPP</FeatureID>
              <FeatureID>OEM_IoTCoreDefaultApp</FeatureID> <!-- keeping old id for compatibility, dropping OEM prefix -->
            </FeatureIDs>
          </PackageFile>
    	  <PackageFile Path="%PKGBLD_DIR%" Name="%OEM_NAME%.Appx.IoTOnboardingTask.cab">
            <FeatureIDs>
              <FeatureID>APP_IOTONBOARDINGTASK</FeatureID>
              <FeatureID>OEM_IoTOnboardingTask</FeatureID> <!-- keeping old id for compatibility, dropping OEM prefix -->
            </FeatureIDs>
          </PackageFile>
    	  <PackageFile Path="%PKGBLD_DIR%" Name="%OEM_NAME%.Appx.MyUWPApp.cab">
            <FeatureIDs>
              <FeatureID>APP_MyUWPApp</FeatureID>
            </FeatureIDs>
          </PackageFile>	  
        </OEM>
        <OEMFeatureGroups/>
      </Features>
    </FeatureManifest>
    ```


  You'll now be able to add your app to any of your products by adding a reference to this feature manifest and Feature ID.

## Update the project's configuration files

**Replace your product's default app with your own**

1.  Open your product's test configuration file: **C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Products\\ProductA\\TestOEMInput.xml**.

2.  Make sure both your feature manifest, OEMFM.xml, and the feature manifest: OEMCommonFM.xml, are both listed in the AdditionalFMs section.

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

3.  Change the features included in the product: 

    a. Remove the sample test apps by adding comment tags: _<!-- --_>. (We'll use these apps again in later labs.)

    b. Confirm that the OEM features: CUSTOM_CMD, and PROV_AUTO are present. 

    c. Add the FeatureID for your app package, example: OEM_MyUWPApp.
    
    ``` xml
    <Features>
      <Microsoft>
    
      ...
      
     <!-- Sample Apps, remove this when you introduce OEM Apps 
      <Feature>IOT_BERTHA</Feature> -->
      <Feature>IOT_ALLJOYN_APP</Feature>
      <Feature>IOT_NANORDPSERVER</Feature>
      <Feature>IOT_SHELL_HOTKEY_SUPPORT</Feature>
      <Feature>IOT_APPLICATIONS</Feature>

    </Microsoft>
    <OEM>
      <!-- Include BSP Features -->
      <Feature>RPI2_DRIVERS</Feature>
      <Feature>RPI3_DRIVERS</Feature>
      <!-- Include OEM features -->
      <Feature>CUSTOM_CMD</Feature>
      <Feature>PROV_AUTO</Feature>
      <Feature>APP_MyUWPApp</Feature>
    </OEM>
    ```


## Build and test the image

Build and flash the image using the same procedures from [Lab 1a: Create a basic image](create-a-basic-image.md). Short version:

1.  From the IoT Core Shell, build the image (`buildimage ProductA Test`).
2.  Install the image: Start **Windows IoT Core Dashboard** > Click the **Setup a new device** tab >  select **Device Type: Custom** >
3.  From **Flash the pre-downloaded file (Flash.ffu) to the SD card**: click **Browse**, browse to your FFU file (C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\ProductA\\Test\\Flash.ffu), then click **Next**.
4.  Enter the device name and password. Put the Micro SD card in the device, select it, accept the license terms, and click **Install**. 
4.  Put the card into the IoT device and start it up.

After a short while, the device should start automatically, and you should see your app.

## Next steps

[Lab 1c: Add a file and a registry setting to an image](add-a-registry-setting-to-an-image.md)

## Related topics

[Update apps on your IoT Core devices](https://docs.microsoft.com/windows-hardware/service/iot/updating-iot-core-apps)
