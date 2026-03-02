# Pico toolchain

These instructions are hastily written for the pico2 as a debugger and pico2 as
main mcu unit. Please be careful with your CMAKE flags when modifying a standard
pico.

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
