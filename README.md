## Objective of Page
To record the procedure of installing different open-source tools and the open-source PDK on the Ubuntu OS.
For those who do not want to go through the installation process, I include the link to download the VirtualBox image [here](https://gohkust-my.sharepoint.com/:u:/g/personal/eescottie_ust_hk/Ebqbb8-q0b5FnH5je9rRQ1QByQP9HLnXeNK9rwZY6Z-3sA).  
Go [here](https://docs.oracle.com/en/virtualization/virtualbox/6.0/user/ovf.html#ovf-import-appliance) to find out how to import the image to VirtualBox.
The image has all the open-source tools and PDK installed.
You can go to [here](https://github.com/eescottie/opensrc_analog) if you want a jumpstart on how to use the tools.

## Ubuntu 22.04 and VirtualBox
To run those open-source tools and use the open-source PDK, we need a linux OS.  **We will use Ubuntu 22.04** (Make sure you download the correct verison).
A virtual machine is needed to run the Ubunut OS assuming your PC is currently running the Windows OS.
Simply follow the steps stated [here](https://ubuntu.com/tutorials/how-to-run-ubuntu-desktop-on-a-virtual-machine-using-virtualbox#1-overview) to run the Ubuntu Desktop using the VirtualBox.
During the setup of VirtualBox, please pay attention to the following two items:
1. Make sure you record down your user password.  We need it during the installation tools and PDK.
2. At the step of "Create Virtual Hard Disk", make sure that you allocate around 30GB as after installation around 20GB is used by the OS, the tools, and the PDK.
Once you can successfully run the Ubuntu OS using VirtualBox, we can now install the tools and the PDK.

## Installation of Open-Source Tools and Open-Source PDK
The following installation instructions are digested from the materials listed in the reference section.
Simply executing the following commands one by one to finish the whole installation process.
Of course you can group all of them into one shell script.  I do think the following can give us a better idea what exactly are installed.

**First, we have to make sure the Ubuntu OS has all necessary libraries to complete the later installation process:**

1a. Updating the package information
```
sudo apt-get update
```
1b. Installing the dependencies
```
sudo apt-get install \
m4 tcsh csh libx11-dev libx11-xcb-dev tcl-dev tcllib swig \
tk-dev libcairo2-dev mesa-common-dev xterm libglu1-mesa-dev \
libncurses-dev build-essential clang bison flex intltool \
libreadline-dev gawk libffi-dev git libgtk-3-dev graphviz \
xdot pkg-config python3 libboost-system-dev vim-gtk3 \
adms autoconf automake libtool libxpm-dev libxaw7-dev \
libssl-dev libboost-python-dev libboost-filesystem-dev \
zlib1g-dev checkinstall --assume-yes
```
1c. Installing the CMAKE
```
sudo snap install cmake --classic
```

**Second, we are going to install different open-source tools:**

2a.  Installing Netgen.

It is used to perform a process called LVS (Layout VS Schematic), checking whether the layout correctly represents the schematic.
```
git clone https://github.com/RTimothyEdwards/netgen
cd netgen/
./configure 
make 
sudo checkinstall --default --pkgname=netgen
cd
```
2b.  Installing Magic.  
It is a layout tool for you to create the layout of your analog IC
```
git clone https://github.com/RTimothyEdwards/magic
cd magic/
./configure
make
sudo checkinstall --default --pkgname=magic
cd
```
2c.  Installing Xschem.  
It is a schematic editor for you to create schematic and generate netlist for simulation
```
git clone https://github.com/StefanSchippers/xschem
cd xschem/
./configure
make
sudo checkinstall --default --pkgname=xschem
cd
```
2d.  Installing NGSPICE.  
It is a simulator used to evaluate the behaviour of your analog circuit.  Be patient, the installation time of NGSPICE is long.
```
git clone git://git.code.sf.net/p/ngspice/ngspice
cd ngspice/
./autogen.sh --adms
mkdir release
cd release 
../configure --with-x --enable-xspice --disable-debug --enable-cider --with-readline=yes --enable-openmp
make
sudo checkinstall --default --pkgname=ngspice
cd
```
2e. Installing modified GAW (If your Ubuntu version is not 22.04, you may have installation error).

It is a analog waveform viewer
```
git clone https://github.com/StefanSchippers/xschem-gaw
cd xschem-gaw
aclocal && automake --add-missing && autoconf
sed -i 's/GETTEXT_MACRO_VERSION = 0.18/GETTEXT_MACRO_VERSION = 0.20/' ./po/Makefile.in.in
./configure
make
sudo checkinstall --default --pkgname=xschem-gaw --pkgversion=0.1
cd
```
**Finally, we will install the open-source PDK.**

Be patient, the following installation time is long !!
To minimize the use of disk space, we will not install the complete PDK.  Hence, there are two steps:

3a.  Configurating which submodule of the Skywater PDK is going to install.
```
git clone https://github.com/google/skywater-pdk
cd skywater-pdk
git submodule init libraries/sky130_fd_io/latest
git submodule init libraries/sky130_fd_pr/latest
git submodule init libraries/sky130_fd_sc_hd/latest
git submodule init libraries/sky130_fd_sc_hvl/latest
git submodule update
make timing
cd
```
3b.  Installing the PDK in open PDK format.
```
git clone https://github.com/RTimothyEdwards/open_pdks
cd open_pdks
./configure --enable-sky130-pdk=~/skywater-pdk/libraries/
make
sudo make install
```
**Finally, we completed the whole installation process !!**

## Reference
- [http://opencircuitdesign.com/open_pdks/index.html](http://opencircuitdesign.com/open_pdks/index.html)
- [https://hasler.ece.gatech.edu/Courses/ECE4430/SkywaterSetup.txt](https://hasler.ece.gatech.edu/Courses/ECE4430/SkywaterSetup.txt)
- [http://web02.gonzaga.edu/faculty/talarico/vlsi/install2.html](http://web02.gonzaga.edu/faculty/talarico/vlsi/install2.html)
