# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**

![VirtualBox](https://img.shields.io/badge/VirtualBox-7.2.22-blue)
![Kali Linux](https://img.shields.io/badge/Kali-Linux-557C94)
![Network](https://img.shields.io/badge/Network-10.0.0.0%2F24-red)
![Status](https://img.shields.io/badge/Purpose-Education%20Only-green)

📄 **Full report (PDF):** [Cybersecurity_Lab_Project_Report.pdf](Cybersecurity_Lab_Project_Report.pdf)

---

## 📌 Project Overview

This project sets up a virtual cybersecurity and penetration-testing laboratory using **VirtualBox** and **Kali Linux**.

The lab provides a controlled environment where security tools, network scanning, reconnaissance and vulnerability assessment can be practised safely and repeatedly.

It runs on a private virtual network, so additional machines can be added later and used as targets for **authorized** security testing.

## 🎯 Objectives

- [x] Install and configure VirtualBox
- [x] Install/import Kali Linux as a virtual machine
- [x] Create a private NAT Network (`10.0.0.0/24`) for the lab
- [x] Configure network connectivity for Kali Linux
- [x] Assign a consistent IP address (`10.0.0.2/24`) to the Kali VM
- [x] Enable clipboard, drag and drop, and a shared Downloads folder
- [x] Verify network connectivity and DNS resolution
- [x] Take a clean VM snapshot for recovery
- [x] Document the complete setup process
- [x] Prepare the environment for future cybersecurity projects

## 🛡️ Purpose of the Lab

The lab is an isolated environment for cybersecurity learning and authorized testing, for example:

- Network reconnaissance
- Port scanning
- Vulnerability assessment
- Packet analysis
- Web security testing
- Exploitation practice
- Security-tool experimentation

> ⚠️ **Important:** This lab must only be used on systems that you own or have explicit permission to test. Do not use the lab or its tools to attack unauthorized systems.

## 🏗️ Lab Architecture

```text
+-----------------------------------------------------------+
|  Host machine (Windows)  -  Oracle VirtualBox 7.2.22      |
|                                                           |
|   NAT Network: NatNetwork   10.0.0.0/24                   |
|   +-------------+   +--------------+   +--------------+   |
|   | Gateway/NAT |   | Kali Linux   |   | Future VMs   |   |
|   | 10.0.0.1    |   | 10.0.0.2/24  |   | 10.0.0.3-99  |   |
|   +------+------+   +--------------+   +--------------+   |
+----------|------------------------------------------------+
           |  NAT
       [ Internet ]
```

Additional target machines (Windows 11/10/7, Android, CTF VMs) can be added to the same virtual network in future projects.

## ⚙️ Lab Configuration

| Component | Configuration |
|---|---|
| 🖥️ Host OS | Windows |
| 🧰 Hypervisor | Oracle VirtualBox 7.2.22 |
| 🐉 Security OS | Kali Linux (official VirtualBox image) |
| 🧠 Kali RAM | 4096 MB |
| 🖼️ Video | VMSVGA, 126 MB video memory, 3D acceleration off |
| 🌐 Virtual Network | NAT Network (`NatNetwork`) |
| 📡 Network Address | `10.0.0.0/24` (DHCP enabled, IPv6 disabled) |
| 🐧 Kali IP Address | `10.0.0.2/24` |
| 🚪 Default Gateway | `10.0.0.1` |
| 🌍 DNS Server | `8.8.8.8` |
| 🔊 Promiscuous Mode | Allow All |
| 📁 Shared Folder | Host Downloads folder |
| 🔮 Future VM Range | `10.0.0.3 – 10.0.0.99` |

## 🪜 Lab Setup Procedure

### Step 1. Install 7-Zip

7-Zip is used to extract the Kali Linux virtual-machine package, which is distributed as a `.7z` archive.

### Step 2. Install VirtualBox

Oracle VirtualBox 7.2.22 (amd64) was installed as the hypervisor. VirtualBox is a Type-2 hypervisor: it runs on top of the host OS and lets several virtual machines share one PC.

![VirtualBox installer](screenshots/01-virtualbox-installer.png)

### Step 3. Create the NAT Network

Open **File → Tools → Network** in VirtualBox and create a NAT Network:

| Setting | Value |
|---|---|
| Network Name | `NatNetwork` |
| IPv4 Prefix | `10.0.0.0/24` |
| DHCP | Enabled |
| IPv6 | Disabled |

![NAT Networks](screenshots/02-nat-networks.png)

A NAT Network was chosen because VMs on the same NAT Network can talk to each other **and** still reach the Internet. This lets future attacker and target VMs communicate inside the lab.

> 📝 VirtualBox creates the network with the default prefix `10.0.2.0/24`. Edit the **IPv4 Prefix** to `10.0.0.0/24` and click **Apply** so it matches the Kali static IP.

For comparison, the default **Host-only** network (`192.168.56.1/24`) connects VMs only to each other and the host, with no Internet access, so it is not used here.

![Host-only networks](screenshots/03-host-only-networks.png)

### Step 4. Import and Configure Kali Linux

The Kali Linux VM was downloaded from the official Kali website and imported into VirtualBox. Its adapter was initially **Bridged**, which joins the VM to the real LAN:

![Bridged adapter](screenshots/04-kali-network-bridged.png)

It was changed to the lab network:

```text
Adapter 1
Attached to:      NAT Network
Name:             NatNetwork
Adapter Type:     Intel PRO/1000 MT Desktop (82540EM)
Promiscuous Mode: Allow All
```

![NAT Network adapter](screenshots/05-kali-network-nat-network.png)

`Promiscuous Mode: Allow All` lets the VM capture traffic addressed to other machines, which tools such as Wireshark need.

#### System, display and acceleration

- **Motherboard:** 4096 MB base memory, chipset PIIX3, no TPM, EFI off, I/O APIC and UTC hardware clock enabled.
- **Acceleration:** Nested Paging enabled (requires VT-x / AMD-V in the BIOS).
- **Display:** VMSVGA controller, 126 MB video memory, 3D acceleration off.

Settings appear greyed out in the screenshots because the VM was running.

| | |
|---|---|
| ![Motherboard](screenshots/06-system-motherboard.png) | ![Motherboard cropped](screenshots/07-system-motherboard-cropped.png) |
| ![Acceleration](screenshots/08-system-acceleration.png) | ![Display](screenshots/09-display.png) |

#### Shared folder, clipboard and drag and drop

- Add the host **Downloads** folder under **Shared Folders**, with *Auto-mount* and *Make Permanent* ticked.
- Set **Shared Clipboard** and **Drag'n'Drop** to *Bidirectional* in **General → Advanced**.
- In Kali the share appears at `/media/sf_Downloads`. Add your user to the `vboxsf` group and reboot:

```bash
sudo usermod -aG vboxsf $USER
```

![Shared folders](screenshots/10-shared-folders.png)

### Step 5. Configure the Kali Linux Network

A fixed IPv4 address makes the lab easier to document and reference.

```text
IP Address:  10.0.0.2
Subnet Mask: 255.255.255.0  (/24)
Gateway:     10.0.0.1
DNS:         8.8.8.8
```

GUI: network icon → **Edit Connections** → **IPv4 Settings** → Method **Manual**. Or from the terminal:

```bash
sudo nmcli connection modify "Wired connection 1" \
    ipv4.method manual ipv4.addresses 10.0.0.2/24 \
    ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

### Step 6. Create a Clean VM Snapshot

After the configuration works, take a VirtualBox snapshot, for example:

```text
Clean Kali - Network Setup
```

It is the clean baseline of the lab. If a later exercise damages the VM, restore this snapshot.

## 🔎 Lab Verification

| ✅ Test | 🧾 Command | 🎯 Expected Result |
|---|---|---|
| Check IP address | `ip a` | `10.0.0.2/24` shown on eth0 |
| Test gateway | `ping -c 3 10.0.0.1` | Successful replies |
| Test Internet | `ping -c 3 8.8.8.8` | Successful replies |
| Test DNS | `nslookup networkwalks.com` | Domain resolves |
| Verify Nmap | `nmap --version` | Nmap version displayed |
| Verify snapshot | Restore snapshot, run `ip a` | Baseline configuration restored |

## 🐞 Problems Encountered & Solutions

### Problem 1. No Internet after network start-up

On VirtualBox 7 with recent Kali versions, the connection can stall at start-up while NetworkManager performs Duplicate Address Detection (DAD). Fix:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

![nmcli fix](screenshots/11-nmcli-fix.jpeg)

My first attempts failed with `unknown connection` because the spaces in the name were not escaped. The final command reported *Connection successfully activated*.

> ⚠️ Connection names differ between systems. Check yours with `nmcli connection show`, and quote the name or escape spaces as `Wired\ connection\ 1`.

### Problem 2. "Invalid settings detected" in VM settings

After switching Adapter 1 to NAT Network, the **Name** field showed *Not selected* and **OK** stayed disabled. Selecting `NatNetwork` in the Name list (the network must exist under *Tools → Network*) fixes it.

> If the VM fails to start with a VT-x / AMD-V error, hardware virtualization is disabled in BIOS/UEFI: restart, enable Intel VT-x (or AMD-V / SVM), save and boot again.

## 💡 What I Learned

1. **NAT vs NAT Network:** plain NAT isolates each VM; a NAT Network puts VMs on one shared private subnet with Internet access, which a multi-machine lab needs.
2. **VM networking:** Bridged, Host-only and NAT Network modes decide who a VM can reach and who can reach it.
3. **Static IP configuration:** configuring and verifying IP, mask, gateway and DNS in Kali, and using ping tests to separate routing problems from DNS problems.
4. **VM snapshots:** a clean snapshot before risky work gives a known-good recovery point.
5. **Documentation:** recording commands, settings, screenshots, problems and fixes is part of professional security work.

## 🔐 Security & Ethical Use

This laboratory is intended strictly for educational purposes and for systems you own or are authorized to test.

## 🔗 Tools & Resources

- 7-Zip: <https://7-zip.org/download.html>
- VirtualBox: <https://virtualbox.org/wiki/Downloads>
- Kali Linux: <https://kali.org/get-kali>

## 📁 Repository Structure

```text
.
├── README.md
├── Cybersecurity_Lab_Project_Report.pdf
└── screenshots/
    ├── 01-virtualbox-installer.png
    ├── 02-nat-networks.png
    ├── 03-host-only-networks.png
    ├── 04-kali-network-bridged.png
    ├── 05-kali-network-nat-network.png
    ├── 06-system-motherboard.png
    ├── 07-system-motherboard-cropped.png
    ├── 08-system-acceleration.png
    ├── 09-display.png
    ├── 10-shared-folders.png
    └── 11-nmcli-fix.jpeg
```

## 👤 Author

**Anurag Verma**

- LinkedIn: <https://www.linkedin.com/in/anurag-verma-5469963a0/>
- GitHub: <https://github.com/anuragverma-18>

## 📌 Project Information

**Program:** Cybersecurity at Networkwalks | **Week:** 01 | **Project:** Cybersecurity & Pentesting Lab Setup (WK1-PM1) | **Date:** 30 October 2026
