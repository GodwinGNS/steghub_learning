# Side Self Study

## PING

 A ping (Packet Internet or Inter-Network Groper) is a basic Internet program that allows a user to test and verify if a particular destination IP address exists and can accept requests in computer network administration. The acronym was contrived to match the submariners' term for the sound of a returned sonar pulse.


Ping is also used diagnostically to ensure that a host computer the user is trying to reach is operating. Any operating system (OS) with networking capability, including most embedded network administration software, can use ping.

## How does ping work?

Ping works by sending an Internet Control Message Protocol (ICMP) Echo Request to a specified interface on the network and waiting for a reply. When a ping command is issued, a ping signal is sent to a specified address. When the target host receives the echo request, it responds by sending an echo reply packet.


This approach serves two specific purposes: verifying that the target host is available and determining round-trip time (RTT) or latency.


RTT is a measure of how long it took to receive a response. Measured in milliseconds (ms), the process starts when a browser sends a request to a server and is completed when a response from the server is received. RTT is a key performance metric of web applications.

By default, ping commands send multiple requests -- usually four or five -- and display the results. The echo ping results show whether a particular request received a successful response. It also includes the number of bytes received and the time it took to receive a reply or the time-to-live.


## Traceroute

Traceroute is a tool to trace the path of an IP packet as it traverses routers locally or globally. It is primarily used for diagnostic and troubleshooting purposes.

Traceroute is used primarily for diagnostic purposes, but it can be a fun tool to learn about networking or simply to satisfy some nerdy curiosity. System administrators and network engineers can use traceroute to see how traffic flows within an organization, and identify any irregular or sub-optimal paths. Externally, traceroute can display the path of a packet as it traverses many different networks, and in some cases even reveal the different cities or geographic regions traffic flows through. Attackers could potentially use traceroute to map out a target’s network, which is why the types of packets used by traceroute are frequently blocked or filtered at the perimeter of corporate networks.


### How traceroute works

1. The user invokes the traceroute (or tracert) command and specifies a target host. If the host is specified in the form of a domain name, traceroute will attempt to resolve it. 
2. Traceroute sends a data packet towards the target with the TTL value set to “1”. The first router in the path will decrement the value by 1, which should trigger a TTL exceeded message that gets sent back to the host on which the traceroute program is running.
3. With details of the first hop in hand, traceroute will increase the TTL value to “2”. That first router in the path will still decrement the value by 1, but because the TTL will no longer drop to zero right out of the gate the packet can live on for one more hop. Once the TTL value does hit zero (in this case, at the second router in the path), another TTL exceeded message should be generated and passed back to traceroute.
4. The process repeats itself, with traceroute increasing the TTL by 1 each time, until the destination is reached or an upper limit of hops is hit. By default, the upper limit is 30 hops, but a different value can be specified when the command is run.
5. When finished, traceroute prints all the hops in the path, along with the amount of time it took to each hop and back (this is known as the Round Trip Time). 