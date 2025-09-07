# First things First

This document logs the steps taken to:

- Wifi (easyroam)
- Fix Debian package sources
- Install Thunderbird
- Full screen
- Copy & Paste commands from terminal
- Default Desktop env
- Brave Browser
- NVIDIA Driver
- Default Terminal
- Conda (Miniforge)
- Screenshot
- External Monitor

---

## 1. Easyroam config

The certificate is only valid for **90 days** and must be replaced after this period.

To configure **eduroam** using a script, follow these steps:

1. Open [https://www.easyroam.de](https://www.easyroam.de) in your web browser.
2. In the search field, enter your institution name, e.g., `UOL`.
3. Log in using your institutional credentials.
4. Click on **"Manual options"** and select **PKCS12** as the format.
5. Enter a profile name of your choice and click **"Generate profile"**.
6. Download the configuration script `easyroam.sh` from:  
   [https://helpdesk.th-ab.de/help/en-us/6-configure-eduroam-easyroam/9-easyroam-linux](https://helpdesk.th-ab.de/help/en-us/6-configure-eduroam-easyroam/9-easyroam-linux)

   easyroam.sh – Script to Configure Eduroam with PKCS12 Certificate

   > This script sets up an eduroam NetworkManager profile using a PKCS12 certificate.  
   > It supports both NetworkManager (most Linux distros) and ConnMan (used on SailfishOS).

<details>
<summary>Click to expand script</summary>

```sh
#!/bin/sh
# easyroam.sh cert - install pkcs12 certificate as Easyroam NetworkManager Profile
helpString="Usage $0 <certificate>"
if [ $# -lt 1 ]; then
	echo "$helpString" >&2
	exit 1
fi

case "$1" in
-h|--help)
	echo "$helpString" >&2
	exit;;
esac

ClientCertificate="$1"
connection="easyroam"

[ -f /etc/os-release ] &&  . /etc/os-release

check_nmcli() {
	if ! type nmcli >/dev/null 2>&1; then
		echo "ERROR: nmcli not found!" >&2
		echo "This wizard assumes that your network connections are managed by NetworkManager." >&2
		exit 1
	fi
}

check_gdbus() {
	if ! type gdbus >/dev/null 2>&1; then
		echo "ERROR: gdbus not found!" >&2
		echo "This wizard assumes that your network connections are managed by NetworkManager." >&2
		exit 1
	fi
}

cleanup_networkmanager() {
	for conn in $connection eduroam; do
		for uuid in $(nmcli connection show | awk '$1==c{ print $2 }' c="$conn"); do
			nmcli connection delete uuid "$uuid"
		done
	done
}

add_networkmanager() {
	nmcli connection add \
		type wifi \
		con-name "$connection" \
		ssid "$SSID" \
		-- \
		wifi-sec.key-mgmt wpa-eap \
		802-1x.eap tls \
		802-1x.identity "$OuterIdentity" \
		802-1x.ca-cert "$root_ca_file" \
		802-1x.client-cert "$client_cert_file" \
		802-1x.private-key-password "$Passphrase" \
		802-1x.private-key "$client_key_file"
}

add_connman() {
	devel-su gdbus call --system --dest net.connman  --object-path / --method net.connman.Manager.CreateService \
	"" \
	"" \
	"" \
	"[('AutoConnect', 'true'), ('CACert', '$(cat "$root_ca_file")'),('ClientCertFile', '$client_cert_file'),
	('PrivateKeyFile', '$client_key_file'), ('PrivateKeyPassphrase', '$Passphrase'),
	('EAP', 'tls'), ('Hidden', 'false'), ('Identity', '$OuterIdentity'), ('Name', 'eduroam'),
	('Phase2', 'PAP'), ('Security', 'ieee8021x')]"
}

if [ "$ID" = "sailfishos" ]; then
	check_gdbus
else
	check_nmcli
fi

for d in openssl awk; do
	type "$d" >/dev/null 2>&1 && continue
	echo "ERROR: $d not found!" >&2
	echo >&2
	echo "You may fix this using:" >&2
	type apt          >/dev/null 2>&1 && echo "sudo apt install -y $d" >&2
	type dnf          >/dev/null 2>&1 && echo "sudo dnf install -y $d" >&2
	type zypper       >/dev/null 2>&1 && echo "sudo zypper install $d" >&2
	type pacman       >/dev/null 2>&1 && echo "sudo pacman -Syu $d" >&2
	type pkcon        >/dev/null 2>&1 && echo "devel-su pkconf install $d" >&2
	type xbps-install >/dev/null 2>&1 && echo "sudo xbps-install -Su $d" >&2
	echo >&2
	exit 2
done

conf_dir="$HOME/.easyroam"
client_cert_file="$conf_dir/easyroam_client_cert.pem"
client_key_file="$conf_dir/easyroam_client_key.pem"
root_ca_file="$conf_dir/easyroam_root_ca.pem"

[ -d "$conf_dir" ] || mkdir -p "$conf_dir"

openssl_extra=
version=$(openssl version | awk -F "[ .]" '{print $2}')
[ "${version:-2}" -ge 3 ] && openssl_extra="-legacy"

SSID=eduroam
Passphrase=$(openssl rand -base64 24)

printf "Extracting client cert ... "
openssl pkcs12 $openssl_extra -in "$ClientCertificate" -passin "pass:" -nokeys -out "$client_cert_file"
printf "success\n"

printf "Extracting client key ... "
openssl pkcs12 $openssl_extra -in "$ClientCertificate" -passin "pass:" -passout "pass:$Passphrase" -nodes -nocerts | \
	openssl rsa -passout "pass:$Passphrase" -aes128 -out "$client_key_file"

printf "Extracting CA cert ... "
openssl pkcs12 $openssl_extra -passin "pass:" -passout "pass:" -nokeys -in "$ClientCertificate" -cacerts -out "$root_ca_file"
printf "success\n"

OuterIdentity="$(openssl x509 -noout -in "$client_cert_file" -subject 2>/dev/null | awk -F \, '{print $1}' | sed -e 's/.*=//' -e 's/\s*//')"

if [ "$ID" = "sailfishos" ]; then
	add_connman
else
	cleanup_networkmanager
	add_networkmanager
fi

```

</details>


7. Open a terminal in your download directory and run the script with the certificate path as an argument. For example:

   ```
   sh easyroam.sh easyroam_*.p12
   ```
8. To check the internet connection, use something similiar

   ```
   ping www.google.com
   ```

## 2. Fixing DVD Source

After trying to install Thunderbird via `apt`, the system prompted for the installation DVD:

   *Media change: please insert the disc labeled 'Debian GNU/Linux 12.11.0 ...'*

This means the system was still using the DVD as a source for packages.

***Solution:***

1. Open the sources list:
   ```
   sudo nano /etc/apt/sources.list
   ```

2. Comment out the CD/DVD source line:

   ```
   # deb cdrom:[Debian GNU/Linux 12.11.0 _Bookworm_ ...]
   ```

3. Add the following online sources (if not already present):

   ```
   deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
   deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
   deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
   ```

4. Save and exit Nano:
   ```
   Ctrl + O, Enter to save
   Ctrl + X to exit
   ```

5. Update package lists:

   ```
   sudo apt update
   ```


3. Checking for Available Package Upgrades

   To find packages that had available updates:

   ``` 
   apt list --upgradable
   ```

   This listed 56 packages, including updates for: Firefox ESR, GIMP, Systemd, Kernel, WebKit, udev, GStreamer, ca-certificates etc.,

4. Performing a Full Upgrade

   To upgrade all listed packages:
   ```
   sudo apt upgrade
   ```
   And then (recommended) reboot for kernel/system updates to take effect:
   ```
   sudo reboot
   ```

## 3. Installing Thunderbird

Once the sources were fixed, Thunderbird could be installed normally:

   ``` 
   sudo apt install thunderbird 
   ```


This downloaded the package from online mirrors instead of prompting for the DVD.



## 4. Fullscreen

   To exit fullscreen mode in a graphical terminal window (like GNOME Terminal, Konsole, etc.):

   Press F11

## 5. Copy & Paste terminal commands

[How to copy the terminal output?](https://superuser.com/questions/357248/how-to-copy-the-terminal-output)

you can copy-paste the selected text using 
   ```
   Ctrl + Shift + C
   Ctrl + Shift + V 
   ```

## 6. Default Desktop Environment
   
   Set other desktop environments as default (Gnome to KDE Plasma)

   ```
   update-alternatives --config x-session-manager
   ```

   To remove the GNOME files completely

   ```
   apt-get autoremove --purge gnome* 
   ```

## 7. Brave Browser

   Here is the linux [download](https://brave.com/linux/) page of Brave browser

   ```
   apt install curl
   curl -fsS https://dl.brave.com/install.sh | sh
   ```

## 8. NVIDIA Driver

   ```
[ 0.259410] ACPI BIOS Error (bug): Failure creating named object [_SB.PCI0.GPP0.VGA], AE_ALREADY_EXISTS (20220331/dswload2-326)
[ 0.259421] ACPI Error: AE_ALREADY_EXISTS, During name lookup/catalog (20220331/psobject-220)
[ 0.259492] ACPI BIOS Error (bug): Failure creating named object [_SB.PCI0.GPP0.HDAU], AE_ALREADY_EXISTS (20220331/dswload2-326)
[ 0.259436] ACPI Error: AE_ALREADY_EXISTS, During name lookup/catalog (20220331/psobject-220)
[ 0.909192] blacklist: Problem blacklisting hash (-13)
[ 0.909414] blacklist: Problem blacklisting hash (-13)
[ 0.909528] blacklist: Problem blacklisting hash (-13)
[ 0.909560] blacklist: Problem blacklisting hash (-13)
[ 0.909787] blacklist: Problem blacklisting hash (-13)
[ 1.998245] nouveau 0000:01:00.0: firmware: failed to load nvidia/ga107/nvdec/scrubber.bin (-2)
[ 1.998271] firmware_class: See https://wiki.debian.org/Firmware for information about missing firmware
[ 1.998307] nouveau 0000:01:00.0: firmware: failed to load nvidia/ga107/nvdec/scrubber.bin (-2)
   ```
   The last three errors shows that the nvidia driver is failed to load. The nvidia-detect script (found in the [nvidia-detect](https://packages.debian.org/nvidia-detect) package in the [non-free](https://www.debian.org/doc/debian-policy/ch-archive#s-non-free) section) can also be used to identify the GPU and the recommended driver package to install. Refer the nvidia debian page [here](https://wiki.debian.org/NvidiaGraphicsDrivers)

   1. Install the detect driver
   ```
   apt install nvidia-detect
   ```

   2. Detect the driver using  
   ```
   nvidia-detect
   ```

   The output is as follows

   ```
   gowtham@deb-gowarc:~$ nvidia-detect
   Detected NVIDIA GPUs:
   01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA107M [GeForce RTX 3050 Mobile] [10de:25a2] (rev a1)

   Checking card:  NVIDIA Corporation GA107M [GeForce RTX 3050 Mobile] (rev a1)
   Your card is supported by all driver versions.
   Your card is also supported by the Tesla 470 drivers series.
   It is recommended to install the
   nvidia-driver
   package.
   ```

   3. Install the nvidia-driver
   ```
   apt install nvidia-driver
   ```
  
   4. During installation, it prompted saying NVIDIA proprietary driver is conflicting with the open-source Nouveau driver. It asked for reboot after installation. Once installed, Reboot in `su`
   ```
   reboot
   ```

   5. verify the installation using 
   ```
   nvidia-smi
   ```

The output is as follows

   ```
gowtham@deb-gowarc:~$ nvidia-smi
Thu Jun 26 17:13:29 2025       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.247.01             Driver Version: 535.247.01   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 3050 ...    On  | 00000000:01:00.0 Off |                  N/A |
| N/A   47C    P3              17W /  30W |      8MiB /  4096MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A       949      G   /usr/lib/xorg/Xorg                            4MiB |
+---------------------------------------------------------------------------------------+
   ```

while rebooting, the nvidia driver error got resolved.

   ```
[ 0.259410] ACPI BIOS Error (bug): Failure creating named object [_SB.PCI0.GPP0.VGA], AE_ALREADY_EXISTS (20220331/dswload2-326)
[ 0.259421] ACPI Error: AE_ALREADY_EXISTS, During name lookup/catalog (20220331/psobject-220)
[ 0.259492] ACPI BIOS Error (bug): Failure creating named object [_SB.PCI0.GPP0.HDAU], AE_ALREADY_EXISTS (20220331/dswload2-326)
[ 0.259436] ACPI Error: AE_ALREADY_EXISTS, During name lookup/catalog (20220331/psobject-220)
[ 0.909192] blacklist: Problem blacklisting hash (-13)
[ 0.909414] blacklist: Problem blacklisting hash (-13)
[ 0.909528] blacklist: Problem blacklisting hash (-13)
[ 0.909560] blacklist: Problem blacklisting hash (-13)
[ 0.909787] blacklist: Problem blacklisting hash (-13)
   ```

Blacklist: Problem blacklisting hash (-13)
   ```
[ 0.909192] blacklist: Problem blacklisting hash (-13)
   ```

Blacklisting in Linux is a way to prevent certain kernel modules from loading (e.g., to block nouveau if using NVIDIA proprietary driver).

It means, the kernel tried to blacklist a module based on a hash but failed.

`Error code -13` corresponds to Permission Denied (EACCES).

This likely means the kernel could not read or write to `/etc/modprobe.d/` or related files due to permission or filesystem issues.

To check ownership, content and permissions of:

```
gowtham@deb-gowarc:~$ ls -l /etc/modprobe.d/
total 8
-rw-r--r-- 1 root root 154 Aug 24  2024 amd64-microcode-blacklist.conf
-rw-r--r-- 1 root root 127 Jul 12  2023 dkms.conf
lrwxrwxrwx 1 root root  53 May  8 22:59 nvidia-blacklists-nouveau.conf -> /etc/alternatives/glx--nvidia-blacklists-nouveau.conf
lrwxrwxrwx 1 root root  43 May  8 22:59 nvidia.conf -> /etc/alternatives/glx--nvidia-modprobe.conf
lrwxrwxrwx 1 root root  45 May  8 22:59 nvidia-options.conf -> /etc/alternatives/nvidia--nvidia-options.conf
```
Now we need to verify the content of that file to ensure it actually blacklists nouveau correctly.

```
gowtham@deb-gowarc:~$ cat /etc/nvidia/nvidia-blacklists-nouveau.conf
# You need to run "update-initramfs -u" after editing this file.

# see #580894
blacklist nouveau
```

The file only has `blacklist nouveau` but not `options nouveau modeset=0` that disables nouveau modesetting. Without that, the nouveau driver can still be loaded early in the boot process via initramfs, which is likely why boot-time errors are seen. To solve, edit or create a file explicitly blacklisting `nouveau` with both required lines. To open the file,
```
nano /etc/modprobe.d/blacklist-nouveau.conf
```
Add the following content
```
blacklist nouveau
options nouveau modeset=0
```

Save and exit (Ctrl + O, Enter, then Ctrl + X).

To apply the change and make sure nouveau is removed from early boot:
```
update-initramfs -u
```

`reboot` once done. **_The blacklist error is still not solved._**

## 9. Changing Default Terminal

   ```
   update-alternatives --config x-terminal-emulator
   ```

   *Understanding Terminal and Bash*

   Konsole = Terminal emulator (a graphical window that shows your shell).

   Bash = The shell (the actual command interpreter you're typing into).

   Konsole usually runs Bash by default on Debian, unless you've changed it to zsh or fish

   To check, run the below command to see *Konsole* is running Bash as your shell.

   ```
   gowtham@deb-gowarc:~$ echo $SHELL
   /bin/bash
   ```


## 10. Miniforge

   1. Download conda-forge installer from the [website](https://conda-forge.org/download/)

   2. Go to the directory and run

   ```
   bash Miniforge3-$(uname)-$(uname -m).sh
   ```
   Since `uname` outputs `Linux` and `uname -m` outputs `x86_64`, running the command `Miniforge3-$(uname)-$(uname -m).sh` is similar to mentioning the actual file name `Miniforge3-Linux-x86_64.sh`.

   3. It will ask to read the license agreement and now it will show `end`. Now type `yes` and the installation will begin.
   ```
   Miniforge3 will now be installed into this location:
   /home/gowtham/miniforge3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below
   ```
   Press `ENTER`


   4.  Once installation is done, it prompts
   ```
   Do you wish to update your shell profile to automatically initialize conda?
   This will activate conda on startup and change the command prompt when activated.
   If you'd prefer that conda's base environment not be activated on startup,
   run the following command when conda is activated:

   conda config --set auto_activate_base false

   You can undo this by running `conda init --reverse $SHELL`? [yes|no]
   ```

   If `yes` - Adds Conda to your shell startup file. conda is ready every time. 
   If `no` - You’ll have to manually source `~/miniforge3/bin/activate` each time.

   press `yes` in this case. 

   5. Successfully installed
   ```
no change     /home/gowtham/miniforge3/condabin/conda
no change     /home/gowtham/miniforge3/bin/conda
no change     /home/gowtham/miniforge3/bin/conda-env
no change     /home/gowtham/miniforge3/bin/activate
no change     /home/gowtham/miniforge3/bin/deactivate
no change     /home/gowtham/miniforge3/etc/profile.d/conda.sh
no change     /home/gowtham/miniforge3/etc/fish/conf.d/conda.fish
no change     /home/gowtham/miniforge3/shell/condabin/Conda.psm1
no change     /home/gowtham/miniforge3/shell/condabin/conda-hook.ps1
no change     /home/gowtham/miniforge3/lib/python3.12/site-packages/xontrib/conda.xsh
no change     /home/gowtham/miniforge3/etc/profile.d/conda.csh
modified      /home/gowtham/.bashrc

==> For changes to take effect, close and re-open your current shell. <==

Running `shell init`, which:
 - modifies RC file: "/home/gowtham/.bashrc"
 - generates config for root prefix: "/home/gowtham/miniforge3"
 - sets mamba executable to: "/home/gowtham/miniforge3/bin/mamba"
The following has been added in your "/home/gowtham/.bashrc" file

# >>> mamba initialize >>>
# !! Contents within this block are managed by 'mamba shell init' !!
export MAMBA_EXE='/home/gowtham/miniforge3/bin/mamba';
export MAMBA_ROOT_PREFIX='/home/gowtham/miniforge3';
__mamba_setup="$("$MAMBA_EXE" shell hook --shell bash --root-prefix "$MAMBA_ROOT_PREFIX" 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__mamba_setup"
else
    alias mamba="$MAMBA_EXE"  # Fallback on help from mamba activate
fi
unset __mamba_setup
# <<< mamba initialize <<<

Thank you for installing Miniforge3!
   ```

   6. Close terminal window (Konsole) and open a new one. *conda env base* can be seen.

   ```
   (base) gowtham@deb-gowarc:~$ conda --version
   conda 25.3.0
   ```
   
   7. To prevent the automatic activation of the conda base environment when you open a new terminal
   ```
   conda config --set auto_activate_base false
   ```

   Once again, reopen the terminal. Conda can still be accessed in Konsole but we need to activate the environment when needed.
   ```
   gowtham@deb-gowarc:~$ conda --version
   conda 25.3.0
   gowtham@deb-gowarc:~$ conda activate base
   (base) gowtham@deb-gowarc:~$ conda deactivate
   gowtham@deb-gowarc:~$ conda env list

   # conda environments:
   #
   base                   /home/gowtham/miniforge3
   ```
## 11. Screenshot

To take a screenshot, install
```
apt install gnome-screenshot
```

launch screenshot app and see the shortcuts.


## 12. External Monitor

You can preview the current setup by just running:
```
xrandr
```

1. Set Kernel Parameter nvidia-drm.modeset=1 
Edit your GRUB configuration:
```
sudo nano /etc/default/grub
```

2. Append nvidia-drm.modeset=1 inside the quotes:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia-drm.modeset=1"
```

3. Save and update GRUB:
```
sudo update-grub
```
Reboot after it.

4. The below will list available GPU output providers.
```
xrandr --listproviders
```

5. To place the monitor to the right of laptop screen.
```
xrandr --output HDMI-1-0 --auto --right-of eDP
```
To duplicate,
```
xrandr --output HDMI-1-0 --mode 1920x1080 --same-as eDP
```

