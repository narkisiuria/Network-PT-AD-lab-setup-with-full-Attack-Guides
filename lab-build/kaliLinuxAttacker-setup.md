how i got a kali Linux VM in to my home Orcale VB lab?
 
1: Download kali linux. Just search 
```bash 
kali linux vm download
```
2: unzip that file. Right-click it → Extract All (or Extract Here).
3: just drag the .vbox file to your currunt machines menu or open the file and you would see your new kali linux machine 
sitting at the machines menu.

after creating the machine we have to also connect it to our isolated VB host-only network lab: 

1: Right click on the kali machine → Settings → Network.
2: Change "Attached to" from NAT to Host-only Adapter. Then in the Name field that appears, pick your "VirtualBox Host-Only Ethernet Adapter".
3: click OK.

**for the next step you need to have at least 2 vm machines with at least one connected to the host-only network**
now after we finaly got the machine ready we have to check if it sees the other machines we have on the host-only VB network: 

1: boot 2 or 3 VMs — start kali first, then other machines that you are sure that are already connected to this network.
2: in the kali terminal Type:
```bash
 ifconfig
 ```
3: Find the IP that is sitting on eth0.
4: VERY IMPORTENT: in order to check if the DHCP server is working fine you need to look at the subnet and see if it is the right one that your DHCP server is sitting at. 
which means that you need to look at the subnet range lets say it's: .56.xk, if your DHCP server is also in this subnet range lets say: 56.yz than DHCP worked. 
other than that your DHCP server is not working! *in order to fix the DHCP server or to find its IP and where it sits at* you have to:

1: Go back to the Orcal VB first screen → click on File → Tools → hover it → look for Network Manager in that sub-list.
2: there you will see the DHCP server and more details about it.

if you dont see a DHCP server in that list than you do not have a DHCP server and need to set it up from scratch. To do that serach "How to set up a DHCP server in Orcal VB".

if your kali machine cant reach the internet outside of the network than:

1: Shut down Kali. Go to Settings → Network → Adapter 1. (IMPORTENT: you need to have a least 1 adapter beeing on host-only...)
2: Change Attached to from whatever it's on now to NAT.
3: TO VALIDATE: type this in the kali terminal:
```bash
ping 8.8.8.8
```

search for a response that is looking something like this: 

If everything went fine you should have a new kali attacker machine in your VB host-only network ready to be ran!


