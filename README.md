# Environment Setup
This is a guide to install WSL, integrate it in VSCode, and other things...

## 1. Install WSL

### Install command
  Open PowerShell in administrator mode, and write
```bash
wsl --install
```

### Set username and password
- This User Name and Password is specific to each separate Linux distribution that you install
- Please note that whilst entering the Password, nothing will appear on screen. This is called blind typing
- Once you create a User Name and Password, the account will be your default user for the distribution and automatically sign-in on launch
- This account will be considered the Linux administrator, with the ability to run sudo (Super User Do) administrative commands
- Each Linux distribution running on WSL has its own Linux user accounts and passwords. You will have to configure a Linux user account every time you add a distribution, reinstall, or reset
  
*To change or reset your password, open the Linux distribution and enter the command: passwd*
    
### Update and upgrade packages
```bash
sudo apt update && sudo apt upgrade
```

### File storage
To open your WSL project in Windows File Explorer, enter: explorer.exe .

*Be sure to add the period at the end of the command to open the current directory*

## 2. Set Up WSL in VSCode
Why?
- develop in a Linux-based environment
- use Linux-specific toolchains and utilities
- run and debug your Linux-based applications from the comfort of Windows
- use the VS Code built-in terminal to run your Linux distribution of choice

nice.
### Install VSCode
...
### Install extension pack
Install the [Remote Development extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
### Update Linux Distribution
