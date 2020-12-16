# rubuntu-wsl

## 1. Enable Windows Subsystem 2 for Linux

Open PowerShell as Admin and run:

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

dism.exe /online /enable-feature featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```
It says `/norestart` but, yeah, reboot.

## 2. Download and install the Linux kernel update package

https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

## 3. Enable WSL 2

This step requiers Windows 10 version 1903 or higher, check `winver`.  
Open PowerShell and run:
```
wsl --set-default-version 2
```

## 4. Download Ubuntu 20.04
Linux distros can be installed from the Microsoft store. But we can also just use:  
https://aka.ms/wslubuntu2004
Enter your Linux user name and password and you are ready to go.
```
sudo apt update
sudo apt upgrade -y
```

## 5. Fix the ssh port
Per default, our new distro listen on port 22 for ssh connections.
That might/will conflict with your Windows host, thus let's fix this:
```
sudo nano /etc/ssh/sshd_config
```
Replace the lines
```
#port 22
PasswordAuthentication no
```
with
```
port 2222
PasswordAuthentication yes
```
Press `CTRL-X y` to save the changes. Now, restart the ssh server:
```
sudo ssh-keygen -A
sudo service ssh start
```
Now we can 'ssh' into the distro from CMD or PowerShell:
```
ssh user@127.0.0.1 -p 2222
```
or even remotely:
