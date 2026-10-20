# Network Analysis With Wireshark
Network traffic analysis using Wireshark on ubuntu 24.04 VM

## Table of Contents

- [Network Interface Selection](#1-network-interface-selection)
- [Network Traffic Capture](#2-network-traffic-capture)
- [DNS Analysis](#3-dns-analysis)
- [TCP Analysis](#4-tcp-analysis)
- [HTTP Analysis](#5-http-analysis)
- [TLS Analysis](#6-tls-analysis-encrypted-http2-traffic)
- [Findings and Conclusion](#7-findings-and-conclusion)

# Project Overview
The purpose of this project is to get hands on experience on capturing and analyzing network traffic in an Ubuntu Linux environment by using Wireshark. I will be analyzing protocols such as DNS, TCP, HTTP, and TLS. 

**Tools And Environment
Versions used**: Wireshark 4.2.2, Ubuntu 24.04, 

VirtualBox, Firefox

## 1. Network Interface Selection
I opened Wireshark on the Ubuntu virtual machine and selected the enp0s3 network interface for capturing network traffic. 
<img width="926" height="702" alt="image" src="https://github.com/user-attachments/assets/cc74d052-e63e-486d-b188-2e101bb1cf42" />

## 2. Network Traffic Capture 
I captured network traffic on the enp0s3 network interface while going to the https://example.com website through Firefox.

Wireshark captured 1,149 packets with 0 packets dropped. Multiple protocols are shown such as DNS, TCP, and HTTP. I saved the capture as example-com-capture.pcapng so I can analyze the packets later without having to capture traffic again.
<img width="1338" height="811" alt="image" src="https://github.com/user-attachments/assets/556fbcfe-1401-47ae-b1ce-8aaa789b82e7" />

This capture also includes background traffic from Firefox, so not all the captured packets I will be analyzing will be directly related to visiting example.com 

## 3. DNS Analysis
I used the dns display filter to see only DNS traffic. 

In packet 1111, my Ubuntu virtual machine (10.0.2.15) requested the IPv4 address of example.org from the DNS server (192.168.1.1). The DNS server responded in packet 1117 returning two IPv4 addresses 104.20.26.136 and 172.66.157.237, completing the DNS lookup in .02 seconds. 
The two IPv4 addresses returned means there are multiple possible destinations for connecting to example.org.

**Packet 1111 Request**
<img width="1272" height="768" alt="image" src="https://github.com/user-attachments/assets/b846d42a-c3c9-4065-9c8f-e3678a67b71c" />

**Packet 1117 DNS Response**
<img width="1290" height="772" alt="image" src="https://github.com/user-attachments/assets/4758be2a-9769-48fe-be0d-048791d9b7fd" />

**Clarification:** For this capture I visited the example.com website, but didn't see a DNS query for that domain in the capture. This is probably because I visited the example.com website already before doing it again while capturing it with Wireshark. Doing this let my VM use a result from the DNS cache. Because of this, I looked at a DNS query and response for the domain example.org.

## 4. TCP Analysis 
I used the tcp display filter at first to show the TCP traffic, then I right clicked packet 5, selected Follow TCP stream which filtered only the packets to this TCP connection. 

Packets 5-7 show the three-way handshake with SYN, SYN-ACK, and then ACK. My VM (10.0.2.15) requests a connection, the server (199.232.89.91) acknowledges and responds, and my VM acknowledges the server. 

Packets 8-10 show the VM communicating with the server using HTTP. First the VM sends a HTTP GET request, the server acknowledges the request and responds with 200 OK showing the request was successful. 

Packets 11-14 show the connection closing with FIN-ACK, ACK, FIN-ACK, and then ACK. The server requests to close the connection, the VM acknowledges this, VM requests to close the connection, and then the server acknowledges this. 
<img width="1286" height="775" alt="image" src="https://github.com/user-attachments/assets/27d0abd7-9478-4c3a-b38c-93be5f787b88" />

## 5. HTTP Analysis
I used the http display filter to show only the http traffic.

In packet 8 Firefox on my VM sent an HTTP GET request to get /canonical.html from detectportal.firefox.com. 

In Packet 10 the server responded with HTTP/1.1 200 OK showing the request was successful. This response had a content type of text/html and content length of 90 bytes. 

This HTTP communication uses port 80, the port for unencrypted HTTP. Since the HTTP content is unencrypted I can see its content through Wireshark. 

**Packet 8 HTTP GET request**
<img width="1282" height="772" alt="image" src="https://github.com/user-attachments/assets/5fe20f62-546d-4026-899d-2d1715e9ca58" />

**Packet 10 HTTP response**
<img width="1282" height="768" alt="image" src="https://github.com/user-attachments/assets/c7b0d4af-7934-430f-a361-918cc6d8732f" />

## 6. TLS Analysis (Encrypted HTTP/2 Traffic)
I used the tls display filter to look at the TLS traffic to see encrypted network traffic.

In Packet 47 my VM sent a client hello which offered 14 cipher suites. 

In packet 51 the server responded with a server hello selecting TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 using TLS 1.2.

In Packet 73 the server sent HTTP/2 application data that was encrypted to my VM using port 443, the port for HTTPS. Since the application data was encrypted with TLS, you cannot read its contents through Wireshark without decryption.

**Packet 47 Client Hello**
<img width="1272" height="772" alt="image" src="https://github.com/user-attachments/assets/005b440e-4bab-4b26-bc9b-9d590a784803" />

**Packet 51 Server Hello**
<img width="1282" height="771" alt="image" src="https://github.com/user-attachments/assets/9d525608-87c8-424d-a4c9-d7041b8e96ca" />


**Packet 73 Encrypted Application Data**
<img width="1273" height="772" alt="image" src="https://github.com/user-attachments/assets/e19ecbbe-2434-45c1-8ff5-7500d9f06fb8" />

## 7. Findings and Conclusion 
**Through the use of Wireshark, I was able to capture network traffic and see how network protocols DNS, TCP, HTTP, and TLS work together**

**DNS: I observed my VM sending a DNS query to resolve a domain name into an IP address and receiving 2 IPv4 addresses from the DNS server.**

**TCP: I observed the three way handshake (SYN, SYN-ACK, ACK) that is used to establish a connection between my VM and the server.**

**HTTP: I observed an HTTP GET Request and a 200 OK response through port 80. This communication is unencrypted so I could see its content through Wireshark**

**TLS: I observed the TLS handshake with Client Hello and Server Hello and the cipher suite that was agreed upon. I also observed encrypted HTTP/2 application data that was transmitted through port 443. Because the data was encrypted I wasn't able to see its content like I was through port 80 HTTP**

In conclusion this network traffic project helped me get a better understanding of how the network protocols work together for what might seem like just a simple exchange such as opening up Firefox and opening up a website on it. Concepts I have heard before such as the TCP three-way handshaker and TLS handshake being able to see happen right from my activity on my VM really helped me understand them more. An important concept I also learned from this was the role of TLS with encrypting HTTP communication compared to unencrypted HTTP communication.


