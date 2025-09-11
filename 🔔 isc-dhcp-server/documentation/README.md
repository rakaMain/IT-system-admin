<p align="left">
  <a href="https://youtu.be/3IktL03vnOs?si=gq_VEiaHTymgFFzv">
    <img src="https://ytcards.demolab.com/?id=3IktL03vnOs&title=Cara+Install+DHCP+Server+Debian+12+-+Panduan+Lengkap&lang=en&background_color=%230d1117&title_color=%23ffffff&stats_color=%23dedede&max_title_lines=1&width=350&border_radius=8" alt="Cara Install DHCP Server Debian 12 - Panduan Lengkap" />
  </a>
</p>

Dalam video ini kita akan mempelajari konsep dasar DHCP (Dynamic Host Configuration Protocol), pengertian ISC (Internet Systems Consortium), cara install dan konfigurasi ISC-DHCP Server di Debian 12, setup topologi star dengan LAN segment, konfigurasi IP address pada server dan client, testing koneksi DHCP client configuration, dan troubleshooting DHCP server. Video ini cocok untuk system administrator pemula yang ingin memahami dasar-dasar network automation menggunakan DHCP dengan praktik langsung step-by-step dari instalasi hingga testing connectivity.

🌍 Network Segments:
LAN Segment: 192.168.1.0/24

💻 Device Configuration:
SRV01 (DHCP Server): 192.168.1.1/24 (Static IP)
CLIENT (DHCP Client): Auto-assigned dari DHCP pool

🌐 DHCP Pool Configuration:
Subnet: 192.168.1.0
Netmask: 255.255.255.0
IP Range: 192.168.1.2 - 192.168.1.100
Gateway: 192.168.1.1
DNS Servers: 8.8.8.8, 8.8.4.4
Domain Name: raka.com
Broadcast Address: 192.168.1.255
Default Lease Time: 600 seconds
Max Lease Time: 7200 seconds

📦  Commands
sudo apt install isc-dhcp-server
nano /etc/default/isc-dhcp-server
Set: INTERFACESv4="ens33"
nano /etc/dhcp/dhcpd.conf
nano /etc/dhcp/dhcpd.conf
systemctl restart isc-dhcp-server
Set interface sebagai DHCP clien

💝 Support Channel:
Donasi: https://saweria.co/rakaky

📱 Social Media:
Instagram: @rkyla_m  
LinkedIn: https://www.linkedin.com/in/rakakaylam/
GitHub : https://github.com/rakaMain

🏷️ Tags
#DHCP #ISC-DHCP #Networking #ServerConfiguration #Debian #LinuxNetworking #NetworkAutomation #IPAddress #NetworkingBasics #Virtualization #ITEducation #NetworkLab #StarTopology #LANSegment #NetworkConfiguration #BelajarNetworking #NetworkingTutorial #VMwareWorkstation #SystemAdmin #NetworkingPractice #DHCPServer #NetworkManagement #LinuxServer #NetworkInfrastructure #BelajarDHCP

📚 Sumber
ISC-DHCP Official Documentation
Debian 12 Network Configuration Guide
VMware Workstation Documentation
Linux System Administration Guide
