# Lecture 5 (16.01.2024)

In our fifth lecture, we ...
* talked about IPv6, how it is designed, which features it has and how it compares to IPv4
* tested multicast ping in IPv6 link-local scope
* deployed IPv6 in our existing (so far IPv4-only) Wireguard tunnel
* configured OpenWrt to have a global-scope IPv6 address and handout Router Advertisment with the global-scope prefix
<br><br>

## Multicast ping in IPv6 link-local scope

In IPv6, a network interface always has a link-local address that the device assigns to itself within the range `fe80::/64`. This address is essential for the basic functioning of an IPv6 network,
e.g. it is used for neighbor discovery, address autoconfiguration, etc.

IPv6 has support for multicasting using special multicast address ranges, similar to IPv4. Multicast addresses in IPv6 always have the prefix `ff::/8`, followed by flags which determine the scope of the multicast address.
In the [IPv6 standard](https://www.iana.org/assignments/ipv6-multicast-addresses/ipv6-multicast-addresses.xhtml) several multicast address ranges are defined to group devices for different purposes.
Most important and useful for us is the `ff02::1` multicast address, which is a link-local multicast address and assigned to the multicast group 'all nodes'. In fact, this is how broadcasting is possible with IPv6.

To test this and see if it works, we used our OpenWrt VMs. Each VM has multiple interfaces with each a link-local address assigned by default.
We then used the ping command to send ICMP echo requests to the multicast address `ff02::1`:
```bash
ping ff02::1%eth0
```
*One important thing to note here is the addition of `%eth0` to the address.* In case you have multiple IPv6-enabled interfaces on your device, each of them has an IPv6 link-local address in the `fe80::/64` address range.
If you would now just try to use the plain address, your operating system would not know by itself which interface it has to use for the operation. Thus, you always also have to specify the interface to use with the syntax `%<interface identifier>`.
In Linux, this `interface identifier` is usually the name of the interface, in our case it was `eth0`. However, Windows uses a slightly different approach by assigning successive numeric identifiers to its network interfaces
starting at zero. Thus, when you try to use an IPv6 address in Windows, you need to find out the numeric identifier at first and specify this with the same syntax in combination with the address, e.g. `ff02::1%5`.

<br>

After running that command, you should see an output similar to this:
```
root@lab-10:~# ping ff02::1%eth0
PING ff02::1%eth0 (ff02::1%2): 56 data bytes
64 bytes from fe80::c801:e1ff:fe5c:1422: seq=0 ttl=64 time=0.135 ms
64 bytes from fe80::230:5ff:fe38:6f86: seq=0 ttl=64 time=0.309 ms (DUP!)
64 bytes from fe80::e8f2:d8ff:fe06:2f12: seq=0 ttl=64 time=0.323 ms (DUP!)
64 bytes from fe80::b886:c2ff:fe25:162c: seq=0 ttl=64 time=0.326 ms (DUP!)
64 bytes from fe80::a8e1:3fff:fe13:2952: seq=0 ttl=64 time=0.390 ms (DUP!)
64 bytes from fe80::ac6b:64ff:fe6e:cc5f: seq=0 ttl=64 time=0.407 ms (DUP!)
64 bytes from fe80::be24:11ff:fead:890c: seq=0 ttl=64 time=0.415 ms (DUP!)
64 bytes from fe80::c8de:f1ff:fe01:98d9: seq=0 ttl=64 time=0.423 ms (DUP!)
64 bytes from fe80::cc99:25ff:fe84:be97: seq=0 ttl=64 time=0.442 ms (DUP!)
64 bytes from fe80::b8cc:3aff:fe31:33cb: seq=0 ttl=64 time=0.457 ms (DUP!)
64 bytes from fe80::bc47:acff:fe8f:fc60: seq=0 ttl=64 time=0.461 ms (DUP!)
64 bytes from fe80::fcc6:23ff:fe97:200a: seq=0 ttl=64 time=0.465 ms (DUP!)
64 bytes from fe80::4c84:52ff:feb9:4910: seq=0 ttl=64 time=0.470 ms (DUP!)
64 bytes from fe80::a0d9:f0ff:fe88:cabd: seq=0 ttl=64 time=0.475 ms (DUP!)
64 bytes from fe80::d49d:a7ff:fecc:fd90: seq=0 ttl=64 time=0.479 ms (DUP!)
64 bytes from fe80::1439:c8ff:fe3e:f8fb: seq=0 ttl=64 time=0.483 ms (DUP!)
64 bytes from fe80::9823:64ff:fe37:3299: seq=0 ttl=64 time=0.488 ms (DUP!)
64 bytes from fe80::446f:9aff:fe37:d320: seq=0 ttl=64 time=0.492 ms (DUP!)
64 bytes from fe80::247b:e4ff:fe72:8c86: seq=0 ttl=64 time=0.497 ms (DUP!)
64 bytes from fe80::50b0:d4ff:fe64:2e19: seq=0 ttl=64 time=0.501 ms (DUP!)
64 bytes from fe80::bd:88ff:fe46:b26e: seq=0 ttl=64 time=0.506 ms (DUP!)
64 bytes from fe80::c0:88ff:fedd:4ba2: seq=0 ttl=64 time=0.510 ms (DUP!)
64 bytes from fe80::c467:64ff:fed7:c826: seq=0 ttl=64 time=0.514 ms (DUP!)
64 bytes from fe80::1854:79ff:fecd:6879: seq=0 ttl=64 time=0.519 ms (DUP!)
```

In the output you can see several ICMP echo-replies which are coming from all devices that are in the same network segment as your device (e.g. all devices connected to a switch).
Thus, the multicast ping can be used to easily find out which devices are connected to your link-local IPv6 network.

> Unfortunately, some operating systems like Windows seem to break this by not replying to the ICMP echo-requests depending on which network you are connected to and if Windows classifies this as `public` or `private`.
> In `public` networks, Windows uses more restrictive firewall rules which also include to reject ICMP echo packets. To solve this, you need to change the firewall settings of Windows to allow all ICMPv6 traffic for all networks.

<br>

## Deploying IPv6 in our Wireguard tunnel

In Lecture 2 we configured a Wireguard tunnel between your laptop and your OpenWrt VM the have a network between both devices. However, we only used simple IPv4 addresses so far. But it is also possible to use IPv6 in Wireguard tunnels.
For that we changed to Wireguard configuration on both the laptop and the OpenWrt VM to add a new IP address.

On our OpenWrt VM, we opened the network configuration with `vim /etc/config/network` and adjusted the Wireguard configuration as follows to assign the address `fe80::1` to our OpenWrt VM:
```diff
 config interface 'wg0'
     option proto 'wireguard'
     option listen_port '20410'
     option private_key '<private key>'
     list addresses '172.20.20.1/24'
+    list addresses 'fe80::1/64'

 config wireguard_wg0
     option description 'laptop_client'
     option public_key '<public key>'
     list allowed_ips '172.20.20.2/24'
+    list allowed_ips 'fe80::2/128'
```
In addition to assigning the IP address, we also added the IP address, that we will assign to the laptop in the next step, to the list of allowed IPs so the client can use it within the tunnel.

Similarly, we adjusted the config of the Wireguard client on the laptop to assign `fe80::2` to the laptop:
```diff
 [Interface]
 PrivateKey = <private key>
-Address = 172.20.20.2/32
+Address = 172.20.20.2/32, fe80::2/128

 [Peer]
 PublicKey = <public key>
 Endpoint = evernet.duckdns.org:20410
-AllowedIPs = 172.20.20.0/24
+AllowedIPs = 172.20.20.0/24, fe80::/64
```

> Adding `fe80::/64` to `AllowedIPs` may not work in some cases, depending on your operating system. Since it is likely that you already have another IPv6-enabled interface, there will already be an entry for `fe80::/64` in the routing table.
> Thus, Wireguard will not able to add another one. This doesn't seem to be a problem with Windows and macOS, but with Linux. For that case, the `ip` command offers the subcommand `append` to add another hop for an existing route,
> which exactly solves this problem. Thus, you would need to manually add the corresponding entry with `ip -6 route append fe80::/64 dev wg0`.

Then we verified that the setup is working by pinging either device through the Wireguard tunnel:
```bash
root@lab-10:~# ping fe80::2%wg0
PING fe80::2%wg0 (fe80::2%35): 56 data bytes
64 bytes from fe80::2: seq=0 ttl=64 time=33.946 ms
64 bytes from fe80::2: seq=1 ttl=64 time=31.219 ms
64 bytes from fe80::2: seq=2 ttl=64 time=30.987 ms
64 bytes from fe80::2: seq=3 ttl=64 time=31.157 ms
64 bytes from fe80::2: seq=4 ttl=64 time=32.599 ms
64 bytes from fe80::2: seq=5 ttl=64 time=31.172 ms
64 bytes from fe80::2: seq=6 ttl=64 time=31.438 ms
64 bytes from fe80::2: seq=7 ttl=64 time=31.857 ms
64 bytes from fe80::2: seq=8 ttl=64 time=31.548 ms
64 bytes from fe80::2: seq=9 ttl=64 time=31.135 ms
64 bytes from fe80::2: seq=10 ttl=64 time=31.977 ms
```

## Configuring Router Advertisments for SLAAC

As we discussed in the seminar, IPv6 provides an alternative mechanism to DHCP for address configuration called `SLAAC` (Stateless Address Autoconfiguration).
This mechanism relies on the ICMPv6 protocol and its Router Advertisement (RA) messages. This kind of message is used in IPv6 network to delegate prefix information from a router to the devices in a network.
A real-world application for this is your IPv6-enabled internet connection provided by your ISP. Your ISP usually assigns an IPv6 prefix to your router, which will then delegate this prefix to the devices in your home
network for SLAAC so that every device has its own global-scope IPv6 address and the router doesn't need to assign these addresses.

To mimic this behaviour in our Wireguard tunnel, we assigned a global-scope IPv6 address to our Wireguard interface on the VM and also allowed to use the address range within the tunnel:
```diff
 config interface 'wg0'
     option proto 'wireguard'
     option listen_port '20410'
     option private_key '<private key>'
     list addresses '172.20.20.1/24'
     list addresses 'fe80::1/64'
+    list addresses '2000::/64'

 config wireguard_wg0
     option description 'laptop_client'
     option public_key '<public key>'
     list allowed_ips '172.20.20.2/24'
     list allowed_ips 'fe80::2/128'
+    list allowed_ips '2000::/64'
```

After that, we switched over to the dhcp configuration in `/etc/config/dhcp`. Since odhcp6d not only provides a DHCPv6 server but is also responsible for Router Advertisements, we need to do the configuration there.
For that we added a new dhcp block for our Wireguard interface with the following options:
```
config dhcp 'ra_wg0'
    option interface 'wg0'
    option ra 'server'
    option ra_slaac '1'
    option dhcpv4 'disabled'
    option dhcpv6 'disabled'
```

After saving this we restarted network, firewall and odhcpd to make sure all changes got applied properly:
```bash
/etc/init.d/network restart
/etc/init.d/firewall restart
/etc/init.d/odhcpd restart
```

Then we switched over to our laptop and use either tcpdump or Wireshark to listen on the Wireguard interface hoping to see some Router Advertisements. When using tcpdump with
```bash
tcpdump -nei <interface> -v icmp6
```
you should see every 16 seconds an ICMP RA packet:
```bash
tcpdump: listening on utun4, link-type RAW (Raw IP), snapshot length 524288 bytes
15:55:43.310899 IP6 (flowlabel 0x80ab5, hlim 255, next-header ICMPv6 (58) payload length: 120) jonas-mbp.local > ff02::1: [icmp6 sum ok] ICMP6, router advertisement, length 120
	hop limit 64, Flags [managed, other stateful], pref medium, router lifetime 0s, reachable time 0ms, retrans timer 0ms
	  source link-address option (1), length 8 (1): 00:00:00:00:00:00
	  mtu option (5), length 8 (1):  1420
	  prefix info option (3), length 32 (4): 2000::/64, Flags [onlink, auto], valid time infinity, pref. time infinity
	  route info option (24), length 24 (3):  2000::/56, pref=medium, lifetime=1800s
	  rdnss option (25), length 24 (3):  lifetime 1800s, addr: 2000::
	  advertisement interval option (7), length 8 (1):  600000ms
```

Looking at the output you can see that the ICMPv6 Router Advertisement packet includes the prefix `2000::/64` that we just used on our OpenWrt VM and is now delegated.
