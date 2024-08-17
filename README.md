# Win-KeX-Setup Guide for Kali Linux on WSL 2
Welcome to the Win-KeX Setup Guide! This repository is dedicated to helping users set up and use Win-KeX, a GUI desktop environment for Kali Linux on Windows Subsystem for Linux (WSL 2). Whether you're a beginner or an advanced user, this guide will walk you through the installation and configuration process.

## **Table of Contents**

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Running Win-KeX](#running-win-kex)
   - [Window Mode](#window-mode)
   - [Enhanced Session Mode](#enhanced-session-mode)
   - [Seamless Mode](#seamless-mode)
5. [Optional Steps](#optional-steps)
   - [Installing Kali’s Default Tools](#installing-kalis-default-tools)
   - [Configuring Windows Terminal](#configuring-windows-terminal)
6. [Troubleshooting](#troubleshooting)
7. [Help and Resources](#help-and-resources)
8. [Contributing](#contributing)
9. [License](#license)

---

## **Introduction**

Win-KeX provides a graphical desktop environment for Kali Linux running on WSL 2, offering a seamless experience that bridges the gap between Windows and Kali. With Win-KeX, users can easily toggle between different environments, utilize the full power of Kali's tools, and enjoy enhanced features like sound support, clipboard sharing, and multi-session capabilities.

### **Key Features of Win-KeX**:
- **Window Mode**: Run Kali Linux in a dedicated window with sound support.
- **Seamless Mode**: Integrate Kali Linux applications and menus directly into the Windows desktop.
- **Enhanced Session Mode**: Utilize RDP for a more feature-rich experience, similar to Hyper-V.
- **Sound and Clipboard Sharing**: Seamless copy-paste and audio support between Kali and Windows.
- **Multi-Session Support**: Run both root and unprivileged sessions concurrently.
- **WSLg Compatibility**: Fully compatible with WSLg for enhanced graphics support.

---

## **Prerequisites**

Before proceeding with the installation, ensure that your system meets the following prerequisites:

### **1. Windows 10 or 11 with WSL 2 Enabled**

To use Win-KeX, you must have WSL 2 enabled on your Windows system. If you haven't set up WSL 2 yet, follow [this guide](https://docs.microsoft.com/en-us/windows/wsl/install) to install and configure it.

### **2. Kali Linux Installed on WSL 2**

You need Kali Linux installed on WSL 2. You can install Kali Linux from the Microsoft Store or manually configure it. Refer to [this guide](https://www.kali.org/docs/wsl/win-kex/) for detailed installation instructions.

### **3. Windows Terminal Installed**

While you can use the default Windows command prompt, Windows Terminal provides a more feature-rich environment and is recommended for running and managing your WSL distributions. Download Windows Terminal from the [Microsoft Store](https://aka.ms/terminal).

---

## **Installation**

Follow these steps to install Win-KeX on your Kali Linux WSL instance:

### **Step 1: Update Your System**

Before installing Win-KeX, it's important to ensure that your Kali Linux system is up to date. Open your Kali Linux terminal in WSL and run:

```bash
sudo apt update && sudo apt upgrade -y
```

This command updates the package lists and installs the latest versions of installed packages.

### **Step 2: Install Win-KeX**

After updating your system, install Win-KeX by running the following command:

```bash
sudo apt install -y kali-win-kex
```

This will install all the necessary components for Win-KeX, enabling you to use the graphical desktop environment.

---

## **Running Win-KeX**

Win-KeX offers three different modes to suit various use cases. Each mode provides a unique way to interact with your Kali Linux environment.

### **1. Window Mode**

Window Mode launches a dedicated Kali Linux desktop window, allowing you to work in a familiar desktop environment with sound support.

- **To start Win-KeX in Window Mode from within Kali WSL**, run:
  ```bash
  kex --win -s
  ```

- **To start Win-KeX in Window Mode from the Windows Command Prompt**, run:
  ```bash
  wsl -d kali-linux kex --win -s
  ```

This mode is ideal for users who prefer a separate window for their Kali Linux environment.

### **2. Enhanced Session Mode**

Enhanced Session Mode leverages RDP (Remote Desktop Protocol) to provide a richer, more feature-complete experience. This mode is similar to using Kali Linux on a virtual machine with Hyper-V.

- **To start Win-KeX in Enhanced Session Mode from within Kali WSL**, run:
  ```bash
  kex --esm --ip -s
  ```

- **To start Win-KeX in Enhanced Session Mode from the Windows Command Prompt**, run:
  ```bash
  wsl -d kali-linux kex --esm --ip -s
  ```

This mode is recommended for users who need advanced features like clipboard sharing and better graphical performance.

### **3. Seamless Mode**

Seamless Mode integrates Kali Linux applications directly into the Windows desktop environment. This mode allows you to run Kali applications alongside Windows applications, with shared menus and taskbars.

- **To start Win-KeX in Seamless Mode from within Kali WSL**, run:
  ```bash
  kex --sl -s
  ```

- **To start Win-KeX in Seamless Mode from the Windows Command Prompt**, run:
  ```bash
  wsl -d kali-linux kex --sl -s
  ```

Seamless Mode is perfect for users who want to blend the two environments and switch effortlessly between them.

---

## **Optional Steps**

### **Installing Kali’s Default Tools**

If you have enough disk space, you can install the full set of Kali Linux tools to get the most out of your environment. This includes all the traditional tools available in a full Kali installation:

```bash
sudo apt install -y kali-linux-large
```

This package installs a comprehensive set of tools that are useful for penetration testing and security research.

### **Configuring Windows Terminal**

To further enhance your experience, you can configure Windows Terminal to launch Win-KeX directly with custom profiles. Here are some configuration examples:

#### **Basic Win-KeX in Window Mode with Sound**

```json
{
    "guid": "{55ca431a-3a87-5fb3-83cd-11ececc031d2}",
    "hidden": false,
    "name": "Win-KeX",
    "commandline": "wsl -d kali-linux kex --wtstart -s",
}
```

#### **Advanced Win-KeX in Window Mode with Custom Icon and Directory**

```json
{
    "guid": "{55ca431a-3a87-5fb3-83cd-11ececc031d2}",
    "hidden": false,
    "icon": "file:///c:/users/<windows user>/pictures/icons/kali-menu.png",
    "name": "Win-KeX",
    "commandline": "wsl -d kali-linux kex --wtstart -s",
    "startingDirectory" : "//wsl$/kali-linux/home/<kali user>"
}
```

#### **Basic Win-KeX in Seamless Mode with Sound**

```json
{
    "guid": "{55ca431a-3a87-5fb3-83cd-11ececc031d2}",
    "hidden": false,
    "name": "Win-KeX",
    "commandline": "wsl -d kali-linux kex --sl --wtstart -s",
}
```

You can customize these profiles further to match your preferences, such as by changing the starting directory or adding additional configuration options.

---

## **Troubleshooting**

If you encounter any issues during the installation or use of Win-KeX, consider the following common solutions:

### **1. Win-KeX Fails to Start**

- **Ensure WSL 2 is correctly installed and configured.**
- **Verify that your Windows system is fully updated.**
- **Check for any package installation errors during the Win-KeX installation.**

### **2. Sound or Clipboard Sharing Not Working**

- **Ensure you are using the correct mode with the `-s` option for sound support.**
- **Verify that the necessary services are running in both Windows and Kali Linux.**

### **3. Performance Issues in Enhanced Session Mode**

- **Ensure that your system meets the minimum requirements for running RDP sessions.**
- **Consider upgrading your WSL 2 settings for better performance.**

If these solutions don't resolve your issue, refer to the [official Win-KeX documentation](https://www.kali.org/docs/wsl/win-kex/) or seek help in the community forums.

---

## **Help and Resources**

If you need further assistance, feel free to explore the following resources:

- **[Win-KeX Official Documentation](https://www.kali.org/docs/wsl/win-kex/)**: The official guide from Kali Linux developers.
- **[Kali Linux Forums](https://forums.kali.org/)**: Engage with the community for help and
