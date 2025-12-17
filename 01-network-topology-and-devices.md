
# **1 - Network Topology and Devices**

<br>

## **1.1 Network Topology and Devices**

This first chapter presents a structured small office network built from one router, three switches and several user segments. The design separates internal hosts, management devices and server infrastructure into clear logical zones. Routing, switching and basic services follow a simple layout, so each part of the network has a specific role. 

All zones are connected through the core router, which forwards traffic between internal segments and the ISP. The next sections describe the topology diagram, the device list and all physical connections used in this project.

<br>

## **1.2 Topology Diagram**

![](images/Pasted%20image%2020251217024425.png)

<br>

## **1.3 Network Zones**

**The topology is organized into these areas:**

- ISP and internet access
    
- Core routing and switching
    
- Server and administrator segment
    
- Multiple workstation groups
    
- Isolated guest area

<br>

## **1.4 Used Devices**

| **Device**                      | **Image / Type**                                       | **Interfaces**                     | **Connection** | **Purpose**                                                                         |
| :------------------------------ | :----------------------------------------------------- | :--------------------------------- | :------------- | :---------------------------------------------------------------------------------- |
| **Router R1**                   | Cisco vIOS (vios-adventerprisek9-m.spa.159-3.m6.qcow2) | 6 adapters (GigabitEthernet ports) | Telnet         | Layer 3 routing device; inter-VLAN routing; NAT/PAT; gateway to ISP.                |
| **ISP Router**                  | Cisco vIOS (same image)                                | 6 adapters (GigabitEthernet ports) | Telnet         | Layer 3 routing device; simulated ISP router providing WAN access.                  |
| **Switches SW1–SW3**            | Cisco vIOS-L2 (viosl2-adventerprisek9-m-15.2)          | 4 adapters (Ethernet) each         | Telnet         | Layer 2 switching; access ports and trunk links across host segments.               |
| **Windows-Admin / Windows-PC6** | Windows 10 Enterprise VM                               | 1 adapter (Ethernet)               | VNC            | Full workstation for administration, management and troubleshooting with testing.   |
| **Windows-Server**              | Windows Server 2022 VM                                 | 1 adapter (Ethernet)               | VNC            | Provides DHCP services only.                                                        |
| **VPCS Clients (PC1–PC5)**      | Default GNS3 VPCS host                                 | 1 adapter (Ethernet)               | Native console | Lightweight hosts placed in different VLANs for DHCP, inter-VLAN and routing tests. |
| **Internet Host**               | GNS3 built-in VPCS                                     | 1 adapter (Ethernet)               | Native console | External host used to verify NAT/PAT and internet-edge connectivity.                |

<br>

## **1.5 Topology Overview**

| **Device** | **Interface** | **Connected to ->** | **Peer Interface** | **Purpose**                                                               |
| :--------- | :------------ | :------------------ | :----------------- | :------------------------------------------------------------------------ |
| ISP Router | Gi0/0         | Router R1           | Gi0/0              | Point-to-point link between ISP router and edge router R1.                |
| ISP Router | Gi0/1         | Internet Host       | e0                 | Connection to simulated external host representing the internet.          |
| Router R1  | Gi0/1         | Switch SW1          | Gi0/1              | Downlink from R1 to access switch for server, admin and PC1 management.   |
| Router R1  | Gi0/2         | Switch SW2          | Gi0/2              | Downlink from R1 to access switch for PC2 and PC3 segment.                |
| Switch SW2 | Gi0/0         | Switch SW3          | Gi0/0              | Switch-to-switch uplink between SW2 and SW3 for PC4, PC5 and Windows-PC6. |
| Switch SW1 | Gi0/0         | Windows-Server      | Ethernet0          | Access link for DHCP server.                                              |
| Switch SW1 | Gi0/3         | Windows-Admin       | Ethernet0          | Access link for admin workstation.                                        |
| Switch SW1 | Gi0/2         | PC1                 | e0                 | Access link for user PC1.                                                 |
| Switch SW2 | Gi0/1         | PC2                 | e0                 | Access link for user PC2.                                                 |
| Switch SW2 | Gi0/3         | PC3                 | e0                 | Access link for user PC3.                                                 |
| Switch SW3 | Gi0/1         | PC5                 | e0                 | Access link for user PC5.                                                 |
| Switch SW3 | Gi0/2         | PC4                 | e0                 | Access link for user PC4.                                                 |
| Switch SW3 | Gi0/3         | Windows-PC6         | Ethernet0          | Access link for Windows test workstation.                                 |
<br>

## **1.6 Conclusion**

This chapter defines the structure of the network and identifies all devices used in the topology. It presents the physical layout, the logical zones and the roles of each router, switch and host. The device list clarifies how different platforms operate in the environment, and the connection overview shows how every segment links to the core router and the simulated internet. Together, these elements establish a clear foundation for the configuration and operation of the network.


---

<br>

**Next chapter:** [Addressing and VLAN Planning](02-addressing-and-vlan-planning.md)

