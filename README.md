# tethered_iphone_raspberry_pi
How to setup Internet sharing with an iPhone and raspberry pi

1. Install Raspian on raspberry pi
2. Update and upgrade system: **sudo apt update & sudo apt upgrade**
3. Install usb handler to connect iPhone: **sudo apt install usbmuxd**
4. Connect iPhone and enable iPhone hotspot, select “trust” when prompted, and check that you get a new interface eth1 with an IP: **sudo ifconfig -a**
5. Create bridge: **sudo nano /etc/systemd/network/bridge-br0.netdev**

[NetDev]
Name=br0
Kind=bridge

6. Make eth0 member of the bridge: **sudo nano /etc/systemd/network/br0-member-eth0.network**

[Match]
Name=eth0

[Network]
Bridge=br0

6. Make eth1 member of the bridge: **sudo nano /etc/systemd/network/br0-member-eth1.network**

[Match]
Name=eth1

[Network]
Bridge=br0

7. Enable the systemd-networkd service to create and populate the bridge when your Raspberry Pi boots:

**sudo systemctl enable systemd-networkd**

8. Set static fallback IP, make sure DHCP is not handed out on eth0 and eth1, and make br0 handle DHCP-requests:

**sudo cp /etc/dhcpcd.conf /etc/dhcpcd.conf.back**

**sudo nano /etc/dhcpcd.conf**

9. Add to the top:

# Disable DHCP on interfaces that are bridged:
denyinterfaces eth0 eth1

10. Comment out following:

# It is possible to fall back to a static IP if DHCP fails:
# define static profile
profile static_eth0
static ip_address=192.168.0.65/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1

# fallback to static profile on eth0
interface eth0
fallback static_eth0

11. Add as last lines:

# Add bridged interface:
interface br0

12. Reboot: **sudo reboot now**

13: Connect iPhone via USB and connect Ethernet to laptop and check you get an IP on your laptop from the iPhone (typically 172-range): **sudo ifconfig -a**
