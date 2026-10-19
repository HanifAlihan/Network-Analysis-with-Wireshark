# Network-Analysis-with-Wireshark
Network traffic analysis using Wireshark on ubuntu 24.04 VM

# Project Overview
The purpose of this project is to get hands on experience on capturing and analyzing network traffic in an Ubuntu Linux environment by using Wireshark



# 1. Network Interface Selection
I opened Wireshark on the Ubuntu virtual machine and selected the enp0s3 network interface for capturing network traffic. 
<img width="926" height="702" alt="image" src="https://github.com/user-attachments/assets/cc74d052-e63e-486d-b188-2e101bb1cf42" />

# 2. Network Traffic Capture 
I captured network traffic on the enp0s3 network interface while going to the https://example.com website through Firefox.

Wireshark captured 1,149 packets with 0 packets dropped. Multiple protocols are shown such as DNS, TCP, and HTTP. I saved the capture as example-com-capture.pcapng so I can analyze the packets later without having to capture traffic again.
<img width="1338" height="811" alt="image" src="https://github.com/user-attachments/assets/556fbcfe-1401-47ae-b1ce-8aaa789b82e7" />

# 3. DNS Analysis
I used the dns display filter to see only DNS traffic. 

In packet 1111, my Ubuntu virtual machine (10.0.2.15) requested the IPv4 address of example.org from the DNS server (192.168.1.1). The DNS server responded in packet 1117 returning two IPv4 addresses 104.20.26.136 and 172.66.157.237, completing the DNS lookup in .02 seconds. 
The two IPv4 addresses returned means there are multiple possible destinations for connecting to example.org.
<img width="1272" height="768" alt="image" src="https://github.com/user-attachments/assets/b846d42a-c3c9-4065-9c8f-e3678a67b71c" />
<img width="1290" height="772" alt="image" src="https://github.com/user-attachments/assets/4758be2a-9769-48fe-be0d-048791d9b7fd" />

# 4. TCP Analysis 
I used the tcp filter at first to show the TCP traffic, then I right clicked packet 5, selected Follow TCP stream which filtered only the packets to this TCP connection. 

Packets 5-7 show the three way handshake with SYN, SYN-ACK, and then ACK. My VM requests a connection, the server acknowledges and responds, and my VM acknowledges the server. 

Packets 8-10 show the VM communicating with the server using HTTP. First the VM sends a HTTP GET request, the server acknowledges the request and responds with 200 OK showing the request was successful. 

Packets 11-14 show the connection closing with FIN-ACK, ACK, FIN-ACK, and then ACK. The server requests to close the connection, the VM acknowledges this, VM requests to close the connection, and then the server acknowledges this. 
<img width="1286" height="775" alt="image" src="https://github.com/user-attachments/assets/27d0abd7-9478-4c3a-b38c-93be5f787b88" />

# 5. HTTP Analysis
I used the http filter to show only the http traffic.

In packet 8 my VM sent an HTTP GET request to get /canonical.html from detectportal.firefox.com

In Packet 10 the server responded with HTTP/1.1 200 OK showing the request was successful. This response contains HTML content that is 90 bytes long.

**Packet 8 HTTP GET request**
<img width="1282" height="772" alt="image" src="https://github.com/user-attachments/assets/5fe20f62-546d-4026-899d-2d1715e9ca58" />

**Packet 10 HTTP response**
<img width="1282" height="768" alt="image" src="https://github.com/user-attachments/assets/c7b0d4af-7934-430f-a361-918cc6d8732f" />


# Findings and Conclusion 
