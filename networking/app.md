
Application Layer
Recommended reading: Kurose chapter 2
Overview
Raisons D’etre of Networks
Overview
App programs on different hosts
Programs communicate over network
Examples
Web browsers and web servers
Netflix apps and Netflix servers
Key point: No app program in network core!
Facilitated the explosion of apps
Architectures
App architecture designed by app developer
Predominant paradigms
client-server
server: always-on host, listening
e.g. webservers for www.amazon.com in datacenters
client: makes requests to server
e.g. web browsers on user hosts
peer-to-peer (P2P) e.g. BitTorrent filesharing
Self-scalable BUT security, performance, reliability
From the trenches: BitTorrent support in AWS S3 proved problematic
Processes Communicating
Process: program on host
Same-host processes use OS to communicate: inter-process-comm (IPC)
Different-hosts processes exchange messages across the network
Sender/initiator: client process
Receiver: server process
(distinct from client-server architecture!)
A process can act as both client and server in different conversations
Addressing Processes
Client must identify the destination process. How?
Address of the receiving host
IP address: 32-bit quantity (for now)
Address of the process on the host
Port number
16-bit integer 
e.g. 80 for a web server
well-known port numbers at www.iana.org
Socket Interface
Socket: interface to the Transport Layer to send/receive app messages
Useful analogy
House = process
Door = socket
Transportation network between houses = network core
Limited app control
Choice of transport protocol
Adjustable parameters like max segment size
Transport Requirements
Internet’s Transport
TCP
Connection-oriented (full duplex)
Reliable
no missing/duplicate/unordered bytes
Congestion control
Flow control
Internet’s Transport
TLS (Transport Layer Security)
TCP enhanced with
encryption
data integrity
end-point authentication
Not strictly a transport protocol but rather app-layer enhancement of TCP
No Bandwidth/Latency Guarantees!?
Neither TCP nor UDP provide any throughput/latency guarantees
Still, latency-sensitive apps (e.g. VoIP) and bandwidth-sensitive apps (e.g. video streaming) thrive
Apps designed to be resilient to bandwidth/latency issues
To an extent
Works well, except when it doesn’t => outages/degradation
App Layer Protocols
Defines how an app’s processes converse over the network:
What types of messages?
What’s the structure/syntax of each message?
What do the message fields mean?
What actions should processes take for each message?
Protocol is just one, albeit important, piece of a distributed app
App Layer Protocols
Public-domain protocols specified in RFCs e.g. HTTP for the Web
Proprietary protocols e.g. Skype's
Socket Programming
UDP Server
import socket  # socket module
server_socket = socket
Demo
I’ll run a server
You send messages to it
Using “netcat”: command line utility to read/write messages across network connections
e.g. nc - u <server_ip> <server_port>
ctrl-c when done
UDP Client
import socket  # socket module
server_socket = socket
Demo
I’ll run the server
You send messages to it
Using your own UDP client!
Using TCP
TCP is connection-oriented
client and server handshake to establish a client/server sockets pair
analogy: knock on the welcoming door and then get assigned own door
no need to address individual packets
TCP Server
import socket  # socket module
server_socket = socket
Demo
I’ll run a TCP server
You send messages to it
Using “netcat”: command line utility to read/write messages across network connections
e.g. nc <server_ip> <server_port>
ctrl-c when done
TCP Client
import socket  # socket module
server_socket = socket
Demo
I’ll run a TCP server
You send messages to it
Using your own TCP client!
Web and HTTP
World Wide Web
1990s
Internet’s killer app that brought it to the masses
Client: web browser e.g. chrome/safari/edge/firefox/etc.
requests docs of user’s choosing e.g. nytimes.com
Server: TCP server listening on port 80
typically in org’s datacenter or cloud-hosted
App protocol: HyperText Transfer Protocol (HTTP)
HTTP: Overview
Defined in RFCs 1945, 7230, 7540
Spoken between web browsers and web servers
Web page: base HTML file and several referenced objects
object: addressable by URL
HTML, JPEG, Javascript, CSS, video, …
URL e.g. http://www.lwtech.edu/admissions/campus-tour.aspx
hostname of web server
object’s path name
HTTP Request Message
Example
GET /somedir/page.html HTTP/1.1
Host: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: fr
Human-readable ASCII text
Methods
GET, POST, HEAD, PUT, DELETE
HTTP Response Message
Example
HTTP/1.1 200 OK
Connection: close
Date: Tue, 18 August 2015 15:44:04 GMT
Server: Apache/2.2.3 (CentOS)
Last-Modified: Tue, 18 August 2015 15:11:03 GMT
Content-Length: 6821
Content-Type: text/html
(data data …)
Entity body contains the requested object
HTTP Status Codes
IANA maintains official registry
2xx successful e.g. 200 OK
3xx redirect e.g. 301 Moved Permanently
4xx client error e.g. 400 Bad Request or 404 Not Found
5xx server error e.g. 503 Service Unavailable
From the trenches: important to distinguish between 4xx and 5xx to operate a service
Demo: Talk To Web Server
Demo: Respond To Browser
Run your own TCP server on port 80
Have it respond with a simple HTTP response with simple text body, regardless of request method
e.g. "HTTP/1.1 200 OK\n\nHello world"
Fetch http://<ip>/ using a browser of your choice
HTTP Interactions
Interactions when user clicks a hyperlink
Takes 2 RTTs (round-trip-time)
RTT: Duration of packet from client to server and back
Includes delays (processing, queueing, transmission, propagation)
Persistent connections in HTTP/1.1 to allow for subsequent HTTP requests to reuse the same connection
Default mode: persistent w/ pipelining
More HTTP
Stateless protocol
HTTP server maintains no info on clients
BUT cookies mechanism to have clients store and echo state
HTTP versions
HTTP/1.0 : original version from the 1990s [RFC 1945]
HTTP/1.1 : popular version today [RFC 7230]
persistent connections
HTTP/2 : [RFC 7540]
site performance and server efficiency
HTTP/3 : not yet standardized
Networking Project

DNS
(Domain Name System)
DNS: Overview
Host IP addresses are hard to remember e.g. 142.251.33.68
Hostname: mnemonic that maps to an IP address
e.g. www.google.com -> 142.251.33.68
Who does the mapping? DNS
Distributed database implemented in a hierarchy of DNS servers
App layer protocol between hosts and DNS servers
An internet service implemented at the application layer
Example: Browser as DNS client
Browser requests URL www.someschool.edu/index.html
Browser extracts hostname from URL and queries the DNS client running on the same machine
DNS client sends query to a remote DNS server
DNS client eventually receives reply containing IP address for hostname
Browser initiates TCP connection to the server process on port 80 at the IP address 
DNS Services
Hostname to IP address translation
Host aliasing
canonical hostname e.g. relay1.west-coast.enterprise.com
alias hostnames e.g. enterprise.com and www.enterprise.com 
Mail server aliasing e.g. johndoe@gmail.com is an alias for a more complicated gmail hostname
Load distribution and availability
Hostname maps to a set of IPs
This is critical and goes way beyond mere convenience!
How DNS Does Not Work
One DNS server that contains all the mappings?!
Simple, BUT doesn’t scale
Single point of failure for the entire internet
Can’t handle internet’s traffic volume
Can’t be close to clients so long delays
Too much data
Too many updates when hosts change
Distributed Hierarchical Database
Hierarchy of DNS servers distributed around the world
Each server maintains a subset of the mapping
Mainly three-level hierarchy
Root: provide IPs of top-level DNS
~1,000 globally, managed by 12 organizations, coordinated through IANA
Top-level domain (TLD) servers: provides IPs of authoritative servers
e.g. com, org, net, edu, gov, uk, fr, ca, jp, …
Verisign maintains com, Educause maintains edu, …
Authoritative DNS servers: provides IPs of publicly addressable hosts (web servers, mail servers, etc.)
Each organization has its own authoritative DNS, either in-house or via a service provider
DNS Root Servers in 2020
Distributed Hierarchical Database
Life of a DNS query
www.amazon.com -> IP address?
Root DNS returns IP of com DNS
com DNS returns IP of amazon.com DNS
amazon.com DNS returns IP of www.amazon.com 
Local DNS Server
aka default name server
Each ISP provides a local DNS which hosts connect to first
acts as proxy to the DNS hierarchy
Example Lookup
Recursive query
e.g. to local name server
Iterative queries
e.g. to the hierarchical DNS
In theory, any DNS query can be iterative or recursive
iterative is more scalable, especially higher up the tree
DNS Caching
Each DNS server caches results of queries to other DNS servers in its local memory
Can serve queries from cache, even if not authoritative
TTL (time-to-live) typically ~ 2 days
Effect: root DNS bypassed for almost all DNS queries
DNS Records
Resource Records (RRs): records stored in the DNS distributed database
Each reply contains 1 or more RRs
Four-tuple: (Name, Value, Type, TTL)
Type=A => Name=hostname, Value=IP
Type=NS => Name=domain (e.g. foo.com), Value=hostname of authoritative DNS
Type=CNAME => Name=alias, Value=canoncial
Type=MX => Name=alias, Value=canonical mail server
DNS Messages
Types
Query and Reply
Format (same for both query and reply)
Header (12 bytes)
Identification (2 bytes): id of query and reply
Flags (2 bytes)
query or reply
Is authoritative server
Is recursion desired
Is recursion supported
4 Numbers fields describing the data sections
DNS Messages
Questions section (present in Query)
Name and Type
e.g. www.google.com, A
Answers section (present in Reply)
RRs for the questions
Possibly multiple RRs for the same question (e.g. if hostname maps to several IPs)
DNS Messages
Authority section
Records of other authoritative servers
Additional section 
e.g. Answer section: MX record for mail server name, Additional section: A record for mail server IP
Demo: nslookup (1)
Commandline tool to query any DNS server
Find the IP address of a host
e.g. nslookup www.mit.edu
Sends query to local name server (address in reply), which may respond from cache (non-authoritative) or by iteratively querying other name servers 
Demo: nslookup (2)
Find the hostname of the authoritative DNS server for a domain
e.g. nslookup -type=NS mit.edu 
Sends query to local name server (address in reply), which may respond from cache (non-authoritative) or by iteratively querying other name servers 
(When -type options is not specified, it defaults to ‘A’)
Demo: nslookup (3)
Find the IP of a hostname from its authoritative DNS server
e.g. nslookup www.mit.edu ns1-37.akam.net
Sends query to ns1-37.akam.net DNS server
Tale From The Trenches
Amazon S3 DNS “hack”
Objects (blobs) stored with <bucket>/<object> naming scheme
Each <bucket> located in a particular datacenter
User in Tokyo reading a bucket in Seattle
DNS lookup for <bucket>.aws.s3.com returns an IP in Japan
Request enters Amazon’s network in Japan (frontend server)
Amazon’s network used to transfer data from Seattle to Tokyo
Tale From The Trenches
Goal: route user request to the datacenter where their data is
How? build an S3 DNS server
Insert DNS record for each bucket to map to IPs in local datacenter
Then <bucket>.aws.s3.com resolves to an IP for a host in the target datacenter
Approach
Computer Networks and the Internet
Top-down networking stack
Application Layer
Transport Layer
Network Layer
Transport Layer
Recommended reading: Kurose chapter 3
Transport Overview
Transport Overview
Logical communication between app processes on different hosts
Only on end hosts, not in network core
Transport Overview
Process-to-process channel (logical) 
Only on the edge (hosts)
not in the core (routers)
Multiplexing and Demultiplexing
Sender side
Receives app message via socket
Wraps message in transport-layer segments
wrap = prefix transport header
Passes to network layer for delivery to host: multiplexing
Receiver side
Receives segment from network layer
Unwraps app message
Delivers to app via socket (based on header): demultiplexing
Multiplexing and Demultiplexing
Sender side multiplexing
App-layer message via 1 of N sockets -> 1 network-layer datagram
Encapsulation
wrap via prefixing header
message -> segment -> datagram
Receiver side demultiplexing
1 network-layer datagram -> app-layer message via 1 of N sockets
De-encapsulation
unwrap via removing header prefix
datagram -> segment -> message
Useful Analogy
Two houses, one on East Coast and one on West Coast
Each house has a dozen kids
Kids in one house are cousins of kids in other house
Ever week, each kid writes a letter to every one of their cousins
Ann (West Coast) and Bill (East Coast) collect letters and hand off to postal-service mail-carrier (and also deliver incoming letters to siblings)
Internet’s Transports
UDP (User Datagram Protocol)
Unreliable, connectionless (Ann/Bill careless)
TCP (Transmission Control Protocol)
Reliable, connection-oriented (Ann/Bill careful)
UDP
UDP
Defined in RFC 768
8 bytes header
Dest. port for receiver-side demultiplexing
(dest. IP, dest. port) -> Receiver socket
Source port for receiver to reply to the sender (swapping src/dst ports aka “port inversion”)
Length: # bytes in segment (header + data)
Checksum: detect corruption/errors
UDP
Defined in RFC 768
8 bytes header
Dest. port for receiver-side demultiplexing
(dest. IP, dest. port) -> Receiver socket
Source port for receiver to reply to the sender (swapping src/dst ports aka “port inversion”)
Length: # bytes in segment (header + data)
Checksum: detect corruption/errors
Port Inversion
Layering
Why UDP?
Low latency (no connection establishment)
reason why DNS uses UDP
App control over when data sent
No congestion control
Handle more connections
No connection state overhead
BUT typically need non-trivial app logic to address unreliability
UDP on Wireshark
TCP
TCP
Defined in RFCs 793, 1122, 2018, 5681, 7323
Src & dst ports, and checksum (ala UDP)
Sequence & Acknowledgement numbers (for reliable data transfer)
Receive window (for flow control)
Header length (20 bytes w/o options)
Options (variable length) (e.g. negotiate MSS or window scaling factor
6 flag bits (details later)
TCP
Defined in RFCs 793, 1122, 2018, 5681, 7323
Src & dst ports, len, and checksum (ala UDP)
Sequence & Acknowledgement numbers (for reliable data transfer)
Receive window (for flow control)
Header length (20 bytes w/o options) (c.f. 8 bytes for UDP)
Options (variable length) (e.g. negotiate MSS or window scaling factor)
6 flag bits (details later)
Layering
TCP Mux/Demux
Example
Host C has 2 HTTP sessions with host B
Host A has 1 HTTP session with host B
TCP “listening socket” accepts connection and creates a connection-socket
Connection socket identified by four-tuple: (src IP, src port, dst IP, dst port)
c.f. UDP two-tuple (dst IP, dst port)
Src port is used for demux in addition to port inversion in replies
TCP Mux/Demux
Example
Host C has 2 HTTP sessions with host B
Host A has 1 HTTP session with host B
TCP “listening socket” accepts connection and creates a connection-socket
Connection socket identified by four-tuple: (src IP, src port, dst IP, dst port)
c.f. UDP two-tuple (dst IP, dst port)
Src port is used for demux in addition to port inversion in replies
Tale From The Trenches
Amazon’s S3 outage in 2008
Single host with a corrupting network interface that eluded TCP checksums!
S3 servers “gossiped” about the state of S3 servers
The one misinformation spreader prevented gossip from converging to the actual state
The entire system had to be shutdown and brought back up
Lesson: End-to-end integrity checks are critical!
TCP Connection
Three-way handshake
SYN segment
SYN + random initial seq num
SYNACK segment
Allocates buffers/variables
SYN + random initial seq num + ack
ACK segment
Allocates buffers/variables
next seq num (w/ piggybacked payload) + ack
1 RTT for connection establishment
TCP Connection
Three-way handshake
Client sends SYN segment (synchronize)
SYN + random initial-seq-num (isn)
1st byte of sent data will have seq-num (sn) = isn+1
Server responds with SYNACK segment
Allocates buffers/variables
SYN + random isn + ack
Ack sn = sn of byte the server expects to receive next
ACK segment
Allocates buffers/variables
next seq num (w/ piggybacked payload) + ack
1 RTT for connection establishment
SYN (Wireshark)
SYNACK (Wireshark)
ACK (Wireshark)
TCP Buffers
TCP buffers data on send/recv hosts
Buffered data transmitted in segments
MSS: max app data in segment
Not max segment size, despite the name!
Based on link-layer MTU (max transmission unit)
1500 bytes for Ethernet
40 bytes of TCP/IP headers => 1460 MSS
Spec: “send that data in segments at its own convenience”
TCP Buffers
TCP buffers data on send/recv hosts
Buffered data transmitted in segments
MSS: max app data in segment
Not max segment size, despite the name!
Based on link-layer MTU (max transmission unit)
1500 bytes for Ethernet
40 bytes of TCP/IP headers => 1460 MSS
Spec: “send data in segments at its own convenience”
TCP Reliability
Guarantees
No lost data
No duplicate data
No out-of-order data
How?
Sequence numbers
Acknowledgement numbers
Retransmits
Sequence Numbers
Data = implicitly ordered stream of bytes
Sequence number for segment = byte number of 1st data byte in segment (base initial-seq-num + 1)
Example: message of 500,000 bytes, MSS of 1,000 bytes
Segment 1: seq-num offset 0
Segment 2: seq-num offset 1,000
… for 500 segments
Random initial sequence number: protects against latent segments from a previous connection incarnation
Sequence Numbers
Data = implicitly ordered stream of bytes
Sequence number for segment = byte number of 1st data byte in segment (base initial-seq-num + 1)
Example: message of 500,000 bytes, MSS of 1,000 bytes
Segment 1: seq-num offset 0
Segment 2: seq-num offset 1,000
… for 500 segments
Random initial sequence number: protects against latent segments from a previous connection incarnation
Acknowledgment Numbers
Set by the receiver of data
Sequence number of the next data byte the receiver expects from the sender
All data bytes up to that number have been successfully received
cumulative acknowledgement
Handling out of order segments is left up to the implementation (discard or buffer)
Example echo server
Acknowledgment Numbers
Example: echo server
Set by the receiver of data
Sequence number of the next data byte the receiver expects from the sender
All data bytes up to that number have been successfully received
cumulative acknowledgement
Handling out of order segments is left up to the implementation (discard or buffer)
Retransmits
Retransmit segment if not acked within timeout
Timeout = estimated-RTT + 4 x deviation-RTT
Retransmits
Retransmit segment if not acked within timeout
Timeout = estimated-RTT + 4 x deviation-RTT
estimated-RTT = exponentially-weighted-moving-average (EWMA)
Resiliency against segment losses
Flow Control
Prevent sender from overflowing receive buffer
Distinct from congestion control
Receiver tracks receive window (rwnd) and includes it in every segment it sends to sender
Sender keeps unacked bytes below rwnd
Flow Control
Prevent sender from overflowing receive buffer
Distinct from congestion control
Receiver tracks receive window (rwnd) and includes it in every segment it sends to sender
Sender keeps unacked bytes below rwnd
Window Scaling
16 bits rwnd => 64 Kilobytes of unacked data
Bandwidth-delay product of a link
How much data is unacked in link
Long Fat Network (LFN)
e.g. 10 Mbit/s link with 100 ms propagation RTT
=> 1 Mbit or ~125 KB unacked in link
=> max of 64 KB rwnd too small!
Congestion Control: Principles
How does sender limit rate?
cwnd: congestion window
unacked-bytes <= min{cwnd, rwnd}
send rate ~ wnd/RTT bytes/sec
How does sender detect congestion?
Loss events
timeout for ack-wait
duplicate acks (when receiving out-of-order segments)
Slow Start
Start of TCP connection: cwnd = 1 MSS
Every ack => cwnd = cwnd + 1 MSS
Exponential increase: doubles every RTT
cwnd (in MSS) = 1, 2, 4, 8, … 
When cwnd >= ssthresh
Transition to congestion-avoidance
Loss event
restart slow start
ssthresh = cwnd / 2
Slow Start
Start of TCP connection: cwnd = 1 MSS
Every ack => cwnd = cwnd + 1 MSS
Exponential increase: doubles every RTT
cwnd (in MSS) = 1, 2, 4, 8, … 
When cwnd >= ssthresh (slow-start-threshold)
Transition to congestion-avoidance
Loss event
restart slow start
ssthresh = cwnd / 2
Tale From The Trenches
Google early days
Low latency for web apps
Otherwise users will not adopt
HTTP response for www.google.com optimized to fit in 1 MSS!
1 RTT response time
Can’t do better than that
Congestion Avoidance
Additive increase rather than exponential increase
Every ack: cwnd = cwnd + MSS / cwnd 
=> Every RTT: cwnd = cwnd + 1 MSS
c.f. Every ack: cwnd = cwnd + 1 MSS => every RTT: cwnd = cwnd * 2
Multiplicative decrease on loss event
cwnd = cwnd / 2
AIMD (Additive Increase Multiplicative Decrease
sawtooth behavior of cwnd
From the trenches: AIMD in app-layer congestion avoidance in Google’s cache-invalidation system!
TCP Disconnection
Either side can end the connection
Resources (buffers/variables) released
Example: client initiates close
Client closes only after
ACK received from server
FIN received from server
Wait time elapsed e.g. to resend a lost ack
TCP Disconnection
Either side can end the connection
Resources (buffers/variables) released
Example: client initiates close
Client closes only after
ACK received from server
FIN received from server
Wait time elapsed e.g. to resend a lost ack
TCP Client States
TCP Server States
TCP on Wireshark
Approach
Computer Networks and the Internet
Top-down networking stack
Application Layer
Transport Layer
Network Layer
Network Layer: Data Plane
Recommended reading: Kurose chapter 4
