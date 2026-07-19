how i got a metasploitable2 VM in to my home Orcale VB lab?
 
1: Download Metasploitable2. Just search "Metasploitable2 download".
2: unzip that file. Right-click it → Extract All (or Extract Here).
3: open VirtualBox → New → Type this in VM Name: Metasploitable2.
4: change OS dropdown to Linux, and OS Version to Debian (32-bit)
5: click "Specify virtual hard disk" to expand it.
6: Click the radio button "Use an Existing Virtual Hard Disk File."
7: click the little folder icon next to that dropdown, and browse to the Metasploitable folder and pick the .vmdk file - if you are having any truble with that than you have to manualy add the .vmdk file to the dropdown option by clicking add in the options you have there.
8: check if it got created: Metasploitable2 sits right next to your Kali VM now in the machines tab.

after creating the machine we have to also connect it to our isolated VB host-only network lab: 

1: Right click on the Metasploitable2 machine → Settings → Network.
2: Change "Attached to" from NAT to Host-only Adapter. Then in the Name field that appears, pick your "VirtualBox Host-Only Ethernet Adapter".
3: click OK.

**for the next step you need to have at least 2 vm machines with at least one connected to the host-only network**
now after we finaly got the machine ready we have to check if it sees the other machines we have on the host-onky VB network: 

1: boot 2 or 3 VMs — start Metasploitable2 first, then other machines that you are sure that are already connected to this network.
2: in the Metasploitable2 terminal Type ifconfig.
3: Find the IP that is sitting on eth0.
4: VERY IMPORTENT: in order to check if the DHCP server is working fine you need to look at the subnet and see if it is the right one that your DHCP server is sitting at. 
which means that you need to look at the subnet range lets say it's: .56.xk, if your DHCP server is also in this subnet range lets say: 56.yz than DHCP worked. 
other than that your DHCP server is not working! *in order to fix the DHCP server or to find its IP and where it sits at* you have to:

1: Go back to the Orcal VB first screen → click on File → Tools → hover it → look for Network Manager in that sub-list.
2: there you will see the DHCP server and more details about it.

if you dont see a DHCP server in that list than you do not have a DHCP server and need to set it up from scratch. To do that serach "How to set up a DHCP server in Orcal VB".

If everything went fine you should have a new Metasploitable2 machine in your VB host-only network ready to be ran!


