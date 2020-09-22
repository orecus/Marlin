# README

Thanks to **Anders Sahlman** and **Fox Ies** in the Facebook group for their hardwork in getting Marlin 2.x and figuring out UART mode for the HiSpeed FLSUN board.

-----------

Select the file you want to use and rename it to `Robin_mini.bin` and put it on your SD card, then insert it into your printer to update the firmware.

This branch is updated to work with the latest PlattformIO release and is based upon the official `bugfix-2.0.x` branch. Future firmwares will be updated as progress is made there and the changes are merged into this `repository`, when new firmwares are built and provied you will see which date and CommitId from the official Marlin repository it was generated on.

To build the firmware yourself, you need Visual Studio Code with the PlattformIO extension. Once setup, make the changes you want and from the PIO tab build either `env:flsun_hispeed` or `env:mks_robin_mini` after you made the applicable changes in `configuration.h` depending on which board and settings you use.

# Firmware Files
Different versions of Firmware for the FLSUN Printers. For TMC2208 on the HiSpeed Board you need to do hardware modifications. Follow this [VIDEO](https://www.youtube.com/watch?v=7ShcFKXrVHo).

## **QQ-S PRO (TMC2208 UART, E3D V6, E3D Hemera, Linear Advance)**
Preconfigured for Genuine E3D Hemera Extruder and Genuine E3D V6 Hotend with TMC2208 drivers (HiSpeed Board), modified to support UART.

### `QQ-S PRO (TMC2208 UART, E3D V6, E3D Hemera, Linear Advance) - 2020-09-22.bin`
- Build Date: 2020-09-22
- Marlin v2.0.6.1
  - Branch: `bugfix-2.0.x`
  - CommitId: `d93471fdad8ebabc460a7eb697c3ac61ea2617eb`
- **Settings:** TMC2208 UART, Linear Advance, UBL, TFT 320x240 Display

### `QQ-S PRO (TMC2208 UART, E3D V6, E3D Hemera, Linear Advance) - 2020-09-17.bin`
- Build Date: 2020-09-17
- Marlin v2.0.6.1
  - Branch: `bugfix-2.0.x`
  - CommitId: `d93471fdad8ebabc460a7eb697c3ac61ea2617eb`
- **Settings:** TMC2208 UART, Linear Advance, UBL, FSMC Display

## **QQ-S PRO (TMC2208 UART, Stock, Linear Advance)**
Preconfigured for stock Hotend and Extruder with TMC2208 drivers (HiSpeed Board), modified to support UART.

### `QQ-S PRO (TMC2208 UART, Stock, Linear Advance) - 2020-09-22.bin`
- Build Date: 2020-09-22
- Marlin v2.0.6.1
  - Branch: `bugfix-2.0.x`
  - CommitId: `d93471fdad8ebabc460a7eb697c3ac61ea2617eb`
- **Settings:** TMC2208 UART, Linear Advance, UBL, TFT 320x240 Display

### `QQ-S PRO (TMC2208 UART, Stock, Linear Advance) - 2020-09-17.bin`
- Build Date: 2020-09-17
- Marlin v2.0.6.1
  - Branch: `bugfix-2.0.x`
  - CommitId: `d93471fdad8ebabc460a7eb697c3ac61ea2617eb`
- **Settings:** TMC2208 UART, Linear Advance, UBL, FSMC Display

# Setup

1. Start by initializing EEPROM
   * Configuration/Advanced Settings/Initialize EEPROM
2. Mount the leveling switch, leveling plate is not recommended but _could_ work
3. Start the delta calibration
   * Configuration/Delta Calibration/Auto Calibration
4. Start bed leveling
   * Motion/Level Bed (See the `UBL Calibration` section for more details)
5. Remove the leveling switch.
6. Turn off soft end stops to make it possible to move z below zero
   * Motion/Move Axis/Soft Endstops
7. Move the nozzle down until it is at a paper from the bed
   * Motion/Move Axis/Move Z
8. Now note the Z height that the printer is at, should be negative, and use this value as Probe offset.
   * Configuration/Probe Z offset
   * As the menu goes 0.01 mm per touch this can be easier to do if you have it connected to the USB with pronterface or Octoprint, then this is done with M851, for example if the offset is 8.45 mm then send 'M851 Z-8.45'
9. Now configuration is done, save to EEPROM (Configuration/Store Settings)

## General Calibration

There have been great discussions about calibration, check this post [here](https://www.facebook.com/groups/120961628750040/?post_id=593987384780793) or this one [here](https://www.facebook.com/groups/120961628750040/permalink/722770495235814).

1. Do an [E-Step Calculation](https://www.deviousweb.com/2019/02/16/calculating-e-steps/) as mentioned in the FB post.
2. Do an Dimensional/Delta calibration, there are several options available depending on if you whish to modify the firmware further, or stick with the options available within it.
   * The 100mm cube calibration, as mentioned in the FB post which can be done trough the UI of these firmwares.
   * **Advanced** Delta Printer Calibration (Tower position + Individual diagonal rod) [here](https://www.thingiverse.com/thing:745523).
   * **Advanced** Delta dimensional calibration tool [here](https://www.thingiverse.com/thing:1274733/comments).
   * **Advanced** Octoprint with DuCalibrator [here](https://github.com/Fabi0San/DuCalibrator).
3. [THIS](https://teachingtechyt.github.io/calibration.html#intro) is a great resource for general calibration too, worth checking out!

### UBL Calibration (Bed Level)
Below is the general steps I did to get a good Bed Level Calibration done, further documentation is available [here](https://marlinfw.org/docs/features/unified_bed_leveling.html). Alternativly you can setup the Plugin mentioned below for Octoprint to make it easier/faster.

```
G28             ; Home XYZ.
G29 P1          ; Do automated probing of the bed.
G29 P3 T        ; Repeat until all mesh points are filled in and there are no empty values (dots) displayed.

G29 T           ; View the Z compensation values to verify that all looks good.
G29 S1          ; Save UBL mesh points to EEPROM.
G29 F 10.0      ; Set Fade Height for correction at 10.0 mm.
G29 A           ; Activate the UBL System.
M500            ; Save current setup. WARNING: UBL will be active at power up, before any G28.

M420 S1         ; Activate & Load UBL with validated Mesh
M501            ; Verify that UBL is loaded and active.
```
As per the documentation [here](https://marlinfw.org/docs/features/auto_bed_leveling.html?fbclid=IwAR14GjhyObWm4P7VEvva1m216vBuqvLtZYc3IBgcZ-nL-K1rDtyLL47C0Qk) I also added `M420 S1` in Cura under Printer settings in the Start Code section, also mentioned in the FB post.

### Linear Advanced
For LA follow the steps [here](https://teachingtechyt.github.io/calibration.html#linadv), I've added the LA configuration in my start code in Cura after the Mesh Load mentioned above. This resource is also great for generic calibration of your printer.

## Per Filament Calibrations

### Flow Rate
I've found that following and doing a Flow Rate calibration per material to really improve quality. I've followed [this](https://e3d-online.dozuki.com/Guide/Flow+rate+(Extrusion+multiplier)+calibration+guide./89?lang=en) guide from E3D with good results.

### Retraction
I've used [this](https://www.thingiverse.com/thing:2563909) retraction test once the flow rate has been set, focusing on three settings. Retraction Distance, Retraction Speed and Temperature.

### General Test Prints
I usually round of the "Per Filament Calibration" by printing the following tests:

 * [Calibration Cube #1](https://www.thingiverse.com/thing:2882777)
 * [Calibration Cube #2](https://www.thingiverse.com/thing:1278865)
 * [Calibration Cat](https://www.thingiverse.com/thing:1545913)
 * [3d-Benchy](https://www.thingiverse.com/thing:763622)

### Torture Test
 * There are several available, but I tend to use [this](https://www.thingiverse.com/thing:1363023) one for a more complete test.

# Octoprint Plugins
Below is a list fo great plugins for Octoprint that I can recomend checking out.

* Bed Visualizer
  * If your are using UBL, set the GCODE in the settings to `G29 T`.
* DuCalibrator
  * Set the GCODE Start commands to only `G28`.
* OctoPrint-WideScreen
* Opitemp Plugin
* Preheat Button
* Terminal Commands
  * If you configure the below commands, you can run them in order from the ```Terminal``` tab to calibrate your printer.
  * Delta Calibration      ```G33```
  * UBL - Probe            ```G28; G29 P1```
  * UBL - Correct Points   ```G29 P3 T```
  * UBL - Verify           ```G29 T```
  * UBL - Save             ```G29 S1; G29 F 10.0; M500```
  * UBL - Activate         ```G29 A; M420 S1```
  * Configuration          ```M501```
  * Save Configuration     ```M500```

* USBControl
  * Use this plugin if your printer is ON when the main power supply is turned off, it gets power from the USB cable and with this Plugin you can turn this off.

# Configuration Options

The following options are defined and can be commented out to disabled or commented in to enable their respective functionality in ```./Marlin/Configuration.h```

```c
//-------Hardware--------
/*    ONLY ONE CAN BE SELECTED    */
//#define STOCK                         //QQ-S with Stock board and A4988 Drivers
//#define QQS                           //QQ-S with FLSUN HiSpeed board and A4988 Drivers
//#define QQS_TMC                       //QQ-S with FLSUN HiSpeed board and TMC2208 in Standalone mode
#define QQS_UART                      //QQ-S with FLSUN HiSpeed board and TMC2208 modified to support UART

/*    Modules       */
//#define ESP_WIFI                      //WIFI Module ESP8266/ESP12, can not be used with QQS_UART.

/*    Speed       */
/*    ONLY ONE CAN BE SELECTED    */
#define SPEED_SLOW                      //(100 * 40) //4000
//#define SPEED_NORMAL                    //(100 * 60) //6000
//#define SPEED_FAST                      //(100 * 80) //8000

/*    Hotend / Extruder       */
//#define HOTEND_E3D_V6                 //Preconfigured temperature ranges for a Genuine E3D V6 Hotend, if not used a stock hotend is expected.

/*    ONLY ONE CAN BE SELECTED    */
//#define EXT_BMG                       //Preconfigured for a BMG Extruder
//#define EXT_E3D_HEMERA                //Preconfigured for a Genuine E3D HEMERA Extruder
#define EXT_STOCK                     //Preconfigured for stock extruder on QQS-PRO

/*-------OPTIONS---(FSMC)-----*/
/*    ONLY ONE CAN BE SELECTED    */
//#define FSMC_GRAPHICAL_TFT            //STANDARD UI
#define TFT_320x240                   //MARLIN UI
//#define TFT_LVGL_UI_FSMC              //MKS UI => (Not tested)

//Choice menu: (OPT)
#define DELTA_CALIBRATION_MENU          //Menu Options for starting Delta Configuration
#define PID_EDIT_MENU                   //Menu Options for PID Configuration
#define PID_AUTOTUNE_MENU               //Menu Options for PID Autotune Configuration
//#define PAUSE_BEFORE_DEPLOY_STOW        //Message Stow/Remove Probe (Buggy with Marlin UI)

//  Type Calibration (CAL)
/*    ONLY ONE CAN BE SELECTED    */
//#define AUTO_BED_LEVELING_BILINEAR     //Bilinear Bed Leveling
#define AUTO_BED_LEVELING_UBL          //UBL Bed Leveling

//Many options for Modules:
#define POWER_LOSS_RECOVERY            //Power Loss support when printing from SD-Card
//#define FILAMENT_RUNOUT_SENSOR         //Enables support for Filament Runout Sensors
#define ADVANCED_PAUSE_FEATURE         //Enables Advanced Pause features
#define LIN_ADVANCE                    //Enables Linear Advance. Possible Bug with BabyStep. When enabled, SPREADCYCLE is automaticlly set for Extruder, otherwise STEALTHCHOP will be used on all drivers.

// Option for Octoprint:
#define HOST_ACTION_COMMANDS           //Action Command Prompt support Message on Octoprint
```
