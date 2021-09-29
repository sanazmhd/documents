**Introduction**

TSN is the IEEE 802.1Q defined standard technology to provide deterministic messaging on standard Ethernet. TSN technology is centrally managed and delivers guarantees of delivery and minimized jitter using time scheduling for those real-time applications that require determinism.

TSN Solution Components There are five main components in the TSN solution:

• TSN flow: Term used to describe the time-critical communication between end devices. Each flow has strict time requirements that the networking devices honor. Each TSN flow is uniquely identified by the network devices.

• End devices: These are the source and destinations of the TSN flows. The end devices are running an application that requires deterministic communication. These are also referred to as talkers and listeners.

• Bridges: Also referred as Ethernet switches. For TSN, these are special bridges capable of transmitting the Ethernet frames of a TSN flow on a schedule and receiving Ethernet frames of a TSN flow according to a schedule.

• Central network controller (CNC): For TSN, the CNC acts as a proxy for the Network (the TSN Bridges and their interconnections) and the control applications that require deterministic communication. The CNC defines the schedule on which all TSN frames are transmitted. The CNC application is provided by the vendor of the TSN bridges. Cisco has developed a CNC application for controlling its TSN bridges for TSN.

• Centralized user configuration (CUC): An application that communicates with the CNC and the end devices. The CUC represents the control applications and the end devices. The CUC makes requests to the CNC for deterministic communication (TSN flows) with specific requirements for those flows. The CUC is an application that is vendor specific. Typically the vendor of the TSN end devices will supply a CUC for those end devices.

In Figure 1, we can see all the components and how they relate.

![](https://github.com/sanazmhd/documents/blob/main/fig1.png)

Before the schedule can be successfully computed, the CNC must learn the physical topology. The CUC will initiate a request to the CNC to discover the physical topology. Using LLDP and a seed device, the CNC walks the physical topology, discovering each device and how they are connected. This includes the end devices that support LLDP(link layer discovery protocol). Being compatible with Ethernet, it is a fair assumption that installing a LLDP Agent on TSN devices does not have any compatibility issue. The CNC can use all the LLDP information to create a topology model using a script, and then send that information to the CUC that can implement some graphical tool to draw the topology.
After completion, the CUC issues a requestof the CNC to return the discovered topology. The engineer at this point could verify that the CNC discovered the topology correctly if they choose. IEEE 802.1AB is an IEEE Standard for Local and metropolitan area networks - Station and Media Access Control Connectivity Discovery. In the next section this protocol will be described with more details.

Using the simplified model as shown in figure above, the steps to create the TSN flow are described:

1. CUC Initiates Physical Topology Discovery: Using LLDP and a seed device, the CNC explores the physical topology, discovering each device and how they are connected. This includes the end devices that support LLDP.
2. CUC Requests Network Resources: Performed by the engineer responsible for defining the end to end communication. The engineer works out which end device (talker) has to communicate with other end devices (listener). The engineer is responsible for identifying all listeners, because there can be more than one for each TSN flow. The engineer can also define the latency requirements for the communication, the maximum size of the Ethernet packet that will be sent, and other dependencies (for example, whether there is a sequence order to the TSN flows). The CUC will gather this for all TSN flow requests and submit to the CNC using an API for accepting requests.
3. Compute Schedule: The engineer (via the CUC) will initiate a request that the CNC compute the schedule. The CNC will return success or failure for the request (depending on how the petitions can be or not fulfilled with the present network topology/elements). The end schedule cannot be computed unless the CNC knows the physical topology.
4. View Computation Results: Typically, the network engineer would want to view the schedule and verify before making it go live. The engineer (via the CUC) requests the CNC return the details of the computed schedule. This includes the details for each device involved in the TSN flows. The details include everything the end devices and bridges need to know for configuring TSN:

• Unique identifiers for each TSN flow (destination MAC address, VLAN, CoS)

• Start and end of transmit window at each hop (talkers and bridges)

• Start and end of receive window at each hop (listeners and bridges)

• End-to-end latency as computed

5. Distribute Schedule: Satisfied that the schedule will work, the engineer (via the CUC) or an automatic process issues a request to the CNC to distribute the computed schedule to the TSN bridges. The CUC will also program the talkers and listeners for the TSN flows. The talkers are expected to transmit every TSN flow according to a schedule.

**What is LLDP (Link Layer Discovery Protocol)?**

The Link Layer Discovery Protocol (LLDP) is a media independent protocol intended to be run on all IEEE 802 devices, allowing a LLDP agent to learn higher layer management reachability and connection endpoint information from adjacent devices. LLDP runs on all 802 media. Additionally the protocol runs over the data-link layer only, allowing two systems running different network layer protocols to learn about each other. Each device configured with an active LLDP Agent sends periodic messages to the Slow Protocols multicast MAC address as specificed by Std 802.3, 2000 Edition Annex 43B. The device sends the periodic messages on all physical interfaces enabled for LLDP transmission, and listens for LLDP messages on the same set on interfaces. Each LLDP message contains information identifying the source port as a connection endpoint identifier. It also contains at least one network address which can be used by an NMS to reach a management agent on the device (via the indicated source port). Each LLDP message contains a configurable time-to-live value, which tells the recipient LLDP agent when to discard each element of learned topology information.

**Benefits of LLDP**

LLDP provides the following benefits:

- Simplifies the use of network management tools in a multi-vendor environment.
- Accurate discovery of physical network topologies that simplifies troubleshooting within enterprise networks.
- Enables discovery of stations in multi-vendor environments.
- Provides device capability and supports optional system name and description, and management address.
- Provides information that can be used to detect duplex and speed mismatches.
- Discover devices with misconfigured or unreachable (IP addresses).

**Frame Encapsulation**

An LLDP PDU is encapsulated within an 802 frame that cooresponds to frame formatted to meet the requirements of an 802 Slow Protocol. The format is shown in the following figure:

![](https://github.com/sanazmhd/documents/blob/main/fig2.png)

**Slow Protocols Multicast DA:**

The Slow Protocols Multicast destination address is 01-80-C2-00-00-02. This address is within the range reserved by ISO/IEC 15802-3 (MAC Bridges) for link-constrained protocols and will not be forwarded by conformant MAC bridges.

**Station SA**

The source MAC address of the sending station

**Slow Protocols Type**

The Slow Protocols Type field encoding of the Length/Type field is 88-09

**Subtype**

The Slow Protocols Subtype field is TBD All reserved fields shall be set to zero.

**LLDP Message Format**

The basic LLDP protocol data unit consists of a header, followed by a variable number of Type-Length-Value (TLV) attributes. A single LLDP PDU is transmitted in a single 802 media frame.

**LLDP Header Format**

![](https://github.com/sanazmhd/documents/blob/main/fig3.png)

The LLDP header is a 4 byte header, in network byte order, containing 3 fields, as shown in figure 3

The LLDP header contains the following fields:

**Version**

The LLDP protocol version number, set to 0x01 for this version of the protocol.

**Flags**

The LLDP flags field provide for future header extensions and keep the header word-aligned for easier processing. No flag definition bits are defined at this time. This field must be set to zero in all version 1 LLDP messages.

**Time to Live**

The number of seconds the information in this LLDP message should be regarded as valid by the recipient. Agents of the PTOPO MIB must not return MIB information based on expired LLDP messages. The valid range is 0 to 65535 for this field.

**LLDP Advertisements**

LLDP transmits advertisements as packets called LLDP Data Units (LLDPDUs). An LLDPDU consists of a set of Type-Length-Value elements (TLV), each of which contains a particular type of information about the device or port transmitting it.

**Type-Length-Value (TLV)**

A single LLDPDU contains multiple TLVs. TLVs are short information elements that communicate data, such as variable length strings, in a standardized format. Each TLV advertises a single type of information, such as its device ID, type, or management addresses. The following table describes fields in a TLV.

| **FIELD**| **DESCRIPTION DESCRIPTION** |
| --- | --- |
| Type | Identifies the kind of infornution, It consists of a 7-bit Type code.|
| Length |Identifies the length of the information. It consists of a 9-bit value that specifies the number of bytes of data in the Value field.|
| Value | Contains the actual value of the advertised information, This is a variable length data field.|

LLDP sends mandatory TLVs in each advertisement; it can also be configured to send one or more

optional TLVs, from the following groups:

- Mandatory Base TLVs, included in all LLDP advertisements. See IEEE 802.1AB-2005.
- Optional Base TLVs, which may be included in any LLDP advertisements.

- IEEE 802.1 Organizationally Specific TLVs (802.1 TLVs).
- IEEE 802.3 Organizationally Specific TLVs (802.3 TLVs).
- LLDP-MED Organizationally Specific TLVs (LLDP-MED TLVs), included in LLDP-MED

Mandatory and optional TLVs for LLDP and LLDP-MED advertisements are shown in the table

following:

**Mandatory Base TLVs—1EEE 802.1AB-2005** 
| **TLV**| **DESCRIPTION** |
| --- | --- |
|Chassis ID | Identifies the device&#39;S chassis, On this switch, this is the MAC address of the switch or stack|
|Port ID | Identifies the port that transmitted the LLDPDU |
|Time To Live(TTL)| Indicates the length of time in seconds for which the information received in the LLDPDIJ remains valid. If the value is greater than zero, the information is stored in the  LLDP remote system MIB. If the value is zero, the information previously received is no longer valid, and is removed from the MIB|
|End of LLDPDU |Signals that there are no more TLVs in the LLDPDU|
| Port VLAN |VLAN identifier that the local port associates with untagged or priority taggedframes|
|Port &amp; Protocol VLANs  | Whether Port &amp; Protocol VLAN is supported and enabled on the port, and the list of Port &amp; Protocol VLAN identifiers |
|VLAN Names  | List of VLAN names that the port is assigned to |
|Protocol IDs  | List Of protocols that are accessible through the port, for instance: <p>9000 (Loopback)</p> <p>00 26 42 42 03 00 00 00 (STP)</p> <p>00 27 42 42 03 00 00 02 (RSTP)</p> <p>00 69 42 42 03 00 00 03 (MSTP)</p> <p>888e01 (802.1x)</p> <p>aa aa 03 00 e0 2b 00 bb (EPSR)</p> <p>88090101 (LACP)</p <p>00540000e302 (loop protection)</p> <p>0800 (IPV4)</p> <p>0806 (ARP)</p> <p>86dd (IPV6)</p>|
 
 **Optional Base TLVs—1EEE 802.1AB-2005** 
| **TLV**| **DESCRIPTION** |
| --- | --- |
|Port description  | A description of the device's port in alpha-numeric format|
|System name  | The system&#39;s assigned name in alpha-numeric format |
|System description  | A description of the device in alpha-numeric format: This includes information about the device&#39;s hardware and operating system |
|System capabilities  | The device's router and bridge functions, and whether or not these functions are currently enabled |
| Management address | The address of the local LLDP agent- This can be used to obtain information related to the local device |

 **IEEE 802.1 Organizationally Specific TLVs (802.1 TLVs)-IEEE 802.1AB-2005 Annex F** 
| **TLV**| **DESCRIPTION** |
| --- | --- |
|Port description  | A description Of the port in alpha-numeric format|
|Network Policy |Network policy information configured on the port for connected media endpoint devices. The switch supports Application Type I : Voice, including the following network policy for connected voice devices to use for voice data:<p>- Voice VLAN ID</p> <p>- Voice VLAN user Priority tagging</p> <p>- Voice VLAN Diffserv Code Point (DSCP)</p>|
|Location Identification|Location information configured for the port, in one or more of the followingFormats:<p>- Civic address</p> <p>- Coordinate-based LCI</p> <p>- Emergency Location Identification Number (ELIN)</p>|
|Extended Power-via-MDI| For PoE-capable devices, this TLV includes: <p>- Power Type field: Power Sourcing Entity (PSE)</p> <p>- Power Source field: current power source, either Primary Power Source or Backup Power Source</p> <p>- Power Priority field: power priority configured on the port</p> <p>- Power Value field: In TLVs transmitted by Power Sourcing Equipment (PSE) such as this switch, this advertises the power that the port can supply over amaximum length cable based on its current configuration (that is, it takes into account power losses over the cable). In TLVs received from Powered Device (PD) neighbors, the power value is the power the neighbor requests.</p>|
|MAC/PHY Configuration/ Status| The current values Of the following for the port: <p>- Speed and duplex mode auto-negotiation support </p> <p>- Auto-negotiation Status</p> <p>- PMD (physical media dependent) auto-negotiation advertised capability</p> <p>- Operational MAU type</p> <p>- This TLV is always included in LLDP-MED advertisements</p>| 
|Power Via MDI| The power-via-MDI capabilities<p>on devices that are LLDP-MED and PoE-capable, we recommend using theExtended Power-via-MDI TLV instead of this TLV</p> | 
| Link Aggregation| Whether the link is capable of being aggregated, whether it is currently in anaggregation and if in an aggregation, the port of the aggregation|
|Maximum Frame Size| The maximum supported 802.3 frame size that the sending device is capable of receiving—larger frames will be dropped| 

**Protocol Operation**

An active LLDP Agent must perform the following tasks:

• transmit LLDP messages

• process received LLDP messages

• maintain an instance of the LLDP MIB

• maintain an instance of the PTOPO MIB

• maintain appropriate ifEntry and/or entPhysicalEntry instances

• implement ifAlias and/or entPhysicalAlias MIB objects

**Protocol Initialization**

Upon system reinitialization, the following tasks are performed by the LLDP agent:

Non-volatile configuration for the LLDP MIB is retrieved if applicable,otherwise appropriate default values are assigned to all LLDP configuration variables.If LLDPAdminStatus is equal to &#39;disabled(2)&#39;, then LLDP initialization is terminated (until such time that the LLDPAdminStatus object is set to &#39;enabled(1)&#39;), otherwise continue.

Internal (implementation-specific) data structures are initialized such that appropriate local physical topology information and LLDP transmission parameters are set.

**Message Encoding**

This section does not assume a particular buffering strategy, and such details are omitted.

**Header Fields**

The version field is set to one (0x01). The flags field is set to zero (0x00). The time-to-live field is set to the value obtained by the following formula: TTL = min(65535, (LLDPMessageTxInterval \* LLDPMessageTxHoldMultiplier))

**TLVs**

Each message must contain one instance of each of the mandatory LLDP TLV elements. Additional TLV data elements may be added as maximum frame size permits. The mandatory TLVs include: Chassis ID, Port ID (optional for repeaters) and Management Address.

**Message Transmission**

LLDP agents must follow the rules for Slow Protocols transmission as defined by Std 802.3, 2000 Edition, Annex 43B. In addition to these rules, an active LLDP agent must transmit a LLDP message out each appropriate port, once each message interval, as determined by the LLDPMessageTxInterval MIB object, subject to the restriction of transmission rules for Slow Protocols. Messages transmitted on repeater devices may be sent for each repeater backplane, once per message interval. Actual transmission intervals should be jittered to prevent synchronization effects.

Note that the agent must suppress the transmission of multiple LLDP messages during a single message interval, in the event message transmission cannot be restricted to a single port, but rather a group of ports (e.g., a repeater device).

In this case, a single port in the port group should be selected (in an implementation-specific manner) to represent the port group. Note that an agent is encouraged to represent port groups as &#39;backplanes&#39;, in the entPhysicalTable of the Entity MIB, rather than individual ports in either the Entity MIB or Interfaces MIB.

Regarding the transmission of a single LLDP message, for the indicated physical interface contained in the local system:

The LLDP agent checks for the existence of a LLDPSuppressEntry for the port. If an entry exists then this port is skipped, otherwise continue. The LLDP message is encapsulated as appropriate for the port. The MAC header is filled in with appropriate SA and DA and EtherType fields as defined above. The frame is transmitted or passed to a lower layer for transmission. The LLDPStatsOutPkts counter is incremented for the indicated local port.

**Message Forwarding**

As indicated by the operation of Slow Protocols, LLDP agents should not forward LLDP messages received on any port. However, some devices, such as repeaters, cannot examine each frame received on an interface or port. Such a device will allow LLDP messages to be retransmitted on one or more local ports, and will transmit its own LLDP messages on those ports as well. These agents are termed &#39;forwarding&#39; LLDP agents.

LLDP agents located on devices which examine each frame before retransmitting it (e.g., routers and bridges), are expected to process received LLDP messages and not retransmit them on any local port. These agents are termed &#39;non-forwarding&#39; LLDP agents.

An NMS may find physical topology information about the same physical port, represented by several LLDP agents. This may occur for one of several reasons, including a mixture of forwarding and non-forwarding LLDP agents within a network.

**Received Message Processing**

An active LLDP agent must process LLDP messages received on each appropriate port, as such messages arrive. Before LLDP specific receive rules are executed, the frame is subject to the receive processing rules of Slow Protocols defined in Std 802.3, 2000 Edition, Annex 43B.

The following sections refer to the reception of a single LLDP message, for the indicated physical interface contained in the local system:

**Header Fields**

The LLDP message and the chassis/port indices associated with the receiving port are retrieved.

The LLDP version and flags field are checked. The version should equal one (0x01) and the flags should equal zero (0x00). If not, the LLDPStatsInErrors counter for the receiving port is incremented and processing is terminated; otherwise continue.

**TLVs**

The TLV portion of the message is decoded. If this portion of the LLDP message is not properly encoded, as defined above, then the LLDPStatsInErrors counter for the receiving port is incremented, and processing is terminated; otherwise continue.

The list of TLV elements is examined. The agent must skip and ignore PDU data elements unknown to the agent. If any of the mandatory data elements are missing, then the LLDPStatsInErrors counter for the receiving port is incremented, and processing is terminated; otherwise continue.

The LLDPStatsInGoodPkts counter is incremented for the receiving port.

**PTOPO MIB Update**

If the time-to-live field in the LLDP message header is zero then execute this interface shutdown procedure, described below. Processing of the LLDP message is now complete.

If the time-to-live field is non-zero, then the appropriate ptopoConnEntry is found or created, based on the data elements included in the LLDP message. If the indicated entry is dynamic (i.e., ptopoConnIsStatic is true), then the current sysUpTime value is stored in the ptopoConnLastVerifyTime field for the entry.

If a ptopoConnEntry was added then the ptopoConnTabInserts counter is incremented. If a topoConnEntry of one type was replaced with an entry of a different type, then the ptopoConnTabReplaces counter is incremented.

If any ptopoConnEntry was added or deleted, or if information other than the ptopoConnLastVerifyTime changed for any entry due to the processing of this LLDP message, then the ptopoLastChangeTime object is set with the current sysUpTime, and a ptopoConfigChange trap event is generated. (See the PTOPO MIB for information on ptopoConfigChange trap generation.)

**Interface Shutdown Procedure**

A special procedure exists for the case in which a LLDP agent knows a particular port is about to become non-operational. Note that the LLDPSuppressTable has precedence over these procedures, and they are only executed if the indicated interface is not specified in the LLDPSuppressTable. If any entries are deleted as a result of these procedures, the ptopoConnTabDeletes counter is incremented for each deleted entry.

**LLDP Shutdown Transmission**

In the event an interface, currently configured with LLDP message transmission enabled, either becomes disabled for LLDP activity, or the interface is administratively disabled, a final LLDP message is transmitted with a time to live value of zero (before the interface is disabled). In the event the LLDPOperStatus is transitioning to the disabled state, then this shutdown procedure should be executed for all local interfaces.

**LLDP Shutdown Reception**

![](https://github.com/sanazmhd/documents/blob/main/fig4.png)

After reception of a valid LLDP message with a time-to-live value equal to zero, the LLDP Agent must remove all information in the PTOPO MIB learned from the particular LLDP agent, which is associated with the indicated remote connection endpoint.

**802.1ABdh**

802.1ABdh is an Standard for Local and Metropolitan Area Networks - Station and Media Access Control Connectivity Discovery and it is is underway in [IEEE802] that may extend the LLDP standard so that stations can announce a larger amount of information:

- Propose to extend LLDP to scale existing LLDP applications and add enhancements for router (i.e. LSVR) and Time Sensitive Networking (TSN) applications
- The LLDPv2 project will be an Amendment to 802.1AB-2016 (P802.1ABdh)
- The LLDPv2 project will allow transmission of LLDPv2 TLV databases to consist of multiple frames
- LLDPv2 and LLDPv1 nodes will interoperate as though they were LLDPv1 with a single frame database
- LLDPv2 should be sufficient to fill most discovery needs without the additional protocols

**Objectives for a new LLDPv2 method:**

• Transparent to current users of LLDP, they just have a larger database of TLVs

• Support LLDP TLV databases larger than a single frame

• IETF needs discovery database sizes around 64K octets

• Support the ability to limit the LLDP frame size to meet timing constraints imposed by some

TSN applications

• Support the ability to communicate with an LLDPv1 implementation

• Only the first LLDPDU would be exchanged between an LLDPv1 and LLDPv2 implementation

• Support shared media, however, optimize for point-to-point links

• Ensure the integrity of the full set of TLVs received by a peer

• This can be useful in v1 implementations as well

- Support pacing of frames to receivers to prevent overloading low-level network

firmware

• Historically OSPF and IS-IS have had problems from lack of flow and congestion management

• Reduce network traffic by reducing periodic transmission to the minimum

• Only update the base LLDPv1 PDU periodically (same as LLDP today)

• Extension PDUs are only updated on demand from receivers

• Update other PDUs only when they have changed

• Other optimizations and considerations which may be useful

• Minimize computational load on LLDPv2 receivers to update and validate the database

• Supporting larger TLVs

• TLVs spanning multiple extension PDUs

• Database authentication, is high want for IETF and other applications

• Address key exchange requirements

**LLDPv2 Operation: Receiver Pacing**

![](https://github.com/sanazmhd/documents/blob/main/fig5.png)

**Topology Discovery for SDN:**

OpenFlow Discovery Protocol (OFDP) is one of the most common protocols used to discover the network topology in a data plane and then transmit it to the control plane for management. OFDP uses the Link Layer. Discovery Protocol (LLDP) message format for that purpose and the SDN-Controller sends a large number of LLDP advertisements at relatively large, fixed intervals to each active SDN-Switch port in the network to discover links between SDN-Switches. The SDNController uses Packet\_Out OpenFlow message to send the LLDP advertisement to each active SDN-Switch port, and the SDN-Switch on the other side (data layer) will send the link information by encapsulating the LLDP packet in a Packet\_In OpenFlow message. This process is cyclic every 10 s, which means that every 10 s the SDN-Controller will send several Packet\_Out messages equal to the number of active SDN-Switch ports The results of the previous processes are a burden on the resources of the SDN-Controller and are inefficient in detecting network topology changes in a timely manner. This leads to many studies in the literature that OFDP has critical limitations in terms of performance which makes it inefficient especially for large SDN networks. Another issue related to topology discovery between SDN and non-SDN networks.

**The performance metrics of using lldp in SDN:**

1. The number of packets sent and received by the SDN-Controller: the number of packets sent and received by the SDN-Controller is one of the factors that can be used as a performance metric. To discover the links between SDN-Switches, in each discovery round the SDN-Controller sends several LLDP Packet\_Out (POUT) messages equals to the number of active SDN-Switch ports in the network and will receive a number of Packet\_In (PIN) messages equals to twice of the number of links.
2. the SDN-Controller will send three LLDP Packet\_Out messages equal to the number of active ports on SDN-Switch (s1) and it will receive one LLDP Packet\_In message to discover the oneway link from s1 and s2. The SDN-Controller also needs to send another three LLDP Packet\_Out messages to s2 to discover the one-way link from s2 to s1 and it also needs to receive an LLDP Packet\_In message. In total, the SDN-Controller will send six LLDP Packet\_Out messages (i.e., number of active ports on s1 and s2) and two LLDP Packet\_In (i.e., double of the number of links) messages to discover the link between s1 and s2.
3. Accumulative CPU Utilization of SDN-Switches

Another factor is the extent to which OFDP is using the CPU for SDN-Switches. SDN-Switch is an essential part of discovering the topology. The SDN-Switch receives LLDP Packet\_Out messages from the SDNController and sends them to its active ports. As a result, the number of packets sent or received by the SDN-Switches will also increase the CPU utilization ratio.

4. Bandwidth Consumed

there are two types of connections: between the SDN-Switches themselves and between SDNSwitches and the SDN-Controller. This metric is determined by the size of the exchanged OFDP packet to maintain the topology.Thus, this bandwidth can be especially important for measuring the performance of large networks and in-band control channels.

5. Learning Time

Learning time is the time the SDN-Controller needs to learn about topology changes. The discovery process will be repeated every discovery interval. The discovery interval is the time interval between two discovery rounds. The problem is that when a topology change occurs, the SDN-Controller will wait for the next discovery round to learn about new topology changes. This means that learning time is at least equal to the discovery interval.

**Challenges of the Link Discovery**

the SDN-Controller sends an LLDP packet encapsulated with a Packet-Out message to each active SDN-Switch port in the network. This discovery mechanism could present serious performance issues to SDN networks, especially of large networks. Therefore, we will summarize the OFDP link discovery challenges as follows:

1. Overhead to SDN-Controller and Control Channel This point of the challenge has been clarified in details in the OFDP performance subsection, and therefore we will avoid re-explaining it
2. Inefficient Link Failure Detection

In most SDN-Controllers, each round of detections is performed approximately every 10 s and the SDN-Controller will become aware of new topology changes. This is a very long process for dynamic network environments where the changes to the topology occur frequently over a short period. This greatly affects the operation of network applications that depend entirely on the SDN-Controller&#39;s network topology. For example, if the learning time is long and there is a link failure on router links, the routing application will still redirect traffic on that path based on a legacy network topology, which means this will cause many packets to be dropped. This figure shows the relationship between learning time and topology changes. When a topology change occurs after the first discovery round, the SDN-Controller will wait for the next round to re-detect changes to the topological structure, which is too long. On the other hand, there is a suggestion to reduce the interval of discovery changes, but this will cause the number of the Packet\_Out messages to increase significantly, which in turn will increase the load the SDN-Controller and uses more bandwidth in the process of discovering the topology. In addition, an increase in the discovery interval means fewer LLDP messages and less overhead, but also means more time required to learn about new changes.

![](https://github.com/sanazmhd/documents/blob/main/fig6.png)

**Sequrity issues**

Adding security to the topology discovery processes also poses a new challenge in terms of Quality of Services (QoS). Secure discovery is a great idea to protect the real-time topology from attacks such as inserting malicious rules at SDN-Switch, denial of services at SDN-Controller, and man-in-themiddle in control channel , but it causes other issues related to the performance of the device and timeconsuming. Therefore, given the contrast of the two trends between network performance and security in sensitive issues (network topology discovery), the issue of balancing between them is also important. the SDN discovery topology protocol needs to provide the SDN-Controller with a real-time view of the network topology to meet the application and dynamic routing Quality of Service (QoS) demands.

**SDN Topology Discovery improvements**

- reducing the number of LLDP Packet\_Out messages to only one LLDP Packet\_Out message per SDN-Switch .
- using a new packet format for link discovery by using minimal features of the frame and removing unnecessary features from the standard LLDP frame.
- the discovery service can be shared between the SDN-Controller and SDN-Switches .
- network state management can be shared between multiple SDN-Controllers.
- centralized techniques, such as the Path Compute Element (PCE).
- Using a sequence of signal checking to detect the links one by one.
- transfering the control logic for topology discovery and its security from SDNController to SDNSwitch in order to reduce arithmetic operations from SDN-Controll.
- Using idle and hard timeoutmechanism in the SDN-Controller to calculate the lifespan of the forwarding entry in the switch flow table
- the control channel failure recovery
- and…

Based on the metrics announced in this section, each of these proposals has some limitations in one or more of these metrics. trade-off should be based on the criticality of the environment and topology change rate. In some environments, such as enterprise networks, the rate of topology change is low and less significant; thus, it is possible to increase the discovery interval. In contrast, in data-centers and transport networks, the rate of topology change is high and critical, thus, it is recommended to decrease the discovery interval as much as possible.

**Introduction on 802.1Qbv and its functional parameters**

Streams can have different priorities defining their traffic class, given by the 3 priority bits of the priority code point (PCP) of the IEEE 802.1Q header. IEEE 802.1Qbv defines a time-based shaper functionality enabling time-triggered communication at the egress ports. A time-aware shaper is essentially a gate enabling or disabling the transmission of frames for a queue following the specification of a periodic schedule.

Figure 7 depicts a logical representation of a simplified 802.1Qbv-capable switch with three ports. The figure depicts a scenario in which network traffic traverses the switch from an ingress port to an egress port. Internally, the ports are connected to a filter distributing incoming frames to the associated queue of the egress port based on classification criteria like e.g. the priority code point (PCP) of the IEEE 802.1Q header.

On regular switches with multiple ports, incoming traffic will be directed to the corresponding egress port by the switching engine. Every port will have a similar logical composition on egress as the one depicted, including a series of logical queues buffering the respective frames until their transmission. In the most generic representation, each 802.1Qbv queue has a timed gate associated to it enabling or disabling the transmission of frames according to a predefined static schedule. Hence, scheduled events determine at which time instants a queue is opened and traffic is forwarded to the egress port and at which time instants the queue is closed such that any pending traffic remains buffered. If multiple queues are opened at the same time, the queue priority determines which of the queues is allowed to forward frames.

![](https://github.com/sanazmhd/documents/blob/main/fig7.png)

One important characteristic is the capability of devices, namely, if they are scheduled or not. If the end-systems are not scheduled but switches are, frames from the end-nodes may arrive in any order to the switches. Therefore, the first switch on the route has to act as a synchronization gate. This synchronization is possible if there are enough queues to isolate flows arriving simultaneously, allowing thus each of them to be scheduled deterministically. Complementary, end-systems may be scheduled but switches are not. In such a case, switches act as delay elements but cannot control the time of forwarding frames. If end-systems and switches are scheduled, the scheduler can plan when to send individual frames of flows such that they arrive with a known order at the first switch and switches can control forwarding times.

The number of queues per egress port, which translates to the number of different priorities that can be handled, affects the amount of traffic and the real-time properties to be guaranteed. Another important aspect is how the queues operate. Queues may follow a strict priority policy, meaning that the gate is always opened and the arbitration is purely based on their assigned priority. Using the time-gates, however, queues can be scheduled following a time-triggered (TT) paradigm, where the priority is overruled by the schedule, i.e., the schedule of the timed-gate defines the servicing order, not the priority. If queues are scheduled following a mutually exclusive pattern, i.e., no two queue gates are opened at the same time, the result is a fully deterministic forwarding policy.

Having different queue types opens up the possibility to have two different types of traffic, namely scheduled and non-scheduled traffic. Scheduled traffic is assigned to the scheduled queues in order to guarantee latency and jitter requirements. Non-scheduled traffic is isolated from scheduled traffic within the priority queues of a device. Within the priority queues, non-scheduled flows may interfere with each other, but not with scheduled flows (since the associated scheduled queues always have higher priority).

**Implementation of simple Queing model in omnet ++**

![](https://github.com/sanazmhd/documents/blob/main/fig8.png)


**Tsn node with qbv capability:**

![](https://github.com/sanazmhd/documents/blob/main/fig9.png)

**Elements of nodes:**

1. **status**

Keeps track of the status of network node (up, down, etc.) for other modules, and also displays it as a small overlay icon on this module and on the module of the network node

2. **scheduler**

Scheduler for the device

3. **sync**

Sync module for synchronization of the scheduler

4.**app[numApps]**

Applications for the device

5. **bgln**

Represents a queue buffer for background traffic. Messages are forwarded immediately

The buffer stores &quot;size&quot; messages in a fifo queue. The buffer removes and sends the first message in the queue. The when full the oldest frame is dropped before inserting the new frame. Can be configured to drop new frame instead using the dropNew parameter (default: false)

6.**bgOut**

Represents a queue buffer for background traffic. Messages are forwarded immediately

The buffer stores &quot;size&quot; messages in a fifo queue. The buffer removes and sends the first message in the queue. The when full the oldest frame is dropped before inserting the new frame. Can be configured to drop new frame instead using the dropNew parameter (default: false)

7.**checkedBuffer[numBuffer]**

Module for Buffer with Incoming Module for TT and RC traffic

8.**interface Table**

Keeps the table of network interfaces.

9.**srpProtocol**

This module handles the Stream Reservation Protocol. SRP messages are sent and received through SRPEtherLLC and SRPRelay

10.**srpTable**

This module handles the mapping between ports and streams.

11.**avbCTC**

AVB critical traffic control

12.**encap**

This module forwards frames (EtherFrame) based on their destination MAC addresses to appropriate ports.

It can handle AVBs SRP frames. Incoming SRP frames are forwarded as SRP messages through srpOut to the SRProtocol module. Outgoing SRP messages coming through srpIn gate are forwarded according to the controlInfo as EtherFrame

13.**Phy[numPorts]**

The IEEE8021QbvPHYPort is a compund module that contains the input Shaper, output Shaper and MAC (EtherMACFullDuplex) It represents a physical port for a IEEE 802.1Q Ethernet device (Switch or Host) with Qbv shaping
TSN switch with IEEE 802.1 QVB capability
![](https://github.com/sanazmhd/documents/blob/main/fig10.png)

**Elements of switch:**

1.**Status**

Keeps track of the status of network node (up, down, etc.) for other modules, and also displays it as a small overlay icon on this module and on the module of the network node.

2.**scheduler**

Scheduler for the device

3.**sync**

Sync module for synchronization of the scheduler

4.**Checked Buffer[numBuffer]**

Module for Buffer with Incoming Module for TT and RC traffic

5.**macTable**

Interface for MAC address tables

6.**InterfaceTable**

Keeps the table of network interfaces.

7.**srpProtocol**

This module handles the Stream Reservation Protocol

SRP messages are sent and received through SRPEtherLLC and SRPRelay

8.**srpTable**

This module handles the mapping between ports and streams.

9.**avbCTC**

AVB critical traffic control

10.**beswitch**

This module forwards frames (EtherFrame) based on their destination MAC addresses to appropriate ports.

It can handle AVBs SRP frames. Incoming SRP frames are forwarded as SRP messages through srpOut to the SRProtocol module. Outgoing SRP messages coming through srpIn gate are forwarded according to the control Info as EtherFrame

11.**Phy[sizeof(ethg)]**

The IEEE8021QbvPHYPort is a compund module that contains the input Shaper, output Shaper and MAC (EtherMACFullDuplex) It represents a physical port for a IEEE 802.1Q Ethernet device (Switch or Host) with Qbv shaping
