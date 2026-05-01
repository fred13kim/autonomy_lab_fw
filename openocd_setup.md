# Pico toolchain

These instructions are hastily written for the pico2 as a debugger and pico2 as
main mcu unit. Please be careful with your CMAKE flags when modifying a standard
pico.

## Windows Users:
Install usbipd in powershell with
```{powershell}
winget install usbipd
```
May need to restart computer after here. Then install `wsl-usb-manager`

## pico-sdk
```{bash}
sudo apt update
# Install dependencies
sudo apt install -y jq cmake libtool automake libusb-1.0-0-dev libhidapi-dev \
    libftdi1-dev patchelf autoconf automake texinfo build-essential gcc-arm-none-eabi \
    libnewlib-arm-none-eabi pkg-config

# Create pico directory if haven't already
mkdir -p ~/pico && cd ~/pico
# Clone & install pico-sdk repo here
git clone https://github.com/raspberrypi/pico-sdk.git

```

In your rc file `.bashrc` or `.zshrc` append the following at the end:
```{bash}
export PICO_SDK_PATH=~/pico/pico-sdk
```

## openocd setup
The raspberry pico uses their own fork of openocd let's set it up!

## setup
```{bash}
cd ~/pico
git clone https://github.com/raspberrypi/openocd.git
cd openocd
git submodule update --init --recursive

./bootstrap
./configure --disable-werror --enable-internal-jimtcl
make clean
make
sudo make install

# Also make sure to copy over the udev rules (should be still in the openocd/ directory here)

sudo cp contrib/60-openocd.rules /etc/udev/rules.d/
udevadm control --reload
```

## picotool

```{bash}
cd ~/pico
git clone https://github.com/raspberrypi/picotool.git

cd picotool
mkdir -p build && cd build
cmake ..
make -j4
```

## debugprobe fw binary

```{bash}
cd ~/pico
git clone https://github.com/raspberrypi/debugprobe.git
cd debugprobe
# revert to a previous release as latest fw version does not connect properly with WSL due to USB-DAP changes
git checkout debugprobe-v2.2.3
git submodule update --init --recursive

mkdir -p build && cd build

# !!! Be careful here !!! If using pico (not a pico2) as the debugger, set
# -DPICO_BOARD=pico
cmake -DDEBUG_ON_PICO=ON -DPICO_BOARD=pico2 ..
make -j4

```

## Let's flash

Flashing means loading a program (binary) into memory. Specifically, flash
memory.

First flash debugprobe fw binary onto debugger. We will be using picotool to
load debugprobe binary. Locate the binary then install using the following.

```{bash}
# sudo picotool load -f <path to binary> 
sudo picotool load -f debugprobe_on_pico2.uf2

```

## pico-examples
```{bash}
cd ~/pico

git clone https://github.com/raspberrypi/pico-examples.git
git submodule update --init --recursive
mkdir -p build && cd build

# !!! Again be careful here !!! Since we are building for the pico2 we insert the
# pico2 cmake flag
cmake -DPICO_BOARD=pico2 ..

# Let's make the blink binary
cd blink
make -j4
```

## flash using openocd
```{bash}
# Locate the blink.elf binary and let's use openocd to flash!
# If you haven't loaded proper udev rules, might have to inject sudo perms here
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg -c "adapter speed 5000" -c "program blink.elf verify reset exit"
```

## openocd.cfg
Typing this entire command is very cumbersome IK!

So! Let's make an openocd.cfg file:
[OpenOCD Confile File Guidelines](https://openocd.org/doc/html/Config-File-Guidelines.html)

An example is shown in this repo as `openocd.cfg`
