# Windows ADK IoT Core Add-ons: contents

The [Windows 10 IoT Core ADK Add-Ons](https://github.com/ms-iot/iot-adk-addonkit/tree/17134) include OEM-specific tools to create images for your IoT Core devices with your apps, board support packages (BSPs), settings, drivers, and features.

This kit 
- makes IoT Core image creation process easy and simple
- enables creation of multiple images/image variants easily
- provides automation support for nightly builds 


The [IoT Core manufacturing guide](iot-core-manufacturing-guide.md) walks you through building images with these tools.

## Key XML definitions

- Package definitions (*.wm.xml) : defines a component package
- Provisioning definitions (customizations.xml) : source file for provisioning settings
- Feature manifests (*FM.xml) : defines feature composition and feature IDs
- Feature manifest List (*FMList.xml) : enumerates the FM files 
- Product definitions (*OEMInputFile.xml) : specifies the product composition with the Microsoft features and OEM features included in the product

| Name | Filename.ext | ADK tool | build command | Output |
|-----|----|----|---|---|
| Package  | *.wm.xml  | `pkggen.exe` | `buildpkg.cmd`   | *.cab  |
| Provisioning  | customizations.xml  | `icd.exe`  | `buildprovpkg.cmd`   | *.ppkg | 
| Feature manifest  | *FM.xml  | `featuremerger.exe` `imageapp.exe`  | -  | - | 
| Feature manifest list | *FMList.xml  | `featuremerger.exe`  | `buildfm.cmd`  | MergerdFM/*FM.xml , *FIP.cab  |
| Product  | *OEMInputFile.xml  | `imageapp.exe`  | `buildimage.cmd`  | *.ffu |

## Code Architecture

- Root folder
    - IoTCoreShell.cmd: Launches the [IoT Core Shell command-line](iot-core-adk-addons-command-line-options.md#iotcoreshell)
    -   README.md: Version info, links to documentation
- Build
    - This is the output directory where the build contents are stored. It starts as empty.
- Common/Packages
    - Architecture *independent*, platform *independent* packages
    - OEMCommonFM.xml - feature manifest file that enumerates common packages and defines common features.
- Source-\<arch\>
    - Packages
        - Architecture *specific*, platform *independent* packages
        - versioninfo.txt: Current version number.
        - OEMFM.xml - the feature manifest file that enumerates arch specific packages and defines arch specific features.
        - OEMFMList.xml - enumeration of OEM FM files. 
    - BSP
        - \<bspname\>/Packages
            -  Architecture *specific*, platform *specific* packages
            - \<bspname\>FM.xml - feature manifest that enumerates the bsp packages and defines supported device layouts and features
            - \<bspname\>FMList.xml - enumeration of BSP FM files.
         - \<bspname\>/OemInputSamples
            - sample oeminput files demonstrating how to use the bsp, these files are used as templates in `newproduct.cmd`
    - Products
        - architecture specific named products
- Templates
    - Templates used by tools to create new bsp/product
- Tools
    - Includes the  [IoT Core Add-ons command-line tools](iot-core-adk-addons-command-line-options.md) 

## Sample packages
Sample packages are provided in the iot-adk-addonkit that can be used as a reference or as is in your image, if it meets your needs. Few of such packages are listed here.

### Common Packages 

| Package Name | Description |
| ----- | ----- |
|  Custom.Cmd | Package to include the oemcustomization cmd. This is product-specific and picks up the input file from product directory. This also makes an registry entry with the product name. |
| Provisioning.Auto  | Package used to [add a provisioning package to an image](add-a-provisioning-package-to-an-image.md). This is product specific and picks up the input ppkg file from the product directory.  |
| Registry.Version  |  Package containing registry settings with product and version information. |
|  DeviceLayout.GPT4GB | Package with GPT [drive/partition layout](device-layout.md) for UEFI-based devices with 4GB drives.  |
|  DeviceLayout.GPT8GB-R | Package with GPT [drive/partition layout](device-layout.md) for UEFI-based devices with 8GB drives with recovery partition.  |
|  DeviceLayout.MBR4GB | Package with MBR [drive/partition layout](device-layout.md) for legacy BIOS-based devices with 4GB drives.  |
|  DeviceLayout.MBR8GB-R | Package with MBR [drive/partition layout](device-layout.md) for legacy BIOS-based devices with 8GB drives with recovery partition.  |
|  Settings.HotKey | Sample package to demonstrate how to [add a registry setting to an image](add-a-registry-setting-to-an-image.md). Read [Switching between apps](https://docs.microsoft.com/en-us/windows/iot-core/develop-your-app/iotcoreshell#switching-between-apps-with-hid-injection-keys) for more details.   | 
|  Security.SecureBoot | Sample package to include secure boot functionality.  |
|  Security.Bitlocker | Sample package to include bitlocker functionality. |
|  Security.DeviceGuard | Sample package to include deviceguard policies.   |

> [!NOTE]
> The security packages contain test contents and you should replace them with your own device specific content when creating your final image. See [Security, BitLocker and Deviceguard](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) for more details. 

### Applications and Services packages

| Package Name | Description |
| ----- | ----- |
| Appx.IoTCoreDefaultApp | Foreground apps package containing [IoTCoreDefaultApp](https://github.com/ms-iot/samples/tree/develop/IoTCoreDefaultApp), see [description](https://developer.microsoft.com/windows/iot/samples/iotdefaultapp).  |
| Appx.DigitalSign | Foreground apps package containing [DigitalSign](https://github.com/ms-iot/samples/tree/develop/DigitalSign), see [description](https://developer.microsoft.com/windows/iot/samples/digitalsign). |
| Appx.IoTOnboardingTask | Background apps package containing [IoTOnboardingTask](https://github.com/ms-iot/samples/tree/develop/IoTOnboarding), see [description](https://developer.microsoft.com/windows/iot/samples/iotonboarding).  |
| AzureDM.Services | Service package contaiing Azure Device Management |

### BSP
Source files to create board support packages (BSPs). 

Some BSPs are included in each folder as a start. You can [create your own BSPs](create-a-new-bsp.md) based on these packages.

### Driver packages

| Package Name | Description |
| ----- | ----- |
| Drivers.GPIO | Sample package for adding a driver. |

### Products

Source file for product configurations. Use our samples (SampleA, SampleB) or [create your own](iot-core-manufacturing-guide.md).

| Product | Description |
| ----- | ----- |
| SampleA | Product with Microsoft provided features / apps |
| SampleB | Product using OEM Apps and OEM drivers |
| SingleLang | Product with single non english language support |
| MultiLang  | Product with multiple language support |
| SecureSample  | Product using security features  |
| RecoverySample  | Product using recovery mechanism   |
| DigitalSign  | Sample real life product using various features Cortana, recovery mechanism, security features, multi languages support  |


## Related topics

[IoT Core manufacturing guides](iot-core-manufacturing-guide.md)

[IoT Core feature list](https://docs.microsoft.com/windows-hardware/manufacture/iot/iot-core-feature-list)


 

 



