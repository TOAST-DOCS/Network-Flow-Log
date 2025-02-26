## Network > Flow Log > Overview
The Flow Log service provides statistics by analyzing packets entering and leaving the network interfaces. This service can be used to view various statistics, such as the number and size of packets allowed or denied by the **Security Groups** rules set on the network interface. With the Flow Log service, you can see if the network interfaces are sending and receiving traffic correctly, who they are communicating with, and if there have been any intrusion attempts from the outside.



### Main Features

* The flow log service examines the headers of all packets going to and from a network interface. Currently, it only provides functionality for an instance's network interface and transit hub attachments.

* However, headers are inspected and statistics are provided only if the L2 type is Ethernet, L3 type is IPv4, and L4 type is TCP/UDP/ICMP. Inspected packets are aggregated based on 5-tuples.
* Currently, the Flow Log service utilizes **Object Storage** as its storage. At each collection interval you set, a file is created in Object Storage (OBS), which you can download to see the actual statistics.

* You can check the statistics to see if **Security Groups** are set up correctly, detect external intrusion attempts, and more.


### Service Targets

* To collect/view connection information, statistics, etc. of packets coming in and out of ports on your instance.

* If you want to collect/view connection information, statistics, etc. of packets flowing to a network service you're using

* To collect/view connection information, statistics of packets allowed or blocked by **Security Groups** settings.

* To enhance the security of your instance by viewing the history of packets coming into your instance and blocking suspicious addresses.


### Terminology

Describes the resources and terminology used by the Flow Log service.

* flowlog logger: A user-created flow log logger. You can set collection intervals, filters, and more.
* flowlog logging port: The network interface on which collection is actually performed by the user-created flowlog logger.
* 5-tuple: A tuple consisting of the following in a typical L4 packet header: protocol, source address, destination address, source port number, and destination port number. If the 5-tuple is the same, it is considered to be the same flow. Since ICMP does not have L4, it considers both the source port number and the destination port number to be zero.



## Statistics Information Items 
* The Flow Log service collects and aggregates packets and presents them to you in the following ways.


| Number | Field | Description | Unit | Note | 
| --- | --- | --- | --- | --- | 
| 1| timestamp_start | When the 5-tuple was first inspected | UNIX TIMESTAMP |  |
| 2| timestamp_end | The last time the 5-tuple was inspected | UNIX TIMESTAMP | |
| 3| interface_id | Network Interface ID | UUID |  |
| 4| owner_type | Type of the equipment that own network interfaces | `instance` or `transithub_attachment` | |
| 5| owner_id | ID of  the equipment that owns network interfaces | UUID | |
| 6| subnet_id | ID of the subnet that owns the network interface. | UUID | |
| 7| vpc_id | ID of the VPC that owns the network interface. | UUID | |
| 8| region | Region information | `KR1` or `KR2`  | KR1: Korea (Pangyo) Region <br> KR2 - Korea (Pyeongchon) Region |
| 9| protocol | Protocol number from the 5-tuple | Represents the protocol number assigned by IANA. <br> * Each number corresponds to a different protocol: 1: ICMP, 6: TCP, 17: UDP <br> * Anything else is not collected.|
| 10 | src_addr | Source address | IPv4 address | |
| 11 | dst_addr | Destination address | IPv4 address |
| 12 | src_port | Source port number| Integer | ICMP is assumed to be 0. |
| 13 | dst_port | Destination port number | Integer | ICMP is assumed to be 0. |
| 14 | tcp_flag | TCP flag | Integer | The TCP flag is a `bitwise OR` of the packets captured within the collection interval. <br>For more information, see TCP flags at the bottom of the table. |
| 15 | packets | Number of packets seen during the collection interval | Integer | | 
| 16 | bytes | The total packet size seen during the collection interval. | Byte | |
| 17 | direction | Packet flow direction of collected 5-tuples | `ingress`, `egress`, or `unknown` | |
| 18 | filter | Security Groups results for the collected 5-tuple | `ACCEPT` or `DROP` |
| 19 | transithub_drop_no_route_packets | Number of packets dropped by the Transit Hub router due to lack of a routing path | Integer | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 20 | transithub_drop_no_route_bytes | The total size of packets dropped by the transit hub router due to lack of a routing path. | Byte | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 21 | transithub_drop_black_hole_packets | Number of packets dropped because they were determined to be black hole routing on the transit hub router | Integer | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 22 | transithub_drop_black_hole_bytes | The sum of the packet sizes dropped by the transit hub router because it was determined to be black hole routing. | Byte | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 23 | String | Log status | `OK` or `SKIPDATA` or `NODATA`                                                                              | * OK: 5-tuple logged successfully. <br> * SKIPDATA: There are packets that were not collected during that collection interval because they exceeded the internal capacity provided by the flow log. <br> * NODATA: No data was collected within that collection interval. |


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
* ARP packet is not logged.

### Cautions by specifying flow logs in transit hub attachment

* Multicast traffic on a transit hub records only packets that are ingressing through the transit hub, relative to the transit hub. The traffic does not record multicast traffic that is egressing through one or multiple attachments.
* All packets that flowed through the transit hub are logged once in `ACCEPT`, regardless of whether they were dropped by the transit hub router or not. Packets that are actually dropped by the transit hub router are logged with `DROP` on a separate line.
* The Transit Hub is not affected by the `connection setup only` option and will collect all packets regardless of connection status.