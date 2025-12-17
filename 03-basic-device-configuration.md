# **3 - Basic Device Configuration**

<br>

## **3.1 Introduction** 

This chapter defines the initial configuration applied to all routers, switches and Windows endpoints in the topology. 
The Windows Server and the Admin PC receive their static IPv4 addresses at the start of the process to ensure stable connectivity for later VLAN routing, DHCP services and management tasks. 

Each network device receives a unique hostname, the WAN interfaces between R1 and the ISP router are assigned their IPv4 addressing, and a simple connectivity check verifies that the point-to-point link operates correctly.  No VLANs, subinterfaces or routing functions are configured at this stage.  

The goal of this chapter is to establish a clean baseline that prepares the network for VLAN creation, inter-VLAN routing and all higher-level configurations in the following chapters.

<br>

## **3.2 Topology Diagram**


![](images/Pasted%20image%2020251217024906.png)

<br>

## **3.3 Objectives**

1. Set static IPv4 addresses on the Windows-Server and the Windows-   
    Admin using PowerShell.  
    
2. Assign hostnames to all routers, switches, and the simulated 
    Internet host.  
    
3. Configure IPv4 addresses on the router interfaces connecting R1,   
    the ISP router, and the simulated Internet host.
    
4. Verify router and host connectivity using interface checks, basic
    show commands, and ping tests between R1, ISP, the simulated Internet host, and both Windows devices.

<br>

## **3.4 Windows Static Addressing**

This section defines the static IPv4 settings for the Windows Server and the Admin PC, which receive fixed IP addresses that are not managed by DHCP. Both devices use static addresses in VLAN 10 to ensure stable connectivity before VLAN routing, DHCP, or any higher-level services are introduced.

### **Windows Server – Static IPv4 Configuration**

Static addressing is applied to the server because it is an administrator-managed device and a critical part of the network, requiring a fixed and predictable IP address.

**Configuration in PowerShell:**

```powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.10.10 -PrefixLength 24 -DefaultGateway 192.168.10.1
```
![](images/Pasted%20image%2020251117001722.png)

### **Verification**

Verify the assigned settings using the following command:

```
ipconfig
```
![](images/Pasted%20image%2020251117001735.png)

### **Windows Admin – Static IPv4 Configuration**

The Admin PC receives a static address because it serves as an administrator workstation and must always remain reachable at a fixed IP for management and control tasks.

**Configuration in PowerShell:**

```powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.10.11 -PrefixLength 24 -DefaultGateway 192.168.10.1
```
![](images/Pasted%20image%2020251117001932.png)

### **Results**

Both Windows devices now use static IPv4 addresses in VLAN 10. The server and the admin workstation are reachable, correctly assigned to their subnet, and prepared for later configuration stages such as VLAN routing and connectivity testing.


>**Notes.:** The command Get-NetIPAddress can also be used for verification. It shows all IP addresses known to the device, so running it on the Windows Server will also display the Windows-Admin address because both devices are in the same VLAN.

<br>

## **3.5 Device Hostname Configuration**

This section defines hostname configuration for all routers and switches in the topology. VPCS endpoints already inherit their names from the GNS3 topology label, so no manual hostname commands are required for them.

### **Router R1**

```plaintext
enable
configure terminal
hostname R1
exit
write
```
![](images/Pasted%20image%2020251117004940.png)

### **Router ISP**

```plaintext
enable
configure terminal
hostname ISP
exit
write
```
![](images/Pasted%20image%2020251117004921.png)

### **Switch SW1**

```plaintext
enable
configure terminal
hostname SW1
exit
write
```
![](images/Pasted%20image%2020251117005035.png)

### **Switch SW2**

```plaintext
enable
configure terminal
hostname SW2
exit
write
```
![](images/Pasted%20image%2020251117005138.png)
### **Switch SW3**

```plaintext
enable
configure terminal
hostname SW3
exit
write
```
![](images/Pasted%20image%2020251117005220.png)

### **Results**

All network devices now use consistent hostnames. Windows endpoints also retain their assigned names from the operating system installation, ensuring full identification across the entire topology.

<br>

## **3.6 IPv4 Address Configuration on Router and Internet Host Interfaces**

This section applies IPv4 addressing to the interfaces of R1, the ISP router, and the Internet Host. These IP addresses establish the essential point-to-point connectivity required before routing or higher-level services are introduced.

**Configuration in CLI:**

### **Router R1**

```plaintext
enable
configure terminal
interface gi0/0
ip address 203.0.113.1 255.255.255.252
no shutdown
exit
end
write
```
![](images/Pasted%20image%2020251117012629.png)

### **Router ISP**

```plaintext
enable
configure terminal
interface gi0/0
ip address 203.0.113.2 255.255.255.252
no shutdown
exit
interface gi0/1
ip address 198.51.100.1 255.255.255.252
no shutdown
exit
end
write
```
![](images/Pasted%20image%2020251117012820.png)

### **Internet Host (Console)**

```plaintext
ip 198.51.100.2 255.255.255.252 198.51.100.1
```
![](images/Pasted%20image%2020251117013042.png)

### **Results**

All router interfaces and the Internet Host now use their assigned IPv4 addresses. Connectivity between R1, the ISP router, and the Internet Host is fully established and ready for verification and further configuration steps.


<br>

## **3.7 Connectivity Verification**

This section verifies basic connectivity between all key devices in the topology. The goal is to confirm that router interfaces are operational, WAN links function correctly, and hosts can reach their directly connected neighbors. Each test validates the interface configuration applied in the previous steps.

### **Router R1**

##### Show Interfaces:

```plaintext
show ip interface brief
```
![](images/Pasted%20image%2020251117014108.png)

##### Ping Test (R1 → ISP):

```plaintext
ping 203.0.113.2
```
![](images/Pasted%20image%2020251117014142.png)

### **Router ISP**

##### Ping Test (ISP → Internet Host):

```plaintext
ping 198.51.100.2
```
![](images/Pasted%20image%2020251117014337.png)

### **Windows Server**

##### Ping Test (Windows Server → Windows Admin):

```plaintext
ping 192.168.10.11
```
![](images/Pasted%20image%2020251117015055.png)

### **Windows Admin**

##### Ping Test (Windows Admin → Windows Server):

```plaintext
ping 192.168.10.10
```
![](images/Pasted%20image%2020251117014958.png)

### **Results**

Connectivity between R1, the ISP router, and the Internet Host is confirmed through successful ping responses. Both Windows devices communicate within their VLAN, indicating correct IPv4 addressing and operational host configuration.


>**Notes.:** Windows endpoints require the IPv4 Echo Request and Echo Reply inbound rules to be enabled in the firewall to allow successful ping tests.

<br>

## 3.8 **Conclusion**

This chapter establishes the initial baseline for the entire network by assigning static IPv4 addresses to the management devices, configuring hostnames on all network nodes, and applying point-to-point addressing to the router and Internet segments.  
With these settings in place, all devices are identifiable, reachable on their directly connected networks, and verified through interface checks and ICMP tests.  
The network now has a stable and fully operational foundation for the upcoming configuration of VLANs, inter-VLAN routing, DHCP services, and all higher-level features introduced in the next chapters.


---

<br>

**Next chapter:** [VLAN Configuration](04-vlan-configuration.md)
