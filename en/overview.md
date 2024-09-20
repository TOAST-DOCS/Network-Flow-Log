## Network > Flow Log > Overview
The Flow Log service provides statistics by analyzing packets entering and leaving the network interfaces. This service can be used to view various statistics, such as the number and size of packets allowed or denied by the **Security Groups** rules set on the network interface. With the Flow Log service, you can see if the network interfaces are sending and receiving traffic correctly, who they are communicating with, and if there have been any intrusion attempts from the outside.



### Main Features

* The Flow Log service examines the headers of all packets going to and from a network interface (currently only the instance's network interface is available).

* However, headers are inspected and statistics are provided only if the L2 type is Ethernet, L3 type is IPv4, and L4 type is TCP/UDP/ICMP. Inspected packets are aggregated based on 5-tuples. 
* Currently, the Flow Log service utilizes **Object Storage** as its storage. At each collection interval you set, a file is created in Object Storage (OBS), which you can download to see the actual statistics.

* You can check the statistics to see if **Security Groups** are set up correctly, detect external intrusion attempts, and more.


### Service Targets

* To collect/view connection information, statistics, etc. of packets coming in and out of ports on your instance.

* To collect/view connection information, statistics of packets allowed or blocked by **Security Groups** settings.

* To enhance the security of your instance by viewing the history of packets coming into your instance and blocking suspicious addresses.


### Terminology

Describes the resources and terminology used by the flow log service.

* flowlog logger: A user-created flow log logger. You can set collection intervals, filters, and more.
* flowlog logging port: The network interface on which collection is actually performed by the user-created flowlog logger.
* 5-tuple: A tuple consisting of the following in a typical L4 packet header: protocol, source address, destination address, source port number, and destination port number. If the 5-tuple is the same, it is considered to be the same flow. Since ICMP does not have L4, it considers both the source port number and the destination port number to be zero.



## Statistics Information
* The Flow Log service collects and aggregates packets and presents them to you in the following ways.


| Number | Field | Description | Unit | Note | 
| --- | --- | --- | --- | --- | 
| 1| timestamp_start | When the 5-tuple was first inspected | UNIX TIMESTAMP |  |
| 2| timestamp_end | The last time the 5-tuple was inspected | UNIX TIMESTAMP | |
| 3| interface_id | Network Interface ID | UUID |  |
| 4| vm_id | The ID of the instance that owns the network interface | UUID | |
| 5| subnet_id | The ID of the subnet that owns the network interface | UUID | |
| 6| vpc_id | The ID of the VPC that owns the network interface | UUID | |
| 7| region | Regions | KR1 \| KR2  | \* KR1: Korea (Pangyo) <br> \* KR2: Korea (Pyeongchon) |
| 8| protocol | Protocol number from the 5-tuple | Represents the protocol number assigned by IANA. <br> \* Each number corresponds to a protocol - 1: ICMP, 6: UDP, 17: TCP <br> \* Nothing else is collected.|
| 9| src_addr | Source address | IPv4 address | |
| 10| dst_addr | Destination address | IPv4 address |
| 11 | src_port | Source port number| Integer | ICMP is assumed to be zero. |
| 12 | dst_port | Destination port number | Integer | ICMP is assumed to be zero. |
| 13 | tcp_flags | TCP flag | Integer | The TCP flag processes the packets captured within the collection interval with `bitwise OR`. <br>See the TCP flags at the bottom of the table for more information. |
| 14 | packets | Number of packets inspected during the collection interval | Integer | | 
| 15 | bytes | Total packet size inspected during the collection interval | Byte | |
| 16 | direction | Packet flow direction of the collected 5-tuple | `ingress` \| `egress` \| `unknown` | |
| 17 | filter | Security Groups results for the collected 5-tuple | `ACCEPT` or `DROP` |
| 18 | String | Log Status | `OK` or `SKIPDATA` |\* OK: 5-tuple logged successfully. <br> \* SKIPDATA: There are packets that were not collected during the collection interval because they exceeded the internal capacity provided by the flow log.|


### TCP Flags
* If the TCP connection is short, the side attempting the TCP Active open may send SYN, FIN within the collection interval. In this case, SYN | FIN (2 | 1 = 3) will be logged. 

* Conversely, incoming data may receive SYN | ACK, and FIN within the collection interval. In this case, SYN | ACK | FIN (16 | 2 | 1 = 19) would be logged. 

* Each digit in SYN, ACK, RST, and FIN follows the TCP header tcp flag bit field (RFC 793, section 3.1. Header Format).

    * FIN: 1
    * SYN: 2
    * RST: 4
    * ACK: 16

* Packets with only the PSH flag, packets with only the ACK flag, and the PSH | ACK flag that are normally used when sending traffic are not included in the collections. This means that only SYN, SYN | ACK, FIN | ACK, RST, and FIN are logged.
* Urgent (URG), ECN-echo (ECE), and congestion window reduced (CWR) are not supported.

## Precautions

### Collection Interval
* If set a longer collection interval, they may be collected as the same 5-tuple even though they are actually different connections.

    * If establish/terminate multiple connections with the same 5-tuple within a collection interval, they are counted as the same 5-tuple, even if they are different connections logically.

    * Therefore, we recommend that you set the appropriate collection interval based on your needs.

### Traffic that Flow Log doesn't capture

* IPv6 traffic is not logged.
* Multicast traffic is not logged.
* Traffic communicating to 169.254.169.0/24 to determine the health of the instance is not logged.
* Traffic mirroring is not logged.
