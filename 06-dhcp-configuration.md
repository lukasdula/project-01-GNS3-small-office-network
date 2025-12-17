# **6 - DHCP Configuration**

<br>

## **6.1 Introduction**

This part prepares the DHCP service on the Windows Server in VLAN 10.  
The server and the admin PC use static IPv4 addresses because they provide core functions and must stay reachable at a fixed location. All other VLANs, including VLAN 50, receive their IP addresses dynamically through DHCP.

Each client VLAN (20, 30, 40, 50, and 60) receives its own scope with an address pool, default gateway, and DNS settings. To make the service reachable across the network, Router R1 is configured as a relay agent. The relay forwards DHCP broadcasts from all VLANs to the server in VLAN 10, because routers do not pass broadcasts by default.

This setup enables automatic addressing for all user segments and prepares the network for further testing and services.

<br>

## **6.2 Topology Diagram**

![](images/Pasted%20image%2020251217030304.png)

<br>

## **6.3 Objectives**

1. Install the **DHCP Server role** on the Windows Server.
    
2. Create a **separate DHCP scope** for each client VLAN (20, 30, 40, 50, and 60).
    
3. Define the **IP range**, **subnet mask**, **default gateway**, and **DNS server** for all scopes.
    
4. Set the **lease duration** according to the lab requirements.
    
5. Activate all scopes and verify that the DHCP service is running.
    
6. Configure **Router R1 as the relay agent** to forward DHCP broadcasts from all VLANs to the server in VLAN 10.
    
7. Verify DHCP address assignment and connectivity on all client devices.

<br>

## **6.4 Configuration (Windows Server)**


#### **Install the DHCP Server Role**

This step installs the DHCP Server role on the Windows Server, allowing the system to provide dynamic IPv4 addressing for all client VLANs. The installation is performed through Server Manager, with the required management tools enabled automatically.

### **Graphical(GUI) installation:**

 ```
Open Server Manager → Manage → Add Roles and Features, select DHCP Server, confirm the required tools, and start the installation.
 ```


![](images/Pasted%20image%2020251117185916.png)

### **Results**

The DHCP Server role is installed and ready for post-installation configuration, as shown on the following screen.  
The next step defines the DHCP scopes for all dynamic VLANs.

<br>

## **6.5 Create DHCP Scopes for Client VLANs**

This step defines the DHCP scopes for all dynamic VLANs in the network.  
Each scope follows the same structure: a name, an IP pool, a subnet mask, and the default gateway that corresponds to the router subinterface for the given VLAN.

The configuration example below demonstrates the process for **VLAN 20 – Finance**.  
Other VLAN scopes follow the same procedure, using the IP ranges shown in the table.

### **Scope Overview for All VLANs**

|VLAN|Scope Name|Start IP|End IP|Mask|Gateway|
|---|---|---|---|---|---|
|20|VLAN20-Finance|192.168.20.100|192.168.20.150|255.255.255.0|192.168.20.1|
|30|VLAN30-Guest|192.168.30.100|192.168.30.150|255.255.255.0|192.168.30.1|
|40|VLAN40-Office|192.168.40.100|192.168.40.150|255.255.255.0|192.168.40.1|
|50|VLAN50-Management|192.168.50.100|192.168.50.150|255.255.255.0|192.168.50.1|
|60|VLAN60-Sales|192.168.60.100|192.168.60.150|255.255.255.0|192.168.60.1|

*Only **VLAN 20** is shown in detail in this documentation.*  
*The remaining scopes are created using the same steps and the IP ranges listed above.*

### **Configuration VLAN 20 (Finance)**

 ```
 Scope Name: VLAN20-Finance
Description: DHCP scope for VLAN 20

IP Range:
  Start IP: 192.168.20.100
  End IP:   192.168.20.150
  Subnet Mask: 255.255.255.0

Router (Default Gateway): 192.168.20.1
DNS Server: 8.8.8.8
 ```
![](images/Pasted%20image%2020251117190902.png)

```
IP Range:
  Start IP:     192.168.20.100
  End IP:       192.168.20.150
  Subnet Mask:  255.255.255.0
```
![](images/Pasted%20image%2020251117190937.png)


```
Set lease duration: 8 Days
```
![](images/Pasted%20image%2020251117191233.png)


```
Router (Default Gateway): 192.168.20.1
```
![](images/Pasted%20image%2020251117191826.png)

After completing the wizard, the scope is activated and the address pool becomes available for clients in VLAN 20. The configuration includes the defined IP range, default gateway, DNS server, and lease duration. This scope now provides automatic IPv4 addressing for devices in the Finance segment and serves as the reference example for the remaining VLAN scopes.

### **Active DHCP Scopes Summary**

![](images/Pasted%20image%2020251117193431.png)

### **Results**

The DHCP scope for VLAN 20 is successfully created and activated.  
All remaining scopes for VLANs 30, 40, 50, and 60 are configured in the same way outside of this documentation, using the IP ranges shown in the overview table.



>**Notes.:** The **Public firewall profile** was disabled on all Windows devices in this lab. In a non-domain environment, Windows classifies every interface as Public, which blocks DHCP responses and ICMP testing. Since the project does not use a domain or trusted network profile, disabling the Public profile was necessary to ensure correct DHCP operation and full connectivity across all VLANs.

<br>

## **6.6 DHCP Relay Agent on Router R1**


This part configures Router R1 as the DHCP relay agent for all client VLANs. Each subinterface forwards DHCP broadcast messages to the DHCP Server in VLAN 10 using the `ip helper-address` command. This ensures that all VLANs (20–60) can receive IP configuration from the central Windows Server.


### **Router R1 - DHCP Relay Configuration**

 ```
enable
configure terminal
interface Gi0/1.50
ip helper-address 192.168.10.10
exit
interface Gi0/2.20
ip helper-address 192.168.10.10
exit
interface Gi0/2.30
ip helper-address 192.168.10.10
exit
interface Gi0/2.40
ip helper-address 192.168.10.10
exit
interface Gi0/2.60
ip helper-address 192.168.10.10
exit
end
write
 ```
![](images/Pasted%20image%2020251117201515.png)

<br>

## **6.7 Connectivity Verification**

This part verifies end-to-end connectivity between all internal hosts in the topology. All VPCS clients obtain their IPv4 configuration dynamically using the command **ip dhcp**, which ensures that each device receives a valid address from the Windows Server in VLAN 10.  
The connectivity test confirms that every endpoint can reach the Windows Server and that inter-VLAN routing and DHCP relay operate correctly.

### **Endpoint Address Table**

| **Device**         | **IPv4 Address**   |
| -------------- | -------------- |
| PC1            | 192.168.50.100 |
| PC2            | 192.168.20.100 |
| PC3            | 192.168.30.101 |
| PC4            | 192.168.40.102 |
| PC5            | 192.168.40.103 |
| Windows-PC6    | 192.168.60.100 |
| Windows-Server | 192.168.10.10  |
| Windows-Admin  | 192.168.10.11  |

<br>

**Windows Server → All Vpcs**

 ```
ping 192.168.50.100  
ping 192.168.20.100  
ping 192.168.30.101  
ping 192.168.40.102  
ping 192.168.40.103  
ping 192.168.60.100
 ```
![](images/Pasted%20image%2020251117232531.png)
```
ping 192.168.40.103  
ping 192.168.60.100
```
![](images/Pasted%20image%2020251117232801.png)

**Windows Admin → All VPCs**

 ``` 
ping 192.168.50.100  
ping 192.168.20.100  
ping 192.168.30.101  
ping 192.168.40.102  
ping 192.168.40.103  
ping 192.168.60.100
 ```
![](images/Pasted%20image%2020251117232945.png)
```
ping 192.168.40.103  
ping 192.168.60.100
```
![](images/Pasted%20image%2020251117233107.png)


**Windows-PC6 → (Windows-Server – 192.168.10.10)**

```
ping 192.168.10.10
```
![](images/Pasted%20image%2020251117233143.png)

**PC1 → VLAN 10 (Windows-Server – 192.168.10.10)**

**PC1 → VLAN 40 (PC4 – 192.168.40.102)**

**PC1 → VLAN 20 (PC2 – 192.168.20.100)**

```
ping 192.168.10.10
ping 192.168.40.102
ping 192.168.20.100
```
![](images/Pasted%20image%2020251117233300.png)

**PC2 → VLAN 10 (Windows-Server – 192.168.10.10)**

**PC2 → VLAN 60 (Windows-PC6 – 192.168.60.100)**

**PC2 → VLAN 50 (PC1 – 192.168.50.100)**

```
ping 192.168.10.10
ping 192.168.60.100
ping 192.168.50.100
```
![](images/Pasted%20image%2020251117234205.png)

**PC3 → VLAN 10 (Windows-Server – 192.168.10.10)**

**PC3 → VLAN 40 (PC5 – 192.168.40.103)**

**PC3 → VLAN 20 (PC2 – 192.168.20.100)**

```
ping 192.168.10.10
ping 192.168.40.103
ping 192.168.20.100
```
![](images/Pasted%20image%2020251117234502.png)

**PC4 → VLAN 10 (Windows-Server – 192.168.10.10)**

**PC4 → VLAN 20 (PC2 – 192.168.20.100)**

**PC4 → VLAN 60 (Windows-PC6 – 192.168.60.100)**

```
ping 192.168.10.10
ping 192.168.20.100
ping 192.168.60.100
```
![](images/Pasted%20image%2020251117234759.png)

**PC5 → VLAN 10 (Windows-Server – 192.168.10.10)**

**PC5 → VLAN 20 (PC2 – 192.168.20.100)**

```
ping 192.168.10.10 
ping 192.168.20.100
```
![](images/Pasted%20image%2020251117234956.png)

### **Results**

All endpoints successfully reach the Windows Server and each other across all VLANs. DHCP assignment operates correctly on every VPCS host, and inter-VLAN routing functions as expected.  
Ping tests confirm full end-to-end connectivity throughout the internal network, verifying that the relay agent, trunk links, and VLAN configuration are applied correctly.

<br>

## **6.8 Conclusion**

This chapter defines the DHCP service for all client VLANs and prepares the Windows Server as the central address provider. Each VLAN receives its own scope with a correct pool, gateway, and DNS settings. Router R1 is configured as a relay agent, so DHCP broadcasts from all VLANs reach the server in VLAN 10. All Windows firewalls are adjusted to allow DHCP and ICMP, which ensures correct operation in a non-domain environment. After verification, every client receives an IP address through DHCP and all VLANs show full end-to-end connectivity. This completes the dynamic addressing setup for the internal network.


---

<br>

**Next chapter:** [NAT/PAT and Static Routing](07-nat-pat-and-static-routing.md)
