<a id="network-flow-log-overview"></a>
## Network > Flow Log > Overview { #network-flow-log-overview }
The Flow Log service provides statistics by analyzing packets entering and leaving the network interfaces. This service can be used to view various statistics, such as the number and size of packets allowed or denied by the **Security Groups** rules set on the network interface. With the Flow Log service, you can see if the network interfaces are sending and receiving traffic correctly, who they are communicating with, and if there have been any intrusion attempts from the outside.



<a id="main-features"></a>
### Main Features { #main-features }

* The Flow Log service examines the headers of all packets going to and from a network interface. Currently, it only provides functionality for an instance's network interface and transit hub attachments.

* However, headers are inspected and statistics are provided only if the L2 type is Ethernet, L3 type is IPv4, and L4 type is TCP/UDP/ICMP. Inspected packets are aggregated based on 5-tuples.

* Currently, the Flow Log service utilizes **Object Storage** as its storage. At each collection interval you set, a file is created in **Object Storage**, which you can download to see the actual statistics.

* You can check the statistics to see if **Security Groups** are set up correctly, detect external intrusion attempts, and more.


<a id="service-targets"></a>
### Service Targets { #service-targets }

* To collect/view connection information, statistics, etc. of packets coming in and out of ports on your instance

* If you want to collect/view connection information, statistics, etc. of packets flowing to a network service you're using

* To collect/view connection information, statistics of packets allowed or blocked by **Security Groups** settings

* To enhance the security of your instance by viewing the history of packets coming into your instance and blocking suspicious addresses


<a id="terminology"></a>
### Terminology { #terminology }

Describes the resources and terminology used by the Flow Log service.

* flowlog logger: A user-created flow log logger. You can set collection intervals, filters, and more.
* flowlog logging port: The network interface on which collection is actually performed by the user-created flowlog logger.
* 5-tuple: A tuple consisting of the following in a typical L4 packet header: protocol, source address, destination address, source port number, and destination port number. If the 5-tuple is the same, it is considered to be the same flow. Since ICMP does not have L4, it considers both the source port number and the destination port number to be zero.



<a id="statistics-information-items"></a>
## Statistics Information Items { #statistics-information-items }
The Flow Log service collects and aggregates packets and presents them to you in the following ways:


| Number | Field | Description | Unit | Note |
| --- | --- | --- | --- | --- |
| 1| timestamp_start | When the 5-tuple was first inspected | UNIX TIMESTAMP |  |
| 2| timestamp_end | The last time the 5-tuple was inspected | UNIX TIMESTAMP | |
| 3| interface_id | Network Interface ID | UUID |  |
| 4| owner_type | Type of the equipment that owns network interfaces | `instance`, `transithub_attachment`, `inter_project_peering`, `inter_region_peering`, `colocation_gateway` or `loadbalancer` | |
| 5| owner_id | ID of the equipment that owns network interfaces | UUID | |
| 6| subnet_id | ID of the subnet that owns the network interface | UUID | |
| 7| vpc_id | ID of the VPC that owns the network interface | UUID | |
| 8| region | Region information | `KR1`, `KR2`, and `KR3` | \* KR1: Korea (Pangyo) Region <br> \* KR2: Korea (Pyeongchon) region <br> \* KR3: Korea (Gwangju) region |
| 9| protocol | Protocol number from the 5-tuple | Represents the protocol number assigned by IANA. <br> \* Each number corresponds to a different protocol: 1: ICMP, 6: TCP, 17: UDP <br> \* Anything else is not collected.|
| 10 | src_addr | Source address | IPv4 address | |
| 11 | dst_addr | Destination address | IPv4 address | |
| 12 | src_port | Source port number| Integer | ICMP is assumed to be 0. |
| 13 | dst_port | Destination port number | Integer | ICMP is assumed to be 0. |
| 14 | tcp_flag | TCP flag | Integer | The TCP flag is a `bitwise OR` of the packets captured within the collection interval. <br>For more information, see TCP flags at the bottom of the table. |
| 15 | packets | Number of packets seen during the collection interval | Integer | |
| 16 | bytes | The total packet size seen during the collection interval. | Byte | |
| 17 | direction | Packet flow direction of collected 5-tuples | `ingress`, `egress` or `unknown` | |
| 18 | filter | Security Groups results for the collected 5-tuple | `ACCEPT` or `DROP` |
| 19 | transithub_drop_no_route_packets | Number of packets dropped by the Transit Hub router due to lack of a routing path | Integer | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 20 | transithub_drop_no_route_bytes | The total size of packets dropped by the transit hub router due to lack of a routing path | Byte | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 21 | transithub_drop_black_hole_packets | Number of packets dropped because they were determined to be black hole routing on the transit hub router | Integer | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 22 | transithub_drop_black_hole_bytes | The sum of the packet sizes dropped by the transit hub router because it was determined to be black hole routing | Byte | This is specific to transit hubs; non-transit hub interfaces are denoted by -1. |
| 23 | status | Log status | `OK` or `SKIPDATA` or `NODATA`                                                                              | \* OK: 5-tuple logged successfully. <br> \* SKIPDATA: There are packets that were not collected during that collection interval because they exceeded the internal capacity provided by the flow log. <br> \* NODATA: No data was collected within that collection interval. |
| 24 | traffic_path | Traffic path of the collected 5-tuple | Integer | Indicates the network path that the packet flowed through with integer values from 1 to 7. <br> \* 1: VPC Local (communication between resources within the same VPC) <br> \* 2: Internet Gateway (outbound internet traffic, including floating IPs) <br> \* 3: VPN Gateway (on-premises connectivity via Site-to-Site VPN) <br> \* 4: VPC Peering (VPC peering within the same project) <br> \* 5: Region Peering (VPC peering between different regions) <br> \* 6: Project Peering (VPC peering between different projects in the same region) <br> \* 7: Service Gateway (Access to internal NHN Cloud services, e.g., Object Storage) |


<a id="tcp-flag"></a>
### TCP Flag { #tcp-flag }
* If a TCP connection is short-lived, the side initiating the TCP Active Open may send both SYN and FIN within a single collection interval. In this case, SYN | FIN (2 | 1 = 3) is recorded.


* Conversely, on the receiving side, SYN | ACK and FIN may be received within the same collection interval. In this case, SYN | ACK | FIN (16 | 2 | 1 = 19) is recorded.

* The values assigned to SYN, ACK, RST, and FIN follow the TCP header tcp flag bit field (RFC 793, Section 3.1. Header Format).

    * FIN: 1
    * SYN: 2
    * RST: 4
    * ACK: 16

* Packets containing only the PSH flag, packets containing only the ACK flag, and the PSH | ACK flag commonly used for general data transmission are excluded from collection. In other words, only SYN, SYN | ACK, FIN | ACK, RST, and FIN are recorded.
* URG (urgent), ECE (ECN-echo), and CWR (congestion window reduced) are not supported.

<a id="caution"></a>
## Caution { #caution }

<a id="collection-interval"></a>
### Collection Interval { #collection-interval }
* If the collection interval is set too long, traffic from different connections may be collected under the same 5-tuple.

    * If connection establishment and termination are repeated multiple times with the same 5-tuple within a collection interval, these connections will be aggregated under the same 5-tuple even if they are logically distinct connections.

    * Therefore, it is recommended to configure an appropriate collection interval based on your requirements.

<a id="traffic-not-captured-by-flow-log"></a>
### Traffic not captured by Flow Log { #traffic-not-captured-by-flow-log }

* IPv6 traffic is not recorded.
* Multicast traffic to and from the instance is not recorded.
* Traffic communicating with 169.254.169.0/24 for monitoring the instance status is not recorded.
* Traffic mirroring is not recorded.
* ARP packets are not recorded.
* `DROP` events caused by temporary network congestion in the physical equipment containing the instance or in the physical equipment of network services are not subject to collection.

<a id="important-notes-for-using-flow-log-designated-for-a-transit-hub-connection"></a>
### Important notes for using Flow Log designated for a transit hub connection { #important-notes-for-using-flow-log-designated-for-a-transit-hub-connection }

* Multicast traffic in a transit hub records only packets ingressing into the transit hub based on the transit hub itself. Multicast traffic egressing through one or more connections is not recorded.
* All packets flowing through a transit hub are recorded once under ACCEPT regardless of whether they are dropped by the transit hub router. Packets actually dropped by the transit hub router are recorded on a separate line with DROP.
* The transit hub is not affected by the **Connection Setup only** option and collects all packets regardless of the connection state.

<a id="important-notes-when-using-flow-log-designated-for-load-balancers"></a>
### Important notes when using Flow Log designated for load balancers { #important-notes-when-using-flow-log-designated-for-load-balancers }

* The load balancer currently collects only ACCEPT packets. The collection of packets that are dropped by the IPACL set on the load balancer is expected to be supported in the future.

* In addition to packets attempting to access the load balancer and packets between the load balancer and members, we also collect status check packets.
* Flow Logs associated with the service are not affected by the **Connection Setup only** option and will collect all packets regardless of the connection state.

<a id="important-notes-when-using-flow-log-on-peering-gateways-and-colocation-gateways"></a>
### Important notes when using Flow Log on peering gateways and colocation gateways { #important-notes-when-using-flow-log-on-peering-gateways-and-colocation-gateways }

* VPC peering gateway is currently not supported.
* DROP is not supported because this is not a service that allows users to explicitly set DROP.
* Flow Logs associated with the service are not affected by the **Connection Setup only** option and will collect all packets regardless of the connection state.


