# 6LoWPANd

6LoWPANd must be run as root in order to create the new network adaptor in the kernel using the tun driver. The only required parameter is the serial port to which the Border-Router node is connected.
```
root@localhost # 6LoWPANd –s /dev/ttyS0
```
Many other parameters of the IEEE802.15.4 network can be set via command line options to 6LoWPANd.
Once 6LoWPANd has started and is connected to the Border-Router node, it will create a new network interface named `tun0` (This may be configured via the `–interface` parameter to 6LoWPANd. This interface must be brought up and given a valid IPv6 address. A link local address is all that is required. A route to the IPv6 prefix of the JenNet-IP network may also be set up.
```
root@localhost # ifconfig tun0 up
root@localhost # ip -6 addr add fe80::1 dev tun0
root@localhost # ip -6 route add <JenNet-IP Prefix>/64 dev tun0
```
6LoWPANd saves this in a text file named `/tmp/6LoWPANd.<Interface name>`, as well as establishing an address record with Avahi if Avahi support was compiled in. It should now be possible to ping the IPv6 address of the coordinator.
```
root@localhost # ping6 `cat /tmp/6LoWPANd.tun0`
PING fd04:bd3:80e8:2:215:8d00:12:147d
(fd04:bd3:80e8:2:215:8d00:12:147d): 56 data bytes
64 bytes from fd04:bd3:80e8:2:215:8d00:12:147d: seq=0 ttl=255 time=11.082 ms
64 bytes from fd04:bd3:80e8:2:215:8d00:12:147d: seq=1 ttl=255 time=9.977 ms
64 bytes from fd04:bd3:80e8:2:215:8d00:12:147d: seq=2 ttl=255 time=9.808 ms
64 bytes from fd04:bd3:80e8:2:215:8d00:12:147d: seq=3 ttl=255 time=9.505 ms
```
Once the localhost can ping the IPv6 address of the Border-Router node, the routing information for other machines on the LAN can be set up to allow them to also route to it. This can be set up manually as follows. First, determine the link local address of the Ethernet interface of the Border-Router via the command `ip -6 addr`:
```
root@localhost # ip -6 addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436
  inet6 ::1/128 scope host
    valid_lft forever preferred_lft forever
3: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qlen 1000
  inet6 fe80::224:a5ff:fed8:1240/64 scope link
    valid_lft forever preferred_lft forever
```
Once the link local address is known, a route to the IPv6 prefix of the JenNet-IP network can be set up on another machine via the following commands:
```
root@localhost # ip -6 route add <JenNet-IP Prefix>/64 via <Border router Link Local Address> dev eth0
```
The Machine must also be given an additional IPv6 address with global scope so that it can send packets off link to the JenNet-IP network using the following commands:
```
root@localhost # ip -6 addr add fd04:80e8:bd3:1::2 dev eth0
```
Following this, the machine should also be able to ping the IPv6 address of the Border- Router node.


```
This software is owned by NXP B.V. and/or its supplier and is protected
under applicable copyright laws. All rights are reserved. We grant You,
and any third parties, a license to use this software solely and
exclusively on NXP products [NXP Microcontrollers such as JN5148, JN5142, JN5139]. 
You, and any third parties must reproduce the copyright and warranty notice
and any other legend of ownership on each copy or partial copy of the 
software.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
POSSIBILITY OF SUCH DAMAGE.

Copyright NXP B.V. 2012. All rights reserved
```
