# Connect Ubuntu (WSL) to VS Code Using SSH

This guide explains how to connect Ubuntu running on Windows Subsystem for Linux (WSL) to VS Code using SSH. It covers installation, SSH configuration, connection setup, and troubleshooting.

## Step 1: Check Whether Ubuntu (WSL) Is Installed

Open **PowerShell** and run:

```powershell
wsl -l -v
```

**Expected output:**

```text
NAME      STATE      VERSION
Ubuntu    Stopped    2
```

If Ubuntu is listed, proceed to the next step.

## Step 2: Start Ubuntu

Run the following command in PowerShell:

```powershell
wsl -d Ubuntu
```

You should see a Linux terminal prompt similar to:

```bash
rishabhr136@Rishabh:~$
```

This indicates that you are inside your Ubuntu environment.

## Step 3: Install the OpenSSH Server

Run the following commands **inside Ubuntu**:

```bash
sudo apt update
sudo apt install openssh-server -y
```

Verify that the SSH client is installed:

```bash
ssh -V
```

**Example output:**

```text
OpenSSH_9.x
```

Note that `ssh -V` verifies the SSH client version. The server installation is performed by the `openssh-server` package.

## Step 4: Start the SSH Service

Start the SSH service inside Ubuntu:

```bash
sudo service ssh start
```

Check its status:

```bash
sudo service ssh status
```

Look for a status indicating that the service is running, such as:

```text
Active: active (running)
```

If the service is not running, try starting it again.

## Step 5: Find the Ubuntu IP Address

Inside Ubuntu, run:

```bash
hostname -I
```

**Example output:**

```text
172.28.165.22
```

Note your Ubuntu IP address. The address shown above is only an example; use the actual IP returned by your system.

## Step 6: Test the SSH Connection from PowerShell

Open a **new PowerShell window** and run:

```powershell
ssh rishabhr136@172.28.165.22
```

Replace `172.28.165.22` with your actual Ubuntu IP address.

### First-Time SSH Connection

You may see a prompt similar to:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

If you have verified that the host is your Ubuntu instance, type:

```text
yes
```

Enter your Ubuntu password when prompted.

If the connection is successful, you should see a Linux prompt similar to:

```bash
rishabhr136@Rishabh:~$
```

You are now connected to Ubuntu through SSH.

## Step 7: Install the Remote SSH Extension in VS Code

1. Open Visual Studio Code.
2. Press `Ctrl + Shift + X` to open Extensions.
3. Search for `Remote - SSH`.
4. Install **Remote - SSH** published by Microsoft.

This extension allows VS Code to connect to remote environments over SSH.

## Step 8: Connect Ubuntu to VS Code

Press:

```text
Ctrl + Shift + P
```

Search for and select:

```text
Remote-SSH: Connect to Host...
```

Enter your SSH connection:

```text
rishabhr136@172.28.165.22
```

Replace the example IP address with your Ubuntu IP.

If prompted, enter your Ubuntu password and select the appropriate Linux platform if VS Code asks.

Once connected, VS Code will open a remote session for your Ubuntu environment. You can open Linux folders, edit files, and use the integrated terminal inside Ubuntu.

## Troubleshooting

### Error: Connection Refused

**Example error:**

```text
ssh: connect to host localhost port 22: Connection refused
```

**Possible causes:**

- Ubuntu is not running.
- The OpenSSH server is not installed.
- The SSH service is not running.
- The connection is using the wrong host or port.

**Solution 1: Start Ubuntu**

Run this command in PowerShell:

```powershell
wsl -d Ubuntu
```

**Solution 2: Start the SSH service**

Inside Ubuntu, run:

```bash
sudo service ssh start
sudo service ssh status
```

**Solution 3: Verify the Ubuntu IP address**

Inside Ubuntu:

```bash
hostname -I
```

Use the correct IP address when connecting from PowerShell.

**Solution 4: Test the SSH connection again**

```powershell
ssh rishabhr136@172.28.165.22
```

Replace the example IP with your actual Ubuntu IP.

If you specifically use `localhost`, ensure SSH is configured to listen on the corresponding local port. Connecting to the Ubuntu IP and connecting to `localhost` are not necessarily equivalent.

## Understanding Your Linux Username and Hostname

Suppose your Ubuntu terminal displays:

```bash
rishabhr136@Rishabh:/mnt/c/Users/risha$
```

This follows the format:

```text
username@hostname
```

In this example:

| Component | Value | Meaning |
|---|---|---|
| Username | `rishabhr136` | Your Linux user account |
| Hostname | `Rishabh` | Your Ubuntu system's hostname |
| Current directory | `/mnt/c/Users/risha` | Your current working directory |

### Verify Your Linux Username

Run inside Ubuntu:

```bash
whoami
```

**Expected output:**

```text
rishabhr136
```

### Verify Your Hostname

Run:

```bash
hostname
```

**Expected output:**

```text
Rishabh
```

## SSH Connection Command

When connecting to Ubuntu from PowerShell or configuring a VS Code SSH host, use this format:

```powershell
ssh username@ubuntu-ip
```

For your example:

```powershell
ssh rishabhr136@172.28.165.22
```

Replace the IP address with the current address of your Ubuntu instance.

**Important:** The Ubuntu IP address can change when the WSL environment restarts. If the connection stops working, check `hostname -I` again.

---

## Quick Reference: Commands

| Task | Command | Where to run |
|---|---|---|
| Check installed WSL distributions | `wsl -l -v` | PowerShell |
| Start Ubuntu | `wsl -d Ubuntu` | PowerShell |
| Install SSH server | `sudo apt install openssh-server -y` | Ubuntu |
| Start SSH service | `sudo service ssh start` | Ubuntu |
| Check SSH status | `sudo service ssh status` | Ubuntu |
| Find Ubuntu IP | `hostname -I` | Ubuntu |
| Check Linux username | `whoami` | Ubuntu |
| Check hostname | `hostname` | Ubuntu |
| Test SSH connection | `ssh rishabhr136@172.28.165.22` | PowerShell |

**Workflow:** Start Ubuntu → Install OpenSSH Server → Start SSH Service → Find Ubuntu IP → Test SSH → Install Remote - SSH → Connect through VS Code.

To copy EVERYTHING, including hidden files
A safer command is:
cp -a /hig/users/mayank.kumar/. /hig/users/rishabh.raj/

The -a preserves permissions, timestamps, symbolic links, etc.
If you want to copy only the Python/DevOps setup
For example, if the other user's folder contains:
.py312

you can do:
cp -a /hig/users/mayank.kumar/.py312 /hig/users/rishabh.raj/

Right — if your terminal is already inside /hig/users/rishabh.raj, you don't need to specify the full destination path.

For example, suppose you want to copy everything from:
/hig/users/mayank.kumar

into your current rishabh.raj folder.
Copy everything, including hidden files
First confirm where you are:
pwd

It should show something like:
/hig/users/rishabh.raj

Then run:
cp -a /hig/users/mayank.kumar/. .

Here:
- /hig/users/mayank.kumar/. = everything inside Mayank's folder
- . = your current rishabh.raj folder
- -a = preserves directories, hidden files, links, permissions, etc.

There is a space, but it is important where the spaces are.

The correct command is:
cp -a /hig/users/mayank.kumar/. /hig/users/rishabh.raj/