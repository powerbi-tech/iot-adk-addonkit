# Lab 1f: Build a retail image

We'll take our customizations, put them together, and test them in a retail build. 

## Prerequisites

-   [Lab 1a: Create a basic image](create-a-basic-image.md)
-   [Lab 1b: Add an app to your image](deploy-your-app-with-a-standard-board.md)
-   [Lab 1c: Add a file and a registry setting to an image](add-a-registry-setting-to-an-image.md)
-   [Lab 1d: Add networking and other provisioning package settings to an image](add-a-provisioning-package-to-an-image.md)
-   [Lab 1e: Add a driver to an image](add-a-driver-to-an-image.md)

## Add your app to the retail configuration file

1.  Open your product's retail configuration file: **C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Products\\ProductA\\RetailOEMInput.xml**.

2.  Add your feature manifest, OEMFM.xml, into the list of AdditionalFMs. At the same time, add the feature manifest: OEMCommonFM.xml, which contains the OEM\_CustomCmd package that configures your app on the first boot:

    ``` xml
    <AdditionalFMs>
      <!-- Including BSP feature manifest -->
      <AdditionalFM>%BLD_DIR%\MergedFMs\RPi2FM.xml</AdditionalFM>
      <!-- Including OEM feature manifest -->
      <AdditionalFM>%BLD_DIR%\MergedFMs\OEMCommonFM.xml</AdditionalFM>
      <AdditionalFM>%BLD_DIR%\MergedFMs\OEMFM.xml</AdditionalFM>
    </AdditionalFMs>
    ```

3.  Add the FeatureIDs for the your app package, and the OEM\_CustomCmd package.

    ``` xml
    <OEM> 
       <!-- Include BSP Features -->
       <Feature>RPI2_DRIVERS</Feature> 
       <Feature>RPI3_DRIVERS</Feature>
       <!-- Include OEM features -->
       <Feature>CUSTOM_CMD</Feature> 
       <Feature>PROV_AUTO</Feature>
       <Feature>APP_MyUWPApp</Feature>
       <Feature>CUSTOM_FilesAndRegKeys</Feature>
       <Feature>DRIVER_HelloBlinky</Feature> 
    </OEM>
    ```
    
    PROV_AUTO is required to pull in the provisioning package.
	
	CUSTOM_FilesAndRegKeys, APP_MyUWPApp, and DRIVER_HelloBlinky were sample packages added in previous labs.

## Copy in the provisioning package from ProductB into ProductA.

1.  Copy the customizations.xml file from C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Products\\ProductB\\prov to C:\\IoT-ADK-AddonKit\\Source-&lt;arch&gt;\\Products\\ProductA\\prov.

2.  Delete ProductAProv.ppkg file if present.
    

## Build and create the image

1.  [Get a code-signing certificate](https://docs.microsoft.com/windows-hardware/drivers/dashboard/get-a-code-signing-certificate). For the kernel driver signing, Standard Code signing certificate is sufficient. You will require an EV cert to access the Device Update Center in Hardware Dev Center portal.

2. Download a [Cross-Certificates for Kernel Mode Code Signing](https://docs.microsoft.com/en-us/windows-hardware/drivers/install/cross-certificates-for-kernel-mode-code-signing) that matches the CA of the code-signing certificate from the previous step.

3.	Configure the cross-signing certificate to be used for retail signing. Edit setsignature.cmd file to set SIGNTOOL_OEM_SIGN:

    ```
	set SIGNTOOL_OEM_SIGN=/s my /i "Issuer" /n "Subject" /ac "CrossCertRoot" /fd SHA256
	```
	
	-  Issuer        : Issuer of the code-signing certificate (see Certificate -> Details -> Issuer) 
	
	-  Subject       : Subject in the code-signing certificate (see Certificate -> Details -> Subject)
	
	-  CrossCertRoot : Full path of the Cross-Certificate file that was downloaed in step 2.
	
	
4.	From the IoT Core Shell, enable retail signing.

    ```
	retailsign On
	```
	
5.	Rebuild all the packages so that they are retail signed.

    ```
	buildpkg all
	```

    If the BSP drivers/packages are test signed, you need to rebuild them to have retail signature. You can re-sign the cabs and its contents using 

    ```
    re-signcabs.cmd <srccabdir> <dstcabdir>
    ```
	
6.  From the IoT Core Shell, create the image:

    ```
    buildimage ProductA Retail
    ```

    This creates the product binaries at C:\\IoT-ADK-AddonKit\\Build\\&lt;arch&gt;\\ProductA\\Retail\\Flash.FFU.

7.  Start **Windows IoT Core Dashboard** &gt; **Setup a new device** &gt; **Custom**, and browse to your image. Put the Micro SD card in the device, select it, accept the license terms, and click **Install**. This replaces the previous image with our new image.

8.  Put the card into the IoT device and start it up.

    After a short while, the device should start automatically, and you should see your app.

**Check to see if everything is working**

With retail builds, you won't be able to log into the device using the SSH clients or by using the web interface. However, any files and reg keys that your app relies on should still work.

## Next steps

- [Lab 2: Creating your own board support package](create-a-new-bsp.md)
