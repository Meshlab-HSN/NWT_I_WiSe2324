# Practical Lectures

This folder contains the steps done in the Practical Lectures of the seminar based on the computer science (INF) group. They should help if you want to retype some of the commands or if you missed a lecture.<br>
You can execute the commands on Linux-based operating systems like Debian, Ubuntu, Fedora, Linux Mint, etc., and most of them also on macOS. If you are using Windows, you can use the Windows Subsystem for Linux (WSL) or install a virtual Linux-based OS via VirtualBox. You can install WSL by using the following command inside the Terminal, Command Prompt, or PowerShell:
<pre>wsl --install</pre>

Since WSL relies on virtualization, you need to make sure that some required features for this are enabled. If this is not the case and you try to start WSL, it will ask you to enable these features:
- CPU virtualization support: needs to be enabled in the BIOS/UEFI menu, the actual name of the option depends on your device
- Virtual Machine Platform feature in Windows: can be activated in the Control panel applet `Turn Windows features on or off`

<br>

Lecture 1 (07.11.2023):
 * Generating an SSH private/public key pair
 * Creating an SSH alias
 * Configuring an IP address on the VM
 * Adjusting the firewall settings
 * Sending a ping to another VM
<br><br>

Lecture 2 (21.11.2023):
 * Wireguard Server Setup
 * Wireguard Client Setup
 * Sending a picture using netcat
<br><br>

Lecture 3 (05.12.2023):
* Capturing data packets using tcpdump
* Analyzing network packets with Wireshark
<br><br>

Lecture 4 (19.12.2023):
* Changing the TCP congestion algorithm
* Network performance tests using iperf3
* Socket Programming in Python

Lecture 5 (16.01.2024):
* refreshed knowledge about IPv6
* used multicast ping in IPv6 link-local scope
* deployed IPv6 in our Wireguard tunnel
* configured a global-scope IPv6 address and Router Advertisments with that prefix
<br><br>
