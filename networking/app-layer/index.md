# Application Layer

[Up to Overview](../index)

Recommended reading: Kurose chapter 2

- [Application Layer](#application-layer)
  - [Overview](#overview)
    - [Architectures](#architectures)
    - [Processes Communicating](#processes-communicating)
    - [App Layer Protocols](#app-layer-protocols)
  - [Socket Programming](#socket-programming)
    - [UDP Server](#udp-server)
    - [UDP Client](#udp-client)
    - [TCP](#tcp)
      - [TCP Server](#tcp-server)
      - [TCP Client](#tcp-client)
  - [Web and HTTP](#web-and-http)
    - [HTTP](#http)
      - [HTTP Request Message](#http-request-message)
      - [HTTP Response Message](#http-response-message)
      - [HTTP Status Codes](#http-status-codes)
      - [Demos](#demos)
  - [Domain Name System (DNS)](#domain-name-system-dns)

## Overview

![alt text](image.png)

- Raisons d'etre of networks/internet
- App programs on different hosts
- Programs communicate over network
- Examples
    - Web browsers and web servers
    - Netflix apps and Netflix servers
- Key point: No app program in network core!
  - Facilitated the explosion of apps

### Architectures

![alt text](image-1.png)

- App architecture designed by app developer
- Predominant paradigms
  - **client-server**
    - server: always-on host, listening
      - e.g. webservers for www.amazon.com in datacenters
    - client: makes requests to server
      - e.g. web browsers on user hosts
  - **peer-to-peer (P2P)** e.g. BitTorrent filesharing
    - Self-scalable BUT security, performance, reliability
    - From the trenches: BitTorrent support in AWS S3 proved problematic

### Processes Communicating

![alt text](image-2.png)

- **Process**: program on host
- Same-host processes use OS to communicate: inter-process-comm (IPC)
- Different-hosts processes exchange **messages** across the network
  - Sender/initiator: client process
  - Receiver: server process
  - (distinct from client-server architecture!)
- A process can act as both client and server in different conversations
- Client must identify the destination process. How?
- Address of the receiving host
  - **IP address**: 32-bit quantity (for now)
- Address of the process on the host
  - Port number
    - 16-bit integer 
    - e.g. 80 for a web server
    - well-known port numbers at www.iana.org
- Socket: interface to the Transport Layer to send/receive app messages
- Useful analogy
  - House = process
  - Door = socket
  - Transportation network between houses = network core
- Limited app control
  - Choice of transport protocol
  - Adjustable parameters like max segment size

### App Layer Protocols

- Defines how an app’s processes converse over the network:
  - What types of messages?
  - What’s the structure/syntax of each message?
  - What do the message fields mean?
  - What actions should processes take for each message?
- Protocol is just one, albeit important, piece of a distributed app
- Public-domain protocols specified in RFCs e.g. HTTP for the Web
- Proprietary protocols e.g. Skype's

![alt text](image-3.png)

[(Back to the top)](#application-layer)

---

## Socket Programming

### UDP Server

```py
# Import socket module
from socket import *

# Create server socket that speaks UDP (transport) and IPv4 (network)
server_socket = socket(family=AF_INET, type=SOCK_DGRAM)

# Bind to a specific port
port = 12000
server_socket.bind(('', port))

# Display server address.
print("Server running on %s:%d" % (gethostbyname(gethostname()), port))

# Keep reading any incoming messages on port.
resp = "ack\n".encode()
bufsize = 4096
while True:
    msg, client_addr = server_socket.recvfrom(bufsize)
    print("Received message from %s:\n  %s" % (client_addr, msg.decode()))
    server_socket.sendto(resp, client_addr)
```

- **Netcat**: commandline utility to read/write messages across network connections
  - Send UDP messages via "-u" flag
    - e.g. *nc -u <server_ip> <server_port>*
      - ctrl-c when done

### UDP Client

```py
from socket import *

server_ip = "192.168.0.18"
server_port = 12000
msg = "Hello from vish"

# Create client socket that speaks IPv4 (network) and UDP (transport)
client_socket = socket(family=AF_INET, type=SOCK_DGRAM)

# Send message to server
client_socket.sendto(msg.encode(), (server_ip, server_port))

# Receive response from server and print it
bufsize = 4096
resp = client_socket.recv(bufsize)
print(resp.decode())

# Close client socket
client_socket.close()
```

### TCP

![alt text](image-4.png)

- TCP is connection-oriented
  - client and server handshake to establish a client/server sockets pair
  - analogy: knock on the welcoming door and then get assigned own door
- no need to address individual packets

#### TCP Server

```py
# Import socket module
from socket import *

# Create server socket that speaks TCP (transport) and IPv4 (network)
server_socket = socket(family=AF_INET, type=SOCK_STREAM)

# Bind to a specific port
port = 12000
server_socket.bind(('', port))

# Listen for client knocking.
backlog = 10
server_socket.listen(backlog)

# Display server address.
print("Server running on %s:%d" % (gethostbyname(gethostname()), port))

# Keep accepting new connections
resp = "ack\n".encode()
bufsize = 4096
while True:
    connection_socket, addr = server_socket.accept()
    msg = connection_socket.recv(bufsize)
    print("Received message from %s:\n  %s" % (addr, msg.decode()))
    connection_socket.send(resp)
    connection_socket.close()
```

- Can use netcat without the -u flag to send TCP messages
- Can also use telnet

#### TCP Client

```py
from socket import *

server_ip = "192.168.0.18"
server_port = 12000
msg = "Hello from vish"

# Create client socket that speaks IPv4 (network) and TCP (transport)
client_socket = socket(family=AF_INET, type=SOCK_STREAM)

# Connect to server
client_socket.connect((server_ip, server_port))

# Send message to server
client_socket.send(msg.encode())

# Receive response from server and print it
bufsize = 4096
resp = client_socket.recv(bufsize)
print(resp.decode())

# Close client socket
client_socket.close()
```

[(Back to the top)](#application-layer)

---

## Web and HTTP

![alt text](image-5.png)

- 1990s
- Internet’s killer app that brought it to the masses
- Client: **web browser** e.g. chrome/safari/edge/firefox/etc.
  - requests docs of user’s choosing e.g. nytimes.com
- Server: **TCP server** listening on **port 80**
  - typically in org’s datacenter or cloud-hosted
- App protocol: HyperText Transfer Protocol (**HTTP**)

### HTTP

- Defined in RFCs 1945, 7230, 7540
- Spoken between web browsers and web servers
- **Web page**: base HTML file and several referenced objects
  - object: addressable by URL
    - HTML, JPEG, Javascript, CSS, video, …
  - **URL** e.g. http://www.lwtech.edu/admissions/campus-tour.aspx
    - hostname of web server: "www.lwtech.edu"
    - object’s path name: "/admissions/campus-tour.aspx"

#### HTTP Request Message

![alt text](image-6.png)

- Example

    GET /somedir/page.html HTTP/1.1 \
    Host: www.someschool.edu \
    Connection: close \
    User-agent: Mozilla/5.0 \
    Accept-language: fr 

- Human-readable ASCII text
- Methods: GET, POST, HEAD, PUT, DELETE, ...

#### HTTP Response Message

![alt text](image-7.png)

- Example

    HTTP/1.1 200 OK \
    Connection: close \
    Date: Tue, 18 August 2015 15:44:04 GMT \
    Server: Apache/2.2.3 (CentOS) \
    Last-Modified: Tue, 18 August 2015 15:11:03 GMT \
    Content-Length: 6821 \
    Content-Type: text/html \
    (data data …)

- Entity body contains the requested object

#### HTTP Status Codes

- IANA maintains official registry
  - 2xx successful e.g. 200 OK
  - 3xx redirect e.g. 301 Moved Permanently
  - 4xx client error e.g. 400 Bad Request or 404 Not Found
  - 5xx server error e.g. 503 Service Unavailable
- From the trenches: important to distinguish between 4xx and 5xx to operate a service

#### Demos

- Talk to Web Server

    % telnet www.google.com 80 \
    HEAD / HTTP/1.1 \
    Host: www.google.com \
    \
    HTTP/1.1 200 OK \
    Content-Type: text/html; charset=ISO-8859-1 \
    …

- Respond to browser
  - Run your own TCP server on port 80
    - Send simple HTTP response e.g. "HTTP/1.1 200 OK\n\nHello world"
  - Fetch http://localhost/ using a browser of your choice

[(Back to the top)](#application-layer)

---

## Domain Name System (DNS)