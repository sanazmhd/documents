**Introduction**

TSN is the IEEE 802.1Q defined standard technology to provide deterministic messaging on standard Ethernet. TSN technology is centrally managed and delivers guarantees of delivery and minimized jitter using time scheduling for those real-time applications that require determinism.

TSN Solution Components There are five main components in the TSN solution:

• TSN flow: Term used to describe the time-critical communication between end devices. Each flow has strict time requirements that the networking devices honor. Each TSN flow is uniquely identified by the network devices.

• End devices: These are the source and destinations of the TSN flows. The end devices are running an application that requires deterministic communication. These are also referred to as talkers and listeners.

• Bridges: Also referred as Ethernet switches. For TSN, these are special bridges capable of transmitting the Ethernet frames of a TSN flow on a schedule and receiving Ethernet frames of a TSN flow according to a schedule.

• Central network controller (CNC): For TSN, the CNC acts as a proxy for the Network (the TSN Bridges and their interconnections) and the control applications that require deterministic communication. The CNC defines the schedule on which all TSN frames are transmitted. The CNC application is provided by the vendor of the TSN bridges. Cisco has developed a CNC application for controlling its TSN bridges for TSN.

• Centralized user configuration (CUC): An application that communicates with the CNC and the end devices. The CUC represents the control applications and the end devices. The CUC makes requests to the CNC for deterministic communication (TSN flows) with specific requirements for those flows. The CUC is an application that is vendor specific. Typically the vendor of the TSN end devices will supply a CUC for those end devices.

In Figure 1, we can see all the components and how they relate.
![](./https://github.com/sanazmhd/documents/blob/main/fig1.png)



Before the schedule can be successfully computed, the CNC must learn the physical topology. The CUC will initiate a request to the CNC to discover the physical topology. Using LLDP and a seed device, the CNC walks the physical topology, discovering each device and how they are connected. This includes the end devices that support LLDP(link layer discovery protocol). After completion, the CUC issues a requestof the CNC to return the discovered topology. The engineer at this point could verify that the CNC discovered the topology correctly if they choose. IEEE 802.1AB is an IEEE Standard for Local and metropolitan area networks - Station and Media Access Control Connectivity Discovery. In the next section this protocol will be described with more details.

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

![](/C:\Users\User\Desktop/fig1.png)

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

The LLDP header is a 4 byte header, in network byte order, containing 3 fields, as shown in figure 2:

![](RackMultipart20210909-4-12oj9qt_html_c9ba631983e0785a.png)

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
|Chassis ID | Identifies the device&#39;S chassis, On this switch, this is the MAC address Of the switch or stack|
|Port ID | Identifies the port that transmitted the LLDPDU |
 |Time To Live(TTL)| Indicates the length Of time in seconds for which the infOrmation received in the LLDPDIJ remains valid. If the value is greater than zero, the information is stored in the  LLDP remote system MIB. If the value is zero, the information previously received is no longer valid, and is removed from the MIB|
 |End of LLDPDU |Signals that there are no more TLVs in the LLDPDU|
 | Port VLAN |VLAN identifier that the local port associates with untagged or priority taggedframes|
 |Port &amp; Protocol VLANs  | Whether Port &amp; Protocol VI-AN is supported and enabled on the port, and the list of Port &amp; Protocol VLAN identifiers |
 |VLAN Names  | List of VLAN names that the port is assigned to |
 |Protocol IDs  | List Of protocols that are accessible through the port, for instance: <p>9000 (Loopback)</p> <p>00 26 42 42 03 00 00 00 (STP)</p> <p>00 27 42 42 03 00 00 02 (RSTP)</p> <p>00 69 42 42 03 00 00 03 (MSTP)</p> <p>888e01 (802.1x)</p> <p>aa aa 03 00 e0 2b 00 bb (EPSR)</p> <p>88090101 (LACP)</p <p>00540000e302 (loop protection)</p> <p>0800 (IPV4)</p> <p>0806 (ARP)</p> <p>86dd (IPV6)</p>|
 
 
 **Optional Base TLVs—1EEE 802.1AB-2005** 
|Port description  | A description Of the port in alpha-numeric format|
| --- | --- |
|System name  | The system&#39;s assigned name in alpha-numeric format |
|System description  | A description of the device in alpha-numeric format: This includes information about the device&#39;s hardware and operating system |
|System capabilities  | The devices router and bridge functions, and whether or not these functions are currently enabled |
| Management address | The address Of the local LLDP agent- This can be used to obtain information related to the local device |

 **IEEE 802.1 Organizationally Specific TLVs (802.1 TLVs)-IEEE 802.1AB-2005 Annex F** 
|Port description  | A description Of the port in alpha-numeric format|
| --- | --- |
|Network Policy |Network policy information configured on the port for connected media endpoint devices. The switch supports Application Type I : Voice, including the following network policy for connected voice devices to use for voice data:<p>- Voice VLAN ID</p> <p>- Voice VLAN user Priority tagging</p> <p>- Voice VLAN Diffserv Code Point (DSCP)</p>|
|Location Identification|Location information configured for the port, in one or more of the followingFormats:<p>- Civic address</p> <p>- Coordinate-based LCI</p> <p>- Emergency Location Identification Number (ELIN)</p>|
|Extended Power-via-MDI| For PoE-capable devices, this TLV includes: <p>- Power Type field: Power Sourcing Entity (PSE)</p> <p>- Power Source field: current power source, either Primary Power Source or Backup Power Source</p> <p>- Power Priority field: power priority configured on the port</p> <p>- Power Value field: In TLVs transmitted by Power Sourcing Equipment (PSE) such as this switch, this advertises the power that the port can supply over amaximum length cable based on its current configuration (that is, it takes into account power losses over the cable). In TLVs received from Powered Device (PD) neighbors, the power value is the power the neighbor requests.</p>|
|MAC/PHY Configuration/ Status| The current values Of the following for the port: <p>- Speed and duplex mode auto-negotiation support </p> <p>- Auto-negotiation Status</p> <p>- PMD (physical media dependent) auto-negotiation advertised capability</p> <p>- Operational MAU type</p> <p>- This TLV is always included in LLDP-MED advertisements</p>| 
|Power Via MDI| The power-via-MDI capabilitiesOn devices that are LLDP-MED and POE-capable, we recommend using theExtended Power-via-MDI TLV instead of this TLV | 
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

After reception of a valid LLDP message with a time-to-live value equal to zero, the LLDP Agent must remove all information in the PTOPO MIB learned from the particular LLDP agent, which is associated with the indicated remote connection endpoint.

![](RackMultipart20210909-4-12oj9qt_html_e2eb76b5a01977b5.png)

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
![](RackMultipart20210909-4-12oj9qt_html_87e828bb678bf86b.png)
