# Lecture 2

In our second lecture, we ...
 * Setup a Wireguard Server on our OpenWrt VM
 * Setup a Wireguard Client on our local machine
 * Sent a picture between our local and virtual machine using netcat
<br><br>


## Wireguard Server Setup

First, we generated a private/public key pair with the following command chain:
<pre>root@lab-10:~# wg genkey | tee wg.key | wg pubkey > wg.pub</pre>
<br>

The <code>|</code> operator pipes <code>stdout</code> of the left command into <code>stdin</code> of the right.<br>
The <code>tee</code> command reads from <code>stdin</code>, writes it to the specified file, and prints it to <code>stdout</code>.<br>
The <code>></code> operator writes <code>stdout</code> of the left command into the file specified on the right.
<br><br>

Therefore, the individual commands do the following:
* <code>wg genkey</code>: Creates a new **private** key and prints it to <code>stdout</code>
* <code>tee wg.key</code>: Reads the **private** key from <code>stdin</code>, writes it into the file <code>wg.key</code> and prints it to <code>stdout</code>
* <code>wg pubkey</code>: Reads the **private** key from <code>stdin</code>, derives the **public** key from it and prints it to <code>stdout</code>
* <code>> wg.pub</code>: Writes the **public** key to the file <code>wg.pub</code>
<br><br>

Now, we should read our **private** key and copy it for the next step:
<pre>
root@lab-10:~# cat wg.key
8BZbUMAjlKaUt0v+0Ayb766E9mYKc0v6EkVhs3GWtG8=
</pre>
<br>

Like the SSH private keys, you shouldn't share the Wireguard private keys with others, i.e., it shouldn't leave your VM.
Afterward, we configured the Wireguard Server on our VM by editing the network configuration file:
<pre>root@lab-10:~# vi /etc/config/network</pre>
<br>

And appended the following content:
<pre>
config interface 'wg0'
        option proto 'wireguard'
        option listen_port '20410'
        option private_key '&lt;your private key from wg.key&gt;'
        list addresses '172.20.20.1/24'
</pre>
<br>

Finally, we restarted the network service to apply the changes:
<pre>root@lab-10:~# /etc/init.d/network restart</pre>
<br>

If we type in <code>wg show</code> now, we should see our configured Wireguard interface <code>wg0</code>:
<pre>
root@lab-10:~# wg show
interface: wg0
  public key: OKWAcHPgKsuZtBdEvxnnHAH7sRxCIslKB6wdq+hkshE=
  private key: (hidden)
  listening port: 20410
</pre>
<br>


### Adjusting the Firewall

After we have added the Wireguard interface, we also need to allow incoming traffic on that interface. To do so, we edited the firewall configuration file:
<pre>root@lab-10:~# vi /etc/config/firewall</pre>
<br>

Then, we added the <code>wg0</code> interface to the <code>lan</code> zone:
<pre>
config zone
        option name             lan
        list   network          'lan1'
        list   network          'lan2'
        list   network          'wg0'
        option input            ACCEPT
        option output           ACCEPT
        option forward          ACCEPT
</pre>
<br>

> For simplicity, we just used one firewall zone for all interfaces which accepts all INPUT, OUTPUT and FORWARDING traffic. Since our setup is virtualised and protected from host-side, this is safe to do here. In real physical networks, this is obviously dangerous!

## Wireguard Client Setup

First, we downloaded the Wireguard Client from the Wireguard Website on our local machine: https://www.wireguard.com/install/

Then, as we did before on the VM, we generated a private/public key pair:
<pre>$ wg genkey | tee wg.key | wg pubkey > wg.pub</pre>
<br>

And read the **private** key of our local machine:
<pre>$ cat wg.key</pre>
<br>

Now, we briefly switch once to our VM and read the **public** key:
<pre>root@lab-10:~# cat wg.pub</pre>
<br>

Next, we created the Wireguard configuration file using any editor (<code>nano</code> in this example). Since the file name is <code>nwt.conf</code> in this example, the Wireguard interface will also be named <code>nwt</code>. <code>sudo</code> is used here since you probably need root access to access the <code>/etc/wireguard</code> folder.
<pre>$ sudo nano /etc/wireguard/nwt.conf</pre>
<br>

And we inserted the following content:
<pre>
[Interface]
Address = 172.20.20.2/32
PrivateKey = &lt;the private key of your local machine from wg.key&gt;

[Peer]
PublicKey = &lt;the public key of your VM from wg.pub&gt;
AllowedIPs = 172.20.20.0/24
EndPoint = evernet.duckdns.org:20410
</pre>
<br>

Now, we read the **public** key of our local machine:
<pre>$ cat wg.pub</pre>
<br>

And switch back to our VM and open the network configuration again:
<pre>root@lab-10:~# vi /etc/config/network</pre>
<br>

And append the following configuration for our Client/Peer:
<pre>
config wireguard_wg0
        list allowed_ips '172.20.20.2/32'
        option public_key '&lt;the public key of your local machine from wg.pub&gt;'
        option description 'peer1'
</pre>
<br>

And we restarted the network service again to apply the changes:
<pre>root@lab-10:~# /etc/init.d/network restart</pre>
<br>

If we type in <code>wg show</code> again, we should still see our Wireguard interface and additionally our configured Peer:
<pre>
root@lab-10:~# wg show
interface: wg0
  public key: OKWAcHPgKsuZtBdEvxnnHAH7sRxCIslKB6wdq+hkshE=
  private key: (hidden)
  listening port: 20410

peer: TsfjnuqEALZIuk4105KLVYLUVpIEIR1kIkTPMUGhTQ8=
  allowed ips: 172.20.20.2/32
</pre>
<br>

Finally, we can enable the Wireguard interface on our local machine:
<pre>$ sudo wg-quick up nwt
[#] ip link add nwt type wireguard
[#] wg setconf nwt /dev/fd/63
[#] ip -4 address add 172.20.20.2/32 dev nwt
[#] ip link set mtu 1420 up dev nwt
[#] ip -4 route add 172.20.20.0/24 dev nwt
</pre>
<br>

And can verify through a ping that we have connected successfully:
<pre>
$ ping 172.20.20.1
PING 172.20.20.1 (172.20.20.1) 56(84) bytes of data.
64 bytes from 172.20.20.1: icmp_seq=1 ttl=64 time=133 ms
64 bytes from 172.20.20.1: icmp_seq=2 ttl=64 time=163 ms
64 bytes from 172.20.20.1: icmp_seq=3 ttl=64 time=195 ms
64 bytes from 172.20.20.1: icmp_seq=4 ttl=64 time=122 ms
^C
--- 172.20.20.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3002ms
rtt min/avg/max/mdev = 121.560/153.195/194.695/28.387 ms
</pre>
<br>


## Sending a picture using netcat

> You will often find that there are different implementations of `netcat`:
> - `nc`: traditional netcat implementation
> - `netcat`: OpenBSD-based implementation of netcat. Compared to `nc` it has more features and options to use.
> - `ncat`: Much-improved reimplementation of the traditional netcat, originating in the Nmap project. It's feature-set can be compared with OpenBSD-netcat.

To send a picture from our VM to our local machine, we need to download one to our VM first (optimally, a relatively big picture). For that, we can either use <code>wget</code> or <code>curl</code>:
<pre>
wget &lt;web link of the picture&gt; -O &lt;filename&gt;
curl &lt;web link of the picture&gt; -o &lt;filename&gt;

# for example
root@lab-10:~# wget https://upload.wikimedia.org/wikipedia/commons/7/71/HS_Nordhausen%2C_Mensa%2C_Haus_10.jpg -O mensa.jpg

# or
root@lab-10:~# curl https://upload.wikimedia.org/wikipedia/commons/7/71/HS_Nordhausen%2C_Mensa%2C_Haus_10.jpg -o mensa.jpg
</pre>
<br>

After downloading the picture, we can start <code>netcat</code> in server/listen mode on our local machine. In this example, the server binds to the dynamic/private port <code>55555</code> and writes every received byte into the file <code>download.jpg</code>.
<pre>$ ncat -l -p 55555 > download.jpg</pre>
<br>

Now we can start sending the picture from our VM:
<pre>root@lab-10:~# ncat 172.20.20.2 55555 < mensa.jpg</pre>
<br>

If that works, we can open the picture on our local machine; it should be the same as the original one.<br>
<code>netcat</code> uses the TCP protocol by default. To use the UDP protocol with <code>netcat</code>, we need to specify the **-u** parameter. So, on our local machine, we type now:
<pre>$ ncat -l -p 55555 -u > download.jpg</pre>
<br>

And on the VM:
<pre>root@lab-10:~# ncat 172.20.20.2 55555 -u < mensa.jpg</pre>
<br>

If the downloaded picture is big enough, we should see that it no longer looks like it did before ...
