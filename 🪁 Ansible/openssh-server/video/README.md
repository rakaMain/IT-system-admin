<p align="left">
  <a href="https://youtu.be/PX7b0KPDCIo?si=Vmz_ljdW_sFboPkw">
    <img src="https://ytcards.demolab.com/?id=PX7b0KPDCIo&title=Cara+Install+dan+Setting+SSH+Server+di+Linux+Debian+12+%7C+Tutorial+Lengkap+untuk+Pemula&lang=en&background_color=%230d1117&title_color=%23ffffff&stats_color=%23dedede&max_title_lines=1&width=350&border_radius=8" alt="Cara Install dan Setting SSH Server di Linux Debian 12 | Tutorial Lengkap untuk Pemula" />
  </a>
</p>

Dalam video ini kita akan mempelajari konsep dasar SSH (Secure Shell), pengertian OpenSSH Server, cara install dan konfigurasi OpenSSH Server di Debian 12, setup topologi star dengan LAN segment, konfigurasi IP address statis pada server, remote connection menggunakan SSH client, dan testing konektivitas antar server. Video ini cocok untuk system administrator pemula yang ingin memahami dasar-dasar remote administration menggunakan SSH dengan praktik langsung step-by-step dari instalasi hingga testing remote connection.

🌍 Network Segments: LAN Segment SSH:
192.168.1.0/24

💻 Device Configuration: 
SRV1 (SSH Server): 192.168.1.1/24 (Static IP)
SRV2 (SSH Server): 192.168.1.2/24 (Static IP) 
PC (SSH Client): 192.168.1.10/24 (Static IP)

🔐 SSH Configuration:
Protocol: SSH-2
Port: 22 (default)
Authentication: Username/Password
Encryption: AES, 3DES
User Account: raka
Remote Access: Enabled
Terminal Access: Full shell access

📦 Commands:

sudo apt update && sudo apt upgrade -y
sudo apt install openssh-server -y
nano /etc/network/interfaces
ssh raka@192.168.1.1
ssh raka@192.168.1.2


🔧 Setup Requirements:
- Debian 12 (Stable Linux Distribution)
- VMware Workstation (Virtualization Platform)
- Star Topology Network Configuration
- Static IP Address Configuration

🔗 Sumber:
OpenSSH Official Documentation
Debian 12 Network Configuration Guide
VMware Workstation Documentation 
Linux System Administration Guide

📱 Social Media:
Instagram: @rkyla_m
LinkedIn: https://www.linkedin.com/in/raka-kayla-m/

💝 Support Channel: 
Donasi: https://saweria.co/rakaky

🏷️ Tags: #SSH #OpenSSH #RemoteAccess #ServerConfiguration #Debian #LinuxNetworking #SecureShell #RemoteAdministration #ITEducation #NetworkLab #StarTopology #LANSegment #NetworkConfiguration #BelajarSSH #NetworkingTutorial #VMwareWorkstation #SystemAdmin #NetworkingSecurity #SSHServer #NetworkManagement #LinuxServer #RemoteConnection #BelajarLinux #NetworkInfrastructure