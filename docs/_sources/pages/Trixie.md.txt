In the starting, nothing can be installed. To install the packages we need to add debian repositories

```
deb http://deb.debian.org/debian trixie main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security trixie-security main contrib non-free non-free-firmware
deb http://deb.debian.org/debian trixie-updates main contrib non-free non-free-firmware
deb http://deb.debian.org/debian trixie-backports main contrib non-free non-free-firmware
```
main → Free software (essential system & desktop packages)
contrib → Free software that depends on non-free packages
non-free → Non-free software (firmware, drivers, proprietary tools)
non-free-firmware → Firmware now separated in Bookworm+ (needed for Wi-Fi, GPU firmware, etc.)
security → Security updates for Trixie
updates → Regular bugfix updates between point releases
backports → Newer versions of packages (optional, but useful if you need fresh software)

Install aptitude package manager

```
sudo apt install aptitude
```

Install curl to install uv

```
sudo aptitude install curl
```

then

```
curl -LsSf https://astral.sh/uv/install.sh | sh
```


To extract .xz file,
```
tar -xvf thunderbird-141.0.tar.xz
```
Install extensions to enable exchange calendar

<img width="761" height="181" alt="image" src="https://github.com/user-attachments/assets/2758093f-1d0c-49c2-8ef1-7a6e9e626ed1" />

Go to TbSync account manager and add an exchange account with the credentials.

sync calendar. It should now appear in Thunderbird calendar.


## Docker

Install using the official [Documentation](https://docs.docker.com/engine/install/debian/#install-using-the-repository)

Follow [this](https://docs.docker.com/engine/install/linux-postinstall/) after installation

## VPN

Install the [GlobalProtect-openconnect](https://github.com/yuezk/GlobalProtect-openconnect) client `.deb package` 

Verify installation using 
```
gowtham@debian:~$ gpclient --version
gpclient 2.4.5 (2025-07-16)
```
To connect, use `sudo` and authenticate the login.

```
gowtham@debian:~$ sudo gpclient connect <gateway>
```
