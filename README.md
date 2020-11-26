# RTEMS Test Repository

Just a personal test repository to get familiar with RTEMS.
It configured and built the RTEMS toolchain in the same path in
a folder named toolchain but I do not include them in the repo. 

There is a small helper file I use to add the built `erc32` BSP
to the path. It can be used like that

```sh
source pathhelper
```

## Demo Application

### Build demo application

The demo application is located inside the hello folder in the application folder.
It is compiled and can be run on a host computer with the `erc32-sis` simulator.

After installing the `sparc/erc32` BSP (instruction can be read in toolchain README), perform following steps to build the demo application.
The `$RTEMS_INST` variable shoulde be set to the RTEMS toolchain location, for example by running `export RTEMS_INST=$(pwd)/toolchain/rtems/6`

```sh
cd applications/hello
./waf configure --rtems=$RTEMS_INST --rtems-bsp=sparc/erc32
./waf
```

### Run demo application in simulator

The demo application can be run with the following command

```sh
cd applications/hello
$RTEMS_INST/bin/rtems-run --rtems-bsp=erc32-sis build/sparc-rtems6-erc32/hello.exe
```

## STM32 Test Application

### Build demo application

The STM32 application is located inside the hello_stm32 folder in the appkication folder. It is compiled with the `arm/stm32h7` BSP.
It is assumed that the RTEMS ARM toolchain binaries have been added to the path.

```sh
cd applications/stm32/blinky
./waf configure --rtems=$RTEMS_INST --rtems-bsp=arm/stm32h7
./waf
```

Alternatively, the shell script `build.sh` has been provided to perform all steps at once. 

The resulting binary will be linked for flash memory and can be flashed via OpenOCD, drag and drop or the STM Cube Flash tools

### Use Eclipse and configure to flash with OpenOCD

It is possible to use Eclipse together with the waf build system. OpenOCD was used to flash the STM32 with the binary and the debug it with `arm-rtems6-gdb` .
A `.project` and `.cproject` file is supplied in the blinky project to have a starting point. It is recommended to copy those from the eclipse folder into the blinky folder and then import the project in Eclipse.

The xPacks OpenOCD software will be used to flash the board. It can be installed with the xpm packet manager.

1. Install npm (NodeJS on Windows)
2. Install xpm
    ```sh
    npm install --global xpm
    ```

3. Install OpenOCD
    ```sh
    xpm install --global @xpack-dev-tools/openocd@latest
    ```
    
4. Install the MCU plugin for Eclipse. After that, check whether the OpenOCD plugin is found in Eclipse by going to Windows &rarr; Preferences &rarr; MCU and check whether the path is found

5. Add the install path of OpenOCD to the system path by adding `export PATH=$PATH:<..../@gnu-mcu-eclipse/open-ocd/<version>/.content/bin>` to the `.profile` file on Linux
