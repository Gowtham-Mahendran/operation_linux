# User specific softwares

## 1. MATLAB installation

   1. Download the iso file for linux
   
   2. Suppose your ISO is called matlab_R2025a_glnxa64.iso and located in your Downloads folder:
   ```
   cd ~/Downloads
   mkdir matlab_iso
   mount -o loop matlab_R2025a_glnxa64.iso matlab_iso
   ```

   3. After mounting, files are extracted to `matlab_iso` folder

   4. Now run the installation script in `user` terminal and not in `root`
   ```
   cd matlab_iso
   ./install
   ```
   
   5. During installation, select the preferred path. After installation, open terminal and enter the path to verify MATLAB.

   ```
gowtham@deb-gowarc:~$ /home/gowtham/MATLAB/R2024a/bin/matlab -desktop &
[1] 2757
gowtham@deb-gowarc:~$ MATLAB is selecting SOFTWARE rendering.
MESA-LOADER: failed to open radeonsi: /usr/lib/dri/radeonsi_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib/x86_64-linux-gnu/dri:\$${ORIGIN}/dri:/usr/lib/dri, suffix _dri)
failed to load driver: radeonsi
MESA-LOADER: failed to open zink: /usr/lib/dri/zink_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib/x86_64-linux-gnu/dri:\$${ORIGIN}/dri:/usr/lib/dri, suffix _dri)
failed to load driver: zink
MESA-LOADER: failed to open kms_swrast: /usr/lib/dri/kms_swrast_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib/x86_64-linux-gnu/dri:\$${ORIGIN}/dri:/usr/lib/dri, suffix _dri)
failed to load driver: kms_swrast
MESA-LOADER: failed to open swrast: /usr/lib/dri/swrast_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib/x86_64-linux-gnu/dri:\$${ORIGIN}/dri:/usr/lib/dri, suffix _dri)
failed to load swrast driver
   ```
   MATLAB will start automatically.

   6. That error means MATLAB's graphics system can't find the OpenGL drivers (Mesa DRI), so it falls back to software rendering, which is slower and might cause GUI glitches.

You're missing the required `.so` driver libraries, such as: radeonsi_dri.so (for AMD GPUs), swrast_dri.so (software fallback) and zink_dri.so (generic fallback)
   ```
sudo apt update
sudo apt install libgl1-mesa-glx libgl1-mesa-dri mesa-utils
   ```

Restart MATLAB

   7. Open MATLAB using
   ```
/home/gowtham/MATLAB/R2024a/bin/matlab   
   ```

Run opengl info in MATLAB command window
   ```
>> opengl info
                          Version: '2.1 Mesa 17.1.3'
                           Vendor: 'Brian Paul'
                         Renderer: 'Mesa X11'
                   MaxTextureSize: 16384
                           Visual: 'Visual 0x1fe, (RGBA 32 bits (8 8 8 8), Z depth 16 bits, Hardware acceleration, Double buffer, Antialias 0 samples)'
                         Software: 'true'
             HardwareSupportLevel: 'none (known graphics driver issues)'
        SupportsGraphicsSmoothing: 0
    SupportsDepthPeelTransparency: 1
       SupportsAlignVertexCenters: 0
                       Extensions: {152×1 cell}
               MaxFrameBufferSize: 16384

   ```


**Summary of Current Graphics State**

* OpenGL Vendor - Brian Paul (Mesa software)

* Renderer - Mesa X11 (CPU rendering)

* Hardware Acceleration -  Disabled

* Stability - Good

* Performance - Slower than GPU, but usable

This confirms that MATLAB is running stably with OpenGL

   8. Verifying openGL
   ```
gowtham@deb-gowarc:~$ glxinfo | grep "OpenGL version"
OpenGL version string: 4.6 (Compatibility Profile) Mesa 22.3.6
gowtham@deb-gowarc:~$ glxinfo | grep "OpenGL renderer"
OpenGL renderer string: AMD Radeon Graphics (renoir, LLVM 15.0.6, DRM 3.49, 6.1.0-37-amd64)
   ```

It should be `renoir`. If it's `llvmpipe` or `Brian Paul`, system GPU acceleration is not working yet.

To check GPU,

   ```
gowtham@deb-gowarc:~$ lspci -vnn | grep -A12 VGA
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA107M [GeForce RTX 3050 Mobile] [10de:25a2] (rev a1) (prog-if 00 [VGA controller])
        DeviceName: NVIDIA Graphics Device
        Subsystem: Hewlett-Packard Company GA107M [GeForce RTX 3050 Mobile] [103c:88de]
        Flags: bus master, fast devsel, latency 0, IRQ 83, IOMMU group 11
        Memory at fb000000 (32-bit, non-prefetchable) [size=16M]
        Memory at fe00000000 (64-bit, prefetchable) [size=4G]
        Memory at ff00000000 (64-bit, prefetchable) [size=32M]
        I/O ports at f000 [size=128]
        Expansion ROM at fc000000 [virtual] [disabled] [size=512K]
        Capabilities: <access denied>
        Kernel driver in use: nvidia
        Kernel modules: nvidia

--
05:00.0 VGA compatible controller [0300]: Advanced Micro Devices, Inc. [AMD/ATI] Cezanne [Radeon Vega Series / Radeon Vega Mobile Series] [1002:1638] (rev c5) (prog-if 00 [VGA controller])
        DeviceName:  Onboard IGD
        Subsystem: Hewlett-Packard Company Cezanne [Radeon Vega Series / Radeon Vega Mobile Series] [103c:88de]
        Flags: bus master, fast devsel, latency 0, IRQ 46, IOMMU group 15
        Memory at d0000000 (64-bit, prefetchable) [size=256M]
        Memory at e0000000 (64-bit, prefetchable) [size=2M]
        I/O ports at c000 [size=256]
        Memory at fc500000 (32-bit, non-prefetchable) [size=512K]
        Capabilities: <access denied>
        Kernel driver in use: amdgpu
        Kernel modules: amdgpu

05:00.2 Encryption controller [1080]: Advanced Micro Devices, Inc. [AMD] Family 17h (Models 10h-1fh) Platform Security Processor [1022:15df]
   ```

   9. Switching between GPU
   ```
gowtham@deb-gowarc:~$ glxinfo | grep "OpenGL renderer"
OpenGL renderer string: AMD Radeon Graphics (renoir, LLVM 15.0.6, DRM 3.49, 6.1.0-37-amd64)
gowtham@deb-gowarc:~$ __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxinfo | grep "OpenGL renderer"
OpenGL renderer string: NVIDIA GeForce RTX 3050 Laptop GPU/PCIe/SSE2
gowtham@deb-gowarc:~$ glxinfo | grep "OpenGL renderer"
OpenGL renderer string: AMD Radeon Graphics (renoir, LLVM 15.0.6, DRM 3.49, 6.1.0-37-amd64)
   ```

To run MATLAB on nvidia GPU
   ```
   __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia /home/gowtham/MATLAB/R2024a/bin/matlab -nosoftwareopengl
   ```
In MATLAB command window,
   ```
>> opengl info
                          Version: '4.6.0 NVIDIA 535.247.01'
                           Vendor: 'NVIDIA Corporation'
                         Renderer: 'NVIDIA GeForce RTX 3050 Laptop GPU/PCIe/SSE2'
                   MaxTextureSize: 32768
                           Visual: 'Visual 0x650, (RGBA 32 bits (8 8 8 8), Z depth 16 bits, Hardware acceleration, Double buffer, Antialias 8 samples)'
                         Software: 'false'
             HardwareSupportLevel: 'full'
        SupportsGraphicsSmoothing: 1
    SupportsDepthPeelTransparency: 1
       SupportsAlignVertexCenters: 1
                       Extensions: {401×1 cell}
               MaxFrameBufferSize: 32768
   ```

   10. Compilers for MATLAB

   We need to install compilers for MATLAB to perform the tasks
   ```
   apt install build-essential
   ```

   This installs: gcc (C compiler), g++ (C++ compiler), make (build tool) and libstdc++ headers. To check the version of gcc

   ```
   gowtham@deb-gowarc:~$ gcc --version
   gcc (Debian 12.2.0-14+deb12u1) 12.2.0
   Copyright (C) 2022 Free Software Foundation, Inc.
   This is free software; see the source for copying conditions.  There is NO
   warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
   ```
   See supported MATLAB compatible linux compilers [here](https://www.mathworks.com/support/requirements/supported-compilers-linux.html)

   To test, in MATLAB command window

   ```
>> mex -setup
MEX configured to use 'gcc' for C language compilation.

To choose a different language, select one from the following:
 mex -setup C++ 
 mex -setup FORTRAN
   ```
If it outputs `MEX configured to use 'gcc' for C language compilation`, everything is in order.


### C2000 Setup

Download [CCS offline installer](https://www.ti.com/tool/download/CCSTUDIO/12.4.0).

To extract the files, go to your Downloads folder,
```
cd ~/Downloads
tar -xvzf ccs*_linux-x64.tar.gz
cd ccs*_linux-x64
```
tar → The program used to create/extract archive files

Options:

x → extract files

v → verbose (shows files while extracting)

z → decompress using gzip (.gz)

f → use the file name provided next


Run the installer using,
```
sudo ./ccs_setup_12.x.x.x.run
```

Official TI Linux host support can be found [here](https://software-dl.ti.com/ccs/esd/documents/ccsv12_linux_host_support.html)

CCS requires a 64-bit distribution of Linux. 32-bit distributions are not supported with current versions of CCS.

CCS has dependencies on several libraries being installed on the system. The CCS installer will run a dependency check for these and inform the user of any missing dependencies.

<img width="796" height="629" alt="image" src="https://github.com/user-attachments/assets/ee564018-2a46-492b-9b06-7d052629a2d6" />

To install dependencies,
```
sudo apt update
sudo apt install libc6-i386 libusb-0.1-4 libgconf-2-4 libncurses5 libpython2.7 libtinfo5
```
Since `libpython2.7` is not in debian 12, it will give error installation. Remove `libpython2.7` and install the rest of the dependencies. Now, to install `libpython2.7` follow the below steps,

**1. Check Python Availability**

CCS requires **Python 2** for some legacy scripts. By default, Debian 12 does not ship Python 2.

```
which python
# Output: python not found

where python
# Output: python not found

python3 --version
# Output: Python 3.11.2

python2 --version
# Output: command not found
```

---

**2. Verify Python Executables in `/usr/bin`**

```
ls /usr/bin/python*
# Output:
# /usr/bin/python3
# /usr/bin/python3.11
```

At this stage, **Python 2 was not installed**.

---

**3. Update System Packages**

```bash
sudo apt update
sudo apt upgrade
```

---

**4. Attempt to Install Python 2 (Fails on Bookworm)**

```bash
sudo apt install python2
```

**Result:**

```
Package python2 is not available, but is referred to by another package.
```

---

**5. Enable Debian Bullseye Repository (for Legacy Python 2)**

I manually edited the **APT sources list**:

```
sudo nano /etc/apt/sources.list
```

**Added the following line:**

```
deb http://deb.debian.org/debian bullseye main
```

Then updated package lists:

```
sudo apt update
```

---

**6. Install Python 2 from Bullseye Repository**

```
sudo apt install -t bullseye python2 libpython2.7 libpython2.7-minimal libpython2.7-stdlib
```
The output will look like this, 
```
gowtham@deb-gowarc  ~  sudo apt install -t bullseye python2 libpython2.7 libpython2.7-minimal libpython2.7-stdlib

Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  libslirp0 pigz slirp4netns
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  libffi7 libpython2-stdlib libssl1.1 python2-minimal python2.7 python2.7-minimal
Suggested packages:
  python2-doc python-tk python2.7-doc binfmt-support
The following NEW packages will be installed:
  libffi7 libpython2-stdlib libpython2.7 libpython2.7-minimal libpython2.7-stdlib libssl1.1 python2
  python2-minimal python2.7 python2.7-minimal
0 upgraded, 10 newly installed, 0 to remove and 0 not upgraded.
Need to get 6,564 kB of archives.
After this operation, 23.9 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
```

---

**7. Verify Python2 Installation**

```
python2 --version
# Output: Python 2.7.18
```

To verify installed dependencies, 

```
gowtham@deb-gowarc  ~  dpkg -l | grep python2
ii  libpython2-stdlib:amd64                       2.7.18-3                            amd64        interactive high-level object-oriented language (Python2)
ii  libpython2.7:amd64                            2.7.18-8+deb11u1                    amd64        Shared Python runtime library (version 2.7)
ii  libpython2.7-minimal:amd64                    2.7.18-8+deb11u1                    amd64        Minimal subset of the Python language (version 2.7)
ii  libpython2.7-stdlib:amd64                     2.7.18-8+deb11u1                    amd64        Interactive high-level object-oriented language (standard library, version 2.7)
ii  python2                                       2.7.18-3                            amd64        interactive high-level object-oriented language (Python2 version)
ii  python2-minimal                               2.7.18-3                            amd64        minimal subset of the Python2 language
ii  python2.7                                     2.7.18-8+deb11u1                    amd64        Interactive high-level object-oriented language (version 2.7)
ii  python2.7-minimal                             2.7.18-8+deb11u1                    amd64        Minimal subset of the Python language (version 2.7)
```
Since libpython2.7 is not seen there. To verify it is installed, run
```
gowtham@deb-gowarc  ~  dpkg -L libpython2.7 | grep libpython2.7.so.1.0

/usr/lib/x86_64-linux-gnu/libpython2.7.so.1.0
```

This confirms that the package is successfully installed.

Now, CCS can run Python2 scripts. Now remove Bullseye from `sources.list` using

```
 gowtham@deb-gowarc  ~  sudo nano /etc/apt/sources.list

 gowtham@deb-gowarc  ~  sudo apt update 
```
---

**8. Install Code Composer Studio**

```
gowtham@deb-gowarc  ~/Downloads/CCS12.4.0.00007_linux-x64  ./ccs_setup_12.4.0.00007.run 
```

<img width="803" height="632" alt="image" src="https://github.com/user-attachments/assets/2abc39d7-d36f-463c-a252-ccd15da6d486" />

<img width="806" height="634" alt="image" src="https://github.com/user-attachments/assets/a9bf172d-7d36-42fa-9ccf-0a748dbae1dc" />

After successful installation, run the `install_scripts.sh` file using
```
gowtham@deb-gowarc  ~  /home/gowtham/ti/ccs1240/ccs/install_scripts/
gowtham@deb-gowarc  ~/ti/ccs1240/ccs/install_scripts  ls
71-ti-permissions.rules  install_drivers.sh  ti_permissions_install.sh  uninstall_drivers.sh
gowtham@deb-gowarc  ~/ti/ccs1240/ccs/install_scripts  sudo ./install_drivers.sh 
TI permissions file installation completed successfully. Some distros of Linux
may require a reboot in order for the driver to function properly.  For other
distros, restarting udev is sufficient.  Restarting udev now ... 
gowtham@deb-gowarc  ~/ti/ccs1240/ccs/install_scripts  
```
The installed python versions can be seen using,
```
gowtham@deb-gowarc  ~  ls /usr/bin/python*
/usr/bin/python2  /usr/bin/python2.7  /usr/bin/python3  /usr/bin/python3.11
```

To run ccs,
```
gowtham@deb-gowarc  ~  ~/ti/ccs1240/ccs/eclipse/ccstudio 
```

<img width="727" height="387" alt="image" src="https://github.com/user-attachments/assets/6b38b8e1-04d6-417e-a8fe-2145dbc723ba" />

Click `Launch`. The ccs is successfully installed and verified.

<img width="771" height="376" alt="image" src="https://github.com/user-attachments/assets/af6e959c-4191-4987-907b-42095c641740" />


Now open MATLAB and enter `c2000setup` in the command window,

<img width="682" height="514" alt="image" src="https://github.com/user-attachments/assets/2497b0c7-5f21-424e-addd-0b2a5de2e575" />

Select the Board and automatically install third party softwares,


<img width="686" height="512" alt="image" src="https://github.com/user-attachments/assets/cfec69cc-54c8-4161-8f06-01e2eab275ba" />

In the next step, it is telling that ccs is not installed. This is because `MATLAB` looks for CCS in standard installation directories like:

`/opt/ti/ccs*/ccs/eclipse/ccstudio` on linux and `C:\ti\ccs*` on Windows

Since I installed it in '/home/gowtham/ti/ccs1240/ccs', it won’t detect it automatically. To verify the existing dir, open command window in MATLAB,

```
>> getpref('c2000')

ans = []
```
The error confirms that MATLAB has no CCS installation path stored yet, which is why the Hardware Setup cannot detect Code Composer Studio (CCS).

To add the location,
```
ccsRoot = '/home/gowtham/ti/ccs1240/ccs';
setpref('c2000','CCSInstallPath',ccsRoot);
```
To verify,
```
>> getpref('c2000')

ans = 

  struct with fields:

    CCSInstallPath: '/home/gowtham/ti/ccs1240/ccs'
```

### C2000Ware

Download [c2000ware](https://www.ti.com/tool/download/C2000WARE/5.00.00.00)

```
gowtham@deb-gowarc  /opt  ~/Downloads 
 gowtham@deb-gowarc  ~/Downloads  ./C2000Ware_5_00_00_00_setup.run --mode unattended --prefix ~/ti/c2000  

Problem running post-install step. Installation may not complete correctly
 Error creating dynamic link ../driverlib/f28p65x/examples /home/gowtham/ti/c2000/C2000Ware_5_00_00_00/driverlib/f28P65x/examples
 ✘ gowtham@deb-gowarc  ~/Downloads  ~/ti/
 gowtham@deb-gowarc  ~/ti  ls
c2000  ccs1240  CCSExternalReferences  tirex-localserver-3.7.1
 gowtham@deb-gowarc  ~/ti  rm -rf c2000
 gowtham@deb-gowarc  ~/ti  ~/Downloads 
 gowtham@deb-gowarc  ~/Downloads  sudo ./C2000Ware_5_00_00_00_setup.run --mode unattended --prefix ~/ti/c2000
```

Run with `sudo` to prevent case sensitive error.

Now run `c2000setup` in matlab

<img width="681" height="510" alt="image" src="https://github.com/user-attachments/assets/c1775602-0f6f-4f3b-a473-6e069a50fa17" />


<img width="681" height="505" alt="image" src="https://github.com/user-attachments/assets/6556aaf4-da3c-47c1-8c5a-041f67742244" />


<img width="681" height="509" alt="image" src="https://github.com/user-attachments/assets/09814df8-654b-427e-8852-5d4eb6315332" />


## 2. GlobalProtect VPN

   GlobalProtect offers you two different methods to install the GlobalProtect app on your Linux device: a GUI-based installation version and a CLI version. If you use a supported Linux operating system that supports a graphical interface, you can install the GUI version of the GlobalProtect; otherwise, download and install the CLI version of the GlobalProtect app. [Reference](https://docs.paloaltonetworks.com/globalprotect/5-1/globalprotect-app-user-guide/globalprotect-app-for-linux/download-and-install-the-globalprotect-app-for-linux#id181NC050F59)

   1. Download GUI version and install the .deb file by double clicking on it
   2. Restart the system
   3. Use one of the [gateway](https://uol.de/en/icbm/it/it-security/2-faktor-authentifizierung/globalprotect-vpn) 

   ```
   v-uol.uol.de
   v.uol.de
   ```

   4. To uninstall

   ```
   apt-get remove globalprotect
   ```
   
   5. Verify the connection
   ```
gowtham@deb-gowarc:~$ nmcli connection show --active
NAME      UUID                     TYPE      DEVICE 
easyroam  bb788f1f-0991-4143-aed0  wifi      wlo1   
gpd0      8c897b6a-a5bd-423e-93aa  tun       gpd0   
lo        9ec90b71-7d0c-4bfb-83f0  loopback  lo     
gowtham@deb-gowarc:~$ ping www.debian.org
PING www.debian.org(klecker.debian.org (2001:67c:2564:a119::77)) 56 data bytes
64 bytes from klecker.debian.org (2001:67c:2564:a119::77): icmp_seq=1 ttl=47 time=20.6 ms
64 bytes from klecker.debian.org (2001:67c:2564:a119::77): icmp_seq=2 ttl=47 time=46.2 ms
64 bytes from klecker.debian.org (2001:67c:2564:a119::77): icmp_seq=3 ttl=47 time=148 ms
64 bytes from klecker.debian.org (2001:67c:2564:a119::77): icmp_seq=4 ttl=47 time=66.1 ms
64 bytes from klecker.debian.org (2001:67c:2564:a119::77): icmp_seq=5 ttl=47 time=192 ms
64 bytes from klecker.debian.org (2001:67c:2564:a119::77): icmp_seq=6 ttl=47 time=112 ms
64 bytes from klecker.debian.org (2001:67c:2564:a119::77): icmp_seq=7 ttl=47 time=136 ms
^C
--- www.debian.org ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 6010ms
rtt min/avg/max/mdev = 20.579/102.865/191.882/56.575 ms
gowtham@deb-gowarc:~$ nmcli connection show --active
NAME      UUID                                  TYPE      DEVICE 
easyroam  bb788f1f-0991-4143-aed0-30925a220183  wifi      wlo1   
lo        9ec90b71-7d0c-4bfb-83f0-f06ad342060c  loopback  lo     
   ```

`gpd0` is a virtual network interface created when you connect via GlobalProtect, the Palo Alto Networks VPN client.

The TYPE is `tun`, which stands for TUNnel — a software-only (non-ethernet) point-to-point device.


## 3. KiCAD - Debian Backports

Debian’s Backports archive is an official way to get newer (“back-ported”) versions of packages on your stable system without sacrificing its overall stability. Backports is a separate APT repository (e.g. bookworm-backports for Debian 12) that Debian maintainers populate with newer releases of software originally built for Debian Testing or Unstable. Stable’s main archive often has older releases (e.g. KiCad 6 in Debian 12). Backports let you get KiCad 9 (or other up-to-date software) without waiting for the next Debian release. [Refer](https://www.kicad.org/download/linux/)

To use backports, we need to enable the backports repo,
```
echo "deb http://deb.debian.org/debian bookworm-backports main contrib non-free" \
  | sudo tee /etc/apt/sources.list.d/bookworm-backports.list
```
Then update apt using
```
sudo apt update
```
Now install KiCAD using,
```
sudo apt install -t bookworm-backports kicad
```
The -t flag in APT stands for “target release” (it’s shorthand for --target-release). It tells APT which release (or suite) you want to pull a package from, rather than using the default “stable” archive.