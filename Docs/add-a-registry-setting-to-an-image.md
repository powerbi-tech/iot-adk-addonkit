# Lab 1c: Add a file and a registry setting to an image

Files and registry keys that you add to your image often won't be specific to an architecture. For these, we recommend creating a common package that you can use across all of your device architectures.
 
We'll create some test files and registry keys to the image, and again package them up so that they can be serviced after they reach your customers.

We'll add these to the common feature manifest (OEMCommonFM.xml), which is used in x86, x64, and ARM builds, and give it a new feature ID, OEM\_FilesAndRegKeys.

For this lab, we'll start an new product, ProductB, so that later we can use the IoT sample app to get the IP address of our device and verify that our files and reg keys have made it. 

## Prerequisites

See [Get the tools needed to customize Windows IoT Core](set-up-your-pc-to-customize-iot-core.md) to get your technician PC ready.

## Create your test files

-   Create a few sample text files using Notepad, add some random text so that the files are not empty, title them TestFile1.txt and TestFile2.txt.

## Build a package for your test files

1.  Open the IoT Core Shell: run **C:\\IoT-ADK-AddonKit\\IoTCoreShell** as an administrator.

2.  Create a working folder for the registry key and test files, for example:

    ```
    newcommonpkg Registry.FilesAndRegKeys
    ```

    The new folder at **C:\\IoT-ADK-AddonKit\\Common\\Packages\\Registry.FilesAndRegKeys\\**.

### Add sample files to the package

1.  Copy your sample files (TestFile1.txt and TestFile2.txt), into the new folder at **C:\\IoT-ADK-AddonKit\\Common\\Packages\\Registry.FilesAndRegKeys\\**.

2.  Update the package definition file, **C:\\IoT-ADK-AddonKit\\Common\\Packages\\Registry.FilesAndRegKeys\\Registry.FilesAndRegKeys.wm.xml**:

    a.  Remove the comment marks and instructions.

    b.  Update the values of RegKey to include a new KeyName, Name, and Value.

    c.  Update the File Source names to TestFile1.txt and TestFile2.txt. By default, files land in C:\\Windows\\System. To change the destination location, add a DestinationDir and Name.
    
    Variables like $(runtime.root) are defined in C:\\Program Files (x86)\\Windows Kits\\10\\Tools\\bin\\i386\\pkggen.cfg.xml.

    ``` xml
    <onecorePackageInfo
        targetPartition="MainOS"
        releaseType="Production"
        ownerType="OEM" />
    <regKeys>
        <regKey
            keyName="$(hklm.software)\$(OEMNAME)\Test">
            <regValue name="StringValue" type="REG_SZ" value="Test string" />
            <regValue name="DWordValue"  type="REG_DWORD" value="0x12AB34CD" />
            <regValue name="BinaryValue" type="REG_BINARY" value="12ABCDEF" />
        </regKey>
        <regKey
            keyName="$(hklm.software)\$(OEMNAME)\EmptyKey" />
    </regKeys>
    <files>
        <file destinationDir="$(runtime.system32)" source="filename.txt" />
        <file
            destinationDir="$(runtime.bootDrive)\OEMInstall" source="filename2.txt"
            name="filename2.txt" />
    </files>
    ```

2.  From the IoT Core Shell, build the package. (The `BuildPkg All` command builds everything in the source folders.)

    ```
    buildpkg Registry.FilesAndRegKeys
    ```

    The package is built, appearing as **C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\pkgs\\&lt;your OEM name&gt;.Registry.FilesAndRegKeys.cab**.

    All packages that you build appear in your architecture-specific folder. Tip: to quickly rebuild for another architecture, use **setenv &lt;arch&gt;**, then **`BuildPkg All`** to rebuild everything for your other architecture.

    **Troubleshooting**: If you get an error: "The elementRegKeys in namespace 'urn:Microsoft.WindowsPhone/PackageSchema.v8.00' has incomplete content", remove the comments and instructions. If you don't want to include a reg key or a file, then remove these XML elements. 

## <span id="Update_your_feature_manifest"></span><span id="update_your_feature_manifest"></span><span id="UPDATE_YOUR_FEATURE_MANIFEST"></span>Update your feature manifest

1.  Open the common feature manifest file, **C:\\IoT-ADK-AddonKit\\Common\\Packages\\OEMCommonFM.xml**

2.  Create a new PackageFile section in the XML, with your package file listed, and give it a new FeatureID, such as "CUSTOM_FilesAndRegKeys".

    ``` xml
    <Features>
      <OEM>
        <!-- Feature definitions below -->
        <PackageFile Path="%PKGBLD_DIR%" Name="%OEM_NAME%.Registry.FilesAndRegKeys.cab">
          <FeatureIDs>
            <FeatureID>CUSTOM_FilesAndRegKeys</FeatureID>
          </FeatureIDs>
        </PackageFile>
    ```
3. Run `buildfm oem` to generate updated files in the MergedFMs folder. This has to be done every time any time an FM file is modified.

You'll now be able to add your files and registry keys to any of your products by adding a reference to this feature manifest and Feature ID.


## Create a new product

1.  Create a new product folder. 

    ```
    newproduct ProductB rpi2
    ```

## Update your product configuration file

1.  Update the test configuration file C:\\IoT-ADK-AddonKit\\Source-_<arch_>\\Products\\ProductB\\TestOEMInput.xml:

    Make sure the feature manifest: **OEMCommonFM.xml** is included, removing comment marks if necessary.

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

2.  Update the features included in the product: 

    a. Make sure the sample apps are included (especially the IOT_BERTHA app).

    b. Verify that the OEM features: CUSTOM_CMD and PROV_AUTO are present.

    c. Add the FeatureID for your registry package, example: CUSTOM_FilesAndRegKeys.
    
    ``` xml
    <Features>
      <Microsoft>
    
      ...
      
      <!-- Sample Apps, remove this when you introduce OEM Apps -->
      <Feature>IOT_BERTHA</Feature>
      <Feature>IOT_ALLJOYN_APP</Feature>
      <Feature>IOT_NANORDPSERVER</Feature>
      <Feature>IOT_SHELL_HOTKEY_SUPPORT</Feature>
      <Feature>IOT_APPLICATIONS</Feature>
      <Feature>IOT_ENABLE_ADMIN</Feature>
      </Microsoft>
      <OEM>
        <!-- Include BSP Features -->
        <Feature>RPI2_DRIVERS</Feature>
        <Feature>RPI3_DRIVERS</Feature>
        <!-- Include OEM features -->
        <Feature>CUSTOM_CMD</Feature>
        <Feature>PROV_AUTO</Feature>
        <Feature>CUSTOM_FilesAndRegKeys</Feature>
     </OEM>
    ```

## Build and test the image

Build and flash the image using the same procedures from [Lab 1a: Create a basic image](create-a-basic-image.md). Short version:

1.  From the IoT Core Shell, build the image (`buildimage ProductB Test`).
2.  Install the image: Start **Windows IoT Core Dashboard** > Click the **Setup a new device** tab >  select **Device Type: Custom** >
3.  From **Flash the pre-downloaded file (Flash.ffu) to the SD card**: click **Browse**, browse to your FFU file (C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\ProductB\\Test\\Flash.ffu), then click **Next**.
4.  Enter device name and password.Put the Micro SD card in the device, select it, accept the license terms, and click **Install**. 
5.  Put the card into the IoT device and start it up.

After a short while, you should see the [IoT test (Bertha) app](https://developer.microsoft.com/windows/iot/samples/iotdefaultapp) which shows basic info about the image.

## See if your files made it

1.  Connect both your technician PC and the device to the same ethernet network. 

    For example, to connect over a wired network, plug in a ethernet cable. To connect directly to the device, plug a network cable directly from your technician PC to the device.   

2.  On the test app, note the IP address, for example, 10.100.0.100. 

3.  On your technician PC, open File Explorer, and type in the IP address of the device with a \\\\ prefix and \\c$ suffix:

    ```
    \\10.100.0.100\c$
    ```

	Use the devicename, the default Administrator account, and password to log on. (Default is: minwinpc\\Administrator / p@ssw0rd)

4.  Check to see if the files exist. Look for:

    \\\\10.100.0.100\c$\\Windows\\system32\\TestFile1.txt

    \\\\10.100.0.100\c$\\OEMInstall\\TestFile2.txt

## See if your registry keys exist

1.  On your technician PC, connect to your device using an SSH client, such as [PuTTY](http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe). For example, use the IP address and port 22 to connect to the device, then log in using the Administrator account and password. (To learn more, see [SSH](https://docs.microsoft.com/windows/iot-core/connect-your-device/SSH).)

2.  From the command line in the SSH client, query the system for the registry key.

    ```
    reg query HKLM\Software\Fabrikam\Test
    ```

    Where Fabrikam is your OEM name. The registry tool should return your test values.

## Next steps

[Lab 1d: Add a provisioning package to an image](add-a-provisioning-package-to-an-image.md)
