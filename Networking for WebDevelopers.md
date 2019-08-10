# Networking for Webdevelopers

[UDC](https://classroom.udacity.com/courses/ud256/lessons/7082481045/concepts/70529739900923)

## Fiddling with HTTP

- One can use netcat `nc` and pipe a `printf` formatted text to `nc` to send a valid HTTP request
  - _`printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n' | nc en.wikipedia.org 80`_
- _`nc` is a thin wrapper around `TCP`_

## NETWORk LAYERS

```javascript
[
  {
      layer:`Application`,
      Protocols:`Http, SSH`,
      Concepts:`URL's,  Passwords`
  },
  {
      layer:`Transport`,
      Protocols:`TCP, UDP`,
      Concepts: `port numbers, sessions`
  },
  {
      layer:`Internet`,
      Protocols:`IP`,
      Concepts:`IP Addresses, Routes`
  },
  {
      layer:`Hardware`,
      Protocols:`WIFI, Ethernet, DSL`,
      Concepts:`Signal Strength, `
  }
]
```

- _Each layer provides specific gaurantees to the layer above_
- _IP layer is the narrow waist of the Internet Protocol_
- _If no process is listening on a port then the requestee will recieve a `re-set packet` indicating that no Server or process is listening the request_
- _TCP session is a two way transmission_
- ***Ports numbers a process can listen to is ranges from 1024 to 65535***
- _Using `sudo` ypu can bring it down to `1` the lowest port number one can listen to_
- _From 0 to 1023 are reserved for the SuperUser / sudo_
- [UPDATE MAC SHELL](https://itnext.io/upgrading-bash-on-macos-7138bd1066ba)

## How Server handles multiple requests

- ***Only one process can listen to a port at a time***
- ***A Server can spawn various `threads` or `child processes` which can listen and process various request coming in through the port Server is listening on***
- _`lsof -i` lists network sockets_

> ## How servers use to handle multiple requests:
>
> - Server used to ask the operating system to fork them
> - Or Server use to have a pool of processes or threads
> - Or Server Quickly switches between requests - as and when requests become available
> - __

## DNS

- Every request over the network is split into packets
- packets have ***`{ IPAddr: Machine-it_is-cominFrom, IPAddr: Machine-it_is-goingTo }`***
- ***HOSTS*** - a machine on the internet which might host services
- _Endpoints of a connection - The Machines communicating over a connection_

### How host name get translated to IP

- with a DNS - Domain Name System
- has A-records in DNS | A stands for Address | Maps a Name to a IPv4 Address
- Clients (Browsers) lookup these A-records to find the IPAdress for the Site they are looking for
- As a Website creator you have to setup DNS record for the site
  - _Registering a Name with a registrar_
  - _Pointing the DNS A-record to the WebSrever IPAddr_
- DNS client code - **Resolver** is built into every OS and processes on the OS like `Browsers, NetCAt, ping` can use it to resolve a site to its IPv4Addr

### Different Commands

- `host` command to get IPv4 addr of a site from a DNS service my Computer is connected with `host -t a bluejeans.com`
- _use `dig` command to get script readable information_
- _DNS is a distributed directory_
- _`gtld` - global top level domain_
- Local **RESOLVER** contacts nearby caching-DNS server
- _DNS Cache entry has `TTL`: Time To Live_
- __

## DNS and HTTPS

- Web apps sets `cookies` for a particular domain name
- Further requests to that domain-name will get the set `cookies` sent back
- `SSL Certificate` help asccertain that you are talking to the said Genuine-server not some imposter-server
- DNS domains are structured as a tree `.com, .edu`
  - www.Github.com `subdomainOf` Github.com
  - test.www.Github.com `subdomainOf` www.Github.com

## IPv4 and IPv6

- IPv4 - dotted quads - four numbers joined with dots - `218.44.56.123`
- each number is 8 bits or Octet - each number ranges from 0-255
- IPv4 address is 32 bits wide - `8bits.8bits.8bits.8bits = 32bits`


## Addressing and Networks

> IP Network Blocks, Routers, Network Address Translations

### IPv4 - not all 32 bits are used for addressing

- ***`1/8`th*** of the bits are used for other purposes than addressing
- 4.3 billion addresses could have been created <  7 billion - population of the world

### Netblocks and Subnets

- Computers in the same network usually have similar looking IP addresses
- All the addresses on a ***network block*** share a particular ***`prefix`***
- Computers on the same ***network block*** can communicate with each other without going through a router
- ***network block*** looks somewhat like ***`198.51.100.0/24`*** - first 24 bits will have a fixed value
- the left-over bits decide the address space - for above example we have 8 bits left - translates to 2ˆ8 addresses = ***`256`*** out of which top and the bottom of the ***network block*** are reserved and ***the first*** is reserved for the router
  - effectively we are left with ***`253`*** addresses

> Subnet Mask - for the above case 24'1s {111111111111111111111111}-8'0s{00000000}
> 255.255.255.0 in dotted quad notation

- _**network traffic consists of Data Packets => `{ Sender Addr, Reciever Addr }`**_
- _IP Addresses dont belong to the hosts(machines)  but to the ***`Intefaces`*** on those hosts_

> A laptop can have multiple intefaces - Ethernet Interface, Wifi Interface, loopback interface
> loopback interface : 127.0.0.1/8

### NAT - NETWORK ADDRESS TRANSLATION

- ISP assigns single `IPv4 address` to a given household connection
- All the devices are connected to the internet through this `IPv4 Address` by a `Router`
- This router acts as a `Default Gateway`
- Router connects to the ISP and gets a single `real public IPv4 address`
- Router then assigns `unique private addresses` to all the connected devices
  - Private address netblocks: 
    - ***10.0.0.0/8***
    - ***172.16.0.0/12***
    - ***192.168.0.0/16***
  - Most common on home routes ***192.168.0.0/24***
    - ***Default gateway being  - 192.168.0.1***
- ***Private IP Addresses are used with a system called `NAT - Network Address Translation`***
- `NAT`  mantains the **mapping** between which `private-addresses:ports` are connected with which `public-addresses:ports`

- `NAT` is a workaround for address shortage
- ***Problems*** caused by `NAT`
  - Difficult to run servers which other people can access etc
  - Your machine doesnt know it's public address it can broadcast

### IPv6

- Fixes address shortage problem
- 128 bits - 16 octets
- 2ˆ128 addresses
- IPv6 Individual address for home can be /64 network space much larger than v4 address space
- IPv6 addresses are written in Hex - block of 2 bytes separated by colons - as decimal quad equivalent
- But on the wire it is sent accross as a string of 128 bits

## Protocol Stack

- HTTP / TCP / IP / HardWare
- `sudo tcpdump -n host 8.8.8.8` to observe TCP traffic when ping 8.8.8.8

> Eureka - for HTTP everything sent over the wire - HAST TO BE TEXT - Semantic depth is given to this text is by the Client (Browser) receiving the text
> For HTTP - it is just a text resource

### Analyzing TCP_Dump data

```javascript
tcpdump: data link type PKTAP
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on pktap, link-type PKTAP (Apple DLT_PKTAP), capture size 262144 bytes

10:25:39.879344 IP 192.168.2.2.58106 > 93.184.216.34.80: Flags [P.], seq 812182894:812182910, ack 707729088, win 4117,// options [nop,nop,TS val 470907000 ecr 2588453651], 
length 16: HTTP: HEAD / HTTP/1.1
/**
length gives the size of the payload-data size not the tcp-packet size
*/


10:25:40.151784 IP 93.184.216.34.80 > 192.168.2.2.58106: Flags [.], ack 16, win 283, 
//options [nop,nop,TS val 2588480090 ecr 470907000], 
length 0

10:25:47.652261 IP 192.168.2.2.58106 > 93.184.216.34.80: Flags [P.], seq 16:34, ack 1, win 4117, 
//options [nop,nop,TS val 470914760 ecr 2588480090], 
length 18: HTTP

10:25:47.894222 IP 93.184.216.34.80 > 192.168.2.2.58106: Flags [.], ack 34, win 283, 
//options [nop,nop,TS val 2588487864 ecr 470914760], 
length 0

10:25:49.036967 IP 192.168.2.2.58106 > 93.184.216.34.80: Flags [P.], seq 34:35, ack 1, win 4117, options [nop,nop,TS val 470916132 ecr 2588487864], length 1: HTTP

10:25:49.287883 IP 93.184.216.34.80 > 192.168.2.2.58106: Flags [.], ack 35, win 283, options [nop,nop,TS val 2588489259 ecr 470916132], length 0

10:25:49.288898 IP 93.184.216.34.80 > 192.168.2.2.58106: Flags [P.], seq 1:339, ack 35, win 283, options [nop,nop,TS val 2588489260 ecr 470916132], length 338: HTTP: HTTP/1.1 200 OK

10:25:49.288942 IP 192.168.2.2.58106 > 93.184.216.34.80: Flags [.], ack 339, win 4107, options [nop,nop,TS val 470916381 ecr 2588489260], length 0
```

### Sequence Diagrams

- [WIKI](https://en.wikipedia.org/wiki/Sequence_diagram)
- A sequence digaram shows one-layer interaction example -  TCP or HTTP

```javascript
[
    {
        `what tcp does`: "communicate between two hosts",
        `how TCP does it`: "IP Layes(--addresses and routing--)"
    },
    {
        `what tcp does`: "multiple applications per host",
        `how TCP does it`: "port numbers"
    },
    {
        `what tcp does`: "In-order delivery",
        `how TCP does it`: "sequence numbers"
    },
    {
        `what tcp does`: "lossless delivery",
        `how TCP does it`: "ack + retransmission"
    },
    {
        `what tcp does`: "keeping connections distinct",
        `how TCP does it`: "random initial sequence numbers"
    }

]
```

- _when a client sends a packet - representing request - Server sends a ack packet before even sending the response packets_
- _why `Sequence numbers` and `how does it work`_
  - _Each endpoint's `OS` `{**NOT in the browser or other application**}` keeps a space in the memory that it fits the incoming data into and uses these `Sequence numbers` as ***positions***{byte positions}_
  - _this `buffer / memory` is the place where packets are fit together `in-order` using `sequence numbers`. 
  
  > ***These packets can overlap in sequence numbers + size or there could be duplicate packets***

### TCP Flags

- Each TCP packet has a section called ***`Flags`***
  - Example ***`[S], [S.], [.], [P.]`, and `[F.]`***
- It tells you which `flags`, or ***`control bits`***, are set on each TCP packet.

> side note: a `Flag` is a boolean value represented as a `bit`. Either the bit is **set** `1` or **cleared** `0`

- Usua;ly flags come in groups, each of which can be `set` or `cleared`

#### The six basic TCP flags

- original TCP packet format has `6 flags`
- plus `2 optional flags`
- tcpdump shows you `which flags` are set on that packet
  - ***`SYN (synchronize) [S]`*** — This packet is opening a new TCP session and contains a new initial sequence number.
  - ***`FIN (finish) [F]`*** — This packet is used to close a TCP session normally
    - The sender is saying that they are finished sending
    - Sender can still receive data from the other endpoint
  - ***`PSH (push) [P]`*** — This packet is the end of a chunk of application data, such as an HTTP request.
  - ***`RST (reset) [R]`*** — This packet is a TCP error message
    - the sender has a problem and wants to `reset` (abandon) the session.
  - ***`ACK (acknowledge) [.]`*** — This packet acknowledges that its sender has received data from the other endpoint
    - _Almost every packet ***except the first `SYN` will have the `ACK` flag set***_
  - ***`URG (urgent) [U]`*** — This packet contains data that needs to be delivered to the application out-of-order 
    - `Not used in HTTP or most other current applications`

> All hail !! `TCPDUMP`

#### TCP Three-way handshake

- The **first packet** sent to initiate a TCP session always has the `SYN` flag `set`
- This initial `SYN` packet is sent to the server _`to start opening`_ a ***`TCP connection`***`
  - This is the first packet with Flags `[S]`
  - This packet contains a new, `randomized sequence number` (`seq` in tcpdump output)

- If the server `accepts the connection`
  - it sends a packet back that has the `SYN` and `ACK` flags, and acknowledges the initial SYN
  - This is the second packet in the sample data, with Flags `[S.]`
  - This contains a different `initial sequence number`

> NOTE : If the server doesn't want to accept the connection, it may not send anything at all. Or it may send a packet with the `RST` flag

- The client ***acknowledges*** receiving the `SYN|ACK [S.]` packet by sending an `ACK[.]` packet of its own

- ***This exchange of three packets is usually called the `TCP three-way handshake`***
  - The two endpoints also exchange other information used to set up the connection

#### Four-way teardown

- When either endpoint is done sending data into the connection
  - it can send a `FIN [F.]` packet to indicate that it is finished
  - The other endpoint will send an `ACK [.]` to indicate that it has received the `FIN [F.]`
- Eventually the other endpoint will be done sending as well, and  
  - will send a `FIN [F.]` of its own
  - Then the first endpoint will send an `ACK [.]`

> In between . In a long-running connection, there will be many packets exchanged back and forth. Some of them will contain application data; others may be only acknowledgments with no data (length 0). However, all TCP packets in a connection except the initial SYN will contain an acknowledgment of all the data that the sender has received so far. Therefore, they will all have the ACK flag set. (This is why tcpdump depicts the ACK flag with just a dot: it's really common.)

#### ICMP and UDP don't have TCP flags

- If you look at tcpdump data for pings or basic DNS lookups, you will not see flags.
- because ping uses `ICMP`, and `basic DNS lookups use UDP`. These protocols **do not have TCP flags** or ***sequence numbers***

#### why do packets drop ? :(

- Two Networks with different link speeds are connected over Internet
  - causes buffer issues at the low speed link network - eventually packets are dropped
- TCP to address this problem
  - TCP starts slow transmission
  - Checks if `ack` from the slow end is able to keep up with the pace
  - `Routers` drops the packets if the link they are trying to send the data on is too busy
  - if the `packets are dropped`
    - TCP slows down and gradually speeds up again
- Endpoints and routers collabrate to send data at optimal speed

### TCP Congestion Control

- [WIKI](https://en.wikipedia.org/wiki/TCP_congestion_control)

### TCP Timeouts

- TCP has in-built timers
  - If server takes a long time to connect, TCP abandons the attempt to make a connection and sends an error to the requesting application

## ***BIG Networks***

- `traceroute google.com`

 ```javascript
traceroute to google.com (216.58.197.78), 64 hops max, 52 byte packets
 1  192.168.2.1 (192.168.2.1)  1.965 ms  2.608 ms  1.714 ms
 2  172.16.17.1 (172.16.17.1)  8.216 ms  3.102 ms  3.268 ms
 3  103.5.134.73 (103.5.134.73)  4.148 ms *  4.971 ms
 4  182.74.195.161 (182.74.195.161)  6.904 ms  4.468 ms  5.725 ms
 5  182.79.198.22 (182.79.198.22)  10.638 ms
    182.79.198.2 (182.79.198.2)  17.607 ms
    182.79.142.220 (182.79.142.220)  10.609 ms
 6  72.14.211.198 (72.14.211.198)  10.765 ms  12.849 ms  18.322 ms
 7  108.170.253.97 (108.170.253.97)  13.413 ms  12.116 ms  14.458 ms
 8  108.170.236.197 (108.170.236.197)  14.056 ms
    108.170.237.95 (108.170.237.95)  12.216 ms
    108.170.236.197 (108.170.236.197)  13.749 ms
 9  maa03s21-in-f78.1e100.net (216.58.197.78)  12.215 ms  13.565 ms  12.047 ms
 ```

- `maa03s21-in-f78.1e100.net (216.58.197.78)  12.215 ms  13.565 ms  12.047 ms` - the `maa` is the International airport code for Chennai
- Network-Operators name large nodes after nearby Intl-Airports, helps keep track of the node Geographically
- `Roundtrip-time` is the measure of the closeness on the network
- _How Traceroute works_
  - Every TCP packet has a TTL field (Time To Live)
  - TTL starts with a Large number (ramdom - 22590) and reduced by 1 each time it hits a router on its way to the destination machine
  - Prevents a Packet to travel in loops
    - If a packet's TTL expires at a Router, that Router sends a error Message back to the packet's original sender
    - The error Message says that packet's TTL expired and the address of the Router where the packet died
  - TraceRoute uses this mechanism to construct the route to the Destination
    - It start with TTL0 and checks which router at hop - 1 the packet expires
    - Then TTL1 to get the second router and so on so forth to ultimately let the the packet die at the destination
    - Traceroute keeps increasing the TTL incrementally to achieve the above said feat ;) clever
    - Traceroute gives an approximation

### Bandwidth Across Networks

- Capacity of a Network / Link / Multi link to carry data is Bits/Sec called the BandWidth
- Usually Server Networks are fast and User Networks are slow
  - updating Server Network will not improve the speed the users see
  - However, in a Situation where there are many Simultaneous Slow Users
    - All the simultaneous requests coming from low-bandwidth will add up
    - The SUM(slows) > fast Bandwidth - this equation demands Server Network to be fast2
    - We can think of `∑ Bits-per-second ask from the user` ≈ `Bits-per-Second reply from Server Network`

#### Bandwidth and Latency

- `Latency` - How much time it takes to get the first Response Byte
- `Bandwidth` - How much time it took to get all the Bytes of the Response after the first Byte
- ***Bandwidth Delay Product*** - `Bandwidth.Latency = Amount of Data in Transit` **but not yet received**

> KLINGON TRANSMISSION : ∏Ø ≥≤µ˜∫√ç≈æ…“‘≠–ºª•¶§∞¢£™¡œ∑´®††¥¨ˆøøπ¬˚∆˙©ƒ∂ßå*-++/


## Middleboxes : Firewalls & Filtering

- Firewalls are devices used to filter traffic that's coming into or leaving their network 
- A Firewall belongs to a class of network devices called Middleboxes

> ***Middleboxes — devices that inspect, modify, or filter network traffic.***
> ***Examples - `intrusion detection systems` and `load balancers`.***

- Technically, it's a `middle-box` if it's a `separate device` from the `client` or `server`
- server-side "firewalls" like `Linux iptables` aren't `middleboxes`.

- Common configuration for a firewall is to drop any incoming traffic ***`except traffic to (host, port) pairs that are supposed to be receiving connections from the Internet`***
  - _This prevents other machines on the network — like backend databases or administrative systems — to get direct attacks from outside_

### ISSUES WITH FIRE WALLS 🔥🔥

- If you're trying to test or deploy a network app and there's a firewall between your server and the user, that firewall can potentially interfere with your app or block it completely.
  - To deploy an application on a particular server and port, it helps to know what kind of firewall might be between you and your user
  - ***One of the reasons that many non-Web applications use HTTP as a transport is that HTTP is often unblocked at firewalls even when other ports are blocked***

- `Middleboxes` can also alter traffic
  - Replacing web pages with error messages
  - Mistakes - filters that try to block rude words, but end up blocking or replacing innocuous words that contain a rude word as a substring.

> HOST AND DIG only het info from the DNS not the actual site

## Middleboxes : Proxies & NAT

- With `NAT`, several devices can access Internet resources through a single public IP address
- `NAT` device uses port numbers to match up connections on the inside and outside
- `NAT` devices overlap with firewalls
  - Home routers can act as both a NAT and a simple firewall - ability to block or filter at a very basic level
  - At a larger scale, ISPs and other organizations have deployed `NAT devices` for their whole customer networks
    - This NAT is called [`carrier-grade NAT`](https://en.wikipedia.org/wiki/Carrier-grade_NAT)

- In the case of `NAT`, your web site can see requests from the same IP address that actually come from different users on different computers
  - `Assigning Session cookie or Login credentials can help differentiate between users (at HTTP LEVEL)`

- Whereas a `NAT` works at the `IP level`, `rewriting packets`, `a web proxy works at the HTTP level`, **taking queries from browsers and sending them out to web servers** 
  - Many organizations use web proxies for caching, including some ISPs 
- ***From the standpoint of a web developer or site operator, traffic from a busy proxy looks much the same as traffic from a busy NAT: queries for many users, on many actual computers, are funneled through a single public IP address***

[STORY WORTH READING](https://blog.chriszacharias.com/page-weight-matters)

> ***FINITO ;)***
