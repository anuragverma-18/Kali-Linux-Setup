Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 1
Cybersecurity Lab Environment
Setup
Building an isolated virtual lab for penetration testing and ethical hacking practice
Program Cybersecurity at Networkwalks
Week / Project Week 01 - Cybersecurity & Pentesting Lab Setup (WK1-PM1)
Author Anurag Verma
Date 30 October 2026
Project Overview
This project sets up a virtual cybersecurity and penetration-testing laboratory using VirtualBox and Kali
Linux. The purpose of the lab is to create a controlled environment where security tools, network
scanning, reconnaissance and vulnerability assessment can be practised safely and repeatedly.
The lab runs on a private virtual network so that additional machines can be added later and used as
targets for authorized security testing.
Objectives
• Install and configure VirtualBox.
• Install/import Kali Linux as a virtual machine.
• Create a private NAT Network (10.0.0.0/24) for the lab.
• Configure network connectivity for Kali Linux.
• Assign a consistent IP address (10.0.0.2/24) to the Kali VM.
• Enable clipboard, drag and drop, and a shared Downloads folder.
• Verify network connectivity and DNS resolution.
• Take a clean VM snapshot for recovery.
• Document the complete setup process and prepare for future projects.
Purpose of the Lab
The lab provides an isolated environment for cybersecurity learning and authorized security testing, for
example:
• Network reconnaissance and port scanning
• Vulnerability assessment
• Packet analysis
• Web security testing
• Exploitation practice
• Security-tool experimentation
Important: This laboratory must only be used on systems that you own or have explicit permission to test.
Do not use the lab or its tools to attack unauthorized systems.
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 2
Lab Architecture
Kali Linux (attacker, 10.0.0.2) sits on the VirtualBox NAT Network NatNetwork (10.0.0.0/24) together
with the gateway 10.0.0.1, which also provides Internet access through the host. Future target VMs join
the same network.
 +-----------------------------------------------------------+
 | Host machine (Windows) - Oracle VirtualBox 7.2.22 |
 | |
 | NAT Network: NatNetwork 10.0.0.0/24 |
 | +-------------+ +--------------+ +--------------+ |
 | | Gateway/NAT | | Kali Linux | | Future VMs | |
 | | 10.0.0.1 | | 10.0.0.2/24 | | 10.0.0.3-99 | |
 | +------+------+ +--------------+ +--------------+ |
 +----------|------------------------------------------------+
 | NAT
 [ Internet ]
Lab Configuration
Component Configuration
Host OS Windows
Hypervisor Oracle VirtualBox 7.2.22
Security OS Kali Linux (official VirtualBox image)
Kali RAM 4096 MB
Video VMSVGA, 126 MB video memory, 3D acceleration off
Virtual Network NAT Network (NatNetwork)
Network Address 10.0.0.0/24 (DHCP enabled, IPv6 disabled)
Kali IP Address 10.0.0.2/24
Default Gateway 10.0.0.1
DNS Server 8.8.8.8
Promiscuous Mode Allow All
Shared Folder Host Downloads folder
Future VM Range 10.0.0.3 - 10.0.0.99
Lab Setup Procedure
Step 1. Install 7-Zip
7-Zip was installed to extract the Kali Linux virtual-machine package, which is distributed as a .7z
archive.
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 3
Step 2. Install VirtualBox
Oracle VirtualBox 7.2.22 (amd64) was installed as the hypervisor. VirtualBox is a Type-2 hypervisor
that runs on top of the host operating system and lets several virtual machines share one PC.
Figure 1: Oracle VirtualBox 7.2.22 installer
Step 3. Create the NAT Network
A dedicated NAT Network was created from File > Tools > Network in VirtualBox.
Setting Value
Network Name NatNetwork
IPv4 Prefix 10.0.0.0/24
DHCP Enabled
IPv6 Disabled
Figure 2: NAT Networks tab in the VirtualBox Network Manager
A NAT Network was chosen because VMs on the same NAT Network can communicate with each
other and still have outbound Internet access. This allows future attacker and target VMs to talk inside
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 4
the lab.
For comparison, the default Host-only network (192.168.56.1/24) connects VMs only to each other and
the host, with no Internet access, so it is not used for this lab.
Figure 3: Host-only Networks tab (default adapter 192.168.56.1/24, not used)
Note: VirtualBox creates NatNetwork with the default prefix 10.0.2.0/24. The IPv4 Prefix must be edited to
10.0.0.0/24 and applied so that it matches the Kali static IP.
Step 4. Import and Configure Kali Linux
The Kali Linux VM was downloaded from the official Kali website and imported into VirtualBox. Its
network adapter was initially set to Bridged Adapter, which joins the VM to the real LAN through the
host's Wi-Fi card:
Figure 4: Initial network setting - Bridged Adapter (Promiscuous Mode: Deny)
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 5
It was then changed to the lab network:
Adapter 1
Attached to: NAT Network
Name: NatNetwork
Adapter Type: Intel PRO/1000 MT Desktop (82540EM)
Promiscuous Mode: Allow All
Figure 5: Adapter 1 attached to NAT Network. The Name field must be set to NatNetwork before clicking OK.
Promiscuous Mode: Allow All lets the VM capture traffic addressed to other machines, which
packet-analysis tools such as Wireshark need.
System settings
The Motherboard tab shows 4096 MB base memory, chipset PIIX3, no TPM, EFI disabled, I/O APIC
and UTC hardware clock enabled. Moving Hard Disk first in the boot order is the usual tidy-up. Settings
appear greyed out because the VM was running.
Figure 6: System > Motherboard
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 6
Figure 7: System > Motherboard (cropped view of the same tab)
Nested Paging (hardware virtualization) is enabled on the Acceleration tab. It lets the CPU handle
guest memory translation and noticeably speeds up the VM. It depends on VT-x / AMD-V being
enabled in the BIOS.
Figure 8: System > Acceleration
In Display, the VMSVGA graphics controller is used with 126 MB video memory and 3D acceleration
off, which is the stable choice for Kali.
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 7
Figure 9: Display settings
Shared folder, clipboard and drag and drop
Under Shared Folders, the host Downloads folder is added with Auto-mount and Make Permanent
ticked. Inside Kali it appears under /media/sf_Downloads; add the user to the vboxsf group (sudo
usermod -aG vboxsf $USER) and reboot. Clipboard and drag and drop are set to Bidirectional in
General > Advanced.
Figure 10: Shared Folders and User Interface settings
Step 5. Configure the Kali Linux Network
Kali was given a fixed IPv4 address so that it is easy to document and reference in later exercises.
IP Address: 10.0.0.2
Subnet Mask: 255.255.255.0 (/24)
Gateway: 10.0.0.1
DNS: 8.8.8.8
This can be done in the GUI (network icon > Edit Connections > IPv4 Settings > Manual) or from the
terminal:
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 8
sudo nmcli connection modify "Wired connection 1" \
 ipv4.method manual ipv4.addresses 10.0.0.2/24 \
 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
Step 6. Create a Clean VM Snapshot
After the initial configuration worked, a VirtualBox snapshot was taken (example name: Clean Kali -
Network Setup). It is the clean baseline of the lab: if a later exercise damages the VM, it can be
restored to this state.
Lab Verification
Test Command Expected Result
Check IP address ip a 10.0.0.2/24 shown on eth0
Test gateway ping -c 3 10.0.0.1 Successful replies
Test Internet ping -c 3 8.8.8.8 Successful replies
Test DNS nslookup networkwalks.com Domain resolves
Verify Nmap nmap --version Nmap version displayed
Verify snapshot Restore snapshot, run ip a Baseline configuration restored
Problems Encountered & Solutions
Problem 1. No Internet After Network Start-up
On VirtualBox 7 with recent Kali versions the connection can stall at start-up because NetworkManager
performs Duplicate Address Detection (DAD) on the IPv4 address. The fix is to disable the DAD timeout
and restart the connection:
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
Figure 11: nmcli fix on Kali. Earlier attempts failed with 'unknown connection' because the spaces in the name were not
escaped; the final command reported 'Connection successfully activated'.
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 9
Important: Connection names differ between systems. Check yours with nmcli connection show
before running the commands, and quote the name or escape spaces as Wired\ connection\ 1.
Problem 2. Settings Dialog Shows 'Invalid settings detected'
After switching Adapter 1 to NAT Network, the Name field showed Not selected and the OK button
stayed disabled. Selecting NatNetwork in the Name list (the network must exist under Tools > Network)
resolves it.
If the VM fails to start at all with a VT-x / AMD-V error, hardware virtualization is disabled in the
BIOS/UEFI: restart, enable Intel VT-x (or AMD-V / SVM), save, and boot again.
What I Learned
1. NAT vs NAT Network
Plain NAT isolates each VM from the others. A NAT Network puts VMs on one shared private subnet
where they can talk to each other and still reach the Internet, which is what a multi-machine lab needs.
2. Virtual Machine Networking
Bridged, Host-only and NAT Network modes connect a VM differently, and the choice decides who the
VM can reach and who can reach it.
3. Static IP Configuration
I learned to configure and verify the IPv4 address, subnet mask, gateway and DNS in Kali Linux, and to
use ping tests to tell routing problems from DNS problems.
4. VM Snapshots
A clean snapshot taken before risky or experimental work gives a known-good recovery point.
5. Documentation
Recording commands, settings, screenshots, problems and fixes is a core part of a professional
cybersecurity project.
Security & Ethical Use
This laboratory is intended strictly for educational purposes and for systems that you own or are
authorized to test.
Tools & Resources
• 7-Zip: https://7-zip.org/download.html
• VirtualBox: https://virtualbox.org/wiki/Downloads
• Kali Linux: https://kali.org/get-kali
Author
Anurag Verma
LinkedIn: https://www.linkedin.com/in/anurag-verma-5469963a0/
GitHub: https://github.com/anuragverma-18
Cybersecurity & Pentesting Lab Setup | Week 01 | Anurag Verma Page 10
Program: Cybersecurity at Networkwalks | Week: 01 | Project: Cybersecurity & Pentesting Lab Setup
