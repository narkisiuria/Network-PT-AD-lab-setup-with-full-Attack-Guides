# pfSense-GATEWAY Setup

Role: acts as the lab's router/gateway, separating the internal lab network from the host/internet, and (optionally) handling DHCP for the lab subnet.

## 1. Create the VM
- New VM in VirtualBox, type: BSD, name `pfSense-GATEWAY`
- Attach the pfSense install ISO
- Give it **2 network adapters**:
  - Adapter 1 (WAN): NAT or Bridged — for internet access out
  - Adapter 2 (LAN): Internal Network or Host-only — this becomes the lab subnet (192.168.56.0/24)

## 2. Install pfSense
- Boot the ISO, run through the installer with defaults
- On first boot, assign interfaces: tell it which NIC is WAN and which is LAN
- Set the LAN interface IP to `192.168.56.1/24` (this becomes the gateway address for every other lab VM)

## 3. Fix DHCP conflict (real issue hit in this lab)
VirtualBox's own built-in DHCP server on the Internal/Host-only network can conflict with pfSense trying to hand out DHCP on the same subnet.
- In VirtualBox: Host Network Manager → find the Host-only/Internal adapter used for LAN → **disable its DHCP server**
- Let pfSense be the only DHCP server on 192.168.56.0/24 (enable DHCP under pfSense: Services → DHCP Server → LAN)

## 4. Confirm it works
- Boot a lab VM (e.g. Kali) on the same internal network
- Check it gets an IP in the 192.168.56.x range and can reach the internet through pfSense
- Access the pfSense web UI from a lab VM: `https://192.168.56.1`

## Result in this lab
- pfSense LAN: `192.168.56.1`
- All other lab VMs (DC, Win10 client, Kali) sit on `192.168.56.0/24`, routed through pfSense