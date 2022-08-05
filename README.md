## Setup guide for PyTorch, Librosa and Scikit-Learn on Raspberry Pi 4B and PiSound Audio Hat 

This guide assumes you have a Raspberry Pi 4B (min 4GB RAM), PiSound audio hat and a free afternoon. 

Requires 32-bit Raspberry Pi OS Lite (headless): https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2022-04-07/2022-04-04-raspios-bullseye-armhf-lite.img.xz 

PiSound software setup: https://blokas.io/pisound/docs/software/#verifying-it-works

Once your hardware, OS and PiSound software are set up, the fun part: 

### Install LLVM 11 
```
sudo apt-get install llvm-11 
LLVM_CONFIG=/usr/bin/llvm-config-11 
```

### Install additional Python tools 
```
sudo apt install libblas-dev m4 cmake python3-dev python3-yaml python3-setuptools python3-wheel python3-pillow
sudo apt-get install python3-pip
sudo pip3 install cython 
```
### Compile Torch & Torchvision (takes a few hours)

```
export NO_CUDA=1
export NO_DISTRIBUTED=1
export NO_MKLDNN=1 
export BUILD_TEST=0
export MAX_JOBS=4
```

```
git clone https://github.com/pytorch/pytorch --recursive && cd pytorch
git checkout v1.7.0
git submodule update --init --recursive
python3 setup.py bdist_wheel
python3 setup.py develop 
```

```
git clone https://github.com/pytorch/vision && cd vision
git checkout v0.8.1
git submodule update --init --recursive
python setup.py bdist_wheel
python setup.py develop 
```
### Install Numba, Scipy, Scikit-Learn, Numpy, Librosa
I thiiink this is the correct order... if you get an error then rest assured it is the correct combination of package versions - just change the order until it works. 
You might also need to install as root. 
```
LLVM_CONFIG=/usr/bin/llvm-config-11 pip3 install numba==0.54
pip3 install scipy==1.7.1
pip3 install scikit-learn==1.0
pip3 install numpy==1.20.3
pip3 install librosa==0.8.0
```
### Create ALSA plugin for diverse sample rates
Necessary because PiSound only supports 48000Hz out-of-the-box 
```
sudo nano .asoundrc
```
Then paste the following and save:
```
pcm.!default {
        type plug
        slave.pcm "qhw"
}

pcm.qhw {
        type hw
        card 1
}

ctl.!default {
        type hw
        card 1
}
```
