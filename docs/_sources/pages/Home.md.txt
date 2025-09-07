
# Getting Started

This guide documents the full process of replacing Windows with Debian 12 on an HP laptop, including BIOS setup, offline driver installation, and EasyRoam setup.

---

## 1. Debian & bootable drive

1. Go to the Debian download page:
   https://www.debian.org/distrib/

2. Download the **firmware-included ISO** for Debian 12:

   Example: `debian-12.5.0-amd64-DVD-1.iso`

3. Download **Rufus** from [https://rufus.ie](https://rufus.ie)

4. Use Rufus to write the ISO to a USB:
   - Select your USB
   - Choose the ISO file
   - Use **GPT partition scheme**
   - Use **UEFI (non-CSM)**

---

## 2. BIOS/UEFI Configuration

Enter BIOS Settings

- Restart your laptop and press `F10` repeatedly during boot to enter **BIOS/UEFI setup**.

Disable Secure Boot

- In BIOS, go to **Security → Secure Boot Configuration**
- Disable **Secure Boot**

Change Boot Order

- Press `F9` on startup to enter **Boot Device Options**
- Select the USB drive prefixed with `UEFI:` (e.g., `UEFI: SanDisk`)

---

## 3. Install Debian 12

1. Boot into the Debian installer from USB
2. Choose **Graphical Install**
3. When prompted for network drivers, skip if your hardware isn’t detected
4. Choose **Manual partitioning** or:
   - Option: `Separate /home partition` or
   - Option: `Separate /home, /var, and /tmp partitions`
5. Confirm partitioning and proceed with install
6. Set a **root password**
7. Create a regular user

---

## 4. Login Manager Selection

One or more desktop environment can be chosen while installing the linux. When prompted, choose:
- `gdm3` → for GNOME desktop (default)
- `sddm` → for KDE Plasma (if installed)

---

## 5. Switching to superuser

Open terminal and Switch to root to get admin privileges for modifying system and installing apps:

```bash
su -
```
To update the system,
```
apt update 
apt upgrade
```

To add `user` to sudo list, go to `root` and execute
```
usermod -aG sudo gowtham
```

To verify,

```
groups gowtham
```

This will show something like, `gowtham : gowtham cdrom floppy sudo audio dip video plugdev users netdev bluetooth lpadmin scanner`. Now `gowtham` is a `superuser` or use `sudo whoami`.
