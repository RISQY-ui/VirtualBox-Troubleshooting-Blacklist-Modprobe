# VirtualBox Troubleshooting: VMX Root Mode Error Fix

# Technical Documentation | OS: Linux Mint | Date: June 19, 2026

---

📋 Table of Contents

· Introduction
· Part 1: Cause & Initial Solution
· Part 2: Blacklist & Modprobe (Advanced Solution)
· Quick Command Summary
· Important Lessons

---

Introduction

Error Description

When trying to run a virtual machine (VM) in VirtualBox, the following error message appears:

```
VirtualBox can't operate in VMX root mode.
Please disable the KVM kernel extension...
(VERR_VMX_IN_VMX_ROOT_MODE)
```

VM Used

VM Name Target OS Initial Status
Kali-Linux-Lab Kali Linux Failed to Start

Objective

Resolve the error so the VM can run normally, both for immediate use and long-term.

---

Part 1: Cause & Initial Solution

Main Cause

Component Function Issue
KVM (Kernel-based Virtual Machine) Linux's built-in virtual machine Locks processor virtualization features
VirtualBox Oracle's virtualization application Requires the same virtualization features
VMX / Intel VT-x Processor virtualization feature Cannot be used simultaneously by KVM and VirtualBox

Note: This issue often appears after using Docker or other tools that automatically activate the KVM module.

---

Quick Solution (Temporary)

Step 1: Open Terminal

Step 2: Run the appropriate command for your processor type

For Intel Processors:

```bash
sudo rmmod kvm_intel
sudo rmmod kvm
```

For AMD Processors:

```bash
sudo rmmod kvm_amd
sudo rmmod kvm
```

Step 3: Enter your Linux password when prompted

Step 4: Click the Start button in VirtualBox

Status Result
✅ VM runs normally

⚠️ Important Note: This solution is temporary. After restarting the laptop, KVM will become active again and the error will reappear.

---

Part 2: Blacklist & Modprobe (Advanced Solution)

Issue Found

The blacklist.conf configuration file was already edited, but the error persisted.

Cause

Aspect Description
blacklist.conf file Only read by the kernel during boot/startup
KVM module Already active in RAM memory and still locking virtualization
Root Problem Configuration changes require a restart, but KVM in memory needs manual removal

---

Solution 1: Remove KVM from Memory (Without Restart)

Command:

For Intel Processors:

```bash
sudo modprobe -r kvm_intel
sudo modprobe -r kvm
```

For AMD Processors:

```bash
sudo modprobe -r kvm_amd
sudo modprobe -r kvm
```

Explanation:

Command Function
sudo modprobe -r Forcefully removes the kernel module from memory

Result:

Status Description
✅ KVM is immediately removed from memory, VirtualBox can run without restart

---

Solution 2: Restart Laptop (Final)

To verify if the blacklist configuration was successful, restart:

```bash
reboot
```

Result:

Status Description
✅ After restart, KVM will not be active permanently

---

Blacklist Configuration (Permanent)

Step 1: Open configuration file

```bash
sudo nano /etc/modprobe.d/blacklist.conf
```

Step 2: Add the following lines at the bottom

For Intel Processors:

```
blacklist kvm_intel
blacklist kvm
```

For AMD Processors:

```
blacklist kvm_amd
blacklist kvm
```

Step 3: Save and exit

Key Function
CTRL + O Save file
Enter Confirm file name
CTRL + X Exit nano editor

Step 4: Restart laptop (optional, to confirm)

```bash
reboot
```

---

Solution Comparison

Solution Advantages Disadvantages When to Use
rmmod / modprobe -r Fast, no restart needed Must be repeated after restart When you need VM running now
Blacklist One-time setting, permanent Requires restart for full effect For long-term solution

---

Benefits After Running Both Solutions

Now Future
✅ KVM removed from memory ✅ KVM will not activate after restart
✅ VirtualBox runs immediately ✅ VirtualBox ready to use anytime
✅ VM ready to use ✅ No more VMX errors

---

Quick Command Summary

Temporary Mode (Without Restart)

Situation Command
Intel - remove KVM sudo modprobe -r kvm_intel && sudo modprobe -r kvm
AMD - remove KVM sudo modprobe -r kvm_amd && sudo modprobe -r kvm

Permanent Mode (Blacklist)

Situation Command
Intel - blacklist echo "blacklist kvm_intel"
AMD - blacklist echo "blacklist kvm_amd"
Restart laptop reboot

---

Important Lessons

Lesson Description
1. KVM vs VirtualBox Both cannot use processor virtualization features simultaneously
2. rmmod Removes module from kernel directly (temporary)
3. modprobe -r Modern way to remove modules, equivalent to rmmod
4. Blacklist blacklist.conf file only takes effect after restart
5. Restart Required for boot configuration changes to be applied

---

Final Status

Component Status
KVM in memory ✅ Removed (modprobe -r)
blacklist.conf file ✅ Edited (blacklist kvm_intel & kvm)
VirtualBox ✅ Running normally
VM ✅ Ready to use
Laptop ✅ Ready to restart anytime

---
