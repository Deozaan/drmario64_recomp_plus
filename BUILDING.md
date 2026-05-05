# Building Dr. Mario 64 Recompiled

## You Will Need

- Dr. Mario 64 (USA) ROM (SHA1 `A130D3622CE40E0158DB2DA4247101F6E92206FC`) as `drmario64.us.z64`.

## Linux (Debian/Ubuntu-based)

If you don't have a Debian-based distro (Ubuntu, Mint, etc.) then you can use distrobox to create a container of one.

Save the following script in a directory where you want the compiled binary to go (e.g., `~/DrMario64Recompiled/`, and place the ROM file in the same folder, then run the script and everything should compile.

If you save the script as `doctor.sh` then you would run it like so: `bash doctor.sh`

The script will verify the correct ROM exists and has the expected filename and checksum hash, then it will set to work at (re)compiling the game. To give you an idea of how long it might take: on my ~15-year-old Intel Core i7 2600K machine running Linux Mint in a virtual machine under Windows 10, it took ~30 minutes to complete the process.

Once it finishes you may run `DrMario64Recomp` to play the game.

```bash
#!/bin/bash

# make sure correct ROM is in correct location
EXPECTED_CHECKSUM="a130d3622ce40e0158db2da4247101f6e92206fc"
ROM_FILENAME="drmario64.us.z64"

if [ ! -f "$ROM_FILENAME" ]; then
    echo "ERROR: Make sure $ROM_FILENAME is in the same directory as this bash script."
    exit 1
else
    ACTUAL_CHECKSUM=$(shasum "$ROM_FILENAME" | awk '{print $1}')
    if [ "$ACTUAL_CHECKSUM" != "$EXPECTED_CHECKSUM" ]; then
        echo "ERROR: Checksum of ROM doesn't match expected result. Be sure to use the USA ROM."
        exit 1
    fi
fi

# make a temporary staging directory for the cloned repositories
mkdir repos && cd repos

# install packages
echo "Making sure required packages are installed."
sudo apt update
sudo apt install make cmake git build-essential wget clang binutils-mips-linux-gnu gcc-mips-linux-gnu libgtk-3-dev ninja-build libsdl2-dev lld llvm -y
# install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# clone repositories + submodules
echo "Cloning repositories and submodules, this may take a while."
git clone --depth 1 --recurse-submodules --shallow-submodules https://github.com/Deozaan/drmario64_recomp_plus.git drmrecomp
git clone --depth 1 https://github.com/AngheloAlf/drmario64.git drmdecomp
git clone --depth 1 --recurse-submodules --shallow-submodules https://github.com/N64Recomp/N64Recomp.git

# build N64Recomp dependencies and copy them to recomp
echo "Building N64 Recomp"
cd N64Recomp
cmake -S . -B build
cmake --build build -j$(nproc)
cp build/N64Recomp ../drmrecomp/
cp build/RSPRecomp ../drmrecomp/
cd ..

# decompress ROM
echo "Decompressing ROM"
cp ../drmario64.us.z64 drmdecomp/config/us/baserom.us.z64
cd drmdecomp
uv sync
make setup
cp config/us/baserom_uncompressed.us.z64 ../drmrecomp/drmario64_uncompressed.us.z64
cd ..

# build the recomp
echo "Building the recomp... this might take a long time..."
cd drmrecomp
./N64Recomp drmario64.us.toml
./RSPRecomp aspMain.us.toml
cmake -S . -B build -DCMAKE_CXX_COMPILER=clang++ -DCMAKE_C_COMPILER=clang -G Ninja -DCMAKE_BUILD_TYPE=Release
time cmake --build build --target drmario64_recomp -j$(nproc) --config Release
cd ..

# copy the binary to the local directory
cp drmrecomp/build/drmario64_recomp ../DrMario64Recomp
cp -r drmrecomp/assets ../

# clean up (erase) all other files
echo "Erasing downloaded files"
cd ..
touch portable.txt
#du -sh repos/
rm -rf repos/
ls -lh

```
