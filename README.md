﻿# PIO + GD32F350CB

Test project for the GigaDevices GD32F350CB using PlatformIO

# **DO NOT USE** 
# This repository is outdated since a new project was started that supports GigaDevice (a lot of them) in a much much better way!
# Read through https://github.com/platformio/platformio-core/issues/3927#issuecomment-888465652

# Installation

A newly created project (not needed when cloning this project directly) can specify usage of my package by adding 

```ini
[env]
; globally override framework-spl for all environments.
platform_packages = 
    maxgerhardt/framework-spl@2.10302.0
```

to the `platformio.ini`. (PS: 2.10302.0 was submitted today and it will take some time)

Another way would be to reference the git repo source. 

```
[env]
; globally override framework-spl for all environments.
platform_packages = 
    framework-spl@https://github.com/maxgerhardt/pio-framework-spl-with-gd32
```

This `framework-spl` version contains `GD32F3x0_Firmware_Library_V2.0.2` to support this `GD32F350CB` as well as `GD32F1x0_Firmware_Library_v3.1.0` (to support `GD32F130`) and `GD32F10x_Firmware_Library_V2.1.2` (to support e.g., `GD32F103`).

Additionally of course, one needs the appropriate board definition file from the `boards/` folder and set that as `boards = ..` value, as well es choosing `framework = spl` in the `platformio.ini`.  

To compile this project, simply clone it and execute `pio run` un it. 

# IDE export 

Use `pio init --ide=<ide here>`. See [docs](https://docs.platformio.org/en/latest/userguide/project/cmd_init.html).

# Flashing

Flash the compiled `.pio\build\gd32f350cbt6\firmware.bin` after compiling using some program or an STLink (default). Run `pio run -t upload` to attempt an OpenOCD upload via an STLink with a STM32F3 target setting (no idea if this works).

# Expected Firmware Behavior

Connect a LED to the PA0 pin. It should blink every 1 second.

Uncomment the other functions in `main()` to e.g. test the UART output (default TX on PA9, RX on PA10, 115200 baud) and GPIO input (default PA4).

# Clock init 

Starts from internal 8MHz oscillator and then uses its PLL to go to 108MHz. Selection is done statically in `system_gd32f3x0.c` in the `framework-spl` folder.

One can define the other macros to change this, see code

```cpp
//#define __SYSTEM_CLOCK_8M_HXTAL              (__HXTAL)
//#define __SYSTEM_CLOCK_8M_IRC8M              (__IRC8M)
//#define __SYSTEM_CLOCK_72M_PLL_HXTAL         (uint32_t)(72000000)
//#define __SYSTEM_CLOCK_72M_PLL_IRC8M_DIV2    (uint32_t)(72000000)
//#define __SYSTEM_CLOCK_84M_PLL_HXTAL         (uint32_t)(84000000)
//#define __SYSTEM_CLOCK_84M_PLL_IRC8M_DIV2    (uint32_t)(84000000)
//#define __SYSTEM_CLOCK_96M_PLL_HXTAL         (uint32_t)(96000000)
//#define __SYSTEM_CLOCK_96M_PLL_IRC8M_DIV2      (uint32_t)(96000000)
//#define __SYSTEM_CLOCK_96M_PLL_IRC48M_DIV2     (uint32_t)(96000000)
//#define __SYSTEM_CLOCK_108M_PLL_HXTAL        (uint32_t)(108000000)
#ifndef PLATFORMIO_GD32_DONT_SET_CLOCK
#define __SYSTEM_CLOCK_108M_PLL_IRC8M_DIV2   (uint32_t)(108000000)
#endif
```

Either use e.g. `build_flags = -DPLATFORMIO_GD32_DONT_SET_CLOCK -D__SYSTEM_CLOCK_108M_PLL_HXTAL=108000000UL` in the `platformio.ini` to active other flags (in this case: 108MHz from standard 8MHz crystal and PLL) or edit the above referenced file manually. 
