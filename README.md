# PSoC 6 MCU: CapSense Buttons and Slider (FreeRTOS)

## Overview

This code example features a 5-segment linear slider and two CapSense buttons. Button 0 turns the LED on, Button 1 turns the LED off, and the slider controls the brightness of the LED. The code example also demonstrates interfacing with Tuner GUI using I2C interface. This project uses the [CapSense Middleware Library](https://github.com/cypresssemiconductorco/capsense).  

This code example uses FreeRTOS. Visit the [FreeRTOS website](https://www.freertos.org) for documentation and API references of FreeRTOS.

## Requirements

- [ModusToolbox™ IDE](https://www.cypress.com/products/modustoolbox-software-environment) v2.0  
- Programming Language: C  
- Associated Parts: All PSoC® 6 MCU [parts](http://www.cypress.com/PSoC6)  

## Supported Kits

- [PSoC 6 Wi-Fi BT Prototyping Kit](https://www.cypress.com/CY8CPROTO-062-4343W) (CY8CPROTO-062-4343W) - Default target
- [PSoC 6 WiFi-BT Pioneer Kit](https://www.cypress.com/CY8CKIT-062-WiFi-BT) (CY8CKIT-062-WiFi-BT)
- [PSoC 6 BLE Pioneer Kit](https://www.cypress.com/CY8CKIT-062-BLE) (CY8CKIT-062-BLE)
- [PSoC 62S2 Wi-Fi BT Pioneer Kit](https://www.cypress.com/CY8CKIT-062S2-43012) (CY8CKIT-062S2-43012)

## Hardware Setup
This example uses the board's default configuration. See the kit user guide to ensure the board is configured correctly.

**Note**: The PSoC 6 BLE Pioneer Kit and the PSoC 6 WiFi-BT Pioneer Kit ship with KitProg2 installed. ModusToolbox software requires KitProg3. Before using this code example, make sure that the board is upgraded to KitProg3. The tool and instructions are available in the [Firmware Loader](https://github.com/cypresssemiconductorco/Firmware-loader) GitHub repository. If you do not upgrade, you will see an error like "unable to find CMSIS-DAP device" or "KitProg firmware is out of date".

## Software Setup
This example requires no additional software or tools.

## Using the Code Example

### In ModusToolbox IDE

1. Click the **New Application** link in the Quick Panel (or, use **File > New > ModusToolbox IDE Application**).

2. Pick a kit supported by the code example from the list shown in the **IDE Application** dialog.

   When you select a supported kit, the example is reconfigured automatically to work with the kit. To work with a different supported kit later, use the **Library Manager** to choose the BSP for the supported kit. You can use the Library Manager to select or update the BSP and firmware libraries used in this application. To access the Library Manager, right-click the application name from the Project Workspace window in the IDE, and select **ModusToolbox > Library Manager**. For more details, see the IDE User Guide: *{ModusToolbox install directory}/ide_2.0/docs/mt_ide_user_guide.pdf*.

   You can also just start the application creation process again and select a different kit.

   If you want to use the application for a kit not listed here, you may need to update source files. If the kit does not have the required resources, the application may not work.

3. In the **Starter Application** window, choose the example.

4. Click **Next** and complete the application creation process.

 If you are unfamiliar with this process, see [KBA225201](https://community.cypress.com/docs/DOC-15968) for all the details.

### In Command-line Interface (CLI)

1. Download and unzip this repository onto your local machine, or clone the repository.
2. Open a CLI terminal and navigate to the application folder.
3. Import required libraries by executing the command `make getlibs`.

## Operation

1. Connect the board to your PC using the provided USB cable through the USB connector.

2. Program the board.

    ### Using ModusToolbox IDE

    1. Select the application project in the Project Explorer.
    2. In the **Quick Panel**, scroll down, and click **\<Application Name> Program (KitProg3)**.

    ### Using CLI:

    From the terminal, execute the following command to program the board for default target 
    
    ```
    make program 
    ```
    You can specify a target and toolchain manually:
    ```
    make program TARGET=<BSP> TOOLCHAIN=<toolchain>
    ```
    Example:

    ```
    make program TARGET=CY8CKIT-062-WIFI-BT TOOLCHAIN=GCC_ARM
    ```
    **Note**:  Before building the application, ensure the *libs* folder contains the BSP (*.lib*) file corresponding to the TARGET. Execute `make getlibs` to fetch the BSP contents before building the application.

3. After programming, the application starts automatically. Confirm that user LED is glowing.
4. To test the application, touch Button 1 to turn the LED OFF, touch Button 0 to turn the LED ON, and touch the slider in different positions to change the brightness when LED is ON.
5. For monitoring CapSense data, CapSense parameter tuning and SNR measurement, see [CapSense Tuner Guide](https://www.cypress.com/ModusToolboxCapSenseTuner). See [AN85951 – PSoC 4 and PSoC 6 MCU CapSense Design Guide](https://www.cypress.com/an85951) for more details on selecting the right tuning parameters. 


### Debugging

You can debug the example to step through the code. In the ModusToolbox IDE, use the **\<Application Name> Debug (KitProg3)** configuration in the **Quick Panel**. If you are unfamiliar with how to start a debug session with ModusToolbox IDE, see [KBA224621](https://community.cypress.com/docs/DOC-15763).

## Design and Implementation

In this project, PSoC 6 MCU scans a self-capacitance (CSD) based, 5-element CapSense slider and two mutual capacitance (CSX) CapSense buttons for user input. The project uses the CapSense middleware, see [ModusToolbox User Guide](http://www.cypress.com/ModusToolboxUserGuide) for more details on selecting a middleware. See [AN85951 – PSoC 4 and PSoC 6 MCU CapSense Design Guide](https://www.cypress.com/an85951) for more details of CapSense features and usage. Based on the user input, the LED state is controlled. A PWM HAL resource is configured for controlling the brightness of the LED.

The [ModusToolbox CapSense Configurator Tool Guide](https://www.cypress.com/ModusToolboxCapSenseConfig) describes step-by-step instructions on how to configure and launch CapSense in ModusToolbox. The CapSense Configurator Tool can be launched in ModusToolbox IDE from the CSD personality, as well as in stand-alone mode.

The firmware uses FreeRTOS to execute the tasks required by this application. The following tasks are created:
1. CapSense task: Initializes the CapSense Hardware block, processes the touch input, and sends a command to the LED task to update the LED status.
2. LED task: Initializes the TCPWM in PWM mode for driving the LED and updates the status of LED based on the received command. 

A FreeRTOS-based timer is used for making the CapSense scan periodic; a queue is used for communication between the
CapSense task and LED task. FreeRTOSConfig.h contains the FreeRTOS settings and configuration.

#### 1.8 V Operation
The application is by default configured to work with 3.3 V. If you want to work with 1.8 V, update the operating conditions as shown in [Figure 1](#figure-1-power-setting-to-work-with-1.8-v) and change the jumper/switch setting as listed in [Table 1](#table-1-jumper/switch-position-for-1.8-V-operation). 

##### Figure 1. Power Setting to Work with 1.8 V
![](images/SystemPowerCfg.png)
 

##### Table 1. Jumper/Switch Position for 1.8 V Operation

| Kit                 | Jumper/Switch Position         |
|:--------------------|--------------------------------|
| CY8CPROTO-062-4343W | J3 (1-2)                       |
| CY8CKIT-062-BLE     | SW5 (1-2)                      |
| CY8CKIT-062-WIFI-BT | SW5 (1-2)                      |
| CY8CKIT-062S2-43012 | J14 (1-2)                      |

### Related Resources

| Application Notes                                            |                                                              |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [AN221774](https://www.cypress.com/AN221774) – Getting Started with PSoC 6 MCU on PSoC Creator | Describes PSoC 6 MCU devices and how to build your first application with PSoC Creator |
| [AN228571](https://www.cypress.com/AN228571) – Getting Started with PSoC 6 MCU on ModusToolbox | Describes PSoC 6 MCU devices and how to build your first application with ModusToolbox |
| [AN210781](https://www.cypress.com/AN210781) – Getting Started with PSoC 6 MCU with Bluetooth Low Energy (BLE) Connectivity on PSoC Creator | Describes PSoC 6 MCU with BLE Connectivity devices and how to build your first application with PSoC Creator |
| [AN215656](https://www.cypress.com/AN215656) – PSoC 6 MCU: Dual-CPU System Design | Describes the dual-CPU architecture in PSoC 6 MCU, and shows how to build a simple dual-CPU design |
| **Code Examples**                                            |                                                              |
| [Using ModusToolbox IDE](https://github.com/cypresssemiconductorco/Code-Examples-for-ModusToolbox-Software) | [Using PSoC Creator](https://www.cypress.com/documentation/code-examples/psoc-6-mcu-code-examples) |
| **Device Documentation**                                     |                                                              |
| [PSoC 6 MCU Datasheets](https://www.cypress.com/search/all?f[0]=meta_type%3Atechnical_documents&f[1]=resource_meta_type%3A575&f[2]=field_related_products%3A114026) | [PSoC 6 Technical Reference Manuals](https://www.cypress.com/search/all/PSoC%206%20Technical%20Reference%20Manual?f[0]=meta_type%3Atechnical_documents&f[1]=resource_meta_type%3A583) |
| **Development Kits**                                         | Buy at Cypress.com                                     |
| [CY8CKIT-062-BLE](https://www.cypress.com/CY8CKIT-062-BLE) PSoC 6 BLE Pioneer Kit | [CY8CKIT-062-WiFi-BT](https://www.cypress.com/CY8CKIT-062-WiFi-BT) PSoC 6 WiFi-BT Pioneer Kit |
| [CY8CPROTO-063-BLE](https://www.cypress.com/CY8CPROTO-063-BLE) PSoC 6 BLE Prototyping Kit | [CY8CPROTO-062-4343W](https://www.cypress.com/cy8cproto-062-4343w) PSoC 6 Wi-Fi BT Prototyping Kit |
| [CY8CKIT-062S2-43012](https://www.cypress.com/CY8CKIT-062S2-43012) PSoC 62S2 Wi-Fi BT Pioneer Kit | |
| **Libraries**                                                |                |
| Cypress Hardware Abstraction Layer Library and docs          | [psoc6hal](https://github.com/cypresssemiconductorco/psoc6hal) on GitHub |
| RetargetIO - Library for redirecting low level IO commands to allow sending messages via standard printf/scanf functions over a UART connection | [retarget-io](https://github.com/cypresssemiconductorco/retarget-io) on GitHub |
| **Middleware**                                               |                                                              |
| CapSense library and docs                                    | [capsense](https://github.com/cypresssemiconductorco/capsense) on GitHub |
| Links to all PSoC 6 Middleware                               | [psoc6-middleware](https://github.com/cypresssemiconductorco/psoc6-middleware) on GitHub |
| **Tools**                                                    |                                                              |
| [ModusToolbox IDE](https://www.cypress.com/modustoolbox)     | The Cypress IDE for PSoC 6 and IoT designers                 |
| [PSoC Creator](https://www.cypress.com/products/psoc-creator-integrated-design-environment-ide) | The Cypress IDE for PSoC and FM0+ development                |

#### Other Resources

Cypress provides a wealth of data at www.cypress.com to help you to select the right device, and quickly and effectively integrate the device into your design.

For the PSoC 6 MCU devices, see [KBA223067](https://community.cypress.com/docs/DOC-14644) in the Cypress community for a comprehensive list of PSoC 6 MCU resources.

### Version History

Document Title: **CE227127 - PSoC 6 MCU: CapSense Buttons and Slider (FreeRTOS)**

| Version | Description of Change |
| ------- | --------------------- |
| 1.0     | New code example      |

------

All other trademarks or registered trademarks referenced herein are the property of their respective
owners.

![Banner](images/Banner.png)

------

© Cypress Semiconductor Corporation, 2019. This document is the property of Cypress Semiconductor Corporation and its subsidiaries (“Cypress”).  This document, including any software or firmware included or referenced in this document (“Software”), is owned by Cypress under the intellectual property laws and treaties of the United States and other countries worldwide.  Cypress reserves all rights under such laws and treaties and does not, except as specifically stated in this paragraph, grant any license under its patents, copyrights, trademarks, or other intellectual property rights.  If the Software is not accompanied by a license agreement and you do not otherwise have a written agreement with Cypress governing the use of the Software, then Cypress hereby grants you a personal, non-exclusive, nontransferable license (without the right to sublicense) (1) under its copyright rights in the Software (a) for Software provided in source code form, to modify and reproduce the Software solely for use with Cypress hardware products, only internally within your organization, and (b) to distribute the Software in binary code form externally to end users (either directly or indirectly through resellers and distributors), solely for use on Cypress hardware product units, and (2) under those claims of Cypress’s patents that are infringed by the Software (as provided by Cypress, unmodified) to make, use, distribute, and import the Software solely for use with Cypress hardware products.  Any other use, reproduction, modification, translation, or compilation of the Software is prohibited.
TO THE EXTENT PERMITTED BY APPLICABLE LAW, CYPRESS MAKES NO WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, WITH REGARD TO THIS DOCUMENT OR ANY SOFTWARE OR ACCOMPANYING HARDWARE, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.  No computing device can be absolutely secure.  Therefore, despite security measures implemented in Cypress hardware or software products, Cypress shall have no liability arising out of any security breach, such as unauthorized access to or use of a Cypress product.  CYPRESS DOES NOT REPRESENT, WARRANT, OR GUARANTEE THAT CYPRESS PRODUCTS, OR SYSTEMS CREATED USING CYPRESS PRODUCTS, WILL BE FREE FROM CORRUPTION, ATTACK, VIRUSES, INTERFERENCE, HACKING, DATA LOSS OR THEFT, OR OTHER SECURITY INTRUSION (collectively, “Security Breach”).  Cypress disclaims any liability relating to any Security Breach, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any Security Breach.  In addition, the products described in these materials may contain design defects or errors known as errata which may cause the product to deviate from published specifications.  To the extent permitted by applicable law, Cypress reserves the right to make changes to this document without further notice. Cypress does not assume any liability arising out of the application or use of any product or circuit described in this document.  Any information provided in this document, including any sample design information or programming code, is provided only for reference purposes.  It is the responsibility of the user of this document to properly design, program, and test the functionality and safety of any application made of this information and any resulting product.  “High-Risk Device” means any device or system whose failure could cause personal injury, death, or property damage.  Examples of High-Risk Devices are weapons, nuclear installations, surgical implants, and other medical devices.  “Critical Component” means any component of a High-Risk Device whose failure to perform can be reasonably expected to cause, directly or indirectly, the failure of the High-Risk Device, or to affect its safety or effectiveness.  Cypress is not liable, in whole or in part, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any use of a Cypress product as a Critical Component in a High-Risk Device.  You shall indemnify and hold Cypress, its directors, officers, employees, agents, affiliates, distributors, and assigns harmless from and against all claims, costs, damages, and expenses, arising out of any claim, including claims for product liability, personal injury or death, or property damage arising from any use of a Cypress product as a Critical Component in a High-Risk Device.  Cypress products are not intended or authorized for use as a Critical Component in any High-Risk Device except to the limited extent that (i) Cypress’s published data sheet for the product explicitly states Cypress has qualified the product for use in a specific High-Risk Device, or (ii) Cypress has given you advance written authorization to use the product as a Critical Component in the specific High-Risk Device and you have signed a separate indemnification agreement.
Cypress, the Cypress logo, Spansion, the Spansion logo, and combinations thereof, WICED, PSoC, CapSense, EZ-USB, F-RAM, and Traveo are trademarks or registered trademarks of Cypress in the United States and other countries.  For a more complete list of Cypress trademarks, visit cypress.com.  Other names and brands may be claimed as property of their respective owners.