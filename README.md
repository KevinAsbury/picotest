### Raspberry Pi Pico - LED Blink

The first program for pi pico that everyone writes.

## Links
[Buy A Raspberry Pi Pico](https://www.raspberrypi.com/products/raspberry-pi-pico/)
[Pico Datasheet](https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf)
[Pico C SDK PDF](https://datasheets.raspberrypi.com/pico/raspberry-pi-pico-c-sdk.pdf)
[Pico SDK Doxygen](https://raspberrypi.github.io/pico-sdk-doxygen/)
[Pico SDK Github](https://github.com/raspberrypi/pico-sdk)

## Requirements (Mac Os)
```bash
brew install cmake
brew tap ArmMbed/homebrew-formulae
brew install arm-none-eabi-gcc
git clone -b master https://github.com/raspberrypi/pico-sdk.git
```

## Path
```bash
export PICO_SDK_PATH=path_to/repo/pico-sdk
```

## Compile and Run
```bash
mkdir build
cd build
cmake ..
make
```

## Recomended VSCode Extensions
- CMAKE by Microsoft
- CMAKE Tools by Microsoft
- C/C++ by Microsoft
- C/C++ Extension Pack by Microsoft
- C/C++ Themes by Microsoft
- Cortex-Debug by Marus25

## Examples
```bash
git clone -b master https://github.com/raspberrypi/pico-examples.git
```

## Installing OpenOCD
OpenOCD (On Chip Debugging) is used to translate commands from gdb (debugger) to SWD.
This requires a second Pico device setup for debugging. If you are using a Raspberry Pi, then you can just connect via the GPIO pins and not need the extra Pico in the mix.

```bash
brew install libtool automake libusb wget pkg-config gcc texinfo
echo 'export PATH="/usr/local/opt/texinfo/bin:$PATH"' >> ~/.zshrc
git clone https://github.com/raspberrypi/openocd.git --branch picoprobe --depth=1
cd openocd
./bootstrap
./configure --enable-picoprobe --disable-werror
make
make install
openocd -v
```

## Install Picoprobe
picoprobe is used to connect the Mac via USB to the Raspberry Pi Pico that will then be attached to the Pico that is being debugged. If you are using a Raspberry Pi, then you can just connect via the GPIO pins and not need the extra Pico in the mix.

```bash
git clone https://github.com/raspberrypi/picoprobe.git
cd picoprobe
mkdir build
cd build
cmake ..
make
```

See Appendix A: Using Picoprobe from the official getting started guide for the wiring diagram.

## Installing gdb 
Joking, you already have it!

```bash
which arm-none-eabi-gdb
```

## Test debug
Run OpenOCD and confirm that it can attach to the Pico being debugged. Ensure the two Picos are wired up correctly.

```bash
openocd -f interface/picoprobe.cfg -f target/rp2040.cfg -s tcl
...
Info : rp2040.core0: hardware has 4 breakpoints, 2 watchpoints
Info : rp2040.core1: hardware has 4 breakpoints, 2 watchpoints
Info : starting gdb server for rp2040.core0 on 3333
Info : Listening on port 3333 for gdb connections
```

Open another terminal while OpenOCD runs
```bash
cd <YOUR_PATH>/blink/
rm -rf build
mkdir build
cd build
export PICO_SDK_PATH=path_to/repo/pico-sdk
cmake -DCMAKE_BUILD_TYPE=Debug ..
make
```

Now run GDB and load the blink.elf file that was output above
```bash
arm-none-eabi-gdb hello_serial.elf
```

Connect OpenOCD to this gdb session
```bash
(gdb) target remote localhost:3333
(gdb) load
```

Set a breakpoint to stop when main() is called and run the program.
```bash
(gdb) monitor reset init
(gdb) b main
...
Breakpoint 1 at 0x1000030e: file .../blink/blink.c, line 3.

(gdb) continue
...
target halted due to debug-request, current mode: Thread
xPSR: 0x01000000 pc: 0x00000178 msp: 0x20041f00

Thread 1 hit Breakpoint 1, main ()
    at .../blink/blink.c:3
3	    stdio_init_all();
```
That's it! Type `q` or `quit` to exit GDB.

Congratulations! 🥳 🎉 You can now build, run and debug a Raspberry Pi Pico from your Mac.