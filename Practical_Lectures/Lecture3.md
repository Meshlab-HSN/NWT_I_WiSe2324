# Lecture 3

In our third lecture, we ...
* Captured some network packets using <code>tcpdump</code>
* Analyzed the captured packets using <code>Wireshark</code>
<br><br>


## Capturing data packets using tcpdump

To capture data packets on a specific network interface (e.g., obtained from <code>ip a</code>) and print them to <code>stdout</code>, we have used the following command. <code>sudo</code> is used here since you probably need root access to capture on your network interface. The parameter **-n** prevents the conversion of host addresses to names.
<pre>
$ sudo tcpdump -i &lt;network interface&gt; -n

# for example
$ sudo tcpdump -i enp7s0 -n
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp7s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
</pre>
<br>

If we generate some network traffic now (e.g., by sending a ping, opening a website, interacting with our VM, ...), we should see the packets of those connections now. The example here shows network traffic generated while pinging google.de (<code>ping google.de</code>):
<pre>
$ sudo tcpdump -i enp7s0 -n
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp7s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
22:46:48.608036 IP 192.168.1.2.49590 > 192.168.1.1.53: 56837+ [1au] A? google.de. (38)
22:46:48.608137 IP 192.168.1.2.40365 > 192.168.1.1.53: 50425+ [1au] AAAA? google.de. (38)
22:46:48.624266 IP 192.168.1.1.53 > 192.168.1.2.49590: 56837 1/0/1 A 172.217.16.131 (54)
22:46:48.630830 IP 192.168.1.1.53 > 192.168.1.2.40365: 50425 1/0/1 AAAA 2a00:1450:4001:808::2003 (66)
22:46:48.631250 IP 192.168.1.2 > 172.217.16.131: ICMP echo request, id 17151, seq 1, length 64
22:46:48.643600 IP 172.217.16.131 > 192.168.1.2: ICMP echo reply, id 17151, seq 1, length 64
22:46:49.632798 IP 192.168.1.2 > 172.217.16.131: ICMP echo request, id 17151, seq 2, length 64
22:46:49.647083 IP 172.217.16.131 > 192.168.1.2: ICMP echo reply, id 17151, seq 2, length 64
22:46:50.634465 IP 192.168.1.2 > 172.217.16.131: ICMP echo request, id 17151, seq 3, length 64
22:46:50.649067 IP 172.217.16.131 > 192.168.1.2: ICMP echo reply, id 17151, seq 3, length 64
22:46:51.635364 IP 192.168.1.2 > 172.217.16.131: ICMP echo request, id 17151, seq 4, length 64
22:46:51.648066 IP 172.217.16.131 > 192.168.1.2: ICMP echo reply, id 17151, seq 4, length 64
...
</pre>
<br>

Since we want to analyze the network packets with Wireshark in the next step, we need to save them into a so-called Packet Capture (<code>pcap</code>) file, which <code>tcpdump</code> can do when using the **-w** parameter. This example stores the captured packets into the file <code>nwt.pcap</code>:
<pre>
$ sudo tcpdump -i enp7s0 -w nwt.pcap -n
tcpdump: listening on enp7s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
</pre>
<br>


## Analyzing network packets with Wireshark

First, we need to download Wireshark: https://www.wireshark.org/download.html

To open the from the previous step created Packet Capture file, we need to go to **File -> Open** and select our file. We should see the captured packets now and can start analyzing them.
