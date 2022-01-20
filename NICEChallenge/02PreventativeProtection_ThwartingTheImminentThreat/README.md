# Challenge 02 - Preventative Protection: Thwarting the Imminent Threat

## Scenario

Our security analyst has been busy with other work recently and is in need of some assistance. She needs help viewing some network traffic logs to identify if any weird activity is going on that could potentially be threats to our network resources. We need you to review a set of logs and identify malicious activity if it exists.

### Specialty Area
Cybersecurity Defense Analysis

### Work Role
Cyber Defense Analyst

## NICE Framework Task
T0023 Characterize and analyze network traffic to identify anomalous activity and potential threats to network resources.

## Tools Used
- Firefox
- Wireshark

## Submitted Documentation

**PCAP file:** 10.0.7.pcap<br>
**Description:** Capture shows evidence of multiple SYN packets being sent from a singular host (172.16.30.109) to the same target (172.16.10.7) on a range of TCP ports. Host never completes a full TCP handshake. This can be seen using Wireshark to open the packet capture and using the filter `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size<=1024` or by viewing Statistics > Conversations > TCP. This is highly likely a TCP SYN stealth scan. There are also a large number of ARP broadcasts coming from the MAC address 00:50:56:85:9b:ef--which also has the IP address 172.16.30.109 in the rest of the traffic. These ARP broadcasts are highly likely to be an ARP scan being done by the same host that was also executing the TCP SYN stealth scan.

**PCAP file:** 20.0.pcap<br>
**Description:** Capture shows evidence of multiple SYN packets being sent from a singular host (172.16.30.109) to the multiple targets on a range of TCP ports. Host never completes a full TCP handshake. This can be seen using Wireshark to open the packet capture and using the filter `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size<=1024` or by viewing Statistics > Conversations > TCP. This is highly likely a TCP SYN stealth scan. There are also a large number of ARP broadcasts coming from the MAC address 00:50:56:85:9b:ef--which also has the IP address 172.16.30.109 in the rest of the traffic. These ARP broadcasts are highly likely to be an ARP scan being done by the same host that was also executing the TCP SYN stealth scan.

**PCAP file:** 30.21.pcap<br>
**Description:** Much like the last two captures, this capture also shows evidence of multiple SYN packets being sent from a singular host (172.16.30.109) to the multiple targets on a range of TCP ports. Host never completes a full TCP handshake. This can be seen using Wireshark to open the packet capture and using the filter `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size<=1024` or by viewing Statistics > Conversations > TCP. This is highly likely a TCP SYN stealth scan. There are also a large number of ARP broadcasts coming from the MAC address 00:50:56:85:9b:ef--which also has the IP address 172.16.30.109 in the rest of the traffic. These ARP broadcasts are highly likely to be an ARP scan being done by the same host that was also executing the TCP SYN stealth scan. Host also makes several attempts to SSH into the mail server (172.16.30.21).