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

Press `CTRL-X y` to save the changes. Now, (re)start the ssh server:

```
sudo ssh-keygen -A
sudo service ssh start
```
Now we can 'ssh' into the distro from CMD or PowerShell (or remotely):
```
ssh user@127.0.0.1 -p 2222
```

## 6. Install Git, R and rstudio-server

```
sudo apt install git r-base r-base-core r-recommended r-base-dev gdebi-core build-essential libcurl4-gnutls-dev libxml2-dev libssl-dev  

wget https://rstudio.org/download/latest/stable/server/bionic/rstudio-server-latest-amd64.deb  

sudo gdebi rstudio-server-latest-amd64.deb  
```

Ignore the message `Couldn't find an alternative telinit implementation to spawn.`

## 7. Start rstudio-server

```
sudo rstudio-server start
```

## 8. Access the server

Open your web-browser and navigate to: `http://127.0.0.1:8787`.

## 9. Autostart Linux servers at Windows-boot

Back in Windows, create the file `rstudio-server.vbs`:
```
Set ws = CreateObject("Wscript.Shell")  
ws.run "wsl -d Ubuntu-20.04 -u root service ssh start", vbhide  
ws.run "wsl -d Ubuntu-20.04 -u root rstudio-server start", vbhide  
```

The type `Windows key + R` to open the run-command.
Type `Shell:startup` to open the startup-folder.
Move the .vbs file to this folder.

## 10. Take a snapshot

Now that we have a 'clean' installation, it's time to take our first
snapshot. In PowerShell run:

```
wsl --export Ubuntu-20.04 rubuntu.tar.gz
```

This will create the file `rubuntu.tar.gz` which serves as backup, could be moved to a different machiene or simply 'imported' back once you have messed up our install enough:

```
wsl --unregister Ubuntu-20.04
wsl --import Ubuntu-20.04 rubuntu.tar.gz
```
