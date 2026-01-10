# Module 7: Ethernet Switching Basics

## 7.1 Overview of Ethernet Protocols

- [[Ethernet]] is the dominant LAN technology (IEEE 802.3), using [[MAC Address]]es and [[Ethernet Frame]]s
- Media-access: legacy [[CSMA-CD]] on shared/half-duplex links; full-duplex on switches eliminates collisions
- Physical speeds and media: see [[Ethernet Standards]] (10M to 400G, copper/fiber)
- Topology: star/tree over switches; hubs are legacy (see [[Hub vs Switch]])
- Domains:
  - [[Collision Domain]]: one per switch port; hubs share one large domain
  - [[Broadcast Domain]]: entire VLAN/segment; broken only by routing or VLANs
- Duplex/speed negotiation: see [[Speed and Duplex]] and [[Half-Duplex vs Full-Duplex]]

## 7.2 Overview of Ethernet Frames

- Frame fields: [[Preamble and SFD]] | Destination [[MAC Address]] | Source MAC | [[EtherType]]/Length | Payload | [[FCS]]
- Size: 64-byte minimum, 1518-byte standard max (payload 46-1500); padding if payload <46; jumbo frames >1500 (vendor-specific)
- Addressing:
  - [[Unicast]] (I/G bit = 0)
  - [[Multicast]] (I/G bit = 1)
  - [[Broadcast]] (FF:FF:FF:FF:FF:FF)
- Encapsulation path: Data → L4 segment → L3 packet → L2 frame (see [[Encapsulation]] and [[Frame vs Packet]])

## 7.3 Overview of Ethernet Switches

- [[Ethernet Switch]] functions: [[Address Learning]] → lookup → [[Forwarding Behavior (Switch)]] (known unicast forward; unknown/broadcast/multicast flood)
- [[MAC Address Table (CAM)]]: source learning with [[Aging Timer]]; unknown unicast flooding until learned
- Switching methods: [[Store-and-Forward vs Cut-Through]] (default is store-and-forward with FCS check)
- Broadcast handling: stays inside the [[Broadcast Domain]]/VLAN; large domains risk [[Broadcast Storm]]
- Per-port [[Collision Domain]]s; full-duplex removes collisions (vs [[Hub]]/shared media)
- Loop prevention & segmentation placeholders: [[Spanning Tree Placeholder]] and [[VLAN Placeholder]]

## 7.4 Process of Data Communication Within a Network Segment

Example host-to-host in same VLAN:

1) Host A ARPs for Host B: [[ARP Basics]] sends broadcast → switch floods (broadcast domain-wide)
2) Host B replies unicast → switch learns B's MAC/port
3) Data frame sent to B's MAC → switch forwards known unicast to B only
4) If MAC ages out, first frame floods again ([[Unknown Unicast Flooding]])

Key points:
- Each port is its own [[Collision Domain]]; VLAN is the [[Broadcast Domain]]
- [[Data Communication on a Switch]] and [[Frame Forwarding Example]] show end-to-end flow
- Flooding types: [[Broadcast]], multicast (unless pruned), and unknown unicast (see [[Flooding]])
