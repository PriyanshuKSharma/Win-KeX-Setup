# Ubuntu GUI on WSL 2 --- XFCE + XRDP

A practical setup guide for running a **full Ubuntu graphical desktop
(XFCE)** inside **Windows Subsystem for Linux 2 (WSL 2)** using **XRDP**
and Windows Remote Desktop.

This setup is useful when you want a desktop-style Ubuntu environment on
Windows without installing Ubuntu in a traditional virtual machine.

------------------------------------------------------------------------

## Table of Contents

1.  [Overview](#overview)
2.  [Architecture](#architecture)
3.  [Prerequisites](#prerequisites)
4.  [Verify WSL](#verify-wsl)
5.  [Verify and Reset the Ubuntu
    Password](#verify-and-reset-the-ubuntu-password)
6.  [Install XFCE](#install-xfce)
7.  [Install XRDP](#install-xrdp)
8.  [Configure XFCE for XRDP](#configure-xfce-for-xrdp)
9.  [Start XRDP](#start-xrdp)
10. [Find the WSL IP Address](#find-the-wsl-ip-address)
11. [Connect from Windows Remote
    Desktop](#connect-from-windows-remote-desktop)
12. [Verify the GUI](#verify-the-gui)
13. [Browser Configuration](#browser-configuration)
14. [Common Troubleshooting](#common-troubleshooting)
15. [Restarting the Environment](#restarting-the-environment)
16. [Useful Commands](#useful-commands)
17. [Optional Improvements](#optional-improvements)
18. [Notes and Limitations](#notes-and-limitations)

------------------------------------------------------------------------

## Overview

### Goal

Run a complete XFCE desktop session inside Ubuntu WSL 2:

``` text
Windows 11
    │
    ▼
Windows Remote Desktop (mstsc)
    │
    ▼
Ubuntu WSL 2
    │
    ├── XRDP
    │
    └── XFCE Desktop
          ├── Terminal
          ├── File Manager
          ├── Settings
          ├── Applications
          └── Linux GUI applications
```

### Why XFCE + XRDP?

XFCE is lightweight and works well for a WSL-based desktop. XRDP
provides an RDP endpoint that can be opened using the built-in Windows
Remote Desktop client.

This is an **Ubuntu equivalent of the full-desktop experience** provided
by tools such as Win-KeX on Kali Linux. Win-KeX itself is Kali-specific
and is not required for this Ubuntu setup.

------------------------------------------------------------------------

# Prerequisites

You should have:

-   Windows 10/11
-   WSL 2
-   Ubuntu installed as a WSL distribution
-   A normal Ubuntu user account
-   `sudo` access
-   Windows Remote Desktop client (`mstsc`)

Check your WSL distributions from PowerShell:

``` powershell
wsl -l -v
```

Expected example:

``` text
  NAME      STATE     VERSION
* Ubuntu    Running   2
```

Check the installed WSL version:

``` powershell
wsl --version
```

------------------------------------------------------------------------

# Verify WSL

From PowerShell:

``` powershell
wsl --status
```

You can also update WSL:

``` powershell
wsl --update
```

To completely stop WSL:

``` powershell
wsl --shutdown
```

Start Ubuntu again:

``` powershell
wsl -d Ubuntu
```

> If your distribution has a different name, replace `Ubuntu` with the
> exact name shown by `wsl -l -v`.

------------------------------------------------------------------------

# Verify and Reset the Ubuntu Password

`sudo` inside Ubuntu uses the **Linux/Ubuntu user's password**, not the
Windows PIN.

For example:

``` bash
sudo whoami
```

Expected result:

``` text
root
```

## If the Ubuntu password is forgotten

Exit Ubuntu:

``` bash
exit
```

From Windows PowerShell:

``` powershell
wsl -l -v
```

Then start the distribution as root:

``` powershell
wsl -d Ubuntu -u root
```

Reset the password for the normal user:

``` bash
passwd prikush
```

Replace `prikush` with your actual Ubuntu username.

After changing it:

``` bash
exit
```

Start Ubuntu normally and test:

``` bash
sudo whoami
```

------------------------------------------------------------------------

# Install XFCE

Inside Ubuntu:

``` bash
sudo apt update
```

Install XFCE:

``` bash
sudo apt install xfce4 xfce4-goodies -y
```

During installation, Ubuntu may ask you to select a display manager.

For this setup, `lightdm` is a suitable choice.

If you are not prompted, continue normally.

------------------------------------------------------------------------

# Install XRDP

Install XRDP:

``` bash
sudo apt install xrdp -y
```

Start the XRDP service:

``` bash
sudo service xrdp start
```

Check its status:

``` bash
sudo service xrdp status
```

A working service should show something similar to:

``` text
Active: active (running)
```

You can also verify that the RDP port is listening:

``` bash
ss -lntp | grep 3389
```

Expected output should contain:

``` text
:3389
```

Port `3389` is the standard RDP port.

------------------------------------------------------------------------

# Configure XFCE for XRDP

Configure the user's desktop session:

``` bash
echo "xfce4-session" > ~/.xsession
```

If you previously used:

``` bash
echo "startxfce4" > ~/.xsession
```

the `xfce4-session` configuration can be used instead:

``` bash
echo "xfce4-session" > ~/.xsession
```

Verify:

``` bash
cat ~/.xsession
```

Expected:

``` text
xfce4-session
```

------------------------------------------------------------------------

# Start XRDP

Start the service:

``` bash
sudo service xrdp start
```

Check:

``` bash
sudo service xrdp status
```

If systemd is enabled in your WSL installation, you can also check:

``` bash
systemctl status xrdp --no-pager
```

------------------------------------------------------------------------

# Find the WSL IP Address

Inside Ubuntu:

``` bash
hostname -I
```

Example:

``` text
192.168.116.151 172.18.0.1 172.17.0.1 fc00:f853:ccd:e793::1
```

For the RDP connection, use the WSL address that is reachable from
Windows.

In the example above:

``` text
192.168.116.151
```

> The WSL IP address can change after restarting WSL or Windows. Do not
> permanently assume that the example address will remain the same.

------------------------------------------------------------------------

# Connect from Windows Remote Desktop

Press:

``` text
Win + R
```

Enter:

``` text
mstsc
```

Press Enter.

In the **Computer** field, enter your current WSL IP address:

``` text
192.168.116.151
```

Click **Connect**.

At the login screen use your Ubuntu credentials:

``` text
Username: prikush
Password: <your Ubuntu Linux password>
```

Do not use your Windows PIN unless it happens to be the same password.

------------------------------------------------------------------------

# Verify the GUI

After successful login, you should see the XFCE desktop.

Typical components include:

-   XFCE panel/taskbar
-   Applications menu
-   Terminal
-   Thunar file manager
-   Desktop
-   Settings
-   Linux GUI applications

Example session:

``` text
Windows
  │
  └── Remote Desktop
        │
        └── Ubuntu WSL 2
              │
              └── XFCE
                   ├── Thunar
                   ├── Terminal
                   ├── Settings
                   └── Applications
```

At this point the Ubuntu GUI setup is complete.

------------------------------------------------------------------------

# Browser Configuration

If clicking the browser icon produces an error similar to:

``` text
Failed to execute child process "www-browser":
Failed to execve: No such file or directory
```

this generally means a browser/default browser command is not available.

First check:

``` bash
which firefox
```

If Firefox is available:

``` bash
firefox
```

If it is not installed, you can attempt:

``` bash
sudo apt update
sudo apt install firefox -y
```

Then test:

``` bash
firefox
```

> Ubuntu releases can package Firefox differently, including through
> Snap. If the installation reports a Snap-related problem, troubleshoot
> that separately rather than repeatedly reinstalling packages.

You can inspect available browsers with:

``` bash
ls /usr/bin | grep -E 'firefox|chromium|google-chrome'
```

------------------------------------------------------------------------

# Common Troubleshooting

## 1. `sudo: Authentication failed`

Example:

``` text
sudo: Authentication failed, try again.
```

The password is your **Ubuntu Linux user's password**.

If forgotten, reset it from Windows by starting Ubuntu as root:

``` powershell
wsl -d Ubuntu -u root
```

Then:

``` bash
passwd <username>
```

------------------------------------------------------------------------

## 2. `Failed to allocate directory watch: Too many open files`

Example:

``` text
Failed to allocate directory watch: Too many open files
```

This can occur when the inotify limits are exhausted.

Check:

``` bash
cat /proc/sys/fs/inotify/max_user_watches
cat /proc/sys/fs/inotify/max_user_instances
ulimit -n
```

A reasonable configuration for a development desktop is:

``` text
max_user_watches     524288
max_user_instances   1024
```

You can temporarily increase the inotify instance limit:

``` bash
sudo sysctl -w fs.inotify.max_user_instances=1024
```

Verify:

``` bash
cat /proc/sys/fs/inotify/max_user_instances
```

Then restart WSL from PowerShell:

``` powershell
wsl --shutdown
```

Start Ubuntu again and test XRDP:

``` bash
sudo service xrdp start
```

### Make the inotify setting persistent

Edit:

``` bash
sudo nano /etc/sysctl.conf
```

Add:

``` text
fs.inotify.max_user_watches=524288
fs.inotify.max_user_instances=1024
```

Save:

``` text
Ctrl + O
Enter
Ctrl + X
```

Apply:

``` bash
sudo sysctl -p
```

------------------------------------------------------------------------

## 3. XRDP does not start

Check:

``` bash
sudo service xrdp status
```

Then:

``` bash
ss -lntp | grep 3389
```

If systemd is enabled:

``` bash
systemctl status xrdp --no-pager
```

Check XRDP logs:

``` bash
sudo journalctl -u xrdp --no-pager -n 100
```

Also check:

``` bash
sudo journalctl -u xrdp-sesman --no-pager -n 100
```

------------------------------------------------------------------------

## 4. Black screen after RDP login

Verify:

``` bash
cat ~/.xsession
```

It should contain:

``` text
xfce4-session
```

Then restart XRDP:

``` bash
sudo service xrdp restart
```

If the problem persists, inspect:

``` bash
cat ~/.xsession-errors
```

and:

``` bash
sudo journalctl -u xrdp-sesman --no-pager -n 100
```

------------------------------------------------------------------------

## 5. WSL IP changed

Run:

``` bash
hostname -I
```

Use the current reachable WSL address in `mstsc`.

For example, if it changed from:

``` text
192.168.116.151
```

to:

``` text
192.168.116.160
```

use:

``` text
192.168.116.160
```

------------------------------------------------------------------------

## 6. GUI works but a specific application does not launch

First test whether the application exists:

``` bash
which <application>
```

Example:

``` bash
which firefox
```

Then try launching it from the XFCE terminal:

``` bash
<application>
```

Running an application from the terminal is useful because it exposes
the actual error message.

------------------------------------------------------------------------

# Restarting the Environment

## Restart XRDP only

Inside Ubuntu:

``` bash
sudo service xrdp restart
```

## Stop XRDP

``` bash
sudo service xrdp stop
```

## Start XRDP

``` bash
sudo service xrdp start
```

## Completely restart WSL

From Windows PowerShell:

``` powershell
wsl --shutdown
```

Then:

``` powershell
wsl -d Ubuntu
```

After Ubuntu starts:

``` bash
sudo service xrdp start
```

Find the new IP:

``` bash
hostname -I
```

Then connect using:

``` text
mstsc
```

------------------------------------------------------------------------

# Useful Commands

## WSL --- Windows PowerShell

List distributions:

``` powershell
wsl -l -v
```

Show WSL status:

``` powershell
wsl --status
```

Show WSL version:

``` powershell
wsl --version
```

Update WSL:

``` powershell
wsl --update
```

Stop all WSL instances:

``` powershell
wsl --shutdown
```

Start Ubuntu:

``` powershell
wsl -d Ubuntu
```

Start Ubuntu as root:

``` powershell
wsl -d Ubuntu -u root
```

------------------------------------------------------------------------

## Ubuntu

Current user:

``` bash
whoami
```

Current hostname:

``` bash
hostname
```

WSL IP:

``` bash
hostname -I
```

Ubuntu version:

``` bash
cat /etc/os-release
```

Kernel:

``` bash
uname -a
```

XFCE version:

``` bash
xfce4-session --version
```

XRDP version:

``` bash
xrdp --version
```

XRDP status:

``` bash
sudo service xrdp status
```

RDP port:

``` bash
ss -lntp | grep 3389
```

------------------------------------------------------------------------

# Optional Improvements

Once the basic GUI is stable, the environment can be improved further.

## 1. Create a Windows launcher

Instead of manually doing:

``` text
wsl → hostname -I → mstsc
```

you can create a PowerShell script that:

1.  Starts Ubuntu
2.  Starts XRDP
3.  Detects the current WSL IP
4.  Launches Windows Remote Desktop

This makes the experience close to a one-click Ubuntu desktop.

------------------------------------------------------------------------

## 2. Create a Desktop Shortcut

A Windows shortcut can be created to launch the Ubuntu GUI launcher
directly.

Suggested workflow:

``` text
Double-click Ubuntu GUI
        │
        ▼
Start WSL
        │
        ▼
Start XRDP
        │
        ▼
Find WSL IP
        │
        ▼
Open mstsc
        │
        ▼
Ubuntu XFCE Desktop
```

------------------------------------------------------------------------

## 3. Configure Automatic XRDP Startup

If systemd is enabled in WSL, XRDP can be configured to start
automatically:

``` bash
sudo systemctl enable xrdp
```

Check:

``` bash
systemctl is-enabled xrdp
```

Expected:

``` text
enabled
```

WSL/systemd configuration varies by Ubuntu/WSL version, so verify
systemd is actually enabled before relying on this.

------------------------------------------------------------------------

## 4. Windows ↔ Ubuntu Files

Windows drives are normally available under:

``` bash
/mnt/
```

For example:

``` bash
/mnt/c/
```

Windows user files can commonly be accessed through:

``` bash
/mnt/c/Users/
```

Your Linux home directory is:

``` bash
/home/prikush/
```

------------------------------------------------------------------------

# Notes and Limitations

### WSL is not a traditional VM

WSL 2 uses a lightweight virtualized Linux environment and has different
networking and lifecycle behavior from a conventional virtual machine.

### WSL IP addresses can change

Do not hard-code the WSL IP unless you have deliberately configured a
networking solution for that purpose.

### XRDP is separate from WSLg

Windows 11's WSLg can run many Linux GUI applications directly without a
traditional full desktop session. This guide instead uses **XFCE +
XRDP** because the goal is a complete Linux desktop environment.

### Win-KeX is not used here

Win-KeX is primarily a Kali Linux solution. Ubuntu does not need
`kali-win-kex`.

### Avoid running the desktop as root

Use your normal Linux user (`prikush` in this example) for the graphical
session. Use `sudo` only when administrative privileges are required.

------------------------------------------------------------------------

# Quick Reference --- Complete Setup

Once Ubuntu and WSL 2 are installed, the essential commands are:

### Install XFCE

``` bash
sudo apt update
sudo apt install xfce4 xfce4-goodies -y
```

### Install XRDP

``` bash
sudo apt install xrdp -y
```

### Configure XFCE

``` bash
echo "xfce4-session" > ~/.xsession
```

### Start XRDP

``` bash
sudo service xrdp start
```

### Verify

``` bash
sudo service xrdp status
ss -lntp | grep 3389
```

### Get IP

``` bash
hostname -I
```

### Connect from Windows

``` text
Win + R
→ mstsc
→ <WSL IP>
→ Ubuntu username/password
```

------------------------------------------------------------------------

# Final Architecture

``` text
┌────────────────────────────── Windows ──────────────────────────────┐
│                                                                     │
│                    Remote Desktop (mstsc)                           │
│                              │                                      │
│                              │ RDP :3389                            │
│                              ▼                                      │
│                 ┌──────── Ubuntu WSL 2 ────────┐                   │
│                 │                              │                   │
│                 │            XRDP              │                   │
│                 │              │               │                   │
│                 │              ▼               │                   │
│                 │            XFCE              │                   │
│                 │                              │                   │
│                 │   ┌────────┐ ┌───────────┐  │                   │
│                 │   │Terminal│ │   Thunar  │  │                   │
│                 │   └────────┘ └───────────┘  │                   │
│                 │                              │                   │
│                 │   Linux GUI Applications     │                   │
│                 └──────────────────────────────┘                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

------------------------------------------------------------------------

## Status Checklist

Use this checklist when rebuilding the environment in the future:

-   [ ] WSL 2 installed
-   [ ] Ubuntu installed
-   [ ] Ubuntu user/password working
-   [ ] `sudo` working
-   [ ] XFCE installed
-   [ ] XRDP installed
-   [ ] `~/.xsession` configured
-   [ ] XRDP service running
-   [ ] Port `3389` listening
-   [ ] WSL IP obtained
-   [ ] Windows Remote Desktop connected
-   [ ] XFCE desktop visible
-   [ ] Browser configured
-   [ ] Optional launcher created

------------------------------------------------------------------------

**Environment:** Ubuntu on WSL 2 + XFCE + XRDP\
**Purpose:** Full Ubuntu GUI desktop on Windows\
**Primary connection:** Windows Remote Desktop (`mstsc`)
