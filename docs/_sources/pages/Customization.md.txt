# Customization

## 1. Fastfetch

   1. Download fastfetch from the [Github](https://github.com/fastfetch-cli/fastfetch) repositary
   2. Install the .deb file
   3. Go to terminal and run 
   ```
   fastfetch
   ```
   4. To view specific linux logo, use
   ```
   fastfetch --logo debian
   ```
   5. To get all information
   ```
   fastfetch -c all.jsonc
   ```

   6. There are also modules to get specific information
   ```
   fastfetch -s <module> --format json
   ```

   Replace 'module' with below

   ```
1)  Battery       : Print battery capacity, status, etc
2)  Bios          : Print information of 1st-stage bootloader (name, version, release date, etc)
3)  Bluetooth     : List (connected) bluetooth devices
4)  BluetoothRadio: List bluetooth radios width supported version and vendor
5)  Board         : Print motherboard name and other info
6)  Bootmgr       : Print information of 2nd-stage bootloader (name, firmware, etc)
7)  Break         : Print a empty line
8)  Brightness    : Print current brightness level of your monitors
9)  Btrfs         : Print Linux BTRFS volumes
10) Camera        : Print available cameras
11) Chassis       : Print chassis type (desktop, laptop, etc)
12) Command       : Run custom shell scripts
13) Colors        : Print some colored blocks
14) CPU           : Print CPU name, frequency, etc
15) CPUCache      : Print CPU cache sizes
16) CPUUsage      : Print CPU usage. Costs some time to collect data
17) Cursor        : Print cursor style name
18) Custom        : Print a custom string, with or without key
19) DateTime      : Print current date and time
20) DE            : Print desktop environment name
21) Display       : Print resolutions, refresh rates, etc
22) Disk          : Print partitions, space usage, file system, etc
23) DiskIO        : Print physical disk I/O throughput
24) DNS           : Print configured DNS servers
25) Editor        : Print information of the default editor ($VISUAL or $EDITOR)
26) Font          : Print system font names
27) Gamepad       : List (connected) gamepads
28) GPU           : Print GPU names, graphic memory size, type, etc
29) Host          : Print product name of your computer
30) Icons         : Print icon style name
31) InitSystem    : Print init system (pid 1) name and version
32) Kernel        : Print system kernel version
33) Keyboard      : List (connected) keyboards
34) LM            : Print login manager (desktop manager) name and version
35) Loadavg       : Print system load averages
36) Locale        : Print system locale name
37) LocalIp       : List local IP addresses (v4 or v6), MAC addresses, etc
38) Media         : Print playing song name
39) Memory        : Print system memory usage info
40) Monitor       : Alias of Display module
41) Mouse         : List connected mouses
42) NetIO         : Print network I/O throughput
43) OpenCL        : Print highest OpenCL version supported by the GPU
44) OpenGL        : Print highest OpenGL version supported by the GPU
45) OS            : Print operating system name and version
46) Packages      : List installed package managers and count of installed packages
47) PhysicalDisk  : Print physical disk information
48) PhysicalMemory: Print system physical memory devices
49) Player        : Print music player name
50) PowerAdapter  : Print power adapter name and charging watts
51) Processes     : Print number of running processes
52) PublicIp      : Print your public IP address, etc
53) Separator     : Print a separator line
54) Shell         : Print current shell name and version
55) Sound         : Print sound devices, volume, etc
56) Swap          : Print swap (paging file) space usage
57) Terminal      : Print current terminal name and version
58) TerminalFont  : Print font name and size used by current terminal
59) TerminalSize  : Print current terminal size
60) TerminalTheme : Print current terminal theme (foreground and background colors)
61) Title         : Print title, which contains your user name, hostname
62) Theme         : Print current theme of desktop environment
63) TPM           : Print info of Trusted Platform Module (TPM) Security Device
64) Uptime        : Print how long system has been running
65) Users         : Print users currently logged in
66) Version       : Print Fastfetch version
67) Vulkan        : Print highest Vulkan version supported by the GPU
68) Wallpaper     : Print image file path of current wallpaper
69) Weather       : Print weather information
70) WM            : Print window manager name and version
71) Wifi          : Print connected Wi-Fi info (SSID, connection and security protocol)
72) WMTheme       : Print current theme of window manager
73) Zpool         : Print ZFS storage pools
   ```


To add fastfetch to display when opening a new terminal, add it to the bashrc file.
   ```
nano ~/.bashrc
   ```

`nano` launches the Nano text editor (a simple terminal-based text editor)
`~/.bashrc` opens the `.bashrc` file located in your home directory. This file defines how your terminal behaves each time you open it. It runs commands, sets aliases, environment variables, or custom startup banners.

Add `fastfetch` at the end.
   ```

        _,met$$$$$gg.          gowtham@deb-gowarc
     ,g$$$$$$$$$$$$$$$P.       ------------------
   ,g$$P""       """Y$$.".     OS: Debian GNU/Linux 12 (bookworm) x86_64
  ,$$P'              `$$$.     Host: HP Pavilion Gaming Laptop 15-ec2xxx
',$$P       ,ggs.     `$$b:    Kernel: Linux 6.1.0-37-amd64
`d$$'     ,$P"'   .    $$$     Uptime: 16 mins
 $$P      d$'     ,    $$P     Packages: 2264 (dpkg)
 $$:      $$.   -    ,d$$'     Shell: bash 5.2.15
 $$;      Y$b._   _,d$P'       Display (LGD05FE): 1920x1080 @ 144 Hz in 16"
 Y$$.    `.`"Y$$$$P"'          DE: KDE Plasma 5.27.5
 `$$b      "-.__               WM: KWin (X11)
  `Y$$b                        WM Theme: Breeze
   `Y$$.                       Theme: Breeze (Light) [Qt], Breeze [GTK2/3]
     `$$b.                     Icons: breeze [Qt], breeze [GTK2/3/4]
       `Y$$b.                  Font: Noto Sans (10pt) [Qt], Noto Sans (10pt) [GTK2/3/4]
         `"Y$b._               Cursor: breeze (24px)
             `""""             Terminal: konsole 22.12.3
                               CPU: AMD Ryzen 7 5800H (16) @ 3.20 GHz
                               GPU 1: NVIDIA GeForce RTX 3050 Mobile [Discrete]
                               GPU 2: AMD Radeon Vega Series / Radeon Vega Mobile Series [Integrated]
                               Memory: 2.59 GiB / 14.97 GiB (17%)
                               Swap: 0 B / 977.00 MiB (0%)
                               Disk (/): 9.52 GiB / 27.33 GiB (35%) - ext4
                               Disk (/home): 14.93 GiB / 439.45 GiB (3%) - ext4
                               Local IP (wlo1): 192.168.0.110/24
                               Battery (Primary): 38% [Discharging]
                               Locale: en_US.UTF-8

gowtham@deb-gowarc:~$ 

   ```


## 2. Oh My Zsh

In order for Oh My Zsh to work, check if Zsh is installed using `zsh --version`. If not,
```
sudo apt install zsh
```

Follow the github [repo](https://github.com/ohmyzsh/ohmyzsh) for installing `ohmyzsh`.
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
After installation, create a new profile on the KDE Konsole and add zsh as default shell by adding `/bin/zsh` as the command in profile settings.

### Themes

To change the theme, edit the theme section in the `.zshrc` file using,
```
nano ~/.zshrc
```
A complete list of themes is [here](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes). I personally like `Agnoster`

Since, the default terminal is now changed to zsh, we can add fastfetch command at the end of the script to show the fastfetch details.

When you want to change directory, it isn’t necessary to enter the `cd command`; `cd Desktop` just becomes `Desktop`. And that works with paths, too: you can enter `/home/gowtham/Desktop` and end up right where you wanted to go.
Typing `...` will move you up two directories. The `first two dots` will take `one dir` backwards. the rest `single dots` takes `one backwards`.

Oh My Zsh also supports dynamic path completion, so typing `/h/g/De` and pressing `Tab` will expand the path to `/home/gowtham/Desktop`. 

### Plugins make life easier

**docker-compose**

Add the needed [plugin](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins) to the `.zshrc` file. For example, `plugins=(git docker-compose)`. [Here](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/docker-compose) is a list of all the shortcut commands for docker-composer.

After adding this, restart the terminal or use `source ~/.zshrc`. 

Now, instead of using `docker compose up`, we can use `dcup`. 

**web-search**

you’re in the terminal and you need to google something.

No need to switch over to your browser, just run the search from your terminal with the [Web Search](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/web-search) plugin. 

```
brs uni oldenburg
```

To open raw urls, use
```
open amazon.de
```


## 3. Thunderbird HTML Signature

Here is a template for custom minimalistic signature 


```
<div style="font-family: Arial, sans-serif; font-size: 12px; color: #333; line-height: 1.4;">
  <p style="margin: 0; font-weight: bold;">Best Regards,</p>
  <p style="margin: 0; font-weight: bold;">Gowtham Mahendran</p>
  <p style="margin: 0;">MSc Sustainable Renewable Energy Technologies</p>
  <p style="margin: 0;">University of Oldenburg, Germany</p>
  <p style="margin: 0;"><a href="https://linkedin.com/in/gowtham-mahendran" style="color: #1a73e8; text-decoration: none;">LinkedIn</a> | <a href="https://github.com/Gowtham-Mahendran?tab=repositories" style="color: #1a73e8; text-decoration: none;">GitHub</a></p>
</div>
```

To fix text while composing a mail, change the settings like below

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/d86b722d-13a1-4376-97ee-1385e041beba" />

