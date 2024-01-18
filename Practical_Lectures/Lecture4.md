# Lecture 4

In our fourth lecture, we ...
* Changed the TCP congestion algorithm on our OpenWrt VM
* Made a few network performance tests using <code>iperf3</code>
* Wrote simple Client/Server applications using Python Sockets
<br><br>


## Changing the TCP congestion algorithm globally

To find out which TCP congestion algorithms our VM supports, we can look into the file <code>tcp_available_congestion_control</code>:
<pre>
root@lab-10:~# cat /proc/sys/net/ipv4/tcp_available_congestion_control
reno bbr bic cdg cubic dctcp westwood highspeed hybla htcp vegas nv veno scalable lp yeah illinois
</pre>
<br>

To change the algorithm to one of the available ones, we only need to write its name into the file <code>tcp_congestion_control</code>. The <code>echo</code> command prints (echoes) the given string to <code>stdout</code>, which is then redirected (<code>></code>) into the file <code>tcp_congestion_control</code>.
<pre>root@lab-10:~# echo bbr > /proc/sys/net/ipv4/tcp_congestion_control</pre>
<br>


## Network performance tests using <code>iperf3</code>

To do some performance tests, we needed to establish the Wireguard connection first (<code>sudo wg-quick up ...</code>), and then we started <code>iperf3</code> in server mode on our VM:
<pre>
root@lab-10:~# iperf3 -s
-----------------------------------------------------------
Server listening on 5201 (test #1)
-----------------------------------------------------------
</pre>
<br>

Next, we started the test on our local machine. The <code>iperf3</code> client sends the data to the server by default. Since the sender manages TCP congestion control and we only changed the algorithm on our VM, we need to tell <code>iperf3</code> that the server (our VM) should send the data using the Reserve parameter **-R**.
<pre>
$ iperf3 -c 172.20.20.1 -R
Connecting to host 172.20.20.1, port 5201
Reverse mode, remote host 172.20.20.1 is sending
[  5] local 172.20.20.2 port 40196 connected to 172.20.20.1 port 5201
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  21.8 MBytes   183 Mbits/sec                  
[  5]   1.00-2.00   sec  29.5 MBytes   247 Mbits/sec                  
[  5]   2.00-3.00   sec  29.2 MBytes   245 Mbits/sec                  
[  5]   3.00-4.00   sec  29.3 MBytes   246 Mbits/sec                  
[  5]   4.00-5.00   sec  29.3 MBytes   245 Mbits/sec                  
[  5]   5.00-6.00   sec  29.4 MBytes   247 Mbits/sec                  
[  5]   6.00-7.00   sec  28.7 MBytes   241 Mbits/sec                  
[  5]   7.00-8.00   sec  29.5 MBytes   248 Mbits/sec                  
[  5]   8.00-9.00   sec  29.3 MBytes   245 Mbits/sec                  
[  5]   9.00-10.00  sec  28.1 MBytes   235 Mbits/sec                  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.02  sec   287 MBytes   240 Mbits/sec  5229             sender
[  5]   0.00-10.00  sec   284 MBytes   238 Mbits/sec                  receiver

iperf Done.
</pre>
<br>

Afterward, we repeated the tests for various congestion algorithms and examined their differences.
<br><br>

## Changing the TCP congestion algorithm with iperf3

If you only want to change the TCP congestion algorithm for a single run, `iperf3` also has an option for that. To accomplish that, `iperf3` makes use of a feature in the Linux kernel to set the TCP congestion algorithm as a TCP socket option.
To learn more about that socket option, see the [man page for tcp](https://www.man7.org/linux/man-pages/man7/tcp.7.html) and search for `TCP_CONGESTION` in the section of socket options.

Taken from the iperf3 help (`iperf3 --help`) you can see there is a specific option to use that feature:
```bash
-C  --congestion <algo>    set TCP congestion control algorithm (Linux and FreeBSD only)
```
There you can use the same algorithm name as seen in `/proc/sys/net/ipv4/tcp_available_congestion_control`, e.g.:
```bash
iperf3 -c 192.168.1.1 --congestion vegas
```

---

## Socket Programming in Python

We wrote the client application on our local machine and the server on our VM based on the examples of the following page: https://realpython.com/python-sockets/
