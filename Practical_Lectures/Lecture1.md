# Lecture 1

In our first lecture, we set up the connection to our OpenWrt-based Virtual Machine (VM). Therefore, we ...
 * Generated an SSH private/public key pair
 * Created an SSH alias and tested the connection to the VM
 * Configured an IP address on the VM
 * Adjusted the firewall settings
 * Sent a ping to a VM of another team
<br><br>


## Generating an SSH private/public key pair

Generally, it is possible to connect to an SSH server via password-based authentication. The disadvantages are that, among others, you have to remember the password for every SSH connection and that it is not very feasible for automated processes and scripts.

Hence, we generated a private/public key pair using the following command inside a Linux or Windows command line:
<pre>$ ssh-keygen -t ed25519 -f ~/.ssh/nwt</pre>
<br>

About the parameters:
 * **-t**: Creates a key of type <code>ed25519</code>. You can also use the default <code>rsa</code> type, but <code>ed25519</code> keys are generally more efficient and secure.
 * **-f**: The file path where to store the keys. In this case, it stores the keys <code>nwt</code> and <code>nwt.pub</code> inside your <code>.ssh</code> directory.

After you have executed the command, you should have a private key called <code>nwt</code> and a public key called <code>nwt.pub</code> in your <code>.ssh</code> directory. As the name suggests, you shouldn't share the private keys with others. But you have to share the public key with the administrator of the SSH server so that he can authorize the SSH connection access when using the corresponding private key. For our seminar, you have forked this repository, entered your public key inside [ssh_public_keys.md](https://github.com/Meshlab-HSN/NWT_I_WiSe2324/blob/main/ssh_public_keys.md), and created a pull request using your [GitHub](https://github.com) account.
<br><br>


## Creating an SSH alias

After we registered your keys, you tried to connect to the so-called stepping stone via the following command:
<pre>$ ssh remote@evernet.duckdns.org -p 20322 -i ~/.ssh/nwt</pre>
<br>

To establish a connection to your OpenWrt VM, you had to do a so-called ProxyJump through the stepping stone. Although this is possible via the **-J** parameter on the SSH command line, it seems that it isn't possible to specify the private key for the ProxyJump server. Moreover, the command would get pretty long, and you have to type the command every time you want to connect.

Because of that, we created an alias inside the SSH configuration file <code>~/.ssh/config</code>. The file should have the following content:
<pre>
Host nwt-vmX-stepping-stone
    Hostname evernet.duckdns.org
    Port 20322
    User remote
    IdentityFile ~/.ssh/nwt

Host nwt-vmX
    Hostname 172.20.1.<10 + team number>
    User root
    ProxyJump nwt-vmX-stepping-stone
    IdentityFile ~/.ssh/nwt
</pre>
<br>

Don't forget to enter the proper IP for your VM. In this case, <code>nwt-vmX</code> is the name of the SSH alias. We can now connect to the OpenWrt VM via SSH. If you configured everything correctly, you should see the OpenWrt banner and can start typing commands inside the shell.
<pre>
$ ssh nwt-vmX


BusyBox v1.36.1 (2024-01-06 16:34:18 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt SNAPSHOT, r24758-e1d8e57614
 -----------------------------------------------------
root@lab-10:~# echo "Ich bin drin!"
Ich bin drin!
root@lab-10:~# uname -a
Linux lab-10 6.1.71 #0 SMP Sat Jan  6 16:34:18 2024 x86_64 GNU/Linux
root@lab-10:~#
</pre>
<br>


## Configuring an IP address on the VM

You can use the <code>ip</code> command to, among many other things, configure an IP address for an interface. First, we checked every interface and their configured addresses on the VM. The output was similar to the following:
<pre>
root@lab-10:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host proto kernel_lo 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether ca:01:e1:5c:14:22 brd ff:ff:ff:ff:ff:ff
    inet 172.20.1.20/24 brd 172.20.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::c801:e1ff:fe5c:1422/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 86:b8:1f:8d:d0:1d brd ff:ff:ff:ff:ff:ff
</pre>
<br>

Since <code>eth0</code> is connected to the stepping stone, we don't want to touch it and configure <code>eth1</code> instead. There is currently no IPv4 address configured on <code>eth1</code>, and it is down/not enabled. To add an IP address to the interface, we can use the following command:
<pre>
root@lab-10:~# ip a add 10.10.23.&lt;team number&gt;/24 dev eth1

# for example
root@lab-10:~# ip a add 10.10.23.10/24 dev eth1
</pre>
<br>

And then we enabled the interface:
<pre>root@lab-10:~# ip link set eth1 up</pre>
<br>

If we execute the <code>ip a</code> command again, we should see the configured changes:
<pre>
root@lab-10:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host proto kernel_lo 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether ca:01:e1:5c:14:22 brd ff:ff:ff:ff:ff:ff
    inet 172.20.1.20/24 brd 172.20.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::c801:e1ff:fe5c:1422/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 86:b8:1f:8d:d0:1d brd ff:ff:ff:ff:ff:ff
    inet 10.10.23.10/24 scope global eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::84b8:1fff:fe8d:d01d/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
</pre>
<br>

The problem with the <code>ip</code> command is that Linux doesn't preserve the changes after a reboot, so you have to type the commands again. That is why we configured the IP via [OpenWrt's Unified Configuration Interface (UCI)](https://openwrt.org/docs/guide-user/base-system/uci) by editing the network configuration file using the <code>vim</code> editor:
<pre>root@lab-10:~# vi /etc/config/network</pre>
<br>

And then, we appended a new <code>lan2</code> interface:
<pre>
config interface 'lan2' 
        option device 'eth1'
        option proto 'static'
        option ipaddr '10.10.23.&lt;team number&gt;'
        option netmask '255.255.255.0'
</pre>
<br>

Finally, we restarted the network service to apply the changes:
<pre>root@lab-10:~# /etc/init.d/network restart</pre>
<br>

If we execute the <code>ip a</code> command again, we should see the same configured changes as before.
<br><br>


## Adjusting the firewall settings

After we added an IP address on <code>eth1</code>, we also need to allow incoming traffic on that interface so that the other VMs can reach/ping us. To do so, we edited the firewall configuration file:
<pre>root@lab-10:~# vi /etc/config/firewall</pre>
<br>

Then, we added the previously defined <code>lan2</code> interface to the <code>lan</code> zone:
<pre>
config zone
        option name             lan
        list   network          'lan1'
        list   network          'lan2'
        option input            ACCEPT
        option output           ACCEPT
        option forward          ACCEPT
</pre>
<br>

Finally, we restarted the firewall service to apply the changes:
<pre>root@lab-10:~# /etc/init.d/firewall restart</pre>
<br>

## Sending a ping to another VM

If you and the other team have configured the firewall correctly, a ping should be sent and received without problems:
<pre>
root@lab-10:~# ping 10.10.23.6
PING 10.10.23.6 (10.10.23.6): 56 data bytes
64 bytes from 10.10.23.6: seq=0 ttl=64 time=0.323 ms
64 bytes from 10.10.23.6: seq=1 ttl=64 time=0.240 ms
64 bytes from 10.10.23.6: seq=2 ttl=64 time=0.237 ms
64 bytes from 10.10.23.6: seq=3 ttl=64 time=0.212 ms
^C
--- 10.10.23.6 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 0.212/0.253/0.323 ms
</pre>
