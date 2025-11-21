
# **2 - Addressing and VLAN Planning**

<br>

## **2.1 Introduction**

This chapter begins with the design of addressing and subnetting, which defines how IPv4 ranges are arranged across the network topology. It describes the use of a separate address space for the simulated internet, representing common ranges used in public networks, and a point-to-point subnet between R1 and the ISP, which forms a dedicated link type for routing toward the WAN. 

The chapter then assigns VLANs to each part of the topology and defines their default gateways, so the network is divided into clear functional segments. A well-chosen addressing plan provides a clear logical foundation for all later configurations.

<br>

## **2.2 IP Addressing and Subnet Overview**

| **Device / Interface** | **Description**               | **IP Address**        | **Subnet Mask** | **Default Gateway** | **Assigned Network** |
| :--------------------- | :---------------------------- | :-------------------- | :-------------- | :------------------ | :------------------- |
| ISP Router Gi0/0       | ISP → R1 link                 | 203.0.113.2           | 255.255.255.252 | N/A                 | 203.0.113.0/30       |
| Router R1 Gi0/0        | R1 → ISP link                 | 203.0.113.1           | 255.255.255.252 | N/A                 | 203.0.113.0/30       |
| ISP Router Gi0/1       | ISP → Simulated Internet host | 198.51.100.1          | 255.255.255.252 | N/A                 | 198.51.100.0/30      |
| Internet Host e0       | Simulated external host       | 198.51.100.2          | 255.255.255.252 | 198.51.100.1        | 198.51.100.0/30      |
| Windows-Server         | DHCP server (VLAN 10)         | 192.168.10.10         | 255.255.255.0   | 192.168.10.1        | 192.168.10.0/24      |
| Windows-Admin          | Admin PC (VLAN 10)            | 192.168.10.11         | 255.255.255.0   | 192.168.10.1        | 192.168.10.0/24      |
| PC1                    | Management PC (VLAN 50)       | DHCP <br>192.168.50.x | 255.255.255.0   | 192.168.50.1        | 192.168.50.0/24      |
| PC2                    | Finance PC (VLAN 20)          | DHCP 192.168.20.x     | 255.255.255.0   | 192.168.20.1        | 192.168.20.0/24      |
| PC3                    | Guest PC (VLAN 30)            | DHCP 192.168.30.x     | 255.255.255.0   | 192.168.30.1        | 192.168.30.0/24      |
| PC4                    | Office PC (VLAN 40)           | DHCP 192.168.40.x     | 255.255.255.0   | 192.168.40.1        | 192.168.40.0/24      |
| PC5                    | Office PC (VLAN 40)           | DHCP 192.168.40.x     | 255.255.255.0   | 192.168.40.1        | 192.168.40.0/24      |
| Windows-PC6            | Sales workstation (VLAN 60)   | DHCP 192.168.60.x     | 255.255.255.0   | 192.168.60.1        | 192.168.60.0/24      |

<br>

>**Notes.:** The DHCP scopes for the dynamic VLAN segments will be defined
>later during the DHCP configuration.  
Each VLAN receives a dedicated IP range, where the server allocates addresses from the dynamic pool.  
The dot-x notation (for example, `192.168.20.x`) indicates dynamically assigned host addresses within the corresponding VLAN subnet.


<br>

## **2.3 VLAN Planning**

| VLAN ID | VLAN Name    | Description / Purpose                      | Assigned Network | Default Gateway | Assigned Devices              | Switch Association |
| :-----: | :----------- | :----------------------------------------- | :--------------- | :-------------- | :---------------------------- | :----------------- |
| **10**  | Admin-Server | Windows Server and Admin PC, core services | 192.168.10.0/24  | 192.168.10.1    | Windows-Server, Windows-Admin | **SW1**            |
| **20**  | Finance      | Finance workstation                        | 192.168.20.0/24  | 192.168.20.1    | PC2                           | **SW2**            |
| **30**  | Guest        | Guest network with restricted access       | 192.168.30.0/24  | 192.168.30.1    | PC3                           | **SW2**            |
| **40**  | Office       | General office workstations                | 192.168.40.0/24  | 192.168.40.1    | PC4, PC5                      | **SW3/SW2**        |
| **50**  | Management   | Dedicated management workstation           | 192.168.50.0/24  | 192.168.50.1    | PC1                           | **SW1**            |
| **60**  | Sales        | Sales workstation segment                  | 192.168.60.0/24  | 192.168.60.1    | Windows-PC6                   | **SW3/SW2**        |

<br>

## **2.4 Conclusion** 

This addressing and VLAN plan establishes a clear logical structure for the entire network. Each subnet is assigned to a specific functional segment, ensuring **transparent routing**, secure isolation, and efficient DHCP allocation. The point-to-point link toward the ISP defines a clean separation between internal and external traffic.  

The VLAN segmentation provides a solid foundation for inter-VLAN routing, security policies, and network service configuration.  
With this design in place, the network is ready for the implementation of switching, routing, DHCP, and access control in the following chapters.

---

<br>

**Next chapter:**