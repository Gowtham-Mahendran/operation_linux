# Coding

## 1. VSCodium

   Here is the official guide from the VSCodium [website](https://vscodium.com/)

   1. Add the GPG key of the repository:
   ```
wget -qO - https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg \
    | gpg --dearmor \
    | sudo dd of=/usr/share/keyrings/vscodium-archive-keyring.gpg
   ```

   2. Add the repository:
   ```
echo 'deb [arch=amd64,arm64 signed-by=/usr/share/keyrings/vscodium-archive-keyring.gpg] https://download.vscodium.com/debs vscodium main' \
    | sudo tee /etc/apt/sources.list.d/vscodium.list
   ```
   3. Update then install vscodium (if you want vscodium-insiders, then replace codium by codium-insiders):
   ```
sudo apt update && sudo apt install codium
   ```

   4. To open VSCodium, execute `codium` in terminal


## 2. Docker Desktop

   1. KVM virtualization support - [Refer](https://docs.docker.com/desktop/setup/install/linux/)

   Docker Desktop runs a VM that requires [KVM support](https://www.linux-kvm.org/). The kvm module should load automatically if the host has virtualization support.

KVM (Kernel-based Virtual Machine) is a Linux kernel module that enables your system to act as a hypervisor — allowing you to run virtual machines (VMs) with near-native performance. KVM lets your Linux system use the CPU's hardware virtualization features to run other operating systems or environments efficiently. It’s built into the Linux kernel (kvm, kvm_intel, or kvm_amd modules). Unlike Docker Engine (which runs containers natively on Linux), Docker Desktop for Linux, Runs Docker inside a virtual machine (VM) for isolation and consistency (like it does on Windows/macOS). That VM is powered by QEMU/KVM. So, Docker Desktop requires KVM to:

* Create and run that internal VM efficiently
* Use hardware-accelerated virtualization

To Check if your CPU supports virtualization:
   ```
egrep -c '(vmx|svm)' /proc/cpuinfo
   ```
If output is 0: your CPU doesn’t support virtualization or it’s disabled in BIOS. If >0: your CPU supports virtualization. Also, make sure virtualization is enabled in BIOS/UEFI.


To check if the KVM modules are enabled, run:
   ```
gowtham@deb-gowarc:~$ lsmod | grep kvm
kvm_amd               163840  0
kvm                  1146880  1 kvm_amd
irqbypass              16384  1 kvm
ccp                   118784  1 kvm_amd
   ```

[Set up KVM device user permissions](https://docs.docker.com/desktop/setup/install/linux/#set-up-kvm-device-user-permissions)
To check ownership of /dev/kvm, run :

   ```
$ ls -al /dev/kvm
crw-rw----+ 1 root kvm 10, 232 Jun 29 11:54 /dev/kvm
   ```
Add your user to the kvm group in order to access the kvm device:

   ```
 sudo usermod -aG kvm $USER
   ```

Sign out and sign back in so that your group membership is re-evaluated.

Check and verify,
   ```
gowtham@deb-gowarc:~$ groups gowtham
gowtham : gowtham cdrom floppy sudo audio dip video plugdev users kvm netdev bluetooth lpadmin scanner
   ```

   2. GNOME terminal - [Refer](https://docs.docker.com/desktop/setup/install/linux/debian/)

If you're not using GNOME, you must install gnome-terminal to enable terminal access from Docker Desktop:
   ```
 sudo apt install gnome-terminal
   ```
   3. [Uninstall old versions](https://docs.docker.com/engine/install/debian/#uninstall-old-versions)
Before you can install Docker Engine, you need to uninstall any conflicting packages.

Your Linux distribution may provide unofficial Docker packages, which may conflict with the official packages provided by Docker. You must uninstall these packages before you install the official version of Docker Engine.

The unofficial packages to uninstall are:
* docker.io
* docker-compose
* docker-doc
* podman-docker
Moreover, Docker Engine depends on `containerd` and `runc`. Docker Engine bundles these dependencies as one bundle: `containerd.io`. If you have installed the `containerd` or `runc` previously, uninstall them to avoid conflicts with the versions bundled with Docker Engine.

Run the following command to uninstall all conflicting packages:
   ```
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
   ```

This will uninstall all the packages if anything is present.

   4. [Install using the apt repository](https://docs.docker.com/engine/install/debian/#install-using-the-repository)

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker apt repository. Afterward, you can install and update Docker from the repository.
   ```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
   ```

To install Docker packages, use
   ```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

Verify that the installation is successful by running the hello-world image:

   ```
 sudo docker run hello-world
   ```
This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.

   ```
gowtham@deb-gowarc:~$ sudo docker run hello-world
[sudo] password for gowtham: 
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
e6590344b1a5: Pull complete 
Digest: sha256:940c619fbd418f9b2b1b63e25d8861f9cc1b46e3fc8b018ccfe8b78f19b8cc4f
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
   ```

You have now successfully installed and started Docker Engine.

   5. Download the latest [DEB package](https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64).

   6. Install the package using apt:
   ```
sudo apt-get update
sudo apt-get install ./docker-desktop-amd64.deb
   ```

At the end of the installation process, apt displays an error due to installing a downloaded package. You can ignore this error message. [Refer](https://docs.docker.com/desktop/setup/install/linux/debian/)
   ```
N: Download is performed unsandboxed as root, as file '/home/user/Downloads/docker-desktop.deb' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)
   ```
   8. Open Docker desktop application and we need to configure the Docker credentials to use the Docker desktop!

If you’re using Docker Desktop on Debian and you see errors or can’t sign in due to missing Docker credential store, it usually means the `docker-credential-pass` helper isn't set up. This is needed when Docker Desktop tries to securely store your login credentials.

`docker-credential-pass` is a credential helper that stores your Docker credentials using GNU pass, which in turn uses GPG encryption. Docker Desktop tries to use this helper by default on Linux when logging in.

Install pass and gnupg
   ```
sudo apt install pass gnupg2
   ```
Generate a GPG key
   ```
gowtham@deb-gowarc:~$ gpg --full-generate-key
gpg (GnuPG) 2.2.40; Copyright (C) 2022 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1w
Key expires at Sun 06 Jul 2025 03:08:06 PM CEST
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: xxxx
Email address: xxxxxxxxxx@xxx.com
Comment: Docker Desktop
You selected this USER-ID:
    "xxxx (Docker Desktop) <xxxxxxxxxx@xxx.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: directory '/home/gowtham/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/gowtham/.gnupg/openpgp-revocs.d/xxxxxxxxxxx.rev'
public and secret key created and signed.

pub   rsa4096 2025-06-29 [SC] [expires: 2025-07-06]
      xxxxxxxxxxxxx
uid                      xxxxx (Docker Desktop) <xxxxxxxxxx@xxxx.com>
sub   rsa4096 2025-06-29 [E] [expires: 2025-07-06]
   ```
Once done, list your keys:
   ```
gpg --list-secret-keys --keyid-format LONG
   ```
Output will look like:
   ```
/home/gowtham/.gnupg/secring.gpg
------------------------------------
sec   rsa4096/ABCDEF1234567890 2025-06-29 [SC]
      Key fingerprint = XXXX XXXX XXXX XXXX XXXX
uid                          Gowtham <you@example.com>
   ```

Copy the part after rsa4096/ — that's your GPG key ID.

Initialize `pass` with your GPG key
   ```
pass init ABCDEF1234567890
   ```

Install `docker-credential-pass`

**docker-credential-helpers** = a project/repository containing multiple credential helpers (including docker-credential-pass)

**docker-credential-pass** = the actual binary you need to use with Docker to store credentials using pass (GPG-based)

The Docker Credential helpers can be found in the [Github page](https://github.com/docker/docker-credential-helpers). The executables can be found in the [release page](https://github.com/docker/docker-credential-helpers/releases)

To download and install,
   ```
curl -Lo docker-credential-pass https://github.com/docker/docker-credential-helpers/releases/download/v0.9.3/docker-credential-pass-v0.9.3.linux-amd64
chmod +x docker-credential-pass
sudo mv docker-credential-pass /usr/local/bin/
   ```
To confirm if it’s installed:
   ```
which docker-credential-pass
   ```
It should return:
   ```
/usr/local/bin/docker-credential-pass
   ```

To configure Docker to use it with:
   ```
nano ~/.docker/config.json
   ```

"credsStore": "desktop" tells Docker to use Docker Desktop's built-in credential store, which usually integrates with the system keyring or GUI-specific helpers.

However, on Debian Linux, the "desktop" credential store might not work properly unless you have all its dependencies set up — and it's likely why your Docker login is failing. Since you’ve now installed docker-credential-pass and setted up pass with GPG, you should replace "desktop" with "pass":

   ```
{
  "credsStore": "pass"
}
   ```

Now, execute `docker login`, Click the link shown and paste the OTP, create an account and login in. It will show success message.
   ```
gowtham@deb-gowarc:~$ sudo docker login

USING WEB-BASED LOGIN

i Info → To sign in with credentials on the command line, use 'docker login -u <username>'
         

Your one-time device confirmation code is: xxxx-xxxx
Press ENTER to open your browser or submit your device code here: https://login.docker.com/activate

Waiting for authentication in the browser…
Login succeeded
   ```
After successful login, `Login succeeded` message will be shown. Close and reopen docker desktop app and the user will be logged in.



**Once the GPG key expires, see the expiration using the command,**
   ```
gpg --list-secret-keys --keyid-format LONG
   ```
Copy the part after rsa4096/ — that’s your key ID.

Edit the key using,
   ```
gpg --edit-key ABCDEF1234567890
   ```
(replace ABCDEF1234567890 with your actual key ID)

Inside the GPG prompt, run `expire`. GPG will prompt you to set a new expiration date for: the primary key and optionally for subkeys (choose yes for each)
   ```
Key is valid for? (0) 1y
   ```

After setting expiration, type `save` and the GPG will update and exit.


   7. For post-install - refer [here](https://docs.docker.com/engine/install/linux-postinstall/) (not needed since we are using Docker desktop)


## Resolving Docker duplicate installations

If there are 2 docker versions, they may conflict with each other. To check,
```
gowtham@deb-gowarc:~/Documents/Gitlab/python-course-platform$ docker context ls
NAME            DESCRIPTION                               DOCKER ENDPOINT                                    ERROR
default *       Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                        
desktop-linux   Docker Desktop                            unix:///home/gowtham/.docker/desktop/docker.sock   
```

The star* next to desktop-linux was there before you switched. After `docker context use default`, the “*” moves to default, meaning all CLI commands now talk to /var/run/docker.sock (the system daemon) instead of the Desktop socket. Now you can safely uninstall docker desktop if you are okay working with CLI (Command Line Interface).

To simply delete the named “desktop-linux” context from your local Docker CLI configuration.
```
docker context rm desktop-linux
```
Stop & disable the Desktop service 
```
systemctl --user stop docker-desktop
systemctl --user disable docker-desktop
```
Uninstall the Docker Desktop package using
```
sudo apt remove docker-desktop
sudo apt autoremove
```