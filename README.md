<h1>Analyzing Network Attacks</h1>

<h2>Description</h2>
In this project, I will consider a scenario involving a customer of the company I work for who experiences a security issue when accessing the company’s website. The task will consist of identifying the likely cause of the service interruption and then explaining how the attack occurred and the negative impact it had on the website. This will be accomplished through analysis of Wireshark Logs.

<h2>Scenario</h2>
I work as a security analyst for a travel agency that advertises sales and promotions on the company’s website. The employees of the company regularly access the company’s sales webpage to search for vacation packages their customers might like. 
<br />
<br />
One afternoon, I receive an automated alert from my monitoring system indicating a problem with the web server. I attempt to visit the company’s website, but I receive a connection timeout error message in my browser.
<br />
<br />
I use a packet sniffer (Wireshark) to capture data packets in transit to and from the web server. I notice a large number of TCP SYN requests coming from an unfamiliar IP address. The web server appears to be overwhelmed by the volume of incoming traffic and is losing its ability to respond to the abnormally large number of SYN requests. I suspect the server is under attack by a malicious actor. 
<br />
<br />
I take the server offline temporarily so that the machine can recover and return to a normal operating status. I also configure the company’s firewall to block the IP address that was sending the abnormal number of SYN requests. I know that my IP blocking solution won’t last long, as an attacker can spoof other IP addresses to get around this block. I need to alert my manager about this problem quickly and discuss the next steps to stop this attacker and prevent this problem from happening again. I will need to be prepared to tell my boss about the type of attack I discovered and how it was affecting the web server and employees.

<h2>Wireshark Log Analysis</h2>

<p align="center">
<img src="https://i.imgur.com/NTiEHQe.png" height="70%" width="70%" alt="Wireshark Log 1"/> 
</p>
The logs indicate that initially, the attacker's SYN requests, which are highlighted and labeled as red, are answered normally by the web server (log items 52-54). However, the attacker keeps sending more SYN requests, which is abnormal. At this point, the web server is still able to respond to normal visitor traffic, which is highlighted and labeled as green. An employee visitor with the IP address 198.51.100.14 successfully completes a SYN/ACK connection handshake with the web server (log items 55, 56, 58). Then, the employee’s browser requests the sales.html webpage with the GET command, and the web server responds (log items 60 and 62). 
<br />
<br />

<p align="center">
<img src="https://i.imgur.com/FnQ79MR.png" height="70%" width="70%" alt="Wireshark Log 2"/> 
<img src="https://i.imgur.com/oAsL18J.png" height="70%" width="70%" alt="Wireshark Log 3"/> 
</p>
In the following 20 rows, the log begins to reflect the struggle the web server is having to keep up with the abnormal number of SYN requests coming in at a rapid pace. The attacker is sending several SYN requests every second. The rows highlighted and labeled yellow are failed communications between legitimate employee website visitors and the web server.
<br />
<br />
There are two types of errors in the logs:
<br />
1. HTTP/1.1 504 Gateway Time-out (text/html) Error Message. This message is generated by a gateway server that was waiting for a response from the web server. If the web server takes too long to respond, the gateway server will send a timeout error message to the requesting browser.
<br />
2. [RST, ACK] Packet. If the [SYN, ACK] packet is not received by the web server, a [RST, ACK] packet is sent to the requesting visitor. The visitor will receive a timeout error message in their browser, and the connection attempt will be dropped. The visitor can refresh their browser to attempt to send a new SYN request.
<br />
<br />

<p align="center">
<img src="https://i.imgur.com/i22QnXh.png" height="70%" width="70%" alt="Wireshark Log 4"/> 
<img src="https://i.imgur.com/MNyH3fL.png" height="70%" width="70%" alt="Wireshark Log 5"/> 
</p>
The rest of the log shows that the web server stops responding to legitimate employee visitor traffic. The visitors receive more error messages indicating that they cannot establish or maintain a connection to the web server (log item 121). From log item number 125 on, the web server stops responding. The only items logged at that point are from the attack. As there is only one IP address (203.0.113.0) attacking the web server, we can assume this is a direct DoS SYN flood attack.

<h2>Cybersecurity Incident Report</h2>

<h3>Identifying the type of attack that may have caused this network interruption </h3>
One potential explanation for the website’s connection timeout error message is a DoS attack since the attack originates from a single source. The logs show that the web server stops responding after it is overloaded with SYN packet requests. This event could be a type of DoS attack called SYN flooding that targets network traffic to slow down traffic. A SYN flood attack simulates a TCP connection and floods the server with SYN packets.

<h3>Explaning how the attack is causing the website malfunction</h3>
When the website visitors try to establish a connection with the web server, a three-way handshake occurs using the TCP protocol. The handshake consists of three steps:
<br />
<br />
1. A SYN packet is sent from the source to the destination, requesting to connect.
<br />
2. The destination replies to the source with a SYN-ACK packet to accept the connection request. The destination will reserve resources for the source to connect.
<br />
3. A final ACK packet is sent from the source to the destination acknowledging the permission to connect. Once the destination receives the ACK packet from the source, a TCP connection is established.
<br />
<br />
In the case of a SYN flood attack, a malicious actor will send a large number of SYN packets all at once, which overwhelms the server’s available resources to reserve for the connection. When this happens, there are no server resources left for legitimate TCP connection requests.
<br />
<br />
The logs indicate that the web server has become overwhelmed and is unable to process the visitors’ SYN requests. The server is unable to open a new connection to new visitors who receive a connection timeout message.
